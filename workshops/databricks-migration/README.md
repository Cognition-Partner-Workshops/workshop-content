# Workshop: Databricks Platform Migration

## Overview

| | |
|---|---|
| **Focus** | Converting standard data engineering code (PySpark, Airflow, dbt, SQL ETL) to Databricks Lakehouse — notebooks, Delta Lake, Workflows, Unity Catalog |
| **Duration** | Flexible (2 tracks, 3 labs each) |
| **Audience** | Databricks users, data engineers, analytics engineers, data platform teams |
| **Key Modules** | [Databricks Platform Migration](../../modules/data-engineering/databricks-platform-migration.md), [Data Source Migration](../../modules/data-engineering/data-source-migration.md) |

## Workshop Narrative

Every Databricks team inherits code that wasn't built for Databricks — PySpark scripts submitted via `spark-submit`, Airflow DAGs, dbt models targeting BigQuery or Snowflake, legacy ETL stored procedures, and CSV/Parquet file dumps. This workshop shows how Devin converts each of these to Databricks-native equivalents: notebooks with Delta Lake, Databricks Workflows, dbt-databricks models, and Unity Catalog schemas.

The two tracks cover different migration stories:
- **Track A** starts from a modern open-source data engineering stack (PySpark + Airflow + dbt + GCS/BigQuery) and converts it piece by piece to the Databricks Lakehouse
- **Track B** starts from legacy enterprise data (all-VARCHAR CDW tables, SQL-based ETL mappings) and generates Databricks-ready ingestion, transformation, and validation code

Both tracks emphasize Devin's ability to understand source code across languages and platforms, then generate idiomatic Databricks artifacts — not just syntactic translations, but architecture-aware conversions.

## Getting the Most from This Workshop

> **Parallelization is the highlight.** Track A is designed for running 3 Devin sessions simultaneously — one per pipeline layer (streaming, orchestration, transformation). This mirrors real migration projects where teams split work by component. Kick off all three in Step 1, then use the wait time for Ask Devin research.

A few tips:
- **Start sessions early, review later.** Kick off the Devin sessions in Step 1, then use Step 2 (Ask Devin) and Step 3 (DeepWiki) while Devin works.
- **Try parallel sessions in Track A.** Each lab targets a different code path in the same repo — no merge conflicts.
- **Leave PR comments to steer.** After Devin opens a PR, ask it to add Delta Live Tables, Unity Catalog governance, or Databricks Asset Bundles.

---

## Table of Contents

