# Workshop: Cloud Agents Across the SDLC — Hands-On with Devin

## Event Details

| | |
|---|---|
| **Date** | 24 August 2026 |
| **Location** | Remote |
| **Host Organization** | *(customer)* — FS ADM team |
| **Level** | 201 (intermediate — assumes at least one prior Devin session) |
| **Duration** | ~3 hours (3 tracks, run in sequence, sessions overlap) |
| **Audience** | ADM delivery pods — developers, quality engineers, leads deciding where cloud agents fit in their SDLC |
| **Repos** | `ts-java-spring-boot-realworld`, `petclinic-*`, `quickapp-*`, `timesheet-*` |

## Workshop Overview

Three patterns cover nearly every place a cloud agent earns its keep in the SDLC. This workshop runs them in the order a team actually adopts them:

| Order | Pattern | What it is | Track |
|---|---|---|---|
| 1 | **Delegate — Every-Day Development** | The work an engineer hands off mid-flow: small, well-defined tasks that should never block the human | [Track 1](#track-1) |
| 2 | **Scale-Out — Parallel Agents** | Many agents running the same job at once — fleet-wide work that used to need a migration team | [Track 2](#track-2) |
| 3 | **Always-On — Event-Driven** | Agents that fire automatically off PRs, alerts, tickets, or schedules — first responders that don't sleep | [Track 3](#track-3) |

Why this order: Track 1 is the pattern every participant can use tomorrow, and it produces the artifact the later tracks build on. Track 2 takes one proven task and multiplies it across a portfolio. Track 3 removes the human from *starting* the work. Each track uses the same four application families, so the codebase stops being the variable and the operating pattern becomes the thing under study.

- **Track 1 (Delegate)** — full-stack feature and bug work on `timesheet-app`, plus a mid-flow test/docs handoff on `ts-java-spring-boot-realworld`.
- **Track 2 (Scale-Out)** — the same job fanned across the seven service modules in `petclinic-microservices`, and across the `quickapp-*` family.
- **Track 3 (Always-On)** — PR review, CI-failure repair, and scheduled drift reporting wired into `ts-java-spring-boot-realworld` and `petclinic-*`.

## Getting the Most from This Workshop

> **Devin works autonomously on its own machine.** Kick off a
> session and move on — you don't watch it work. You'll be notified
> when it opens a PR.

- **Overlap everything.** Start the next track's session while reviewing the previous track's PR. Nothing here is meant to be run serially by a waiting human.
- **Use Ask Devin as the research step** before you refine a prompt. A better second prompt is the core skill this workshop teaches.
- **Steer through PR comments.** Comment (general or inline) and Devin wakes up and iterates — this is the collaboration model, not a fallback.
- **Accept Knowledge suggestions.** Track 2 and Track 3 are dramatically better when Track 1's learnings were persisted.

## Table of Contents

- [Agenda](#agenda)
- [Track 1 — Delegate: Every-Day Development](#track-1)
  - [Lab 1A — Full-Stack Feature Handoff](#lab-1a)
  - [Lab 1B — Bug Fix with a Regression Test](#lab-1b)
  - [Lab 1C — Mid-Flow Handoff: Tests and Docs](#lab-1c)
- [Track 2 — Scale-Out: Parallel Agents](#track-2)
  - [Lab 2A — One Job, Seven Services](#lab-2a)
  - [Lab 2B — Fleet-Wide Lint and Warning Cleanup](#lab-2b)
  - [Lab 2C — Parallel Decomposition Across the QuickApp Family](#lab-2c)
- [Track 3 — Always-On: Event-Driven](#track-3)
  - [Lab 3A — Automated PR Review](#lab-3a)
  - [Lab 3B — CI Failure Analysis and Repair](#lab-3b)
  - [Lab 3C — Scheduled Drift and Incident Triage](#lab-3c)
- [Repos Required](#repos-required)
- [Facilitator Notes](#facilitator-notes)
- [Workshop Key Takeaways](#workshop-key-takeaways)

---

<a id="agenda"></a>

## Agenda

| Time | Activity |
|------|----------|
| 0:00–0:10 | Welcome, the three patterns, platform walkthrough |
| 0:10–0:20 | **Track 1:** kick off Lab 1A and Lab 1B |
| 0:20–0:45 | Ask Devin + DeepWiki on `timesheet-app` while sessions run |
| 0:45–1:10 | Review Track 1 PRs — leave comments, watch Devin iterate |
| 1:10–1:20 | Turn the Track 1 session into a playbook (feeds Track 2) |
| 1:20–1:30 | Break |
| 1:30–1:40 | **Track 2:** kick off Lab 2A (fan-out across seven service modules) |
| 1:40–2:10 | Review the child-session PRs as they land — compare consistency |
| 2:10–2:40 | **Track 3:** configure PR review + CI-failure automations, trigger them live |
| 2:40–3:00 | Wrap-up: which of your real workflows belongs in which pattern, Q&A |

> **Why tracks overlap.** In production a pod runs many sessions at
> once. The agenda deliberately has you reviewing one track's PRs
> while the next track's sessions are already running.

---

<a id="track-1"></a>

## Track 1 — Delegate: Every-Day Development

**The pattern:** the work an engineer hands off mid-flow. Small, well-defined, unblocking. This is the pattern with the shortest path to daily use, and it is where participants calibrate what a good prompt looks like.

<a id="lab-1a"></a>

### Lab 1A — Full-Stack Feature Handoff

**Value driver:** *A scoped feature crossing API, persistence, and UI — the task an engineer would otherwise context-switch into for half a day.*

- **Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Lab module:** [New Feature Development](../../../labs/application-development/new-feature-development.md)

Express backend in `backend/src/` (routes: `auth.js`, `clients.js`, `workEntries.js`, `reports.js`; middleware in `backend/src/middleware/`; schema in `backend/src/database/init.js`), React + TypeScript frontend in `frontend/src/` (pages: `WorkEntriesPage.tsx`, `ClientsPage.tsx`, `ReportsPage.tsx`, `DashboardPage.tsx`; API client in `frontend/src/api/client.ts`).

#### Paste into Devin

```
Add timesheet approval to timesheet-app, end to end.

1. Read `backend/src/routes/workEntries.js`,
   `backend/src/middleware/auth.js`, and
   `backend/src/database/init.js` first and follow the
   existing patterns.
2. Backend: add a status on work entries (draft →
   submitted → approved/rejected) with the schema change,
   endpoints to submit, approve, and reject, and the rule
   that only an approver may approve and an approved entry
   can no longer be edited.
3. Tests: extend `backend/src/__tests__/` to cover the
   valid transitions and every rejected transition.
4. Frontend: surface status on
   `frontend/src/pages/WorkEntriesPage.tsx` with a submit
   action, and add the approver's pending-approvals view,
   wired through `frontend/src/api/client.ts`.
5. In the PR description, list the state machine, the API
   changes, and anything a reviewer must check by hand.
```

#### Research with Ask Devin

- *"How does timesheet-app handle auth and roles today, and where would an approver role fit?"*
- *"Which existing tests in `backend/src/__tests__/` would break if work entries became immutable after approval?"*

#### Review & Give Feedback

- Check the transition guard is enforced server-side, not just hidden in the UI.
- Leave a PR comment asking for an audit trail of who approved what and when — a realistic scope addition mid-review.

#### Key Takeaways

- Well-scoped delegation includes the *rules*, not just the feature name — the state machine is what makes this reviewable.
- Naming the files to read first is the cheapest way to get output that matches your conventions.
- The PR is where the remaining scope conversation happens, and Devin participates in it.

---

<a id="lab-1b"></a>

### Lab 1B — Bug Fix with a Regression Test

**Value driver:** *The two-hour bug that never justifies interrupting feature work — handed off and returned as a PR with a failing-then-passing test.*

- **Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Lab modules:** [Fix Data Bug](../../../labs/application-development/fix-data-bug.md) · [Fix UI Bug](../../../labs/application-development/fix-ui-bug.md)

#### Paste into Devin

```
Audit the reporting path in timesheet-app for correctness
bugs and fix what you find.

1. Read `backend/src/routes/reports.js` and
   `frontend/src/pages/ReportsPage.tsx`, then look
   specifically for: date-range boundary handling
   (inclusive vs exclusive), timezone handling on entry
   dates, rounding of billable hours and amounts, and
   totals when a client has no entries.
2. For each issue, first add a test that fails, then fix
   it, then show the test passing.
3. Do not refactor beyond what the fixes require.
4. In the PR description, list each bug, the failing test
   that proves it, and the fix.
```

#### Key Takeaways

- "Find the bug" is a legitimate delegation when you name the *classes* of bug to look for.
- Test-first proof turns an unverifiable fix into a reviewable one.
- "Do not refactor beyond what the fixes require" is the constraint that keeps a hand-off PR small enough to merge.

---

<a id="lab-1c"></a>

### Lab 1C — Mid-Flow Handoff: Tests and Docs

**Value driver:** *The work that gets dropped when the sprint tightens. Ideal to hand off precisely because it is not on the critical path.*

- **Repository:** [ts-java-spring-boot-realworld](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld)
- **Lab modules:** [Unit Testing](../../../labs/testing-qa/unit-testing.md) · [API Documentation](../../../labs/technical-documentation/api-documentation.md)

Spring Boot REST + GraphQL app: `src/main/java/io/spring/api/`, `application/`, `core/`, `infrastructure/`, `graphql/`. The repo carries `.agents/` guidance and a Gradle coverage gate — point participants at both.

#### Paste into Devin

```
In ts-java-spring-boot-realworld, raise test coverage on
the weakest area and document the API surface.

1. Read the repo's `.agents/` guidance and `build.gradle`
   first, then report current coverage per package and pick
   the package where added tests would reduce the most
   risk. Say why.
2. Write tests there that assert behavior, including error
   and authorization paths — not getters.
3. Keep `./gradlew build` green, including Spotless
   formatting and the JaCoCo gate.
4. Then document the REST endpoints you touched: path,
   auth requirement, request/response shape, and error
   codes.
```

#### Key Takeaways

- Asking Devin to *choose and justify* the target teaches you where your risk actually sits.
- Repo-level agent guidance (`.agents/`, `AGENTS.md`) is how conventions survive across sessions and across engineers.
- The verification gate — build, formatter, coverage threshold — is what makes an unattended hand-off safe to merge.

---

### End of Track 1 — Capture the Method

Before the break, distill what just worked. This artifact is the input to Track 2.

```
Based on the session you just completed, write a playbook
another engineer could run against a different service to
get the same quality of result. Include the ordered
procedure, the verification commands, what must appear in
the PR description, and the cases where the engineer should
stop and escalate instead of continuing.
```

---

<a id="track-2"></a>

## Track 2 — Scale-Out: Parallel Agents

**The pattern:** hundreds of agents running the same job in parallel — bulk remediation, large-scale migrations, version upgrades across repos, codebase-wide refactors, lint cleanup at scale. Work that used to need a dedicated migration team.

The unit of scale is the repo, the service, or the module — not the engineer. Track 1 proved the job; this track multiplies it.

<a id="lab-2a"></a>

### Lab 2A — One Job, Seven Services

**Value driver:** *A version upgrade across a service estate: identical procedure, seven service modules, seven PRs, concurrently.*

- **Repository:** [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices)
- **Lab modules:** [Repetitive Framework Upgrades](../../../labs/migration-modernization/repetitive-framework-upgrades.md) · [Framework Upgrade](../../../labs/migration-modernization/framework-upgrade.md)

Independent Maven modules: `spring-petclinic-customers-service`, `spring-petclinic-vets-service`, `spring-petclinic-visits-service`, `spring-petclinic-api-gateway`, `spring-petclinic-config-server`, `spring-petclinic-discovery-server`, `spring-petclinic-admin-server`.

#### Paste into Devin

```
petclinic-microservices contains independent Spring Boot
service modules (customers, vets, visits, api-gateway,
config-server, discovery-server, admin-server).

1. Analyze the repo and the parent `pom.xml` to establish
   the shared dependency baseline and which modules deviate
   from it.
2. Create one child session per service module. Each child
   must: raise its dependency versions to the current
   baseline, fix breaking API changes, keep `./mvnw verify`
   passing for that module, and open its own PR describing
   what changed and what a reviewer must verify.
3. Give every child the same procedure so the PRs are
   comparable.
4. Report a summary table: module, child session, PR, build
   result, and anything a child had to escalate.
```

#### Research with Ask Devin

- *"Which petclinic-microservices modules share dependencies through the parent POM, and which upgrades would ripple across all of them?"*
- *"Which module is the riskiest to upgrade, and why?"*

#### Review & Give Feedback

- Read two child PRs side by side: is the approach consistent? Inconsistency tells you the procedure was underspecified, not that the agents were wrong.
- Comment on one child PR — note that only that session iterates, which is exactly the isolation you want.

#### Key Takeaways

- Fan-out quality is a function of procedure quality: a vague parent prompt produces seven differently-shaped PRs.
- Independent PRs mean independent review and independent merge — one problem service doesn't block the other six.
- This is the shape most portfolio-wide jobs take: bulk CVE remediation, framework upgrades, codebase-wide refactors.

---

<a id="lab-2b"></a>

### Lab 2B — Fleet-Wide Lint and Warning Cleanup

**Value driver:** *Thousands of warnings nobody will ever be funded to fix, cleared in parallel with mechanical, reviewable diffs.*

- **Repositories:** [petclinic-backend](https://github.com/Cognition-Partner-Workshops/petclinic-backend), [petclinic-rest-api](https://github.com/Cognition-Partner-Workshops/petclinic-rest-api), [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular)
- **Lab module:** [Linting & Static Analysis](../../../labs/testing-qa/linting-static-analysis.md)

#### Paste into Devin

```
Run a lint and warning cleanup across three repos:
petclinic-backend, petclinic-rest-api, and
petclinic-angular. `petclinic-angular` has ESLint
(`.eslintrc.json`) and an `AGENTS.md` — follow it.

1. For each repo, inventory current warnings by category
   and count.
2. Create one child session per repo. Each child fixes only
   the mechanical categories (unused imports, deprecated
   API usage, formatting, obvious null-safety), leaves
   anything requiring a behavior decision listed in its PR
   description as follow-up, and keeps the build and tests
   green.
3. Do not suppress warnings to make counts drop, and do not
   change behavior.
4. Report warnings before and after per repo.
```

#### Key Takeaways

- The instruction that matters most is the boundary: *fix mechanical, escalate judgment*. Without it, cleanup silently becomes refactoring.
- "Do not suppress to make the number drop" is the guardrail that keeps the metric honest.
- Warning debt is per-repo independent — perfectly parallel, and near-zero review risk when scoped this way.

---

<a id="lab-2c"></a>

### Lab 2C — Parallel Decomposition Across the QuickApp Family

**Value driver:** *A decomposition where each bounded context is its own independent workstream — the classic case for concurrent agents.*

- **Repositories:** [quickapp-monolith](https://github.com/Cognition-Partner-Workshops/quickapp-monolith) (source) → [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices), [quickapp-microfrontends](https://github.com/Cognition-Partner-Workshops/quickapp-microfrontends), [quickapp-iac](https://github.com/Cognition-Partner-Workshops/quickapp-iac) (target state)
- **Lab module:** [.NET Monolith Decomposition](../../../labs/migration-modernization/dotnet-monolith-decomposition.md)

The monolith is `QuickApp.Core` / `QuickApp.Server` / `quickapp.client`. The target-state repos show one possible decomposition — `quickapp-microservices` has `src/Services/{Identity,Customer,Order,Product,Notification}` behind `src/ApiGateway`, with `src/Shared/Shared.Contracts`; `quickapp-microfrontends` holds `apps/` and `libs/`; `quickapp-iac` holds `charts/` and `environments/`. Have Devin propose its own boundaries *before* looking at them.

#### Paste into Devin

```
Analyze quickapp-monolith (`QuickApp.Core`,
`QuickApp.Server`, `quickapp.client`) and plan a parallel
decomposition.

1. Map module and data dependencies, and identify bounded
   contexts with the coupling evidence for each: shared
   entities, cross-module calls, shared DB tables.
2. Rank the contexts by extraction difficulty and say which
   are independent enough to extract concurrently and which
   must be sequenced behind another.
3. For the concurrent set, create one child session per
   context. Each produces the service skeleton, its API
   contract, its data ownership decision, and tests — one
   PR per context.
4. Then compare your boundaries with the target state in
   quickapp-microservices (`src/Services/`) and write
   `DECOMPOSITION_PLAN.md` explaining where you differ and
   why yours is or is not better.
```

#### Key Takeaways

- Parallelism has a dependency structure: the valuable output is knowing what *cannot* run concurrently.
- Having agents propose boundaries before revealing the reference architecture makes the comparison a real design discussion.
- Shared contracts (`src/Shared/Shared.Contracts`) are where concurrent extractions collide — decide them centrally, before fan-out.

---

<a id="track-3"></a>

## Track 3 — Always-On: Event-Driven

**The pattern:** agents that fire automatically off PRs, alerts, tickets, or schedules. Nobody starts these sessions. This is the pattern that changes cycle time rather than throughput, and it is last because it should automate a procedure you have already proven in Tracks 1 and 2.

<a id="lab-3a"></a>

### Lab 3A — Automated PR Review

**Value driver:** *Every PR gets a consistent first-pass review within minutes, so human review starts from a higher baseline.*

- **Repository:** [ts-java-spring-boot-realworld](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld)
- **Lab module:** [PR Review Automation](../../../labs/devops-cicd/pr-review-automation.md)

The repo already has workflows in `.github/workflows/` (`gradle.yml`, `dependency-check.yml`, `security-issue-automation.yml`) — read them before adding a fourth.

#### Paste into Devin

```
In ts-java-spring-boot-realworld, define an automated
first-pass review for every incoming PR.

1. Read `.github/workflows/` and the repo's `.agents/`
   guidance first, then write the review criteria this repo
   actually needs: layering rules between `api`,
   `application`, `core`, and `infrastructure`; auth checks
   on new endpoints; Spotless formatting; test coverage on
   changed code; and no new dependency without a
   justification.
2. Encode it as a reusable review playbook, with severity
   levels and an explicit rule that style nits are noted,
   not blocking.
3. Add the workflow that triggers the review on PR open and
   on push to an open PR, and posts findings as PR
   comments.
4. Include a worked example: run the criteria against a
   recent commit and show the review it would have produced.
```

**Try it live:** open a small PR against the repo (or reuse a Track 1 PR) and watch the review land.

#### Key Takeaways

- Automated review is only useful when its criteria are *this repo's* rules, not generic advice.
- Severity levels keep the automation from becoming noise that reviewers learn to ignore.
- The human still decides; the agent ensures nothing obvious reaches them unflagged.

---

<a id="lab-3b"></a>

### Lab 3B — CI Failure Analysis and Repair

**Value driver:** *A red build on main is an event with a known shape. An agent triaging it immediately is strictly faster than the next engineer to notice.*

- **Repositories:** [ts-java-spring-boot-realworld](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld), [petclinic-backend](https://github.com/Cognition-Partner-Workshops/petclinic-backend)
- **Lab module:** [CI Failure Resolution](../../../labs/devops-cicd/ci-failure-resolution.md)

#### Paste into Devin

```
Design and implement a CI-failure first responder for
ts-java-spring-boot-realworld.

1. Read `.github/workflows/gradle.yml` and classify the
   ways this build can fail: compile error, test failure,
   flaky test, Spotless violation, coverage gate,
   dependency resolution, environment/timeout.
2. For each class, define the automated response —
   including the classes where the correct response is to
   diagnose and escalate rather than push a fix. Flaky
   tests must never be silently retried into green.
3. Implement the trigger on workflow failure, and have the
   session post a diagnosis comment first, then a fix PR
   only for the classes you deemed safe.
4. Prove it: deliberately break the build in a branch (a
   formatting violation and a genuine test failure), let
   the automation run, and show both responses.
```

#### Key Takeaways

- The valuable design work is deciding where the agent must *stop* — auto-fixing a flaky test is how a team loses trust in automation.
- Diagnose-then-fix gives reviewers the reasoning, not just a diff.
- Same pattern, other triggers: scanner findings, dependency alerts, failed deployments.

---

<a id="lab-3c"></a>

### Lab 3C — Scheduled Drift and Incident Triage

**Value driver:** *Recurring hygiene and inbound tickets both arrive on a clock or a queue, never on an engineer's plan.*

- **Repositories:** [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices), [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app), [timesheet-infra](https://github.com/Cognition-Partner-Workshops/timesheet-infra)
- **Lab modules:** [Incident Response Triage](../../../labs/observability-sre/incident-response-triage.md) · [Configuration Management & Feature Flags](../../../labs/devops-cicd/configuration-management-feature-flags.md)

`timesheet-infra` holds `terraform/`, `lambda/`, and `docker/` — useful for showing that drift reporting is not only about application dependencies.

#### Build these in the Automations tab

The other labs start a session. This one is configured in the platform, so the trigger belongs to the org rather than to you:

1. Open **Automations** in the left sidebar of the organization page.
2. Rather than filling in the trigger and action form by hand, use the option to generate the automation with Devin — describe what you want in the chat input and Devin drafts the trigger, conditions, prompt, and session config.
3. Paste **Automation 1** below into that input, then review the draft: check the schedule, the repo scope, and the guardrails before saving.
4. Repeat with **Automation 2**.
5. For each one, note where the human checkpoint sits — if you can't point to it, the automation isn't ready for a client estate.

**Automation 1 — weekly drift report**

```
On a weekly schedule, produce a dependency and
infrastructure drift report across petclinic-microservices,
timesheet-app, and timesheet-infra.

1. Report dependency versions behind current, known
   advisories, and the Terraform provider and module
   versions in `timesheet-infra/terraform/`.
2. Output one report plus a ranked, sized remediation queue
   — the queue is the input to a Track 2 fan-out, so each
   item must be small enough for one child session.
3. Do not change code. Reporting only.
```

**Automation 2 — ticket-triggered triage**

```
When a new bug report arrives for timesheet-app, triage it.

1. Reproduce the reported behavior against `backend/src/`
   and `frontend/src/`.
2. Identify the responsible code path, assess severity, and
   post the findings back on the ticket.
3. Ask for confirmation before fixing anything beyond a
   trivial change.
```

#### Key Takeaways

- Scheduled reporting is what keeps a Track 2 fan-out fed with real, prioritized work.
- Triage automation earns its place by compressing time-to-diagnosis, before it ever writes a fix.
- An always-on automation needs an explicit human checkpoint — that is what makes it deployable in a regulated estate.

---

<a id="repos-required"></a>

## Repos Required

| Track | Lab | Repository |
|---|---|---|
| 1 | 1A, 1B | [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) |
| 1 | 1C | [ts-java-spring-boot-realworld](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld) |
| 2 | 2A | [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices) |
| 2 | 2B | [petclinic-backend](https://github.com/Cognition-Partner-Workshops/petclinic-backend), [petclinic-rest-api](https://github.com/Cognition-Partner-Workshops/petclinic-rest-api), [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular) |
| 2 | 2C | [quickapp-monolith](https://github.com/Cognition-Partner-Workshops/quickapp-monolith), [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices), [quickapp-microfrontends](https://github.com/Cognition-Partner-Workshops/quickapp-microfrontends), [quickapp-iac](https://github.com/Cognition-Partner-Workshops/quickapp-iac) |
| 3 | 3A, 3B | [ts-java-spring-boot-realworld](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld), [petclinic-backend](https://github.com/Cognition-Partner-Workshops/petclinic-backend) |
| 3 | 3C | [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices), [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app), [timesheet-infra](https://github.com/Cognition-Partner-Workshops/timesheet-infra) |

---

<a id="facilitator-notes"></a>

## Facilitator Notes

- **One lab per track is enough.** Nine labs are listed so a room can split; a single participant should do 1A → 2A → 3A and go deep rather than wide.
- **Track 1 feeds Track 2.** The playbook step at the end of Track 1 is not optional — Lab 2A is noticeably worse without it, and that contrast is the lesson.
- **Track 2 needs the child-session limit checked** before the event, so a seven-module fan-out isn't throttled mid-agenda.
- **Track 3 needs something to react to.** Have participants reuse their own Track 1 PR as the trigger, or pre-stage a deliberately broken branch for Lab 3B.
- **Timing risk:** Lab 2C is the longest. If the room is behind, run 2A instead and mention 2C as take-home.
- **Wrap-up anchor:** ask each participant to place three items from their real backlog into the three patterns, with a count for the Scale-Out one. That count is the business case.

---

<a id="workshop-key-takeaways"></a>

## Workshop Key Takeaways

- **Delegate first.** Small, well-specified hand-offs are the pattern a team adopts on day one — and the prompt discipline learned there is what makes the other two patterns work.
- **Scale-out multiplies a proven procedure, not a hope.** Fan-out quality equals procedure quality; the parent's specification is the whole game.
- **Always-on changes cycle time, not throughput.** Nobody starting the session is the point — and every automation needs its guardrails and human checkpoint written down.
- **Boundaries beat capability.** In every track, the highest-value instruction was where the agent must stop and escalate.
- **Context compounds.** Playbooks, Knowledge notes, and repo-level `AGENTS.md`/`.agents/` guidance are why session fifty is better than session one.
