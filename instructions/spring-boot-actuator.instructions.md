---
description: "Actuator and health check rules: dependency setup, endpoint exposure, security, and custom health indicators."
applyTo: "**/*ActuatorConfig*.java, **/*HealthIndicator.java, **/management/**/*.java"
---

# Actuator and Health Check Rules

## Dependency
Include `spring-boot-starter-actuator` in every project.

## Endpoint Exposure
- Expose only `/actuator/health` and `/actuator/info` over the default management port
- All other actuator endpoints are disabled by default; enable specific ones explicitly in `application.yml` only when there is a clear operational need

```yaml
management:
  endpoints:
    web:
      exposure:
        include: "health,info"
  endpoint:
    health:
      show-details: "when-authorized"
```

## Security
- Actuator endpoints must be secured; never expose them without authentication in production
- Use a dedicated management port (`management.server.port`) to isolate actuator traffic from the application API
- Do not expose actuator endpoints through the public API gateway

## Custom Health Indicators
- Implement a `HealthIndicator` for each external dependency (database, message broker, external API)
- Return `Health.down()` with a descriptive detail key (from `messages.properties`) when the dependency is unavailable
- Keep health indicator classes package-private; register them as `@Component`
