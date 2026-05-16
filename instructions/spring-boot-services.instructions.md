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
    if (userMapper.existsByEmail(request.getEmail())) {
      throw new UserAlreadyExistsException(request.getName(), request.getEmail());
    }
    User user = new User(request.getName(), request.getEmail());
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