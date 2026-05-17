---
description: "Use when creating or reviewing PostgreSQL schema-facing SQL. Covers database object naming, keys/constraints/indexes, domain-based column prefixes, legacy exceptions, and formal data dictionary documentation quality."
applyTo: "**/*.{sql,ddl,psql,pgsql}"
---

# SQL PostgreSQL Design Conventions

## Object Naming
- **Length Limit:** Keep physical object names within 30 characters.
- **Syntax:** Use uppercase snake case. Avoid accents, special characters, articles, and prepositions.
- **Multiplicity:** Prefer singular names unless business semantics strictly require plural.
- **Abbreviations:** Abbreviate only to meet length constraints; prioritize readability.

## Prefix and Suffix Standards
- **Tables:** `TB_<BUSINESS_NAME>`
- **Views:** `VW_<BUSINESS_NAME>`
- **Log Tables:** `TB_LOG_<BUSINESS_NAME>`
- **History Tables:** `TB_<BUSINESS_NAME>_HIST` or `TB_HISTORICO_<BUSINESS_NAME>`
- **Temporary Tables:** `TB_<BUSINESS_NAME>_TMP` (must remove at process end)
- **Replica Tables:** `TB_<BUSINESS_NAME>_REP` (read-only copy)
- **Stored Procedures:** `SP_<BUSINESS_NAME>`
- **Triggers:** `TG_<BUSINESS_NAME>`
- **Functions:** `FN_<BUSINESS_NAME>`
- **PostgreSQL Note:** Do not use Oracle-style package naming (`PA_`) as PostgreSQL does not natively support PL/SQL packages.

## Keys, Constraints, and Indexes
- **Primary Key:** `PK_<TABLE_NAME>`
- **Unique Key:** `UK_<TABLE_NAME>_<NN>`
- **Foreign Key:** `FK_<ORIGIN_TABLE>_<TARGET_TABLE>` (abbreviate target first, then origin, if exceeding 30 chars).
- **Check Constraint:** `CK_<TABLE_NAME>_<NN>`
- **Index:** `IX_<TABLE_NAME>_<NN>`

## Column Prefixes by Domain
- **CD_:** Code / Identifier
- **DS_:** Description
- **NM_:** Name
- **NR_:** Number (or alphanumeric identifier)
- **DT_:** Date
- **HR_:** Hour/Time
- **FL_:** Boolean/Flag (two-value domain)
- **TP_:** Type (multi-value categorical)
- **ST_:** Status
- **QT_:** Quantity
- **VL_:** Amount/Value
- **PC_:** Percentage
- **TX_:** Rate
- **SQ_:** Sequence-linked identifier
- **BN_:** Binary payload

## Surrogate Key Rule
- **Standard:** Use `CD_<BUSINESS_NAME>` for surrogate identifiers.
- **Sequences:** Use `SQ_<BUSINESS_NAME>` for sequence generation names.

## Abbreviation and Legacy Rules
- **Length:** Keep abbreviations between 3 and 5 characters.
- **Acronyms:** Preserve public acronyms (`CPF`, `CNPJ`, `INSS`, `FGTS`, `IR`).
- **Consistency:** Align semantic variants to the same base abbreviation.
- **Legacy Systems:** Preserve interoperability for legacy conflicts and document the explicit justification.
- **Domain Conflicts:** If business naming and type conflict (e.g., `NR_` with alphanumeric values), retain the business meaning and document the type decision.

## Data Dictionary Requirements
- **Mandatory Documentation:** Every persistent table and key business column must have a formal, testable business definition.
- **Tone:** Definitions must be precise, concise, non-redundant, and aligned with business terminology.
- **No Storage-Only Text:** Avoid "stores X data"; document the actual business meaning and category boundaries.
- **Quality Factors:** Enforce clarity, objectivity, explicit inclusion/exclusion boundaries, and concrete examples.
- **Anti-Ambiguity:** Avoid over-summarization, implicit inference, and distorted terminology.

## Documentation Checklists
**Table definitions must answer:**
- **Identity:** What the element is and what role it plays.
- **Usage:** What it is used for.
- **Boundaries:** What belongs (and what is excluded) from this category.
- **Lifecycle:** When an instance starts/stops belonging to this category, and if membership can change.

**Column definitions must answer:**
- **Purpose:** Business meaning of the attribute.
- **Domain:** Valid value set (when categorical) and examples.
- **Rules:** Assignment and change rules.
- **Identifiers:** Explicitly state if the attribute is a primary key/identifier.

**Relationship definitions must answer:**
- **Purpose:** What relationship the element represents.
- **Rules:** Business rules and cardinality constraints.
- **Lifecycle:** Operational exceptions and lifecycle constraints.
- **Roles:** Clear role of each participating table/key.
