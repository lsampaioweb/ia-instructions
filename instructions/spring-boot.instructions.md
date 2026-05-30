---
description: "Cross-cutting Spring Boot rules: DI, API boundaries, mapping, exception handling, logging, and project layout."
applyTo: "**/*.java, **/pom.xml, **/application*.yml"
---

# Spring Boot Cross-Cutting Rules

## Versions
Target the latest stable Spring Boot and Java versions for new projects. For existing projects, detect the version from `pom.xml` and apply rules compatible with that version without suggesting upgrades unless asked.

## Build Tool
Use Maven. See `spring-boot-pom.instructions.md` for dependency rules.

## Dependency Injection
Use constructor injection for all Spring-managed dependencies. Never use `@Autowired` on fields.

## API Boundaries
Never expose domain objects in controller responses or request parameters. Pass DTOs across all API boundaries.

## Object Mapping
Use MapStruct for all object mapping between layers. See `spring-boot-dto-mapper.instructions.md` for mapper conventions.

## Exception Handling
Centralize all exception handling in a single `@RestControllerAdvice` class. See `spring-boot-exception.instructions.md` for the full pattern.

## Logging
Use `@Slf4j` (Lombok) for all logging. See `spring-boot-logging.instructions.md` for level and content rules.

## Project Layout
Non-Java project files belong at the project root, never inside `src/`:

```text
project-root/
├── .dockerignore
├── .env
├── .gitignore
├── docker-compose.yml
├── Dockerfile
├── pom.xml
├── README.md
├── logs/
│   └── container/
│       └── .keep
├── ssl/
│   └── .keep
└── src/
    ├── main/
    │   ├── java/
    │   └── resources/
    │       ├── application.yml
    │       ├── application-development.yml
    │       ├── application-production.yml
    │       ├── i18n/
    │       │   └── messages.properties
    │       │   └── messages_pt_BR.properties
    │       └── log/
    │           └── logback-spring.xml
    └── test/
        └── java/
```
