# Workshop: Application Development & Maintenance (ADM) 201 — Hands-On with Devin

## Event Details

| | |
|---|---|
| **Date** | TBD |
| **Location** | TBD |
| **Host Organization** | *(customer)* |
| **Level** | 201 (intermediate — assumes a prior 101 session or completion of [`courses/foundations/`](../../../courses/foundations/)) |
| **Duration** | ~3 hours (choose 3–4 labs from 3 tracks, run in parallel) |
| **Audience** | ADM delivery teams — maintenance, modernization, quality engineering, and run/operate pods |
| **Tracks** | A: Maintain & Assure Quality · B: Modernize · C: Run & Operate · Capstone: Orchestration at Portfolio Scale |

## Workshop Overview

A 101 session proves Devin can complete one task. This 201 session is about **how an ADM pod operates Devin across a portfolio**: shared context (Knowledge, DeepWiki, playbooks), parallel sessions, the PR feedback loop, and event-driven/scheduled triggers.

Every lab below runs on a repository that already exists in this tenant, so participants can start within the first five minutes. Each lab is one Devin session; participants pick one lab per track and kick off all of them before reviewing anything.

- **Track A — Maintain & Assure Quality:** generate executable BDD coverage for a REST API, and remediate a real CVE backlog with SBOM plus CI gating.
- **Track B — Modernize:** COBOL-to-Java with parity tests, a Spring Boot 2.6 → 3.x upgrade with service extraction, and a .NET monolith decomposition.
- **Track C — Run & Operate:** volume anomaly detection tuning, and automated pod remediation after a credential rotation.
- **Capstone — Orchestration:** encode a repeatable method as a playbook, then fan it out with child sessions across a multi-service estate.

## Getting the Most from This Workshop

> **All sessions run in parallel.** Paste your chosen prompts in the
> first ten minutes, then use Ask Devin and DeepWiki while sessions
> run. PRs begin arriving in 10–20 minutes — then review, comment,
> and watch Devin respond.

- **Kick off everything first.** Devin sessions run on separate machines; there is no reason to serialize them.
- **Use Ask Devin as the research step.** Ask questions about the repo before refining a prompt; a better second prompt is a core 201 skill.
- **Steer through PR comments.** Comment on the PR (general or inline) and Devin wakes up and iterates. This is the collaboration model, not a fallback.
- **Accept Knowledge suggestions.** Knowledge notes are the team's shared context layer — they compound across sessions and across the pod.
- **Compare notes.** Two participants running the same lab with different prompts is the most useful conversation of the day.

## Table of Contents

