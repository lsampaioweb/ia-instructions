---
description: "Feature-based packaging, dependency flow, visibility, domain object rules, interface conventions, and code style for all Java files."
applyTo: "**/*.java"
---

# Spring Boot Architecture Conventions

## No ORM
Never use JPA, Hibernate, or any ORM framework. Do not annotate domain objects with `@Entity`, `@Table`, `@Column`, or any ORM annotation. Do not add ORM dependencies to the project. Use MyBatis or Spring JDBC Templates for all data access. See `spring-boot-repository.instructions.md` for data access rules.

## Packaging
Organize code by feature or domain. Use packages like `user`, `product`, `order`, `config`, `integration`. Do not create generic root packages named `controller`, `service`, or `repository`.

Keep all classes for a feature together in one package. Example: `UserController`, `UserService`, `UserMapper`, `User`, `CreateUserRequest`, and `UserNotFoundException` all belong in `com.example.user`.

## Dependency Flow
One-way only: `controller → service → repository` or `service → integration client`. Skip-layer calls are not allowed.

Specific rules:
- `UserController` calls `UserService`; it does not call `UserMapper` or integration clients directly
- `UserService` calls `UserMapper`, repositories, and integration clients
- `UserMapper` has no knowledge of web concerns; it does not use `ResponseEntity` or any web-layer class
- Integration clients do not call controllers and do not depend on web-layer classes

## Visibility
Use the narrowest visibility that works:
- `private` for helpers not used outside the class
- Package-private (no modifier) for classes and methods used only within the same feature package
- `public` only for types and methods that must be accessible from outside the package

Do not make every class or method `public` by default.

## Interfaces
Define interfaces for services and any component that may have multiple implementations or needs to be mocked in tests. A dedicated interface is not required for one-off utilities or configuration classes that are never tested in isolation.

## Domain Objects
- Domain objects are plain Java records or classes with no persistence annotations
- Field names and types map to SQL result sets via MyBatis result maps defined in XML; no annotations are required on the domain class itself
- Use Java records for immutable domain objects; use a class only when mutation is genuinely necessary

## Code Style
- 2 spaces for indentation; never use tabs
- Use modern Java features where the project's Java version supports them: records, pattern matching, sealed classes, text blocks

## Hardcoded Text
Never hardcode human-readable text (messages, labels, error descriptions) as string literals in Java code. All text must be defined in `messages.properties` and referenced by its i18n key. See `spring-boot-logging.instructions.md` for how this applies to log statements.
