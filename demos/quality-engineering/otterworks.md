# Quality Engineering on a Polyglot Estate — OtterWorks Demo

A single linear demo for quality engineers, SDETs, and test architects: measure
test coverage across eleven backend services in nine languages, pick the weakest
one on evidence rather than instinct, have Devin write meaningful tests in that
service's own framework, then push quality outward — cross-service API-flow
tests that mirror the flows a real user performs on the live system, and a
contract audit that compares published OpenAPI and event schemas against what
the services actually emit.

The through-line is that quality work on a polyglot estate is *bounded by
language breadth and reading time*, not by test-writing skill. Devin reads Rust,
Kotlin, Scala, Ruby, C#, Go, Java, Python, and TypeScript, so the estate can be
measured and improved in one pass — and the improvement is measured against a
recorded baseline, not asserted.

The live system is at [https://t-main.otterworks.app](https://t-main.otterworks.app)
(user SPA at `/`, admin dashboard at `/admin`, health at `/api/health`). The
API-flow tests in `tests/api/` mirror the flows you can click through there, so
"what the tests assert" and "what a user does" stay the same thing.

## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Runs the Quality Pass](#part-1)
  - [Act 1 — Measure the estate](#act-1)
  - [Act 2 — Strengthen the weakest service](#act-2)
  - [Act 3 — Cross-service API flows](#act-3)
  - [Act 4 — Contract audit](#act-4)
  - [Act 5 — Fan out one child session per weak service](#act-5)
- [Part 2 — Run the Produced Artifacts](#part-2)
- [Confirming Completion](#confirming-completion)
- [Where This Goes Next](#going-next)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Open the live system first, then bring up the local stack that the test targets
run against:

```bash
# The live golden app — click a login, a document, a search
open https://t-main.otterworks.app
curl -s https://t-main.otterworks.app/api/health

# Local stack for the verification loop
make up seed=1              # start services and seed development data
make test-coverage          # per-service coverage across the estate
make test-api-flows         # black-box API flow tests via the gateway
```

`make test-coverage` runs each service's native coverage tool (pytest-cov, Jest,
`go test -cover`, Jacoco, `cargo test`, RSpec) and does not stop on failure, so
one broken toolchain never hides the rest of the report. `make test-api-flows`
runs `tests/api/` against `OTTERWORKS_API_BASE_URL`, defaulting to
`http://localhost:8080`.

`t-main` tracks `main` and is the shared reference environment — read from it,
never change it.

---

<a id="repository"></a>
## Repository

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — a
  polyglot collaborative file-storage and document-editing platform. Eleven
  backend services live under `services/` (api-gateway in Go, auth-service in Java
  17 / Spring Boot 3, file-service in Rust, document-service in Python /
  FastAPI, collab-service in Node / Yjs, notification-service in Kotlin,
  search-service in Python / Flask, analytics-service in Scala, admin-service
  in Ruby on Rails, audit-service in C#, and report-service on an intentionally
  legacy Java 8 / Spring Boot 2.5 stack), alongside a `legacy-portal` module, a
  React `frontend/client-app`, and an Angular `frontend/admin-dashboard`.

The quality surfaces this demo uses:

| Path | What it is |
|---|---|
| `Makefile` | `test-coverage`, `test-api-flows`, `test-api-flows-collect`, `up`, `seed`, `lint`, `security-scan` |
| `tests/api/` | 10 black-box flow modules (auth, document, file, search, collaboration, notification/admin/gateway, audit/analytics/report, side effects, WebSocket, degradation) with a shared `conftest.py` client |
| `tests/contract/` | `test_search_contract.py` — validates a running search-service against `shared/openapi/search-service.yaml` |
| `shared/openapi/` | OpenAPI 3.0 specs for search-service, document-service, notification-service |
| `shared/events/schemas/` | JSON Schema event contracts for document, file, notification, audit, and collaboration events |
| `docs/api-route-matrix.md` | Gateway route inventory with current API-test coverage per flow |
| `docs/labs/contract-audit-guide.md` | Documented drift areas across specs and implementations |

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Tests | Contracts |
|---|---|---|
| **Before** | Coverage varies widely by service; some have several test files, others rely on a handful of inline `#[cfg(test)]` modules. No single recorded baseline exists. | `shared/openapi/` and `shared/events/schemas/` are published as the source of truth, but parts have drifted from the code |
| **After** | A recorded coverage baseline, new tests in the weakest service written in that service's own framework, and a green run of the cross-service API flows | An audit report naming each divergence, the direction chosen to resolve it, and a contract test that keeps it from recurring |

The verification loop is the point. Every claim in this demo is produced by a
command that anyone watching can re-run: `make test-coverage` for the baseline
and the improvement, `make test-api-flows` for the cross-service behavior, and
`pytest tests/contract/` for the specs. The goal is a *measurable* delta on the
service that needed it — not a coverage number pushed to 100% by asserting that
functions exist.

---

<a id="part-1"></a>
## Part 1 — Devin Runs the Quality Pass

<a id="act-1"></a>
### Act 1 — Measure the estate

Start with evidence. Devin runs the coverage target across every service and
turns raw output into a ranked baseline that names the untested critical paths,
not just the percentages.

```
Using the Cognition-Partner-Workshops/otterworks repo on main, measure
test coverage across the whole estate and record a baseline.

Run `make test-coverage` from the repo root. That target runs each
service's native coverage tool and continues past failures, so capture
both the coverage numbers and any service whose toolchain did not run.

Then write docs/quality/coverage-baseline.md containing:
1. A table of every backend service under services/ with: language,
   test framework, test command, line coverage if reported, number of
   test files or inline test modules, and whether the run succeeded.
2. A ranked "weakest first" list of the three services with the largest
   gap between behavior that matters and behavior under test. Rank on
   untested critical paths (request handlers, persistence, event
   publishing, error branches), not on percentage alone.
3. For the weakest service, a specific list of untested code paths with
   file and function names.

Note explicitly where coverage could not be measured and why. Do not
change any service code in this step.
```

Expected: a baseline table covering the services under `services/`, the
uninstrumented paths called out by name, and a ranked shortlist. Services with
few dedicated test files — file-service (Rust, tests live inline in
`src/handlers.rs`, `src/metadata.rs`, and `src/events.rs`) and collab-service
(Node) among them — typically surface near the top, but the ranking comes from
the run, not from this document.

<a id="act-2"></a>
### Act 2 — Strengthen the weakest service

Now improve the service the measurement chose, in that service's own framework.
The instruction that matters is the one that rules out vanity coverage.

```
Using the ranking in docs/quality/coverage-baseline.md in the
Cognition-Partner-Workshops/otterworks repo, add meaningful tests to the
weakest service.

Rules:
- Write tests in that service's existing framework and file layout
  (Rust #[cfg(test)] modules in src/, Jest for Node, pytest for Python,
  JUnit/Kotest for the JVM services, RSpec for Ruby, xUnit for C#).
  Match the naming and structure of the tests already there.
- Cover real behavior: success and failure branches of request handlers,
  persistence and metadata logic, event publishing shapes, and boundary
  or error conditions. No tests that only assert a function or route
  exists, and no assertions weakened to make a run pass.
- Add 5 or more such tests.

Run that service's test command until it is green, then re-run
`make test-coverage` and report the before and after numbers for the
service, quoting both runs. Update docs/quality/coverage-baseline.md
with an "After" column and a short note on which critical paths are now
covered and which remain open.
```

Expected: new tests that read like the ones already in the service, a green
run of that service's native command, and a quoted before/after from
`make test-coverage`. If a real defect surfaces while writing the tests, the
fix goes in the code and the test stays as written — the assertion is the
contract, and relaxing it to get green defeats the exercise.

<a id="act-3"></a>
### Act 3 — Cross-service API flows

Unit tests inside one service cannot see the seams between eleven of them. The
`tests/api/` suite drives the gateway exactly as the SPA does, so the flows you
click on `t-main` are the flows under assertion.

```
In the Cognition-Partner-Workshops/otterworks repo, run and extend the
black-box API flow suite.

1. Start the stack with `make up seed=1`, then run `make test-api-flows`
   (pytest over tests/api/ against OTTERWORKS_API_BASE_URL, default
   http://localhost:8080). Report which flow modules pass, fail, or are
   skipped, and why.
2. Read docs/api-route-matrix.md and compare it against the flows the
   suite actually exercises. Identify the highest-value user flow on
   https://t-main.otterworks.app that has no end-to-end coverage today
   (the user SPA at / and the admin dashboard at /admin show the flows
   that matter).
3. Add one new flow module under tests/api/ for that gap, following the
   fixtures and ApiClient helpers in tests/api/conftest.py and the
   markers declared in tests/api/pytest.ini. Assert the cross-service
   result, not just the HTTP status of the first call.
4. Get the suite green and quote the pytest summary line.

Treat https://t-main.otterworks.app as read-only: use it to confirm the
shape of the user flow, and run all tests against the local stack.
```

Expected: a pass/fail/skip breakdown of the 10 existing flow modules, a named
coverage gap justified against `docs/api-route-matrix.md`, and one new module
that asserts a cross-service outcome. Some event-driven paths (notification and
search fan-out) depend on SNS/SQS being enabled, so Devin should report those as
skipped or environment-gated rather than silently passing them.

<a id="act-4"></a>
### Act 4 — Contract audit

The last class of defect no unit test catches: the spec and the implementation
disagree, and every consumer that trusts the spec breaks.

```
In the Cognition-Partner-Workshops/otterworks repo, audit the published
contracts against the implementations.

Read docs/labs/contract-audit-guide.md first — it documents the known
drift areas — but verify each claim against the code rather than
trusting the guide.

Scope:
- shared/events/schemas/notification-events.json against the event
  models and publishing code in
  services/notification-service/src/main/kotlin/com/otterworks/notification/.
  The guide describes a camelCase vs snake_case field mismatch
  (notificationId vs notification_id); confirm what the service actually
  emits, including any required schema field the code never sets.
- shared/events/schemas/audit-events.json against the batch event path
  in services/audit-service/, where timestamp is required by the schema
  but may be omitted per event.
- At least one OpenAPI spec in shared/openapi/ against the running
  service, following the pattern in
  tests/contract/test_search_contract.py.

Write docs/quality/contract-audit.md with one row per divergence:
schema or spec location, code location, what each side says, blast
radius for consumers, and whether the fix belongs in the spec or the
code, with the reason. Apply the fixes you recommend, and add a
contract test under tests/contract/ for at least one divergence so it
cannot silently return. Run the contract tests and quote the results.
```

Expected: an audit table grounded in file and symbol names, the notification
event divergence characterized from the Kotlin models rather than from the
guide's summary, a stated direction for each fix (schema-side changes are
usually the safer default when live consumers already depend on the emitted
shape), and a new test under `tests/contract/` that fails if the drift returns.

<a id="act-5"></a>
### Act 5 — Fan out one child session per weak service

The baseline from Act 1 ranked more than one weak service. Rather than working
the list in series, run one parent session that spawns a child per service. Each
child owns one service, one branch, and one green native test command.

```
Act as the orchestrator for a test-coverage wave across the
Cognition-Partner-Workshops/otterworks repo, using child Devin sessions
to parallelize the work.

Read docs/quality/coverage-baseline.md and take the ranked weakest
services from it. Spawn one child Devin session per service. Give each
child: the repo, the single service it owns, its own branch, and these
instructions — add 5 or more meaningful tests in that service's native
framework following the existing test layout, cover real behavior rather
than existence checks, run the service's own test command until green,
and re-run `make test-coverage` to quote the before and after for that
service.

Constraints for every child:
- One service per child; do not edit another child's service.
- Do not weaken existing assertions to get a green run.
- Report the coverage delta and the critical paths still uncovered.

Monitor the children until each reports a green run, then summarize:
per-service coverage delta, tests added, and any real defects the new
tests exposed.
```

Because each child owns one service and one branch, the runs do not collide, and
the parent gets a single roll-up of the estate-wide delta. The coverage baseline
is the shared context that keeps the children consistent — promote it to a
Knowledge note and the ranking becomes the starting point for every future
quality session instead of something re-derived each time.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifacts

Everything produced above is executable. Re-run it in front of the room.

```bash
# The coverage delta, from the same command that produced the baseline
make test-coverage

# The weakest service's own gate (example: Rust file-service)
cd services/file-service && cargo test

# Cross-service flows through the gateway
make test-api-flows

# Collect-only, to show the flow inventory without a full run
make test-api-flows-collect

# The contract tests, including the new regression test
# (requires pyyaml, jsonschema, requests, pytest)
SEARCH_SERVICE_URL=http://localhost:8087 pytest tests/contract -v
```

Then open the live system and walk the same path the API-flow tests drive —
sign in at [https://t-main.otterworks.app](https://t-main.otterworks.app),
create a document, run a search, check
[https://t-main.otterworks.app/admin](https://t-main.otterworks.app/admin).
The tests and the clicks describe the same system, which is what makes the
suite worth trusting.

---

<a id="confirming-completion"></a>
## Confirming Completion

Walk these in order:

**1. The baseline is real.** Open `docs/quality/coverage-baseline.md`. Every
service under `services/` appears with its language, framework, command, and
result, and the weakest-first ranking cites untested code paths by file and
function. The ranking came from a run, not from a guess.

**2. The improvement is measured.** Show the before and after
`make test-coverage` output for the target service side by side, and open the
new tests to show they assert behavior — error branches, boundaries, event
shapes — rather than existence.

**3. The seams are covered.** Show the `make test-api-flows` summary line with
the new flow module passing, and point at the row in
`docs/api-route-matrix.md` that it closes.

**4. The contracts agree with the code.** Open `docs/quality/contract-audit.md`,
show one divergence with both sides quoted, and run the new test under
`tests/contract/` that now fails if it returns.

**5. The estate moved, not just one service.** Show the parent session's
roll-up from Act 5: per-service coverage deltas from the child sessions, each on
its own branch with its own green gate.

---

<a id="going-next"></a>
## Where This Goes Next

- **Quality gates in CI.** `.github/workflows/ci.yml` already runs on the repo;
  the coverage baseline gives the numbers a gate can be set against, and the new
  contract test belongs on every PR that touches `shared/openapi/` or
  `shared/events/schemas/`.
- **Scheduled coverage sweeps.** A recurring session that re-runs
  `make test-coverage`, diffs against `docs/quality/coverage-baseline.md`, and
  raises the regressions keeps the ranking current without anyone owning the
  chore.
- **Event-driven test authoring.** A merged PR that adds an endpoint without a
  flow test, or a failed CI check, can start a session automatically. See
  [Automations](https://docs.devin.ai/product-guides/automations).
- **Shared context.** Promote the baseline, the audit table, and the
  service-by-service test conventions to Knowledge notes or a playbook, so every
  later session — and every child in the next fan-out — inherits the same
  definition of a meaningful test.
- **The PR feedback loop.** Each unit of work lands as its own PR with a green
  gate and a quoted before/after, reviewable by the engineer who owns that
  service and reviewed automatically by Devin Review.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Measurement comes first.** The weakest service is chosen by
  `make test-coverage` and a recorded baseline, so the improvement can be quoted
  as a delta instead of asserted.
- **Meaningful beats maximal.** Tests target untested critical paths — error
  branches, persistence, event shapes — in each service's own framework. A
  higher percentage bought with existence checks is not the goal.
- **Language breadth stops being the constraint.** Nine backend languages, each
  with its own test framework and idioms, are covered in one pass — the work
  that usually stalls waiting for whoever knows Rust or Scala.
- **Quality lives at three levels.** Unit tests inside a service, API-flow tests
  across the seams, and contract tests between published specs and
  implementations catch three genuinely different classes of defect. The
  notification event schema drift is invisible to the first two.
- **The tests mirror the live system.** The flows asserted in `tests/api/` are
  the flows a user performs on the live app, which is what makes a green suite
  mean something.
- **Fan-out turns a backlog into a wave.** One parent session and a child per
  weak service move the whole estate in parallel, each with its own branch,
  green gate, and reviewable PR.
