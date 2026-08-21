# MuleSoft → Boomi — API Migration Demo

A single linear demo that shows Devin migrating a MuleSoft Mule 4 Employee
Services API to Boomi process components: assess the MuleSoft estate, convert
one route live, prove behavioral parity by replaying golden fixtures recorded
from the source and diffing field-by-field, catch a real divergence and fix
it, then fan the remaining routes out in parallel. Each conversion lands as a
PR with green component + parity gates.

The prompts below invoke the `!convert-mulesoft-to-boomi` Devin Playbook — the
reusable conversion procedure — whose source lives in the code repo at
[`uc-api-migration-mulesoft-to-boomi/.workshop/playbooks/convert-mulesoft-to-boomi.devin.md`](https://github.com/Cognition-Partner-Workshops/uc-api-migration-mulesoft-to-boomi/blob/main/.workshop/playbooks/convert-mulesoft-to-boomi.devin.md).
The repo-specific `make validate` / `make parity` mechanics come from that
repo's Skill (`.agents/skills/mulesoft-to-boomi-migration/SKILL.md`).

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Does the Migration](#part-1)
  - [Act 1 — Assess the MuleSoft estate](#act-1)
  - [Act 2 — Convert one route live, with verification](#act-2)
  - [Act 3 — Fan out in parallel](#act-3)
  - [Act 4 — Confidence = programmatic verification](#act-4)
- [Part 2 — Run the Produced Artifact](#part-2)
- [Confirming Completion on the Boomi Side](#confirm-boomi)
- [Concurrent Runs](#concurrent)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

From the `uc-api-migration-mulesoft-to-boomi` repo root:

```bash
make db-up install            # seeded PostgreSQL (Docker) + Python deps (uv)
make harness-test             # runner self-tests — green on main
make verify                   # component gate + parity gate — RED on main (no
                              # components yet: that is the starting point)
make db-down                  # tear down
```

Prerequisites: Docker, Python 3.11+, and [uv](https://docs.astral.sh/uv/).

---

<a id="repositories"></a>
## Repositories

- [ts-java-mulesoft-employee-api](https://github.com/Cognition-Partner-Workshops/ts-java-mulesoft-employee-api) — the MuleSoft Mule 4 source estate: Mule XML flows for OAuth2, employee goals, learning status, pay date, and PTO; the RAML spec; PostgreSQL integration. Read-only reference for the "before".
- [uc-api-migration-mulesoft-to-boomi](https://github.com/Cognition-Partner-Workshops/uc-api-migration-mulesoft-to-boomi) — the Boomi target: the vendored source contract (`contracts/source/`), estate inventory tooling (`inventory/`), an empty component scaffold (`boomi/components/`) with a closed-set component schema, a golden-fixture parity harness with a local process runner (`harness/`), the conversion playbook source (`.workshop/playbooks/`), and the repo Skill (`.agents/skills/`).

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Data |
|---|---|---|
| **Before** | `main`: the vendored MuleSoft flows + RAML, the inventory/assessment tool, an **empty** `boomi/components/` directory, the component schema, 15 golden fixture cases with an integrity manifest, the local process runner, the playbook source, and the Skill. | `employee_db`: deterministic seed (`api_clients`, `employee_goals`, `employee_learning`, `employee_pto`) from `db/init/` |
| **After** | a PR branch with Boomi process components for all 7 in-scope routes — built live by Devin | Same database; the parity gate resets it to the seed before every replayed case |

What Devin builds **live** is the Boomi side: one process component per route
(OAuth token, health, and the five employee operations) plus a shared
token-validation subprocess, by reading the Mule XML and mapping each construct
to its Boomi equivalent.

The verification loop sits between them: the **component gate** proves each
route has a schema-valid process with auth wired before data access, and the
**parity gate** replays every golden fixture recorded from the source MuleSoft
behavior and diffs status + body field-by-field (array order and numeric types
preserved), plus SQL probes for database side effects. Fixtures are
integrity-locked — editing them requires an audited reason — so a red gate can
only be fixed by fixing the components.

> **On "parity":** there is no live Boomi tenant in this environment, so the
> components run through a local, deterministic interpreter of the component
> XML. The same definitions and the same fixtures replay against a real test
> Atom via the AtomSphere Platform API (`harness/atomsphere_client.py` shows
> the call plan it would make).

---

<a id="part-1"></a>
## Part 1 — Devin Does the Migration

<a id="act-1"></a>
### Act 1 — Assess the MuleSoft estate

The part that usually kills migration timelines is the inventory. Ask Devin to
produce it. With DeepWiki over the repo, Devin typically maps an unfamiliar
estate in minutes (coverage depends on repo structure).

```
Using the uc-api-migration-mulesoft-to-boomi repo, run the estate
assessment (make inventory) over the vendored MuleSoft source at
contracts/source/employee-services-api.xml and walk me through the
report: every flow, its endpoint, connectors and SQL used, DataWeave
transform count, error handlers, the complexity score, and — most
importantly — which flows map cleanly to Boomi processes and which are
flagged for redesign (object store usage, Salesforce callback flows,
static web content) and why.
```

Expected: the per-flow inventory from `inventory/report/estate.md` — the 7
in-scope routes (OAuth token, health, goals, learning-status, next-pay-date,
PTO balance, PTO schedule) classified *maps cleanly*, and the remaining flows
(registration/login/refresh/disconnect, CORS preflight, static content)
flagged for redesign with reasons. This is the wave-sequencing input: what to
convert now, what needs a design decision, what to kill.

<a id="act-2"></a>
### Act 2 — Convert one route live, with verification

The core beat. Paste the playbook prompt for the goals route. Devin reads the
Mule flow, authors the Boomi process component + the shared token-validation
subprocess, runs the component gate, then the parity gate — and the gate
catches a divergence.

```
!convert-mulesoft-to-boomi

Convert the employee goals route from the MuleSoft estate into a Boomi
process component in Cognition-Partner-Workshops/uc-api-migration-mulesoft-to-boomi.

- MuleSoft source: contracts/source/employee-services-api.xml
  (the get:\employee\(employeeId)\goals flow and validate-token-subflow)
- Scope: GET /api/employee/{employeeId}/goals, plus the shared
  "Common - Validate Token" subprocess it requires
- Namespace: migration/emp-goals
```

**The verification beat (the real bug).** Every other endpoint in this estate
returns a 404 with an error body when a lookup comes up empty — so the natural
Boomi mapping does the same for goals. The parity gate fails the `goals-none`
case:

```
[FAIL] goals-none — SOURCE QUIRK: employee with no goals returns HTTP 200 (not 404)
        status: 404 != expected 200
        $.message: missing (expected "No goals found for employee 74")
```

The source Mule flow really does return **HTTP 200** with
`{"message": "No goals found for employee 74"}` — a quirk consumers may depend
on. The fix is a decision shape routing the empty result to a 200 message
shape reproducing that exact body — and a migration note flagging the quirk
rather than silently "improving" it. Re-run:

```bash
make parity
# [PASS] goals-found — GET goals for employee 101 — bare ordered JSON array...
# [PASS] goals-none  — SOURCE QUIRK: employee with no goals returns HTTP 200...
```

The point: "looks reasonable" review would have shipped the 404. The recorded
source behavior caught it. The full write-up is in the playbook at
`.workshop/playbooks/convert-mulesoft-to-boomi.devin.md` → *Worked example*.

<a id="act-3"></a>
### Act 3 — Fan out in parallel

The remaining routes are independent, so run one **orchestrator** session that
spawns a child Devin session per route group and monitors them to green — one
agent dividing and conquering across the wave, each child on its own namespace
branch with its own verified PR. Paste:

```
Act as the orchestrator for a MuleSoft-to-Boomi migration, using child
Devin sessions to parallelize the work in
Cognition-Partner-Workshops/uc-api-migration-mulesoft-to-boomi.

Spawn one child Devin session per route group below. Give each child its
own namespace branch (migration/child1, child2, ...) and tell it to
follow the !convert-mulesoft-to-boomi playbook (the repo's Skill supplies
the make validate / make parity mechanics): treat the recorded MuleSoft
behavior as the source of truth, reproduce it exactly, and iterate until
the component gate and every parity case for its routes are green.

Route groups:
1. OAuth + health: POST /oauth/token, GET /health
2. Learning status: GET /api/employee/{employeeId}/learning-status
3. Pay date: GET /api/employee/{employeeId}/next-pay-date
4. PTO: GET /api/employee/{employeeId}/pto/balance +
   POST /api/employee/{employeeId}/pto/schedule

After launching, monitor the children until every route group is
converted with green gates. Summarize results and call out any parity
divergences the children caught (status codes, field types, array
ordering, or database side effects that did not match the source).
```

Each child runs in its own isolated VM with its own scoped credentials and
namespace branch, so the parallel conversions never collide — isolation is
what makes the fan-out safe. This is the same verified loop as Act 2, run many
times at once. The 5th route costs nearly nothing once the pattern is codified
in the playbook — that is the leverage over per-app hand porting.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

The gates that make every PR trustworthy:

- **Component gate** (`make validate`): every in-scope route has a schema-valid
  Boomi process (closed-set schema — an unknown shape type fails, never
  silently no-ops), with the token-validation subprocess wired before any
  database access.
- **Parity gate** (`make parity`): 15 golden fixture cases replayed against
  the components with the database reset to seed before each — status, body
  field-by-field (order- and type-sensitive), volatile fields masked but never
  structure, SQL probes for side effects. The fixture manifest makes the
  evidence tamper-evident: changing a golden case requires an audited reason.
- **Devin Review**: an automated reviewer on every PR.

A conversion is "done" when both gates are green on the PR — not when the
component XML "looks right". The same loop runs unattended on a cadence as a
scheduled Devin (a nightly parity sweep), and an Automation can trigger the
conversion loop on events — e.g. rerun and push a fix when CI turns red.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifact

Show the converted processes serving real traffic, end to end. On the
migration branch:

```bash
make db-up                    # seeded PostgreSQL
make run                      # serve the Boomi components locally on 127.0.0.1:8090
```

In a separate terminal, exercise the full API lifecycle:

```bash
# 1. Health check
curl -s http://127.0.0.1:8090/health
# {"status":"healthy","database":"connected","timestamp":"..."}

# 2. Obtain an OAuth token (client credentials from the seed)
TOKEN=$(curl -s -X POST http://127.0.0.1:8090/oauth/token \
  -H "Content-Type: application/json" \
  -d '{"client_id":"demo-portal","client_secret":"demo-portal-secret"}' \
  | jq -r '.access_token')

# 3. Employee goals — ordered array of goal strings
curl -s -H "Authorization: Bearer $TOKEN" http://127.0.0.1:8090/api/employee/101/goals

# 4. The quirk, live: no goals -> 200 with a message body (not 404)
curl -s -w "\n%{http_code}\n" -H "Authorization: Bearer $TOKEN" \
  http://127.0.0.1:8090/api/employee/74/goals
# {"message": "No goals found for employee 74"}
# 200

# 5. Learning status, pay date, PTO balance
curl -s -H "Authorization: Bearer $TOKEN" http://127.0.0.1:8090/api/employee/101/learning-status
curl -s -H "Authorization: Bearer $TOKEN" http://127.0.0.1:8090/api/employee/101/next-pay-date
curl -s -H "Authorization: Bearer $TOKEN" http://127.0.0.1:8090/api/employee/101/pto/balance

# 6. Schedule PTO (writes to the database)
curl -s -X POST -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" \
  -d '{"startDate":"2026-10-01","endDate":"2026-10-05"}' \
  http://127.0.0.1:8090/api/employee/74/pto/schedule

# 7. Missing token -> 401 with the source's exact error body
curl -s -w "\n%{http_code}\n" http://127.0.0.1:8090/api/employee/101/goals
# {"error":"missing_token","error_description":"Authorization header is missing or invalid"}
# 401
```

Then run the full gate suite and tear down:

```bash
make verify
# COMPONENT GATE: 7/7 routes implemented
# PARITY: 15/15 parity cases passed
make db-down
```

---

<a id="confirm-boomi"></a>
## Confirming Completion on the Boomi Side

The migration milestone is complete when four things are true; walk through in
this order:

**1. The component gate is green.** `make validate` shows all 7 routes covered
by schema-valid process components, each protected route calling the shared
`Common - Validate Token` subprocess before touching the database. This is the
structural proof — the Boomi side is fully populated, not partially sketched.

**2. The parity report — every case PASS.** Open
`harness/reports/parity-report.json` (or the summary pasted in the PR body):
15/15 cases, each with the fixture fingerprint of the exact source XML,
schema, seed, and golden cases it was graded against. The quirk cases
(`goals-none`, `pto-schedule`) passing is the "behaves the same" evidence —
field-by-field, side effects included.

**3. The deployment plan for a real tenant.** Open
`harness/reports/atomsphere-plan.json`: the recorded
`create_component` / `deploy` / `execute` calls the AtomSphere client would
make against a test Atom — the same component definitions, the same fixtures,
pointed at a real Boomi account by setting `BOOMI_API_TOKEN`. This view proves
the path from local verification to a live tenant is the same loop, not a
rewrite.

**4. The before is untouched.** `main` still has an empty
`boomi/components/` directory and a red `make verify` — the durable starting
point. The converted components live on the namespace branch (`migration/…`),
ready for review and merge when the team decides. The MuleSoft source is
completely unmodified.

---

<a id="concurrent"></a>
## Concurrent Runs

Each conversion targets its own namespace branch, and each session runs in its
own isolated VM, so multiple runs — and the Act 3 fan-out — coexist with no
collisions. On a shared machine, namespace the database too:

```bash
COMPOSE_PROJECT_NAME=run-a PGPORT=5433 make db-up
DATABASE_URL=postgresql://employee_user:employee_pass@localhost:5433/employee_db make parity
```

The parity gate resets the database to the seed before every case, so runs are
repeatable by construction; revert is deleting the branch and `make db-down`.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The value on display is **Devin doing the migration**: assessing an unfamiliar MuleSoft estate (inventory, clean-map vs redesign flags, complexity scores), converting flows to Boomi processes off a reusable playbook, and proving each conversion against recorded source behavior — not a finished artifact to admire.
- **Confidence comes from programmatic verification.** A closed-set component gate plus field-by-field golden-fixture replay (order, types, and database side effects included) gate every conversion, and the demo shows a real divergence — the 200-with-message goals quirk — being caught and fixed. The fixtures are tamper-evident, so a red gate cannot be "fixed" by editing the evidence.
- **Recorded source behavior is the source of truth**: quirks are reproduced and flagged, never silently redesigned; API cleanup is a separate, deliberate decision.
- Conversions are **independent and parallelizable** — an orchestrator session fans out child sessions per route group, each in its own isolated VM and namespace branch with its own verified PR, and the playbook keeps every run consistent. The 5th route is nearly free once the pattern is codified.
- The local runner keeps the loop offline and deterministic, while `harness/atomsphere_client.py` shows the identical loop pointed at a real test Atom via the AtomSphere Platform API — local verification and live deployment are the same procedure, not two migrations.
