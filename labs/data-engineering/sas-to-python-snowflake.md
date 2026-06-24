# SAS to Python/Snowflake

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [ts-sas-legacy-analytics](#ts-sas-legacy-analytics)
- [uc-data-migration-sas-to-snowflake](#uc-data-migration-sas-to-snowflake)
- [Going Further](#going-further)

---

## Challenge

Convert SAS analytics programs and data transformations to Python and Snowflake SQL equivalents. This is a cross-language migration that tests Devin's ability to understand legacy SAS syntax (DATA steps, PROC operations, macro variables) and produce equivalent modern code with proper validation.

This exercise uses **non-invasive static analysis**: Devin reads the SAS macro source files and sample datasets in the repository — no SAS license, no running SAS environment, and no modifications to the legacy system are required. The sample datasets in both SAS7BDAT and CSV formats provide validation baselines.

## Quick Start

1. Open Devin and connect to both **ts-sas-legacy-analytics** and **uc-data-migration-sas-to-snowflake** repositories
2. Paste the [Step 1 prompt](#step-1-paste-into-devin) into Devin
3. Watch Devin translate SAS macros to Python functions with pytest validation, and open a PR

## Repositories

- [ts-sas-legacy-analytics](#ts-sas-legacy-analytics)
- [uc-data-migration-sas-to-snowflake](#uc-data-migration-sas-to-snowflake)

---

## Target Outcomes

- Python equivalents of SAS DATA step and PROC operations using pandas/polars
- Snowflake SQL translations of SAS data transformations for the sample datasets (CUST_ACCOUNTS, DAILY_BALANCE, MONTHLY_AMB)
- Validation tests comparing SAS and Python/SQL outputs for correctness
- PR with converted code, Snowflake DDL, and a `SAS_MIGRATION_NOTES.md` documenting translation decisions

## What Participants Will Learn

- How Devin reads and understands legacy SAS syntax including macros and PROC steps
- How Devin translates SAS data operations to equivalent Python (pandas) and SQL
- How Devin handles SAS-specific constructs (macro variables, formats, informats, BY-group processing)
- The importance of output validation when migrating between analytics platforms

## Devin Features Exercised

- Cross-language translation (SAS to Python/SQL)
- Legacy code understanding and SAS syntax comprehension
- Data transformation logic mapping across paradigms
- PR creation with migration documentation

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="ts-sas-legacy-analytics"></a>ts-sas-legacy-analytics

**Repository:** [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics)

Legacy SAS codebase with 90+ macros covering data export, transformation, deduplication, and formatting operations. The `Macro/` directory contains production-style SAS macros that represent typical enterprise analytics workflows.

### Step 1: Paste into Devin

```
Analyze the SAS macros in ts-sas-legacy-analytics/Macro/ — focus on the data transformation macros: transpose.sas, subset_data.sas, compare.sas, dedup_string.sas, dedup_mstring.sas, and the export family (export_csv.sas, export_xlsx.sas, export_dbms.sas). For each macro, translate the SAS logic into equivalent Python functions using pandas. Preserve the same function signatures (input dataset, parameters, output dataset). Create pytest tests that validate the Python functions produce the same results as the SAS originals for sample inputs. Document each translation decision in a SAS_TO_PYTHON_TRANSLATION.md.
```

### Step 2: Research with Ask Devin

- *"What SAS-specific features are used in the Macro/ directory of ts-sas-legacy-analytics? Which macros use SAS-only constructs like PROC TRANSPOSE, BY-group processing, or macro variable resolution that need special handling in Python?"*
- *"What's the best Python library strategy for converting these SAS macros — pandas for everything, or should some use polars or PySpark for better performance with large datasets?"*
- Use the analysis to prioritize which macros to convert first based on complexity and reusability

### Step 3 (Optional): Read the DeepWiki

Open the DeepWiki page for ts-sas-legacy-analytics to understand the macro library organization. Identify which macros are data transformation primitives (transpose, subset, dedup) vs I/O utilities (export, import) to plan the conversion in dependency order.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the Python functions handle the same edge cases as the SAS macros (missing values, empty datasets, special SAS missing value indicators)?
- **Leave a comment** asking Devin to add type hints and docstrings to all converted functions, following NumPy docstring conventions
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Non-invasive analysis**: Devin reads SAS macro source files to understand transformation logic — no SAS license, no running SAS environment needed
- **Construct-level mapping**: Every SAS pattern (PROC TRANSPOSE, BY-group processing, macro variable resolution) maps to a documented Python equivalent
- **Parallel conversion potential**: With 90+ macros, a [child-session pattern](../../reference/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents) can convert groups of related macros in parallel — one Devin session per macro category

---

## <a id="uc-data-migration-sas-to-snowflake"></a>uc-data-migration-sas-to-snowflake

**Repository:** [uc-data-migration-sas-to-snowflake](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-snowflake)

SAS-to-Snowflake migration toolkit with sample banking datasets (CUST_ACCOUNTS, DAILY_BALANCE, MONTHLY_AMB) in both SAS7BDAT and CSV formats in `sample_data/`, SAS lineage metadata in `lineage/`, validation configurations, and a Streamlit migration app. Includes two migration scenarios (`sample_data/Scenario1/`, `sample_data/Scenario2/`) with before/after data snapshots.

### Step 1: Paste into Devin

```
Analyze the sample datasets in uc-data-migration-sas-to-snowflake/sample_data/ — examine the CSV files (CUST_ACCOUNTS.csv, DAILY_BALANCE.csv, MONTHLY_AMB.csv) and the two migration scenarios in Scenario1/ and Scenario2/. Create Snowflake-compatible DDL for each dataset with proper column types, constraints, and clustering keys. Then write Python scripts that: (1) read the SAS7BDAT files using the sas7bdat or pyreadstat library, (2) apply any necessary data type conversions (dates, decimals, string encoding), (3) generate Snowflake COPY INTO statements for bulk loading, and (4) create validation queries comparing row counts and checksums between source and target. Include a SAS_TO_SNOWFLAKE_MIGRATION.md documenting the schema mapping and loading strategy.
```

### Step 2: Research with Ask Devin

- *"What are the schema differences between Scenario1 and Scenario2 in uc-data-migration-sas-to-snowflake/sample_data/? What data changes do the scenarios represent (schema evolution, data corrections, incremental loads)?"*
- *"What SAS-specific data types and formats in the SAS7BDAT files need special handling when loading into Snowflake? How should SAS date values (days since 1960-01-01) be converted?"*
- Use the analysis to build a more robust migration pipeline that handles both scenarios

### Step 3 (Optional): Read the DeepWiki

Open the DeepWiki page for uc-data-migration-sas-to-snowflake to understand the lineage metadata structure and validation configuration. Examine how the existing `lineage/SAS_lineage.json` maps data flows from SAS to Snowflake and use this to validate your migration covers all documented data paths.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the Snowflake DDL types correct for the SAS source data? Do the COPY INTO statements handle CSV quoting and null values properly?
- **Leave a comment** asking Devin to add an incremental load strategy that handles Scenario1-to-Scenario2 as a delta migration instead of full reload
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Non-invasive analysis**: Devin reads SAS7BDAT files and CSV snapshots to understand schemas and data patterns — no SAS server, no Snowflake account needed for the conversion work
- **SAS date handling**: SAS stores dates as days since January 1, 1960 — Devin handles this conversion to standard date types automatically
- **Scenario-based validation**: The two migration scenarios provide a built-in validation framework — Devin can verify that the migration pipeline handles both schema snapshots correctly

---

## Going Further

### Automation and Webhooks

Set up a CI trigger so that whenever new SAS datasets are added to the repository, a Devin session automatically generates the Snowflake DDL, loading scripts, and validation queries. See [Design Patterns → Event-Driven Triggers](../../reference/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers).

### Child Sessions for Scale

For large SAS estates with hundreds of datasets, use the [divide-and-conquer pattern](../../reference/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents): a parent session inventories all datasets and groups them by domain, then spawns a child session per group. Each child generates DDL, loading scripts, and validation queries independently.

### Scheduled Sessions

Schedule a recurring Devin session to re-validate the migration pipeline against the latest dataset snapshots — catching drift if schemas evolve upstream. See [Platform Capabilities → Scheduled Sessions](../../reference/general-themes/platform-capabilities.md#scheduled-sessions).

### Shared Context Layer

Create organization-level [knowledge notes](../../reference/general-themes/architecture-strengths.md#shared-context-layer) documenting your SAS-to-Snowflake migration conventions (e.g., "SAS date values → DATE with epoch 1960-01-01 conversion", "SAS7BDAT string encoding → UTF-8 normalization", "PROC FORMAT values → Snowflake lookup tables"). Every Devin session inherits these conventions.

### Team-Based Operation

SAS analysts familiar with the data semantics and Snowflake engineers experienced with DDL patterns can review the migration PR simultaneously. Devin reads feedback from any reviewer and iterates. See [Collaboration Model](../../reference/general-themes/collaboration-model.md).
