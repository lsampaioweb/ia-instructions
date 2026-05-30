---
description: "Code review checklist for Spring Boot projects: architecture, ORM, hardcoded text, logging, exceptions, i18n, security, and test quality."
agent: agent
---

Review the attached files against all active instruction files. Check each item below and report findings. Group findings by file and label each with the rule it violates.

## Architecture
- [ ] No ORM annotations (`@Entity`, `@Table`, `@Column`, `@OneToMany`, etc.) on any class
- [ ] No JPA/Hibernate/Spring Data JPA imports or dependencies
- [ ] Package structure is feature-based, not layer-based
- [ ] Dependency flow is one-way: controller → service → repository/mapper; no skip-layer calls
- [ ] Visibility is as narrow as possible; no unnecessary `public` modifiers

## Maven POM
- [ ] `spring-boot-starter-parent` is the Maven parent
- [ ] No hardcoded versions for Spring-managed dependencies
- [ ] `spring-boot-devtools` declared with `<scope>runtime</scope>` and `<optional>true</optional>`

## i18n
- [ ] Locale resolved exclusively from `Accept-Language` header; no `?lang=` parameter handling
- [ ] `LocaleContextHolder.getLocale()` used in `@RestControllerAdvice` and service layer; no hardcoded `Locale`
- [ ] Log messages always resolved with `Locale.ENGLISH` in `LogMessages` utility

## Hardcoded Text
- [ ] No hardcoded human-readable string literals in Java code
- [ ] All messages, labels, and descriptions are resolved from `messages.properties` by key
- [ ] Both `messages.properties` (EN) and `messages_pt_BR.properties` (pt_BR) are present and in sync

## Logging
- [ ] All classes that log use `@Slf4j`; no manual `Logger` declarations
- [ ] No hardcoded string literals in log statements; all message templates resolved via `LogMessages` utility
- [ ] Correct log levels used per the level table in `spring-boot-logging.instructions.md`
- [ ] No PII, passwords, tokens, or secrets logged

## Controllers
- [ ] Versioned base path declared at class level with `@RequestMapping("/api/v1/...")`
- [ ] Method annotations use only path suffixes; base path not repeated in method annotations
- [ ] No business logic in controller methods; all processing delegated to service
- [ ] Request bodies annotated with `@Valid`
- [ ] Return types are DTOs, not domain objects

## Data Access
- [ ] All SQL lives in XML files; no SQL string literals in Java code
- [ ] MyBatis interfaces use `@Mapper`; no `extends JpaRepository` or `CrudRepository`
- [ ] JDBC code uses `NamedParameterJdbcTemplate`; no positional `?` parameters

## Exceptions
- [ ] Exception constructors pass an i18n key string as `messageKey`; no hardcoded message text in constructors
- [ ] `MessageSource` is never called inside exception constructors
- [ ] One `@RestControllerAdvice` handles all exceptions; no scattered `@ExceptionHandler` in controllers
- [ ] All handlers return `ErrorResponse`; stack traces are not exposed by default

## Security
- [ ] Spring Security 6 Lambda DSL used in `SecurityFilterChain`
- [ ] Deny-by-default (`anyRequest().denyAll()` or `authenticated()`) is in place
- [ ] Sensitive operations are protected with `@PreAuthorize`
- [ ] Error messages do not reveal internal structure or user enumeration information

## Testing
- [ ] Unit tests exist for all service methods
- [ ] Controller tests use `@WebMvcTest`; full context tests use `@SpringBootTest` with `@ActiveProfiles("test")`
- [ ] Mocks use `@MockitoBean` (Spring Boot 3.4+) or `@MockBean` for older versions
- [ ] Assertions use AssertJ (`assertThat`); no `assertTrue`/`assertEquals`
