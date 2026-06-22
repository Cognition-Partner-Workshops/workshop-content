# DW Migration: Teradata to Snowflake

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Teradata-Specific Features to Migrate](#teradata-specific-features-to-migrate)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [uc-dw-migration-teradata-to-snowflake](#uc-dw-migration-teradata-to-snowflake)
- [Going Further](#going-further)

---

## Challenge

Convert Teradata DDL/DML, stored procedures, and macros to Snowflake-compatible SQL. Build a migration runbook and implement validation tests.

This exercise uses **non-invasive static analysis**: Devin reads the Teradata DDL, DML, stored procedures, macros, and BTEQ scripts in the repository and translates them to Snowflake-compatible SQL — no connection to a Teradata instance or Snowflake account is required for the conversion work.

## Quick Start

1. Open Devin and connect to the **uc-dw-migration-teradata-to-snowflake** repository
2. Paste the [Step 1 prompt](#step-1-paste-into-devin) into Devin
3. Watch Devin convert the Teradata DDL to Snowflake SQL and open a PR with the migration runbook

## Repositories

- [uc-dw-migration-teradata-to-snowflake](#uc-dw-migration-teradata-to-snowflake)

---

## Teradata-Specific Features to Migrate

| Feature | Snowflake Equivalent |
|---------|---------------------|
| `SET TABLE` / `MULTISET TABLE` | Standard `CREATE TABLE` |
| `PRIMARY INDEX (PI)` | `CLUSTER BY` (optional) |
| `PARTITION BY RANGE_N` | Micro-partitioning + `CLUSTER BY` |
| `COMPRESS` | Automatic compression |
| `NOT CASESPECIFIC` | `COLLATE 'en-ci'` or `UPPER()`/`LOWER()` |
| `FORMAT` on columns | `TO_CHAR()` in queries |
| `COLLECT STATISTICS` | Not needed (automatic) |
| `QUALIFY` | Supported natively |
| `ZEROIFNULL` / `NULLIFZERO` | Supported natively |
| `CSUM()` (cumulative sum) | `SUM() OVER (ORDER BY ...)` |
| `MAVG()` (moving average) | `AVG() OVER (ROWS BETWEEN ...)` |
| `HASHROW()` | `HASH()` |
| `SEL` (shorthand) | `SELECT` |
| `REPLACE MACRO` | Convert to stored procedure |
| `VOLATILE TABLE` | `CREATE TEMPORARY TABLE` |
| `ACTIVITY_COUNT` | `SQLROWCOUNT` |
| BTEQ `.LOGON`, `.EXPORT`, `.IF` | SnowSQL or stored procedures |

## Target Outcomes

- Snowflake-compatible DDL/DML for all converted objects
- `MIGRATION_RUNBOOK.md` with loading approach documented
- Validation queries with row counts, checksums, and business-level reconciliations
- `SQL_TRANSLATION_NOTES.md` documenting every translation decision
- PR with all migration artifacts

## What Participants Will Learn

- How Devin understands Teradata-specific SQL dialects
- How Devin translates vendor-specific features to Snowflake equivalents
- Data warehouse migration methodology (inventory → convert → validate)
- The importance of validation queries in migration projects

## Devin Features Exercised

- SQL dialect comprehension
- Large-scale SQL translation
- Documentation generation
- PR creation with migration artifacts

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="uc-dw-migration-teradata-to-snowflake"></a>uc-dw-migration-teradata-to-snowflake

**Repository:** [uc-dw-migration-teradata-to-snowflake](https://github.com/Cognition-Partner-Workshops/uc-dw-migration-teradata-to-snowflake)

Teradata-based retail banking analytics data warehouse. 7 DDL tables (5 dimensions + 2 fact tables) in `ddl/tables/`, 3 views in `ddl/views/`, 3 stored procedures and 3 macros in `dml/`, seed data, and validation queries in `data/validation/`.

### Step 1: Paste into Devin

```
Analyze all Teradata DDL in uc-dw-migration-teradata-to-snowflake/ddl/ (tables and views). Convert every table and view to Snowflake-compatible SQL, handling all Teradata-specific features (SET/MULTISET, PI, PPI, COMPRESS, CASESPECIFIC, FORMAT, QUALIFY, CSUM, MAVG). Place converted files in a new snowflake/ directory mirroring the original structure. Create a MIGRATION_RUNBOOK.md documenting every translation decision.
```

### Step 2: Research with Ask Devin

- *"What Teradata-specific features are used across the DDL and DML files? Which ones require the most complex translation?"*
- *"What's the best strategy for converting the BTEQ scripts — SnowSQL, stored procedures, or an orchestration tool like Airflow?"*
- Use the analysis to plan a more comprehensive migration that includes stored procedures and macros

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the data warehouse schema relationships and ETL flow. Use this to ensure the migration preserves data lineage and business semantics.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the Snowflake DDL files compile? Are Teradata-specific features correctly translated?
- **Leave a comment** asking Devin to also convert the stored procedures in `dml/stored_procedures/` and macros in `dml/macros/`
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Non-invasive static analysis**: Devin converts Teradata SQL from source files alone — no Teradata instance access, no ODBC connection, no production system changes required
- **Feature-level mapping**: Every Teradata-specific construct (PI, PPI, COMPRESS, CSUM, MAVG) has a documented Snowflake equivalent or removal rationale
- **Parallel migration workstreams**: DDL tables, views, stored procedures, and macros can each be converted independently — a natural fit for [child-session parallelism](../../shared/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents)
- **Validation-first methodology**: Row count and checksum queries provide confidence that the conversion preserves data semantics

---

## Going Further

### Automation and Webhooks

Set up a CI trigger so that whenever new Teradata DDL is added to the repository, a Devin session automatically generates the Snowflake equivalent and updates the migration runbook. See [Design Patterns → Event-Driven Triggers](../../shared/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers).

### Child Sessions for Scale

For large Teradata estates with hundreds of tables, use the [divide-and-conquer pattern](../../shared/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents): a parent session inventories all DDL objects, then spawns a child session per object type or per schema. Each child converts its objects and opens its own PR.

### Scheduled Sessions

Schedule a recurring Devin session to re-validate converted DDL against evolving Snowflake best practices (e.g., new clustering recommendations, deprecated syntax). See [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md#scheduled-sessions).

### Shared Context Layer

Create organization-level [knowledge notes](../../shared/general-themes/architecture-strengths.md#shared-context-layer) documenting your Teradata-to-Snowflake translation standards (e.g., "PI columns map to CLUSTER BY", "COMPRESS columns are dropped — Snowflake handles compression automatically"). Every Devin session inherits these conventions.

### Team-Based Operation

DBAs, data engineers, and business analysts can all review the migration PR simultaneously. Devin reads feedback from any reviewer and iterates. See [Collaboration Model](../../shared/general-themes/collaboration-model.md).
