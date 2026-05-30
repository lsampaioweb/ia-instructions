---
description: "Generate a complete CRUD feature for a Spring Boot domain object following all project conventions."
---

Generate a complete CRUD feature.

Follow all active instruction files. Generate each file fully before proceeding to the next. Do not summarize or abbreviate any file.

Generate the following files in order, adhering to all conventions:

## Instruction Files
`spring-boot-*.instructions.md`

If any required confirmation item is missing, ask for it before generating code. Mention the specific instruction file and rule that requires the missing information.
Example confirmation items:
- **Domain object name** (e.g. `User`)
- **Base package** (e.g. `com.example`)
- **Fields** — list each field as `name: type [validations]`, e.g. `email: String [@NotBlank, @Email]`, `age: Integer [@Min(0)]`
