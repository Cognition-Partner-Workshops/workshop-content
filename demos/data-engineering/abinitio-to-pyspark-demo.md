# Ab Initio → PySpark — End-to-End Migration Demo

A single linear demo that shows Devin migrating an Ab Initio ETL estate to
runnable PySpark with **verifiable confidence**: orient over the legacy graphs,
convert one graph live, prove parity with the source through programmatic
reconciliation, catch a real divergence and fix it, then fan the work out across
many graphs in parallel. Everything here runs **locally** — no cloud workspace
required — so it is fast to set up and safe to repeat.

The prompts below invoke the `!convert-abinitio-to-pyspark` Devin Playbook — the
reusable conversion procedure — whose source lives in the code repo at
[`uc-data-migration-abinitio-to-pyspark/.workshop/playbooks/abinitio-to-pyspark-conversion.devin.md`](https://github.com/Cognition-Partner-Workshops/uc-data-migration-abinitio-to-pyspark/blob/main/.workshop/playbooks/abinitio-to-pyspark-conversion.devin.md).
The repo-specific `make demo-up` / `make reconcile` mechanics come from that
repo's Skill (`.agents/skills/abinitio-to-pyspark-conversion/SKILL.md`).

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
- [Concurrent Runs](#concurrent)
- [Key Takeaways](#key-takeaways)
- [How Devin Produced This](#how-devin)

---

<a id="quick-start"></a>
## Quick Start

Everything runs locally with PySpark — no Databricks/cloud account needed. From
the repo root:

```bash
pip install -r requirements.txt

make demo-up   NS=dev    # before -> after: seed raw extracts + run jobs + reconcile
make reconcile NS=dev    # source -> target reconciliation report
make test                # end-to-end pipeline + reconciliation tests
make demo-down NS=dev    # drop this namespace's outputs (raw data untouched)
```

Prerequisites: Python 3.10+ and Java 8/11/17 (PySpark needs a JVM). Outputs land
in `out/<NS>/...`, so multiple runs (`NS=dev`, `NS=alice`, …) never collide.

---

<a id="repositories"></a>
## Repositories

- [ts-python-abinitio-etl](https://github.com/Cognition-Partner-Workshops/ts-python-abinitio-etl) — the legacy Ab Initio estate (graphs, DML record formats, PSET parameter sets, CDC, KornShell/AutoSys orchestration). Read-only reference for the "before".
- [uc-data-migration-abinitio-to-pyspark](https://github.com/Cognition-Partner-Workshops/uc-data-migration-abinitio-to-pyspark) — the PySpark target: converted jobs, DML→StructType mapping, a local reconciliation harness, a deterministic seeder, the conversion playbook source (`.workshop/playbooks/`), and the repo Skill (`.agents/skills/`).

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Data |
|---|---|---|
| **Before** | `main`: the **customer + orders** graphs already converted (staging → marts), plus the reconciliation harness, the seeder, the conversion playbook source (`.workshop/playbooks/`), and the repo Skill. The Ab Initio estate lives in `ts-python-abinitio-etl`. | `data/raw/*.dat` (durable; regenerated deterministically, never overwritten by jobs) |
| **After** | a PR branch with the **transactions** graph converted live (the PySpark job + its reconciliation control) | `out/<NS>/staging \| intermediate \| marts \| curated` (per-run, disposable) |

The **before** state is deliberately a *partial* migration: the customer and
orders graphs are already on `main` with working reconciliation controls, so the
harness and playbook are in place. What Devin converts **live** is the next wave —
the transactions detail graph that is *not* yet on `main`.

The verification loop sits between them: every converted job is run into a
namespace and checked by reconciliation controls before it is trusted. The before
state is durable; the after state is namespaced and disposable — which is what
makes this safe to repeat and safe to run concurrently.

> **On "parity":** there is no live Co>Operating System runtime in this
> environment, so parity means source → target reconciliation against the Ab
> Initio code as the source of truth (control totals, row counts, per-class
> parity) — a deterministic contract, not a byte-for-byte Ab Initio-vs-Spark
> output diff.

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
for a dbt-style PySpark job) or procedural/multi-output (a custom PySpark job).
```

Expected: a tour of `graphs/cdc_processor.py` and `graphs/parallel_loader.py`,
the eight `dml/*.dml` record formats (including the nested `transaction_detail.dml`),
the three `psets/pset_templates/*.pset`, and the `scripts/run_daily_orders.ksh` /
`run_customer_cdc.ksh` wrappers — with a set-based-vs-procedural call per pipeline.

<a id="act-2"></a>
### Act 2 — Convert one graph live, with verification

The core beat. Paste the playbook prompt for one graph. Devin reads the DML and
graph, writes the PySpark job plus its reconciliation control, runs it into a
namespace, runs the control, catches a divergence, fixes it, and produces a PR
with the reconciliation report.

```
!convert-abinitio-to-pyspark

Convert the Ab Initio transactions detail pipeline from the ts-python-abinitio-etl
estate into a PySpark job in uc-data-migration-abinitio-to-pyspark.

- DML: dml/transaction_detail.dml (nested merchant_info + line_items[] array)
- Source layout/orchestration: scripts/run_daily_orders.ksh
- Target: src/jobs/curated_transactions.py writing out/<NS>/curated/transactions
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

Reproduce the DML default — `coalesce`/empty-string handling that yields exactly
`UNKNOWN` — re-run, and the report goes green:

```bash
make reconcile NS=dev
#   transactions_channel_parity | PASS | all transactions carry a channel (DML null("UNKNOWN") default applied)
```

The point: "looks reasonable" review would have shipped `NULL`; the parity check
against the source did not. The full write-up is in the playbook at
`.workshop/playbooks/abinitio-to-pyspark-conversion.devin.md` → *Worked example:
the transaction channel default divergence*.

<a id="act-3"></a>
### Act 3 — Fan out in parallel

Conversions are independent, so launch a Devin session per graph. Each follows
the same playbook and produces its own verified PR — the same review bar applied
many times in parallel instead of once in series.

| Session | Ab Initio pipeline | Target |
|---|---|---|
| 1 | `dml/transaction_detail.dml` + `scripts/run_daily_orders.ksh` | `src/jobs/curated_transactions.py` (the Act 2 worked example) |
| 2 | `graphs/cdc_processor.py` + `psets/pset_templates/customer_cdc.pset` | `src/jobs/customer_cdc.py` (compare-by-key + row hash) |
| 3 | `dml/order_items.dml` (nested `item_names[]`/`item_quantities[]`) | `src/jobs/int_order_items.py` (explode arrays) |

Each session uses its own namespace (`NS=session1`, …) so the local outputs never
collide.

#### Parallelize from a single session (parent → child)

Instead of launching each session by hand, run one **orchestrator** session that
spawns a child Devin session per graph and monitors them — one agent fanning
itself out across the wave. Paste:

```
Act as the orchestrator for an Ab Initio->PySpark migration across multiple
graphs, using child Devin sessions to parallelize the work.

Repos: read Cognition-Partner-Workshops/ts-python-abinitio-etl (the Ab Initio
source), write Cognition-Partner-Workshops/uc-data-migration-abinitio-to-pyspark.

Spawn one child Devin session per pipeline below. Give each child both repos, its
own namespace (NS=child1, child2, ...), and tell it to follow the
!convert-abinitio-to-pyspark playbook (the repo's Skill supplies the
`make demo-up NS=...` / `make reconcile NS=...` mechanics): treat the Ab Initio
source as the source of truth and reproduce its logic exactly, including DML
null(...) defaults; flag (do not silently fix) anything that looks wrong; add
reconciliation controls (completeness, a control total, and a parity check for
every default/mapping/CDC class); and build until everything is green, with the
reconciliation report included.

Pipelines:
1. dml/transaction_detail.dml (+ scripts/run_daily_orders.ksh)
   -> src/jobs/curated_transactions.py
2. graphs/cdc_processor.py (+ psets/pset_templates/customer_cdc.pset)
   -> src/jobs/customer_cdc.py (compare-by-key + row hash)
3. dml/order_items.dml -> src/jobs/int_order_items.py (explode arrays)

After launching, monitor the child sessions until each pipeline is converted with
a green reconciliation report. Then summarize the results and call out any
source-parity divergences the children caught (e.g. the channel default).
```

The children each write to their own namespace (`child1`, `child2`, …) so the
parallel runs never collide. This is the same verified conversion loop as a single
session — run many times at once, from one parent.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

The gates that make every PR trustworthy:

- **CI** (`.github/workflows/ci.yml`): ruff lint → a full `demo-up` run
  (seed → jobs → reconciliation) on a CI namespace → the pytest suite.
- **Reconciliation controls** (`verify/reconcile.py`): completeness, control
  totals, per-date parity, and per-class parity (the channel default) — documented
  as a contract in the `!convert-abinitio-to-pyspark` playbook and the repo's
  Skill. The script exits non-zero on any FAIL, so it gates CI and pre-merge.
- **Devin Review**: an automated reviewer on every PR.

A conversion is "done" when the source-parity controls are green, in CI, on the
PR — not when the code merely runs.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifact

Show the converted estate running end to end, with a repeatable before/after.

```bash
make demo-up   NS=dev     # seed (idempotent) + run jobs into out/dev/...
make reconcile NS=dev     # all controls PASS
```

The reconciliation report ends with every control green (completeness, control
total, per-date parity, and — once the transactions graph is converted — the
channel-default parity). Inspect the before and after side by side:

```bash
# before: the durable raw Ab Initio extracts
head -3 data/raw/transactions.dat

# after: the converted, reconciled outputs
ls out/dev/staging out/dev/marts out/dev/curated
python -c "import pandas as pd; print(pd.read_parquet('out/dev/curated/transactions').head())"
```

Tear down the after-state for this namespace; the raw data is never touched:

```bash
make demo-down NS=dev     # drop out/dev/* (raw extracts untouched)
```

---

<a id="concurrent"></a>
## Concurrent Runs

Each output path is namespaced, so multiple runs — and the parallel fan-out in
Act 3 — coexist with no collisions:

```bash
make demo-up   NS=alice
make demo-up   NS=run2
make reconcile NS=alice
make demo-down NS=alice
```

---

<a id="key-takeaways"></a>
## Key Takeaways

- The value on display is **Devin doing the migration**: reading an unfamiliar Ab Initio estate, converting graphs off a reusable playbook, and proving each conversion against the source — not just a finished artifact to run.
- **Confidence comes from programmatic verification.** Reconciliation controls (completeness, control totals, per-class parity) gate every run and CI build, and the demo shows a real divergence (blank channel → `NULL` vs the source's `UNKNOWN`) being caught and fixed. "Looks reasonable" review would have missed it.
- The **Ab Initio source is the source of truth**: conversions reproduce legacy logic faithfully, including DML `null(...)` defaults (quirks flagged, not silently "fixed"); remediation is a separate, deliberate decision.
- Conversions are **independent and parallelizable** — multiple Devin sessions convert multiple graphs at once, each producing its own verified PR. The playbook keeps every run consistent.
- The whole story runs **locally** — PySpark, a deterministic seeder, namespaced outputs, and a one-command teardown make it safe to repeat and run concurrently, with no cloud account required.

---

<a id="how-devin"></a>
## How Devin Produced This

This reference solution was built by Devin working from the Ab Initio source
estate and the partially-built PySpark target: it analyzed the graphs and DML,
built the converted customer/orders jobs, generated deterministic synthetic
extracts, authored the reconciliation harness, and validated the whole pipeline by
running it end to end locally. The same Context Loop (source analysis → target
mapping → produce PR → programmatic verification → human review → refine)
described in
[`labs/data-engineering/abinitio-migration-analysis.md`](../../labs/data-engineering/abinitio-migration-analysis.md)
applies here, with the conversion procedure codified as the
`!convert-abinitio-to-pyspark` Devin Playbook (source in the code repo at
`.workshop/playbooks/abinitio-to-pyspark-conversion.devin.md`).
