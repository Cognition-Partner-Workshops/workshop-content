# SAS → Snowflake — Data Migration Validation Demo

A single linear demo that shows Devin validating a SAS-to-Snowflake data
migration with **verifiable confidence**: orient over the legacy SAS estate,
run a programmatic reconciliation against the Snowflake target, catch a real
divergence (the `is_active` filter that silently drops inactive accounts), fix
it, then fan the work out across many tables in parallel. The second half runs
the produced validation harness end to end and confirms completion in
Snowflake's worksheet UI.

The prompts below invoke the `!validate-sas-to-snowflake` Devin Playbook — the
reusable validation procedure — whose source lives in the code repo at
[`uc-data-migration-sas-to-snowflake/.workshop/playbooks/sas-to-snowflake-migration.devin.md`](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-snowflake/blob/main/.workshop/playbooks/sas-to-snowflake-migration.devin.md).
The repo-specific `make validate` / lineage-tracing mechanics come from the
repo's Skill (`.agents/skills/sas-to-snowflake-migration/SKILL.md`).

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Validates the Migration](#part-1)
  - [Act 1 — Orient over the SAS estate](#act-1)
  - [Act 2 — Validate one table live, with verification](#act-2)
  - [Act 3 — Fan out in parallel](#act-3)
  - [Act 4 — Confidence = programmatic verification](#act-4)
- [Part 2 — Run the Produced Artifact](#part-2)
- [Confirming Completion in Snowflake](#confirm-snowflake)
- [Concurrent Runs](#concurrent)
- [Key Takeaways](#key-takeaways)
- [How Devin Produced This](#how-devin)

---

<a id="quick-start"></a>
## Quick Start

Install dependencies and run the full validation suite from the repo root:

```bash
pip install pandas sas7bdat

# Validate all tables against the baseline scenario:
make validate SCENARIO=Scenario1

# Validate a single table:
make validate TABLE=MONTHLY_AMB SCENARIO=Scenario1

# Launch the interactive Streamlit dashboard:
make dashboard
```

Prerequisites: Python 3.9+, pandas, sas7bdat. For live Snowflake validation,
also set `SNOWFLAKE_ACCOUNT`, `SNOWFLAKE_USER`, `SNOWFLAKE_PASSWORD`,
`SNOWFLAKE_DATABASE`, `SNOWFLAKE_SCHEMA` environment variables.

---

<a id="repositories"></a>
## Repositories

- [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics) — the legacy SAS estate (banking/insurance programs, macros, formats, batch orchestration). Read-only reference for the "before".
- [uc-data-migration-sas-to-snowflake](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-snowflake) — the Snowflake migration target: validation harness, sample datasets (SAS `.sas7bdat` + Snowflake CSV exports), Collibra-style lineage metadata, the Streamlit validation dashboard, the playbook source (`.workshop/playbooks/`), and the repo Skill (`.agents/skills/`).

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Data |
|---|---|---|
| **Before** | `main`: the validation harness (`verify/reconcile.py`), lineage metadata, Streamlit dashboard, the playbook source, and the repo Skill. The SAS estate lives in `ts-sas-legacy-analytics`. | SAS `.sas7bdat` source files (durable, immutable) + per-scenario Snowflake CSV exports |
| **After** | a PR branch with any migration fixes, updated validation rules, and a green reconciliation report | corrected Snowflake CSV exports or SQL migration scripts in the scenario directory |

The **before** state includes the validation harness and SAS baseline on `main`.
Snowflake CSV exports live in scenario directories (`Scenario1`, `Scenario2`)
representing different migration states. What Devin validates **live** is the
parity between the SAS source (the immutable baseline) and the Snowflake target
(the migration output).

The verification loop sits between them: every migrated table is checked by
the reconciliation harness (Row Count, Sum Amount, Distinct Count, Not Null,
Uniqueness) before it is trusted. The before state is immutable; the after
state is scenario-scoped and disposable — which is what makes this safe to
repeat and safe to run concurrently.

> **On "parity":** there is no live SAS runtime in this environment, so parity
> means source → target reconciliation against the SAS `.sas7bdat` files as the
> source of truth (row counts, control totals, cardinality, null checks) — a
> deterministic contract, not a byte-for-byte SAS-vs-Snowflake output diff.

---

<a id="part-1"></a>
## Part 1 — Devin Validates the Migration

<a id="act-1"></a>
### Act 1 — Orient over the SAS estate

Open the SAS estate and ask Devin to explain it. With DeepWiki over the repo,
Devin typically maps an unfamiliar estate in minutes (coverage depends on repo
structure).

```
Using the ts-sas-legacy-analytics repo, give me a map of the SAS estate:
the banking and insurance programs, what each one reads and writes, the
LIBNAMEs, the macros and PROC FORMATs they depend on, and the data flow
from raw sources through staging to curated/reporting layers.
```

Expected: a tour of `Programs/Banking/*` (load_customer_accounts,
daily_transaction_processing, credit_risk_scoring, monthly_regulatory_reporting),
`Programs/Insurance/*` (claims_processing, policy_valuation), the `Macro/` and
`Formats/` dependencies, and the Control-M-style `BatchJobs/` wrappers — with a
lineage map from `RAW` → `STAGING` → `CURATED` → `REPORTS`.

<a id="act-2"></a>
### Act 2 — Validate one table live, with verification

The core beat. Paste the playbook prompt for one table. Devin loads the SAS
source and Snowflake target, runs the validation suite, catches a divergence,
traces its root cause through the lineage, fixes it, and produces a PR with
the reconciliation report.

```
!validate-sas-to-snowflake

Validate the MONTHLY_AMB table migration from SAS to Snowflake.

- SAS table: MONTHLY_AMB (source: sample_data/MONTHLY_AMB.sas7bdat)
- Snowflake table: MONTHLY_AMB (target: sample_data/Scenario1/)
- Repo: Cognition-Partner-Workshops/uc-data-migration-sas-to-snowflake
```

**The verification beat (the real bug).** The SAS source computes Monthly
Average Balance for **all** accounts — active and inactive. The Snowflake
migration's `JOB03_CALC_AMB.sql` added a `WHERE c.is_active = 'ACTIVE'` clause
that excludes inactive accounts. The reconciliation harness catches it:

```
make validate TABLE=MONTHLY_AMB SCENARIO=Scenario1

  Row Count:      SAS=1980   SF=1709    -> FAIL <<<
  Distinct Count [customer_id]:
                  SAS=1000   SF=942     -> FAIL <<<
  Sum Amount [average_monthly_balance]:
                  SAS=5702329  SF=4928656  -> FAIL <<<
```

271 rows missing (inactive accounts excluded), 58 customers lost entirely,
$773,673 gap in the control total. The lineage trace pinpoints the divergence
at the `JOB03_CALC_AMB` transformation node — the SF lineage metadata shows the
extra `WHERE c.is_active = 'ACTIVE'` filter that the SAS version does not have.

The fix: remove the extra filter from the Snowflake SQL, re-export, and re-run:

```
make validate TABLE=MONTHLY_AMB SCENARIO=Scenario1
  Row Count:      SAS=1980   SF=1980    -> PASS
  Distinct Count: SAS=1000   SF=1000    -> PASS
  Sum Amount:     SAS=5702329  SF=5702329  -> PASS
```

The point: "looks reasonable" review would have shipped the filtered data;
the parity check against the SAS source did not. The full write-up is in the
playbook at `.workshop/playbooks/sas-to-snowflake-migration.devin.md` →
*Worked example: the `is_active` filter divergence*.

<a id="act-3"></a>
### Act 3 — Fan out in parallel

Table validations are independent, so launch a Devin session per table. Each
follows the same playbook and produces its own verified PR — the same review
bar applied many times in parallel instead of once in series.

| Session | Table | Key validations |
|---|---|---|
| 1 | `MONTHLY_AMB` | Row Count, Sum Amount, Distinct Count (the Act 2 worked example) |
| 2 | `CUST_ACCOUNTS` | Row Count, Not Null (is_active, start_date), Uniqueness (account_id) |
| 3 | `DAILY_BALANCE` | Row Count, Sum Amount (end_of_day_balance), date-format parity |

Each session uses its own scenario directory so the validation runs never
collide.

#### Parallelize from a single session (parent → child)

Instead of launching each session by hand, run one **orchestrator** session that
spawns a child Devin session per table and monitors them — one agent fanning
itself out across the wave. Paste:

```
Act as the orchestrator for a SAS-to-Snowflake migration validation
across multiple tables, using child Devin sessions to parallelize.

Repo: Cognition-Partner-Workshops/uc-data-migration-sas-to-snowflake

Spawn one child Devin session per table below. Give each child the repo,
its own scenario directory, and tell it to follow the
!validate-sas-to-snowflake playbook (the repo's Skill supplies the
make validate mechanics): treat the SAS .sas7bdat as the source of truth;
flag (do not silently fix) anything that diverges; run the full validation
suite; and include the reconciliation report in the PR.

Tables:
1. MONTHLY_AMB (Scenario1) — Row Count, Distinct Count, Sum Amount
2. CUST_ACCOUNTS (Scenario1) — Row Count, Not Null, Uniqueness
3. DAILY_BALANCE (Scenario1) — Row Count, Sum Amount
4. DAILY_BALANCE (Scenario2) — Row Count, Sum Amount, date-format check

After all children finish, summarize which tables passed and which had
divergences, and call out the root cause for each failure.
```

The children inherit the organization's Snowflake secrets, and each writes to
its own scenario directory so the parallel validations never collide. This is
the same verified reconciliation loop as a single session — run many times at
once, from one parent.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

The gates that make every PR trustworthy:

- **CLI harness** (`verify/reconcile.py`): loads SAS source and Snowflake
  target, runs all configured validation rules (Row Count, Sum Amount, Distinct
  Count, Not Null, Uniqueness), exits non-zero on any FAIL — a programmatic
  gate, not a visual inspection.
- **Lineage tracing** (`lineage/SAS_lineage.json` + `lineage/SF_lineage.json`):
  Collibra-style metadata that maps every transformation from SAS to Snowflake,
  enabling automated root-cause analysis when a validation fails.
- **Streamlit dashboard** (`app4.py`): interactive visual interface for
  exploring validation results, upstream lineage graphs, and LLM-generated
  recommendations — the human-friendly layer on top of the programmatic checks.
- **Devin Review**: an automated reviewer on every PR.

A migration is "done" when the reconciliation harness is green for every table
in scope — not when the data merely loads.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifact

Show the validation harness running end to end, with a repeatable before/after.

```bash
# Full validation suite against Scenario1 (baseline):
make validate SCENARIO=Scenario1

# Full validation against Scenario2 (delta — has additional divergences):
make validate SCENARIO=Scenario2
```

Scenario1 (after fix) ends with all checks PASS. Scenario2 shows the delta
migration's additional issues — `DAILY_BALANCE` has 122,760 SAS rows vs only
16,802 in Snowflake (massive row loss) plus date-format mismatches
(`YYYY-MM-DD` in SAS vs `DD-MM-YYYY` in Snowflake).

Query the before and after side by side (or in Snowsight):

```sql
-- Before: SAS source row counts (from .sas7bdat baseline)
-- CUST_ACCOUNTS: 1,980 rows
-- DAILY_BALANCE: 122,760 rows
-- MONTHLY_AMB: 1,980 rows (including inactive accounts)

-- After (Scenario1, corrected): Snowflake target matches
SELECT COUNT(*) FROM FINANCE_DB.STAGING.MONTHLY_AMB;          -- 1,980
SELECT SUM(average_monthly_balance) FROM FINANCE_DB.STAGING.MONTHLY_AMB;
-- 5,702,329 (matches SAS)

-- The is_active divergence, visible as data:
SELECT is_active, COUNT(*) AS n_accounts
FROM FINANCE_DB.RAW.CUST_ACCOUNTS
GROUP BY is_active;
-- ACTIVE: 1,709  INACTIVE: 271  (the 271 rows the filter dropped)
```

Launch the Streamlit dashboard for interactive exploration:

```bash
make dashboard
```

The dashboard lets you select tables, run validation rules interactively, view
SAS and Snowflake lineage graphs side by side, trace upstream dependencies for
failures, and generate LLM-powered root-cause analysis reports.

---

<a id="confirm-snowflake"></a>
## Confirming Completion in Snowflake

The migration milestone is complete when four things are true and visible:
the migrated tables are **loaded** in Snowflake, the **reconciliation checks
are green** (parity proven against the SAS source), the **lineage is traced**
for every table, and you can **prove it ran live**. Walk the evidence in that
order.

**1. Snowsight — the tables exist with correct row counts.** Open Snowsight →
`FINANCE_DB` → `STAGING` schema. Show the migrated tables (`CUST_ACCOUNTS`,
`DAILY_BALANCE`, `MONTHLY_AMB`) with row counts matching the SAS source:

```sql
SELECT 'CUST_ACCOUNTS' AS tbl, COUNT(*) AS rows FROM FINANCE_DB.STAGING.CUST_ACCOUNTS
UNION ALL
SELECT 'DAILY_BALANCE',       COUNT(*)          FROM FINANCE_DB.STAGING.DAILY_BALANCE
UNION ALL
SELECT 'MONTHLY_AMB',         COUNT(*)          FROM FINANCE_DB.RAW.MONTHLY_AMB;
```

**2. The source-parity beat — the numbers tie out to SAS.** Run the MONTHLY_AMB
grouped by account status and show that inactive accounts are present (not
filtered out), matching the SAS source:

```sql
SELECT c.is_active, COUNT(*) AS n_accounts,
       SUM(m.average_monthly_balance) AS total_amb
FROM FINANCE_DB.STAGING.MONTHLY_AMB m
JOIN FINANCE_DB.RAW.CUST_ACCOUNTS c
  ON m.customer_id = c.customer_id AND m.account_id = c.account_id
GROUP BY c.is_active;
```

Both ACTIVE and INACTIVE rows are present — the `is_active` filter divergence
has been corrected.

**3. The reconciliation report — every control PASS.** Show the report produced
by `make validate SCENARIO=Scenario1`: every control — Row Count, Sum Amount,
Distinct Count, Not Null — reports PASS. A green report is the definition of
"done"; the data merely loading is not.

**4. The Streamlit dashboard — lineage traced.** Open the dashboard
(`make dashboard`) and show the SAS and Snowflake lineage graphs side by side
for MONTHLY_AMB. The lineage confirms the complete data flow from
`RAW.CUST_ACCOUNTS` and `RAW.DAILY_BALANCE` through `JOB03_CALC_AMB` to
`MONTHLY_AMB`, with no extra filters or missing transformations.

---

<a id="concurrent"></a>
## Concurrent Runs

Each scenario directory is isolated, so multiple runs — and the parallel
fan-out in Act 3 — coexist with no collisions:

```bash
make validate SCENARIO=Scenario1      # baseline
make validate SCENARIO=Scenario2      # delta
make validate SCENARIO=Scenario_alice # custom per-user scenario
```

For live Snowflake validation, namespace by schema:

```sql
CREATE SCHEMA FINANCE_DB.VALIDATION_ALICE;
-- Load and validate against the alice schema
```

---

<a id="key-takeaways"></a>
## Key Takeaways

- The value on display is **Devin doing the validation**: loading SAS and Snowflake datasets, running programmatic reconciliation off a reusable playbook, and proving parity against the source — not just a finished migration to review.
- **Confidence comes from programmatic verification.** The reconciliation harness (Row Count, Sum Amount, Distinct Count, Not Null, Uniqueness) gates every migration, and the demo shows a real divergence (the `is_active` filter that silently drops 271 inactive accounts and creates a $773K control-total gap) being caught and fixed. "Looks reasonable" review would have missed it.
- The **SAS source is the source of truth**: migrations reproduce legacy data faithfully (quirks flagged, not silently "fixed"); remediation is a separate, deliberate decision.
- Validations are **independent and parallelizable** — multiple Devin sessions validate multiple tables at once, each producing its own verified PR. The playbook keeps every run consistent.
- **Lineage tracing** pinpoints the exact transformation job where a divergence originated, turning a failed Row Count from a mystery into a root-cause diagnosis in seconds. The Streamlit dashboard provides the human-friendly exploration layer on top.

---

<a id="how-devin"></a>
## How Devin Produced This

This validation framework was built by Devin working from the SAS source estate
and the existing Snowflake migration target: it analyzed the SAS programs and
their lineage, built the CLI reconciliation harness (`verify/reconcile.py`),
authored the Makefile for repeatable validation, and ran the full suite to
discover and document the `is_active` filter divergence. The same Context Loop
(source analysis → validation → catch divergence → root-cause → fix → verify)
described in
[`modules/data-engineering/sas-migration-analysis.md`](../../modules/data-engineering/sas-migration-analysis.md)
applies here, with the validation procedure codified as the
`!validate-sas-to-snowflake` Devin Playbook (source in the code repo at
`.workshop/playbooks/sas-to-snowflake-migration.devin.md`).
