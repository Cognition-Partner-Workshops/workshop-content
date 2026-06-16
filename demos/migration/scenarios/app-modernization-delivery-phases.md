# App Modernization — Delivery Phases for SI / Professional Services Engagements

Systems integrators and professional services firms deliver app modernization as
a multi-phase engagement. The client has a portfolio of legacy applications —
monoliths, aging frameworks, proprietary runtimes, accumulated tech debt — and
needs them moved to a modern target state under a fixed timeline and budget.

The constraint is rarely strategy. Most organizations already know *what* needs
to modernize. The constraint is **engineering capacity to execute at the scale
the portfolio demands** — and that is where each delivery phase can absorb
autonomous engineering agents to multiply throughput without multiplying
headcount.

This document walks through six delivery phases that map to how modernization
engagements typically run in practice, and shows where Devin operates within
each one.

<a id="toc"></a>
## Table of Contents

- [The Engagement Shape](#engagement-shape)
- [Phase 1 — Discovery & Assessment](#phase-1)
- [Phase 2 — Architecture & Planning](#phase-2)
- [Phase 3 — Foundation & Scaffolding](#phase-3)
- [Phase 4 — Iterative Conversion](#phase-4)
- [Phase 5 — Validation & Quality Gate](#phase-5)
- [Phase 6 — Cutover & Stabilization](#phase-6)
- [How the Phases Compound](#compound)
- [Key Takeaways](#key-takeaways)

---

<a id="engagement-shape"></a>
## The Engagement Shape

A typical SI modernization engagement follows this arc:

```
Discovery       →  Architecture    →  Foundation
(weeks 1-2)        (weeks 2-4)        (weeks 3-6)
    ↓                   ↓                  ↓
Iterative Conversion  →  Validation  →  Cutover
(weeks 4-N)              (ongoing)       (final sprint)
```

Phases overlap. Discovery findings feed architecture decisions. Foundation work
starts before the full architecture is finalized. Conversion begins on
well-understood components while the team is still planning the complex ones.
Validation runs continuously — not as a phase gate at the end.

The delivery team typically includes:

| Role | Responsibility |
|------|---------------|
| **Engagement lead** | Client relationship, scope, timeline, budget |
| **Solution architect** | Target architecture, migration strategy, technology decisions |
| **Tech leads** | Domain expertise, code review, escalation handling |
| **Delivery engineers** | Implementation — the largest headcount and the capacity bottleneck |
| **QA / validation** | Test strategy, parity verification, acceptance criteria |

Devin operates as a force multiplier for the delivery engineers — it absorbs
the high-volume, well-defined conversion work so that architects and tech leads
can focus on the decisions that require human judgment, and the engagement
delivers on a timeline that would otherwise require twice the team.

---

<a id="phase-1"></a>
## Phase 1 — Discovery & Assessment

**Objective:** Produce a complete inventory of the legacy estate — applications,
dependencies, data flows, integration points, technical debt hotspots — so the
team can size the engagement and sequence the work.

**What the delivery team does:**
- Interviews stakeholders to map business criticality and risk
- Identifies external integration contracts (APIs, file feeds, batch jobs)
- Classifies applications by modernization strategy (replatform, refactor,
  rewrite, retire)

**What Devin does:**

Devin reads the legacy codebases and produces structured analysis artifacts.
This is system-understanding work — the kind that would otherwise require an
engineer to spend days reading unfamiliar code.

```
Analyze the legacy codebase in <repo-name>. Produce:

1. SYSTEM_INVENTORY.md — list every module, service,
   and batch job with a one-line description of its
   purpose
2. DEPENDENCY_MAP.md — internal dependencies (which
   modules call which), external dependencies (third-
   party libraries, APIs, databases), and data flow
   between components
3. TECH_DEBT_REPORT.md — deprecated APIs, known
   vulnerabilities, dead code paths, and framework
   version gaps
4. COMPLEXITY_ASSESSMENT.md — LOC per module, cyclomatic
   complexity hotspots, and test coverage gaps

Use the repo's actual file structure and package
manifests. Do not speculate — report only what the
code shows.
```

**Phase output:** A discovery package that the solution architect uses to
define the target architecture and the engagement lead uses to scope the
statement of work. What previously took 2-3 weeks of manual code archaeology
compresses to days — Devin typically processes the full file estate rather than a sample.

> **Scale pattern:** For multi-application portfolios, spawn a child agent per
> application. Each child produces the same discovery artifact set. The parent
> agent aggregates the results into a portfolio-level summary. A 30-application
> estate that would require 30 engineer-weeks of analysis runs in parallel.

---

<a id="phase-2"></a>
## Phase 2 — Architecture & Planning

**Objective:** Define the target architecture, migration strategy per component,
dependency sequencing, and the verification approach that will prove each
conversion is correct.

**What the delivery team does:**
- Solution architect designs the target state (microservices, API contracts,
  data schemas, infrastructure topology)
- Tech leads sequence components by dependency graph — leaf nodes first,
  shared libraries before consumers
- QA defines the parity verification strategy: what "correct" means for each
  component (contract tests, golden-file comparison, data reconciliation)

**What Devin does:**

Devin generates planning artifacts from the discovery outputs and the target
architecture decisions. The architect defines the direction; Devin does the
mapping work.

```
Using SYSTEM_INVENTORY.md and DEPENDENCY_MAP.md from the
discovery phase, produce a migration plan:

1. COMPONENT_MAPPING.md — for each legacy module, the
   target service/package in the new architecture, the
   migration strategy (replatform / refactor / rewrite),
   and the estimated complexity (low / medium / high)
2. MIGRATION_SEQUENCE.md — dependency-ordered conversion
   sequence, identifying which components can be
   converted in parallel and which must be serialized
3. INTERFACE_CONTRACTS.md — for each module boundary
   that will become a service boundary, document the
   current implicit interface (function signatures,
   data shapes, error codes) as an explicit contract

Reference the actual code in <repo-name> for all
interface details. Flag any circular dependencies or
shared mutable state that will require architectural
decisions.
```

**Phase output:** A migration roadmap the engagement lead can map to sprints
and resource allocation. The architect validates the component mapping; the
tech leads validate the sequencing. Devin's artifacts are inputs to human
decision-making — not replacements for it.

---

<a id="phase-3"></a>
## Phase 3 — Foundation & Scaffolding

**Objective:** Build the target-state infrastructure before conversion begins —
project scaffolds, CI/CD pipelines, test harnesses, container configurations,
and the verification loop that every subsequent conversion will run through.

**What the delivery team does:**
- Provisions cloud infrastructure (Kubernetes clusters, managed databases,
  container registries)
- Establishes CI/CD pipelines with quality gates
- Defines coding standards, linting rules, and PR review policies for the
  target codebase
- Sets up the verification harness (contract tests, integration tests,
  data reconciliation scripts)

**What Devin does:**

Devin generates the scaffolding code — the boilerplate that follows established
patterns and does not require architectural decisions.

```
Create the project scaffold for the <target-service>
microservice in <target-repo>:

1. Project structure following the patterns in
   <reference-service> (same framework, same layout)
2. Dockerfile with multi-stage build matching the
   base image and build conventions in
   infrastructure/docker/
3. CI pipeline configuration (.github/workflows/ or
   azure-pipelines.yml) matching the existing pipeline
   patterns
4. Health check endpoint, structured logging, and
   externalized configuration following the platform
   conventions documented in <platform-standards-repo>
5. Contract test stubs using the interface contracts
   from INTERFACE_CONTRACTS.md

The scaffold should compile and pass CI with empty
implementations (stubs that return 501 Not Implemented).
```

**Phase output:** A target-state codebase that compiles, deploys, and runs
through CI — with empty implementations ready to receive converted business
logic. The verification harness is in place and running (all tests fail against
stubs, which is correct — they will pass as implementations land).

> **Playbook pattern:** The scaffold prompt above becomes a Devin Playbook.
> When the team needs to scaffold 15 microservices, each child agent runs the
> same playbook against a different target. The scaffolding is consistent
> because the process is codified, not ad-hoc.

---

<a id="phase-4"></a>
## Phase 4 — Iterative Conversion

**Objective:** Convert legacy components to the target architecture, one unit
at a time, with each conversion verified before the next begins. This is the
longest phase and the one where capacity constraints typically cause schedule
overruns in traditional engagements.

**What the delivery team does:**
- Tech leads review PRs and make judgment calls on edge cases
- Solution architect handles cross-cutting concerns that span multiple
  conversions (shared libraries, authentication, data migration)
- Engagement lead tracks velocity against the migration sequence and
  adjusts scope or resources

**What Devin does:**

Devin executes the conversion for each component. The playbook encodes the
methodology; Devin applies it to each target.

```
Convert the <legacy-module> from <source-repo> to its
target implementation in <target-repo>/<target-service>.

Follow the conversion playbook:
1. Read the legacy source code and the interface
   contract from INTERFACE_CONTRACTS.md
2. Implement the business logic in the target
   framework, following the patterns established in
   <reference-service>
3. Write unit tests covering the core logic paths
4. Run the contract test suite — the tests must pass
   against your implementation
5. Run the full CI pipeline and resolve any lint,
   type-check, or build failures

Reference the legacy code for behavioral accuracy.
Use the interface contract as the source of truth for
input/output shapes.
```

**Phase output:** A stream of PRs, each converting one component, each
verified by the contract test suite and CI pipeline. Tech leads review the
PRs — minutes of review effort per component instead of hours of
implementation effort.

> **Parallel conversion:** Run multiple child agents simultaneously, each
> converting a different component from the migration sequence. The team's
> effort shifts from implementation to review — tech leads verify PRs
> while the next batch of conversions is already in progress.

---

<a id="phase-5"></a>
## Phase 5 — Validation & Quality Gate

**Objective:** Prove that the converted system is functionally equivalent to
the legacy system and meets the client's acceptance criteria. This phase runs
continuously from Phase 4 onward — it is not a big-bang gate at the end.

**What the delivery team does:**
- QA defines acceptance test scenarios beyond contract tests (end-to-end
  workflows, performance benchmarks, data integrity checks)
- Tech leads investigate and resolve discrepancies flagged by automated
  validation
- Engagement lead presents validation evidence to the client's governance
  board

**What Devin does:**

Devin runs validation campaigns — systematic, thorough checks that would
be prohibitively time-consuming to execute manually.

```
Run the full validation suite for <target-repo>:

1. Execute all contract tests and report pass/fail
   per endpoint
2. Run data reconciliation between legacy database
   snapshots and target database state — compare row
   counts, checksums, and sample records for each
   table documented in DATA_MAPPING.md
3. Execute performance baseline tests: measure
   response time for each endpoint under <N>
   concurrent requests and compare against the
   legacy baseline in PERFORMANCE_BASELINE.md
4. Generate VALIDATION_REPORT.md summarizing results,
   flagging any discrepancies, and linking to the
   specific test output for each finding

Report discrepancies with enough detail for a
developer to investigate: expected value, actual
value, test command to reproduce.
```

**Phase output:** A validation report the engagement lead can present to the
client — evidence-backed, not anecdotal. Discrepancies are flagged early and
tracked to resolution. The SI team demonstrates rigor because the validation
is thorough, not sampled.

> **Continuous validation:** Configure a Devin Automation to run the
> validation suite on every PR merge to `main`. The validation report
> updates automatically as conversions land. The client has real-time
> visibility into migration progress and quality.

---

<a id="phase-6"></a>
## Phase 6 — Cutover & Stabilization

**Objective:** Transition production traffic from the legacy system to the
modernized system, monitor for regressions, and resolve post-cutover issues
under SLA.

**What the delivery team does:**
- Plans the cutover sequence (blue-green, canary, feature flags, DNS
  switchover) based on the client's risk tolerance
- Coordinates with the client's operations team for the production switch
- Provides on-call support during the stabilization window

**What Devin does:**

Devin operates as an incident response agent during stabilization — monitoring
for issues and producing fixes faster than a human on-call rotation.

```
Monitor the deployment of <target-service> in the
staging environment. If any health check, log error
rate, or integration test failure occurs:

1. Investigate: query application logs, identify the
   failing component, and trace the root cause
2. Classify: determine whether this is a regression
   from the conversion (fixable) or a pre-existing
   issue in the legacy system (document and escalate)
3. If fixable: implement the fix, run the contract
   test suite, and push a PR targeting the release
   branch
4. If not fixable: open a GitHub Issue with the
   investigation findings, tagged for human review

Log all investigation steps in the PR or Issue for
the client's audit trail.
```

**Phase output:** Rapid incident response during the highest-risk window of
the engagement. The SI team's SLA commitments are backed by an agent that
responds immediately — not after the on-call engineer finishes their current
meeting.

> **Event-driven pattern:** Configure a Devin Automation triggered by
> alerting (PagerDuty, Azure Monitor, CloudWatch) to start investigation
> sessions automatically. The agent queries logs via MCP integrations,
> identifies the root cause, and opens a fix PR — often before the on-call
> engineer has context-switched to the incident.

---

<a id="compound"></a>
## How the Phases Compound

The investment in each phase pays forward into subsequent phases:

| Phase | What it produces | How later phases use it |
|-------|-----------------|----------------------|
| **Discovery** | System inventory, dependency map, tech debt report | Architecture uses these to define the target state and sequence the work |
| **Architecture** | Component mapping, migration sequence, interface contracts | Foundation uses these to scaffold targets; Conversion uses contracts as the verification source of truth |
| **Foundation** | Scaffolded projects, CI pipelines, test harnesses, Devin playbooks | Conversion runs the playbooks against each target; Validation runs the harness continuously |
| **Conversion** | PRs with verified implementations | Validation aggregates results; Cutover deploys the verified artifacts |
| **Validation** | Evidence-backed quality reports | Cutover uses these as go/no-go criteria; the client's governance board uses them for sign-off |
| **Cutover** | Production-running modernized system | Stabilization monitors it; the engagement closes with documented evidence |

The **shared context layer** — Knowledge notes encoding standards, Playbooks
encoding methodology, MCP integrations connecting tools, Environment
blueprints pre-configuring build environments — is a one-time investment that
compounds across conversion sessions. The 50th conversion typically runs
faster than the 1st because the agent has accumulated context from
preceding runs.

For the SI firm, this means:
- **Faster delivery** — the engagement timeline compresses because conversion
  is parallelized and verification is automated
- **Senior focus shifts** — architects and tech leads spend time on
  architecture and client communication, not bulk implementation
- **Repeatable methodology** — the playbooks and scaffolds transfer to the
  next engagement, reducing ramp-up time
- **Demonstrable quality** — conversions typically produce a PR, a test
  suite, and a validation report. The audit trail is automatic

---

<a id="key-takeaways"></a>
## Key Takeaways

- **App modernization is a capacity problem, not a strategy problem.** Most
  organizations know what needs to modernize — they lack the engineering
  hours to execute at portfolio scale. Devin absorbs the high-volume
  conversion work so the delivery team can focus on architecture, review,
  and client engagement.

- **Each delivery phase has a Devin operating mode.** Discovery uses
  system-understanding prompts. Architecture uses planning prompts.
  Foundation uses scaffold playbooks. Conversion uses conversion playbooks
  with child agents for parallelism. Validation uses automation-triggered
  test campaigns. Cutover uses event-driven incident response.

- **The verification loop is the quality backbone.** Every conversion runs
  through the same contract test suite and CI pipeline. The client sees
  evidence — not promises — that each component works correctly in the
  target architecture.

- **The shared context layer compounds across engagements.** Playbooks,
  Knowledge notes, and Environment blueprints encode the methodology once
  and apply them consistently across conversions — reducing ramp-up
  time when the same patterns apply to new codebases.

- **Human judgment stays where it matters.** Architects make technology
  decisions. Tech leads review edge cases. The engagement lead manages
  scope and client relationships. Devin does the implementation work that
  scales linearly with portfolio size — the work that determines whether
  the engagement finishes on time and on budget.
