---
description: "DTO and MapStruct mapper rules: immutable records, validation placement, and @Mapper conventions."
applyTo: "**/*DTO.java, **/*Dto.java, **/*Mapper.java, **/*Request.java, **/*Response.java"
---

# DTO and Mapper Rules

## DTOs
- Use Java records for DTOs; use a class only when the framework requires mutability
- Name request DTOs with suffix `Request` (e.g. `CreateUserRequest`), response DTOs with suffix `Response` (e.g. `UserResponse`)
- Place all validation annotations (`@NotNull`, `@NotBlank`, `@Size`, etc.) on request DTOs, never on entities

## MapStruct
- Define mappers as interfaces annotated with `@Mapper(componentModel = "spring")`
- Use consistent method names: `toEntity`, `toResponse`, `toCreateResponse`
- Keep mapper interfaces package-private when used only within the same feature package
