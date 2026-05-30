---
description: "Configuration rules: mandatory profile files, @ConfigurationProperties, @Value policy, secrets pointer, and @Bean organization."
applyTo: "**/application*.yml, **/*ConfigurationProperties.java, **/*Configuration.java"
---

# Configuration Rules

## Files
- Use `application.yml` as the only configuration format; do not use `application.properties`
- These three profile files are mandatory in every project: `application.yml` (shared defaults), `application-development.yml` and `application-production.yml`
- Set `spring.profiles.active: "development"` in `application.yml` as the default active profile; override it with the `SPRING_PROFILES_ACTIVE` environment variable or `--spring.profiles.active` argument at runtime

## Binding
- Bind groups of related settings to a `@ConfigurationProperties` class
- Use `@Value` only for a single isolated property that does not belong to a larger config group

## Secrets
Never hardcode credentials, tokens, or secrets in configuration files or code. See `spring-boot-security.instructions.md` for the full secrets rule.

## Organization
- Keep each `@Configuration` class focused on one concern (security, messaging, persistence, web, etc.)
- Each `@Configuration` class owns its own `@Bean` definitions; do not scatter beans across unrelated classes

## Logging
Always declare the logging configuration file in `application.yml`:

```yaml
logging:
  config: "classpath:log/logback-spring.xml"
```

Place the `logback-spring.xml` file under `src/main/resources/log/`.

## Server
- Always set `server.port` explicitly in `application.yml`; do not rely on the Spring Boot default of 8080
- Enable virtual threads in `application.yml`:

```yaml
spring:
  threads:
    virtual:
      enabled: true
```

## Database
Only add database configuration when the project requires a database — confirm this before generating any datasource code. Configure the datasource using environment variables with fallback defaults where appropriate. Use Hikari as the connection pool with these baseline settings:

```yaml
spring:
  datasource:
    url: "jdbc:postgresql://${DB_HOST:localhost}:${DB_PORT:5432}/${DB_NAME}"
    username: "${DB_USER}"
    password: "${DB_PASSWORD}"
    hikari:
      maximum-pool-size: 10
      minimum-idle: 2
      idle-timeout: 30000
      connection-timeout: 20000
```

Standard environment variable names: `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD`.
