---
description: "Use when creating or reviewing Spring Boot package structure, feature modules, dependency flow, or Java visibility. Covers package-by-feature architecture, layer boundaries, and avoiding overly broad public visibility."
applyTo: "**/*.java"
---

# Spring Boot Architecture Conventions

- Target the latest Java version, e.g: 25; use modern language features (Records, Pattern Matching) where applicable
- Organize code by feature or domain, not by technical layer
- Prefer packages like `user`, `product`, `order`, `config`, or `integration`, not generic `controller`, `service`, or `repository` root packages
- Keep related classes together inside the same feature package, for example `UserController`, `UserService`, `UserMapper`, DTOs, and exceptions in `user/`
- Respect one-way dependency flow: controller -> service -> repository or integration client
- A class should call only the next layer in the chain; skip-layer access is not allowed
- Use the narrowest visibility that works: `private` first, package-private by default for internal classes and methods, `public` only for the API that must be exposed outside the package
- Do not make every class or method `public` by default
- Prefer package-private for service and repository classes when they are used only inside the same feature package
- Prefer package-private service methods and repository methods unless cross-package access is intentionally required
- Keep controller request handler methods `public`, but do not widen helper methods just for consistency
- Keep helper methods `private` unless another class in the same package genuinely needs them
- If a class is only used inside one feature package, keep it package-private
- Prefer architecture that helps the package enforce the design, not architecture that depends only on team discipline

## Example Structure

```text
com.learning.restapi.user/
├── UserController.java
├── UserService.java
├── UserMapper.java
├── User.java
├── CreateUserRequest.java
└── UserNotFoundException.java
```

## Dependency Rule

- `UserController` can call `UserService`
- `UserService` can call `UserMapper`
- `UserService` can call an integration client when that client is the next infrastructure boundary
- `UserController` should not call `UserMapper` directly
- `UserController` should not call integration clients directly
- `UserMapper` should not know about web concerns such as `ResponseEntity`
- `UserMapper` and integration clients should not call controllers or depend on web-layer classes
