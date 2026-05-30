---
description: "Repository rules: MyBatis and Spring JDBC Templates, SQL in XML files, no ORM, and no business logic."
applyTo: "**/*Repository.java, **/*Mapper.java, **/mapper/**/*.xml, **/sql/**/*.xml"
---

# Repository Rules

## No ORM
Never use JPA, Hibernate, Spring Data JPA, or any ORM abstraction. Do not extend `JpaRepository`, `CrudRepository`, or any Spring Data interface. Do not use JPQL or any ORM query language.

## MyBatis
- Define repositories as interfaces annotated with `@Mapper`
- SQL statements live in XML mapper files under `src/main/resources/mapper/`; never inline SQL as Java string literals
- Reference statements by their XML ID; the mapper interface method name must match the XML `id`
- Use MyBatis result maps in XML to map SQL result sets to domain objects; no annotations on domain classes
- Keep mapper interfaces package-private when used only within the same feature package

## Spring JDBC Templates
- Use `NamedParameterJdbcTemplate` for all JDBC access; never use positional `?` parameters
- SQL statements live in external XML files referenced by key; never inline SQL as string literals in Java
- Map result sets with a `RowMapper` implementation; keep `RowMapper` classes package-private

## General
- No business logic in repositories; data access only
- Keep repository and mapper classes package-private when used only within the same feature package
