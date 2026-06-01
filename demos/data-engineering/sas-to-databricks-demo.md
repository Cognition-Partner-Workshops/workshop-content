# SAS → dbt/Databricks — End-to-End Migration Demo (Facilitator-Led)

A **facilitator-led demo module**. Unlike the hands-on modules in `modules/`,
this is a scripted showcase a presenter runs live against a real Databricks
workspace to tell the SAS → dbt/Databricks migration story end to end. Attendees
watch and discuss; they do not drive it themselves.

The commands and prompts here are kept **identical** to the runbook in the code
repo: [`uc-data-migration-sas-to-databricks/docs/DEMO_RUNBOOK.md`](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks/blob/main/docs/DEMO_RUNBOOK.md).
If you change one, change the other.

## Table of Contents

- [Quick Start](#quick-start)
- [What This Demonstrates](#what-this-demonstrates)
- [Repositories](#repositories)
- [The Before / After Model](#before-after)
- [Difficulty & Estimated Time](#difficulty--estimated-time)
- [Demo Script](#demo-script)
  - [Setup (once)](#setup)
  - [Step 1 — Show the Before](#step-1)
  - [Step 2 — Build the After (dbt)](#step-2)
  - [Step 3 — Query Before vs After](#step-3)
  - [Step 4 — PySpark Alternative](#step-4)
  - [Step 5 — IaC + CD Deploy](#step-5)
  - [Step 6 — Run the Workflow](#step-6)
  - [Step 7 — Revert / Reset](#step-7)
- [Concurrent Runs](#concurrent)
- [Warehouse Sizing Note](#warehouse)
- [Key Takeaways](#key-takeaways)
- [How Devin Produced This](#how-devin)

---

<a id="quick-start"></a>
## Quick Start

Set workspace credentials, then run the lifecycle from the repo root on the demo
branch:

```bash
export DATABRICKS_HOST="https://<workspace>.cloud.databricks.com"
export DATABRICKS_HTTP_PATH="/sql/1.0/warehouses/<warehouse_id>"
export DATABRICKS_TOKEN="dapi..."

pip install -r requirements.txt -r seed/requirements.txt
cd dbt_project && dbt deps && cd ..

make seed                 # before: raw source data
make demo-up NS=dev       # after: dbt build (13 models + 43 tests)
make deploy               # IaC: bundle deploy
make run-job TARGET=dev   # run deployed Workflow (dbt + PySpark)
make destroy TARGET=dev   # revert deploy
make demo-down NS=dev     # drop after-state schemas (raw untouched)
```

---

<a id="what-this-demonstrates"></a>
## What This Demonstrates

- A legacy SAS estate (DATA steps, PROC SQL, PROC FORMAT, hash objects, Control-M
  batch) migrated to a working dbt project on Databricks with Unity Catalog.
- Two migration paths side by side: **dbt** (set-based, primary) and a **custom
  PySpark job** (imperative, for procedural multi-output programs).
- The operational spine the narrative needs but legacy estates lack: synthetic
  data seeding, **IaC** via Databricks Asset Bundles, and **CI/CD** that deploys
  the pipeline.
- A repeatable before→after story: durable raw "before" data, disposable
  per-namespace "after" outputs, and a one-command revert — safe to run many times.

---

<a id="repositories"></a>
## Repositories

- [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics) — the legacy SAS estate (banking/insurance programs, macros, formats, batch orchestration). Read-only reference for the "before".
- [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks) — the dbt + Databricks target: models, seeder, PySpark job, Asset Bundle (IaC), CI/CD, and the demo runbook.

---

<a id="before-after"></a>
## The Before / After Model

| | Code | Data |
|---|---|---|
| **Before** | `main` branch + the SAS estate | `banking_analytics.raw.*` (durable; never overwritten) |
| **After** | the demo branch (full models + PySpark + IaC + CD) | `banking_analytics.<NS>_staging / _intermediate / _marts / _curated` (per-run, disposable) |

The before state is durable; the after state is namespaced and disposable. This
is what makes the demo safe to repeat and safe to run concurrently.

---

<a id="difficulty--estimated-time"></a>
## Difficulty & Estimated Time

- **Difficulty:** Intermediate (facilitator should be comfortable with dbt and the Databricks CLI)
- **Estimated Time:** 20–30 minutes live
- **Prerequisites:** Databricks workspace with Unity Catalog + a serverless SQL warehouse; a PAT that can use the warehouse and create catalogs/schemas/jobs

---

<a id="demo-script"></a>
## Demo Script

<a id="setup"></a>
### Setup (once)

```bash
export DATABRICKS_HOST="https://<workspace>.cloud.databricks.com"
export DATABRICKS_HTTP_PATH="/sql/1.0/warehouses/<warehouse_id>"
export DATABRICKS_TOKEN="dapi..."

git checkout <demo-branch>
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt -r seed/requirements.txt
cd dbt_project && dbt deps && cd ..
databricks current-user me        # confirm connectivity
make seed                         # seed before-state raw data (idempotent)
```

<a id="step-1"></a>
### Step 1 — Show the Before

Narrate the legacy estate, then show the raw inputs exist in Unity Catalog:

```sql
SHOW TABLES IN banking_analytics.raw;
SELECT * FROM banking_analytics.raw.cust_accounts LIMIT 10;
SELECT * FROM banking_analytics.raw.claims        LIMIT 10;
```

Open one SAS source for contrast, e.g.
`ts-sas-legacy-analytics/Programs/Insurance/claims_processing.sas`.

<a id="step-2"></a>
### Step 2 — Build the After (dbt)

```bash
make demo-up NS=dev
```

Expected: `PASS=56 WARN=0 ERROR=0` — 13 models (3 staging views, 3 intermediate
tables, 7 marts) + 43 data tests. Call out a few translations: SAS IF/THEN →
`CASE`, PROC FORMAT → Jinja macros, WOE scorecard → nested `CASE` + `exp()`,
multi-source `MERGE BY` → multi-`ref()` `JOIN`.

<a id="step-3"></a>
### Step 3 — Query Before vs After

```sql
SELECT count(*) FROM banking_analytics.raw.cust_accounts;            -- before
SELECT count(*) FROM banking_analytics.dev_marts.mart_risk_scores;  -- after
SELECT * FROM banking_analytics.dev_marts.mart_customer_pnl ORDER BY net_profit DESC LIMIT 20;
SELECT policy_type, agg_loss_ratio, agg_combined_ratio FROM banking_analytics.dev_marts.mart_loss_ratios;
```

<a id="step-4"></a>
### Step 4 — PySpark Alternative

The procedural, multi-output `claims_processing.sas` is shown as a custom PySpark
job (`src/pyspark/claims_processing.py`) — the alternative to dbt:

```sql
SELECT * FROM banking_analytics.dev_curated.claims_register     LIMIT 20;
SELECT * FROM banking_analytics.dev_curated.fraud_alerts;
SELECT * FROM banking_analytics.dev_curated.claims_review_queue;
```

<a id="step-5"></a>
### Step 5 — IaC + CD Deploy

```bash
make deploy               # databricks bundle deploy -t dev (schedule paused)
```

The pipeline is a Databricks Asset Bundle (`databricks.yml` +
`resources/daily_banking_pipeline.job.yml`). To show CD without merging to
`main`, trigger the GitHub Actions **deploy bundle** workflow manually
(Actions → Run workflow → pick target + namespace). On a real merge to `main`
the same workflow deploys automatically.

<a id="step-6"></a>
### Step 6 — Run the Workflow

```bash
make run-job TARGET=dev
```

Watch the DAG `dbt_staging → dbt_intermediate → dbt_marts → dbt_test` with
`pyspark_claims_processing` in parallel. Expect `TERMINATED SUCCESS`.

<a id="step-7"></a>
### Step 7 — Revert / Reset

```bash
make destroy TARGET=dev   # remove the deployed job (revert CD)
make demo-down NS=dev     # drop dev_* output schemas (raw data untouched)
```

`main` is never modified and the raw "before" data is intact — ready to run again.

---

<a id="concurrent"></a>
## Concurrent Runs

Each output schema is namespaced, so multiple runs coexist with no collisions:

```bash
make demo-up   NS=alice
make demo-up   NS=run2
make demo-down NS=alice
```

For the deployed job, pass the namespace through the bundle variable:

```bash
databricks bundle deploy -t dev --var="dbt_schema=alice"
```

---

<a id="warehouse"></a>
## Warehouse Sizing Note

The data is tiny; on a 2X-Small serverless warehouse, queries run ~0.5–3.3s each
(median ~1.9s), dominated by planning/queueing, not compute. A larger warehouse
does not speed this up and only adds cost — leave it at 2X-Small. Full-job
wall-clock (~3–4 min) is mostly serverless cold-start.

---

<a id="key-takeaways"></a>
## Key Takeaways

- A SAS estate can be migrated to a **working, tested** dbt project on Databricks — not just a paper mapping. The before→after is queryable side by side in Unity Catalog.
- dbt covers the set-based transforms; a custom **PySpark** job is the right tool for procedural, multi-output SAS programs. Showing both frames the migration choice honestly.
- **IaC + CI/CD** (Databricks Asset Bundles + GitHub Actions) replace hand-promoted SAS packages with version-controlled, reviewable, automated deployment.
- Namespaced outputs + a one-command revert make the whole story **safe to repeat** and **safe to run concurrently** without touching the durable source data.

---

<a id="how-devin"></a>
## How Devin Produced This

This reference solution was built by Devin working from the SAS source estate and
the partially-built dbt target: it analyzed the SAS programs, built the missing
insurance/regulatory models, generated synthetic data, authored the Asset Bundle
and CD workflow, and validated the whole pipeline by deploying and running it live
in a Databricks workspace. The same Context Loop (source analysis → target
mapping → produce PR → human review → refine) described in
[`modules/data-engineering/sas-migration-analysis.md`](../../modules/data-engineering/sas-migration-analysis.md)
applies here.
