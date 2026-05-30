---
description: "Service layer rules: business logic ownership, @Transactional, domain exceptions, and interface+impl pattern."
applyTo: "**/*Service.java, **/*ServiceImpl.java"
---

# Service Rules

- All business logic lives in the service layer; never in controllers, repositories, or entities
- Define a service interface; provide a single implementation class suffixed with `Impl`
- Apply `@Transactional` at the method level, not on the class; read-only methods use `@Transactional(readOnly = true)`
- Throw domain-specific exceptions that extend the project's base exception class; do not throw raw Spring or JPA exceptions
- Services may call repositories, mappers, and integration clients; they do not call controllers
- Keep service classes and methods package-private when used only within the same feature package
