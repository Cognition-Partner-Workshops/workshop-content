# Oracle-Style Stored Procedures → Microservices + React — End-to-End Demo

Move business logic out of the **database** and into a service, with a human
approving the rules and a harness proving the result: inventory the procedures,
record the behavior of the *running* database, get the derived rules signed off,
extract one module live, catch a real divergence, fan the rest out in parallel,
and drive the new service from a React UI.

This thread's estate is PostgreSQL PL/pgSQL — the same problem shape (packages
of procedures and functions holding the rules, a thin app that only calls
them), running in Docker with no licensing to arrange. Everything in this
thread applies unchanged to an Oracle, DB2, or SQL Server estate; only the
dialect of the `.sql` files differs.

For a genuine Oracle starting point, the same repo carries the **Commission
Pay insurance fixture** under `services/industry-solutions/insurance/` — see
the [Repository](#repository) section.

Two gates run this demo, and neither is optional:

| Gate | What it is | What it prevents |
|---|---|---|
| **Human-approved rule ledger** | Devin derives each business rule from the SQL, cites its source lines, and raises an explicit question wherever the SQL is ambiguous. A human approves, or changes it with a reason. | An extraction built on a *plausible reading* of decades of accreted SQL. Nothing gets implemented that a person did not sign off on. |
| **Golden-transcript parity** | The running procedures were driven scenario by scenario and their results recorded as immutable transcripts. The extracted service replays them. | A service that passes review and its own unit tests and is quietly wrong about a rounding direction, an ordering, or a date boundary. |

The extraction prompts invoke the `!stored-procs-to-microservices` Devin
Playbook, whose source lives in the target repo at
`.workshop/playbooks/stored-procs-to-microservices.devin.md`. The repo-specific
mechanics (make targets, namespaces, harness paths, exit codes) come from that
repo's Skill at `.agents/skills/stored-procs-to-microservices/SKILL.md`, which
Devin auto-loads when working there.

## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [Before, After, and the Two Gates](#before-after)
- [Phase 1 — Orient Over the Estate](#phase-1)
- [Phase 2 — Record, Approve, Extract](#phase-2)
  - [Record the legacy behavior first](#record)
  - [The human gate: the rule ledger](#hitl)
  - [Extract one module live](#extract-one)
  - [The divergence the harness catches](#divergence)
  - [Fan out in parallel](#fan-out)
- [Phase 3 — The React UI Talks to the Service, Not the Database](#phase-3)
  - [Always-on: scheduled and event-driven](#always-on)
- [Confidence = Programmatic Verification](#confidence)
- [Run the Produced Artifact](#run-artifact)
- [Concurrent Runs](#concurrent)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

```bash
git clone https://github.com/Cognition-Partner-Workshops/otterworks
cd otterworks

make procs-up NS=demo          # legacy Postgres + procs + thin Flask app, and the extracted service
make procs-list                # the four modules, their scenarios, and which are extracted
make procs-rules-gate ALL=1    # the human-approval gate
make procs-parity NS=demo      # replay the recorded legacy behavior against the service
make procs-down NS=demo
```

The starting state prints `Rules gate PASS: plans` and
`Parity PASS=5 FAIL=0 SKIP=19`. The 19 SKIPs are the three modules whose logic
is still in the database. Turning those SKIPs into PASSes is the demo.

Host ports are derived from the namespace name (`crc32(NS) % 1000` as an offset)
and bound to `127.0.0.1`, so several runs coexist. For `NS=demo` that is the
legacy app on `8944` and the billing service on `12944`.

---

<a id="repository"></a>
## Repository

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — everything is in one repo:
  - `services/legacy-billing/` — the **before**: a billing estate whose rules live in `db/procs/{plans,rating,invoicing,dunning}.sql`, fronted by a deliberately thin Flask app that only binds parameters, calls `billing.fn_*` / `billing.sp_*`, and renders the result. No rule is duplicated in Python.
  - `services/industry-solutions/insurance/` — the **Oracle before**: the Commission Pay fixture on an Oracle Database Free container (zero license cost, full PL/SQL fidelity), with the commission-rate and split-commission business rules in the `COMMISSION_PAY` PL/SQL package and an OLAP star schema in `COMMISSION_DW`, plus seed data and PL/SQL test suites. Run it with `make insurance-up NS=<ns>`, `make insurance-test NS=<ns>`, `make insurance-down NS=<ns>`. Use it when the demo should start from real Oracle PL/SQL.
  - `procs/` — the controls: `scenarios/` (24 scenarios across the four modules), `transcripts/` (the immutable recordings plus `SOURCE_SHA` and `FIXTURE_SHA`), `rules/` (the approved ledgers), `routes.yaml` (the mapping contract), `harness/`, `reports/`.
  - `services/billing-service/` — the **after** pattern: FastAPI, its own `billing_svc` schema, rules in pure Python, a thin repository with no ordering or conditional logic in SQL.
  - `frontend/client-app/src/features/billing/` — the React screens for the extracted module.

Note what the repo deliberately does **not** contain: any document stating the
business rules. Deriving them from the SQL is the work.

---

<a id="before-after"></a>
## Before, After, and the Two Gates

`main` is the durable before-state. One module — `plans` — is already extracted
as the reference pattern, so the target architecture, the ledger format, and the
harness are established. What Devin extracts **live** is a module that is still
`pending` on `main`: `rating`, whose rules are the interesting ones (included
quota, rollover credit from unused prior periods, tiered overage).

A transcript is a recording of what the *running* database did, normalized to
business outcomes — `procs/transcripts/plans/PLANS-001.json`:

```json
{
  "module": "plans",
  "scenario": "PLANS-001",
  "entrypoint": "billing.fn_list_plans",
  "rules": ["PLANS-001"],
  "source_sha": "2b98f8dc350f9c70906398cf1ee3bae173239b53237b89b2ade4a51d43821929",
  "fixture_sha": "53b308f59df78d844cc95bb8d9071fdd8eb03bbae52c708dd67820d099f406da",
  "inputs": {},
  "business_fields": {
    "codes": ["STARTER", "GROWTH", "SCALE"],
    "fees": ["49.00", "149.00", "499.00"]
  },
  "probes": {}
}
```

For a mutating procedure, the transcript also captures state probes — the rows
the call left behind — because "the response looked right" is not the same
claim as "the database ended up in the same state". Read-only function
scenarios have no probes because their `fields` already capture the returned
outcome. Mutation probes use one named business value per probe id, with
`collect_rows` and explicit columns when several rows matter.

The transcript's `source_sha` covers only `services/legacy-billing/db/procs/*.sql`
(procedure logic). Its `fixture_sha` covers
`services/legacy-billing/db/schema.sql` and `seed.sql`. Both are stored globally
under `procs/transcripts/` and on every transcript, and replay verifies both
globally and per transcript. Row comparisons preserve the order recorded by the
scenario's `capture_query`; there is no `sort_by` canonicalization because
ordering can be a business rule.

Scenarios are pure operations over one fixed seed. They do not declare
`setup_sql`, and the harness test rejects any scenario that tries to add it.
Preconditions are seeded as distinct data instead: `RATING-001` rates a tenant
with no prior finalized periods, `RATING-005` rates a tenant seeded at 202
units, and `INVOICE-003` issues a tenant whose credit dates are chronological.
The recorder's `after_sql` hook is different: it runs after the entrypoint.
`DUNNING-005` uses it to call the suspension procedure a second time, proving
idempotency through `notification_kinds` rather than a notification count.

Replay grades only modules marked `extracted`, skips only modules marked
`pending`, and treats any other status as a contract error. Once grading has
begun, it writes both `procs/reports/parity.md` and
`procs/reports/parity.json` on every exit path, including a failed or
unreachable target, so the diagnostic is available when it is most needed.

> **On "parity":** parity means the recorded behavior of the running procedures
> reproduces against the new service — a deterministic contract over business
> outcomes, not a byte-for-byte diff. Recordings are immutable: the harness
> refuses to overwrite them unless the procedure or fixture digest changed, or
> an audited re-record reason was supplied.

---

<a id="phase-1"></a>
## Phase 1 — Orient Over the Estate

Nothing here changes code. The output is the map an extraction plan needs, and
the module boundaries it will respect.

```
Using the Cognition-Partner-Workshops/otterworks repo, map the stored-procedure
estate under services/legacy-billing/db:

- every procedure and function in db/procs/*.sql: its signature, the tables it
  reads, the tables it writes, and whether it is called from the app
  (services/legacy-billing/app/app.py) or from another procedure
- where the module boundaries actually fall, based on table ownership and
  cross-procedure calls rather than on which file something happens to live in;
  call out every shared table and every cross-module call, since those are the
  seams that will hurt
- for each procedure, which lines encode a business rule versus which are
  plumbing (parameter binding, cursor mechanics, result assembly)

Give me a table per section and write the inventory to
analysis/PROCS_INVENTORY.md.
```

Then the question that decides the plan.

```
For the four modules (plans, rating, invoicing, dunning) in
Cognition-Partner-Workshops/otterworks, tell me which business rules are
implemented *only* in SQL and would be lost if someone rewrote these procedures
from the documentation. Quote the exact lines.

Pay specific attention to: rounding direction and where it happens in the
arithmetic, date boundaries (inclusive vs exclusive), the order in which
credits, caps, and tiers apply, and any ORDER BY whose result a caller depends
on. Write it to analysis/RULES_AT_RISK.md.
```

That last sentence is not decoration. Those four categories are where ports
diverge, and the demo's caught divergence is one of them.

> Commit the accepted map, or keep it as a Knowledge note. Every later session —
> including the children in the fan-out — then starts from the same agreed map
> instead of re-deriving it.

---

<a id="phase-2"></a>
## Phase 2 — Record, Approve, Extract

<a id="record"></a>
### Record the legacy behavior first

The baseline must come from the running database *before* the port exists —
otherwise it agrees with the port's mistakes.

```bash
make procs-up NS=demo
make procs-record NS=demo MODULE=rating
```

The `rating` transcripts are already on `main`, so this run should refuse:

```
would overwrite immutable transcript(s) (pass --allow-rerecord only after
procedure source changes)
```

That refusal is the point — a recording is evidence, and evidence that can be
regenerated on demand is not evidence. A changed procedure source
(`SOURCE_SHA`) permits an authorized re-record. A changed fixture
(`schema.sql` or `seed.sql`, reflected in `FIXTURE_SHA`) is also a legitimate
re-record condition and does not require an audited reason. When both digests
are unchanged, use one of the audited paths:
`RERECORD_REASON=harness-change` for recorder or harness behavior changes, or
`RERECORD_REASON=scenario-redesign` for scenario setup or probe-shape changes.
Each reason is written into the regenerated transcripts.

<a id="hitl"></a>
### The human gate: the rule ledger

Now Devin derives the rules — and, more importantly, tells you what it is not
sure about.

```
In Cognition-Partner-Workshops/otterworks, derive the business rules of the
rating module from services/legacy-billing/db/procs/rating.sql and write the
ledger at procs/rules/rating.rules.yaml, following the format and field set of
the approved procs/rules/plans.rules.yaml exactly.

For each rule: the statement, the source file and line range it comes from, the
scenarios under procs/scenarios/rating/ that exercise it, your confidence, and —
wherever the SQL is ambiguous, surprising, or could be read two ways — an
explicit question for me.

Leave every decision status pending. Do not answer your own questions and do not
approve anything. Then show me the questions as a list, with the lines of SQL
each one is about.
```

Read the questions and answer them; that is the human-in-the-loop step, and the
gate enforces it:

```
Here are the decisions on procs/rules/rating.rules.yaml. Record each one with me
as the reviewer and today's date, and record my answer to every question
verbatim next to the rule it belongs to.

<your decisions here, rule by rule: approved as stated, or changed with the
reason and the corrected statement>

Then run `make procs-rules-gate MODULE=rating` and show me the output. Implement
only what I approved.
```

`make procs-rules-gate MODULE=rating` fails unless every rule has a decision
with a reviewer and a date, every question has an explicit answer, each cited
source range resolves inside that module's own `.sql` file, every scenario of
the module is claimed by a rule, and every rule id appears on a
`@pytest.mark.rule(...)` test in the service. `make procs-parity` refuses to
grade a module whose ledger is not green — the sign-off is a build gate, not a
convention.

Scenario YAML files do not declare rules. The approved ledger's `scenarios:`
claims are the only source of rule attribution; the recorder, transcript index,
and `make procs-list` derive their scenario rule lists from those claims. A
scenario with no approved claim remains recordable and gradable with an empty
rule list.

<a id="extract-one"></a>
### Extract one module live

```
!stored-procs-to-microservices

Module: rating, in Cognition-Partner-Workshops/otterworks —
billing.fn_usage_rating, billing.fn_usage_summary and
billing.sp_finalize_rating in services/legacy-billing/db/procs/rating.sql.

Approved rules: procs/rules/rating.rules.yaml (already reviewed and approved).

Target: extend services/billing-service the way the plans module is
implemented — rules in pure Python in app/domain.py with the rule id on each
test, a thin parameterized repository with no ordering or conditional logic in
SQL, routes mapped declaratively in procs/routes.yaml with rating flipped to
extracted.

Done means, in namespace demo:

    make procs-rules-gate MODULE=rating
    make procs-parity NS=demo

with the rating scenarios green, the plans scenarios still green, and invoicing
and dunning still SKIP.

Namespace: demo.
```

Devin implements the approved rules, maps the routes, and runs the loop. The
first run is the interesting one.

<a id="divergence"></a>
### The divergence the harness catches

It happened on the reference module, and it is the cleanest illustration of what
these gates are for. The extracted plan catalog returned the right three plans
at the right three prices. Its unit tests were green. Parity was not:

```
$ make procs-parity NS=demo

Parity PASS=4 FAIL=1 SKIP=19
FAIL plans/PLANS-001
  field codes:
    expected ['STARTER', 'GROWTH', 'SCALE']
    actual   ['SCALE', 'GROWTH', 'STARTER']
  field fees:
    expected ['49.00', '149.00', '499.00']
    actual   ['499.00', '149.00', '49.00']
make: *** [Makefile: procs-parity] Error 1
```

Right set, wrong order — and the order **is** the rule, because the first row of
that catalog is the plan the picker offers by default. Nothing in the procedure
said so. The ordering was a side effect of an `ORDER BY` at the bottom of a
function, which is exactly the kind of line a reviewer skims, and it appeared in
no documentation because there is none.

Note what did *not* catch it: not a unit test (they encode the developer's
understanding, which was the thing that was wrong), and not a human reviewer
looking at three correct plan codes and three correct prices.

The same gate then caught a more fundamental fixture defect. The recorder had
been applying scenario `setup_sql` to the legacy database before recording,
while replay reset only the target and never reproduced that setup. Two
scenarios therefore had identical inputs but different expected answers: one
deleted prior rating results to create a no-rollover case, while the other
retained them for rollover. A correct extracted service could not reproduce
both states from the fixed seed, so the gate refused to certify it even when
its business logic was right. The repair moved those preconditions into
distinct seeded tenants and re-recorded the transcripts; the procedures
themselves remained unchanged. That is a stronger reason to treat recordings
as evidence than merely checking that a probe is well-shaped: the harness
caught an unreproducible experiment, not an implementation divergence.

The fix goes into the domain code, against the procedure — never into the
transcript, the scenario, or the mapping contract. That distinction is what
separates a gate from a decoration, and the harness is built to make the
shortcut hard: there are no per-scenario contract overrides, transcripts are
immutable, and a transcript whose `SOURCE_SHA` or `FIXTURE_SHA` no longer
matches the procedures or fixture is a hard failure rather than a re-record.

> To reproduce it live, reverse the ordering in the plans domain function in
> `services/billing-service/app/domain.py`, then `make procs-up NS=demo` and
> `make procs-parity NS=demo`. It fails the same way every time.

<a id="fan-out"></a>
### Fan out in parallel

Modules are independent once their boundaries are agreed, so the rest of the
wave parallelizes.

```
Fan out the remaining billing modules in
Cognition-Partner-Workshops/otterworks. Spawn one child session per pending
module — invoicing and dunning — each following
!stored-procs-to-microservices in its own namespace, on its own branch:

- invoicing → NS=inv
- dunning   → NS=dun

Each child writes its own rule ledger with pending decisions and stops for my
approval before implementing anything. After approval, a child is done when
`make procs-rules-gate MODULE=<module>` and `make procs-parity NS=<ns>
MODULE=<module>` are both green, the already-extracted modules still pass, and
its PR is open with the parity report and the approved ledger in it.

Monitor them and report each child's status, the questions it raised, and the
divergences parity caught. No child may edit a transcript, a scenario, or
routes.yaml for another module. A child reporting "parity green" for a module
still marked pending has skipped, not passed — check the report, not the
summary.
```

Each child runs on its own VM with its own scoped credentials, namespace, and
branch. Isolation is what makes the fan-out safe: nothing shares a database, a
port, or a working tree. The playbook is what makes it consistent — the same
procedure and the same two gates applied N times in parallel instead of once in
series, including the pause for human approval, which every child performs
independently.

---

<a id="phase-3"></a>
## Phase 3 — The React UI Talks to the Service, Not the Database

The point of moving logic out of the database is that a modern client can now
consume it. The plans screens under
`frontend/client-app/src/features/billing/` are the pattern: they call the
extracted service's API, and no screen contains a rule.

```
In Cognition-Partner-Workshops/otterworks, add the React screens for the newly
extracted rating module under frontend/client-app/src/features/billing/,
following how the plans screens are built: a usage/rating view for a tenant and
a period, driven only by the billing service's API.

Requirements: every request has error handling and a visible dismissible error
alert, loading state starts true and empty-state copy never shows while loading
or after an error, stale responses from a previous tenant or period are never
painted, inputs have labels, and errors are associated with their field. Add
React Testing Library tests with the API mocked, including the failure paths.

No business rule may live in the client — if a number needs computing, the
service computes it.
```

Then harden what the extraction produced, which is safe to do precisely because
behavior is pinned:

```
Harden the billing service in Cognition-Partner-Workshops/otterworks:

- one test per approved rule, tagged with its rule id, so a future refactor
  cannot silently drop a rule the ledger says exists
- make the linters, the type checks, the service tests, the harness tests and
  the client suite clean
- re-run make procs-rules-gate ALL=1 and make procs-parity after every change

Report anything you could not fix without changing behavior, rather than
changing behavior.
```

<a id="always-on"></a>
### Always-on: scheduled and event-driven

The same procedure runs unattended.

**Scheduled** — a recurring parity sweep, so drift is caught by the harness
rather than by a customer:

```
Create a scheduled Devin that runs every weekday at 07:00 UTC against
Cognition-Partner-Workshops/otterworks:

Run `make procs-up NS=nightly && make procs-rules-gate ALL=1 && make
procs-parity NS=nightly && make procs-down NS=nightly`. If everything passes,
post a one-line summary. If anything fails, open an issue with the failing
scenarios, the field-level diffs from procs/reports/parity.md, and the commits
merged since the last green run.
```

**Event-driven** — an [Automation](https://docs.devin.ai/product-guides/automations)
that reacts instead of waiting:

- *On CI failure* → start a session that reads `procs/reports/parity.json` from the failed job, reproduces the failure locally, fixes the service, and opens a PR. Transcripts are immutable, so an automated fix cannot paper over a behavior change.
- *On a commit touching `services/legacy-billing/db/procs/`* → the procedures moved, so `SOURCE_SHA` no longer matches and parity fails closed. A commit touching `services/legacy-billing/db/schema.sql` or `seed.sql` likewise changes `FIXTURE_SHA`. The automation re-records against the changed procedures or fixtures, replays, and opens a PR reporting exactly which recorded behaviors changed — which is also the review a DBA's "small procedure tweak" never gets today.

---

<a id="confidence"></a>
## Confidence = Programmatic Verification

Nothing in this thread asks anyone to eyeball a diff and decide whether an
extraction is correct.

| Claim | What proves it |
|---|---|
| "A human approved these rules" | `make procs-rules-gate` — every rule has a decision, a reviewer, a date, and an answer to every question raised, or the build fails |
| "The rules that were approved are the rules that got implemented" | every rule id appears on a `@pytest.mark.rule(...)` test, and the gate rejects a marker for a rule no ledger contains |
| "The service behaves like the procedures" | `make procs-parity` — every recorded scenario replays with identical business fields *and* identical resulting state |
| "The recordings are still about these procedures and fixtures" | per-transcript and global `SOURCE_SHA` checks for procedure logic plus `FIXTURE_SHA` checks for `schema.sql` and `seed.sql`; either mismatch fails rather than silently re-recording |
| "An unextracted module is not quietly passing" | pending modules report `SKIP`, and a selection that matches no transcripts exits non-zero instead of reporting an empty green run |
| "The logic actually left the database" | the repository layer holds no `CASE` or business `ORDER BY`; the domain tests run without a database |
| "Nothing regressed" | CI on every PR: lint → service tests → harness tests → client suite → rules gate → stack up → parity, with the report uploaded |

The failure mode this eliminates is the one that actually happens when teams
rewrite database logic: a service that passes review, passes its own tests, and
is subtly wrong about a rounding direction or an ordering — because that rule
was never written down anywhere except in the SQL.

---

<a id="run-artifact"></a>
## Run the Produced Artifact

```bash
make procs-up NS=demo
curl -s localhost:8944/health    # {"service":"legacy-billing","status":"UP"}
curl -s localhost:12944/health  # {"status":"healthy","service":"billing-service"}
make procs-parity NS=demo
```

Then run the React client against it:

```bash
cd frontend/client-app && npm install && npm run dev
```

Open the pieces that show the result rather than describe it:

| Open | What it proves |
|---|---|
| `procs/reports/parity.md` (and the CI job summary) | scenario-by-scenario PASS/FAIL/SKIP with field-level diffs — the extraction's scoreboard |
| `procs/rules/<module>.rules.yaml` | the approved rules, each with its source lines, the reviewer, the date, and the answered questions — the audit trail an auditor or a regulator would ask for |
| The billing screens in the running client | the same business outcomes the legacy Jinja pages showed, now served by an API |
| The legacy app on its own port | the before-state still running, unchanged, for side-by-side comparison |
| `/docs` on the billing service | the procedures are now a documented API contract |
| The PR, its CI checks, and Devin Review's comments | the whole loop re-run by a machine on a diff a human can review |

---

<a id="concurrent"></a>
## Concurrent Runs

Every target is namespaced, and host ports are derived from the namespace name,
so runs do not collide:

```bash
make procs-up NS=alice
make procs-up NS=bob
```

Each namespace gets its own legacy database, its own target database, its own
app instances, and its own volumes, all bound to `127.0.0.1`. To revert a run
completely:

```bash
make procs-down NS=alice
```

`main` stays the durable before-state — the extraction lives on a branch and in
a disposable namespace, so the demo repeats cleanly.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **The running database is the source of truth.** Not the schema comments, not
  the wiki, not a plausible reading of the SQL. Rules accreted in procedures over
  years, and users depend on them whether or not anyone intended it.
- **A rule does not exist until a human approves it.** Devin's job is to derive
  the rules *and surface the ambiguities*; the reviewer's job is to decide them.
  The gate refuses to build on a guess, and the ledger is the audit trail.
- **Verification is programmatic, not visual.** An extraction is done when the
  recorded legacy behavior replays green — not when the new code looks right.
- **A caught divergence is the point.** Right values in the wrong order passed
  review and passed unit tests; only the recording caught it. Rounding, date
  boundaries, ordering, and the sequence in which credits and caps apply are
  where ports diverge.
- **Getting the logic out is the whole objective.** Rules in plain code with
  database-free tests, a data layer with no rules in it, and a React client that
  computes nothing — that is what makes the estate maintainable afterwards.
- **The procedure is codified once and reused.** A playbook invoked by `!macro`
  plus a repo Skill means every session, and every child in a fan-out, follows
  the same steps and the same two gates.
- **Isolation makes parallelism safe.** Each session and child gets its own VM,
  scoped credentials, namespace, and branch, so a wave of extractions runs
  concurrently and each lands as its own verified PR.
- **Then it runs unattended.** The same loop on a schedule catches drift, and on
  a commit to the procedures it reports exactly which recorded behaviors moved.