- [Agenda](#agenda)
- [Track A — Maintain & Assure Quality](#track-a)
  - [Lab A1 — BDD Coverage for a REST API](#lab-a1)
  - [Lab A2 — CVE Remediation with Compliance Gating](#lab-a2)
- [Track B — Modernize](#track-b)
  - [Lab B1 — COBOL to Java with Parity Tests](#lab-b1)
  - [Lab B2 — Spring Boot 2 to 3 Upgrade + Service Extraction](#lab-b2)
  - [Lab B3 — .NET Monolith Decomposition](#lab-b3)
- [Track C — Run & Operate](#track-c)
  - [Lab C1 — Volume Anomaly Detection Tuning](#lab-c1)
  - [Lab C2 — Pod Remediation After Credential Rotation](#lab-c2)
- [Capstone — Orchestration at Portfolio Scale](#capstone)
- [Repos Required](#repos-required)
- [Facilitator Notes](#facilitator-notes)
- [Workshop Key Takeaways](#workshop-key-takeaways)

---

<a id="agenda"></a>

## Agenda

| Time | Activity |
|------|----------|
| 0:00–0:15 | Recap of 101, platform walkthrough, how a pod operates Devin |
| 0:15–0:25 | **Kick off one lab per track** (paste 3 prompts) |
| 0:25–0:45 | Ask Devin + DeepWiki exploration while sessions run |
| 0:45–1:15 | Review first PRs — leave comments, watch Devin iterate |
| 1:15–1:30 | Break |
| 1:30–2:00 | Knowledge notes + playbook authoring from the labs just completed |
| 2:00–2:40 | **Capstone:** playbook + child sessions across a multi-service estate |
| 2:40–3:00 | Wrap-up: automation patterns (webhooks, scheduled sessions), Q&A |

---

<a id="track-a"></a>

## Track A — Maintain & Assure Quality

<a id="lab-a1"></a>

### Lab A1 — BDD Coverage for a REST API

**Value driver:** *Scenario authoring is high-volume, pattern-driven work. Devin reads the existing Cucumber glue code and produces readable, executable coverage that follows the framework's established patterns.*

- **Repository:** [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber)
- **Lab module:** [BDD Test Generation](../../../labs/testing-qa/bdd-test-generation.md)

The repo is a Spring Boot + Cucumber framework. Existing feature files live in `src/test/resources/features/` (`users.feature`, `users-import.feature`), step definitions in `src/main/java/fr/redfroggy/bdd/restapi/glue/`, and the runner is `src/test/java/fr/redfroggy/bdd/restapi/RestApiCucumberTest.java`.

#### Paste into Devin

```
In uc-bdd-test-generation-cucumber, extend the Cucumber
suite with coverage for a Pet resource (CRUD + list).

1. Read `src/test/resources/features/users.feature` and the
   glue code in
   `src/main/java/fr/redfroggy/bdd/restapi/glue/` first, and
   follow those patterns — do not introduce a second style
   of step definition.
2. Add `src/test/resources/features/pets.feature` covering:
   create, get by id, update, delete, list with pagination,
   validation failure on a missing required field, and a
   404 on an unknown id.
3. Use a Scenario Outline with an Examples table for the
   validation boundary cases.
4. Add whatever controller/fixtures are needed under
   `src/test/java/fr/redfroggy/bdd/restapi/` so the suite is
   self-contained, and make `mvn test` pass.
5. In the PR description, list each scenario and the
   endpoint plus condition it covers.
```

#### Research with Ask Devin

- *"How does the glue code in uc-bdd-test-generation-cucumber map Gherkin steps to HTTP calls, and how is scenario state shared?"*
- *"Which of our scenarios would break if run in parallel?"*

#### Review & Give Feedback

- Read the feature file as a business analyst would — does it describe behavior or implementation?
- Leave a PR comment asking for negative-path scenarios on authentication, and watch Devin push a follow-up commit.

#### Key Takeaways

- Devin extends an existing test convention rather than inventing a parallel one — when you point it at the pattern.
- Reviewing generated tests for *readability* is the real quality gate; passing status codes are the easy part.
- The same prompt, saved as a playbook, becomes the pod's standard for every API onboarding.

---

<a id="lab-a2"></a>

### Lab A2 — CVE Remediation with Compliance Gating

**Value driver:** *A vulnerability backlog is capacity-constrained work that never reaches the top of a sprint. Devin upgrades, fixes the breakage, proves it with tests, and leaves an auditable trail.*

- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Lab modules:** [Remediate Vulnerabilities](../../../labs/security/remediate-vulnerabilities.md) · [Shift-Left Security](../../../labs/security/shift-left-security.md)

A Spring Boot 2.6.3 / Gradle application with a genuine dependency backlog. `docker-compose.sonarqube.yml` is available if the group wants a scanner in the loop.

#### Paste into Devin

```
In uc-cve-remediation-regulatory-compliance, remediate the
vulnerable dependency backlog and add gating so it cannot
regress.

1. Produce a current inventory of vulnerable dependencies
   from `build.gradle` with severity and fixed version.
2. Upgrade them, starting with the highest severity. Fix
   every breaking API change and keep `./gradlew build`
   green after each step.
3. Add an SBOM (CycloneDX) generation task to the Gradle
   build and a GitHub Actions workflow that fails the build
   on new HIGH or CRITICAL findings.
4. Write `SECURITY_REMEDIATION.md`: a table of each CVE, the
   version delta, the code changes required, and the
   residual risk for anything you could not upgrade.
```

#### Research with Ask Devin

- *"Which dependency upgrades in this repo carry breaking API changes, and which are drop-in?"*
- *"What in this codebase would block a Spring Boot 3 upgrade later?"*

#### Review & Give Feedback

- Check the tests actually exercise the changed code paths, not just that the build compiles.
- Comment asking Devin to split anything it deferred into a clearly scoped follow-up section.

#### Key Takeaways

- Remediation is only complete when it is *verified and documented* — the report is a deliverable, not a nicety.
- Adding the CI gate in the same PR converts a one-time cleanup into a standing control.
- Connect this to a webhook on scanner findings and the backlog stops accumulating between releases.

---

<a id="track-b"></a>

## Track B — Modernize

<a id="lab-b1"></a>

### Lab B1 — COBOL to Java with Parity Tests

**Value driver:** *Devin reads copybooks, WORKING-STORAGE, and packed-decimal arithmetic and translates them to idiomatic Java — then writes the tests that prove equivalence.*

- **Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- **Lab modules:** [COBOL to Java](../../../labs/migration-modernization/cobol-to-java.md) · [Migration Test Harness](../../../labs/migration-modernization/migration-test-harness.md)

The CardDemo estate: COBOL programs in `app/cbl/` (including `CBACT01C.cbl`, `CBTRN02C.cbl`), copybooks in `app/cpy/`, and 3270 screen maps in `app/bms/`.

#### Paste into Devin

```
In uc-legacy-modernization-cobol-to-java, migrate the batch
program `app/cbl/CBTRN02C.cbl` to Java 17.

1. Explain the program's business logic first: inputs,
   outputs, files, and every branch that changes a balance
   or a status code. Read the copybooks it uses in
   `app/cpy/`.
2. Implement the Java version as a Maven project under
   `java-migration/`. Map PIC clauses and COMP-3 fields to
   types that preserve decimal precision exactly — do not
   use double for money.
3. Write JUnit parity tests driven by the ASCII feed files
   in `app/data/ASCII/` that assert the Java output matches
   the COBOL-defined behavior, including at least two
   boundary/error paths.
4. Document the field-by-field mapping and any COBOL
   construct you had to reinterpret in
   `java-migration/MIGRATION_NOTES.md`.
```

#### Research with Ask Devin

- *"Which programs in `app/cbl/` are the most complex, and what are their dependencies on each other?"*
- *"Where does this program rely on COBOL-specific arithmetic behavior that Java would get wrong?"*

#### Review & Give Feedback

- Verify the money types (`BigDecimal`, scale, rounding mode) before anything else.
- Comment asking for an enum in place of string status constants and see the ripple through the tests.

#### Key Takeaways

- Migration without parity tests is guesswork; the harness is the deliverable that makes the translation reviewable.
- Each program is an independent unit of work — this is the canonical child-session fan-out (see the [Capstone](#capstone)).
- Devin's explanation step is reusable value on its own: it documents an estate nobody currently understands.

---

<a id="lab-b2"></a>

### Lab B2 — Spring Boot 2 to 3 Upgrade + Service Extraction

**Value driver:** *The javax → jakarta migration touches nearly every file — tedious, mechanical, and exactly what an autonomous agent should own.*

- **Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)
- **Lab modules:** [Framework Upgrade](../../../labs/migration-modernization/framework-upgrade.md) · [Containerization & Microservice Extraction](../../../labs/migration-modernization/containerization-microservice-extraction.md)

Java 11 / Spring Boot 2.6.3 Gradle monolith. The repo has an `AGENTS.md` — point participants at it as an example of repo-level agent guidance.

#### Paste into Devin

```
In uc-spring-boot-upgrade-microservice-extraction, upgrade
from Java 11 / Spring Boot 2.6.3 to Java 17 / Spring Boot
3.2.

1. Read `AGENTS.md` and `build.gradle` first and follow the
   repo's conventions.
2. Do the upgrade in reviewable stages: Gradle and plugin
   versions, then the javax to jakarta namespace migration,
   then deprecated API replacements, then Spring Security
   configuration changes.
3. Keep `./gradlew build` passing at each stage and report
   which stage broke what.
4. In the PR description, give a stage-by-stage summary and
   call out any behavior change a reviewer must verify
   manually.
```

Stretch, in a second session once the upgrade PR is open:

```
Using the upgraded uc-spring-boot-upgrade-microservice-
extraction codebase, propose extracting the user/profile
domain into a standalone Spring Boot 3 service. Produce:
the target module layout, the API contract between the two
services, what happens to the shared persistence layer,
and a phased cutover plan with the risk at each phase.
Implement the extracted service skeleton with its
controllers and tests.
```

#### Research with Ask Devin

- *"What are the highest-risk parts of the Spring Boot 2 to 3 upgrade in this repo, and which files change the most?"*
- *"Which domains in this codebase are the cleanest seams for extraction, and which are too coupled?"*

#### Key Takeaways

- Staged upgrades are reviewable upgrades — ask for stages explicitly and the PR becomes readable.
- Repo-level agent guidance (`AGENTS.md`) is how a pod makes conventions stick across every session.
- One proven upgrade playbook applies to every Spring Boot 2.x service in the portfolio.

---

<a id="lab-b3"></a>

### Lab B3 — .NET Monolith Decomposition

**Value driver:** *Decomposition planning normally needs the one architect who knows the system. Devin produces the seam analysis and a working service skeleton in a single session.*

- **Repositories:** [quickapp-monolith](https://github.com/Cognition-Partner-Workshops/quickapp-monolith) (source) → [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices) (target state)
- **Lab module:** [.NET Monolith Decomposition](../../../labs/migration-modernization/dotnet-monolith-decomposition.md)

A .NET + Angular monolith (`QuickApp.Core`, `QuickApp.Server`, `quickapp.client`). The target-state repo shows one possible decomposition — use it to compare, after Devin proposes its own.

#### Paste into Devin

```
Analyze the quickapp-monolith codebase (`QuickApp.Core`,
`QuickApp.Server`, `quickapp.client`) and produce a
decomposition proposal.

1. Map the current module and data dependencies, and
   identify bounded contexts with the coupling evidence for
   each (shared entities, cross-module calls, shared DB
   tables).
2. Recommend a service boundary set, ranked by
   extraction difficulty, and say which one to do first and
   why.
3. Implement the first extraction as a standalone .NET
   service: project layout, API contract, data ownership,
   and tests.
4. Write `DECOMPOSITION_PLAN.md` with the target
   architecture, the strangler-fig sequence, and the
   rollback position at each step.
```

#### Review & Give Feedback

- Compare Devin's proposed boundaries with `quickapp-microservices` and discuss where they differ and why.
- Comment challenging one boundary — a good agent defends or revises with evidence.

#### Key Takeaways

- Coupling evidence, not intuition, is what makes a decomposition proposal reviewable.
- The plan and the first extraction in one PR gives the architecture group something concrete to argue about.

---

<a id="track-c"></a>

## Track C — Run & Operate

<a id="lab-c1"></a>

### Lab C1 — Volume Anomaly Detection Tuning

**Value driver:** *Alert tuning is continuous, low-glamour work. Devin can extend detectors, backtest them against history, and explain the false-positive trade-off.*

- **Repository:** [uc-volume-anomaly-detection](https://github.com/Cognition-Partner-Workshops/uc-volume-anomaly-detection)
- **Lab module:** [Volume Anomaly Detection](../../../labs/observability-sre/volume-anomaly-detection.md)

Python detectors in `src/detectors/` (`zscore_detector.py`, `seasonal_detector.py`), agents in `src/agents/`, rules in `config/detection_rules.yaml`, history in `data/historical/sample_transactions.csv`.

#### Paste into Devin

```
In uc-volume-anomaly-detection, reduce false positives
without losing real incidents.

1. Read `src/detectors/zscore_detector.py`,
   `src/detectors/seasonal_detector.py`, and
   `config/detection_rules.yaml`, then backtest the current
   rules against `data/historical/sample_transactions.csv`
   and report the alerts they produce.
2. Add a detector that handles a pattern the current ones
   miss (for example a sustained level shift versus a
   single spike), following the existing detector
   interface.
3. Add tests in `tests/` covering the new detector,
   including a case that must NOT alert.
4. Write a short tuning report: alerts before, alerts
   after, which ones you consider real, and the threshold
   trade-off you chose.
```

#### Research with Ask Devin

- *"How does the seasonal baseline in this repo handle holidays and month-end spikes?"*
- *"How do the agents in `src/agents/` turn a detection into an incident insight?"*

#### Key Takeaways

- Backtest-before-change is the difference between tuning and guessing.
- A scheduled session can re-run this tuning report weekly and open a PR when thresholds drift.

---

<a id="lab-c2"></a>

### Lab C2 — Pod Remediation After Credential Rotation

**Value driver:** *A rotation-induced outage is a known-shape incident with a known-shape fix — the ideal candidate for event-driven, approval-gated automation.*

- **Repository:** [uc-pod-remediation-credential-rotation](https://github.com/Cognition-Partner-Workshops/uc-pod-remediation-credential-rotation)
- **Lab module:** [Pod Remediation & Credential Rotation](../../../labs/observability-sre/pod-remediation-credential-rotation.md)

Agents in `src/agents/` (`failure_detector.py`, `rotation_monitor.py`, `approval_workflow.py`, `remediation.py`), K8s manifests in `k8s/base/`, a ServiceNow client in `src/utils/servicenow_client.py`, and an inventory fixture at `data/sample_inventory.json`.

#### Paste into Devin

```
In uc-pod-remediation-credential-rotation, harden the
remediation path end to end.

1. Trace the flow from `src/agents/rotation_monitor.py`
   through `failure_detector.py`, `approval_workflow.py`,
   and `remediation.py`, and document it as a sequence
   diagram in `docs/REMEDIATION_FLOW.md`.
2. Find the failure modes that are currently unhandled —
   for example a partially rotated secret, an approval
   timeout, or a remediation that does not resolve the
   failure — and implement handling with bounded retries
   and a clear terminal state for each.
3. Never allow an unapproved change to reach the cluster:
   add tests in `tests/` that prove the approval gate holds
   under each failure mode.
4. Add a runbook section covering what an on-call engineer
   does when automated remediation gives up.
```

#### Key Takeaways

- Automated remediation is trustworthy only when its refusal paths are tested as carefully as its happy path.
- Approval workflows (ServiceNow-style) are how autonomous remediation becomes acceptable in a regulated estate.

---

<a id="capstone"></a>

## Capstone — Orchestration at Portfolio Scale

This is the part that makes the session 201-level. Individually, each lab above is one session. An ADM pod's real problem is *fifty* of them.

**Step 1 — Turn what you just did into a playbook.** Take the lab you completed and write its method down as a playbook: the ordered steps, the verification gate, and the required PR output. Ask Devin to draft it from the session you just ran.

```
Based on the session you just completed, write a playbook
that another engineer could run against a different service
to get the same quality of result. Include: the ordered
procedure, the verification commands, what must appear in
the PR description, and the failure cases where the
engineer should stop and escalate instead of continuing.
```

**Step 2 — Fan it out with child sessions.** Pick an estate with repeated units of work and run the playbook across all of them concurrently:

```
petclinic-microservices contains several independent Spring
Boot services (customers, vets, visits, api-gateway,
config-server, discovery-server). Analyze the repo, then
for each service create a child session that follows our
upgrade playbook: raise dependency versions, fix breaking
changes, keep the build green, and open a PR per service.
Report a summary table of every service, its child session,
and its PR.
```

Good fan-out candidates already in this tenant: `petclinic-microservices` (multiple services), `uc-legacy-modernization-cobol-to-java` (one child per COBOL program), `quickapp-monolith` → `quickapp-microservices` (one child per extracted service).

**Step 3 — Wire the trigger.** Discuss with the group which of their real workflows should be event-driven rather than human-initiated: scanner findings → remediation session; failed CI on main → fix session; Jira ticket transition → implementation session; weekly scheduled dependency drift report.

**Step 4 — Persist the context.** Whatever the sessions learned about these repos should end up in Knowledge notes, an `AGENTS.md`, or a repo Skill — otherwise the next session re-learns it. `uc-spring-boot-upgrade-microservice-extraction` and `ts-java-spring-boot-realworld` both carry repo-level agent guidance worth showing.

### Key Takeaways

- Playbooks convert one good session into a repeatable pod-level standard.
- Child sessions turn portfolio work from sequential to parallel — the unit of scale is the repo or the program, not the engineer.
- Triggers (webhooks, schedules) remove the human from the *initiation* of routine work while keeping them on the review.
- Knowledge, `AGENTS.md`, and Skills are the shared context layer that makes session #50 better than session #1.

---

<a id="repos-required"></a>

## Repos Required

| Lab | Repository |
|---|---|
| A1 | [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber) |
| A2 | [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) |
| B1 | [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java) |
| B2 | [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) |
| B3 | [quickapp-monolith](https://github.com/Cognition-Partner-Workshops/quickapp-monolith), [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices) |
| C1 | [uc-volume-anomaly-detection](https://github.com/Cognition-Partner-Workshops/uc-volume-anomaly-detection) |
| C2 | [uc-pod-remediation-credential-rotation](https://github.com/Cognition-Partner-Workshops/uc-pod-remediation-credential-rotation) |
| Capstone | [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices), [ts-java-spring-boot-realworld](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld) |

### Optional Substitutions

If a group's interest sits elsewhere, these repos in the same tenant swap in without changing the lab shape:

| Interest | Repository | Substitute for |
|---|---|---|
| Feature development on a full-stack app | [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) (`backend/`, `frontend/`), [timesheet-infra](https://github.com/Cognition-Partner-Workshops/timesheet-infra) | A1 or B3 |
| REST/GraphQL API work, E2E UI testing | [ts-java-spring-boot-realworld](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld) | A1 |
| Angular framework upgrades | [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular), [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld), [ts-angularjs-blur-admin](https://github.com/Cognition-Partner-Workshops/ts-angularjs-blur-admin) | B2 |
| .NET Framework → cloud native | [eShopModernizing](https://github.com/Cognition-Partner-Workshops/eShopModernizing) | B3 |
| Mainframe comprehension without migration | [ts-cobol-carddemo](https://github.com/Cognition-Partner-Workshops/ts-cobol-carddemo) | B1 |
| Database migration | [uc-db-migration-oracle-to-postgres](https://github.com/Cognition-Partner-Workshops/uc-db-migration-oracle-to-postgres) | B1 or B3 |
| SAS / data engineering estate | [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics), [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks) | C1 |
| AppSec scanning and remediation | [uc-appsec-nodegoat](https://github.com/Cognition-Partner-Workshops/uc-appsec-nodegoat), [uc-vulnerability-remediation-java-dotnet](https://github.com/Cognition-Partner-Workshops/uc-vulnerability-remediation-java-dotnet) | A2 |
| Selenium / Page Object test estates | [ts-java-selenium-testng](https://github.com/Cognition-Partner-Workshops/ts-java-selenium-testng), [ts-katalon-web-automation](https://github.com/Cognition-Partner-Workshops/ts-katalon-web-automation) | A1 |

The complete inventory of repositories available in this tenant is in [`catalog/repos.md`](../../../catalog/repos.md).

---

<a id="facilitator-notes"></a>

## Facilitator Notes

- **Prerequisite check.** This session assumes participants have already run at least one Devin session. If half the room has not, run [`workshops/general/`](../../../workshops/general/) instead and return to this one.
- **Track assignment.** Assign tracks rather than letting the room self-select — otherwise everyone picks modernization and there is nothing to compare across tracks in the wrap-up.
- **Timing risk.** B1 and B2 are the longest labs. Have those participants kick off first, at 0:15.
- **Capstone dependency.** The capstone playbook step needs a completed session to draw from, so it cannot move earlier in the agenda.
- **Discussion anchor for the wrap-up.** Ask each track to name one task from their real backlog that has the same shape as the lab they ran, and how many instances of it they have. That number is the whole point of the capstone.

---

<a id="workshop-key-takeaways"></a>

## Workshop Key Takeaways

- **Devin is a pod-level resource, not a personal tool.** Playbooks, Knowledge notes, and `AGENTS.md` are how a team's standards — not one engineer's habits — get applied to every session.
- **Parallelism is the unit of value.** One session proves capability; concurrent sessions across a portfolio change delivery economics. Every lab here has a child-session fan-out form.
- **Verification is part of the deliverable.** Parity tests, backtests, CI gates, and remediation reports are what make autonomous output reviewable and safe to merge.
- **The PR is the collaboration surface.** Comments steer the work; review stays with the humans while the mechanical iteration does not.
- **Routine work should be triggered, not initiated.** Scanner findings, CI failures, ticket transitions, and schedules are better session starters than a person remembering to start one.
