---
name: sql-postgresql
description: 'Write and review PostgreSQL queries and schema-facing SQL using conventions. Use for MyBatis XML SQL, performance-safe CRUD, migrations review, and query correctness checks.'
argument-hint: 'Business use case, table names, and expected result shape'
---

# SQL PostgreSQL

## When To Use
- Creating or reviewing SQL queries for PostgreSQL-backed services.
- Writing SQL for MyBatis XML mapper files.
- Validating query correctness, joins, and index usage assumptions.
- Improving readability and maintainability of DB-facing logic.

## Style Conventions
- Precedence: apply Guardrails first, then Procedure, then Style Conventions.
- Keep SQL explicit and readable.
- Prefer static SQL in mapper files unless dynamic behavior is necessary.
- Avoid ORM-style hidden query generation when project uses mapper-driven SQL.
- Treat schema ownership as external by default unless explicit ownership is provided by the user, especially when DBAs manage all DDL.
- Keep one database per service boundary in microservice architectures.
- Use clear naming and predictable parameter binding conventions.

## Procedure
1. Confirm schema/table/column names from existing mapper files before editing.
2. Write minimal, deterministic SQL aligned with existing style.
3. Keep CRUD queries straightforward and index-friendly.
4. For updates/deletes, include explicit predicates to prevent accidental broad writes.
5. Document assumptions (nullability, uniqueness, expected cardinality) when needed.

## Known Failure Modes
These are patterns that tend to reappear even when the rules are known. Check for each one before producing output:

- **Dynamic SQL when static suffices**: Wrapping simple queries in `<choose>` or `<if>` blocks when a plain static SQL statement would work.
- **`UPDATE` or `DELETE` without a `WHERE` clause**: Generating broad mutation queries without explicit predicates, risking full-table writes.
- **Cross-service table joins**: Writing a query that joins tables owned by different service boundaries, introducing hidden coupling.
- **ORM-style result mapping assumptions**: Assuming auto-mapping by column name instead of declaring explicit `<resultMap>` when column names differ from Java field names.
- **Schema DDL in mapper files**: Adding `CREATE TABLE` or `ALTER TABLE` statements to MyBatis mapper XML files when schema is DBA-managed.
- **Unparameterized user input**: Interpolating values with `${param}` (string substitution) instead of `#{param}` (prepared statement binding), enabling SQL injection.

## Guardrails
- Do not introduce implicit cross-service DB coupling.
- Do not embed secrets in SQL or committed config.
- Do not make destructive schema assumptions without explicit user confirmation.
