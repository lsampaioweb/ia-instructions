---
description: "Use when creating or reviewing Spring Boot service classes. Covers package-private services, constructor injection, service responsibilities, Optional usage, and one-way dependency flow."
applyTo: "**/*Service.java"
---

# Spring Boot Service Conventions

- Annotate service classes with `@Service`
- Prefer package-private service classes when they are only used inside the same feature package
- Use constructor injection for dependencies; never use `@Autowired` field injection
- Keep service classes responsible for business rules, orchestration, and delegation to the next layer
- A service may call a repository or an integration client, but should not call controllers or web-layer classes
- Return `Optional<T>` when absence is a valid outcome for the caller to handle
- Throw domain-specific exceptions when absence or duplication is part of the business rule
- Return `boolean` for delete operations when the only relevant outcome is success versus not found
- Keep helper logic in `private` methods unless another class in the same package genuinely needs that behavior
- Use small private predicates or helper methods to avoid repeating filter and comparison logic

## Example

```java
@Service
class UserService {

  private List<User> users = new ArrayList<>();
  private AtomicLong idCounter = new AtomicLong();

  List<User> findAll() {
    return users;
  }

  User findById(Long id) {
    Optional<User> entity = users.stream().filter(getById(id)).findFirst();

    if (entity.isPresent()) {
      return entity.get();
    } else {
      throw new UserNotFoundException(id);
    }
  }

  boolean delete(Long id) {
    User entity = findById(id);
    return users.remove(entity);
  }

  private Predicate<? super User> getById(Long id) {
    return u -> u.getId().equals(id);
  }
}
```