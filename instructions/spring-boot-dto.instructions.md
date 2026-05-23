---
description: "Use when creating or reviewing Spring Boot request/response DTOs. Covers DTO design, Lombok usage, Bean Validation annotations, and records vs classes."
applyTo: "**/{*Request,*Response,*DTO,*Dto}.java"
---

# Spring Boot DTO Conventions

- Use dedicated DTOs for request and response bodies; never expose domain entities or database models directly
- Name request DTOs with the operation and resource: `CreateUserRequest`, `UpdateUserRequest`
- Name response DTOs with the resource and suffix: `UserResponse`, `PagedUserResponse`
- Use Java records for immutable DTOs; use classes with Lombok `@Data` only when mutability is required
- Annotate request DTO fields with Bean Validation constraints (`@NotNull`, `@NotBlank`, `@Size`, `@Email`, etc.)
- Do not annotate response DTOs with validation constraints; validation applies only to input
- Keep DTO constructors and accessors consistent: records provide them automatically; for classes, use Lombok
- Do not add business logic to DTOs; they are pure data transfer structures
- Annotate response DTO classes with `@JsonInclude(JsonInclude.Include.NON_NULL)` to omit null fields from JSON output
- Use a `static from(Entity entity)` factory method on response DTOs to encapsulate the entity-to-DTO mapping

## Request DTO Examples

```java
// Immutable request using Java record with validation
public record CreateUserRequest(
    @NotBlank(message = "{user.name.required}")
    String name,

    @NotBlank(message = "{user.email.required}")
    @Email(message = "{user.email.invalid}")
    String email
) {}

// Mutable request using Lombok (when downstream frameworks require setters)
@Data
@NoArgsConstructor
@AllArgsConstructor
public class UpdateUserRequest {

  @NotBlank(message = "{user.name.required}")
  private String name;

  @Email(message = "{user.email.invalid}")
  private String email;
}
```

## Response DTO Example

```java
// Response DTO — no validation annotations
public record UserResponse(
    Long id,
    String name,
    String email
) {
  public static UserResponse from(User user) {
    return new UserResponse(user.getId(), user.getName(), user.getEmail());
  }
}
```

## Lombok Usage Rules

- Prefer `@Data` for mutable classes that need getters, setters, `equals`, `hashCode`, and `toString`
- Use `@Value` for Lombok-managed immutable classes (alternative to records)
- Use `@AllArgsConstructor` and `@NoArgsConstructor` together when both Jackson deserialization and all-args construction are needed
- Do not mix Lombok-generated and manually written constructors in the same class
