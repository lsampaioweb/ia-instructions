---
description: "Use when creating or reviewing Spring Boot logging. Covers @Slf4j usage, log levels, structured logging, and what to log or avoid."
applyTo: "**/*.java"
---

# Spring Boot Logging Conventions

- Use `@Slf4j` (Lombok) to inject the logger; never declare `private static final Logger` manually
- Use the appropriate log level for each message: `ERROR` for failures, `WARN` for recoverable issues, `INFO` for key lifecycle events, `DEBUG` for diagnostic details
- Use parameterized log statements (`log.debug("User {} not found", id)`); never use string concatenation
- Log at `INFO` level when a request is received or a significant business event occurs
- Log at `DEBUG` level for method entry/exit and internal state useful during development
- Log at `ERROR` level when an exception is caught and cannot be recovered; always include the exception object as the last argument
- Do not log sensitive data: passwords, tokens, full credit card numbers, or personally identifiable information (PII)
- Do not log inside tight loops or high-throughput paths without a level guard (`if (log.isDebugEnabled())`)

## What to Log

| Level   | When to use |
|---------|-------------|
| `ERROR` | Unrecoverable failure; exception caught at the boundary |
| `WARN`  | Recoverable issue; unexpected but non-fatal state |
| `INFO`  | Service started, significant business event (order created, user registered) |
| `DEBUG` | Internal state during processing; useful for developers only |
| `TRACE` | Very high frequency or low-level detail; disabled in all non-local environments |

## What NOT to Log

- Passwords, API keys, secrets, or tokens — even masked versions
- Full request/response bodies that may contain PII
- Stack traces at `INFO` or `DEBUG` level — reserve those for `ERROR`
- Redundant messages that repeat what the framework already logs

## Example

```java
@Slf4j
@Service
class UserService {

  private final UserMapper userMapper;

  UserService(UserMapper userMapper) {
    this.userMapper = userMapper;
  }

  User create(CreateUserRequest request) {
    log.info("Creating new user");

    if (userMapper.existsByEmail(request.email())) {
      log.warn("User creation rejected: email already in use");
      throw new UserAlreadyExistsException(request.name(), request.email());
    }

    User user = new User(request.name(), request.email());
    userMapper.insert(user);

    log.info("User created with id {}", user.getId());
    return user;
  }
}
```

## Logback Configuration

- Store the logback config at `resources/log/logback-spring.xml`; reference it in `application.yml` as `logging.config: classpath:log/logback-spring.xml`
- Use `<springProperty>` to read `spring.application.name` and `logging.file.path` from the Spring environment
- Use a `ConsoleAppender` with color patterns for local development
- Use a `RollingFileAppender` wrapped in an `AsyncAppender` for file output; cap each file at 100 MB, keep 1 day of history, and set a 1 GB total size cap
- Define log levels per Spring profile using `<springProfile>`: `DEBUG` for the `debug` profile, `INFO` for `development` and `default`, `INFO` to file only for `production`

```xml
<!-- resources/log/logback-spring.xml -->
<configuration>
  <springProperty scope="context" source="spring.application.name" name="APPLICATION_NAME" />
  <springProperty scope="context" source="logging.file.path" name="LOG_DIR" defaultValue="./logs" />

  <appender name="Console" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <Pattern>%black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %yellow(%logger{60}): %msg%n%throwable</Pattern>
    </encoder>
  </appender>

  <appender name="RollingFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${LOG_DIR}/${APPLICATION_NAME}.log</file>
    <encoder>
      <Pattern>%d{ISO8601} %-5level [%t] %logger{60}: %msg%n%throwable</Pattern>
    </encoder>
    <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
      <fileNamePattern>${LOG_DIR}/archived/${APPLICATION_NAME}-%d{yyyy-MM-dd}.%i.gz</fileNamePattern>
      <maxFileSize>100MB</maxFileSize>
      <maxHistory>1</maxHistory>
      <totalSizeCap>1GB</totalSizeCap>
    </rollingPolicy>
  </appender>

  <appender name="File" class="ch.qos.logback.classic.AsyncAppender">
    <appender-ref ref="RollingFile" />
  </appender>

  <springProfile name="debug">
    <root level="DEBUG">
      <appender-ref ref="Console" />
      <appender-ref ref="File" />
    </root>
  </springProfile>

  <springProfile name="default | development">
    <root level="INFO">
      <appender-ref ref="Console" />
      <appender-ref ref="File" />
    </root>
  </springProfile>

  <springProfile name="production">
    <root level="INFO">
      <appender-ref ref="File" />
    </root>
  </springProfile>
</configuration>
```
