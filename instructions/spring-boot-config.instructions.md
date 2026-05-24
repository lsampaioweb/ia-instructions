---
description: "Use when creating or reviewing Spring Boot configuration classes, @ConfigurationProperties, @Value usage, or application.yml structure. Covers typed config, profile-specific files, and secret externalization."
applyTo: "**/{*Config,*Configuration,*Properties}.java"
---

# Spring Boot Configuration Conventions

- Use `application.yml` as the primary configuration file; avoid `application.properties`
- Always create three profile files: `application.yml` (shared defaults), `application-development.yml`, and `application-production.yml`; these three files are mandatory in every project
- Use profile-specific override files (`application-development.yml`, `application-production.yml`) for environment differences
- Prefer `@ConfigurationProperties` over `@Value` for groups of related settings; bind to a typed class
- Use `@Value` only for a single isolated property that does not belong to a larger config group
- Annotate `@ConfigurationProperties` classes with `@ConfigurationPropertiesScan` or register them explicitly
- Never hardcode credentials, tokens, or secrets; inject them via environment variables using `${ENV_VAR_NAME}`
- Keep `@Configuration` classes focused on one concern (security, messaging, persistence, web, etc.)
- Do not scatter `@Bean` definitions across unrelated classes; each `@Configuration` class owns its own beans

## @ConfigurationProperties Example

```java
@ConfigurationProperties(prefix = "app.jwt")
public record JwtProperties(
    String secret,
    long expirationSeconds
) {}
```

```yaml
# application.yml
app:
  jwt:
    secret: ${JWT_SECRET}
    expiration-seconds: 3600
```

Enable scanning in the main class or a configuration class:

```java
@SpringBootApplication
@ConfigurationPropertiesScan
public class Application {
  public static void main(String[] args) {
    SpringApplication.run(Application.class, args);
  }
}
```

## @Value Usage (single property only)

```java
@Value("${app.feature.enabled:false}")
private boolean featureEnabled;
```

## Profile-Based Configuration

```yaml
# application.yml — shared defaults
server:
  port: 8080
spring:
  datasource:
    url: ${DB_URL}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
```

```yaml
# application-development.yml — dev overrides
server:
  error:
    include-stacktrace: always
logging:
  level:
    root: DEBUG
```

## Secrets Rule

- All secrets must use `${ENV_VAR}` syntax in configuration files
- Never commit real credentials to any configuration file, even in comments
- Document required environment variables in the project `README.md`

## Virtual Threads

Enable virtual threads for all production services unless the service has code that is incompatible with virtual threads (e.g. `ThreadLocal`-heavy frameworks that assume platform threads):

```yaml
spring:
  threads:
    virtual:
      enabled: true
```

## DataSource and HikariCP

```yaml
spring:
  datasource:
    driver-class-name: "org.postgresql.Driver"
    url: "jdbc:postgresql://${DB_HOST:localhost}:${DB_PORT:5432}/${DB_NAME}"
    username: "${DB_USER}"
    password: "${DB_PASSWORD}"
    hikari:
      maximum-pool-size: 10
      minimum-idle: 2
      idle-timeout: 30000
      connection-timeout: 20000
```

## Actuator

Expose only the endpoints needed per profile. Never expose all endpoints in production:

```yaml
# application-development.yml
management:
  endpoints:
    web:
      exposure:
        include: "health,metrics,info"
  endpoint:
    health:
      show-details: "always"
```

```yaml
# application-production.yml
management:
  endpoints:
    web:
      exposure:
        include: "health,metrics"
  endpoint:
    health:
      show-details: "never"
```
