# Synthetic Test-Data Generation from GitHub Issues — Demo

A single linear demo showing Devin reading a GitHub Issue (user story +
acceptance criteria), generating synthetic test data scripts for a lower
environment, loading the data into Postgres, and **proving** correctness through
a programmatic validation harness — catching a real temporal-consistency bug,
fixing it, and going green. The second half fans out across multiple issues in
parallel and confirms completion in the database.

The prompts below invoke the `!generate-testdata` Devin Playbook — the reusable
generation procedure — whose source lives in the code repo at
[`otterworks/.workshop/playbooks/synthetic-testdata-generation.devin.md`](https://github.com/Cognition-Partner-Workshops/otterworks/blob/main/.workshop/playbooks/synthetic-testdata-generation.devin.md).
The repo-specific `make testdata-validate` / `make testdata-clean` mechanics
come from that repo's Skill
(`.agents/skills/synthetic-testdata-generation/SKILL.md`).

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Generates the Test Data](#part-1)
  - [Act 1 — Orient over the application schema](#act-1)
  - [Act 2 — Generate data for one issue, with verification](#act-2)
  - [Act 3 — Fan out across multiple issues](#act-3)
  - [Act 4 — Confidence = programmatic verification](#act-4)
- [Part 2 — Confirm the Data in the Database](#part-2)
- [Concurrent Runs](#concurrent)
- [Scheduling and Automations](#scheduling)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Start the local infrastructure, then run the full lifecycle:

```bash
make infra-up                         # Postgres, Redis, LocalStack
make testdata-setup-schema NS=dev     # create otterworks_dev schema
python testdata/generated/dev/generate.py --ns dev   # load data
make testdata-validate NS=dev CRITERIA=testdata/generated/dev/criteria.json
make testdata-clean NS=dev            # revert
```

Prerequisites: Docker (for `make infra-up`) or a running Postgres instance with
OtterWorks credentials set via environment variables.

---

<a id="repositories"></a>
## Repositories

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — the
  polyglot microservices platform with real Postgres schemas (auth-service
  Flyway migrations + admin-service Rails schema), the validation harness
  (`testdata/`), the playbook source (`.workshop/playbooks/`), and the repo
  Skill (`.agents/skills/`).

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Data |
|---|---|---|
| **Before** | `main`: the validation harness, Makefile targets, schema DDL, the playbook source, the repo Skill, and sample GitHub Issues with user stories | The empty namespaced schema (`otterworks_<ns>`) — tables exist, no rows |
| **After** | A PR branch with generated scripts in `testdata/generated/<ns>/` (the generation + simulation scripts and criteria.json) | Rows loaded into `otterworks_<ns>` satisfying all acceptance criteria |

The **before** state is deliberately empty: the harness and tooling are in place
on `main`, but the generated scripts are what Devin produces live. Each run
writes to an isolated namespace, so the demo repeats without collision.

The verification loop bridges them: every generated dataset is validated against
the schema invariants AND the issue's acceptance criteria before the PR ships.

---

<a id="part-1"></a>
## Part 1 — Devin Generates the Test Data

<a id="act-1"></a>
### Act 1 — Orient over the application schema

Open the OtterWorks repo and ask Devin to map the database estate. With
DeepWiki over the repo, Devin typically maps an unfamiliar schema in minutes
(coverage depends on repo structure).

```
Using the Cognition-Partner-Workshops/otterworks repo, give me a map of
the database schema across all services: the tables in auth-service
(Flyway migrations) and admin-service (Rails schema.rb), their columns,
types, FK relationships, enum constraints, and business rules. Identify
which tables need synthetic data for a realistic lower-environment dataset.
```

Expected: a tour of `services/auth-service/src/main/resources/db/migration/V*.sql`
and `services/admin-service/db/schema.rb`, with FK relationships, enum values,
and a recommendation for generation order (parents before children).

<a id="act-2"></a>
### Act 2 — Generate data for one issue, with verification

The core beat. Paste the playbook prompt for one GitHub Issue. Devin reads the
issue, introspects the schema, generates the scripts, loads data, runs the
validation harness, catches a divergence, fixes it, and produces a PR with a
green report.

```
!generate-testdata

Read GitHub Issue #376 on the otterworks repo. It describes a user story
for load-testing the file-sharing feature — 500 users with storage quotas,
2000+ audit-log entries showing file operations, and realistic activity
distribution.

Generate the synthetic data scripts, load them into namespace "dev", and
validate everything passes.

- Issue: #376
- Namespace: dev
- Repo: Cognition-Partner-Workshops/otterworks
```

**The verification beat (the real bug).** A natural implementation generates
audit-log entries and admin users independently, then assigns `actor_id` values
from the user pool. This produces entries where `audit_logs.created_at` is
**earlier** than the referenced `admin_users.created_at` — the audit log claims
a user performed an action before their account existed.

```bash
make testdata-validate NS=dev CRITERIA=testdata/generated/dev/criteria.json
#   temporal_consistency:audit_logs.created_at<admin_users.created_at | FAIL |
#     12 entries reference resources created after the event timestamp
```

Fix: generate users first with a `created_at` window, then constrain each
audit-log entry to fall within its actor's lifetime. Re-run:

```bash
make testdata-validate NS=dev CRITERIA=testdata/generated/dev/criteria.json
#   temporal_consistency:audit_logs.created_at<admin_users.created_at | PASS |
#     all events respect causal ordering
```

The point: "looks reasonable" review would ship temporally impossible data that
breaks realistic test scenarios (e.g., "show me a user's first-week activity"
returns nothing). The harness caught it programmatically.

<a id="act-3"></a>
### Act 3 — Fan out across multiple issues

Data-generation tasks are independent per issue, so launch a Devin session per
issue. Each follows the same playbook and produces its own verified PR — the same
quality bar applied many times in parallel instead of once in series.

| Session | Issue | Data produced |
|---|---|---|
| 1 | #376 — Load-test file sharing | 500 users, 2000+ audit logs, storage quotas (the Act 2 worked example) |
| 2 | #377 — QA incident management | 50 incidents across severities, linked audit trails, resolution timestamps |
| 3 | #378 — Staging announcements & feature flags | 20 announcements with scheduling, 15 feature flags with rollout configs |

Each session uses its own namespace (`NS=child1`, `NS=child2`, …) so the
parallel loads never collide.

#### Parallelize from a single session (parent → child)

Instead of launching each session by hand, run one **orchestrator** session that
spawns child Devin sessions per issue and monitors them:

```
Act as the orchestrator for synthetic test-data generation across multiple
GitHub Issues, using child Devin sessions to parallelize the work.

Repo: Cognition-Partner-Workshops/otterworks

Spawn one child Devin session per issue below. Give each child the repo, its
own namespace, and tell it to follow the !generate-testdata playbook: read the
issue's acceptance criteria, generate scripts, load data, and validate until
green.

Issues:
1. Issue #376 -> namespace child1 (500 users + file-sharing audit logs)
2. Issue #377 -> namespace child2 (50 incidents + audit trails)
3. Issue #378 -> namespace child3 (announcements + feature flags)

After launching, monitor the child sessions until each is validated green.
Summarize results and call out any validation failures the children caught
and fixed.
```

The children inherit the organization's DB credentials, and each writes to its
own namespace so the parallel loads never collide. This is the same verified
generation loop as a single session — run many times at once, from one parent.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

The gates that make every PR trustworthy:

- **Validation harness** (`testdata/harness/validate.py`): schema conformance,
  FK integrity, temporal consistency, enum values, business rules, and
  acceptance-criteria coverage — all encoded as machine-checkable assertions.
- **Criteria file** (`criteria.json`): maps directly from the GitHub Issue's
  acceptance criteria to row-count / distribution / uniqueness checks.
- **Devin Review**: an automated reviewer on every PR.

A generation is "done" when the validation harness reports all PASS, against the
criteria derived from the issue — not when the script merely runs without errors.

---

<a id="part-2"></a>
## Part 2 — Confirm the Data in the Database

Show the generated data live in Postgres:

```bash
make testdata-setup-schema NS=dev
python testdata/generated/dev/generate.py --ns dev
make testdata-validate NS=dev CRITERIA=testdata/generated/dev/criteria.json
```

Query the before and after side by side:

```sql
-- Schema exists and has data
SELECT schemaname, tablename, n_live_tup
FROM pg_stat_user_tables
WHERE schemaname = 'otterworks_dev'
ORDER BY n_live_tup DESC;

-- Users generated with realistic distribution
SELECT role, count(*) FROM otterworks_dev.user_roles GROUP BY role;
SELECT status, count(*) FROM otterworks_dev.admin_users GROUP BY status;

-- Temporal consistency proven: no impossible orderings
SELECT count(*) AS temporal_violations
FROM otterworks_dev.audit_logs a
JOIN otterworks_dev.admin_users u ON a.actor_id = u.id
WHERE a.created_at < u.created_at;  -- should be 0

-- Storage quotas respect the business rule
SELECT count(*) AS quota_violations
FROM otterworks_dev.storage_quotas
WHERE used_bytes > quota_bytes;  -- should be 0
```

Clean up is one command — the durable infrastructure is never touched:

```bash
make testdata-clean NS=dev   # DROP SCHEMA otterworks_dev CASCADE
```

---

<a id="concurrent"></a>
## Concurrent Runs

Each output schema is namespaced, so multiple runs — and the parallel fan-out in
Act 3 — coexist with no collisions:

```bash
make testdata-setup-schema NS=alice
make testdata-setup-schema NS=bob
# Both generate and validate independently
make testdata-validate NS=alice
make testdata-validate NS=bob
make testdata-clean NS=alice
make testdata-clean NS=bob
```

---

<a id="scheduling"></a>
## Scheduling and Automations

### Scheduled Devins — nightly data refresh

Configure a scheduled Devin session to regenerate test data nightly, keeping
lower environments stocked with fresh, validated datasets:

```
Every night at 2 AM UTC, run !generate-testdata against Issues labeled
testdata-refresh in the Cognition-Partner-Workshops/otterworks repo,
namespace nightly.
```

The same playbook and harness run unattended on a cadence — if validation fails,
Devin attempts to fix the generator before opening the PR.

### Devin Automations — on new issue

Set up an automation triggered when a new issue is labeled `testdata`:

```
When a GitHub Issue is labeled testdata on the
Cognition-Partner-Workshops/otterworks repo, start a Devin session that
invokes !generate-testdata with the issue number and namespace derived
from the issue ID.
```

This turns test-data requests into a self-service workflow: a QA engineer writes
the user story, labels it, and Devin generates validated data automatically.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The value on display is **Devin interpreting a user story** and turning acceptance criteria into working, validated data-generation code — not manually translating requirements into scripts.
- **Confidence comes from programmatic verification.** The validation harness (FK integrity, temporal consistency, business rules, criteria coverage) gates every generation, and the demo shows a real bug (temporal impossibility in audit logs) being caught and fixed. "Looks reasonable" review would have shipped broken test data.
- **The GitHub Issue is the source of truth**: acceptance criteria become machine-checkable constraints in `criteria.json`, creating a direct link from requirement to verification.
- Generations are **independent and parallelizable** — multiple Devin sessions handle multiple issues at once, each producing its own verified PR. The playbook keeps every run consistent.
- **Namespaced schemas** make runs safe to repeat and run concurrently. One-command revert (`make testdata-clean NS=...`) ensures no contamination. Isolation is a feature, not a flaw.
- **Automations + schedules** turn this from a one-off demo into an operational workflow: new issues auto-trigger generation, nightly runs keep environments fresh.
