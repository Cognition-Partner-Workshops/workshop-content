# ETL Pipeline Modernization

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [uc-data-migration-airflow](#uc-data-migration-airflow)
- [ts-sas-legacy-analytics](#ts-sas-legacy-analytics)
- [Going Further](#going-further)

---

## Challenge

Migrate legacy ETL processes built with SAS batch jobs and macro-driven workflows to a modern Apache Airflow orchestration pipeline. Participants will analyze existing data transformation logic and create equivalent Airflow DAGs with proper scheduling, error handling, and monitoring.

This exercise uses **non-invasive static analysis**: Devin reads the SAS macro source files and batch orchestration scripts in the repository to understand the transformation logic — no running SAS environment, no SAS license, and no modifications to the legacy system are required.

## Quick Start

1. Open Devin and connect to both **uc-data-migration-airflow** and **ts-sas-legacy-analytics** repositories
2. Paste the [Step 1 prompt](#step-1-paste-into-devin) into Devin
3. Watch Devin analyze the SAS macros, generate equivalent Airflow DAGs, and open a PR

## Repositories

- [uc-data-migration-airflow](#uc-data-migration-airflow)
- [ts-sas-legacy-analytics](#ts-sas-legacy-analytics)

---

## Target Outcomes

- Modern Airflow DAG replacing legacy SAS-based ETL logic with equivalent data transformations
- Data validation checks comparing old vs new pipeline outputs for correctness
- Documentation of migration decisions including SAS-to-Python transformation mappings
- PR with new pipeline code, DAG definitions, and migration notes
- `ETL_MIGRATION_NOTES.md` documenting orchestration decisions and SAS construct mappings

## What Participants Will Learn

- How Devin analyzes legacy SAS programs and understands data transformation logic
- How Devin generates modern Airflow DAGs from legacy batch workflows
- How Devin handles cross-language translation (SAS macros to Python/Airflow operators)
- The importance of validation testing when migrating ETL pipelines

## Devin Features Exercised

- Codebase analysis across multiple repositories
- Cross-language translation (SAS to Python)
- Pipeline architecture understanding and DAG generation
- PR creation with migration documentation

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="uc-data-migration-airflow"></a>uc-data-migration-airflow

**Repository:** [uc-data-migration-airflow](https://github.com/Cognition-Partner-Workshops/uc-data-migration-airflow)

Docker Compose-based Apache Airflow 2.0 environment with PostgreSQL backend, configured for local DAG development with LocalExecutor. The `dags/` directory is volume-mounted for live DAG authoring.

### Step 1: Paste into Devin

```
Analyze the SAS macro library in ts-sas-legacy-analytics/Macro/ — focus on the data transformation macros (export_csv.sas, transpose.sas, subset_data.sas, compare.sas, dedup_string.sas). For each macro, understand the input/output data flow and transformation logic. Then create equivalent Airflow DAGs in uc-data-migration-airflow/dags/ that replicate the same data transformations using Python operators. Include a validation task in each DAG that compares input/output row counts and checksums. Create an ETL_MIGRATION_NOTES.md documenting each SAS-to-Airflow translation decision.
```

### Step 2: Research with Ask Devin

- *"What are the most complex SAS macros in ts-sas-legacy-analytics/Macro/ and what data transformations do they perform? Which ones have dependencies on other macros?"*
- *"What's the best Airflow DAG structure for orchestrating the SAS macro equivalents — one monolithic DAG or separate DAGs per transformation with cross-DAG dependencies?"*
- Use the analysis to plan a DAG dependency graph before starting the migration session

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page for both uc-data-migration-airflow and ts-sas-legacy-analytics. Understand the Airflow infrastructure setup (executor type, connections, volume mounts) and map out which SAS macros perform ETL-style operations vs utility functions.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the Airflow DAGs correctly replicate the SAS transformation logic? Are task dependencies properly defined?
- **Leave a comment** asking Devin to add error handling and retry logic to the DAG tasks, plus Slack/email alerting on failure
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Non-invasive analysis**: Devin reads SAS macro source code to understand transformation logic — no SAS license, no running SAS environment needed
- **Cross-language translation**: SAS macros map to Python operators within Airflow DAGs — Devin handles the paradigm shift from macro-driven to DAG-driven orchestration
- **Validation-first approach**: Each migrated DAG includes validation tasks that compare outputs against the original SAS pipeline

---

## <a id="ts-sas-legacy-analytics"></a>ts-sas-legacy-analytics

**Repository:** [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics)

Legacy SAS codebase containing 90+ macros in the `Macro/` directory, Enterprise Guide projects, and batch processing programs. Represents a typical legacy analytics environment with macro-driven ETL workflows.

### Step 1: Paste into Devin

```
Analyze the batch orchestration pattern in ts-sas-legacy-analytics — examine RunAll.sas and RunAll_ControlTable.sas in the Macro/ directory to understand how SAS orchestrates multi-step ETL workflows using control tables. Then create a modern Airflow DAG in uc-data-migration-airflow/dags/ that replicates this control-table-driven execution pattern using Airflow's task dependencies and XCom for inter-task data passing. Include Python equivalents of the key utility macros (loop.sas, loop_control.sas, execute_macro.sas). Include a CONTROL_TABLE_MIGRATION.md explaining the translation.
```

### Step 2: Research with Ask Devin

- *"How does RunAll_ControlTable.sas in ts-sas-legacy-analytics/Macro/ manage execution ordering and dependencies between SAS macros? What's the control table schema?"*
- *"What SAS-specific features in the batch_submit.sas and stp_batch_submit.sas macros would need special handling when converting to Airflow task execution?"*
- Use these insights to design a more robust Airflow orchestration pattern

### Step 3 (Optional): Read the DeepWiki

Open the DeepWiki page for ts-sas-legacy-analytics to understand the full macro dependency graph. Identify which macros are orchestration-level (RunAll, loop, batch_submit) vs data transformation (transpose, subset_data, export) to prioritize the migration scope.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Airflow DAG handle the same execution ordering as the SAS control table pattern? Are edge cases (failed steps, retries) covered?
- **Leave a comment** asking Devin to add a dynamic DAG generator that reads a YAML control table instead of hardcoded task definitions
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Control-table pattern migration**: SAS's macro-driven orchestration translates to Airflow's DAG model — control tables become task dependencies and XCom
- **Parallel migration potential**: With 90+ macros, a [child-session pattern](../../shared/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents) can convert groups of related macros in parallel — one Devin session per macro category

---

## Going Further

### Automation and Webhooks

Set up a CI trigger so that when new SAS macros are added or modified, a Devin session automatically generates or updates the corresponding Airflow DAG. See [Design Patterns → Event-Driven Triggers](../../shared/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers).

### Child Sessions for Scale

For estates with dozens of SAS macros, use the [divide-and-conquer pattern](../../shared/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents): a parent session categorizes macros (orchestration, transformation, I/O utility), then spawns a child session per category. Each child converts its macros to Airflow operators and opens its own PR.

### Scheduled Sessions

Schedule a weekly Devin session to validate that the Airflow DAGs remain functionally equivalent to the SAS source macros — catching drift if either side changes. See [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md#scheduled-sessions).

### Shared Context Layer

Create organization-level [knowledge notes](../../shared/general-themes/architecture-strengths.md#shared-context-layer) documenting your SAS-to-Airflow translation conventions (e.g., "%MACRO → PythonOperator", "control table → DAG task dependency graph", "SYSPARM → Airflow Variables"). Every Devin session inherits these conventions automatically.

### Team-Based Operation

Data engineers can review the Airflow DAG logic while SAS SMEs validate the transformation accuracy — both reviewing the same PR simultaneously. See [Collaboration Model → Multi-User Communication](../../shared/general-themes/collaboration-model.md#multi-user-communication).
