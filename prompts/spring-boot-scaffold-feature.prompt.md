---
name: "spring-boot-scaffold-feature"
description: "Generate the full package-by-feature skeleton for a new Spring Boot feature. Use when starting a new domain feature from scratch."
argument-hint: "Feature name (e.g. 'user', 'product', 'order') and a one-line description of its purpose"
agent: "agent"
---

# Spring Boot Scaffold Feature

Generate a complete package-by-feature skeleton for the provided feature name.

Follow all conventions from:

- [Spring Boot Skill](~/.copilot/skills/spring-boot/SKILL.md)

## What To Generate

Create the following files under `src/main/java/.../feature/<feature-name>/`:

1. `<Feature>Controller.java` — `@RestController`, mapped to `/api/<feature-name>`, one stub POST create endpoint returning `ResponseEntity`
2. `<Feature>Service.java` — `@Service`, constructor-injected, delegates to mapper
3. `<Feature>Mapper.java` — `@Mapper` interface with one stub method matching the service call
4. `Create<Feature>Request.java` — record with at least one `@NotBlank` field and Bean Validation annotations
5. `<Feature>Response.java` — record with `id` and the same fields; include a `static from()` factory stub

Under `src/main/resources/mappers/`:

6. `<Feature>Mapper.xml` — MyBatis mapper XML with a stub SQL block for the single mapper method

Under `src/main/resources/`:

7. `messages.properties` — stub entries for validation messages and a not-found message key for this feature

Under `src/test/java/.../feature/<feature-name>/`:

8. `<Feature>ControllerTest.java` — `@WebMvcTest` stub with one test method for the create endpoint
9. `<Feature>MapperTest.java` — `@MybatisTest` stub with one test method for the mapper

## Rules

- Leave all method bodies as `// TODO` stubs — do not implement business logic.
- Match the existing project's package structure and naming conventions exactly.
- Do not create entity or domain model classes unless the project already uses them.

## Output

List every file path you will create. Then generate each file in full. After all files are created, stop and wait for confirmation before making any further changes.
