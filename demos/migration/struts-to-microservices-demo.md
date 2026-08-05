# Struts → Java Microservices — End-to-End Modernization Demo

Modernize a Struts 1.x monolith into Spring Boot microservices with
**verifiable confidence**: assess the legacy estate, extract a module live,
prove behavior parity by replaying recorded legacy traffic, catch a real
divergence and fix it, fan the remaining modules out in parallel, then harden
what was produced.

The thread follows the three phases of a real modernization program:

| Phase | What Devin does | What proves it |
|---|---|---|
| **1. Analysis / assessment** | Ingests the legacy codebase and produces the groundwork for the modernization plan: dependency and call graphs, Struts action/JSP inventories, business logic vs framework plumbing, service-boundary candidates, dead code and duplication | A reviewable inventory grounded in file-level evidence, compressing discovery an architect would otherwise do by hand |
| **2. Code transformation** | Executes the high-volume conversion at scale: Struts Actions → Spring controllers, form beans → validated DTOs, JSP/tag logic → an API contract, build and dependency configs, monolith modules → service-aligned components | Golden transcripts recorded from the running legacy app replay green against the new service |
| **3. Modernization / hardening** | Everything past the lift-and-shift: Java version upgrade, test generation that locks in behavior parity, SAST remediation, CI/build fixes — event-driven and always-on | CI gates every PR; scheduled and event-driven sessions keep it green without a human in the loop |

