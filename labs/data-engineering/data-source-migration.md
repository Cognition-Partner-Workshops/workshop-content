# Data Source Migration

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Legacy Schema Characteristics](#legacy-schema-characteristics)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [uc-data-source-migration-jdbc-normalization](#uc-data-source-migration-jdbc-normalization)
- [Going Further](#going-further)

---

## Challenge

Migrate a running application's data source from a legacy data warehouse schema (denormalized, all-VARCHAR, cryptic column names) to a modern normalized schema with proper types, foreign keys, and clear naming — while keeping the API endpoints functioning identically.

This exercise uses **non-invasive static analysis**: Devin reads the legacy schema definitions, column mappings, and target schema DDL in the repository to plan and implement the migration. The application uses an H2 in-memory database, so both legacy and modern schemas can coexist — no external database or production system access is required.

## Quick Start

1. Open Devin and connect to the **uc-data-source-migration-jdbc-normalization** repository
2. Paste the [Step 1 prompt](#step-1-paste-into-devin) into Devin
3. Watch Devin create modern JPA entities, write the migration service, and open a PR with validated API parity

## Repositories

- [uc-data-source-migration-jdbc-normalization](#uc-data-source-migration-jdbc-normalization)

---

## Legacy Schema Characteristics

The app currently reads from legacy CDW-style tables that exhibit common data warehouse antipatterns:

- **All-VARCHAR typing** — dates as `MM/DD/YYYY` strings, amounts as `"285,000"` strings
- **Cryptic column names** — `BORR_FST_NM`, `LN_CURR_BAL`, `PMT_ESCROW_AMT`
- **Denormalized** — borrower fields duplicated inside loan account records
- **No foreign keys** — relationships implied but not enforced
- **Code abbreviations** — `ACT`, `CLO`, `DFT`, `FRB` for status values

## Target Outcomes

- Modern JPA entities with proper types and FK relationships
- Data migration script that transforms and loads all records
- Service layer rewired to modern repositories (no more string parsing)
- Golden-file validation: API responses match pre-migration baseline
- `DATA_SOURCE_MIGRATION_NOTES.md` documenting decisions

## What Participants Will Learn

- How Devin understands and maps between legacy and modern schemas
- How Devin handles data type conversions (string to proper types)
- The importance of golden-file testing during data source migrations
- How to validate migration correctness at the API level

## Devin Features Exercised

- Schema comprehension and entity generation
- Data transformation logic
- Service layer refactoring
- Test generation for migration validation
- PR creation with migration documentation

## Difficulty

Intermediate

## Estimated Time

60 minutes

---

## <a id="uc-data-source-migration-jdbc-normalization"></a>uc-data-source-migration-jdbc-normalization

**Repository:** [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization)

Spring Boot 3.2 / Java 17 loan management application reading from legacy CDW-style tables. Includes modern target schema DDL, column mappings, and 5 workshop migration tasks.

### Step 1: Paste into Devin

```
Review the legacy CDW schema in uc-data-source-migration-jdbc-normalization. Create modern JPA entities matching the target schema in data/modern-schema/modern_tables.sql. Write a migration service that reads from legacy tables, transforms the data (parse dates, amounts, expand codes per data/mappings/column_mappings.md), and inserts into modern tables. Then update LoanService.java to read from modern repositories instead of legacy ones. Verify all API endpoints return the same data.
```

### Step 2: Research with Ask Devin

- *"What are the data type mismatches between the legacy and modern schemas? Which transformations are most error-prone?"*
- *"How should we handle the code abbreviations (ACT, CLO, DFT, FRB) — enum mapping, lookup table, or inline conversion?"*
- Use the analysis to plan a more robust migration with error handling and validation

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the loan service domain model and API contracts. Map out which endpoints read from which legacy tables to ensure complete coverage.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the data type conversions correct? Does the migration handle edge cases (null values, malformed dates)?
- **Leave a comment** asking Devin to add a dual-read feature flag that can switch between legacy and modern data sources at runtime
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Non-invasive migration planning**: Devin reads schema definitions and column mappings to plan the migration — no need to connect to the production database or modify the running application during analysis
- **Golden-file validation**: Capturing API responses before and after migration provides a reliable correctness check — Devin automates this comparison
- **Parallel migration workstreams**: Each legacy table can be migrated independently (entity creation, migration script, service rewiring), making this a candidate for [child-session parallelism](../../reference/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents) in larger estates
- **Incremental confidence**: The dual-read feature flag pattern lets teams switch between data sources at runtime — reducing migration risk

---

## Going Further

### Automation and Webhooks

Set up a CI trigger so that whenever the legacy schema or column mappings change, a Devin session automatically regenerates the migration service and validates API parity. See [Design Patterns → Event-Driven Triggers](../../reference/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers).

### Child Sessions for Scale

For migrations involving many legacy tables, use the [divide-and-conquer pattern](../../reference/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents): a parent session inventories all tables, then spawns a child session per table. Each child handles entity creation, migration script, and service rewiring independently.

### Shared Context Layer

Create organization-level [knowledge notes](../../reference/general-themes/architecture-strengths.md#shared-context-layer) documenting your migration conventions (e.g., "Status code abbreviations always expand to full enums", "Date fields migrate from VARCHAR MM/DD/YYYY to LocalDate"). Every Devin session inherits these conventions automatically.

### Team-Based Operation

Data engineers, application developers, and QA analysts can all review the migration PR simultaneously. Devin reads feedback from any reviewer and iterates — the migration evolves through the same PR feedback loop as any other code change. See [Collaboration Model](../../reference/general-themes/collaboration-model.md).
