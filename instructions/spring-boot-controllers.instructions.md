---
description: "Use when creating or reviewing Spring Boot REST controllers. Covers ResponseEntity, request mapping, pagination, and service delegation."
applyTo: "**/*Controller.java"
---

# Spring Boot Controller Conventions

- Annotate with `@RestController` and `@RequestMapping("/api/v1/{resource}")`
- Return `ResponseEntity<T>` with explicit HTTP status on every endpoint
- Keep controllers thin: delegate all business logic to the `@Service` layer
- Use `Optional.map(ResponseEntity::ok).orElseGet(() -> ResponseEntity.notFound().build())` for single-resource GET
- Accept `Pageable` and return `PagedModel<EntityModel<T>>` for collection endpoints
- Use dedicated request DTOs (e.g. `CreateUserRequest`) for `@RequestBody`; never bind domain entities directly
- Annotate `@RequestBody` with `@Valid` for automatic bean validation
- Return a `Location` header on POST using `UriComponentsBuilder`
- Return `204 No Content` on successful DELETE; return `404 Not Found` when the resource does not exist

## Example

```java
@RestController
@RequestMapping("/api/v1/users")
public class UserController {

  private final UserService userService;

  public UserController(UserService userService) {
    this.userService = userService;
  }

  @GetMapping("/{id}")
  public ResponseEntity<User> findById(@PathVariable Long id) {
    return userService.findById(id)
        .map(ResponseEntity::ok)
        .orElseGet(() -> ResponseEntity.notFound().build());
  }

  @PostMapping
  public ResponseEntity<User> create(@Valid @RequestBody CreateUserRequest request, UriComponentsBuilder uriBuilder) {
    User created = userService.create(request);
    URI location = getLocation(uriBuilder, "/{id}", created.getId());
    return ResponseEntity.created(Objects.requireNonNull(location)).body(created);
  }

  @DeleteMapping("/{id}")
  public ResponseEntity<Void> delete(@PathVariable Long id) {
    boolean removed = userService.delete(id);
    return removed ? ResponseEntity.noContent().build() : ResponseEntity.notFound().build();
  }

  private URI getLocation(UriComponentsBuilder uriBuilder, String path, Object... uriVariableValues) {
    return uriBuilder.path(Objects.requireNonNull(path))
        .buildAndExpand(Objects.requireNonNull(uriVariableValues))
        .toUri();
  }
}
```
