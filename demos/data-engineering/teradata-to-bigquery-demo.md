# Teradata → BigQuery — Data Warehouse Migration Demo

A single linear demo that shows Devin migrating a Teradata data warehouse to
**BigQuery Standard SQL (GoogleSQL)** with **verifiable confidence**: orient over
the legacy estate, convert the analytic views live, prove parity through a
programmatic harness, catch a real divergence and fix it, then fan the work out
across the rest of the estate in parallel. The verification loop runs entirely
locally (DuckDB stands in for BigQuery), so the whole thread is safe to repeat
with no GCP account.

The prompts below invoke the `!convert-teradata-to-bigquery` Devin Playbook — the
reusable conversion procedure — whose source lives in the code repo at
[`uc-dw-migration-teradata-to-bigquery/.workshop/playbooks/convert-teradata-to-bigquery.devin.md`](https://github.com/Cognition-Partner-Workshops/uc-dw-migration-teradata-to-bigquery/blob/main/.workshop/playbooks/convert-teradata-to-bigquery.devin.md).
The repo-specific verification mechanics come from that repo's Skill
(`.agents/skills/teradata-to-bigquery/SKILL.md`).

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Does the Migration](#part-1)
  - [Act 1 — Orient over the Teradata estate](#act-1)
  - [Act 2 — Convert the views live, with verification](#act-2)
  - [Act 3 — Fan out in parallel](#act-3)
  - [Act 4 — Confidence = programmatic verification](#act-4)
- [Part 2 — Run the Produced Artifact](#part-2)
- [Key Takeaways](#key-takeaways)
- [How Devin Produced This](#how-devin)

---

<a id="quick-start"></a>
## Quick Start

Run the verification loop from the repo root — no credentials required:

```bash
pip install -r verify/requirements.txt

# On main (no converted SQL yet) → the harness reports the work as not done:
python verify/run_parity.py          # exits non-zero

# After the conversion (or: git checkout bigquery-reference) → green:
python verify/run_parity.py          # RESULT: PASS — all parity metrics match golden
```

No Teradata instance and no GCP account are needed: DuckDB executes the converted
GoogleSQL locally as a stand-in for BigQuery Standard SQL.

---

<a id="repositories"></a>
## Repositories

- [uc-dw-migration-teradata-to-bigquery](https://github.com/Cognition-Partner-Workshops/uc-dw-migration-teradata-to-bigquery) — the Teradata retail-banking warehouse (DDL, views, stored procedures, macros, BTEQ, seed data), the DuckDB parity harness, the conversion Playbook source (`.workshop/playbooks/`), and the repo Skill (`.agents/skills/`). `main` is the "before"; the reference conversion is on the `bigquery-reference` branch.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Data |
|---|---|---|
| **Before** | `main`: the Teradata source (DDL/views/SPs/macros/BTEQ), the parity harness, the conversion Playbook source, and the repo Skill. No converted SQL. | `data/seed/*.csv` — hand-curated dimensions + deterministically generated facts (durable; never overwritten) |
| **After** | a PR branch with the analytic views converted live to GoogleSQL under `bigquery/views/`, plus a `MIGRATION_RUNBOOK.md` | the same seeds, loaded into in-memory DuckDB per run (disposable) |

The **before** state is deliberately incomplete: the Teradata estate and the
harness are in place, but `bigquery/views/` is empty, so `python
verify/run_parity.py` fails — the work is provably not done. What Devin produces
**live** is the GoogleSQL conversion that turns the harness green.

> **On "parity":** there is no live Teradata runtime here, so parity means the
> converted GoogleSQL, executed against the seed data, reproduces deterministic
> row counts and measure sums (the golden checksums) — including the `CSUM`/`MAVG`
> window translations. It is a deterministic contract, not a byte-for-byte
> Teradata-vs-BigQuery output diff.

---

<a id="part-1"></a>
## Part 1 — Devin Does the Migration

<a id="act-1"></a>
### Act 1 — Orient over the Teradata estate

Open the repo and ask Devin to explain it. With DeepWiki over the repo, Devin
typically maps an unfamiliar estate in minutes (coverage depends on repo
structure).

```
Using the uc-dw-migration-teradata-to-bigquery repo, give me a map of the
Teradata warehouse: the dimension and fact tables in ddl/tables/, the views in
ddl/views/, the stored procedures and macros in dml/, the BTEQ scripts, and the
Teradata-specific features each one depends on (SET/MULTISET, PI/PPI, COMPRESS,
QUALIFY, ZEROIFNULL, CSUM, MAVG, HASHROW). Note which objects are the parity
harness's targets.
```

Expected: a tour of the 7 tables, 3 views, 3 stored procedures, 3 macros, and the
BTEQ scripts, with the Teradata constructs called out and the three
harness-checked views (`vw_regulatory_large_transactions`,
`vw_branch_monthly_performance`, `vw_customer_360`) identified.

<a id="act-2"></a>
### Act 2 — Convert the views live, with verification

The core beat. Paste the playbook prompt. Devin reads the Teradata views, writes
the GoogleSQL conversions into `bigquery/views/`, runs the parity harness, catches
a divergence, fixes it, and produces a PR with the parity report.

```
!convert-teradata-to-bigquery

Convert the three Teradata views in ddl/views/ to BigQuery Standard SQL in the
uc-dw-migration-teradata-to-bigquery repo. Write the converted CREATE OR REPLACE
VIEW statements into bigquery/views/ so the parity harness can run them, then run
`python verify/run_parity.py` and iterate until every metric matches golden.
```

**The verification beat (the real bug).** Teradata `MAVG(volume, 3, month)` in
`vw_branch_performance` is a 3-row moving average (current month + 2 preceding).
A plausible-looking conversion writes `ROWS BETWEEN 3 PRECEDING AND CURRENT ROW`,
which averages 4 rows. The harness catches it:

```bash
python verify/run_parity.py
#   MISMATCH 20_branch_performance.sum_moving_avg_volume  expected 6789480.28  got 6764463.37
#   RESULT: FAIL — 1 metric(s) diverged from golden.
```

Fix the frame to `ROWS BETWEEN 2 PRECEDING AND CURRENT ROW` (source-faithful),
re-run, and the report goes green:

```bash
python verify/run_parity.py
#   RESULT: PASS — all 13 parity metrics match golden.
```

The point: "looks reasonable" review would have shipped the 4-row window; the
parity check against the golden contract did not. The full write-up is in the
playbook at `.workshop/playbooks/convert-teradata-to-bigquery.devin.md` →
*Worked example — a real bug the parity loop caught*.

<a id="act-3"></a>
### Act 3 — Fan out in parallel

Conversions are independent, so launch a Devin session per object group. Each
follows the same playbook and produces its own verified PR — the same review bar
applied many times in parallel instead of once in series.

```
Act as the orchestrator for a Teradata->BigQuery migration across the estate,
using child Devin sessions to parallelize the work.

Repo: Cognition-Partner-Workshops/uc-dw-migration-teradata-to-bigquery.

Spawn one child Devin session per object group below. Tell each child to follow
the !convert-teradata-to-bigquery playbook (the repo's Skill supplies the
`python verify/run_parity.py` mechanics): treat the Teradata SQL as the source of
truth and reproduce its logic exactly; flag (do not silently fix) anything that
looks wrong; and build until the parity harness is green, with the report
included in its PR.

Object groups:
1. ddl/views/ -> bigquery/views/ (the three analytic views; the Act 2 example)
2. ddl/tables/ -> bigquery/tables/ (datatype + partition/cluster mapping)
3. dml/stored_procedures/ + dml/macros/ -> bigquery/procedures/ (scripting / table functions)
4. dml/scripts/ (BTEQ) -> a bq load / EXPORT DATA + orchestration plan

After launching, monitor the child sessions until each group is converted with a
green parity report. Then summarize results and call out any source-parity
divergences the children caught (e.g. a window-frame off-by-one).
```

This is the same verified conversion loop as a single session — run many times at
once, from one parent.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

The gates that make every PR trustworthy:

- **Parity harness** (`verify/run_parity.py` + `verify/checks/*.sql` +
  `verify/expected/parity_checksums.csv`): loads the seeds, applies the converted
  views, and compares deterministic row counts and measure sums to golden —
  documented as a contract in the `!convert-teradata-to-bigquery` playbook and the
  repo's Skill.
- **Branch discipline**: `main` stays the durable before-state; the converted SQL
  lands on a PR branch, never merged into `main`.
- **Devin Review**: an automated reviewer on every PR.

A conversion is "done" when the parity harness is green, on the PR — not when the
SQL merely parses.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifact

Show the converted estate verified end to end, with a repeatable before/after.

```bash
# Before: on main, the harness proves the work is not done
git checkout main
python verify/run_parity.py            # exits non-zero (no bigquery/views/)

# After: the reference conversion goes green
git checkout bigquery-reference
python verify/run_parity.py            # RESULT: PASS — all 13 parity metrics match golden
```

Inspect the converted GoogleSQL and the parity contract side by side:

```bash
sed -n '1,40p' bigquery/views/02_vw_branch_monthly_performance.sql   # MAVG -> 2 PRECEDING
cat verify/checks/20_branch_performance.sql                          # what parity means
cat verify/expected/parity_checksums.csv                             # the golden numbers
```

`main` and the seed data are never modified — the after-state is the converted SQL
plus an in-memory DuckDB run, which makes the whole thread safe to repeat.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The value on display is **Devin doing the migration**: reading an unfamiliar Teradata estate, converting objects off a reusable playbook, and proving each conversion against a golden contract — not just a finished artifact to read.
- **Confidence comes from programmatic verification.** The parity harness gates every run, and the demo shows a real divergence (the `MAVG` 3-row vs 4-row window) being caught and fixed. "Looks reasonable" review would have missed it.
- The **Teradata source is the source of truth**: conversions reproduce legacy logic faithfully (quirks flagged, not silently "fixed"); remediation is a separate, deliberate decision.
- Conversions are **independent and parallelizable** — multiple Devin sessions convert different object groups at once, each producing its own verified PR. The playbook keeps every run consistent.
- The loop is **self-contained and reversible**: DuckDB stands in for BigQuery so there is no cloud dependency, `main` stays the before-state, and the whole thread is safe to repeat and run concurrently.

---

<a id="how-devin"></a>
## How Devin Produced This

This reference solution was built by Devin working from the Teradata source: it
analyzed the warehouse, authored the DuckDB-backed parity harness and golden
checksums, produced the reference GoogleSQL conversion on the `bigquery-reference`
branch, and validated the whole loop end to end. The same Context Loop (source
analysis → target mapping → produce PR → programmatic verification → human review
→ refine) used across the data-engineering modules applies here, with the
conversion procedure codified as the `!convert-teradata-to-bigquery` Devin
Playbook (source in the code repo at
`.workshop/playbooks/convert-teradata-to-bigquery.devin.md`).
