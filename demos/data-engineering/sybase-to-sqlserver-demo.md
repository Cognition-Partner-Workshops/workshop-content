# Sybase ASE → SQL Server — End-to-End Migration Demo

A single linear demo that shows Devin migrating a Sybase ASE loan-servicing
database to SQL Server T-SQL with **verifiable confidence**: orient over the
legacy estate, convert one stored procedure live, prove parity through
programmatic reconciliation, catch a real divergence (outer join row loss) and
fix it, then fan the work out across many procedures in parallel. The second
half runs the produced artifact end to end (before/after, CI gating, namespace
isolation) and reverts — safe to repeat.

The commands and prompts here are kept **identical** to the runbook in the code
repo: [`uc-db-migration-sybase-to-sqlserver/docs/DEMO_RUNBOOK.md`](https://github.com/Cognition-Partner-Workshops/uc-db-migration-sybase-to-sqlserver/blob/main/docs/DEMO_RUNBOOK.md).
If you change one, change the other.

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Does the Migration](#part-1)
  - [Act 1 — Orient over the Sybase estate](#act-1)
  - [Act 2 — Convert one procedure live, with verification](#act-2)
  - [Act 3 — Fan out in parallel](#act-3)
  - [Act 4 — Confidence = programmatic verification](#act-4)
- [Part 2 — Run the Produced Artifact](#part-2)
- [Concurrent Runs](#concurrent)
- [SSMA Comparison](#ssma)
- [Key Takeaways](#key-takeaways)
- [How Devin Produced This](#how-devin)

---

<a id="quick-start"></a>
## Quick Start

Set SQL Server connection credentials, then run the full lifecycle from the
target repo root:

```bash
export SQLSERVER_HOST=localhost
export SQLSERVER_PORT=1433
export SQLSERVER_DB=loan_servicing
export SQLSERVER_USER=sa
export SQLSERVER_PASSWORD='...'

pip install -r requirements.txt -r seed/requirements.txt -r verify/requirements.txt

make seed                 # before: synthetic loan data into [raw] schema
make demo-up   NS=dev     # after: deploy all converted objects into [dev]
make reconcile NS=dev     # source → target reconciliation report
make demo-down NS=dev     # drop [dev] namespace (raw data untouched)
```

Prerequisites: a SQL Server instance (Docker `mcr.microsoft.com/mssql/server:2022-latest`
or Azure SQL), Python 3.10+, and the ODBC Driver 18 for SQL Server.

---

<a id="repositories"></a>
## Repositories

- [ts-tsql-sybase-legacy-db](https://github.com/Cognition-Partner-Workshops/ts-tsql-sybase-legacy-db) — the legacy Sybase ASE estate (loan servicing: stored procedures, views, triggers, functions, batch scripts, schema DDL). Read-only reference for the "before."
- [uc-db-migration-sybase-to-sqlserver](https://github.com/Cognition-Partner-Workshops/uc-db-migration-sybase-to-sqlserver) — the SQL Server target: converted T-SQL, reconciliation harness, synthetic data seeder, conversion playbook, CI/CD, and the demo runbook.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Data |
|---|---|---|
| **Before** | `main`: Phase 1 objects already converted (tables, functions, simple CRUD), plus the tooling, reconciliation harness, seeder, and the conversion playbook. The Sybase source estate lives in `ts-tsql-sybase-legacy-db`. | `[raw].*` tables (durable; never overwritten) |
| **After** | a PR branch with complex procedures, views, and triggers converted live (namespace-isolated SQL Server objects + their reconciliation controls) | `[$(NS)].*` objects (per-run, disposable) |

The **before** state is deliberately a *partial* migration: the tables,
scalar functions, and simple CRUD procedures are already on `main` with a
working reconciliation harness, so the tooling is in place. What Devin converts
**live** is the next wave — the complex procedures, views, and triggers that
contain Sybase-specific constructs.

The verification loop sits between them: every converted object is deployed into
a namespace and checked by reconciliation controls before it is trusted. The
before state is durable; the after state is namespaced and disposable — which
makes this safe to repeat and safe to run concurrently.

> **On "parity":** there is no live Sybase runtime in this environment, so
> parity means source → target reconciliation against the synthetic data as the
> source of truth (row counts, control totals, business-rule invariants,
> delinquency bucket parity) — a deterministic contract, not a byte-for-byte
> Sybase-vs-SQL-Server output diff.

---

<a id="part-1"></a>
## Part 1 — Devin Does the Migration

<a id="act-1"></a>
### Act 1 — Orient over the Sybase estate

Open the Sybase source estate and ask Devin to explain it.

```
Using the ts-tsql-sybase-legacy-db repo, give me a map of the Sybase estate:
the tables, stored procedures (Servicing, Reporting, Batch, CRUD), views,
triggers, and functions. For each object, identify every Sybase-specific
construct that needs conversion to SQL Server. Reference the migration map at
uc-db-migration-sybase-to-sqlserver/docs/SYBASE_TO_SQLSERVER_MIGRATION_MAP.md
and rank each object by conversion complexity (simple, medium, complex).
Explain clearly and simply to someone completely new to this codebase.
```

Expected: Devin maps 7 tables, 12 stored procedures, 3 views, 2 triggers, and
3 functions. The complex objects (`sp_process_monthly_payments`,
`sp_delinquency_aging_report`, `vw_active_loan_portfolio`) are flagged with
multiple Sybase-specific constructs: `*=` outer joins, `@@sqlstatus`,
`DEALLOCATE CURSOR`, `RAISERROR` without parentheses, `SET ROWCOUNT`,
`COMPUTE BY`, and `@@error + GOTO` error handling.

<a id="act-2"></a>
### Act 2 — Convert one procedure live, with verification

The core beat. Paste the playbook prompt for the delinquency aging report.
Devin reads the Sybase source, writes the SQL Server conversion, deploys it,
runs the reconciliation controls, catches a divergence, fixes it, and produces
a PR with the reconciliation report.

```
Convert the Sybase stored procedure
ts-tsql-sybase-legacy-db/stored_procs/Reporting/sp_delinquency_aging_report.sql
to SQL Server, following the conversion playbook at
uc-db-migration-sybase-to-sqlserver/docs/CONVERSION_PLAYBOOK.md.

Place the converted file at:
  migrations/stored_procs/Reporting/111_sp_delinquency_aging_report.sql

Use [$(NS)] schema prefix for namespace isolation. After conversion, deploy
and run reconciliation:
  make demo-down NS=dev && make demo-up NS=dev

If any reconciliation control fails, identify the root cause and fix it.
Include the reconciliation report in the PR.
```

**The verification beat (the real bug).** The Sybase source uses `*=` (outer
join) to join `dbo.loans` to `dbo.loan_modifications`. A plausible conversion
removes the `*=` and writes a comma join or `INNER JOIN` — which silently drops
every loan that has no modification history. The completeness control catches it:

```bash
make reconcile NS=dev
#   loan_portfolio_completeness | FAIL | expected=704, actual=440
#   Missing 264 loans in target.
#   Likely cause: *= outer join to loan_modifications converted to INNER JOIN
```

Fix the join to `LEFT JOIN`, redeploy, and the report goes green:

```bash
make reconcile NS=dev
#   loan_portfolio_completeness | PASS | expected=704, actual=704
#   delinquency_bucket_parity   | PASS
#   payment_waterfall_totals    | PASS
#   va_late_fee_exclusion       | PASS
```

The point: "looks reasonable" review (or SSMA's bulk conversion) would have
shipped the INNER JOIN; the completeness control against the source did not. The
full write-up is in the code repo at `docs/CONVERSION_PLAYBOOK.md` →
*Worked Example: sp_delinquency_aging_report*.

<a id="act-3"></a>
### Act 3 — Fan out in parallel

Conversions are independent, so launch a Devin session per procedure. Each
follows the same playbook and produces its own verified PR — the same review
bar applied many times in parallel instead of once in series.

| Session | Sybase Procedure | Key Constructs | Target File |
|---|---|---|---|
| 1 | `sp_delinquency_aging_report` | `*=` outer join (the Act 2 worked example) | `111_sp_delinquency_aging_report.sql` |
| 2 | `sp_process_monthly_payments` | `@@sqlstatus`, `DEALLOCATE CURSOR`, `@@error + GOTO`, `@@identity` | `100_sp_process_monthly_payments.sql` |
| 3 | `sp_apply_late_fees` | `SET ROWCOUNT`, `@@identity`, VA late-fee business rule | `102_sp_apply_late_fees.sql` |
| 4 | `sp_nightly_accrual` | `HOLDLOCK`, cursor pattern, `RAISERROR` | `120_sp_nightly_accrual.sql` |
| 5 | `vw_active_loan_portfolio` | `*=` outer join (view), `COMPUTE BY` removal | `010_vw_active_loan_portfolio.sql` |

Each session uses its own namespace (`NS=session1`, …) so the live deployments
never collide.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

The gates that make every PR trustworthy:

- **CI** (`.github/workflows/sql_ci.yml`): sqlfluff lint → deploy (into an
  ephemeral namespace) → reconciliation controls → report artifact.
- **Reconciliation controls** (`verify/reconcile.py`): loan portfolio
  completeness, delinquency bucket parity, payment waterfall control totals,
  VA late-fee business-rule exclusion — documented as a contract in
  `docs/CONVERSION_PLAYBOOK.md`.
- **Deterministic synthetic data** (`seed/generate_and_load.py`): fixed RNG
  seed (42), 880 loans, 500 borrowers, ~30% of loans have no modification
  history (the outer-join trap population).

A conversion is "done" when the source-parity controls are green, in CI, on the
PR — not when the code merely parses.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifact

Show the converted estate running end to end, with a repeatable before/after.

```bash
make seed                 # load synthetic data into [raw] (idempotent)
make demo-up   NS=dev     # deploy all converted objects into [dev]
make reconcile NS=dev     # all controls PASS
```

Query the before and after side by side:

```sql
-- Source data (before)
SELECT COUNT(*) FROM [raw].loans WHERE loan_status IN ('AC', 'DL');
SELECT COUNT(*) FROM [raw].loan_modifications;

-- Converted view (after) — includes loans WITHOUT modifications
SELECT COUNT(*) FROM [dev].vw_active_loan_portfolio;

-- VA loans: zero late fees (business rule)
SELECT loan_type, SUM(late_fee_balance) AS total_fees
FROM [dev].loans
WHERE loan_type = 'VA'
GROUP BY loan_type;
```

Clean up when done:

```bash
make demo-down NS=dev     # drop [dev] namespace (raw data untouched)
```

---

<a id="concurrent"></a>
## Concurrent Runs

Each output schema is namespaced, so multiple runs — and the parallel fan-out
in Act 3 — coexist with no collisions:

```bash
make demo-up   NS=alice
make demo-up   NS=state1
make reconcile NS=alice
make demo-down NS=alice
```

---

<a id="ssma"></a>
## SSMA Comparison

The code repo includes a side-by-side comparison
([`docs/SSMA_COMPARISON.md`](https://github.com/Cognition-Partner-Workshops/uc-db-migration-sybase-to-sqlserver/blob/main/docs/SSMA_COMPARISON.md))
showing what SSMA handles well (schema DDL, simple CRUD, data types) and where
it struggles (`*=` outer joins, `@@sqlstatus`, `SET ROWCOUNT`, `COMPUTE BY`,
error handling patterns, and — critically — no reconciliation or CI
integration).

---

<a id="key-takeaways"></a>
## Key Takeaways

- The value on display is **Devin doing the migration**: reading an unfamiliar Sybase estate, converting procedures off a reusable playbook, and proving each conversion against the source — not just a finished artifact to run.
- **Confidence comes from programmatic verification.** Reconciliation controls (completeness, control totals, delinquency bucket parity, VA late-fee exclusion) gate every build and CI run, and the demo shows a real divergence (`*=` outer join → INNER JOIN, 264 loans silently disappear) being caught and fixed. "Looks reasonable" review — or SSMA's bulk conversion — would have missed it.
- The **Sybase source is the source of truth**: conversions reproduce legacy logic faithfully (quirks flagged, not silently "fixed"); remediation is a separate, deliberate decision.
- Conversions are **independent and parallelizable** — multiple Devin sessions convert multiple procedures at once, each producing its own verified PR. The playbook keeps every run consistent. Namespace isolation (`NS=state1`…`NS=state7`) maps directly to per-client-state migrations.
- **CI gates every conversion**: lint → deploy → reconcile → report artifact. No migration merges without passing reconciliation.

---

<a id="how-devin"></a>
## How Devin Produced This

This reference solution was built by Devin working from the Sybase source estate
and the partially-built SQL Server target: it analyzed the stored procedures,
built the conversion playbook, generated deterministic synthetic data, authored
the reconciliation harness, and validated the pipeline by deploying and
reconciling against the source. The same Context Loop (source analysis → target
mapping → produce PR → programmatic verification → human review → refine)
described in the SAS demo applies here, with the conversion procedure codified
in the code repo's `docs/CONVERSION_PLAYBOOK.md`.
