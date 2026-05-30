---
description: "Configuration rules: mandatory profile files, @ConfigurationProperties, @Value policy, secrets pointer, and @Bean organization."
applyTo: "**/application*.yml, **/*ConfigurationProperties.java, **/*Configuration.java"
---

# Configuration Rules

## Files
- Use `application.yml` as the only configuration format; do not use `application.properties`
- These three profile files are mandatory in every project: `application.yml` (shared defaults), `application-development.yml` and `application-production.yml`

## Binding
- Bind groups of related settings to a `@ConfigurationProperties` class
- Use `@Value` only for a single isolated property that does not belong to a larger config group

## Secrets
Never hardcode credentials, tokens, or secrets in configuration files or code. See `spring-boot-security.instructions.md` for the full secrets rule.

## Organization
- Keep each `@Configuration` class focused on one concern (security, messaging, persistence, web, etc.)
- Each `@Configuration` class owns its own `@Bean` definitions; do not scatter beans across unrelated classes

## Server
- Always set `server.port` explicitly in `application.yml`; do not rely on the Spring Boot default of 8080
- Enable virtual threads in `application.yml`:

```yaml
spring:
  threads:
    virtual:
      enabled: true
```
