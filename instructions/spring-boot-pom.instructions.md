---
description: "Maven POM rules: spring-boot-starter-parent, no hardcoded managed versions, dependency ordering, and BOM usage."
applyTo: "**/pom.xml"
---

# Maven POM Rules

## Project Coordinates
- `groupId`: use `io.github.lsampaioweb` as the default unless the user specifies a different groupId
- `artifactId`: project name in kebab-case (e.g. `proxmox-installer-endpoint`)
- `version`: start at `0.0.1-SNAPSHOT` for new projects
- `name`: human-readable title in title case (e.g. `Proxmox Installer Endpoint`)

## Java Version
Always use the latest GA release of Java. Set it via the `<java.version>` property managed by `spring-boot-starter-parent`:

```xml
<properties>
  <java.version>25</java.version>
</properties>
```

## Project Initialization
Always initialize a new project using `mvn archetype:generate`; never create `pom.xml` manually from scratch. This ensures the Maven wrapper (`mvnw`), `.mvn/` directory, and standard directory layout are present and the project compiles out of the box.

## Parent
Every Spring Boot project must declare `spring-boot-starter-parent` as the Maven parent. This gives you managed dependency versions, plugin configuration, and sensible defaults.

## Versions
- Do not hardcode versions for any dependency managed by `spring-boot-starter-parent` or a BOM already imported
- Declare versions in a `<properties>` block when a version must be explicit (third-party libraries not managed by the parent)
- Declare BOM imports via `<dependencyManagement>` using `import` scope; never copy-paste version numbers from a BOM into individual `<dependency>` entries

## Dependencies
- Add starters rather than individual Spring Framework or Spring Boot jars
- Common ordering (alphabetically within groups): Spring starters → production libraries → optional/runtime → test-only
- Declare `spring-boot-devtools` with `<scope>runtime</scope>` and `<optional>true</optional>`; it must never be packaged in the production artifact
- Declare `spring-boot-starter-test` and `mockito-core` with `<scope>test</scope>`

## Plugins
- Do not add `spring-boot-maven-plugin` configuration unless you need to change a specific default
- Keep plugin configuration minimal and documented when present
