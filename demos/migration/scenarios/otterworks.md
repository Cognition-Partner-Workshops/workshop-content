# Running a Modernization Engagement on a Live Polyglot Estate — OtterWorks

[App Modernization — Delivery Phases](app-modernization-delivery-phases.md)
describes the six phases of an SI / professional-services modernization
engagement in the abstract. This is the same engagement run concretely, on one
real estate, against a system that is already serving users.

The estate is **OtterWorks** — a collaborative file storage and document
editing platform: eleven deployable backend services across nine languages,
plus the separate `legacy-portal` application and two frontends, on EKS, with
CI, contract tests, observability, and runbooks
already in place. The running system is at
**[t-main.otterworks.app](https://t-main.otterworks.app)**. That constraint is
the whole point of the thread: the engagement has to inventory the estate, plan
waves, convert services, and stabilize *without the live system going down*.

| Delivery phase | What it is on this estate | Evidence it produced |
|---|---|---|
| **1. Discovery & assessment** | Inventory eleven deployable backend services plus the separate `legacy-portal` application across nine languages, classify each one replatform / refactor / rewrite / retain, and baseline the quality gates that already exist | `analysis/ESTATE_INVENTORY.md`, `MODERNIZATION_STRATEGY.md`, `GATE_BASELINE.md` |
| **2. Architecture & wave planning** | Sequence the estate into waves by dependency and blast radius, with the risk to the live system priced per wave | `analysis/WAVE_PLAN.md`, `analysis/RISK_REGISTER.md` |
| **3. Foundation** | Already built and reused, not rebuilt: `.github/workflows/ci.yml`, `tests/api/`, `tests/contract/`, `make test-coverage`, and per-branch ephemeral tenants from `cd-tenant.yml` | A tenant URL per branch, CI green per service |
| **4. Iterative conversion** | One wave executed: the wave anchor live, the remaining items fanned out to child sessions in parallel | One PR per unit, each with its own tenant and CI run |
| **5. Validation & quality gate** | Contract tests, API flow tests, coverage deltas, and `make security-scan` run per unit rather than once at the end | `analysis/STABILIZATION_REPORT.md` |
| **6. Cutover & stabilization** | Chaos rehearsal on a throwaway tenant, alert → incident → session automation, runbooks in `docs/runbooks/` | An incident with a fix PR attached |

The engagement lead's problem is not that any one of these is hard. It is that
there are eleven deployable backend services plus a separate legacy application
across nine languages, and users are on the system today.

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [The Live System, and What "Don't Break It" Means](#live-system)
- [Phase 1 — Discovery and Assessment](#phase-1)
- [Phase 2 — Wave Planning](#phase-2)
- [Phase 3 — The Foundation Already Exists](#phase-3)
- [Phase 4 — Execute Wave 1](#phase-4)
  - [The wave anchor, live](#anchor)
  - [Fan out the rest of the wave](#fan-out)
- [Phase 5 — Validation and Quality Gates](#phase-5)
- [Phase 6 — Stabilization](#phase-6)
- [What the Engagement Lead Reports](#reporting)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

The live system, in a browser:

```bash
open https://t-main.otterworks.app          # user SPA
open https://t-main.otterworks.app/admin    # admin dashboard
curl -s https://t-main.otterworks.app/api/health
# {"status":"healthy","service":"web-app"}
```

The estate and its gates, locally:

```bash
git clone https://github.com/Cognition-Partner-Workshops/otterworks
cd otterworks

make up            # LocalStack + Postgres + Redis + the services
make seed          # synthetic tenant data
make test          # per-service unit tests across nine languages
make test-coverage # the same, with coverage reports
make lint          # per-service linters
make security-scan # Semgrep / Trivy / dependency scanning
```

`make help` lists every target. The API-level gates are Python:

```bash
pip install -r tests/api/requirements.txt
make test-api-flows                       # tests/api/ — 10 cross-service flows
pytest tests/contract/ -v                 # OpenAPI contract validation
```

---

<a id="repositories"></a>
## Repositories

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — the
  estate. Eleven deployable backend service directories under `services/`
  (`api-gateway` Go 1.22,
  `auth-service` Java 17 / Spring Boot 3.2, `file-service` Rust / Actix,
  `document-service` Python 3.12 / FastAPI, `collab-service` Node 20 / Express,
  `notification-service` Kotlin / Ktor, `search-service` Python 3.12 / Flask,
  `analytics-service` Scala 3 / Akka HTTP, `admin-service` Ruby 3.3 / Rails 7.1,
  `audit-service` C# / .NET 8, `report-service` Java 8 / Spring Boot 2.5, and
  `legacy-portal` Java 11 / Spring Boot 2.7), plus `frontend/client-app`
  (React 18 + Vite) and `frontend/admin-dashboard` (Angular 17). Also
  `infrastructure/terraform`, `infrastructure/helm`, `observability/`,
  `demo-platform/`, `docs/runbooks/`, and the workflows under
  `.github/workflows/`.
- [workshop-content](https://github.com/Cognition-Partner-Workshops/workshop-content)
  — this repo. The hands-on modules under `workshops/otterworks/` cover the
  individual units of work this engagement sequences: `A1-etl-modernization.md`,
  `A2-framework-upgrade.md`, `A3-language-translation.md`,
  `B1-investigate-incident.md`, `B2-complete-runbooks.md`,
  `B3-add-observability.md`, `C1-security-sprint.md`, `C2-contract-audit.md`,
  `C3-test-coverage.md`.

---

<a id="live-system"></a>
## The Live System, and What "Don't Break It" Means

`t-main` is the perpetual tenant. It tracks `main`, it is the environment
everyone shares, and it never receives chaos injection or destructive changes.
It is the engagement's production stand-in.

Every other environment is disposable and comes from a branch name.
`.github/workflows/cd-tenant.yml` deploys `workshop-<id>` and `demo-<id>`
branches to their own tenant at `t-<id>.demo.otterworks.app`, with their own
namespace, database, hostname, and image tag. It builds only the services the
push actually changed and pulls the golden image for everything else, and it
deploys through the ops dashboard API rather than straight at the cluster.

That gives the engagement the property it needs:

| | `t-main` | `t-<id>.demo.otterworks.app` |
|---|---|---|
| Source | `main` | `demo-<id>` / `workshop-<id>` branch |
| Lifetime | perpetual | disposable, reaped when idle |
| Chaos injection | never | this is where it belongs |
| Role in the engagement | the system the client is using | where each conversion is proven |

So every conversion in this thread is proven on its own tenant URL before it
goes anywhere near `main`. Two caveats worth knowing before you plan around
them: tenant SNS/SQS eventing is off by default, so event-driven notification
and search paths can be inert on an ephemeral tenant, and `admin-service` has
a known boot defect (below) that is part of the estate's before-state.

---

<a id="phase-1"></a>
## Phase 1 — Discovery and Assessment

The engagement cannot be scoped until someone has read nine languages' worth of
code. This is the phase that normally consumes the first two weeks and produces
a slide rather than an artifact.

```
Using the Cognition-Partner-Workshops/otterworks repo, produce a discovery
package for a modernization engagement. Work from the actual files — package
manifests, Dockerfiles, workflow files — and cite paths for every claim.

Write three files under analysis/:

1. ESTATE_INVENTORY.md — one row per directory under services/ plus
   frontend/client-app and frontend/admin-dashboard: language and runtime
   version, framework and version, source lines, its dependencies on other
   services, the datastores it touches, and whether it has a CI job in
   .github/workflows/ci.yml.

2. MODERNIZATION_STRATEGY.md — classify each component as replatform,
   refactor, rewrite, or retain, with the reason and a low/medium/high
   effort estimate. Call out the components running an end-of-life or
   unpinned runtime and name the specific version evidence.

3. GATE_BASELINE.md — for every quality gate that already exists, record
   what it covers and where it is weak: the change-gated jobs in
   .github/workflows/ci.yml, the per-service targets behind
   `make test`, `make test-coverage`, `make lint` and `make security-scan`,
   the flows in tests/api/, the contract tests in tests/contract/, and the
   event schemas in shared/events/schemas/. Flag any gate that is
   configured so that it cannot fail.

Report only what the code shows. Where a path referenced by tooling does not
exist, say so and give the file and line.
```

This estate has real findings waiting, and a discovery pass that reports them
with evidence is the difference between a scope estimate and a guess:

| Finding | Where |
|---|---|
| `report-service` runs **Java 8 on Spring Boot 2.5.14** — both out of support — while `auth-service` next to it is Java 17 / Boot 3.2.4. The estate is not uniformly behind; it is unevenly behind | `services/report-service/pom.xml` vs `services/auth-service/build.gradle` |
| `search-service` is the only Python service still on **Flask**, while `document-service` is FastAPI. Two Python idioms to maintain, one of them the older one | `services/search-service/`, `services/document-service/` |
| The Rust build is on **unpinned `rust:latest`** — the build is not reproducible, which matters more to a delivery timeline than to a developer | `services/file-service/Dockerfile` |
| The **admin dashboard's lint and test steps are suffixed `\|\| true`** — a gate that is configured never to fail. The engagement was counting it as coverage | `.github/workflows/ci.yml` |
| Three Makefile targets reference **`frontend/web-app`**, which does not exist; the real directory is `frontend/client-app`. Frontend targets silently do nothing | `Makefile:107`, `Makefile:125`, `Makefile:150` |
| `admin-service` fails to boot in production: `ActiveSupport::TaggedLogging.logger($stdout)` is not the Rails 7.1 factory API | `services/admin-service/config/environments/production.rb:8` |
| The notification event schema requires `notificationId` while the audit of consumer expectations in `docs/labs/contract-audit-guide.md` documents drift against it | `shared/events/schemas/notification-events.json` |
| `tests/contract/` contains exactly one contract test — `test_search_contract.py`. Eleven services, one contract gate | `tests/contract/` |

The last three rows are the ones that change a plan. A gate that cannot fail
and a Makefile target that does nothing both look like coverage on a status
report, and both are discovered by reading files rather than by asking the
client's team what their coverage is.

> Commit the accepted discovery package to the repo, or capture it as a
> Knowledge note. Every later session in the engagement — including the
> children in the fan-out — then starts from the same agreed inventory instead
> of re-deriving it, and the 20th conversion typically starts faster than the
> first.

---

<a id="phase-2"></a>
## Phase 2 — Wave Planning

Discovery says what is wrong. Planning decides the order — and on a live estate
the ordering constraint is not just dependency direction, it is blast radius.
`api-gateway` sits in front of everything; `report-service` sits behind a
report button.

```
Using analysis/ESTATE_INVENTORY.md and analysis/MODERNIZATION_STRATEGY.md in
Cognition-Partner-Workshops/otterworks, produce the delivery plan.

Write analysis/WAVE_PLAN.md: group the work into three waves, each wave a set
of units that can run in parallel. For every unit give the component, the
strategy, the effort estimate, the services that depend on it (from the
inventory), and the gates that must be green before it merges. Order the waves
so that the lowest blast radius goes first: components nothing else calls,
then components with contract-covered consumers, then shared-path components
like api-gateway and auth-service.

Write analysis/RISK_REGISTER.md: for each unit, what breaks on the live system
at t-main.otterworks.app if the change is wrong, how it would be detected
(name the CI job, test file, or Grafana dashboard under observability/), and
how it would be rolled back. Explicitly identify the units that cannot be
verified on an ephemeral tenant because tenant SNS/SQS eventing is disabled by
default.

Do not propose changes to main or to t-main. Every unit is verified on its own
demo-<id> tenant first.
```

Wave 1 comes out of this looking like the units that touch nothing else:
`report-service` (nothing calls it), `search-service` (contract-covered),
the cron ETL scripts, and the `admin-service` boot defect. Waves 2 and 3 are
where `api-gateway` and `auth-service` live, and they wait until the harness
around them is stronger — which is the argument for running the coverage and
contract work of Wave 1 first rather than treating it as cleanup.

This is the artifact the engagement lead maps to sprints. The architect
validates the sequencing; the plan is an input to that decision, not a
substitute for it.

---

<a id="phase-3"></a>
## Phase 3 — The Foundation Already Exists

On a greenfield target the foundation phase builds scaffolds, pipelines, and
harnesses. On this estate most of it is already there, and the phase's real job
is to *find that out* — because rebuilding a harness that exists is the most
common way an engagement burns its first sprint.

What Wave 1 inherits:

| Foundation asset | Where | What it gives the engagement |
|---|---|---|
| Change-gated CI | `.github/workflows/ci.yml` | A `detect-changes` job routes to per-service jobs, so a one-service PR runs one service's build, lint, and tests |
| Per-branch environments | `.github/workflows/cd-tenant.yml` | `demo-<id>` → `t-<id>.demo.otterworks.app`, rebuilding only changed services |
| Cross-service flow tests | `tests/api/` | 10 flow suites — auth, file, document, collaboration, search, audit/analytics/report, side effects, degradation, WebSocket presence |
| Contract validation | `tests/contract/` | OpenAPI conformance, currently for search only — a gate to extend, not to invent |
| Coverage | `make test-coverage` | A per-service coverage baseline to measure a delta against |
| Security | `make security-scan`, `.github/workflows/security-scan.yml`, `.github/workflows/sast-auto-remediate.yml` | Scanning plus an event-driven remediation path |
| Observability | `observability/` | Grafana dashboards (`incident-overview.json`, `chaos-scenarios.json`, `service-detail.json`, and more), Prometheus alerts, Jaeger, OTel collector, Fluent Bit |
| Runbooks | `docs/runbooks/` | Seven incident runbooks keyed to the failure modes the estate can actually exhibit |
| Repo context | `.devin/wiki.json`, `.agents/skills/`, `.workshop/playbooks/` | The estate's own conventions, loaded automatically by any session working in it |

The gaps discovery found are the foundation work Wave 1 actually needs: the
`|| true` gate, the one-service contract suite, the dead Makefile targets. That
is a day of work, not a sprint of scaffolding.

---

<a id="phase-4"></a>
## Phase 4 — Execute Wave 1

<a id="anchor"></a>
### The wave anchor, live

`report-service` is the anchor: end-of-life runtime, nothing depends on it,
and it exercises the whole loop — branch, tenant, gates, PR. Java 8 / Spring
Boot 2.5.14 → Java 17 / Spring Boot 3.2+, which means the `javax.*` →
`jakarta.*` namespace migration, the Boot 3 configuration property renames,
and the dependency floor that comes with them.

```
Work in Cognition-Partner-Workshops/otterworks on a new branch named
demo-report-upgrade so that .github/workflows/cd-tenant.yml deploys it to
t-report-upgrade.demo.otterworks.app. Do not touch main.

Upgrade services/report-service from Java 8 / Spring Boot 2.5.14 to Java 17 /
Spring Boot 3.2.x:

- update pom.xml: java.version, the spring-boot-starter-parent version, and
  any dependency whose current version is incompatible with Boot 3
- migrate every javax.* import that Boot 3 moved to jakarta.*
- apply the Boot 3 configuration property renames in
  services/report-service/src/main/resources/
- update services/report-service/Dockerfile to a Java 17 base image
- update the report-service job in .github/workflows/ci.yml to build on 17

Then prove it:
- `cd services/report-service && mvn verify` passes
- `make test-report` passes
- `make test-api-flows` — the report assertions in
  tests/api/test_audit_analytics_report_flow.py still pass, including report
  generation, download, and the terminal state
- the CI run on the branch is green

Write MIGRATION_NOTES.md in services/report-service/ recording each breaking
change you hit, the file it was in, and why the fix is behavior-preserving.
Flag anything you could not change without altering behavior instead of
altering behavior.
```

When the branch deploys, the result is a URL. Open
`t-report-upgrade.demo.otterworks.app`, generate a report in the UI, and
download it — the same flow the API tests assert, seen in a browser. Then open
`t-main.otterworks.app` alongside it: unchanged, on the golden image, serving
users. That side-by-side is what the engagement lead shows a governance board.

The PR is where the loop closes. CI runs the report-service job on the diff,
Devin Review comments on it like any other reviewer, and the review feedback is
handled in the same session that produced the change.

<a id="fan-out"></a>
### Fan out the rest of the wave

The rest of Wave 1 is independent by construction — that is what the wave
planning was for — so it runs in parallel rather than in series. One child
session per unit, each on its own branch, its own tenant, its own PR.

```
Fan out the rest of Wave 1 from analysis/WAVE_PLAN.md in
Cognition-Partner-Workshops/otterworks. Spawn one child session per unit,
each on its own branch so cd-tenant.yml gives it its own tenant, and each
following the corresponding module in
Cognition-Partner-Workshops/workshop-content under workshops/otterworks/:

1. branch demo-search-fastapi — translate services/search-service from Flask
   to FastAPI per A3-language-translation.md. Done when
   `pytest tests/contract/test_search_contract.py -v` is green against the
   running service and the search flows in tests/api/test_search_flow.py
   pass. The OpenAPI contract is the source of truth; do not edit it to
   match the implementation.

2. branch demo-contract-audit — audit the event schemas in
   shared/events/schemas/ and the OpenAPI specs in shared/openapi/ against
   the implementations per C2-contract-audit.md, starting with the
   notificationId field in notification-events.json. Fix the drift on the
   implementation side and add a contract test under tests/contract/ that
   fails if it recurs.

3. branch demo-admin-boot — fix the Rails 7.1 logger defect at
   services/admin-service/config/environments/production.rb:8 so the
   service boots in production configuration, and add a test that covers
   production boot per C3-test-coverage.md.

4. branch demo-coverage-blitz — run `make test-coverage`, identify the three
   services with the weakest coverage, and add meaningful tests to them per
   C3-test-coverage.md. Report the before and after coverage number per
   service.

No child may modify main, deploy to t-main, run scripts/inject-bug.sh, or
weaken a gate to make it pass — including removing an assertion or adding
`|| true`. A child is done when its CI run is green, its tenant serves
traffic, and its change is ready for review with the gate output quoted in the
description.
Monitor them and report each child's status, the gate results, and anything
it escalated.
```

Each child runs on its own machine with its own branch and its own tenant, so
nothing shares a namespace, a database, or a hostname — which is what makes
four conversions at once safe rather than four conversions at once colliding.
The engagement's effort shifts from writing four upgrades to reviewing four
PRs, and the review bar is the same one applied four times.

---

<a id="phase-5"></a>
## Phase 5 — Validation and Quality Gates

Validation is not a phase gate at the end of this engagement; it is the
condition each unit had to satisfy to merge. What is left is aggregation — the
evidence the client's governance board sees.

```
In Cognition-Partner-Workshops/otterworks, produce the Wave 1 quality
evidence. Write analysis/WAVE1_VALIDATION.md containing:

- per merged unit: the PR, the CI jobs that ran, and the gate results
- `make test-coverage` per service, before and after Wave 1, as a delta table
- `pytest tests/contract/ -v` results, noting which services now have
  contract coverage and which still do not
- `make test-api-flows` results across the 10 suites in tests/api/
- `make security-scan` findings, grouped by severity, with the ones Wave 1
  closed marked
- the gates that were weak at baseline per analysis/GATE_BASELINE.md and
  their state now — specifically the `|| true` steps in ci.yml and the
  Makefile targets pointing at frontend/web-app

For every number, include the command that produces it so a reviewer can
re-run it. Where a gate is still weak, say so rather than reporting the
number it would produce if it were not.
```

> Run this on a schedule rather than on request. A recurring session that
> re-runs the gates against `main` and posts the delta keeps the engagement's
> status report generated rather than assembled, and turns "quality is
> improving" into a table with commands attached.

---

<a id="phase-6"></a>
## Phase 6 — Stabilization

Cutover on this estate is a merge to `main`, which redeploys `t-main`. The risk
window is what happens after. Stabilization is rehearsed before it is needed —
on a throwaway tenant, never on `t-main`.

The rehearsal uses the estate's own chaos harness. `scripts/inject-bug.sh` and
`scripts/bug-catalog.yaml` define six scenarios — `file-upload-fails`,
`search-suggest-500`, `document-slow`, `notification-schema`,
`file-bad-bucket`, and `code-variant` — each mapping to a runbook in
`docs/runbooks/` and a panel in
`observability/grafana/dashboards/incident-overview.json`.

```
Rehearse post-cutover incident response in
Cognition-Partner-Workshops/otterworks on a disposable tenant only. Create
branch demo-stabilization; never target main and never inject anything into
t-main.

Inject the search-suggest-500 scenario from scripts/bug-catalog.yaml with
scripts/inject-bug.sh against that tenant, then respond to it as an on-call
engineer would:

1. Reproduce the failure through the tenant's own URL and record the
   request and the response
2. Investigate using what the estate already emits — the Prometheus alert in
   observability/prometheus/alerts.yml that fires, the incident-overview and
   chaos-scenarios Grafana dashboards, service logs, and traces
3. Follow docs/runbooks/search-suggest-500.md, and where the runbook is
   thin or wrong, fix the runbook as part of the work
4. Fix the defect, then verify with
   `pytest tests/contract/test_search_contract.py -v` and the suggest
   assertions in tests/api/test_search_flow.py
5. Write analysis/INCIDENT_REPORT.md: timeline, the signal that detected it,
   root cause with file and line, the fix, and the gate that would have
   caught it earlier

Report which signals were sufficient to diagnose the failure and which
services would not have produced them, referencing observability/.
```

The estate is already wired to start that session without a human. A Prometheus
alert fires, the alert reaches a webhook, the webhook opens an incident in
`admin-service`, and the incident can start a Devin session on it. Combined
with `.github/workflows/sast-auto-remediate.yml`, which turns a security finding
into a remediation session, the stabilization window has a responder that is
already in context rather than one who has to context-switch into it.

Two things are worth naming honestly here: an automated responder is scoped to
propose a PR, not to deploy one, and the estate's weakest services are the ones
that emit the least — which is exactly why the observability work
(`B3-add-observability.md`) belongs in a wave rather than in a backlog.

---

<a id="reporting"></a>
## What the Engagement Lead Reports

Every phase in this thread ends in something a client can read, and every
number in it has a command behind it:

| Phase | Artifact | Backed by |
|---|---|---|
| Discovery | `ESTATE_INVENTORY.md`, `MODERNIZATION_STRATEGY.md`, `GATE_BASELINE.md` | File-level citations from the repo |
| Planning | `WAVE_PLAN.md`, `RISK_REGISTER.md` | The inventory's dependency data |
| Conversion | One PR per unit, one tenant URL per unit | The branch's CI run and its live tenant |
| Validation | `WAVE1_VALIDATION.md` | `make test`, `make test-coverage`, `make test-api-flows`, `pytest tests/contract/`, `make security-scan` |
| Stabilization | `INCIDENT_REPORT.md`, updated `docs/runbooks/` | A rehearsed incident on a disposable tenant |

And the thing the board actually asks about is a URL:
`t-main.otterworks.app` served traffic throughout, while each conversion was
proven on its own hostname first.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **The engagement's bottleneck is capacity, and it shows up per language.**
  Eleven deployable backend services plus a separate legacy application across
  nine languages means many upgrade paths, linters, and test idioms. That is the
  work that scales with the
  portfolio, and it is the work that parallelizes.
- **Discovery is worth doing against files, not interviews.** A gate suffixed
  `|| true`, three Makefile targets pointing at a directory that does not
  exist, and a service that cannot boot in production configuration are all
  discoverable by reading the repo — and all of them would otherwise be
  counted as coverage on a status report.
- **Per-branch environments are what make a live estate safe to modernize.**
  Each unit of work gets its own tenant and hostname; the perpetual
  environment stays on the golden image. "Verified" means verified somewhere
  a client can click, not verified in a diff.
- **Wave order is blast radius, not just dependency order.** The units nothing
  calls go first, and the shared paths wait until the contract and coverage
  gates around them are stronger.
- **Validation aggregates rather than gates.** Each unit merged only with its
  gates green, so the phase-5 report is a table of results with commands
  attached — evidence the governance board can re-run.
- **Stabilization is rehearsed on a disposable environment.** Chaos injection,
  runbook execution, and the alert → incident → session path are exercised
  before cutover, and the runbook gets fixed while someone is using it.
- **Human judgment stays where it belongs.** The architect validates the wave
  sequencing, tech leads review the PRs, and the engagement lead owns scope
  and the client relationship. The conversions underneath them are the
  part that does not need to consume a sprint each.
