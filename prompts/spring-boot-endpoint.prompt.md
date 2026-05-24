---
name: "spring-boot-endpoint"
description: "Create, edit, or delete a single REST endpoint across all affected layers. Use when adding a new operation to an existing feature or changing an existing one."
argument-hint: "Operation (create/edit/delete), HTTP method, path, feature name, and a one-line description of what the endpoint does"
agent: "agent"
---

# Spring Boot Endpoint

Apply the requested endpoint operation across all affected layers.

Follow all conventions from:

<!-- Path traverses 6 levels up from ~/.config/Code/User/profiles/HASH/prompts/ to reach ~/ -->
- [Spring Boot Skill](./../../../../../../.copilot/skills/spring-boot/SKILL.md)

## Scope

Touch only the layers required by the requested operation:

- **Controller** — add, update, or remove the handler method
- **Service** — add, update, or remove the matching service method
- **Repository** — add, update, or remove the mapper interface method and its XML SQL block
- **DTOs** — add, update, or remove request or response records
- **i18n** — add, update, or remove message keys in `messages.properties`
- **Tests** — add, update, or remove the corresponding controller test and mapper test methods

## Rules

- One endpoint operation per invocation; do not touch unrelated methods or files.
- For **delete**: remove the handler, service method, mapper interface method, XML SQL block, DTOs if unused, and related test methods. Do not leave dead code.
- Before deleting any existing production code, list what will be removed and stop for confirmation.

## Output

List each file that will change and briefly describe what will be modified. Then apply all changes. Stop and wait for confirmation before making any changes beyond what was explicitly requested.
