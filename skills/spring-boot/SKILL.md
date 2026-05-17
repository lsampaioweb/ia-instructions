---
name: spring-boot
description: 'Orchestrate Spring Boot implementation and review workflows by routing to canonical instruction files and enforcing layer-aware checks.'
argument-hint: 'Touched files, target layer(s), and requested action (implement, refactor, or review)'
---

# Spring Boot

## When To Use
- Creating or reviewing Spring Boot services and APIs.
- Enforcing architecture boundaries in microservices.
- Hardening configuration, security, and observability setup.
- Implementing async event-driven flows with RabbitMQ.

## Ownership Model
- This skill orchestrates task flow and review sequence.
- Spring rules are owned by the instruction files listed below.
- Keep this skill stable and short; update rule details in instruction files only.

## Programming Flow
1. Identify touched files and active layer (controller, service, repository, config, DTO, advice, test).
2. Apply global behavior from `copilot-behavior.instructions.md`.
3. Apply matching Spring instruction files by `applyTo` pattern.
4. Implement or review only one executable step per turn.
5. Validate cross-layer flow (controller -> service -> repository or integration client).
6. Report findings or changes with exact file references and stop for confirmation when needed.

## Review Checklist
- Validate dependency direction and visibility against Architecture and Services instructions.
- Validate HTTP semantics, DTO boundaries, and response patterns against Controllers and DTO instructions.
- Validate mapper usage and SQL placement against Repositories instructions.
- Validate error contract and status propagation against Exception Handling instructions.
- Validate configuration style and secret externalization against Configuration instructions.
- Validate logging hygiene and PII handling against Logging instructions.
- Validate test scope and naming against Testing instructions.

## Guardrails
- Do not introduce hidden cross-service database access.
- Do not expose internal service ports publicly when reverse proxy routing exists.

## Instruction Files

These instruction files are applied automatically to matching files and cover additional conventions:

- [Behavior Baseline](../../instructions/copilot-behavior.instructions.md) — response shape, token reduction, anti-hallucination
- [Architecture](../../instructions/spring-boot-architecture.instructions.md) — package-by-feature, visibility, dependency flow
- [Configuration](../../instructions/spring-boot-config.instructions.md) — ConfigurationProperties, secrets, profiles
- [Controllers](../../instructions/spring-boot-controllers.instructions.md) — ResponseEntity, DTOs, HTTP semantics
- [DTOs](../../instructions/spring-boot-dto.instructions.md) — request/response DTOs, Lombok, Bean Validation
- [Exception Handling](../../instructions/spring-boot-exception-handling.instructions.md) — RestControllerAdvice, ErrorResponse
- [i18n](../../instructions/spring-boot-i18n.instructions.md) — MessageSource, locale resolution
- [Logging](../../instructions/spring-boot-logging.instructions.md) — Slf4j, log levels, PII
- [Repositories](../../instructions/spring-boot-repositories.instructions.md) — MyBatis mappers, XML files, null handling
- [Services](../../instructions/spring-boot-services.instructions.md) — business logic, Optional, delegation
- [Testing](../../instructions/spring-boot-testing.instructions.md) — WebMvcTest, Mockito, test naming