The extraction prompts invoke the `!struts-to-microservice` Devin Playbook,
whose source lives in the target repo at
`.workshop/playbooks/struts-to-microservices.devin.md`. The repo-specific
mechanics (make targets, ports, harness paths) come from that repo's Skill at
`.agents/skills/struts-to-microservices/SKILL.md`, which Devin auto-loads when
working there.

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Phase 1 — Analysis and Assessment](#phase-1)
- [Phase 2 — Code Transformation](#phase-2)
  - [Extract one module live, with verification](#extract-one)
  - [The divergence the harness catches](#divergence)
  - [Fan out in parallel](#fan-out)
- [Phase 3 — Modernization and Hardening](#phase-3)
  - [Always-on: scheduled and event-driven](#always-on)
- [Confidence = Programmatic Verification](#confidence)
- [Run the Produced Artifact](#run-artifact)
- [Concurrent Runs](#concurrent)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Everything in the target repo runs from a clean checkout:

```bash
git clone https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-struts-to-microservices
cd uc-legacy-modernization-struts-to-microservices

make up NS=demo        # Postgres + the extracted services (8081, 8082)
make parity NS=demo    # replay the golden transcripts, print the report
make down NS=demo
```

The starting state prints `Summary: PASS=8 FAIL=0 SKIP=14` — the SKIPs are the
modules not yet extracted. Turning those SKIPs into PASSes is the demo.

To run the legacy application alongside it:

```bash
git clone https://github.com/Cognition-Partner-Workshops/ts-java-struts-claims-management
cd ts-java-struts-claims-management
make seed && make run   # http://localhost:8080/claims/  (supervisor / supervisor)
```

If Maven Central rate-limits the box, prefix the build with
`MAVEN_MIRROR=https://maven.aliyun.com/repository/central`.

---

<a id="repositories"></a>
## Repositories

- [ts-java-struts-claims-management](https://github.com/Cognition-Partner-Workshops/ts-java-struts-claims-management) — the legacy estate. NorthStar Claims: a property & casualty claims monolith in 2008-era idiom — Struts 1.3.10, Servlet 2.4 `web.xml`, JSP 2.0 + JSTL + Struts taglibs, raw JDBC over HSQLDB, no Spring, no JPA. 93 Java classes, 40 JSPs, an Ant build alongside Maven, and 22 recorded golden transcripts. Read-only reference for the "before".
- [uc-legacy-modernization-struts-to-microservices](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-struts-to-microservices) — the Spring Boot target: the already-extracted services, the Struts→Spring mapping notes and reference architecture, the parity replay harness, the playbook source, and the repo Skill.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

The **before** state is deliberately a *partial* migration: the policy
administration and FNOL intake modules are already extracted on `main` with
their transcripts green, so the target architecture and the harness are
established. What Devin extracts **live** is the next module in the wave — the
settlement and payments module, which is not on `main`.

The verification loop sits between them. The legacy application was recorded
once, as golden transcripts: for each scenario, the request submitted and the
business outcome observed — status, result target, the business fields
rendered, the validation error keys, and the resulting database state. Markup is
normalized away, because the new service renders nothing like a JSP but must
produce identical outcomes. Every extraction replays those transcripts and is
not done until they match.

A recorded scenario looks like this — `transcripts/settlement_blank_deductible.json`:

```json
{
  "scenario": "settlement_blank_deductible",
  "description": "FNOL settlement with a blank deductible",
  "request": {
    "method": "POST",
    "path": "/claims/settlement/calculate.do",
    "form": { "claimId": "120", "coveredAmount": "5000.00",
              "deductible": "", "depreciation": "500.00" }
  },
  "expected": {
    "status": 200,
    "result": "forward:/WEB-INF/jsp/settlement/calculate.jsp",
    "business_fields": { "settlementAmount": "4500.00",
                         "deductibleApplied": "0.00", "cappedAtLimit": "false" },
    "validation_errors": [],
    "db_state": { "claim.120.status": "CLOSED" }
  }
}
```

> **On "parity":** parity means the recorded behavior of the running legacy
> application reproduces against the new service — a deterministic contract over
> business outcomes, not a byte-for-byte HTML diff.

The before state is durable and the live work is namespaced and disposable,
which is what makes this repeatable and safe to run concurrently.

---

<a id="phase-1"></a>
## Phase 1 — Analysis and Assessment

This is the discovery phase architects normally do by hand over weeks. Nothing
here changes code — the output is the groundwork for the modernization plan.
With DeepWiki indexed over the repo, Devin typically orients in minutes
(coverage depends on repo structure).

Start with the estate map.

```
Using the Cognition-Partner-Workshops/ts-java-struts-claims-management repo,
give me a map of this Struts estate:

- every action mapping across src/main/webapp/WEB-INF/struts-config.xml,
  struts-config-claims.xml and struts-config-admin.xml, with its Action class,
  form bean, validation rules, and forwards
- the JSP inventory under src/main/webapp/WEB-INF/jsp: which JSPs post to which
  actions, which are reachable, and which custom tags they use
- the DAO layer: every SQL statement in com.northstar.claims.dao and the tables
  it touches
- a call graph from each Action down through the manager singletons and DAOs

Give me a table per section, and write the raw inventories to
analysis/ESTATE_INVENTORY.md.
```

Then the judgment call that actually matters — what is business logic and what
is framework plumbing.

```
For each Action class in com.northstar.claims.web, classify every method into
(a) domain business rules, (b) Struts/JSP framework plumbing, or (c)
persistence. Call out anything you cannot cleanly classify and explain why.

Then tell me specifically which business rules are embedded inside Action
classes rather than in a service layer, since those are the ones a migration is
most likely to drop. Write it to analysis/LOGIC_VS_PLUMBING.md.
```

Then the boundaries and the debt.

```
Propose service boundaries for decomposing this monolith based on the actual
coupling in the code rather than the package layout: which actions, tables, and
domain rules move together, and where the seams are cleanest. For each candidate
service, list what it owns and what it would have to call.

Separately, report the estate's technical debt with file and line evidence:
duplicated logic, dead code, string-concatenated SQL, and any place where
framework behavior — blank-field coercion, lenient date parsing, money
rounding — is silently deciding a business outcome. Write both to
analysis/SERVICE_BOUNDARIES.md and analysis/TECH_DEBT.md.
```

The estate has real finds waiting, and the report should surface them with
evidence rather than generalities:

| Finding | Where |
|---|---|
| Duplicate date-formatting helpers in different packages | `dao/DateHelper.java` and `util/DateUtil.java` |
| A form bean mapped in `struts-config` but unreachable from any JSP | `DeadForm` |
| String-concatenated SQL — an injection path, not a style nit | `web/WorkbenchStatusAction.java:25`, `web/WorkbenchReserveAction.java:22`, `web/WorkbenchAssignAction.java:25`, `web/ClaimsActionSupport.java:68` |
| Architecture doc describing a class that no longer exists | `docs/ARCHITECTURE.txt` describes `WorkflowAction`; nothing in `src/` references it |
| Business rules living inside `execute()` rather than a service | the settlement and workbench actions |

That last row is the bridge into Phase 2. The framework behaviors surfaced here
— blank-field coercion, lenient dates, `double` rounding — are exactly what the
parity harness will hold the migration to.

> Capture the accepted output as a Knowledge note or commit it to the repo. Every
> later session — including the children in the fan-out — starts from the same
> agreed map instead of re-deriving it.

---

<a id="phase-2"></a>
## Phase 2 — Code Transformation

<a id="extract-one"></a>
### Extract one module live, with verification

This is the core beat: one module, off the playbook, gated by the transcripts.
Start a session on the target repo and invoke the macro.

```
!struts-to-microservice

Module: the settlement and payments module of the NorthStar Claims monolith in
Cognition-Partner-Workshops/ts-java-struts-claims-management —
SettlementCalculateAction, SettlementSaveAction, SettlementDetailAction,
PaymentIssueAction, PaymentHistoryAction, PaymentDetailAction,
PaymentRemittanceAction, SettlementForm, PaymentForm, SettlementDAO,
PaymentDAO, SettlementCalculator, and the JSPs those actions forward to.

Target service: services/settlement-service on port 8083, wired into
docker-compose, the Makefile, and CI the same way policy-service and
claims-intake-service are.

Transcript scope: the settlement module. Every one of those scenarios reports
SKIP (not yet extracted) today and must end green:

    make up NS=settlement
    make parity NS=settlement MODULE=settlement

Namespace: settlement (PORT_OFFSET=100).
```

Two things are worth watching before any code is written. Devin reads the legacy
module end to end and separates domain rules from Struts plumbing — the
settlement formula, limit capping and status transitions are migrated as logic,
while `ActionForm` population and `ActionForward` selection are simply replaced
by the framework. Then it enumerates the **coercions the framework was silently
performing** on each form field: what `BeanUtils` does with an empty submission,
what the lenient date format does with an out-of-range value, and how money is
rounded on the way to the database. Those are behavior, not accidents, and they
are where migrations quietly break.

Then it writes the service, adds its routes declaratively to
`parity/routes.yaml`, flips `settlement: extracted`, and runs the loop.

<a id="divergence"></a>
### The divergence the harness catches

The most instructive failure in this migration is not caused by sloppy work. It
is caused by doing the **modern, correct** thing.

Every Java reviewer knows the rule: never use `double` for money, use
`BigDecimal`. So the natural way to write the settlement calculator is:

```java
BigDecimal amount = gross.subtract(deductibleValue);
BigDecimal rounded = amount.setScale(2, RoundingMode.HALF_UP);
```

That code is better than what it replaced. It also changes what claimants get
paid. Run the loop and the harness says so:

```
$ make parity NS=settlement

settlement_calculate             PASS
settlement_blank_deductible      PASS
settlement_half_cent             FAIL | settlementAmount 1.01 != legacy 1.00
settlement_policy_cap            PASS
settlement_deductible_floor      PASS
Summary: PASS=15 FAIL=1 SKIP=6
make: *** [Makefile:16: parity] Error 1
```

One scenario, one field, both values, and a non-zero exit that fails CI.

The explanation is in the legacy code. `SettlementCalculator` does its
arithmetic in `double` and rounds with `Math.round(amount * 100.0) / 100.0`. A
covered amount of `1.005` is not exactly 1.005 in binary floating point — it is
a hair *below* it — so `Math.round` returns `100`, and the legacy application
has always paid `1.00`. `BigDecimal.HALF_UP` reads the decimal string `1.005`
exactly, rounds up, and pays `1.01`.

A one-cent difference sounds harmless until you multiply it by every settlement
ever issued and reconcile it against the general ledger. Nobody would catch this
in code review — the new code is the code a reviewer would *ask* for.

The fix is to reproduce the legacy arithmetic explicitly rather than to adjust
the transcript:

```java
// legacy-faithful: Java double half-cent rounding happens last.
double rounded = Math.round(amount * 100.0) / 100.0;
```

...and to record it in `docs/KNOWN_LEGACY_QUIRKS.md` with a unit test pinning
it, so that moving to `BigDecimal` later becomes a deliberate, priced business
decision instead of a silent side effect of a migration.

This is the whole argument for the loop in one scenario: **"the new code looks
correct" and "the new code behaves identically" are different claims, and only
one of them can be checked by a machine.**

> To reproduce this live, swap the `Math.round` line in
> `services/settlement-service/.../SettlementCalculator.java` for the
> `BigDecimal.HALF_UP` version above, then `make up` and `make parity`. It fails
> the same way every time.

<a id="fan-out"></a>
### Fan out in parallel

Modules are independent once their seams are identified, so the remaining wave
parallelizes. From an orchestrator session:

```
Fan out the rest of the NorthStar migration. Spawn one child session per
remaining module — workbench and reporting — each following
!struts-to-microservice with its own namespace, its own branch, and its own
transcript scope:

- workbench  → services/workbench-service,  NS=workbench,  PORT_OFFSET=200
- reporting  → services/reporting-service,  NS=reporting,  PORT_OFFSET=300

Monitor them. A child is done when `make parity MODULE=<module>` is green in
its namespace with the already-extracted modules still passing, and its PR is
open with the parity report attached. Report each child's status, the
divergences it hit, and the quirks it reproduced. Do not let a child modify a
transcript.
```

Each child runs on its own VM, with its own scoped credentials, its own
namespace and its own branch. Isolation is what makes the fan-out safe: nothing
shares a database, a port, or a working tree, so nothing collides and each PR
stands on its own evidence. The playbook is what makes the fan-out consistent —
every child follows the same procedure and is held to the same parity contract,
so it is the same review bar applied N times in parallel instead of once in
series.

---

<a id="phase-3"></a>
## Phase 3 — Modernization and Hardening

The lift-and-shift is not the finish line. With behavior pinned by transcripts,
the surrounding modernization is safe to do, because anything that changes
behavior fails the loop immediately.

```
Now harden the extracted services in
uc-legacy-modernization-struts-to-microservices:

- generate unit tests that lock in every legacy quirk listed in
  docs/KNOWN_LEGACY_QUIRKS.md — one test per quirk, named for the behavior it
  pins, so a future refactor cannot silently drop it
- fix every Semgrep finding `make sast` reports, including the SQL that was
  string-concatenated in the legacy estate; parameterize it
- make `make lint` clean across the services and the harness
- re-run `make parity` after every change

Report anything you could not fix without changing behavior, rather than
changing behavior.
```

The Java upgrade is the same shape of task: the legacy estate is Java-6-era code
compiled at source level 7, the target services are Java 21 with records,
pattern matching, and Spring Boot 3.5.x. The transcripts are what make that
upgrade auditable rather than hopeful.

<a id="always-on"></a>
### Always-on: scheduled and event-driven

The same procedure runs without a human in the loop.

**Scheduled** — a recurring session that re-runs the full parity suite against
the extracted services and reports drift:

```
Create a scheduled Devin that runs every weekday at 07:00 UTC against
uc-legacy-modernization-struts-to-microservices:

Run `make up NS=nightly && make parity NS=nightly && make down NS=nightly`.
If every scenario passes, post a one-line summary. If anything fails or errors,
open an issue with the failing scenarios, the field-level diffs from
parity/report.md, and the commits merged since the last green run.
```

**Event-driven** — an [Automation](https://docs.devin.ai/product-guides/automations)
that reacts instead of waiting:

- *On CI failure on `main`* → start a session that reads the parity report from
  the failed job, reproduces the failure locally, fixes the service, and opens a
  PR. The transcript is never touched, so an automated fix cannot paper over a
  behavior change.
- *On a new commit to the legacy repo* → re-run `make sync-transcripts`, replay
  the affected transcripts, and open a PR (or an issue) if the legacy behavior
  moved. The legacy estate is still live; this keeps the migration honest while
  it is in flight.

---

<a id="confidence"></a>
## Confidence = Programmatic Verification

Nothing in this thread asks anyone to eyeball a diff and decide whether a
migration is correct. Every claim is backed by something that runs:

| Claim | What proves it |
|---|---|
| "The service behaves like the legacy app" | `make parity` — every recorded scenario replays with identical business fields, validation keys, and database state |
| "The legacy quirks survived the migration" | one unit test per entry in `docs/KNOWN_LEGACY_QUIRKS.md` |
| "The legacy recording is still accurate" | `make capture` in the legacy repo is deterministic; CI runs it and fails on any diff in `transcripts/` |
| "Nothing regressed" | CI on every PR: build → tests → lint → SAST → services up → parity, with the report published to the job summary |
| "The code is reviewable" | each unit of work is a PR with the parity report attached; Devin Review comments on it like any other reviewer |

The failure mode this design eliminates is the one that actually happens on
modernization programs: a service that passes review, passes its own unit tests,
and is subtly wrong on the boundary cases nobody wrote a test for — because
those cases were never in the code, they were in the framework.

---

<a id="run-artifact"></a>
## Run the Produced Artifact

Bring up what was produced and exercise it:

```bash
make up NS=demo
curl -s localhost:8081/actuator/health    # {"status":"UP"}
curl -s localhost:8082/actuator/health    # {"status":"UP"}
make parity NS=demo
```

Then open the pieces that show the result rather than describe it:

| Open | What it proves |
|---|---|
| `parity/report.md` (and the CI job summary) | scenario-by-scenario PASS/FAIL/SKIP — the migration's scoreboard |
| Swagger UI on each service | the legacy screens are now a documented API contract |
| `/actuator/health` | the produced services actually run |
| `docs/STRUTS_TO_SPRING_MAPPING.md` | the construct-by-construct mapping the extraction followed |
| `docs/KNOWN_LEGACY_QUIRKS.md` | the quirks reproduced deliberately, each one a business decision rather than an accident |
| The PR and its CI checks | the whole loop re-run by a machine, on a diff a human can review |

---

<a id="concurrent"></a>
## Concurrent Runs

Every command is namespaced, so several people can run this at once on the same
machine or in the same repo:

```bash
make up NS=alice PORT_OFFSET=10
make up NS=bob   PORT_OFFSET=20
```

`NS` names the Compose project, the volumes, and the database; `PORT_OFFSET`
shifts every host port. To revert a run completely:

```bash
docker compose -p claims-alice down -v
```

`main` stays the durable before-state — the live work lives on a branch and in a
disposable namespace, so the demo repeats cleanly.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **The running legacy application is the source of truth.** Not the docs, not
  the code's apparent intent. Struts 1.x has a decade of framework behavior baked
  into what users actually see, and users depend on it whether or not anyone
  intended it.
- **Verification is programmatic, not visual.** A migration is done when the
  recorded behavior replays green — not when the new code looks right. The code
  that looks *better* is often the code that diverges.
- **A caught divergence is the point.** The loop exists to find the cases that
  pass review and fail reality, which on Struts estates are mostly implicit
  framework coercions: blank-field conversion, lenient dates, `double` rounding.
- **The procedure is codified once and reused.** A playbook invoked by `!macro`
  plus a repo Skill means every session — and every child in a fan-out — follows
  the same steps and is held to the same contract.
- **Isolation makes parallelism safe.** Each session and child gets its own VM,
  scoped credentials, namespace, and branch, so a wave of extractions runs
  concurrently without colliding and each lands as its own verified PR.
- **Modernization keeps going after the migration.** With behavior pinned,
  test generation, SAST remediation, Java upgrades and CI fixes are safe to
  automate — on a schedule or triggered by an event, with no human in the loop.
