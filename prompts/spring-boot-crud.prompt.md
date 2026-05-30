---
agent: "agent"
description: "Generate a complete CRUD feature for a Spring Boot domain object following all project conventions."
---

Generate a complete CRUD feature. Before starting, confirm:
- **Domain object name** (e.g. `User`)
- **Base package** (e.g. `com.example`)
- **Fields** — list each field as `name: type [validations]`, e.g. `email: String [@NotBlank, @Email]`, `age: Integer [@Min(0)]`

If any required confirmation item is missing, ask for it before generating code.

Place all files in `{basePackage}.{domainname}` (lowercase).
Use the confirmed fields consistently across domain object, request/response DTOs, mappers, service, controller, and tests.

Generate in this exact order:
Generate each file fully before proceeding to the next. Do not summarize or abbreviate any file.

1. `{Domain}.java` — plain Java record; no persistence annotations; fields match the confirmed list
2. `Create{Domain}Request.java` — Java record with validation annotations (`@NotNull`, `@NotBlank`, etc.)
3. `Update{Domain}Request.java` — Java record with validation annotations
4. `{Domain}Response.java` — Java record (response DTO)
5. `{Domain}Mapper.java` — public MapStruct interface, `@Mapper(componentModel = "spring")`; methods `toDomain` and `toResponse`; `toDomain` maps from `Create{Domain}Request` or `Update{Domain}Request`
6. `{Domain}Repository.java` — MyBatis `@Mapper` interface; package-private; methods: `findAll()`, `findById(Long id)`, `insert({Domain} domain)`, `update({Domain} domain)`, `deleteById(Long id)`
7. `{Domain}Mapper.xml` — MyBatis XML mapper under `src/main/resources/mapper/`; `namespace` matches the fully-qualified `{Domain}Repository` interface; define a `<resultMap>` mapping SQL columns to `{Domain}` fields; one `<select>`, `<insert>`, `<update>`, `<delete>` statement per repository method; no SQL string literals in Java
8. `messages.properties` entries — add i18n keys for all log messages and exception messages used in the feature (e.g. `{domainname}.not.found`, `{domainname}.conflict`, `{domainname}.created`, `{domainname}.updated`, `{domainname}.deleted`); show the English values; also add the same keys to `messages_pt_BR.properties` with Portuguese translations
9. `{Domain}Service.java` — interface with CRUD method signatures
10. `{Domain}ServiceImpl.java` — `@Service` implementation; inject `{Domain}Repository`, `{Domain}Mapper` (MapStruct), and `LogMessages` via constructor; `@Transactional` at method level; use `logMessages.get(key, args)` for all log statements; throw domain exceptions from `{basePackage}.exception`: `{Domain}NotFoundException` (passes i18n key `{domainname}.not.found` as `messageKey`) for missing records, and `{Domain}ConflictException` (passes i18n key `{domainname}.conflict` as `messageKey`) for duplicates; exceptions must never contain hardcoded message text
11. `{Domain}Controller.java` — `@RestController`; class-level `@RequestMapping("/api/v1/{domainname}s")` (lowercase plural); method annotations use only the path suffix (`/`, `/{id}`); endpoints: `GET /` returns `List<{Domain}Response>`, `GET /{id}` returns `{Domain}Response` or 404, `POST /` returns 201 Created with `{Domain}Response`, `PUT /{id}` returns 200 OK with updated `{Domain}Response` or 404, `DELETE /{id}` returns 204 No Content or 404; delegates to service; uses `@Valid`; returns DTOs only; annotate each method with `@Operation` and `@ApiResponse`
12. `{Domain}ServiceImplTest.java` — unit test using `@ExtendWith(MockitoExtension.class)`, `@Mock`, and `@InjectMocks`; one test per behavior; AssertJ assertions
13. `{Domain}ControllerTest.java` — `@WebMvcTest` slice test with `@ActiveProfiles("test")`; asserts status codes, response body, and headers; at least one negative test per endpoint returning non-2xx (e.g. 404); use `@MockitoBean` (Spring Boot 3.4+) to stub service exceptions; if the project uses Spring Boot older than 3.4, use `@MockBean` instead

Follow all active instruction files. Explicit instructions above take precedence only where they add detail not covered by instruction files.
