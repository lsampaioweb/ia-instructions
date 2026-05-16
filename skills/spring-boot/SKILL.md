---
name: spring-boot
description: 'Design and implement Spring Boot services using conventions. Prioritize tasks in this order: REST endpoints, microservice boundaries, RabbitMQ flows, observability setups, SQL mappings, then externalized configurations.'
argument-hint: 'Service/module name, business flow, and integration boundaries'
---

# Spring Boot

## When To Use
- Creating or reviewing Spring Boot services and APIs.
- Implementing async event-driven flows with RabbitMQ.
- Enforcing architecture boundaries in microservices.
- Hardening configuration, security, and observability setup.

## Style Conventions
- Apply these conventions in order: Core Stack, API/Validation, Architecture/Messaging, then Code Style/Secrets.
- Core stack: Maven only, Java 21 baseline, and `application.yml` with profile-specific files (`application-{profile}.yml`).
- API and validation: use constructor injection only, `ResponseEntity<T>` for REST responses, centralized exception handling with `@RestControllerAdvice`, and Bean Validation (`@Valid`, `@NotNull`, etc.).
- Architecture and messaging: keep business logic decoupled from provider/channel specifics via interfaces, keep event payloads independent of specific source or target system formats or protocols, and preserve stateless service behavior unless persistence is explicitly required.
- Code style and secrets: prefer Lombok (`@Data`, `@Slf4j`, `@AllArgsConstructor`, `@NoArgsConstructor`) when consistent with project patterns, and keep secrets out of source code by injecting them via environment variables.

## Procedure
1. Read existing package structure and service boundaries before changing code.
2. Keep controller layer thin and push rules to service/application layer.
3. Preserve or introduce clear adapter interfaces for integrations.
4. Keep configuration and secret handling externalized.
5. Preserve telemetry compatibility (logs, metrics, traces).
6. Add or update tests that validate changed behavior.

## Known Failure Modes
These are patterns that tend to reappear even when the rules are known. Check for each one before producing output:

- **Field injection on new dependencies**: When adding a dependency to an existing class, switching from constructor injection to `@Autowired` field injection. Always add to the constructor.
- **JPA/Hibernate introduced silently**: Suggesting `@Entity`, `@Repository`, or Spring Data JPA when the project has not adopted Spring Data JPA. Never introduce JPA unless explicitly requested.
- **Unsolicited docstrings and comments**: Adding Javadoc or inline comments to methods that weren't changed. Only document what you touched.
- **`.properties` instead of `.yml`**: Generating `application.properties` when the project standard is `application.yml`.
- **Business logic leaking into controllers**: Placing conditional logic or data transformation inside `@RestController` methods instead of the service layer.
- **Secrets hardcoded in examples**: Putting literal passwords or tokens in code snippets or `application.yml` templates instead of `${ENV_VAR}` placeholders.
- **Missing `ResponseEntity`**: Returning plain objects from controller methods instead of `ResponseEntity<T>` with an explicit HTTP status.

## Guardrails
- Do not introduce hidden cross-service database access.
- Do not introduce JPA/Flyway unless explicitly requested.
- Do not expose internal service ports publicly when reverse proxy routing exists.

## Instruction Files

These instruction files are applied automatically to matching files and cover additional conventions:

- [Architecture](../../instructions/spring-boot-architecture.instructions.md) — package-by-feature, visibility, dependency flow
- [Controllers](../../instructions/spring-boot-controllers.instructions.md) — ResponseEntity, DTOs, HTTP semantics
- [Services](../../instructions/spring-boot-services.instructions.md) — business logic, Optional, delegation
- [Repositories](../../instructions/spring-boot-repositories.instructions.md) — MyBatis mappers, XML files, null handling
- [Exception Handling](../../instructions/spring-boot-exception-handling.instructions.md) — RestControllerAdvice, ErrorResponse
- [DTOs](../../instructions/spring-boot-dto.instructions.md) — request/response DTOs, Lombok, Bean Validation
- [Configuration](../../instructions/spring-boot-config.instructions.md) — ConfigurationProperties, secrets, profiles
- [i18n](../../instructions/spring-boot-i18n.instructions.md) — MessageSource, locale resolution
- [Logging](../../instructions/spring-boot-logging.instructions.md) — Slf4j, log levels, PII
- [Testing](../../instructions/spring-boot-testing.instructions.md) — WebMvcTest, Mockito, test naming
