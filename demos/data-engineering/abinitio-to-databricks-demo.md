# Ab Initio → dbt/Databricks — End-to-End Migration Demo

A single linear demo that shows Devin migrating an Ab Initio ETL estate to
runnable dbt/Databricks code with **verifiable confidence**: orient over the
legacy graphs, convert one graph live, prove parity with the source through
programmatic reconciliation, catch a real divergence and fix it, then fan the
work out across many graphs in parallel. The second half runs the produced
artifact end to end (before/after, IaC, scheduled Workflow) and reverts — safe to
repeat.

The prompts below invoke the `!convert-abinitio-to-databricks` Devin Playbook —
the reusable conversion procedure — whose source lives in the code repo at
[`uc-data-migration-abinitio-to-databricks/.workshop/playbooks/abinitio-to-databricks-conversion.devin.md`](https://github.com/Cognition-Partner-Workshops/uc-data-migration-abinitio-to-databricks/blob/main/.workshop/playbooks/abinitio-to-databricks-conversion.devin.md).
The repo-specific `make demo-up` / `make reconcile` mechanics come from that
repo's Skill (`.agents/skills/abinitio-to-databricks-conversion/SKILL.md`).

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Does the Migration](#part-1)
  - [Act 1 — Orient over the Ab Initio estate](#act-1)
  - [Act 2 — Convert one graph live, with verification](#act-2)
  - [Act 3 — Fan out in parallel](#act-3)
  - [Act 4 — Confidence = programmatic verification](#act-4)
- [Part 2 — Run the Produced Artifact](#part-2)
- [Confirming Completion in Databricks](#confirm-databricks)
- [Concurrent Runs](#concurrent)
- [Warehouse Sizing Note](#warehouse)
- [Key Takeaways](#key-takeaways)
- [How Devin Produced This](#how-devin)

---

<a id="quick-start"></a>
## Quick Start

Set workspace credentials, then run the full lifecycle from the repo root on the
demo branch:

```bash
export DATABRICKS_HOST="https://<workspace>.cloud.databricks.com"
export DATABRICKS_HTTP_PATH="/sql/1.0/warehouses/<warehouse_id>"
export DATABRICKS_TOKEN="dapi..."

pip install -r requirements.txt -r verify/requirements.txt

make demo-up   NS=dev     # before -> after: seed raw + dbt build (models + schema + reconcile tests)
make reconcile NS=dev     # source -> target reconciliation report
make deploy               # IaC: bundle deploy (schedule paused, per-user)
make run-job   TARGET=dev # run the deployed Workflow
make destroy   TARGET=dev # revert deploy
make demo-down NS=dev     # drop after-state schemas (raw untouched)
```

Prerequisites: a Databricks workspace with Unity Catalog + a serverless SQL
warehouse, and a PAT that can use the warehouse and create catalogs/schemas/jobs.

---

<a id="repositories"></a>
## Repositories

- [ts-python-abinitio-etl](https://github.com/Cognition-Partner-Workshops/ts-python-abinitio-etl) — the legacy Ab Initio estate (graphs, DML record formats, PSET parameter sets, CDC, KornShell/AutoSys orchestration). Read-only reference for the "before".
- [uc-data-migration-abinitio-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-abinitio-to-databricks) — the dbt + Databricks target: models, reconciliation harness (dbt tests + report), seeder, Asset Bundle (IaC), CI, the conversion playbook source (`.workshop/playbooks/`), and the repo Skill (`.agents/skills/`).

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Data |
|---|---|---|
| **Before** | `main`: the **customer + orders** graphs already migrated (staging → intermediate → marts), plus the tooling, reconciliation harness, seeder, the conversion playbook source (`.workshop/playbooks/`), and the repo Skill. The Ab Initio estate lives in `ts-python-abinitio-etl`. | `retail_analytics.raw.*` (durable; never overwritten) |
| **After** | a PR branch with the **transactions + customer-CDC** graphs converted live (dbt models / a notebook job + their reconciliation controls) | `retail_analytics.<NS>_staging / _intermediate / _marts / _curated` (per-run, disposable) |

The **before** state is deliberately a *partial* migration: the customer and
orders graphs (`stg_customers`, `stg_orders`, `int_customer_orders`,
`mart_daily_orders`) are already on `main` with working reconciliation controls,
so the harness and playbook are in place. What Devin converts **live** is the next
wave — the transactions and customer-CDC graphs that are *not* yet on `main`.

The verification loop sits between them: every converted model is built into a
namespace and checked by reconciliation controls before it is trusted. The before
state is durable; the after state is namespaced and disposable — which is what
makes this safe to repeat and safe to run concurrently.

> **On "parity":** there is no live Co>Operating System runtime in this
> environment, so parity means source → target reconciliation against the Ab
> Initio code as the source of truth (control totals, row counts, per-class
> parity) plus dbt tests — a deterministic contract, not a byte-for-byte Ab
> Initio-vs-Databricks output diff.

---

<a id="part-1"></a>
## Part 1 — Devin Does the Migration

<a id="act-1"></a>
### Act 1 — Orient over the Ab Initio estate

Open the Ab Initio estate and ask Devin to explain it. With DeepWiki over the
repo, Devin typically maps an unfamiliar estate in minutes (coverage depends on
repo structure).

```
Using the ts-python-abinitio-etl repo, give me a map of the Ab Initio estate:
the graphs in graphs/, the DML record formats in dml/, the PSET parameter sets
in psets/, and the KornShell wrappers in scripts/. For each pipeline, tell me
what it reads and writes, which DML it binds, and whether it is set-based (good
for dbt) or procedural/multi-output (better as a PySpark/notebook job).
```

Expected: a tour of `graphs/cdc_processor.py` and `graphs/parallel_loader.py`,
the eight `dml/*.dml` record formats (including the nested `transaction_detail.dml`),
the three `psets/pset_templates/*.pset`, and the `scripts/run_daily_orders.ksh` /
`run_customer_cdc.ksh` wrappers — with a dbt-vs-PySpark recommendation per pipeline.

<a id="act-2"></a>
### Act 2 — Convert one graph live, with verification

The core beat. Paste the playbook prompt for one graph. Devin reads the DML and
graph, writes the dbt model plus reconciliation controls, builds against the live
workspace, runs the controls, catches a divergence, fixes it, and produces a PR
with the reconciliation report.

```
!convert-abinitio-to-databricks

Convert the Ab Initio transactions detail pipeline from the ts-python-abinitio-etl
estate into dbt models on Databricks, writing to
uc-data-migration-abinitio-to-databricks.

- DML: dml/transaction_detail.dml (nested merchant_info + line_items[] array)
- Source layout/orchestration: scripts/run_daily_orders.ksh
- Target model(s): stg_transactions + curated_transactions
- Namespace: dev
```

**The verification beat (the real bug).** `dml/transaction_detail.dml` declares
`string("\n", null("UNKNOWN")) channel` — a **blank** channel in the extract is
read by Ab Initio as the literal `UNKNOWN`, never NULL. A plausible-looking
conversion loads transactions without reproducing that default, so blank channels
land as `NULL`. Row counts and the amount control total still tie out, so "looks
reasonable" review passes it. The parity control catches it:

```bash
make reconcile NS=dev
#   transactions_channel_parity | FAIL | 6 transaction(s) with NULL/blank channel (expected the DML default 'UNKNOWN')
```

Reproduce the DML default — `coalesce(nullif(trim(channel), ''), 'UNKNOWN')` —
re-run, and the report goes green:

```bash
make reconcile NS=dev
#   transactions_channel_parity | PASS | all transactions carry a channel (DML null("UNKNOWN") default applied)
```

The point: "looks reasonable" review would have shipped `NULL`; the parity check
against the source did not. The full write-up is in the playbook at
`.workshop/playbooks/abinitio-to-databricks-conversion.devin.md` → *Worked
example: the transaction channel default divergence*.

<a id="act-3"></a>
### Act 3 — Fan out in parallel

Conversions are independent, so launch a Devin session per graph. Each follows
the same playbook and produces its own verified PR — the same review bar applied
many times in parallel instead of once in series.

| Session | Ab Initio pipeline | Target |
|---|---|---|
| 1 | `dml/transaction_detail.dml` + `scripts/run_daily_orders.ksh` | `stg_transactions` + `curated_transactions` (the Act 2 worked example) |
| 2 | `graphs/cdc_processor.py` + `psets/pset_templates/customer_cdc.pset` | customer-CDC: dbt snapshot + a Delta `MERGE` (compare-by-key + row hash) |
| 3 | `dml/order_items.dml` (nested arrays) | `int_order_items` (explode `item_names[]`/`item_quantities[]`) |

Each session uses its own namespace (`NS=session1`, …) so the live builds never
collide.

#### Parallelize from a single session (parent → child)

Instead of launching each session by hand, run one **orchestrator** session that
spawns a child Devin session per graph and monitors them — one agent fanning
itself out across the wave. Paste:

```
Act as the orchestrator for an Ab Initio->Databricks migration across multiple
graphs, using child Devin sessions to parallelize the work.

Repos: read Cognition-Partner-Workshops/ts-python-abinitio-etl (the Ab Initio
source), write Cognition-Partner-Workshops/uc-data-migration-abinitio-to-databricks.

Spawn one child Devin session per pipeline below. Give each child both repos, its
own namespace (NS=child1, child2, ...), and tell it to follow the
!convert-abinitio-to-databricks playbook (the repo's Skill supplies the
`make demo-up NS=...` / `make reconcile NS=...` mechanics): treat the Ab Initio
source as the source of truth and reproduce its logic exactly, including DML
null(...) defaults and the CDC hash column list; flag (do not silently fix)
anything that looks wrong; add reconciliation controls (completeness, a control
total, and a parity check for every default/mapping/CDC class); and build until
everything is green, with the reconciliation report included.

Pipelines:
1. dml/transaction_detail.dml (+ scripts/run_daily_orders.ksh)
   -> stg_transactions + curated_transactions
2. graphs/cdc_processor.py (+ psets/pset_templates/customer_cdc.pset)
   -> customer-CDC dbt snapshot + Delta MERGE
3. dml/order_items.dml -> int_order_items (explode arrays)

After launching, monitor the child sessions until each pipeline is converted with
a green reconciliation report. Then summarize the results and call out any
source-parity divergences the children caught (e.g. the channel default).
```

The children inherit the organization's Databricks secrets, and each writes to
its own namespace (`child1`, `child2`, …) so the parallel builds never collide.
This is the same verified conversion loop as a single session — run many times at
once, from one parent.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

The gates that make every PR trustworthy:

- **CI** (`.github/workflows/dbt_ci.yml`): sqlfluff lint → `dbt parse`. With
  workspace credentials, `make test` adds `dbt test` (schema **and** the
  `reconcile_*.sql` reconciliation tests) and `make reconcile` publishes the
  report.
- **Reconciliation controls** (`dbt_project/tests/reconcile_*.sql` +
  `verify/reconcile.py`): completeness, control totals, and source-parity classes
  (e.g. the channel default) — documented as a contract in the
  `!convert-abinitio-to-databricks` playbook and the repo's Skill.
- **Devin Review**: an automated reviewer on every PR.

A conversion is "done" when the source-parity controls are green, in CI, on the
PR — not when the code merely runs.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifact

Show the converted estate running end to end, with a repeatable before/after.

```bash
make demo-up   NS=dev     # seed (idempotent) + dbt build into dev_* schemas
make reconcile NS=dev     # all controls PASS
```

`dbt build` runs the models plus the schema and reconciliation tests. Query the
before and after side by side:

```sql
SELECT count(*) FROM retail_analytics.raw.orders;                 -- before
SELECT count(*) FROM retail_analytics.dev_marts.mart_daily_orders; -- after
SELECT * FROM retail_analytics.dev_marts.mart_daily_orders ORDER BY order_date;

-- once the transactions graph is converted: blank channels carry the DML default
SELECT channel, count(*) FROM retail_analytics.dev_curated.curated_transactions
GROUP BY channel ORDER BY channel;
```

Deploy as IaC, run the Workflow, then revert — `main` and the raw data are never
touched:

```bash
make deploy               # databricks bundle deploy -t dev (schedule paused, per-user)
make run-job   TARGET=dev # DAG: dbt_staging -> intermediate -> marts -> test
make destroy   TARGET=dev # remove the deployed job (revert CD)
make demo-down NS=dev     # drop dev_* output schemas (raw data untouched)
```

The pipeline is a Databricks Asset Bundle (`databricks.yml` +
`workflows/daily_orders_pipeline.yml`) that replaces the Ab Initio
AutoSys/Control-M + KornShell orchestration. See
[`workflows/README.md`](https://github.com/Cognition-Partner-Workshops/uc-data-migration-abinitio-to-databricks/blob/main/workflows/README.md)
for the construct-by-construct mapping.

---

<a id="confirm-databricks"></a>
## Confirming Completion in Databricks

The migration milestone is complete when five things are true and visible in the
workspace: the converted models are **materialized** in Unity Catalog, the
**reconciliation controls are green** (parity proven against the Ab Initio
source), the orchestrated **Workflow runs end to end** as IaC, the **before is
untouched** next to the after, and you can **prove it ran live**. Walk the
workspace UI in that order to show the work is done.

**1. Catalog Explorer — the models exist (after), the source is intact (before).**
Open **Catalog** → `retail_analytics`. Show the durable `raw` schema (the
"before" — `customers`, `orders`, `transactions`) sitting unchanged next to the
freshly built `<NS>_marts` / `<NS>_curated` schemas (the "after").

```sql
SELECT count(*) FROM retail_analytics.raw.orders;                  -- before (durable)
SELECT count(*) FROM retail_analytics.dev_marts.mart_daily_orders; -- after (built live)
```

**2. The source-parity beat — the value ties out to Ab Initio.** Run the curated
transactions grouped by channel and show every blank source channel sitting at
the literal `UNKNOWN` (the DML `null("UNKNOWN")` default), not `NULL`. This is the
"verification caught a wrong conversion" proof, visible as data:

```sql
SELECT channel, count(*) FROM retail_analytics.dev_curated.curated_transactions
GROUP BY channel ORDER BY channel;
```

**3. The reconciliation report — every control PASS.** Show the report produced by
`make reconcile NS=dev`: each control — completeness, control totals, and
per-class parity — reports `PASS`. A green report is the definition of "done"; the
code merely running is not.

**4. Workflows — the pipeline runs end to end as IaC.** Open **Workflows** →
`daily_orders_pipeline` (the deployed Asset Bundle job) and show the most recent
run's task graph all green: `dbt_staging → dbt_intermediate → dbt_marts →
dbt_test`. This proves the converted estate runs as an orchestrated, scheduled
pipeline — not just ad-hoc queries — replacing the Ab Initio AutoSys schedule.

**5. Query History — proof it executed live.** Open the SQL warehouse's **Query
History** to show the `dbt build` statements that just ran against the workspace,
with durations. This confirms the conversion was built and verified live, here,
during the session.

For a multi-graph or fan-out run, repeat step 1 across the per-session namespaces
(`session1_marts`, `child1_marts`, …) to show several converted graphs landing
independently and all reconciling green — the milestone achieved in parallel.

---

<a id="concurrent"></a>
## Concurrent Runs

Each output schema is namespaced, so multiple runs — and the parallel fan-out in
Act 3 — coexist with no collisions:

```bash
make demo-up   NS=alice
make demo-up   NS=run2
make reconcile NS=alice
make demo-down NS=alice
```

---

<a id="warehouse"></a>
## Warehouse Sizing Note

The data is tiny; on a 2X-Small serverless warehouse, queries run in a few seconds
each, dominated by planning/queueing, not compute. A larger warehouse does not
speed this up and only adds cost — leave it at 2X-Small.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The value on display is **Devin doing the migration**: reading an unfamiliar Ab Initio estate, converting graphs off a reusable playbook, and proving each conversion against the source — not just a finished artifact to run.
- **Confidence comes from programmatic verification.** Reconciliation controls (completeness, control totals, source-parity classes) gate every build and CI run, and the demo shows a real divergence (blank channel → `NULL` vs the source's `UNKNOWN`) being caught and fixed. "Looks reasonable" review would have missed it.
- The **Ab Initio source is the source of truth**: conversions reproduce legacy logic faithfully, including DML `null(...)` defaults and CDC hash rules (quirks flagged, not silently "fixed"); remediation is a separate, deliberate decision.
- Conversions are **independent and parallelizable** — multiple Devin sessions convert multiple graphs at once, each producing its own verified PR. The playbook keeps every run consistent.
- dbt covers set-based transforms; a custom **PySpark/notebook** job fits procedural, multi-output graphs (e.g. CDC). **IaC + CI/CD** (Asset Bundles + GitHub Actions) replace AutoSys/Control-M + KornShell orchestration, and namespaced outputs + one-command revert make the whole story safe to repeat and run concurrently.

---

<a id="how-devin"></a>
## How Devin Produced This

This reference solution was built by Devin working from the Ab Initio source
estate and the partially-built dbt target: it analyzed the graphs and DML, built
the customer/orders models, generated synthetic data, authored the reconciliation
harness, the Asset Bundle, and the Workflow, and validated the pipeline. The same
Context Loop (source analysis → target mapping → produce PR → programmatic
verification → human review → refine) described in
[`labs/data-engineering/abinitio-migration-analysis.md`](../../labs/data-engineering/abinitio-migration-analysis.md)
applies here, with the conversion procedure codified as the
`!convert-abinitio-to-databricks` Devin Playbook (source in the code repo at
`.workshop/playbooks/abinitio-to-databricks-conversion.devin.md`).
