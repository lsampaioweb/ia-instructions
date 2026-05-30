---
description: "Generate a complete CRUD feature for a Spring Boot domain object following all project conventions."
---

Generate a complete CRUD feature.

Follow all active instruction files. Generate each file fully before proceeding to the next. Do not summarize or abbreviate any file.

Generate the following files in order, adhering to all conventions:

## Instruction Files
`spring-boot-*.instructions.md`

Before generating any code, confirm all of the following. Ask for every missing item before proceeding:
- **Project already initialized?** — if not, generate `pom.xml` via Spring Initializr conventions per `spring-boot-pom.instructions.md` before any other file
- **Domain object name** (e.g. `User`)
- **Base package** (e.g. `com.example`)
- **Fields** — list each field as `name: type [validations]`, e.g. `email: String [@NotBlank, @Email]`, `age: Integer [@Min(0)]`
- **Database?** — yes or no; only generate repository, XML mapper, and schema SQL files when the answer is yes
