# Oracle → PostgreSQL / EnterpriseDB — End-to-End Migration Demo

A single linear demo that shows Devin migrating an Oracle 19c Forms-era HRMS
database to PostgreSQL (PL/pgSQL) with **verifiable confidence**: orient over
the legacy estate, convert one view live, prove parity through programmatic
reconciliation, catch a real divergence (outer-join row loss) and fix it, then
fan the work out across many objects in parallel. The second half runs the
produced artifact end to end (before/after, CI gating, namespace isolation) and
reverts — safe to repeat.

The commands and prompts here are kept **identical** to the runbook in the code
repo: [`uc-db-migration-oracle-to-postgres/docs/DEMO_RUNBOOK.md`](https://github.com/Cognition-Partner-Workshops/uc-db-migration-oracle-to-postgres/blob/main/docs/DEMO_RUNBOOK.md).
If you change one, change the other.

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Does the Migration](#part-1)
  - [Act 1 — Orient over the Oracle estate](#act-1)
  - [Act 2 — Convert one object live, with verification](#act-2)
  - [Act 3 — Fan out in parallel](#act-3)
  - [Act 4 — Confidence = programmatic verification](#act-4)
- [Part 2 — Run the Produced Artifact](#part-2)
- [Concurrent Runs](#concurrent)
- [ora2pg / EDB Toolkit Comparison](#ora2pg)
- [Key Takeaways](#key-takeaways)
- [How Devin Produced This](#how-devin)

---

<a id="quick-start"></a>
## Quick Start

Set PostgreSQL connection credentials, then run the full lifecycle from the
target repo root:

```bash
export PGHOST=localhost
export PGPORT=5432
export PGDATABASE=hrms
export PGUSER=postgres
export PGPASSWORD='...'

pip install -r requirements.txt -r seed/requirements.txt -r verify/requirements.txt

make seed                 # before: synthetic HRMS data into the raw schema
make demo-up   NS=dev     # after: deploy all converted objects into dev
make reconcile NS=dev     # source → target reconciliation report
make demo-down NS=dev     # drop the dev namespace (raw data untouched)
```

Prerequisites: a PostgreSQL 15+ instance (Docker `postgres:16`) or an
EnterpriseDB instance, and Python 3.10+. On EDB, its Oracle-compatibility mode
narrows the diff further — several constructs below convert with fewer changes.

---

<a id="repositories"></a>
## Repositories

- [ts-plsql-oracle-forms-hrms](https://github.com/Cognition-Partner-Workshops/ts-plsql-oracle-forms-hrms) — the legacy Oracle 19c HRMS estate (PL/SQL packages, schema DDL, sequences, views, triggers). Read-only reference for the "before."
- [uc-db-migration-oracle-to-postgres](https://github.com/Cognition-Partner-Workshops/uc-db-migration-oracle-to-postgres) — the PostgreSQL target: converted PL/pgSQL, reconciliation harness, synthetic data seeder, conversion playbook, CI/CD, and the demo runbook.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Data |
|---|---|---|
| **Before** | `main`: Phase 1 objects already converted (tables, sequences, scalar functions, simple CRUD), plus the tooling, reconciliation harness, seeder, and the conversion playbook. The Oracle source estate lives in `ts-plsql-oracle-forms-hrms`. | `raw.*` tables (durable; never overwritten) |
| **After** | a PR branch with complex views, procedures, and triggers converted live (namespace-isolated PostgreSQL objects + their reconciliation controls) | `$(NS).*` objects (per-run, disposable) |

The **before** state is deliberately a *partial* migration: the tables,
sequences, scalar functions, and simple CRUD functions are already on `main`
with a working reconciliation harness, so the tooling is in place. What Devin
converts **live** is the next wave — the complex views, procedures, and triggers
that contain Oracle-specific constructs.

The verification loop sits between them: every converted object is deployed into
a namespace and checked by reconciliation controls before it is trusted. The
before state is durable; the after state is namespaced and disposable — which
makes this safe to repeat and safe to run concurrently.

> **On "parity":** there is no live Oracle runtime in this environment, so
> parity means source → target reconciliation against the synthetic data as the
> source of truth (row counts, control totals, business-rule invariants) — a
> deterministic contract, not a byte-for-byte Oracle-vs-PostgreSQL output diff.

---

<a id="part-1"></a>
## Part 1 — Devin Does the Migration

<a id="act-1"></a>
### Act 1 — Orient over the Oracle estate

Open the Oracle source estate and ask Devin to explain it.

```
Using the ts-plsql-oracle-forms-hrms repo, give me a map of the Oracle estate:
the tables (schema/tables/), sequences (schema/sequences/), views
(schema/views/hrms_views.sql), PL/SQL packages (plsql/packages/), and triggers
(plsql/triggers/). For each object, identify every Oracle-specific construct
that needs conversion to PostgreSQL. Reference the migration map at
uc-db-migration-oracle-to-postgres/docs/ORACLE_TO_POSTGRES_MIGRATION_MAP.md
and rank each object by conversion complexity (simple, medium, complex).
```

Expected: Devin maps 30 tables, 29 sequences, 6 views, 11 PL/SQL packages, and
2 triggers. The complex objects (`VW_ACTIVE_EMPLOYEES`, `VW_ORG_HIERARCHY`,
`PKG_PAYROLL`, `PKG_LEAVE`, `trg_audit`) are flagged with multiple
Oracle-specific constructs: `LEFT JOIN` to current salary, `CONNECT BY` /
`START WITH` / `SYS_CONNECT_BY_PATH`, `PRAGMA AUTONOMOUS_TRANSACTION`,
`SEQ.NEXTVAL`, `SYSDATE`, `SYS_CONTEXT`, `SQL%ROWCOUNT`, `DBMS_OUTPUT`, and
`VARCHAR2`/`CLOB`/`NUMBER` data types.

<a id="act-2"></a>
### Act 2 — Convert one object live, with verification

The core beat. Paste the playbook prompt for the active-employees view. Devin
reads the Oracle source, writes the PostgreSQL conversion, deploys it, runs the
reconciliation controls, catches a divergence, fixes it, and produces a PR with
the reconciliation report.

```
Convert the Oracle view VW_ACTIVE_EMPLOYEES from
ts-plsql-oracle-forms-hrms/schema/views/hrms_views.sql to PostgreSQL,
following the conversion playbook at
uc-db-migration-oracle-to-postgres/docs/CONVERSION_PLAYBOOK.md.

Place the converted file at:
  migrations/schema/views/500_vw_active_employees.sql

Use the "$(NS)" schema prefix for namespace isolation. After conversion, deploy
and run reconciliation:
  make demo-down NS=dev && make demo-up NS=dev

If any reconciliation control fails, identify the root cause and fix it.
Include the reconciliation report in the PR.
```

**The verification beat (the real bug).** The Oracle source uses a `LEFT JOIN`
to `SALARY_RECORDS` so that active employees with no *current* salary row are
still returned. A plausible conversion writes an `INNER JOIN` — which silently
drops every such employee. The completeness control catches it:

```bash
make reconcile NS=dev
#   active_employee_completeness | FAIL | expected=277, actual=224
#   53 active employees missing from the view.
#   Likely cause: LEFT JOIN salary_records converted to INNER JOIN
```

Fix the join back to `LEFT JOIN`, redeploy, and the report goes green:

```bash
make reconcile NS=dev
#   active_employee_completeness | PASS | expected=277, actual=277
#   org_hierarchy_reachability   | PASS
#   payroll_control_totals       | PASS
#   leave_balance_nonnegative    | PASS
```

The point: "looks reasonable" review (or an automated tool's bulk conversion)
would have shipped the `INNER JOIN`; the completeness control against the source
did not. The full write-up is in the code repo at `docs/CONVERSION_PLAYBOOK.md`
→ *Worked Example: VW_ACTIVE_EMPLOYEES*.

<a id="act-3"></a>
### Act 3 — Fan out in parallel

Conversions are independent, so launch a Devin session per object. Each follows
the same playbook and produces its own verified PR — the same review bar applied
many times in parallel instead of once in series.

| Session | Oracle Object | Key Constructs | Target File |
|---|---|---|---|
| 1 | `VW_ACTIVE_EMPLOYEES` | `LEFT JOIN` outer join (the Act 2 worked example) | `500_vw_active_employees.sql` |
| 2 | `VW_ORG_HIERARCHY` | `CONNECT BY`, `START WITH`, `SYS_CONNECT_BY_PATH`, `LEVEL` | `510_vw_org_hierarchy.sql` |
| 3 | `PKG_PAYROLL` run processing | package → functions, cursor loops, `SQL%ROWCOUNT`, `SUM`/`CASE` sign handling | `600_payroll_run.sql` |
| 4 | `PKG_LEAVE` accrual | `%ROWTYPE`, `DBMS_OUTPUT` → `RAISE NOTICE`, NULL arithmetic | `610_leave_accrual.sql` |
| 5 | `trg_audit` | `PRAGMA AUTONOMOUS_TRANSACTION`, `SEQ.NEXTVAL`, `SYS_CONTEXT` | `700_trg_audit.sql` |

Each session uses its own namespace (`NS=session1`, …) so the live deployments
never collide.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

The gates that make every PR trustworthy:

- **CI** (`.github/workflows/sql_ci.yml`): sqlfluff lint → deploy (into an
  ephemeral namespace) → reconciliation controls → report artifact.
- **Reconciliation controls** (`verify/reconcile.py`): active-employee
  completeness, org-hierarchy reachability, payroll control totals, and
  leave-balance non-negativity — documented as a contract in
  `docs/CONVERSION_PLAYBOOK.md`.
- **Deterministic synthetic data** (`seed/generate_and_load.py`): fixed RNG
  seed (42), 300 employees (277 active), ~20% of active employees have no
  current salary record (the outer-join trap population).

A conversion is "done" when the source-parity controls are green, in CI, on the
PR — not when the code merely parses.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifact

Show the converted estate running end to end, with a repeatable before/after.

```bash
make seed                 # load synthetic data into raw (idempotent)
make demo-up   NS=dev     # deploy all converted objects into dev
make reconcile NS=dev     # all controls PASS
```

Query the before and after side by side:

```sql
-- Source data (before)
SELECT count(*) FROM raw.employees WHERE employment_status = 'ACTIVE';
SELECT count(*) FROM raw.salary_records WHERE active_flag = 'Y';

-- Converted view (after) — includes active employees WITHOUT a current salary
SELECT count(*) FROM dev.vw_active_employees;

-- Org hierarchy reachability (after) — every active employee reachable
SELECT count(DISTINCT emp_id) FROM dev.vw_org_hierarchy;
```

Clean up when done:

```bash
make demo-down NS=dev     # drop the dev namespace (raw data untouched)
```

---

<a id="concurrent"></a>
## Concurrent Runs

Each output schema is namespaced, so multiple runs — and the parallel fan-out
in Act 3 — coexist with no collisions:

```bash
make demo-up   NS=alice
make demo-up   NS=team1
make reconcile NS=alice
make demo-down NS=alice
```

---

<a id="ora2pg"></a>
## ora2pg / EDB Toolkit Comparison

The code repo includes a side-by-side comparison
([`docs/ORA2PG_COMPARISON.md`](https://github.com/Cognition-Partner-Workshops/uc-db-migration-oracle-to-postgres/blob/main/docs/ORA2PG_COMPARISON.md))
showing what ora2pg and the EDB Migration Toolkit handle well (schema DDL, data
types, sequences, bulk data copy) and where they struggle (outer-join semantics,
`CONNECT BY` hierarchies, `PRAGMA AUTONOMOUS_TRANSACTION`, Oracle NULL /
empty-string semantics, and — critically — no reconciliation or CI integration).

---

<a id="key-takeaways"></a>
## Key Takeaways

- The value on display is **Devin doing the migration**: reading an unfamiliar Oracle estate, converting objects off a reusable playbook, and proving each conversion against the source — not just a finished artifact to run.
- **Confidence comes from programmatic verification.** Reconciliation controls (completeness, control totals, org-hierarchy reachability, leave-balance parity) gate every build and CI run, and the demo shows a real divergence (`LEFT JOIN` → `INNER JOIN`, 53 active employees silently disappear) being caught and fixed. "Looks reasonable" review — or a tool's bulk conversion — would have missed it.
- The **Oracle source is the source of truth**: conversions reproduce legacy logic faithfully (quirks flagged, not silently "fixed"); remediation is a separate, deliberate decision.
- Conversions are **independent and parallelizable** — multiple Devin sessions convert multiple objects at once, each producing its own verified PR. The playbook keeps every run consistent. Namespace isolation (`NS=team1`…`NS=teamN`) maps directly to per-team or per-schema migrations.
- **CI gates every conversion**: lint → deploy → reconcile → report artifact. No migration merges without passing reconciliation.

---

<a id="how-devin"></a>
## How Devin Produced This

This reference solution was built by Devin working from the Oracle source estate
and the partially-built PostgreSQL target: it analyzed the packages and views,
built the conversion playbook, generated deterministic synthetic data, authored
the reconciliation harness, and validated the pipeline by deploying and
reconciling against the source. The same Context Loop (source analysis → target
mapping → produce PR → programmatic verification → human review → refine)
described in the SAS demo applies here, with the conversion procedure codified
in the code repo's `docs/CONVERSION_PLAYBOOK.md`.
