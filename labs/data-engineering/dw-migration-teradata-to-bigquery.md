# DW Migration: Teradata to BigQuery

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Teradata-Specific Features to Migrate](#teradata-specific-features-to-migrate)
- [The Verification Loop](#the-verification-loop)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [uc-dw-migration-teradata-to-bigquery](#uc-dw-migration-teradata-to-bigquery)
- [Going Further](#going-further)

---

## Challenge

Convert a Teradata retail-banking data warehouse — DDL, views, stored
procedures, macros, and BTEQ scripts — to **BigQuery Standard SQL (GoogleSQL)**,
and **prove** the conversion with a programmatic parity harness rather than
eyeballing the diff.

This exercise uses **non-invasive static analysis**: Devin reads the Teradata SQL
in the repository and translates it to GoogleSQL — no connection to a Teradata
instance and no GCP account is required. The included parity harness runs locally
against DuckDB (a stand-in for BigQuery Standard SQL), so the verification loop
is fully self-contained.

## Quick Start

1. Open Devin and connect to the **uc-dw-migration-teradata-to-bigquery** repository
2. Create your working branch from `main`: `workshop-<attendee_id>`
3. Paste the [Step 1 prompt](#step-1-paste-into-devin) into Devin
4. Watch Devin convert the Teradata DDL/views to GoogleSQL, run
   `python verify/run_parity.py`, and iterate until every parity metric is green

## Repositories

- [uc-dw-migration-teradata-to-bigquery](#uc-dw-migration-teradata-to-bigquery)

---

## Teradata-Specific Features to Migrate

| Feature | BigQuery (GoogleSQL) Equivalent |
|---------|---------------------------------|
| `SET TABLE` / `MULTISET TABLE` | Standard `CREATE TABLE` |
| `PRIMARY INDEX (PI)` | `CLUSTER BY` (optional) |
| `PARTITION BY RANGE_N` | `PARTITION BY DATE_TRUNC(<date>, MONTH)` / date partitioning |
| `COMPRESS` | Automatic columnar compression (remove) |
| `NOT CASESPECIFIC` | Case-sensitive `STRING`; use `LOWER()`/`UPPER()` |
| `FORMAT` on columns | `FORMAT()` / `FORMAT_DATE()` in queries |
| `COLLECT STATISTICS` | Not needed (automatic) |
| `QUALIFY` | Supported natively |
| `ZEROIFNULL` / `NULLIFZERO` | `IFNULL(x, 0)` / `NULLIF(x, 0)` |
| `CSUM()` (cumulative sum) | `SUM() OVER (ORDER BY ... ROWS UNBOUNDED PRECEDING)` |
| `MAVG()` (moving average) | `AVG() OVER (... ROWS BETWEEN (n-1) PRECEDING AND CURRENT ROW)` |
| `HASHROW()` | `FARM_FINGERPRINT()` |
| `SEL` (shorthand) | `SELECT` |
| `REPLACE MACRO` | Convert to a procedure or `CREATE TABLE FUNCTION` |
| `VOLATILE TABLE` | `CREATE TEMP TABLE` (in a script) or a CTE |
| `ACTIVITY_COUNT` | `@@row_count` |
| `ADD_MONTHS(d, n)` | `DATE_ADD(d, INTERVAL n MONTH)` |
| BTEQ `.LOGON`, `.EXPORT`, `.IF` | `bq load` / `EXPORT DATA` + Dataform/Composer |

## The Verification Loop

What makes this module different from a "translate the SQL" exercise: the repo
ships a **programmatic parity harness** (`verify/run_parity.py`) that loads the
seed data, applies the converted views, and compares row counts and measure sums
against golden values. A conversion is "done" only when the harness is green.

The harness is built to catch a real, easy-to-miss divergence. Teradata
`MAVG(x, 3)` is a **3-row** moving average (current month + 2 preceding). The
intuitive-but-wrong translation `ROWS BETWEEN 3 PRECEDING AND CURRENT ROW`
averages 4 rows and the harness fails:

```
MISMATCH 20_branch_performance.sum_moving_avg_volume  expected 6789480.28  got 6764463.37
```

Fixing the frame to `2 PRECEDING` turns it green. "Looks reasonable" review would
have shipped the wrong number.

## Target Outcomes

- GoogleSQL DDL/views for the converted objects under a new `bigquery/` directory
- `python verify/run_parity.py` exits green — all parity metrics match golden
- `MIGRATION_RUNBOOK.md` documenting loading approach and every translation decision
- Validation evidence: row counts, checksums, and the parity report
- A PR with all migration artifacts (the reference conversion on
  `bigquery-reference` is available to compare against)

## What Participants Will Learn

- How Devin comprehends Teradata SQL dialects and maps them to GoogleSQL
- Why a programmatic verification loop beats visual review for migrations
- Data warehouse migration methodology (inventory → convert → verify → cut over)
- How partition/cluster design on BigQuery replaces Teradata PI/PPI

## Devin Features Exercised

- SQL dialect comprehension and large-scale translation
- Programmatic verification loop (run, read failures, fix, re-run)
- Documentation generation (runbook, translation notes)
- PR creation with migration artifacts and Devin Review feedback

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="uc-dw-migration-teradata-to-bigquery"></a>uc-dw-migration-teradata-to-bigquery

**Repository:** [uc-dw-migration-teradata-to-bigquery](https://github.com/Cognition-Partner-Workshops/uc-dw-migration-teradata-to-bigquery)

Teradata-based retail banking analytics data warehouse: 7 DDL tables (5 dimensions
+ 2 fact tables) in `ddl/tables/`, 3 views in `ddl/views/`, 3 stored procedures
and 3 macros in `dml/`, 2 BTEQ scripts in `dml/scripts/`, seed data in
`data/seed/`, and a parity harness in `verify/`. `main` is the before-state (no
converted SQL); a reference conversion lives on the `bigquery-reference` branch.

### Step 1: Paste into Devin

```
Analyze all Teradata DDL in uc-dw-migration-teradata-to-bigquery/ddl/ (tables
and views). Convert every table and view to BigQuery Standard SQL (GoogleSQL),
handling all Teradata-specific features (SET/MULTISET, PI, PPI, COMPRESS,
CASESPECIFIC, FORMAT, QUALIFY, ZEROIFNULL, CSUM, MAVG, HASHROW). Place converted
view files in the bigquery/views/ directory so the parity harness can run them.
Then run `python verify/run_parity.py` and iterate until every parity metric
matches golden. Create a MIGRATION_RUNBOOK.md documenting every translation
decision.
```

### Step 2: Research with Ask Devin

- *"Which Teradata-specific features are used across the DDL and DML files, and
  which require the most careful translation to GoogleSQL?"*
- *"What's the best strategy for the BTEQ scripts — `bq load`/`EXPORT DATA`,
  Dataform, or Cloud Composer?"*
- Use the analysis to plan a fuller migration that includes the stored procedures
  and macros.

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the warehouse schema relationships and
ETL flow. Coverage depends on repo structure, but this typically helps ensure the
migration preserves data lineage and business semantics.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the GoogleSQL parse? Are `CSUM`/`MAVG`/`QUALIFY`
  correctly translated? Is the parity harness green?
- **Leave a comment** asking Devin to also convert the stored procedures in
  `dml/stored_procedures/` and macros in `dml/macros/`.
- **Watch Devin respond** and push a follow-up commit.

### Key Takeaways

- **Non-invasive static analysis**: Devin converts Teradata SQL from source files
  alone — no Teradata instance access and no GCP account required for the
  conversion work.
- **Confidence comes from programmatic verification**: the parity harness gates
  "done" and catches a real divergence (the `MAVG` 3-row vs 4-row window) that
  visual review would miss.
- **Feature-level mapping**: every Teradata construct (PI, PPI, COMPRESS, CSUM,
  MAVG, HASHROW) has a documented GoogleSQL equivalent or removal rationale in
  `docs/teradata_features_reference.md`.
- **Parallel migration workstreams**: DDL tables, views, stored procedures, and
  macros can each be converted independently — a natural fit for
  [child-session parallelism](../../reference/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents).

---

## Going Further

### Automation and Webhooks

Set up a CI trigger so that whenever new Teradata DDL is added to the repository,
a Devin session generates the GoogleSQL equivalent, runs the parity harness, and
updates the migration runbook. See
[Design Patterns → Event-Driven Triggers](../../reference/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers).

### Child Sessions for Scale

For large Teradata estates with hundreds of tables, use the
[divide-and-conquer pattern](../../reference/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents):
a parent session inventories all DDL objects, then spawns a child session per
object type or per schema. Each child converts its objects, runs the parity
harness on its branch, and opens its own PR.

### Scheduled Sessions

Schedule a recurring Devin session to re-validate converted SQL against evolving
BigQuery best practices (clustering recommendations, deprecated syntax). See
[Platform Capabilities → Scheduled Sessions](../../reference/general-themes/platform-capabilities.md#scheduled-sessions).

### Shared Context Layer

Create organization-level
[knowledge notes](../../reference/general-themes/architecture-strengths.md#shared-context-layer)
documenting your Teradata-to-BigQuery translation standards (e.g., "PI columns
map to CLUSTER BY", "MAVG(x, n) → ROWS BETWEEN (n-1) PRECEDING AND CURRENT ROW").
Every Devin session inherits these conventions. A portable Playbook
(`.workshop/playbooks/convert-teradata-to-bigquery.devin.md`) and a repo Skill
(`.agents/skills/teradata-to-bigquery/`) already ship in the repo.

### Team-Based Operation

DBAs, data engineers, and business analysts can review the migration PR
simultaneously. Devin reads feedback from any reviewer and iterates. See
[Collaboration Model](../../reference/general-themes/collaboration-model.md).
