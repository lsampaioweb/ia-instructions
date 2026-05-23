---
name: sql-postgresql
description: 'Write and review PostgreSQL queries and schema-facing SQL using conventions. Use for MyBatis XML SQL, performance-safe CRUD, migrations review, and query correctness checks.'
argument-hint: 'Business use case, table names, and expected result shape'
---

# SQL PostgreSQL

## When To Use
- **Reviewing Queries:** Creating or reviewing SQL queries for PostgreSQL-backed services.
- **MyBatis Integration:** Writing SQL for MyBatis XML mapper files.
- **Validation:** Validating query correctness, joins, and index usage assumptions.

## Style Conventions & Procedure
- **Prioritize Guardrails:** Apply Guardrails first, then Procedure, then Style Conventions.
- **Static First:** Prefer static SQL in mapper files. Avoid dynamic SQL (`<choose>`, `<if>`) unless strictly necessary.
- **Explicit Ownership:** Treat schema ownership as external (DBA-managed) by default. Keep one database per service boundary.
- **Deterministic Validation:** Confirm schema/table/column names from existing mapper files before editing. Write minimal, deterministic SQL.
- **Safe Mutations:** Always include explicit predicates in `UPDATE` or `DELETE` queries to prevent broad writes.
- **Document Assumptions:** Note assumptions regarding nullability, uniqueness, and expected cardinality.
- **Apply Naming Rules:** For DDL, strictly apply the SQL design instruction file.

## Known Failure Modes
Check for these recurring anti-patterns before producing output:

- **Unnecessary Dynamic SQL:** Wrapping simple queries in `<choose>` or `<if>` blocks when plain static SQL suffices.
- **Unbounded Mutations:** Generating `UPDATE` or `DELETE` queries without a `WHERE` clause.
- **Cross-Service Coupling:** Joining tables owned by different microservice boundaries.
- **Implicit Mapping:** Assuming auto-mapping by column name instead of declaring explicit `<resultMap>` when column names differ from Java fields.
- **Unauthorized DDL:** Adding `CREATE TABLE` or `ALTER TABLE` to MyBatis mappers when the schema is DBA-managed.
- **SQL Injection Risks:** Interpolating user input with `${param}` (string substitution) instead of `#{param}` (prepared statement binding).

## Guardrails
- **No Cross-Coupling:** Do not introduce implicit cross-service DB coupling.
- **No Secret Leakage:** Do not embed secrets in SQL or committed config files.
- **No Destructive DDL:** Do not make destructive schema assumptions without explicit user confirmation.

## Review Checklist
- **Parameterization:** Validate query safety (`#{}` over `${}` in MyBatis).
- **Static Preference:** Validate avoidance of unnecessary dynamic SQL.
- **Naming Standards:** Validate tables, columns, keys, and indexes against the SQL design instructions.
- **Dictionary Quality:** Validate table/column definitions against the data dictionary quality checklist.

## Instruction Files

These instruction files are applied automatically to matching files and cover additional conventions:

- [Behavior Baseline](../../instructions/copilot-instructions.md) — response shape, token reduction, anti-hallucination
- [SQL Database Design](../../instructions/sql-postgresql-design.instructions.md) — object naming, key/index standards, domain prefixes, legacy exceptions, and formal data dictionary rules