- [Track A: Open-Source Stack → Databricks Lakehouse](#track-a-open-source-stack--databricks-lakehouse)
  - [Lab A1 — PySpark Streaming → Databricks Notebooks + Delta Lake](#lab-a1--pyspark-streaming--databricks-notebooks--delta-lake)
  - [Lab A2 — Airflow DAGs → Databricks Workflows](#lab-a2--airflow-dags--databricks-workflows)
  - [Lab A3 — dbt-BigQuery → dbt-Databricks](#lab-a3--dbt-bigquery--dbt-databricks)
- [Track B: Legacy Data → Databricks Lakehouse](#track-b-legacy-data--databricks-lakehouse)
  - [Lab B1 — Legacy CDW → Delta Lake Migration](#lab-b1--legacy-cdw--delta-lake-migration)
  - [Lab B2 — SQL ETL → PySpark Notebooks](#lab-b2--sql-etl--pyspark-notebooks)
  - [Lab B3 — Data Quality Framework for Databricks](#lab-b3--data-quality-framework-for-databricks)
- [Additional Challenges](#additional-challenges)
- [Suggested Formats](#suggested-formats)
- [Repos Required](#repos-required)
- [Key Takeaways](#key-takeaways)
- [Devin Features Checklist](#devin-features-checklist)

---

## Track A: Open-Source Stack → Databricks Lakehouse

Track A demonstrates Devin converting a complete open-source data engineering stack to Databricks-native code. The source is `streamify-data-engineering` — a music streaming pipeline with PySpark Structured Streaming, Airflow hourly batch DAGs, and dbt star schema models targeting BigQuery.

**Run Labs A1, A2, and A3 as parallel Devin sessions** — they target different code paths in the same repo and produce separate PRs with no conflicts.

### Lab A1 — PySpark Streaming → Databricks Notebooks + Delta Lake

- **Module:** [Databricks Platform Migration](../../modules/data-engineering/databricks-platform-migration.md)
- **Repository:** [streamify-data-engineering](https://github.com/Cognition-Partner-Workshops/streamify-data-engineering) — PySpark Structured Streaming code consuming Kafka events and writing Parquet to GCS
- **Objective:** Convert standard PySpark streaming scripts into Databricks notebooks that write to Delta Lake tables using Auto Loader

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
In streamify-data-engineering, convert the PySpark streaming code (spark_streaming/stream_all_events.py, spark_streaming/streaming_functions.py, spark_streaming/schema.py) into Databricks notebooks that write to Delta Lake tables instead of Parquet on GCS. Use Auto Loader (cloudFiles) for incremental ingestion. Add markdown cells explaining each step. Place output in databricks/notebooks/. Include a STREAMING_MIGRATION.md documenting the conversion decisions. Open a PR.
```

#### Step 2: Research with Ask Devin

While Devin works on the conversion, explore:
- *"What GCS-specific code patterns in streamify-data-engineering/spark_streaming/ need to change for Databricks? List every reference to GCS, Parquet output paths, and checkpoint locations."*
- *"What's the difference between using Auto Loader (cloudFiles) vs Structured Streaming readStream for the Kafka consumer in Databricks? When should I use which?"*
- *"What Delta Lake features (OPTIMIZE, Z-ORDER, liquid clustering) would improve query performance for high-volume event data?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the streaming architecture: Eventsim event generation, Kafka topic structure, Spark streaming functions, and the Parquet partitioning scheme (month/day/hour). Verify Devin's notebooks preserve all data flows.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the notebook correctly replace GCS paths with Delta Lake tables? Is the Auto Loader configuration correct?
- **Leave a comment** asking Devin to add a Delta Live Tables (DLT) pipeline as an alternative implementation
- **Watch Devin respond** and push DLT Python pipeline code

- **Key Takeaways:**
  - **"Same Spark, different platform"** — Devin understands PySpark idioms and retargets them to Databricks-native APIs (Delta Lake, Auto Loader)
  - **"Architecture, not just syntax"** — the conversion adds Delta Lake features (ACID transactions, time travel, schema enforcement) that didn't exist in the Parquet-based original

- **Target Outcomes:**
  - Databricks notebooks with Delta Lake writes replacing Parquet/GCS
  - Auto Loader configuration for incremental file ingestion
  - `STREAMING_MIGRATION.md` with conversion rationale
  - PR with review comments and Devin's responses

---

### Lab A2 — Airflow DAGs → Databricks Workflows

- **Module:** [Databricks Platform Migration](../../modules/data-engineering/databricks-platform-migration.md)
- **Repository:** [streamify-data-engineering](https://github.com/Cognition-Partner-Workshops/streamify-data-engineering) — Airflow DAGs for hourly batch loading from GCS to BigQuery and dbt execution
- **Objective:** Convert Airflow orchestration into Databricks Workflow JSON definitions with equivalent task dependencies and scheduling

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
In streamify-data-engineering, convert the Airflow orchestration (airflow/dags/streamify_dag.py, airflow/dags/load_songs_dag.py, airflow/dags/dbt_test_dag.py, airflow/dags/task_templates.py) into Databricks Workflow JSON definitions. Map each Airflow task to equivalent Databricks notebook or SQL tasks with proper dependency chains and scheduling. Replace BigQuery operators with Databricks SQL tasks. Place output in databricks/workflows/. Include an ORCHESTRATION_MIGRATION.md documenting the DAG-to-Workflow mapping. Open a PR.
```

#### Step 2: Research with Ask Devin

- *"How does the streamify Airflow DAG define task dependencies? What's the execution order and what does each task do?"*
- *"What Databricks Workflow features (task dependencies, conditional execution, retry policies) map to Airflow concepts like catchup, max_active_runs, and trigger rules?"*
- *"Should the dbt tasks in the Workflow use a Databricks SQL task, a notebook task running dbt-core, or the dbt Cloud integration?"*

#### Step 3 (Optional): Read the DeepWiki

Review the Airflow DAG structure, task templates, and SQL queries used for BigQuery loading. Understand the scheduling cadence and data partitioning scheme that the Workflow must preserve.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Workflow JSON correctly represent the DAG's task dependency graph? Is the scheduling equivalent?
- **Leave a comment** asking Devin to add Databricks Asset Bundle (DAB) configuration for deploying the Workflow via CI/CD

- **Key Takeaways:**
  - **"Orchestration is portable"** — Devin maps Airflow concepts (DAGs, operators, XCom) to Databricks equivalents (Workflows, tasks, job parameters)
  - **"Eliminate the Airflow server"** — Databricks Workflows provide native orchestration without managing separate infrastructure

- **Target Outcomes:**
  - Databricks Workflow JSON definitions with task dependencies
  - Scheduling configuration matching the original Airflow cadence
  - `ORCHESTRATION_MIGRATION.md` with DAG-to-Workflow mapping
  - PR with review comments

---

### Lab A3 — dbt-BigQuery → dbt-Databricks

- **Module:** [Databricks Platform Migration](../../modules/data-engineering/databricks-platform-migration.md)
- **Repository:** [streamify-data-engineering](https://github.com/Cognition-Partner-Workshops/streamify-data-engineering) — dbt project with dimensional models (dims + facts) targeting BigQuery
- **Objective:** Retarget the dbt project from the BigQuery adapter to dbt-databricks with Delta Lake materializations

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
In streamify-data-engineering, convert the dbt project (dbt/) from the BigQuery adapter to dbt-databricks. Update profiles.yml for a Databricks SQL Warehouse connection, convert all models in dbt/models/core/ (dim_users, dim_songs, dim_artists, dim_location, dim_datetime, fact_streams) to use Delta Lake materializations, replace BigQuery-specific SQL (CAST, date_trunc, partition_by timestamp granularity) with Databricks SQL equivalents, and update dbt_project.yml. Place output in databricks/dbt/. Include a DBT_MIGRATION.md documenting syntax changes. Open a PR.
```

#### Step 2: Research with Ask Devin

- *"What BigQuery-specific SQL syntax and dbt materializations are used in the streamify dbt models? Which ones need changes for Databricks?"*
- *"What are the key differences between dbt-bigquery and dbt-databricks adapters? What config options change?"*
- *"Should the fact_streams model use incremental materialization with merge strategy on Databricks? What's the performance tradeoff vs full refresh?"*

#### Step 3 (Optional): Read the DeepWiki

Review the dimensional model: how dim tables are built (SCD Type 2 for users), how fact_streams joins all dimensions, and the BigQuery-specific partition_by configuration. Understand what must change for Delta Lake semantics.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the dbt models compile with the Databricks adapter? Are materializations appropriate (table vs incremental)?
- **Leave a comment** asking Devin to add dbt tests for data quality (unique, not_null, relationships) and a sources.yml for Unity Catalog

- **Key Takeaways:**
  - **"dbt is portable — mostly"** — the core SQL is transferable but materializations, partitioning, and adapter-specific configs need updating
  - **"Delta Lake adds capabilities"** — schema enforcement, MERGE for incrementals, and time travel are available once models target Delta

- **Target Outcomes:**
  - dbt-databricks project with updated profiles, models, and materializations
  - `DBT_MIGRATION.md` documenting syntax changes
  - PR with review comments

---

## Track B: Legacy Data → Databricks Lakehouse

Track B demonstrates Devin generating Databricks-ready migration code from legacy enterprise data sources. These labs are independent — run them sequentially or in parallel.

### Lab B1 — Legacy CDW → Delta Lake Migration

- **Module:** [Databricks Platform Migration](../../modules/data-engineering/databricks-platform-migration.md)
- **Repository:** [uc-data-source-migration-legacy-to-modern](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-legacy-to-modern) — Spring Boot loan app with legacy CDW (all-VARCHAR, cryptic column names)
- **Objective:** Generate Delta Lake DDL, PySpark ingestion notebooks, and a data quality framework from a legacy corporate data warehouse

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Analyze the legacy CDW schema in uc-data-source-migration-legacy-to-modern. Review the schema in src/main/resources/schema-legacy.sql, seed data in src/main/resources/data-legacy.sql, and column mappings in data/mappings/column_mappings.md.

Generate a complete Databricks migration pipeline:

1. Delta Lake DDL (databricks/ddl/): Create Delta Lake CREATE TABLE statements for the modern target schema with proper Spark SQL types (DATE, DECIMAL, STRING), meaningful column names, and partitioning strategy.

2. PySpark Ingestion (databricks/ingestion/): Write PySpark notebooks that read from legacy tables, parse dates and amounts, expand status codes (ACT→Active, CLO→Closed, DFT→Default, FRB→Forbearance), split denormalized fields into dimension tables, and handle nulls with logging.

3. Data Quality Framework (databricks/quality/): Create validation notebooks checking row counts, null constraints, referential integrity, and business rules. Generate a DATA_QUALITY_REPORT.md.

4. Migration Runbook (docs/DATABRICKS_MIGRATION_RUNBOOK.md): Document transformation decisions, column mappings, type conversions, and execution order.

Open a PR.
```

#### Step 2: Research with Ask Devin

- *"What are the riskiest type conversions from all-VARCHAR columns to typed Delta Lake columns? What edge cases should the ingestion handle?"*
- *"What's the best approach for incremental loads after the initial migration — Delta Lake Change Data Feed, MERGE, or full refresh?"*
- *"What Unity Catalog schema layout makes sense for a loan management domain — one schema per layer (bronze/silver/gold) or per business domain?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the legacy schema, column mapping rules, and the application's data access patterns. Verify Devin's Delta Lake schema design preserves the domain semantics.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the Delta Lake types appropriate? Does the ingestion handle edge cases in the seed data?
- **Leave a comment** asking Devin to add a Medallion Architecture layer (Bronze → Silver → Gold) to the ingestion pipeline

- **Key Takeaways:**
  - **"Legacy to lakehouse"** — Devin reads a legacy CDW schema and produces production-ready Databricks artifacts
  - **"Cross-language generation"** — Devin reads Java/SQL and produces PySpark/Delta Lake code

- **Target Outcomes:**
  - Delta Lake DDL with proper types and partitioning
  - PySpark ingestion notebooks with transformation logic
  - Data quality validation framework
  - Migration runbook
  - PR with review comments

---

### Lab B2 — SQL ETL → PySpark Notebooks

- **Module:** [Databricks Platform Migration](../../modules/data-engineering/databricks-platform-migration.md)
- **Repository:** [etl-workflow](https://github.com/Cognition-Partner-Workshops/etl-workflow) — FastAPI ETL app with YAML mapping specs executing as PostgreSQL INSERT...SELECT
- **Objective:** Convert declarative YAML-defined SQL ETL mappings into PySpark Databricks notebooks with Delta Lake targets

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Analyze the ETL mapping specifications in etl-workflow/mapping_specs/. These YAML files define five data transformations (employee directory enrichment, department roster, salary analytics, manager hierarchy, title progression) that currently execute as PostgreSQL INSERT...SELECT statements.

Convert each mapping to a Databricks notebook:

1. Databricks Notebooks (databricks/notebooks/): For each YAML spec, create a PySpark notebook that reads from Delta Lake source tables, applies the same joins/filters/aggregations/window functions, and writes to Delta Lake target tables. Add markdown cells documenting the transformation logic.

2. Delta Lake DDL (databricks/ddl/): Create source and target table definitions in Spark SQL with appropriate types and partitioning.

3. Databricks Workflow (databricks/workflows/workflow.json): Define a Workflow that runs the notebooks in dependency order.

4. Migration Notes (docs/ETL_TO_DATABRICKS.md): Document the PostgreSQL → Spark SQL translation decisions.

Open a PR.
```

#### Step 2: Research with Ask Devin

- *"What SQL patterns in the etl-workflow mapping specs are PostgreSQL-specific and need Spark SQL equivalents?"*
- *"How do the YAML mapping specs define joins, filters, and expressions? What's the most complex transformation?"*

#### Step 3 (Optional): Read the DeepWiki

Review the mapping spec format, the PostgreSQL source schema (employees database), and the transformation patterns (window functions, self-joins, aggregations).

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the PySpark notebooks produce equivalent results to the SQL versions? Are window functions correctly translated?
- **Leave a comment** asking Devin to add data profiling statistics comparing source and target row counts

- **Key Takeaways:**
  - **"SQL is portable — with translation"** — Devin converts PostgreSQL-specific syntax to Spark SQL and wraps it in Databricks notebooks
  - **"YAML specs → executable notebooks"** — Devin reads declarative ETL definitions and generates runnable code

- **Target Outcomes:**
  - PySpark notebooks for each ETL mapping
  - Delta Lake source and target DDL
  - Databricks Workflow definition
  - PostgreSQL → Spark SQL migration notes

---

### Lab B3 — Data Quality Framework for Databricks

- **Module:** [Data Quality & Validation](../../modules/data-engineering/data-quality-validation.md)
- **Repository:** [uc-data-source-migration-legacy-to-modern](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-legacy-to-modern)
- **Objective:** Build a reusable PySpark data quality framework that validates Delta Lake tables after migration

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
In uc-data-source-migration-legacy-to-modern, create a reusable Databricks data quality framework:

1. Quality Check Library (databricks/quality/checks.py): A PySpark module with reusable functions for row count validation, null checks, uniqueness checks, referential integrity, range/format validation, and cross-table reconciliation.

2. Quality Notebook (databricks/quality/run_quality_checks.py): A Databricks notebook that imports the library and runs all checks against the loan data tables. Use Databricks widgets for parameterizing table names and thresholds.

3. Alert Integration (databricks/quality/alerts.py): Generate Databricks SQL alert definitions that trigger on quality check failures.

4. Quality Dashboard SQL (databricks/quality/dashboard.sql): Databricks SQL queries for a quality monitoring dashboard showing pass/fail trends over time.

Open a PR.
```

#### Step 2: Research with Ask Devin

- *"What data quality patterns are most important for financial/loan data? What regulatory requirements apply?"*
- *"How do Databricks SQL alerts work? What's the best way to integrate quality checks into a Workflow?"*

- **Key Takeaways:**
  - **"Quality is a first-class citizen"** — Devin generates validation frameworks, not just migration code
  - **"Databricks-native monitoring"** — SQL alerts and dashboards provide ongoing quality visibility

- **Target Outcomes:**
  - Reusable PySpark quality check library
  - Parameterized validation notebook
  - Databricks SQL alerts and dashboard queries

---

## Additional Challenges

These can be assigned as follow-up exercises after the main labs:

| Exercise | Description | Repo |
|----------|-------------|------|
| **Delta Live Tables** | Convert Lab A1 notebooks to a DLT pipeline with expectations (data quality constraints) | streamify-data-engineering |
| **Unity Catalog Governance** | Add column-level tags, row filters, and column masks to the Delta Lake tables from Lab B1 | uc-data-source-migration-legacy-to-modern |
| **Databricks Asset Bundles** | Package the Workflows from Lab A2 as a DAB project with CI/CD deployment config | streamify-data-engineering |
| **Medallion Architecture** | Restructure the Lab B1 ingestion into Bronze (raw) → Silver (cleansed) → Gold (business) layers | uc-data-source-migration-legacy-to-modern |
| **MLflow Integration** | Add an ML model training notebook that uses the fact_streams data to predict user churn, tracked with MLflow | streamify-data-engineering |

---

## Scheduled Maintenance: Ongoing Databricks O&M

These prompts are designed for **scheduled Devin sessions** — recurring tasks that run daily or weekly to maintain Databricks estate health. Set them up as scheduled sessions and review the PRs each morning.

### Delta Lake Table Maintenance (Weekly)

```
Run OPTIMIZE and VACUUM on all Delta tables in the catalog. Check for tables with >1000 small files, run OPTIMIZE with Z-ORDER on high-query columns. Run VACUUM with 7-day retention. Generate a TABLE_MAINTENANCE_REPORT.md with before/after file counts and sizes. Open a PR if any table definitions need TBLPROPERTIES updates.
```

### dbt Model Drift Detection (Daily)

```
Compare the dbt models in databricks/dbt/ against the actual Delta Lake table schemas in Unity Catalog. Flag any schema drift (columns added/removed in the warehouse but not in the dbt model, type mismatches, stale materializations). Generate a DRIFT_REPORT.md and fix any dbt models that are out of sync. Open a PR.
```

### Notebook Code Quality & Linting (Weekly)

```
Scan all Databricks notebooks in databricks/notebooks/ for code quality issues: hardcoded credentials, deprecated Spark APIs (e.g., spark.sql.legacy.*), missing error handling, inefficient patterns (collect() on large DataFrames, UDFs that could be native functions). Generate a CODE_QUALITY_REPORT.md with findings and severity. Fix all HIGH severity issues. Open a PR.
```

### Dependency & Runtime Hygiene (Weekly)

```
Audit the cluster library dependencies and init scripts. Check for outdated PyPI packages, deprecated Spark configs, and unused libraries. Compare against the latest Databricks Runtime LTS version and flag incompatibilities. Generate an UPGRADE_PLAN.md. Open a PR updating requirements files.
```

### Data Quality Monitoring (Daily)

```
Run the data quality framework in databricks/quality/ against all Delta tables. Check row counts vs expected ranges, null rates, freshness (last update timestamp), referential integrity. Generate a weekly DATA_QUALITY_REPORT.md. If any check fails, open a PR with a fix or flag for human review.
```

**The narrative:** "5 scheduled Devin sessions run overnight, each maintaining a different aspect of the Databricks estate. You come in the morning to PRs." This demonstrates Devin as an always-on platform engineer, not just a migration tool.

---

## Suggested Formats

| Format | Duration | Labs | Notes |
|--------|----------|------|-------|
| **Quick tryout** | 45 min | A1 only | Single conversion, fastest path to a PR |
| **Parallelization showcase** | 60 min | A1 + A2 + A3 (parallel) | Three simultaneous sessions, one repo — the headline act |
| **Half-day workshop** | 2-3 hours | Track A (parallel) + Lab B1 | Full platform migration story |
| **Full-day workshop** | 4-6 hours | Track A + Track B + additional challenges | Complete hands-on experience |

---

## Repos Required

- [ ] streamify-data-engineering
- [ ] uc-data-source-migration-legacy-to-modern
- [ ] etl-workflow

---

## Key Takeaways

- **"One pipeline, three Devins, three PRs"** — parallel sessions convert streaming, orchestration, and transformation layers simultaneously
- **"Platform migration, not just syntax translation"** — Devin generates Databricks-native artifacts (Delta Lake, Auto Loader, Workflows) rather than just porting code line by line
- **"Legacy data has a Databricks path"** — even all-VARCHAR CDW tables with cryptic names get converted to properly typed Delta Lake schemas with quality checks
- **"dbt is portable — mostly"** — the transformation layer transfers between warehouses, but materializations and adapter configs need Databricks-specific updates
- **"Quality comes with the migration"** — Devin generates validation frameworks alongside the migration code, not as an afterthought

---

## Devin Features Checklist

| Feature | Where It Appears |
|---------|-----------------|
| Codebase analysis | All labs — Devin reads PySpark, Airflow, dbt, SQL, YAML |
| Cross-platform translation | A1 (GCS→Delta), A2 (Airflow→Workflows), A3 (BigQuery→Databricks SQL), B2 (PostgreSQL→Spark SQL) |
| Parallel sessions | Track A — three sessions on the same repo |
| PR creation | All labs |
| PR review & iteration | All Step 4s — leave comments, Devin responds |
| Ask Devin (research) | All Step 2s — architecture planning before/during sessions |
| DeepWiki | All Step 3s — understand source architecture |
| Documentation generation | Migration plans, runbooks, quality reports |
| Multi-file generation | All labs — notebooks, DDL, workflows, docs in one PR |
| Knowledge building | Accept Knowledge suggestions during sessions for Databricks conventions |
