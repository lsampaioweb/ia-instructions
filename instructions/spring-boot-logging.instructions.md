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
