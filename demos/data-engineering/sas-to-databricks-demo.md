# SAS → dbt/Databricks — End-to-End Migration Demo

A single linear demo that shows Devin migrating a SAS estate to runnable
dbt/Databricks code with **verifiable confidence**: orient over the legacy
estate, convert one program live, prove parity with the source through
programmatic reconciliation, catch a real divergence and fix it, then fan the
work out across many programs in parallel. The second half runs the produced
artifact end to end (before/after, IaC, CD) and reverts — safe to repeat.

The prompts below invoke the `!convert-sas-to-databricks` Devin Playbook — the
reusable conversion procedure — whose source lives in the code repo at
[`uc-data-migration-sas-to-databricks/.workshop/playbooks/sas-to-databricks-conversion.devin.md`](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks/blob/main/.workshop/playbooks/sas-to-databricks-conversion.devin.md).
The repo-specific `make demo-up` / `make reconcile` mechanics come from that
repo's Skill (`.agents/skills/sas-to-databricks-conversion/SKILL.md`).

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Does the Migration](#part-1)
  - [Act 1 — Orient over the SAS estate](#act-1)
  - [Act 2 — Convert one program live, with verification](#act-2)
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

pip install -r requirements.txt -r seed/requirements.txt -r verify/requirements.txt
cd dbt_project && dbt deps && cd ..

make seed                 # before: raw source data
make demo-up   NS=dev     # after: dbt build (13 models + schema + reconciliation tests)
make reconcile NS=dev     # source -> target reconciliation report
make deploy               # IaC: bundle deploy
make run-job   TARGET=dev # run deployed Workflow (dbt + PySpark)
make destroy   TARGET=dev # revert deploy
make demo-down NS=dev     # drop after-state schemas (raw untouched)
```

Prerequisites: a Databricks workspace with Unity Catalog + a serverless SQL
warehouse, and a PAT that can use the warehouse and create catalogs/schemas/jobs.

---

<a id="repositories"></a>
## Repositories

- [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics) — the legacy SAS estate (banking/insurance programs, macros, formats, batch orchestration). Read-only reference for the "before".
- [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks) — the dbt + Databricks target: models, reconciliation harness, seeder, PySpark job, Asset Bundle (IaC), CI/CD, the conversion playbook source (`.workshop/playbooks/`), and the repo Skill (`.agents/skills/`).

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Data |
|---|---|---|
| **Before** | `main`: the **banking** domain already migrated (Phase 1), plus the tooling, reconciliation harness, seeder, the conversion playbook source (`.workshop/playbooks/`), and the repo Skill. The SAS estate lives in `ts-sas-legacy-analytics`. | `banking_analytics.raw.*` (durable; never overwritten) |
| **After** | a PR branch with the **regulatory + insurance** programs converted live (dbt models + their reconciliation controls + the PySpark job + IaC/CD) | `banking_analytics.<NS>_staging / _intermediate / _marts / _curated` (per-run, disposable) |

The **before** state is deliberately a *partial* migration: the banking programs
(`load_customer_accounts`, `daily_transaction_processing`, `credit_risk_scoring`)
are already on `main` with a working reconciliation control, so the harness and
playbook are in place. What Devin converts **live** is the next wave — the
regulatory and insurance programs that are *not* yet on `main`.

The verification loop sits between them: every converted model is built into a
namespace and checked by reconciliation controls before it is trusted. The before
state is durable; the after state is namespaced and disposable — which is what
makes this safe to repeat and safe to run concurrently.

> **On "parity":** there is no live SAS runtime in this environment, so parity
> means source → target reconciliation against the SAS code as the source of
> truth (control totals, row counts, mapping parity, referential integrity) plus
> dbt tests — a deterministic contract, not a byte-for-byte SAS-vs-Databricks
> output diff.

---

<a id="part-1"></a>
## Part 1 — Devin Does the Migration

<a id="act-1"></a>
### Act 1 — Orient over the SAS estate

Open the SAS estate and ask Devin to explain it. With DeepWiki over the repo,
Devin typically maps an unfamiliar estate in minutes (coverage depends on repo
structure).

```
Using the ts-sas-legacy-analytics repo, give me a map of the SAS estate:
the banking and insurance programs, what each one reads and writes, the
LIBNAMEs, the macros and PROC FORMATs they depend on, and which programs are
set-based (good for dbt) vs procedural/multi-output (better as PySpark).
```

Expected: a tour of `Programs/Banking/*`, `Programs/Insurance/*`, the `Macro/`
and `Formats/` dependencies, and the Control-M-style `BatchJobs/` wrappers — with
a dbt-vs-PySpark recommendation per program.

<a id="act-2"></a>
### Act 2 — Convert one program live, with verification

The core beat. Paste the playbook prompt for one program. Devin reads the SAS,
writes the dbt model plus reconciliation controls, builds against the live
workspace, runs the controls, catches a divergence, fixes it, and produces a PR
with the reconciliation report.

```
!convert-sas-to-databricks

Convert the SAS program Programs/Banking/monthly_regulatory_reporting.sas in
the ts-sas-legacy-analytics estate into dbt models on Databricks, writing to
uc-data-migration-sas-to-databricks.

- SAS program: Programs/Banking/monthly_regulatory_reporting.sas
- Target model(s): mart_regulatory_rwa + mart_delinquency_aging
- Namespace: dev
```

**The verification beat (the real bug).** The SAS CASE maps `LOC` (line of
credit) → risk weight **1.00**. A plausible-looking conversion maps `LOC` → 0.75
to match the other revolving-credit products — which diverges from the source and
silently overstates capital relief. The parity control catches it:

```bash
make reconcile NS=dev
#   rwa_risk_weight_parity | FAIL | LOC=0.75 (source expects 1.00)
```

Restore `LOC` → 1.00 (source-faithful), re-run, and the report goes green:

```bash
make reconcile NS=dev
#   rwa_risk_weight_parity | PASS | all account_types match the SAS risk-weight mapping
```

The point: "looks reasonable" review would have shipped 0.75; the parity check
against the source did not. The full write-up is in the playbook at
`.workshop/playbooks/sas-to-databricks-conversion.devin.md` → *Worked example:
the LOC risk-weight divergence*.

<a id="act-3"></a>
### Act 3 — Fan out in parallel

Conversions are independent, so launch a Devin session per program. Each follows
the same playbook and produces its own verified PR — the same review bar applied
many times in parallel instead of once in series.

| Session | SAS program | Target |
|---|---|---|
| 1 | `Programs/Banking/monthly_regulatory_reporting.sas` | `mart_regulatory_rwa` + `mart_delinquency_aging` (the Act 2 worked example) |
| 2 | `Programs/Insurance/claims_processing.sas` | `stg_claims` + `int_claims_adjudication` (dbt) **and** the PySpark `claims_processing` job (procedural, multi-output) |
| 3 | `Programs/Insurance/policy_valuation.sas` | `int_policy_valuation` + `mart_loss_ratios` |
| 4 | `Programs/Reports/customer_profitability.sas` | `mart_customer_pnl` |

Each session uses its own namespace (`NS=session1`, …) so the live builds never
collide.

#### Parallelize from a single session (parent → child)

Instead of launching each session by hand, run one **orchestrator** session that
spawns a child Devin session per program and monitors them — one agent fanning
itself out across the wave. Paste:

```
Act as the orchestrator for a SAS->Databricks migration across multiple
programs, using child Devin sessions to parallelize the work.

Repos: read Cognition-Partner-Workshops/ts-sas-legacy-analytics (the SAS
source), write Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks.

Spawn one child Devin session per program below. Give each child both repos, its
own namespace (NS=child1, child2, ...), and tell it to follow the
!convert-sas-to-databricks playbook (the repo's Skill supplies the
`make demo-up NS=...` / `make reconcile NS=...` mechanics): treat the SAS source
as the source of truth and reproduce its logic exactly; flag (do not silently
fix) anything that looks wrong; add reconciliation controls (completeness, a
control total, and a parity check for every CASE/mapping); and build until
everything is green, with the reconciliation report included.

Programs:
1. Programs/Banking/monthly_regulatory_reporting.sas
   -> mart_regulatory_rwa + mart_delinquency_aging
2. Programs/Insurance/claims_processing.sas
   -> stg_claims + int_claims_adjudication (dbt) AND a PySpark job
      src/pyspark/claims_processing.py
3. Programs/Insurance/policy_valuation.sas
   -> int_policy_valuation + mart_loss_ratios
4. Programs/Reports/customer_profitability.sas -> mart_customer_pnl

After launching, monitor the child sessions until each program is converted with
a green reconciliation report. Then summarize the results and call out any
source-parity divergences the children caught (e.g. a risk-weight mapping that
did not match the SAS source).
```

The children inherit the organization's Databricks secrets, and each writes to
its own namespace (`child1`, `child2`, …) so the parallel builds never collide.
This is the same verified conversion loop as a single session — run many times at
once, from one parent.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

The gates that make every PR trustworthy:

- **CI** (`.github/workflows/dbt_ci.yml`): sqlfluff lint → `dbt parse` →
  `dbt test` (schema **and** reconciliation tests against the live workspace) →
  a reconciliation-report job that publishes the report as a build artifact.
- **Reconciliation controls** (`dbt_project/tests/reconcile_*.sql` +
  `verify/reconcile.py`): completeness, control totals, source-parity mappings,
  cross-engine referential integrity — documented as a contract in the
  `!convert-sas-to-databricks` playbook and the repo's Skill.
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

`dbt build` ends `PASS=104 ERROR=0` (13 models + schema tests + reconciliation
tests). Query the before and after side by side:

```sql
SELECT count(*) FROM banking_analytics.raw.cust_accounts;            -- before
SELECT count(*) FROM banking_analytics.dev_marts.mart_risk_scores;  -- after
SELECT * FROM banking_analytics.dev_marts.mart_customer_pnl ORDER BY net_profit DESC LIMIT 20;

-- LOC sits at risk_weight = 1.00, matching the SAS source
SELECT account_type, risk_weight, n_accounts, total_exposure, rwa
FROM banking_analytics.dev_marts.mart_regulatory_rwa ORDER BY account_type;
```

The procedural, multi-output `claims_processing.sas` becomes a custom PySpark job
(`src/pyspark/claims_processing.py`) — the alternative to dbt:

```sql
SELECT * FROM banking_analytics.dev_curated.claims_register     LIMIT 20;
SELECT * FROM banking_analytics.dev_curated.fraud_alerts;
SELECT * FROM banking_analytics.dev_curated.claims_review_queue;
```

Deploy as IaC, run the Workflow, then revert — `main` and the raw data are never
touched:

```bash
make deploy               # databricks bundle deploy -t dev (schedule paused, per-user)
make run-job   TARGET=dev # DAG: dbt_staging -> intermediate -> marts -> test, + pyspark in parallel
make destroy   TARGET=dev # remove the deployed job (revert CD)
make demo-down NS=dev     # drop dev_* output schemas (raw data untouched)
```

The pipeline is a Databricks Asset Bundle (`databricks.yml` +
`resources/daily_banking_pipeline.job.yml`). To show CD without merging to
`main`, trigger the GitHub Actions **deploy bundle** workflow manually
(Actions → Run workflow → pick target + namespace). On a real merge to `main` the
same workflow deploys automatically.

---

<a id="confirm-databricks"></a>
## Confirming Completion in Databricks

The migration milestone is complete when five things are true and visible in the
workspace: the converted models are **materialized** in Unity Catalog, the
**reconciliation controls are green** (parity proven against the SAS source), the
orchestrated **Workflow runs end to end** as IaC, the **before is untouched**
next to the after, and you can **prove it ran live**. Walk the workspace UI in
that order to show the work is done.

**1. Catalog Explorer — the models exist (after), the source is intact (before).**
Open **Catalog** → `banking_analytics`. Show the durable `raw` schema (the
"before" — `cust_accounts`, transactions, claims) sitting unchanged next to the
freshly built `<NS>_marts` / `<NS>_curated` schemas (the "after"). The converted
marts are populated tables with row counts and a column schema:

```sql
SELECT count(*) FROM banking_analytics.raw.cust_accounts;            -- before (durable)
SELECT count(*) FROM banking_analytics.dev_marts.mart_regulatory_rwa; -- after (built live)
```

**2. The source-parity beat — the number ties out to SAS.** Run the regulatory
mart grouped by account type and show `LOC` sitting at `risk_weight = 1.00`,
matching the SAS source (not the plausible-but-wrong 0.75). This is the
"verification caught a wrong conversion" proof, visible as data:

```sql
SELECT account_type, risk_weight, n_accounts, total_exposure, rwa
FROM banking_analytics.dev_marts.mart_regulatory_rwa ORDER BY account_type;
```

**3. The reconciliation report — every control PASS.** Show the report produced
by `make reconcile NS=dev` (also published as a CI build artifact on the PR):
each control — completeness, control totals, and per-mapping parity — reports
`PASS`. A green report is the definition of "done"; the code merely running is
not.

**4. Workflows — the pipeline runs end to end as IaC.** Open **Workflows** →
`daily_banking_pipeline` (the deployed Asset Bundle job) and show the most recent
run's task graph all green: `dbt_staging → intermediate → marts → test`, with the
PySpark `claims_processing` task running in parallel. This proves the converted
estate runs as an orchestrated, scheduled pipeline — not just ad-hoc queries.

**5. Query History — proof it executed live.** Open the SQL warehouse's **Query
History** to show the `dbt build` statements that just ran against the workspace,
with durations. This confirms the conversion was built and verified live, here,
during the session.

For a multi-program or fan-out run, repeat step 1 across the per-session
namespaces (`session1_marts`, `child1_marts`, …) to show several converted
programs landing independently and all reconciling green — the milestone achieved
in parallel.

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

- The value on display is **Devin doing the migration**: reading an unfamiliar SAS estate, converting programs off a reusable playbook, and proving each conversion against the source — not just a finished artifact to run.
- **Confidence comes from programmatic verification.** Reconciliation controls (completeness, control totals, source-parity mappings) gate every build and CI run, and the demo shows a real divergence (LOC risk weight 0.75 vs the source's 1.00) being caught and fixed. "Looks reasonable" review would have missed it.
- The **SAS source is the source of truth**: conversions reproduce legacy logic faithfully (quirks flagged, not silently "fixed"); remediation is a separate, deliberate decision.
- Conversions are **independent and parallelizable** — multiple Devin sessions convert multiple programs at once, each producing its own verified PR. The playbook keeps every run consistent.
- dbt covers set-based transforms; a custom **PySpark** job fits procedural, multi-output programs. **IaC + CI/CD** (Asset Bundles + GitHub Actions) replace hand-promoted SAS packages, and namespaced outputs + one-command revert make the whole story safe to repeat and run concurrently.

---

<a id="how-devin"></a>
## How Devin Produced This

This reference solution was built by Devin working from the SAS source estate and
the partially-built dbt target: it analyzed the SAS programs, built the missing
insurance/regulatory models, generated synthetic data, authored the reconciliation
harness, the Asset Bundle, and the CD workflow, and validated the whole pipeline
by deploying and running it live in a Databricks workspace. The same Context Loop
(source analysis → target mapping → produce PR → programmatic verification → human
review → refine) described in
[`labs/data-engineering/sas-migration-analysis.md`](../../labs/data-engineering/sas-migration-analysis.md)
applies here, with the conversion procedure codified as the
`!convert-sas-to-databricks` Devin Playbook (source in the code repo at
`.workshop/playbooks/sas-to-databricks-conversion.devin.md`).
