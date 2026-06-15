# SAS → Snowflake — Full Migration Demo

Devin **migrates** a legacy SAS estate to Snowflake end to end: reads SAS
programs, converts them to Snowflake SQL and Snowpark Python, replaces Control-M
batch orchestration with Snowflake Tasks, validates parity against the SAS
source with a programmatic reconciliation harness (catching a real divergence),
and confirms the results live in Snowsight.

The prompts below invoke the `!convert-sas-to-snowflake` Devin Playbook — the
reusable conversion-and-validation procedure — whose source lives in the code
repo at
[`uc-data-migration-sas-to-snowflake/.workshop/playbooks/sas-to-snowflake-migration.devin.md`](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-snowflake/blob/main/.workshop/playbooks/sas-to-snowflake-migration.devin.md).
The repo-specific mechanics come from the repo's Skill
(`.agents/skills/sas-to-snowflake-migration/SKILL.md`).

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Converts and Validates](#part-1)
  - [Act 1 — Orient over the SAS estate](#act-1)
  - [Act 2 — Convert SAS to Snowflake SQL](#act-2)
  - [Act 3 — Convert procedural SAS to Snowpark Python](#act-3)
  - [Act 4 — Replace Control-M with Snowflake Tasks](#act-4)
  - [Act 5 — Validate with the reconciliation harness](#act-5)
- [Part 2 — Run the Produced Artifacts](#part-2)
- [Confirming Completion in Snowsight](#confirm-snowsight)
- [Concurrent Runs](#concurrent)
- [Key Takeaways](#key-takeaways)

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
- [uc-data-migration-sas-to-snowflake](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-snowflake) — the Snowflake migration target: converted SQL/Snowpark code, validation harness, sample datasets, lineage metadata, the playbook source (`.workshop/playbooks/`), and the repo Skill (`.agents/skills/`).

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Data |
|---|---|---|
| **Before** | `main`: the validation harness (`verify/reconcile.py`), lineage metadata, Streamlit dashboard, playbook source, and Skill. The SAS estate lives in `ts-sas-legacy-analytics`. | SAS `.sas7bdat` source files (durable, immutable) + per-scenario Snowflake CSV exports |
| **After** | a PR branch with converted Snowflake SQL (`snowflake_sql/`), Snowpark Python (`snowpark/`), Snowflake Task DDL (`snowflake_sql/orchestration/`), and a green reconciliation report | Snowflake tables matching SAS source row counts and control totals |

The verification loop sits between them: every converted program's output is
checked by the reconciliation harness (Row Count, Sum Amount, Distinct Count,
Not Null, Uniqueness) before it is trusted. The before state is immutable; the
after state is scenario-scoped and disposable — safe to repeat, safe to run
concurrently.

> **On "parity":** there is no live SAS runtime in this environment, so parity
> means source → target reconciliation against the SAS `.sas7bdat` files as the
> source of truth (row counts, control totals, cardinality, null checks) — a
> deterministic contract, not a byte-for-byte output diff.

---

<a id="part-1"></a>
## Part 1 — Devin Converts and Validates

<a id="act-1"></a>
### Act 1 — Orient over the SAS estate

Open the SAS estate and ask Devin to map it. With DeepWiki over the repo,
Devin typically maps an unfamiliar estate in minutes (coverage depends on repo
structure).

```
Using the ts-sas-legacy-analytics repo, give me a map of the SAS
estate: the banking and insurance programs, what each one reads and
writes, the LIBNAMEs, the macros and PROC FORMATs they depend on,
and the data flow from raw sources through staging to
curated/reporting layers. Also map the Control-M batch orchestration
in BatchJobs/ — which jobs depend on which, and what schedule each
runs on.
```

Expected: a tour of `Programs/Banking/*` (load_customer_accounts,
daily_transaction_processing, credit_risk_scoring,
monthly_regulatory_reporting), `Programs/Insurance/*` (claims_processing,
policy_valuation), the `Macro/` and `Formats/` dependencies, and the Control-M
`BatchJobs/` wrappers — with a lineage map from `RAW` → `STAGING` → `CURATED` →
`REPORTS` and the scheduling DAG (BANK_DAILY_01 → BANK_DAILY_02 →
BANK_WEEKLY_01 → BANK_MONTHLY_01).

<a id="act-2"></a>
### Act 2 — Convert SAS to Snowflake SQL

The first conversion beat. Devin reads a set-based SAS program (PROC SQL with
CASE expressions, aggregations, joins) and converts it to native Snowflake SQL.

```
!convert-sas-to-snowflake

Convert monthly_regulatory_reporting.sas to Snowflake SQL.

- SAS program: Programs/Banking/monthly_regulatory_reporting.sas
  (in ts-sas-legacy-analytics)
- Target: Snowflake SQL under snowflake_sql/ in
  uc-data-migration-sas-to-snowflake
- Scenario: Scenario1

The SAS program computes Basel III risk-weighted assets, delinquency
aging, loan loss provisions, and capital adequacy ratios. It uses
PROC SQL with CASE-based risk-weight mapping. Convert it faithfully
to Snowflake SQL — every risk weight, every threshold, every filter
must match the SAS source exactly. Also write a Snowflake Task
definition to replace the Control-M BANK_MONTHLY_01 schedule.
```

What Devin produces:

- **`snowflake_sql/JOB04_MONTHLY_REGULATORY.sql`** — four Snowflake SQL
  statements creating `REPORTS.MONTHLY_RWA`, `REPORTS.DELINQUENCY_AGING`,
  `REPORTS.LLP_COVERAGE`, `REPORTS.CAPITAL_ADEQUACY`. The risk-weight CASE
  faithfully maps the `ACCOUNT_TYPE` × LTV combinations from the SAS source:

  ```sql
  -- LOC risk weight = 1.00, matching SAS source exactly.
  -- A prior conversion attempt used 0.75 here; the parity
  -- check flagged the RWA divergence.
  WHEN a.ACCOUNT_TYPE = 'LOC' THEN 1.00
  ```

- **`snowflake_sql/orchestration/tasks.sql`** — a `TASK_JOB04_MONTHLY_REGULATORY`
  with `AFTER TASK_JOB03_CALC_AMB` dependency and a conditional `WHEN` clause
  that replaces the Control-M monthly schedule.

The point: Devin read a 199-line SAS program with Basel III logic, converted
the CASE branches and aggregations to Snowflake SQL, and wired the
result into the Task DAG — with a parity check gating correctness.

<a id="act-3"></a>
### Act 3 — Convert procedural SAS to Snowpark Python

Not all SAS programs are set-based SQL. The claims processing program uses DATA
step procedural logic — hash-table policy lookups, multi-output datasets
(CLAIMS_VALID / CLAIMS_INVALID, AUTO_ADJUDICATED / MANUAL_REVIEW), and
conditional routing. Devin converts this to Snowpark Python.

```
!convert-sas-to-snowflake

Convert claims_processing.sas to Snowpark Python.

- SAS program: Programs/Insurance/claims_processing.sas
  (in ts-sas-legacy-analytics)
- Target: Snowpark Python under snowpark/ in
  uc-data-migration-sas-to-snowflake
- Target construct: snowpark
- Scenario: Scenario1

The SAS program uses:
  - Hash-table lookup (h_pol) against POLICIES to validate claims
  - Multi-output DATA step: CLAIMS_VALID vs CLAIMS_INVALID
  - Fraud screening with risk-score thresholds
  - Auto-adjudication rules with multiple OUTPUT datasets
    (AUTO_ADJUDICATED, MANUAL_REVIEW)

Convert to a Snowpark stored procedure that preserves the procedural
semantics: DataFrame joins replace hash lookups, filtered DataFrames
replace multi-output OUTPUT statements. Reproduce every threshold
and routing rule from the SAS source.
```

What Devin produces:

- **`snowpark/claims_processing.py`** — a Snowpark stored procedure with:
  - **Step 1 (Validate):** DataFrame join replaces `declare hash h_pol` — claims
    matched against active policies, split into valid/invalid
  - **Step 2 (Fraud screening):** join to `FRAUD_INDICATORS`, CASE-style
    `FRAUD_RISK` classification (`HIGH` ≥ 80, `MEDIUM` ≥ 50, `LOW`)
  - **Step 3 (Auto-adjudicate):** reproduces the SAS `output` routing:
    - `DENY` for high fraud risk
    - `APPR` for low-risk small claims (≤ $5K) and standard claims
      (≤ 25% of sum insured, ≤ $50K)
    - `PEND` for everything else → manual review queue
  - **Step 4 (Persist):** `write.mode("append")` to three target tables

The point: Devin chose the right Snowflake construct for a procedural program —
Snowpark Python instead of SQL — and preserved the multi-output, conditional-
routing semantics that would be awkward in pure SQL.

<a id="act-4"></a>
### Act 4 — Replace Control-M with Snowflake Tasks

The SAS batch orchestration runs via Control-M: `BANK_MASTER` triggers a chain
of jobs in dependency order. Devin replaces this with native Snowflake Tasks.

```
Look at the BatchJobs/run_daily_banking.sas orchestrator in
ts-sas-legacy-analytics. It runs four SAS programs in sequence via
Control-M. Convert this to a Snowflake Task DAG that preserves the
same dependency chain. Write the DDL in
snowflake_sql/orchestration/tasks.sql in the
uc-data-migration-sas-to-snowflake repo.

Map the Control-M jobs:
  BANK_DAILY_01  → load_customer_accounts    (Daily 06:00)
  BANK_DAILY_02  → daily_transactions        (Daily 07:30, after 01)
  BANK_WEEKLY_01 → credit_risk_scoring       (Weekly Sun, after 02)
  BANK_MONTHLY_01→ monthly_regulatory        (Monthly 3rd BD, after 03)
  INS_DAILY_01   → claims_processing         (Daily 08:00, independent)
```

What Devin produces:

- **`snowflake_sql/orchestration/tasks.sql`** — a Task DAG:

  ```
  TASK_DAILY_BANKING_ROOT (CRON 0 6 * * *)
      └→ TASK_JOB01_LOAD_CUST_ACCOUNTS (AFTER ROOT)
          └→ TASK_JOB02_DAILY_TRANSACTIONS (AFTER JOB01)
              └→ TASK_JOB03_CALC_AMB (AFTER JOB02)
                  └→ TASK_JOB04_MONTHLY_REGULATORY (AFTER JOB03, conditional)
  TASK_INS_CLAIMS_PROCESSING (CRON 0 8 * * *, Snowpark SP)
  TASK_WEEKLY_CREDIT_RISK (CRON 0 2 * * SUN)
  ```

  Each task uses `EXECUTE IMMEDIATE FROM @SQL_STAGE/...` (SQL jobs) or
  `CALL SP_CLAIMS_PROCESSING(...)` (Snowpark job), and the DAG chains via
  `AFTER` dependencies — the same execution order as Control-M, native in
  Snowflake.

<a id="act-5"></a>
### Act 5 — Validate with the reconciliation harness

The confidence beat. Every conversion is gated by the same parity check.

```
!convert-sas-to-snowflake

Validate the MONTHLY_AMB table migration. The SAS source computes
Monthly Average Balance for all accounts (active and inactive). Run
the reconciliation harness against Scenario1 and confirm the
Snowflake output ties out.

- SAS table: MONTHLY_AMB (source: sample_data/MONTHLY_AMB.sas7bdat)
- Snowflake table: MONTHLY_AMB (target: sample_data/Scenario1/)
- Repo: Cognition-Partner-Workshops/uc-data-migration-sas-to-snowflake
```

**The verification beat (the real bug).** The Snowflake migration's
`JOB03_CALC_AMB.sql` added a `WHERE c.is_active = 'ACTIVE'` clause that the SAS
source does not have. The reconciliation harness catches it:

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
extra `WHERE c.is_active = 'ACTIVE'` filter.

The fix: remove the extra filter, matching the SAS logic faithfully. Re-run:

```
make validate TABLE=MONTHLY_AMB SCENARIO=Scenario1
  Row Count:      SAS=1980   SF=1980    -> PASS
  Distinct Count: SAS=1000   SF=1000    -> PASS
  Sum Amount:     SAS=5702329  SF=5702329  -> PASS
```

The point: "looks reasonable" review would have shipped the filtered data;
the parity check against the SAS source did not.

#### Fan out in parallel

Table validations are independent. Run one **orchestrator** session that spawns
a child per table:

```
Act as the orchestrator for a SAS-to-Snowflake migration validation
across multiple tables, using child Devin sessions to parallelize.

Repo: Cognition-Partner-Workshops/uc-data-migration-sas-to-snowflake

Spawn one child session per table below. Each child follows the
!convert-sas-to-snowflake playbook: treat the SAS .sas7bdat as the
source of truth; flag (do not silently fix) anything that diverges;
run the full validation suite; include the reconciliation report in
the PR.

Tables:
1. MONTHLY_AMB (Scenario1) — Row Count, Distinct Count, Sum Amount
2. CUST_ACCOUNTS (Scenario1) — Row Count, Not Null, Uniqueness
3. DAILY_BALANCE (Scenario1) — Row Count, Sum Amount
4. DAILY_BALANCE (Scenario2) — Row Count, Sum Amount, date-format
```

Each child inherits the org's Snowflake secrets and writes to its own scenario
directory — parallel validations never collide.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifacts

Show the validation harness running end to end:

```bash
# Full validation suite against Scenario1 (baseline):
make validate SCENARIO=Scenario1

# Full validation against Scenario2 (delta — has additional divergences):
make validate SCENARIO=Scenario2
```

Scenario1 (after fix) ends with all checks PASS. Scenario2 shows the delta
migration's additional issues — `DAILY_BALANCE` has 122,760 SAS rows vs only
16,802 in Snowflake (massive row loss) plus date-format mismatches.

Launch the Streamlit dashboard for interactive exploration:

```bash
make dashboard
```

---

<a id="confirm-snowsight"></a>
## Confirming Completion in Snowsight

The migration is complete when five things are visible: the **converted code**
exists (SQL + Snowpark), the **Task DAG** is defined, the migrated tables are
**loaded**, the **reconciliation is green**, and you can **prove it live** in
Snowsight.

**1. Snowsight — the tables exist with correct row counts.**

```sql
SELECT 'CUST_ACCOUNTS' AS tbl, COUNT(*) AS rows
    FROM FINANCE_DB.STAGING.CUST_ACCOUNTS
UNION ALL
SELECT 'DAILY_BALANCE', COUNT(*)
    FROM FINANCE_DB.STAGING.DAILY_BALANCE
UNION ALL
SELECT 'MONTHLY_AMB', COUNT(*)
    FROM FINANCE_DB.STAGING.MONTHLY_AMB;
-- Expected: 1980, 122760, 1980 (matching SAS source)
```

**2. The parity beat — control totals tie out to SAS.**

```sql
SELECT SUM(average_monthly_balance) FROM FINANCE_DB.STAGING.MONTHLY_AMB;
-- Expected: 5,702,329 (matches SAS)

-- The is_active divergence, visible as data:
SELECT is_active, COUNT(*) AS n_accounts
FROM FINANCE_DB.RAW.CUST_ACCOUNTS
GROUP BY is_active;
-- ACTIVE: 1,709  INACTIVE: 271
```

**3. Risk-weighted assets — the Basel III conversion is correct.**

```sql
SELECT ACCOUNT_TYPE, RISK_WEIGHT, N_ACCOUNTS, TOTAL_EXPOSURE, RWA
FROM FINANCE_DB.REPORTS.MONTHLY_RWA
ORDER BY ACCOUNT_TYPE;
-- Verify: LOC risk weight = 1.00 (not 0.75)
```

**4. Task DAG — Snowflake Tasks replaced Control-M.**

```sql
SELECT NAME, STATE, SCHEDULE, PREDECESSORS
FROM TABLE(INFORMATION_SCHEMA.TASK_DEPENDENTS(
    TASK_NAME => 'FINANCE_DB.PUBLIC.TASK_DAILY_BANKING_ROOT',
    RECURSIVE => TRUE
));
```

**5. Claims processing — Snowpark procedure works.**

```sql
CALL FINANCE_DB.PUBLIC.SP_CLAIMS_PROCESSING(CURRENT_DATE()::VARCHAR);
-- Returns: summary with new/auto/review/fraud counts
```

---

<a id="concurrent"></a>
## Concurrent Runs

Each session's work is isolated by scenario directory (`Scenario1`, `Scenario2`,
or `Scenario_<session_id>`) and by branch. Parallel runs never collide because:

- The SAS `.sas7bdat` source files are immutable — read-only baseline.
- Each child writes to its own scenario directory under `sample_data/`.
- Each session opens its own PR branch.
- Snowflake schemas can be namespaced per session (`STAGING_<session_id>`).

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **Devin reads SAS, writes Snowflake.** Set-based programs become Snowflake
   SQL; procedural programs become Snowpark Python. The choice of target
   construct is deliberate, not one-size-fits-all.

2. **Orchestration migrates too.** Control-M batch scheduling → Snowflake Tasks
   with `AFTER` dependencies and CRON expressions. The execution order is
   preserved; the scheduling is native.

3. **Verification is programmatic, not visual.** Every conversion is gated by
   the reconciliation harness — Row Count, Sum Amount, Distinct Count, Not Null,
   Uniqueness — against the SAS source as the immutable baseline. A real bug
   (the `is_active` filter) was caught this way, not by code review.

4. **Parallel fan-out.** One orchestrator session spawns a child per table (or
   per program). Each child follows the same playbook, writes to its own
   namespace, and opens its own verified PR. The same review bar applied many
   times in parallel.

5. **Snowsight confirms it live.** Row counts, control totals, risk-weighted
   assets, Task DAG status, Snowpark procedure output — all visible and
   queryable in Snowflake's native UI.
