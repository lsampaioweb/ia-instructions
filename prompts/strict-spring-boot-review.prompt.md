---
name: "strict-spring-boot-review"
description: "Review Spring Boot code against repository conventions for architecture, controllers, services, visibility, and HTTP patterns. Use when doing a strict code review or refactoring review."
argument-hint: "Files, package, or feature to review and any specific concern"
agent: "agent"
---

# Strict Spring Boot Review

Review the provided Spring Boot code using these repository conventions:

- [Spring Boot skill](../skills/spring-boot/SKILL.md)
- [Spring Boot architecture instructions](../instructions/spring-boot-architecture.instructions.md)
- [Spring Boot controller instructions](../instructions/spring-boot-controllers.instructions.md)
- [Spring Boot service instructions](../instructions/spring-boot-services.instructions.md)
- [Spring Boot repository instructions](../instructions/spring-boot-repositories.instructions.md)
- [Spring Boot DTO instructions](../instructions/spring-boot-dto.instructions.md)
- [Spring Boot exception handling instructions](../instructions/spring-boot-exception-handling.instructions.md)
- [Spring Boot configuration instructions](../instructions/spring-boot-config.instructions.md)
- [Spring Boot i18n instructions](../instructions/spring-boot-i18n.instructions.md)
- [Spring Boot logging instructions](../instructions/spring-boot-logging.instructions.md)
- [Spring Boot testing instructions](../instructions/spring-boot-testing.instructions.md)

Perform a strict review. Be direct and precise.

Check specifically for:

1. Package-by-feature structure instead of generic layer packages.
2. One-way dependency flow: controller -> service -> mapper or integration client.
3. Overuse of `public` where package-private or `private` is more appropriate.
4. Field injection or unnecessary framework coupling.
5. Business logic leaking into controllers.
6. Incorrect HTTP patterns: missing `ResponseEntity<T>`, wrong DELETE status (must be 204), missing `Location` header on POST.
7. Service classes reaching across layers or skipping the next dependency in the chain.
8. Direct use of domain entities as `@RequestBody`; dedicated request DTOs must be used.
9. Hardcoded HTTP status codes in the advice instead of reading from the exception.
10. SQL embedded in Java annotations instead of external XML mapper files.
11. PII or secrets logged at any level.
12. `ErrorResponse` missing `@JsonInclude(NON_NULL)`, leaking null fields to the client.
13. Violations of any other Spring Boot coding conventions documented in the linked files above.

## Output

Return:

1. A short findings list ordered by severity.
2. The exact rule or convention being violated for each finding.
3. If refactoring is needed, provide the corrected implementation for the first affected layer only, then stop and wait for confirmation.

Do not invent repository standards beyond the linked files. If the code is acceptable, say so clearly and list only the remaining risks or tradeoffs.