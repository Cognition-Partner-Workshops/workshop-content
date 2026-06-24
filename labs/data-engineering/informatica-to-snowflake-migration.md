# Informatica PowerCenter to Snowflake Migration

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Informatica-to-Snowflake Translation Patterns](#informatica-to-snowflake-translation-patterns)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [ts-informatica-powercenter](#ts-informatica-powercenter)
- [uc-dw-migration-teradata-to-snowflake](#uc-dw-migration-teradata-to-snowflake)
- [Going Further](#going-further)

---

## Challenge

Convert Informatica PowerCenter ETL mappings and Oracle-based data flows to a Snowflake-compatible architecture. Participants will extract transformation logic from PowerCenter XML exports and re-implement it as Snowflake SQL (stored procedures, tasks, streams) or dbt models, replacing the legacy Oracle source/target schemas with Snowflake equivalents.

This exercise uses **non-invasive static analysis**: Devin reads the PowerCenter XML exports, Oracle SQL scripts, and shell orchestration files in the repository to extract and translate the ETL logic — no Informatica server, no Oracle database, and no Snowflake account are required for the conversion work.

## Quick Start

1. Open Devin and connect to the **ts-informatica-powercenter** repository (and optionally **uc-dw-migration-teradata-to-snowflake** as a Snowflake DDL reference)
2. Paste the [Step 1 prompt](#step-1-paste-into-devin) into Devin
3. Watch Devin extract transformation logic from the XML, generate Snowflake DDL and stored procedures, and open a PR

## Repositories

- [ts-informatica-powercenter](#ts-informatica-powercenter)
- [uc-dw-migration-teradata-to-snowflake](#uc-dw-migration-teradata-to-snowflake) *(reference for Snowflake DDL patterns)*

---

## Informatica-to-Snowflake Translation Patterns

| PowerCenter Concept | Snowflake Equivalent |
|---------------------|---------------------|
| Source Qualifier (Oracle SQL) | Snowflake `SELECT` / external table |
| Expression Transformation | Snowflake SQL expressions / `CASE` / UDFs |
| Filter Transformation | `WHERE` clause |
| Lookup Transformation | `JOIN` or `MERGE` |
| Aggregator Transformation | `GROUP BY` with window functions |
| Joiner Transformation | `JOIN` (inner, outer, cross) |
| Sorter Transformation | `ORDER BY` / `CLUSTER BY` |
| Sequence Generator | `AUTOINCREMENT` / Snowflake sequence |
| Router Transformation | `CASE` with `INSERT` into multiple targets |
| Update Strategy | `MERGE INTO ... WHEN MATCHED / NOT MATCHED` |
| Session (scheduling) | Snowflake Task with CRON schedule |
| Workflow (orchestration) | Snowflake Task DAG or Airflow DAG |
| `pmcmd` (CLI invocation) | Snowflake Task API / SnowSQL |
| Flat-file Source (VSAM/CSV) | Snowflake Stage + `COPY INTO` / Snowpipe |
| Pre/Post-session SQL | Task predecessor/successor or stored procedure |

## Target Outcomes

- Snowflake DDL for all source and target tables currently defined in Oracle
- Snowflake stored procedures or dbt models implementing the transformation logic from each PowerCenter mapping
- Data loading strategy using Snowflake Stages and `COPY INTO` for flat-file sources
- Orchestration replacement: Snowflake Task DAG or Airflow DAG replacing `pmcmd` shell workflows
- `INFORMATICA_MIGRATION_RUNBOOK.md` documenting every translation decision, risk, and validation approach
- PR with all migration artifacts

## What Participants Will Learn

- How Devin extracts transformation logic from PowerCenter XML and translates it to SQL
- How Devin maps Informatica concepts (Sessions, Workflows, Mappings) to Snowflake-native equivalents (Tasks, Stored Procedures, Stages)
- How to handle Oracle-to-Snowflake SQL dialect differences during migration
- The importance of a migration runbook documenting every decision for auditability

## Devin Features Exercised

- Complex XML parsing and transformation logic extraction
- Cross-platform SQL translation (Oracle to Snowflake)
- ETL architecture redesign (batch PowerCenter to cloud-native Snowflake)
- Documentation generation (migration runbook)
- Multi-repo awareness (referencing Snowflake DDL patterns from companion repo)
- PR creation with migration artifacts

## Difficulty

Advanced

## Estimated Time

75 minutes

---

## <a id="ts-informatica-powercenter"></a>ts-informatica-powercenter

**Repository:** [ts-informatica-powercenter](https://github.com/Cognition-Partner-Workshops/ts-informatica-powercenter)

Informatica PowerCenter 9.6.1 XML exports for a government HR data integration system (EHRP-to-BIIS). Contains 11 mapping exports in the `XML/` directory with Oracle source/target definitions, Expression/Filter/Lookup/Aggregator transformations, sessions, and workflows. Also includes Oracle SQL pre/post-load scripts (`ehrp2biis_preload`, `ehrp2biis_afterload.sql`) and shell-based `pmcmd` orchestration in `Transfer Scripts/`.

### Step 1: Paste into Devin

```
Analyze the PowerCenter XML exports in ts-informatica-powercenter/XML/ — focus on the CPM mapping (the largest and most complex). Extract all SOURCE definitions and create equivalent Snowflake DDL (CREATE TABLE statements with appropriate Snowflake data types replacing Oracle types). Then extract the MAPPING transformation chain and re-implement the ETL logic as a Snowflake stored procedure. For flat-file sources (VSAM/CSV defined in the XML), create a Snowflake Stage and COPY INTO loading pattern. Convert the pre/post-load Oracle SQL (ehrp2biis_preload, ehrp2biis_afterload.sql) to Snowflake stored procedures. Create an INFORMATICA_MIGRATION_RUNBOOK.md documenting each translation.
```

### Step 2: Research with Ask Devin

- *"What Oracle-specific SQL features are used in the pre/post-load scripts (ehrp2biis_preload, ehrp2biis_afterload.sql)? Which ones need special handling for Snowflake (e.g., PL/SQL procedures, sequences, SPOOL commands)?"*
- *"What transformation types does the CPM mapping use, and what's the best Snowflake equivalent for each — inline SQL, stored procedure, or dbt model?"*
- Use the analysis to decide whether a stored-procedure or dbt approach is more appropriate for each mapping's complexity

### Step 3 (Optional): Read the DeepWiki

Open the DeepWiki page for both ts-informatica-powercenter and uc-dw-migration-teradata-to-snowflake. Use the Teradata-to-Snowflake migration as a reference for Snowflake DDL conventions and validation patterns, then apply them to the Informatica migration.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Snowflake DDL correctly translate Oracle data types? Do the stored procedures preserve the transformation logic from the PowerCenter mappings?
- **Leave a comment** asking Devin to also convert the remaining mappings (CPM_AFPS, CPM_CDC, CPM_NIH, CPM_OIG) and add Snowflake Task definitions for orchestration
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Non-invasive static analysis**: Devin extracts the full ETL logic from PowerCenter XML and Oracle SQL scripts — no Informatica server, no Oracle database, no Snowflake account required for the conversion work
- **Multi-layer migration**: This exercise covers ETL logic (PowerCenter → Snowflake stored procedures), database platform (Oracle → Snowflake DDL), and orchestration (pmcmd → Snowflake Tasks) — all from static artifacts
- **Parallel migration workstreams**: Each PowerCenter mapping can be converted independently, making this a strong candidate for [child-session parallelism](../../reference/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents)

---

## <a id="uc-dw-migration-teradata-to-snowflake"></a>uc-dw-migration-teradata-to-snowflake

**Repository:** [uc-dw-migration-teradata-to-snowflake](https://github.com/Cognition-Partner-Workshops/uc-dw-migration-teradata-to-snowflake)

Teradata-based retail banking analytics data warehouse with Snowflake migration artifacts. Used here as a **reference** for Snowflake DDL conventions, validation query patterns, and migration runbook structure — not as the primary challenge target.

### How to Use as Reference

```
When working on the Informatica-to-Snowflake migration, review the Snowflake DDL and validation queries in uc-dw-migration-teradata-to-snowflake for established patterns. Apply the same conventions (naming, data types, clustering keys) and validation approach (row counts, checksums, business-level reconciliations) to the Informatica migration artifacts.
```

---

## Going Further

### Automation and Webhooks

Set up a CI trigger so that whenever new PowerCenter XML exports are added, a Devin session automatically generates the Snowflake equivalent DDL, stored procedures, and orchestration tasks. See [Design Patterns → Event-Driven Triggers](../../reference/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers).

### Child Sessions for Scale

For large Informatica estates, use the [divide-and-conquer pattern](../../reference/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents): a parent session inventories all mappings and categorizes them by complexity, then spawns a child session per mapping. Each child extracts the transformation logic, generates Snowflake equivalents, and opens its own PR.

### Scheduled Sessions

Schedule a recurring Devin session to re-validate migrated Snowflake stored procedures against the original PowerCenter XML — catching semantic drift if either side changes during the migration project. See [Platform Capabilities → Scheduled Sessions](../../reference/general-themes/platform-capabilities.md#scheduled-sessions).

### Shared Context Layer

Create organization-level [knowledge notes](../../reference/general-themes/architecture-strengths.md#shared-context-layer) documenting your Informatica-to-Snowflake translation standards (e.g., "Lookup Transformations → LEFT JOIN", "Expression Transformations with Oracle functions → Snowflake UDFs", "pmcmd orchestration → Snowflake Task DAG"). Every Devin session inherits these conventions.

### Team-Based Operation

ETL developers familiar with PowerCenter, DBAs experienced with Snowflake, and migration architects can all review the PR simultaneously. Devin reads feedback from any reviewer and iterates. See [Collaboration Model](../../reference/general-themes/collaboration-model.md).
