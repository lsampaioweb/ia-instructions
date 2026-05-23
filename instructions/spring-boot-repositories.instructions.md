---
description: "Use when creating or reviewing Spring Boot repository or data-access classes. Covers MyBatis mapper conventions, SQL file organization, result mapping, and null handling."
applyTo: "**/{*Repository,*Mapper,*Dao}.java"
---

# Spring Boot Repository Conventions

- Use MyBatis mapper interfaces annotated with `@Mapper`; do not introduce JPA or Hibernate unless explicitly requested
- Keep SQL in external `.xml` mapper files under `resources/mappers/`; do not embed SQL as inline annotations in Java code
- Name mapper XML files to match the mapper interface (e.g. `UserMapper.java` → `resources/mappers/UserMapper.xml`)
- Return `null` from mappers when a record is not found; wrap in `Optional` at the service layer
- Do not call other mapper interfaces from within a mapper; that is the service's responsibility
- Keep mapper methods focused on a single query or DML operation
- Use `@Param` to name mapper parameters explicitly when the method has more than one parameter
- Never place business logic inside mapper interfaces or XML files; mappers are pure data-access artifacts

## Mapper Interface Example

```java
@Mapper
public interface UserMapper {

  List<User> findAll();

  User findById(@Param("id") Long id);

  boolean existsByEmail(@Param("email") String email);

  void insert(User user);

  void deleteById(@Param("id") Long id);
}
```

## Mapper XML Example

```xml
<!-- resources/mappers/UserMapper.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.example.user.UserMapper">

  <resultMap id="userResultMap" type="com.example.user.User">
    <id     property="id"    column="id" />
    <result property="name"  column="name" />
    <result property="email" column="email" />
  </resultMap>

  <select id="findAll" resultMap="userResultMap">
    SELECT id, name, email FROM users
  </select>

  <select id="findById" resultMap="userResultMap">
    SELECT id, name, email FROM users WHERE id = #{id}
  </select>

  <select id="existsByEmail" resultType="boolean">
    SELECT COUNT(1) > 0 FROM users WHERE email = #{email}
  </select>

  <insert id="insert" useGeneratedKeys="true" keyProperty="id">
    INSERT INTO users (name, email) VALUES (#{name}, #{email})
  </insert>

  <delete id="deleteById">
    DELETE FROM users WHERE id = #{id}
  </delete>

</mapper>
```

## application.yml Configuration

```yaml
mybatis:
  mapper-locations: classpath:mappers/*.xml
  type-aliases-package: com.example
  configuration:
    map-underscore-to-camel-case: true
```

## JdbcClient (Spring Boot Native Alternative)

- Use `JdbcClient` when MyBatis is not available or when the project uses `spring-boot-starter-jdbc` directly
- Declare it as a `@Bean` in a `@Configuration` class; Spring Boot auto-configures the `DataSource`
- Use named parameters (`:name`) for all queries
- Return `Optional` from single-row queries using `.optional()`; throw domain exceptions in `orElseThrow`
- Use `@Transactional` on methods that execute multiple writes that must succeed or fail together
- Never swallow exceptions; wrap unexpected JDBC failures in a domain-level exception

```java
@Configuration
public class JdbcClientConfig {
  @Bean
  public JdbcClient jdbcClient(DataSource dataSource) {
    return JdbcClient.create(Objects.requireNonNull(dataSource));
  }
}
```

```java
@Slf4j
@Repository
class UserRepository {

  private final JdbcClient jdbcClient;

  UserRepository(JdbcClient jdbcClient) {
    this.jdbcClient = jdbcClient;
  }

  Optional<User> findById(Long id) {
    return jdbcClient
        .sql("SELECT id, name, email FROM users WHERE id = :id")
        .param("id", id)
        .query(User.class)
        .optional();
  }

  @Transactional
  void saveAll(List<User> users) {
    String sql = "INSERT INTO users (name, email) VALUES (:name, :email)";
    for (User user : users) {
      jdbcClient.sql(sql)
          .param("name", user.getName())
          .param("email", user.getEmail())
          .update();
    }
  }
}
```
