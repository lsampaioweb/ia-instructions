---
description: "Use when creating or reviewing Spring Boot service classes. Covers service responsibilities, Optional usage, exception throwing, and transaction boundaries."
applyTo: "**/*Service.java"
---

# Spring Boot Service Conventions

- Annotate service classes with `@Service`
- Use a concrete service class by default; introduce a service interface only when multiple implementations are required
- Keep service classes responsible for business rules, orchestration, and delegation to the next layer
- Return `Optional<T>` when absence is a valid outcome for the caller to handle
- Throw domain-specific exceptions when absence or duplication is part of the business rule
- Return `boolean` for delete operations when the only relevant outcome is success versus not found
- Keep helper logic in `private` methods unless another class in the same package genuinely needs that behavior
- Use small private predicates or helper methods to avoid repeating filter and comparison logic
- Annotate a service method with `@Transactional` when it coordinates multiple repository writes that must succeed or fail together; keep the transaction boundary at the service layer, not the repository layer

## Example

```java
@Service
class UserService {

  private final UserMapper userMapper;

  UserService(UserMapper userMapper) {
    this.userMapper = userMapper;
  }

  List<User> findAll() {
    return userMapper.findAll();
  }

  Optional<User> findById(Long id) {
    return Optional.ofNullable(userMapper.findById(id));
  }

  User create(CreateUserRequest request) {
    if (userMapper.existsByEmail(request.email())) {
      throw new UserAlreadyExistsException(request.name(), request.email());
    }
    User user = new User(request.name(), request.email());
    userMapper.insert(user);
    return user;
  }

  boolean delete(Long id) {
    Optional<User> entity = findById(id);
    entity.ifPresent(u -> userMapper.deleteById(u.getId()));
    return entity.isPresent();
  }
}
```