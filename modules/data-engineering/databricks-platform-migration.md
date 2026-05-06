# Databricks Platform Migration

## Repositories

- [streamify-data-engineering](#streamify-data-engineering)
- [uc-data-source-migration-legacy-to-modern](#uc-data-source-migration-legacy-to-modern)
- [etl-workflow](#etl-workflow)

---

## Challenge

Migrate a standard open-source data engineering stack (PySpark scripts, Airflow DAGs, dbt models targeting BigQuery/GCS) to the Databricks Lakehouse platform. Participants will convert existing pipeline code to Databricks Notebooks, replace file-based sinks with Delta Lake tables, translate Airflow orchestration to Databricks Workflows, and retarget dbt models from BigQuery to the dbt-databricks adapter.

## Platform Conversion Map

| Source Component | Databricks Target |
|---|---|
| PySpark scripts via `spark-submit` | Databricks Notebooks (Python) |
| Parquet files on GCS / local disk | Delta Lake tables in Unity Catalog |
| Airflow DAGs (scheduling + dependencies) | Databricks Workflows (JSON task definitions) |
| dbt-bigquery models (SQL + Jinja) | dbt-databricks models with Delta materialization |
| BigQuery staging / external tables | Databricks SQL warehouses + Unity Catalog schemas |
| GCP Terraform modules | Databricks Terraform provider resources |
| `spark-submit` with `--packages` | Databricks cluster libraries + init scripts |

## Target Outcomes

- Databricks Notebooks replicating the PySpark streaming and batch logic with Delta Lake as the sink
- Databricks Workflow JSON definition replacing the Airflow DAG with equivalent task dependencies
- dbt project retargeted to the `dbt-databricks` adapter with Delta table materializations
- `MIGRATION_PLAN.md` documenting every conversion decision, Delta Lake partitioning strategy, and Unity Catalog schema design
- PR with all Databricks-ready artifacts

## What Participants Will Learn

- How Devin reads standard PySpark code and re-expresses it as Databricks Notebooks with Delta Lake APIs
- How Devin converts Airflow DAG definitions into Databricks Workflow task graphs
- How Devin retargets dbt models from one warehouse adapter (BigQuery) to another (Databricks)
- The mapping between open-source data engineering concepts and their Databricks equivalents

## Devin Features Exercised

- Multi-file codebase analysis (PySpark, Airflow, dbt, Terraform)
- Cross-platform code translation
- Infrastructure-as-Code generation (Databricks Workflow JSON, Terraform)
- Documentation generation with architecture rationale
- PR creation with migration artifacts

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

## Notes

- The `streamify-data-engineering` repo uses GCP infrastructure (BigQuery, GCS, Compute Engine). For workshop purposes, participants do not need a GCP or Databricks account — the goal is for Devin to generate the Databricks-ready code, not to deploy it.
- The Airflow DAG in `airflow/dags/streamify_dag.py` uses BigQuery-specific operators; Devin should replace these with equivalent Databricks notebook task references in the Workflow definition.
- The dbt models in `dbt/models/core/` use BigQuery-specific materializations (partition_by with timestamp granularity); the dbt-databricks equivalent uses `liquid_clustering` or `partition_by` with Delta semantics.

---

## <a id="streamify-data-engineering"></a>streamify-data-engineering

**Repository:** [streamify-data-engineering](https://github.com/Cognition-Partner-Workshops/streamify-data-engineering)

Music streaming data pipeline with PySpark Structured Streaming (Kafka → Spark → Parquet/GCS), Airflow hourly batch DAGs, dbt star schema models targeting BigQuery, and Terraform for GCP infrastructure. Three event streams (listen_events, page_view_events, auth_events) with full dimensional modeling.

### Step 1: Paste into Devin

> Analyze the data pipeline in streamify-data-engineering. This project streams music events through Kafka → PySpark Structured Streaming → GCS (Parquet), then uses Airflow to load hourly batches into BigQuery staging tables, and dbt to build a star schema (dim_users, dim_songs, dim_artists, dim_location, dim_datetime, fact_streams).
>
> Convert this entire pipeline to the Databricks Lakehouse platform:
>
> 1. **PySpark → Databricks Notebooks** (`databricks/notebooks/`): Convert `spark_streaming/stream_all_events.py` and `spark_streaming/streaming_functions.py` into Databricks notebooks that write to Delta Lake tables instead of Parquet files on GCS. Use Auto Loader (`cloudFiles`) for incremental ingestion where appropriate. Add markdown cells explaining each transformation step.
>
> 2. **Airflow → Databricks Workflows** (`databricks/workflows/`): Convert the Airflow DAG in `airflow/dags/streamify_dag.py` into a Databricks Workflow JSON definition. Map each Airflow task (create_external_table, insert_job, dbt_run) to equivalent Databricks notebook tasks or SQL tasks with proper dependency chains.
>
> 3. **dbt-bigquery → dbt-databricks** (`databricks/dbt/`): Convert the dbt project in `dbt/` to use the `dbt-databricks` adapter. Update `profiles.yml` for a Databricks SQL Warehouse connection, convert BigQuery-specific materializations (timestamp partitioning) to Delta Lake equivalents, and replace any BigQuery SQL syntax with Databricks SQL (Spark SQL).
>
> 4. **Migration Plan** (`docs/MIGRATION_PLAN.md`): Document every conversion decision — why Auto Loader vs. structured streaming, Delta Lake partitioning/clustering strategy for the event tables, Unity Catalog schema layout, and Workflow scheduling configuration.
>
> Open a PR with all generated artifacts.

### Step 2: Research with Ask Devin

- *"What GCP-specific code patterns in streamify-data-engineering need to change for Databricks? List every GCS, BigQuery, and Airflow reference."*
- *"What's the best Delta Lake partitioning strategy for high-volume event data — partition by date or use liquid clustering?"*
- *"How should the dbt models change when moving from BigQuery to Databricks SQL? What syntax differences exist?"*
- Use the analysis to plan follow-up sessions — try adding Unity Catalog governance, Delta Live Tables, or Databricks Asset Bundles

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the full architecture: event generation (Eventsim), Kafka topics, Spark streaming functions, Airflow DAG structure, and dbt dimensional model. Use this to verify Devin's conversion covers all data flows end-to-end.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the Databricks Notebooks preserve the streaming logic? Is the Delta Lake schema well-designed?
- **Leave a comment** asking Devin to add Delta Live Tables (DLT) pipeline definitions as an alternative to the notebook-based approach
- **Watch Devin respond** and push a follow-up commit with DLT Python pipeline code

---

## <a id="uc-data-source-migration-legacy-to-modern"></a>uc-data-source-migration-legacy-to-modern

**Repository:** [uc-data-source-migration-legacy-to-modern](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-legacy-to-modern)

Spring Boot loan management application with a legacy Corporate Data Warehouse (all-VARCHAR columns, cryptic names like BORR_FST_NM, LN_CURR_BAL). Includes column mappings, seed data, and a modern target schema for migration.

### Step 1: Paste into Devin

> Analyze the legacy CDW schema in uc-data-source-migration-legacy-to-modern. Review the schema in `src/main/resources/schema-legacy.sql`, seed data in `src/main/resources/data-legacy.sql`, and column mappings in `data/mappings/column_mappings.md`.
>
> Generate a complete Databricks migration pipeline:
>
> 1. **Delta Lake DDL** (`databricks/ddl/`): Create `CREATE TABLE` statements for the modern target schema using proper Spark SQL types (DATE, DECIMAL, STRING), meaningful column names, and Delta Lake partitioning.
>
> 2. **PySpark Ingestion** (`databricks/ingestion/`): Write PySpark notebooks that read from legacy tables (CSV/Parquet source), parse dates and amounts, expand status codes (ACT→Active, CLO→Closed), split denormalized fields into dimension tables, and handle nulls/malformed values with logging.
>
> 3. **Data Quality Framework** (`databricks/quality/`): Create validation notebooks checking row counts, null constraints, referential integrity, and business rules (e.g., balance > 0 for active loans). Generate a `DATA_QUALITY_REPORT.md`.
>
> 4. **Migration Runbook** (`docs/DATABRICKS_MIGRATION_RUNBOOK.md`): Document transformation decisions, column mappings, type conversions, and execution order.
>
> Open a PR.

### Step 2: Research with Ask Devin

- *"What are the riskiest type conversions from all-VARCHAR columns to typed Delta Lake columns? What edge cases should the ingestion handle?"*
- *"What's the best approach for incremental loads after the initial migration — Change Data Feed, MERGE, or full refresh?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the domain model, legacy schema quirks, and the column mapping rules. Use this to verify Devin's migration covers all tables and transformations.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the Delta Lake types appropriate? Does the ingestion handle all edge cases in the seed data?
- **Leave a comment** asking Devin to add Databricks notebook versions with markdown cells explaining each transformation

---

## <a id="etl-workflow"></a>etl-workflow

**Repository:** [etl-workflow](https://github.com/Cognition-Partner-Workshops/etl-workflow)

Full-stack ETL workflow application with YAML mapping specs defining five data transformations (employee directory enrichment, department roster, salary analytics, manager hierarchy, title progression) that execute as PostgreSQL INSERT...SELECT statements. FastAPI web UI with optional Informatica IICS integration.

### Step 1: Paste into Devin

> Analyze the ETL mapping specifications in etl-workflow/mapping_specs/. These YAML files define five data transformations that currently execute as PostgreSQL INSERT...SELECT statements.
>
> Convert each mapping to a Databricks notebook:
>
> 1. **Databricks Notebooks** (`databricks/notebooks/`): For each YAML spec, create a PySpark notebook that reads from Delta Lake source tables, applies the same joins/filters/aggregations/window functions, and writes to Delta Lake target tables. Add markdown cells documenting the transformation logic.
>
> 2. **Delta Lake DDL** (`databricks/ddl/`): Create source and target table definitions in Spark SQL with appropriate types and partitioning.
>
> 3. **Databricks Workflow** (`databricks/workflows/workflow.json`): Define a Workflow that runs the notebooks in dependency order.
>
> 4. **Migration Notes** (`docs/ETL_TO_DATABRICKS.md`): Document the PostgreSQL → Spark SQL translation decisions.
>
> Open a PR.

### Step 2: Research with Ask Devin

- *"What SQL patterns in the etl-workflow mapping specs are PostgreSQL-specific and need Spark SQL equivalents?"*
- *"How do the YAML mapping specs define joins, filters, and expressions? What's the most complex transformation?"*

### Step 3 (Optional): Read the DeepWiki

Review the mapping spec format, the PostgreSQL source schema (employees database), and the transformation patterns (window functions, self-joins, aggregations).

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the PySpark notebooks produce equivalent results to the SQL versions? Are window functions correctly translated?
- **Leave a comment** asking Devin to add data profiling statistics comparing source and target row counts
