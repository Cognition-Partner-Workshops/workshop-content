# Workshop Track: Solutions

## Overview

| | |
|---|---|
| **Focus** | Platform architecture, SDLC integration design, live walkthrough delivery, automation topology, and full platform configuration — everything a Solutions Engineer or Architect needs to design and present Devin in client-facing engagements |
| **Duration** | 8-10 hours (self-paced across multiple sessions) |
| **Audience** | Solutions Engineers and Architects at SI partner firms who design systems and operational processes within the SDLC, and who need to present Devin's full capabilities in client-facing simulated environments |
| **Format** | Reading + knowledge checks + design challenges + live walkthrough practice |
| **Prerequisite** | [Foundations workshop](../../foundations/) (Level 1) |

## Workshop Narrative

This track transforms you from someone who understands Devin's capabilities (Foundations) into someone who can **design** Devin integrations for client environments and **deliver** live walkthroughs that demonstrate real value. Solutions Engineers are the bridge between platform capability and client adoption — you must know the architecture cold when fielding questions, map Devin's patterns to the client's SDLC, and recover gracefully when things go off-script.

The first half covers **knowledge** — platform architecture internals, SDLC phase mapping, and automation topology design. The second half covers **execution** — running live walkthroughs end-to-end, configuring a complete Devin organization from scratch, and handling unexpected outcomes during client-facing sessions.

## Getting the Most from This Workshop

> **This is a design-and-delivery workshop.** Unlike hands-on labs where you paste prompts and review PRs, this workshop builds the knowledge and judgment you need to architect Devin solutions and present them convincingly. Each section ends with knowledge checks and design exercises — take them seriously, as they mirror the questions clients will ask.

Tips:
- **Read the source material first.** Each section references files in `shared/general-themes/` and `demos/`. Read those before attempting the knowledge checks.
- **Practice walkthrough delivery out loud.** Section 4 includes delivery scripts. Practice explaining what is happening while Devin runs — this is the core skill clients evaluate you on.
- **Build the simulated environment.** Section 6 walks you through configuring a complete Devin org. Do this hands-on — the muscle memory matters when you are configuring a client's environment live.
- **Use the design challenges as preparation.** The end-of-workshop design challenge mirrors the kind of architecture proposal you will deliver in real engagements.

---

<a id="toc"></a>
## Table of Contents

- [1. Overview](#overview)
- [2. Platform Architecture Mastery](#platform-architecture-mastery)
- [3. SDLC Integration Design](#sdlc-integration-design)
- [4. Live Walkthrough Delivery](#live-walkthrough-delivery)
- [5. Automation Topology Design](#automation-topology-design)
- [6. Full Platform Configuration](#full-platform-configuration)
- [7. Handling Unexpected Outcomes](#handling-unexpected-outcomes)
- [Design Challenge: End-to-End Engagement Architecture](#design-challenge-end-to-end-engagement-architecture)
- [Self-Assessment Rubric for Walkthrough Delivery](#self-assessment-rubric-for-walkthrough-delivery)
- [Gaps & Future Content](#gaps--future-content)

---

<a id="platform-architecture-mastery"></a>
## 2. Platform Architecture Mastery

*Reference: [`shared/general-themes/architecture-strengths.md`](../../../shared/general-themes/architecture-strengths.md)*

This section covers what you must know cold when fielding client questions about how Devin works, where their data goes, and how security is maintained.

### 2.1 Clean-Room Execution Model

Every Devin session runs on its own isolated virtual machine. This is not a container sharing a kernel with other workloads — it is a full Linux VM with:

- **No lateral movement** — sessions cannot access other sessions' resources, data, or credentials
- **Ephemeral by default** — the VM is destroyed after the session completes; no persistent state leaks between tasks
- **Reproducible from scratch** — every session boots from the same base image (configured via environment blueprints), eliminating environment drift
- **Service account identity** — Devin authenticates using scoped credentials provisioned by the organization, not individual user tokens

**What clients ask:**
- *"Where does our code go?"* → Cloned onto the session VM, processed in isolation, pushed back via Git. The VM is ephemeral.
- *"Can one session access another session's data?"* → No. Each session has its own VM, its own filesystem, its own network namespace.
- *"What about secrets?"* → Injected at session start via the platform's secrets management layer. Never embedded in prompts, logs, or code.

### 2.2 The Shared Context Layer

While each session's runtime is isolated, Devin does not start from scratch. A persistent configuration layer flows into every session:

| Component | What It Provides | Configured By |
|-----------|-----------------|---------------|
| **Environment blueprints** | Pre-built VM images with dependencies, runtimes, tools, and startup scripts | Platform team (once) |
| **Knowledge notes** | Persistent context — coding standards, architecture decisions, domain glossary — retrieved automatically based on task relevance | Engineers (iteratively) |
| **Playbooks** | Repeatable step-by-step procedures encoding institutional methodology | Senior engineers / architects |
| **MCP servers** | Pre-configured integrations (Jira, Datadog, Confluence, Azure DevOps) available to every session | Platform team (once) |
| **Secrets** | Scoped credentials (API keys, service account tokens) injected at session start | Security / platform team |
| **Git connections** | Repository access configured at the org level — every session can clone and push immediately | Admin (once) |

**The design insight:** This gives you both **clean-room isolation for security** and a **shared context layer for productivity**. Each worker VM is sandboxed, but the organization's accumulated knowledge and configuration flow in automatically.

### 2.3 Session Lifecycle

Sessions move through a deliberate lifecycle optimized for efficiency:

```
Active → Waiting for Feedback → Hibernated → Resumed
```

| State | What Happens | Compute Cost |
|-------|-------------|--------------|
| **Active** | Devin executes tasks, runs builds, writes code | Full |
| **Waiting for feedback** | PR opened or question asked; monitoring for comments and CI results | Minimal |
| **Hibernated** | VM state snapshotted, compute released after inactivity | None |
| **Resumed** | New feedback arrives (PR comment, CI result, user message); VM restored from snapshot with full context | Full |

**Why this matters for clients:**
- Sessions do not waste compute while waiting for human review
- Multi-day iterations (back-and-forth PR reviews, blocked dependencies) are natural — context is preserved across the full task lifecycle
- Cost is proportional to active work time, not wall-clock time

### 2.4 The Verification Model

Devin verifies its own work through a layered approach:

1. **Local testing** — Unit tests, integration tests, linting, and type checking run on the session VM before opening a PR
2. **CI monitoring** — After pushing, Devin watches CI checks, reads failure logs, diagnoses issues, pushes fixes, and re-checks — up to multiple iterations
3. **PR as the review gate** — Devin never merges its own work. The PR is the handoff point where humans (or Devin Review) inspect the diff
4. **External system validation** — Devin connects to staging environments, external test systems, or cloud services to validate integration-level outcomes

**Client implication:** The merge decision is always human. Devin proposes; humans approve. This fits existing governance and compliance models without modification.

### 2.5 Team Integration

Devin operates as a team member within existing workflows:

- **Organizational configuration** — One engineer configures the shared context layer; every subsequent session benefits
- **PR-based communication** — Multiple team members comment on the same Devin PR; Devin reads and responds to feedback from any reviewer
- **Session continuity** — Context is preserved across hibernation/resume cycles. No re-reading from scratch when feedback arrives
- **Audit trail** — Every session has a complete log of actions, decisions, and outputs

---

### Knowledge Checks — Platform Architecture

**Knowledge Check 2.1**
Q: A client asks: "If Devin has access to our production database credentials, could a session accidentally leak those credentials to another project?" How do you respond?
A) "Credentials are shared across sessions for efficiency"
B) "Each session runs in an isolated VM with scoped secrets — credentials injected into one session are not accessible to any other session"
C) "We encrypt credentials at rest, so they cannot be leaked"
D) "Devin does not support database access"
*Answer: B — The clean-room execution model ensures each session has its own isolated VM. Secrets are injected per-session through the platform's secrets management layer, scoped to the resources that session needs.*

**Knowledge Check 2.2**
Q: A client has 20 engineers. They are concerned about configuration overhead — does each engineer need to set up Devin independently?
A) "Yes, each engineer configures their own Devin environment"
B) "No — the shared context layer (blueprints, Knowledge, Playbooks, MCP, Secrets, Git connections) is configured once at the org level and flows into every session from any team member"
C) "Only the first engineer needs to configure it, then they share their credentials"
D) "Devin requires no configuration"
*Answer: B — The shared context layer is the key design insight. One engineer invests in configuration; every subsequent session — from any team member — inherits it automatically.*

**Knowledge Check 2.3**
Q: What happens when a Devin session opens a PR and waits 3 days for human review?
A) "The session times out and is lost"
B) "The session continues consuming compute while waiting"
C) "The session hibernates (releasing compute), then resumes with full context when feedback arrives"
D) "A new session must be started to address review comments"
*Answer: C — The hibernation/resume lifecycle means sessions do not waste compute while waiting. When new feedback arrives (PR comment, CI result, user message), the VM is restored from its snapshot with full context preserved.*

**Knowledge Check 2.4**
Q: In Devin's verification model, who makes the merge decision?
A) "Devin merges automatically when CI passes"
B) "The session creator merges"
C) "A human always makes the merge decision — Devin proposes via PR, humans approve"
D) "Devin Review merges if it finds no issues"
*Answer: C — Devin never merges its own work. The PR is the review gate where humans inspect the diff and decide whether to merge. This preserves existing governance models.*

### Key Takeaways

- **Clean-room isolation + shared context** is the core architectural insight: security through VM isolation, productivity through persistent configuration
- **Sessions are ephemeral; knowledge is persistent** — the VM is destroyed after use, but the organization's context layer grows over time
- **The session lifecycle optimizes for cost** — hibernation releases compute while preserving context; cost tracks active work, not wall-clock time
- **Verification is layered** — local tests → CI monitoring → PR gate → external validation; the merge decision is always human

---

<a id="sdlc-integration-design"></a>
## 3. SDLC Integration Design

*Reference: [`shared/general-themes/design-patterns-for-devin.md`](../../../shared/general-themes/design-patterns-for-devin.md)*

This section maps Devin's capabilities to each phase of a client's software development lifecycle. For each phase: the trigger that starts Devin, the pattern Devin follows, and where the human touchpoint occurs.

### 3.1 Planning Phase

**Trigger:** Engineer needs research, analysis, or design exploration before writing code.

**Devin Pattern:** Ask Devin (conversational research mode) — no session VM spun up, no PR created. Used for:
- Codebase exploration ("How does the authentication flow work in this service?")
- Technology evaluation ("Compare migration paths from Spring Boot 2.x to 3.x")
- Architecture analysis ("Map the dependencies between these 12 microservices")
- Feasibility assessment ("Can this COBOL program be converted to set-based SQL, or does it need procedural logic?")

**Human Touchpoint:** The engineer reviews Ask Devin's analysis and decides whether to proceed with a full session.

**Design implication:** Ask Devin reduces the planning overhead for complex tasks. Engineers get codebase understanding in minutes rather than hours of manual exploration.

### 3.2 Implementation Phase

**Trigger:** A task is defined (ticket assigned, requirement documented, manual prompt).

**Devin Pattern:** Cloud session for autonomous execution:
- Feature development — implement from requirements, write tests, open PR
- Bug fixes — reproduce, diagnose root cause, fix, verify with tests
- Refactoring — extract service, upgrade framework, modernize patterns
- Migration — translate code between languages/frameworks with parity verification

**Human Touchpoint:** PR review — the engineer inspects the diff, leaves comments, and Devin iterates until approved.

**Design implication:** Implementation sessions are the core unit of work. Each session produces a PR — the auditable artifact that flows through existing review processes.

### 3.3 Testing Phase

**Trigger:** Code merged or PR opened; coverage gaps identified; E2E suite needs expansion.

**Devin Pattern:** Test generation and execution:
- Unit test generation for uncovered code paths
- Integration test creation using existing test patterns
- E2E test authoring (Playwright, Cypress, Selenium) with browser interaction
- Performance test scaffolding with benchmark baselines
- Accessibility audit and remediation

**Human Touchpoint:** Review generated tests for correctness and relevance. Approve or request changes via PR comments.

**Design implication:** Testing is high-volume, pattern-repeatable work — ideal for parallelization with child sessions across many files or modules.

### 3.4 Deployment Phase

**Trigger:** Infrastructure change needed; IaC drift detected; new environment requested.

**Devin Pattern:** Infrastructure-as-Code execution:
- Terraform/CloudFormation authoring and `plan` verification
- Kubernetes manifest generation and `kubectl apply --dry-run` validation
- CI/CD pipeline creation (GitHub Actions, Azure DevOps, Jenkins)
- Asset Bundle deployment (Databricks, AWS CDK)
- Environment provisioning scripts with idempotency verification

**Human Touchpoint:** Review the `plan` output, approve the PR, and merge to trigger deployment through existing CD pipelines.

**Design implication:** Devin proposes infrastructure changes; existing CD pipelines execute them after human approval. No new deployment pathway is introduced.

### 3.5 Operations Phase

**Trigger:** Event-driven (CI failure, incident alert, schedule) — no human initiation required.

**Devin Pattern:** Automated operational responses:
- **Scheduled maintenance** — Weekly dependency bumps, license audits, dead code detection, documentation refresh
- **Event-driven incident response** — Alert fires → Devin investigates logs/traces → triages → implements fix or escalates
- **CI failure remediation** — Build breaks → Devin reads logs → diagnoses → pushes fix → monitors re-run
- **Compliance scanning** — Scheduled SAST/SCA runs with automated remediation of findings below threshold

**Human Touchpoint:** Review the PR (for fixes) or the escalation issue (for complex problems requiring human judgment).

**Design implication:** Operations is where automation topology matters most. The combination of scheduled sessions and event-driven triggers creates an "always-on" maintenance layer that responds without human initiation.

### 3.6 SDLC Integration Summary

```
┌─────────────────────────────────────────────────────────────────┐
│                        CLIENT SDLC                               │
├──────────┬──────────────┬──────────┬────────────┬───────────────┤
│ Planning │Implementation│ Testing  │ Deployment │  Operations   │
├──────────┼──────────────┼──────────┼────────────┼───────────────┤
│ Ask Devin│ Cloud Session│ Cloud    │ Cloud      │ Scheduled +   │
│ (research│ (autonomous  │ Session  │ Session    │ Event-Driven  │
│  mode)   │  execution)  │ (gen +   │ (IaC +    │ Sessions      │
│          │              │  execute)│  validate) │ (automated)   │
├──────────┼──────────────┼──────────┼────────────┼───────────────┤
│ Engineer │   PR Review  │PR Review │ Plan Review│ PR / Escalate │
│ Decision │              │          │            │   Issue       │
└──────────┴──────────────┴──────────┴────────────┴───────────────┘
         Trigger ──→ Devin Pattern ──→ Human Touchpoint
```

---

### Knowledge Checks — SDLC Integration

**Knowledge Check 3.1**
Q: A client asks: "Where does Devin fit in our deployment process? We use Terraform with a plan-apply workflow and require manual approval before `apply`." How do you respond?
A) "Devin replaces your Terraform workflow with its own deployment mechanism"
B) "Devin writes and validates Terraform code, opens a PR with the `plan` output, and your existing approval/apply workflow triggers on merge — no new deployment pathway"
C) "Devin runs `terraform apply` directly in its session"
D) "Devin does not support infrastructure work"
*Answer: B — Devin proposes infrastructure changes via PR. The existing CD pipeline (plan on PR, apply on merge after approval) is unchanged. Devin adds capacity to write and validate IaC, not a new deployment path.*

**Knowledge Check 3.2**
Q: Which SDLC phase benefits most from event-driven automation (no human initiation)?
A) "Planning — research should be automated"
B) "Implementation — feature development should be fully automated"
C) "Operations — scheduled maintenance, CI failure remediation, and incident response are high-volume, reactive tasks where event-driven triggers eliminate human paging"
D) "Deployment — all deployments should be automated end to end"
*Answer: C — Operations is where automation topology creates the most value. Maintenance tasks and incident response are reactive, high-volume, and follow repeatable patterns — ideal for event-driven Devin sessions.*

**Knowledge Check 3.3**
Q: A client's team uses Ask Devin to research a migration path, then kicks off a Cloud session to execute it. What is the human touchpoint between these two phases?
A) "There is no human touchpoint — it flows automatically"
B) "The engineer reviews Ask Devin's analysis and decides whether to proceed with a full session"
C) "A manager must approve the session"
D) "The PR reviewer decides whether to start the session"
*Answer: B — Ask Devin is a research tool (no VM, no PR). The engineer reviews the analysis and makes the decision to start a Cloud session for execution. This keeps humans in the decision loop for high-impact work.*

### Key Takeaways

- **Each SDLC phase has a natural Devin pattern:** research (Ask Devin), execution (Cloud session), testing (parallel sessions), deployment (IaC + PR), operations (event-driven/scheduled)
- **The human touchpoint is always defined:** PR review for implementation, plan review for deployment, escalation issue for operations — no phase runs without a human gate
- **Operations is the automation frontier:** event-driven triggers and scheduled sessions create an always-on maintenance layer that responds without human initiation
- **Devin does not replace existing processes — it plugs into them:** PRs flow through existing review workflows, Terraform plans trigger existing CD pipelines, and existing CI pipelines verify Devin's output

---

<a id="live-walkthrough-delivery"></a>
## 4. Live Walkthrough Delivery

*Reference: [`demos/security/general.md`](../../../demos/security/general.md), [`demos/data-engineering/sas-to-databricks-demo.md`](../../../demos/data-engineering/sas-to-databricks-demo.md), [`demos/migration/mulesoft-to-spring-boot-demo.md`](../../../demos/migration/mulesoft-to-spring-boot-demo.md)*

This section teaches you how to run each walkthrough type end-to-end in a simulated environment. For each: the setup steps, the prompts to run, what to explain while it runs, and how to handle if things go off-script.

### 4.1 Security Remediation — Scan → Fix → Rescan Closed Loop

**What you are showing:** Devin as a background security agent that responds to scan findings, remediates vulnerabilities, and re-scans to prove the fix — in a closed loop.

#### Setup Steps

1. Ensure the `Cognition-Partner-Workshops/otterworks` repo is accessible in your Devin org
2. Verify a Trivy scan workflow exists (or use the prompt below to create one)
3. Pre-configure a Devin Automation to fire on security scan failures (optional — you can create it live)

#### Prompts to Run

**Create the scanner workflow:**

```
Create a GitHub Actions workflow called security-scan.yml
on the Cognition-Partner-Workshops/otterworks repo that:

1. Triggers on pull_request events (opened, synchronize).
2. Runs a Trivy scan targeting HIGH and CRITICAL severity
   findings.
3. Reports results as a GitHub check run.
4. Posts a PR comment summarizing findings by service
   directory.
```

**Create the automation trigger:**

```
When a security scan check run fails on
Cognition-Partner-Workshops/otterworks, start a Devin
session that:

1. Reads the scan findings attached to the check run.
2. Triages HIGH and CRITICAL findings by service directory.
3. Applies fixes — dependency upgrades or code changes —
   in the correct manifest or source file.
4. Runs each affected service's tests.
5. Pushes the fix to the same branch.

Cap at 2 invocations per PR. Set an ACU limit of 50 per
session.
```

#### What to Explain While It Runs

- **Before launching:** Explain the closed-loop pattern — scanner detects → automation triggers → Devin remediates → push triggers re-scan → converges to green
- **While Devin scans:** Point out that Devin is reading the scan output, identifying the correct manifest files per language/package manager, and prioritizing by severity
- **While Devin fixes:** Explain that Devin runs affected service tests before pushing — local verification before CI verification
- **When CI re-runs:** Highlight the converging loop — each iteration reduces findings. When findings reach zero, CI goes green automatically
- **Safeguards:** Mention invocation limits (prevents runaway loops), ACU caps (controls cost), and escalation policy (after N attempts, opens an issue for human review)

#### If It Goes Off-Script

| Scenario | Recovery |
|----------|----------|
| Scanner finds no vulnerabilities | Explain this is the "green" steady state. Switch to the backlog burn-down prompt to show remediation on existing findings |
| Devin picks wrong manifest file | Leave a PR comment: "The vulnerable dependency is in `services/auth/package.json`, not the root" — show the PR feedback loop |
| Fix breaks tests | This is a feature, not a bug — explain that Devin will iterate (read failure logs, push fix, re-run). Monitor the session timeline |
| Automation does not fire | Check the trigger conditions in the Automations UI. Common issue: the check run name does not match the filter. Adjust and re-trigger |

---

### 4.2 Migration — Language/Framework Translation with Parity Verification

**What you are showing:** Devin reading an unfamiliar legacy estate, converting code to a modern target, and proving parity through programmatic verification — not just "it compiles."

#### Setup Steps

1. Ensure both repos are accessible: `ts-java-mulesoft-employee-api` (source) and `uc-api-migration-mulesoft-to-spring-boot` (target)
2. Verify the target repo's contract test harness works: `make db-up && make build && make db-down`
3. Familiarize yourself with the "real bug" — the 404 empty body divergence (see walkthrough below)

#### Prompts to Run

**Orient over the legacy estate:**

```
Using the ts-java-mulesoft-employee-api repo, give me a map
of the MuleSoft API estate: the Mule XML flows in
src/main/mule/employee-services-api.xml, what each flow does
(OAuth, employee goals, learning, pay date, PTO), the RAML
spec at src/main/resources/api/employee-services-api.raml,
the database tables (api_clients, employee_goals,
employee_learning, employee_pto), and how the authentication
flow works (client credentials → token validation →
protected endpoints).
```

**Convert one endpoint with playbook verification:**

```
!convert-mulesoft-to-spring-boot

Convert the employee goals endpoint from the MuleSoft estate
in ts-java-mulesoft-employee-api into the Spring Boot target
in uc-api-migration-mulesoft-to-spring-boot.

- MuleSoft source: src/main/mule/employee-services-api.xml
  (get:\employee\{employeeId}\goals flow + related subflows)
- RAML spec:
  src/main/resources/api/employee-services-api.raml
- Target: spring-boot-app/ in
  uc-api-migration-mulesoft-to-spring-boot
- Namespace: migration/emp-goals
```

#### What to Explain While It Runs

- **Before launching:** Explain the "before" state — an empty Spring Boot scaffold with a contract test harness and OpenAPI spec, ready for Devin to fill in
- **During orientation (Act 1):** Point out that Devin is reading XML flows, RAML specs, and DataWeave transforms it has never seen before — mapping the estate using context retrieval
- **During conversion (Act 2):** Explain the pattern mapping: Mule HTTP Listeners → `@RestController`, `<db:select>` → Spring Data JPA, DataWeave → Jackson DTOs, `<choice>` error handlers → `@ControllerAdvice`
- **When verification catches the bug:** This is the key beat. The contract test fails because the MuleSoft `<choice>` handler returns a JSON error body on 404, but a plausible-looking conversion uses `ResponseEntity.notFound().build()` (empty body). Explain: "Compiles and looks reasonable" review would have shipped this bug. The contract test against the spec caught it
- **After the fix:** Show the green test suite. Emphasize: a conversion is "done" when contract tests are green, not when the code compiles

#### If It Goes Off-Script

| Scenario | Recovery |
|----------|----------|
| Devin does not hit the 404 bug | This can happen if Devin reads the contract test expectations first. Explain the verification model is still working — the tests passed because Devin got it right. Reference the worked example from the playbook documentation |
| Database connection fails | Leave a PR comment: "Run `make db-up` first — the PostgreSQL container needs to be running for contract tests." This shows real-world iteration |
| Devin converts multiple endpoints at once | Good — show the parallel conversion value. Point out that each endpoint's tests pass independently |
| Compilation errors in generated code | Explain that Devin will iterate — this is local testing catching issues before CI. Watch the session timeline for the fix cycle |

---

### 4.3 Data Engineering — ETL Translation with Reconciliation

**What you are showing:** Devin migrating a SAS analytics estate to dbt/Databricks with programmatic reconciliation — proving source-target parity through control totals, not just "it runs."

#### Setup Steps

1. Ensure both repos are accessible: `ts-sas-legacy-analytics` (source) and `uc-data-migration-sas-to-databricks` (target)
2. Verify Databricks workspace credentials are configured as org secrets (`DATABRICKS_HOST`, `DATABRICKS_TOKEN`, `DATABRICKS_HTTP_PATH`)
3. Confirm the reconciliation harness works: `make seed && make demo-up NS=dev && make reconcile NS=dev && make demo-down NS=dev`
4. Familiarize yourself with the "real bug" — the LOC risk-weight divergence (0.75 vs. source's 1.00)

#### Prompts to Run

**Orient over the SAS estate:**

```
Using the ts-sas-legacy-analytics repo, give me a map of
the SAS estate: the banking and insurance programs, what
each one reads and writes, the LIBNAMEs, the macros and
PROC FORMATs they depend on, and which programs are
set-based (good for dbt) vs procedural/multi-output
(better as PySpark).
```

**Convert one program with reconciliation verification:**

```
!convert-sas-to-databricks

Convert the SAS program
Programs/Banking/monthly_regulatory_reporting.sas in the
ts-sas-legacy-analytics estate into dbt models on
Databricks, writing to
uc-data-migration-sas-to-databricks.

- SAS program:
  Programs/Banking/monthly_regulatory_reporting.sas
- Target model(s): mart_regulatory_rwa +
  mart_delinquency_aging
- Namespace: dev
```

#### What to Explain While It Runs

- **Before launching:** Explain the "before" state — raw source data seeded into `banking_analytics.raw.*`, the banking domain already migrated (Phase 1), and the reconciliation harness in place. What Devin converts live is the next wave
- **During orientation (Act 1):** Point out that Devin is mapping SAS programs, macros, formats, and batch orchestration — distinguishing set-based logic (→ dbt) from procedural multi-output logic (→ PySpark)
- **During conversion (Act 2):** Explain that each converted model gets reconciliation controls: completeness checks, control totals, and source-parity mappings
- **When reconciliation catches the divergence:** This is the key beat. The SAS CASE maps `LOC` (line of credit) → risk weight 1.00. A plausible conversion maps it to 0.75 (matching other revolving-credit products). The parity control catches: `rwa_risk_weight_parity | FAIL | LOC=0.75 (source expects 1.00)`. Explain: "Looks reasonable" review would have shipped silent capital miscalculation. The reconciliation against the source caught it
- **After the fix:** Show the green reconciliation report. Emphasize: a conversion is "done" when source-parity controls are green, not when the code runs

#### If It Goes Off-Script

| Scenario | Recovery |
|----------|----------|
| Databricks connection fails | Check secrets are configured. Explain this is a common setup issue — the shared context layer (Secrets) must be configured once for the org |
| Devin skips the reconciliation step | Leave a PR comment: "Please run `make reconcile NS=dev` and include the report." Show the PR feedback loop driving quality |
| Reconciliation fails on a different control | This is fine — explain that multiple controls may catch different issues. The point is programmatic verification, not a specific bug |
| Namespace collision with another session | Use a different namespace: `NS=walkthrough1`. Explain namespaced outputs enable concurrent execution |

---

### 4.4 Feature Development — Implement → Test → PR → Iterate

**What you are showing:** Devin as a day-to-day development partner — taking a feature requirement, implementing it, writing tests, opening a PR, and iterating based on review feedback.

#### Setup Steps

1. Ensure the target repository is accessible (choose based on audience — `timesheet-app` for full-stack, `otterworks` for microservices)
2. Prepare a realistic feature request (or use the prompt below)
3. Familiarize yourself with the PR feedback loop — you will leave review comments live

#### Prompts to Run

**Feature implementation:**

```
In the Cognition-Partner-Workshops/timesheet-app repo,
add a "Bulk Import" feature to the timesheet entries.
Requirements:

1. Add a CSV upload endpoint (POST /api/entries/import)
   that accepts a CSV file with columns: date, project,
   hours, description.
2. Validate each row (date format, hours > 0, project
   exists).
3. Return a summary: imported count, skipped count,
   and error details for skipped rows.
4. Add unit tests for the CSV parsing and validation
   logic.
5. Add an integration test that uploads a sample CSV
   and verifies the response.
```

#### What to Explain While It Runs

- **Before launching:** Explain the prompt structure — it includes the repo, the endpoint path, specific requirements, validation rules, and expected output format. This is what makes prompts effective: concrete, verifiable acceptance criteria
- **During implementation:** Point out that Devin is reading existing code patterns (how other endpoints are structured, what test framework is used) and following conventions automatically via Knowledge notes
- **When the PR opens:** Show the PR — Devin has written code, tests, and a description. This is the handoff point
- **During review iteration:** Leave a review comment live ("Add input validation for the hours field — reject values over 24"). Watch Devin respond and push a new commit. Explain: this is the core workflow — PR comments are the communication channel

#### If It Goes Off-Script

| Scenario | Recovery |
|----------|----------|
| Devin uses a different framework/library than expected | Leave a PR comment asking for the preferred approach. This shows how Knowledge notes prevent this in production |
| Tests fail on CI | Explain that Devin monitors CI and will iterate. Watch the session timeline for the fix cycle |
| Feature scope creeps beyond the prompt | Explain that well-scoped prompts with explicit boundaries prevent this. Reference the planning phase (Ask Devin) for requirement refinement |

---

### Knowledge Checks — Live Walkthrough Delivery

**Knowledge Check 4.1**
Q: During a security remediation walkthrough, the scanner finds no vulnerabilities. What do you do?
A) "Apologize and end the walkthrough"
B) "Explain this is the green steady state, then switch to the backlog burn-down prompt to show remediation on existing findings"
C) "Run the scanner again with lower thresholds"
D) "Manually introduce a vulnerability to trigger the automation"
*Answer: B — A green scan is a valid state. Pivot to showing the backlog burn-down pattern, which shows the same remediation loop against existing (pre-committed) findings.*

**Knowledge Check 4.2**
Q: What is the key beat in the migration walkthrough that distinguishes Devin's approach from "it compiles and looks reasonable" review?
A) "The speed of conversion"
B) "The contract test / reconciliation catching a real parity divergence that human review would likely miss"
C) "The number of lines of code generated"
D) "The parallel fan-out across multiple endpoints"
*Answer: B — The verification beat is the core value message. Programmatic verification against the source of truth (contract tests, reconciliation controls) catches bugs that "looks reasonable" review would miss.*

**Knowledge Check 4.3**
Q: During a live walkthrough, Devin's PR has a compilation error. How do you handle this?
A) "Stop the walkthrough and start over"
B) "Explain that Devin will iterate — this is local testing catching issues before CI. Show the session timeline as Devin reads the error, pushes a fix, and re-runs"
C) "Manually fix the code in the PR"
D) "Switch to a different walkthrough type"
*Answer: B — Compilation errors during a walkthrough are a feature, not a failure. They show the verification model in action — Devin iterates toward green, just as a developer would.*

**Knowledge Check 4.4**
Q: What should you explain BEFORE launching a Devin session in a walkthrough?
A) "Nothing — just launch it and explain as it goes"
B) "The pattern Devin will follow, the verification mechanism, and what a successful outcome looks like — so the audience knows what to watch for"
C) "A full technical deep-dive on the architecture"
D) "The pricing model"
*Answer: B — Always set context before launching. The audience needs to know the pattern (what Devin will do), the verification (how you know it worked), and the success criteria (what done looks like). Without this framing, they are watching a terminal scroll.*

### Key Takeaways

- **Always explain the pattern before running** — set context so the audience knows what to watch for and what success looks like
- **The verification beat is the core value message** — the moment programmatic verification catches a bug that human review would miss is the highest-impact moment in any walkthrough
- **Off-script scenarios are recoverable** — every walkthrough type has predictable failure modes with known recovery patterns. Practice these
- **The PR feedback loop is the universal recovery tool** — when in doubt, leave a PR comment and show Devin iterating. This demonstrates the collaboration model regardless of what went off-script

---

<a id="automation-topology-design"></a>
## 5. Automation Topology Design

*Reference: [`shared/general-themes/platform-capabilities.md`](../../../shared/general-themes/platform-capabilities.md), [`shared/general-themes/design-patterns-for-devin.md`](../../../shared/general-themes/design-patterns-for-devin.md)*

Given client requirements, design the webhook/schedule/trigger architecture. This section covers the building blocks, design principles, and exercises for constructing automation topologies.

### 5.1 Event-Driven Triggers

Event-driven triggers respond to signals from existing systems — no human initiation required.

| Trigger Source | Event | Devin Response |
|---------------|-------|---------------|
| **CI pipeline** | Check run failure (SAST, lint, test) | Read failure logs, diagnose, push fix |
| **SAST scanner** | Finding above severity threshold | Triage finding, remediate, re-scan |
| **Issue tracker** | Ticket assigned to Devin (label/tag) | Read ticket, implement, open PR |
| **PR event** | PR opened / comment / review requested | Devin Review analysis, or respond to feedback |
| **Incident alert** | PagerDuty / Datadog / CloudWatch alert | Investigate logs/traces, triage, implement fix or escalate |
| **Webhook** | External system event (custom payload) | Parse payload, execute configured response |

**Architecture pattern:**

```
Event Source (CI, alerting, issue tracker)
    ↓ webhook / API call
Devin Automation (trigger conditions + prompt template)
    ↓ creates session with full context
Devin Session (autonomous execution on isolated VM)
    ↓ PR / comment / escalation issue
Review Gate (human approval, CI checks)
```

**Safeguards (mandatory in every topology):**
- **Invocation limit** — cap fires per time window (prevents runaway loops)
- **ACU budget** — maximum compute per session (prevents cost overrun)
- **Trigger conditions** — filter events (only specific repos, branches, severity levels)
- **Loop prevention** — filter out Devin's own events (check PR author, skip `devin-ai-integration[bot]`)
- **Idempotency** — prevent duplicate sessions from the same event
- **Escalation policy** — after N attempts, stop and notify humans

### 5.2 Scheduled Sessions

Scheduled sessions run on a recurring cadence — daily, weekly, or custom cron expressions — for proactive maintenance.

| Schedule | Task | Value |
|----------|------|-------|
| Weekly (Monday AM) | Dependency version bumps | Prevents CVE accumulation; low-risk PRs for human review |
| Monthly | License compliance audit | Catches policy violations before legal review |
| Weekly | Dead code detection | Reduces maintenance burden incrementally |
| Post-merge (triggered) | Documentation refresh | READMEs, API docs, changelogs stay current |
| Bi-weekly | Test coverage analysis + generation | Coverage trends upward without manual effort |
| Daily | Security scanning + remediation | Findings remediated same-day rather than accumulating |

**Configuration elements:**
- **Prompt** — what Devin does each time (specific, verifiable)
- **Target repository** — which repo the session operates on
- **Recurrence pattern** — cron expression or preset (daily, weekly, monthly)
- **Branch strategy** — create new branch each run, or push to a standing branch

### 5.3 The Parent-Child Pattern for Campaigns

For large-scale work across many targets, a parent agent orchestrates child agents:

```
Parent Agent (orchestrator)
├── Analyzes scope (list of targets: repos, services, files)
├── Creates/references playbook (reusable methodology)
├── Spawns Child Agent 1 → target A → PR
├── Spawns Child Agent 2 → target B → PR
├── ...
├── Spawns Child Agent N → target N → PR
└── Monitors progress, handles failures, summarizes results
```

**When to use parent-child:**
- Migrating N modules/services/programs (each is independent)
- Remediating N security findings across M repos
- Generating tests for N uncovered files
- Applying the same refactoring pattern across many codebases
- Upgrading N microservices to a new framework version

**Best practices:**
- Define a clear, repeatable unit of work for each child (one PR per child)
- Use playbooks so every child follows the same validated process
- Set maximum concurrency to avoid overwhelming CI or rate limits
- Have the parent check child results and escalate failures
- Use namespaced outputs (branches, schemas) so parallel children do not collide

### 5.4 Design Exercises

**Exercise 5A: Security Remediation Topology**

*Scenario:* A client has 30 microservices across 3 repositories (monorepo per product team). They want automated security remediation — when a SAST scan finds HIGH+ findings, Devin should remediate them automatically.

*Design the topology:*
1. What triggers the automation? (Hint: CI check run failure or webhook from external scanner)
2. What safeguards do you set? (Hint: invocation limits, ACU caps, loop prevention)
3. How do you handle a finding that spans multiple services in the monorepo? (Hint: scope the session to the affected service directory)
4. What is the escalation policy after failed remediation attempts?

*Reference answer:*
- Trigger: GitHub `check_run` event with `conclusion: failure` on the SAST job, filtered to the 3 product repos
- Safeguards: 3 invocations per PR per hour, 50 ACU per session, filter out Devin's own PRs
- Multi-service: The prompt includes "triage by service directory, fix each affected service independently" — or use child sessions, one per service
- Escalation: After 2 failed attempts, open a GitHub Issue labeled `security/needs-human-review` and stop

**Exercise 5B: Quarterly Framework Upgrade Campaign**

*Scenario:* A client has 50 microservices on Spring Boot 2.7. They want to upgrade to Spring Boot 3.x across the entire estate over one quarter.

*Design the topology:*
1. Is this event-driven or scheduled? (Hint: campaign — one-time orchestrated effort)
2. How do you parallelize? (Hint: parent-child, one child per microservice)
3. What is the playbook structure? (Hint: check compatibility → update deps → fix breaking changes → run tests → update docs)
4. How do you track progress? (Hint: parent monitors child completion, summary report)

*Reference answer:*
- Type: Campaign (one-time parent-child orchestration, not recurring schedule)
- Parallelization: Parent session lists 50 microservices, spawns one child per service, each following the `!spring-boot-upgrade` playbook
- Playbook: Check Spring Boot 3 migration guide → update `pom.xml` → fix `javax.` → `jakarta.` namespace changes → run tests → update README version badge
- Progress: Parent monitors child sessions, reports completion rate, escalates failures (e.g., services with custom Spring internals that need human review)
- Concurrency: 10 children at a time to avoid overwhelming CI (stagger launches)

---

### Knowledge Checks — Automation Topology

**Knowledge Check 5.1**
Q: A client wants Devin to respond to PagerDuty alerts by investigating and triaging incidents. What type of trigger is this?
A) "Scheduled session"
B) "Event-driven trigger via webhook from PagerDuty"
C) "Parent-child orchestration"
D) "Manual prompt"
*Answer: B — Incident response is event-driven. PagerDuty fires a webhook when an alert occurs, which triggers a Devin Automation that creates a session to investigate.*

**Knowledge Check 5.2**
Q: When designing an event-driven security remediation automation, what prevents the automation from firing in an infinite loop (scanner detects → Devin fixes → push triggers scanner → scanner detects new issue → Devin fixes → ...)?
A) "Devin is smart enough to stop"
B) "Invocation limits cap how many times the automation fires per time window, and loop prevention filters out Devin's own events"
C) "The scanner only runs once"
D) "The automation has a built-in timer"
*Answer: B — Two safeguards work together: invocation limits (max N fires per PR/hour) prevent runaway loops, and trigger conditions filter out events from `devin-ai-integration[bot]` to avoid self-triggering.*

**Knowledge Check 5.3**
Q: A client has 80 microservices that need the same dependency upgrade. What pattern do you recommend?
A) "One session that modifies all 80 services sequentially"
B) "Parent-child orchestration: one parent spawns a child session per microservice, each following the same playbook and producing its own PR"
C) "80 manual sessions launched by hand"
D) "A scheduled session that upgrades one service per week"
*Answer: B — Parent-child orchestration is the correct pattern for large-scale, independent, repeatable work. Each child operates on its own VM/branch/PR, failures are isolated, and the playbook ensures consistency.*

**Knowledge Check 5.4**
Q: What elements must every automation topology include for production safety?
A) "Just a trigger and a prompt"
B) "Invocation limits, ACU budget per session, trigger conditions (filtering), loop prevention, and an escalation policy"
C) "Only ACU limits"
D) "A human watching every session"
*Answer: B — Production automation topologies require all five safeguards: invocation limits (rate control), ACU budget (cost control), trigger conditions (scope control), loop prevention (self-trigger avoidance), and escalation (human fallback).*

### Key Takeaways

- **Three trigger types cover most scenarios:** event-driven (reactive), scheduled (proactive), and parent-child campaigns (one-time large-scale)
- **Safeguards are mandatory, not optional:** invocation limits, ACU caps, loop prevention, and escalation policies are non-negotiable in production topologies
- **Parent-child scales linearly:** one agent becomes N agents, each producing independent PRs. Failures in one child do not affect others
- **Design the escalation path first:** knowing when and how the automation hands off to humans is more important than the happy path

---

<a id="full-platform-configuration"></a>
## 6. Full Platform Configuration

This section walks through configuring a complete Devin organization from scratch — every component of the shared context layer in the order you should set them up.

### 6.1 Git Connections

**What it is:** Repository access configured at the org level. Once connected, every session in the organization can clone and push to connected repos immediately.

**When to configure:** First. Without Git connections, Devin cannot access any code.

**Configuration steps:**
1. Navigate to Settings → Git Connections in the Devin web app
2. Connect your Git provider (GitHub, GitLab, Azure DevOps, Bitbucket)
3. Select which repositories or organizations Devin can access
4. Verify access by starting a test session that clones a repo

**Example configuration:**
- Connect the organization's GitHub account
- Grant access to specific repositories (not all — follow least-privilege)
- Verify with: start a session and ask "Clone `<org>/<repo>` and list the top-level directory structure"

**Common mistakes:**
- Granting access to all repos when only a subset is needed
- Forgetting to connect repos in secondary organizations (e.g., shared libraries in a different GitHub org)
- Not verifying the connection works before configuring downstream components

### 6.2 Environment Blueprints

**What it is:** YAML configuration defining the VM setup for Devin sessions — dependencies, language runtimes, tools, startup scripts. Sessions boot from cached snapshots built from these blueprints.

**When to configure:** Second. After Git connections, configure the environment so sessions start ready to build.

**Configuration steps:**
1. Navigate to Settings → Environment in the Devin web app
2. Create a blueprint with `initialize` (persistent software install) and `maintenance` (per-session startup) sections
3. Build a snapshot from the blueprint
4. Test by starting a session and verifying all tools are available

**Example configuration:**

```yaml
initialize:
  - apt-get update && apt-get install -y postgresql-client redis-tools
  - curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
  - apt-get install -y nodejs
  - npm install -g pnpm@9

maintenance:
  - echo "Session started at $(date)" >> /tmp/session.log
```

**Common mistakes:**
- Putting ephemeral state (database connections, API tokens) in `initialize` instead of `maintenance`
- Not testing the snapshot build — a broken blueprint blocks all future sessions
- Including secrets directly in the blueprint YAML (use `$VARIABLE_NAME` syntax instead)
- Forgetting that `initialize` runs once (at snapshot build time) while `maintenance` runs at every session start

### 6.3 Knowledge Notes

**What it is:** Persistent, human-curated context — coding standards, architecture decisions, team conventions, domain glossary — that Devin retrieves automatically based on task relevance.

**When to configure:** Third. After the environment is ready, add the context that makes Devin's output match your team's standards.

**Configuration steps:**
1. Navigate to Settings → Knowledge in the Devin web app
2. Create notes with a trigger description (when should this note be retrieved?) and content (what should Devin know?)
3. Start a session on a relevant task and verify the Knowledge note appears in context

**Example configuration:**

| Note Name | Trigger | Content |
|-----------|---------|---------|
| "Coding Standards" | "When writing code in any repo" | "Use TypeScript strict mode. Prefer functional components. Import from `@/lib` not relative paths..." |
| "API Conventions" | "When creating or modifying API endpoints" | "Use RESTful naming. Return 404 with JSON body. Pagination via cursor, not offset..." |
| "Testing Policy" | "When writing or modifying tests" | "Unit tests use Vitest. E2E tests use Playwright. Minimum 80% branch coverage for new code..." |

**Common mistakes:**
- Writing notes that are too broad (trigger: "always") — they dilute relevance
- Writing notes that are too specific (trigger: "when modifying `src/auth/login.ts`") — they rarely fire
- Not including concrete examples in the note content — abstract guidance produces abstract code
- Duplicating information that already exists in the codebase (README, CONTRIBUTING.md)

### 6.4 Playbooks

**What it is:** Repeatable, step-by-step procedures that encode institutional methodology. When Devin follows a playbook, every session applies the same process regardless of who triggers it.

**When to configure:** Fourth. After Knowledge notes define the "what" (standards), Playbooks define the "how" (process).

**Configuration steps:**
1. Navigate to Settings → Playbooks in the Devin web app (or commit a `.devin/playbooks/` file in the repo)
2. Write the playbook as a step-by-step procedure with verification criteria at each step
3. Test by invoking the playbook in a session and verifying it follows every step

**Example configuration:**

A security remediation playbook:
1. Run the configured SAST tool and capture output
2. Parse findings — filter to HIGH and CRITICAL severity
3. For each finding: identify the correct file, determine the fix type (dependency upgrade vs. code change)
4. Apply the fix in the correct manifest or source file
5. Run affected tests locally
6. Push the fix
7. Verify CI re-scan shows reduced findings
8. If findings persist after 2 iterations, open an escalation issue

**Common mistakes:**
- Writing vague steps ("fix the issue") instead of concrete actions ("upgrade the dependency in `package.json` to the latest patch version")
- Omitting verification steps — without "run tests after fixing," the playbook may produce untested code
- Not specifying escalation criteria — playbooks that do not define "when to stop" can loop
- Making playbooks too long — break complex workflows into composable sub-playbooks

### 6.5 MCP Servers

**What it is:** Model Context Protocol — pre-configured integrations that let Devin call external tools (Jira, Datadog, Confluence, Azure DevOps, Slack) as if they were local. Configured once, available to every session.

**When to configure:** Fifth. After the core development workflow is set up, add the external tool integrations your team uses.

**Configuration steps:**
1. Navigate to Settings → MCP Servers in the Devin web app
2. Add the server configuration (connection URL, authentication)
3. Test by starting a session and asking Devin to query the integrated tool

**Example configuration:**

| MCP Server | Purpose | Use Cases |
|-----------|---------|-----------|
| Jira | Issue tracking | Read ticket details, update status, link PRs |
| Datadog | Observability | Query logs and traces during incident investigation |
| Confluence | Documentation | Read existing docs for context, update docs as part of implementation |
| Azure DevOps | Work items + pipelines | Read work items, trigger pipelines, check build status |

**Common mistakes:**
- Not testing the MCP connection before using it in a production workflow
- Granting broader permissions than needed (e.g., write access to Jira when only read is required)
- Configuring MCP servers that require VPN access without setting up network connectivity first
- Not updating MCP server credentials when they rotate

### 6.6 Secrets Management

**What it is:** Scoped credentials (API keys, service account tokens, database passwords) injected into session environments at start. Secrets never appear in prompts, logs, or code.

**When to configure:** Sixth. After integrations are identified, provision the credentials they require.

**Configuration steps:**
1. Navigate to Settings → Secrets in the Devin web app
2. Add secrets with appropriate scope (org-wide, user-specific, or repo-specific)
3. Reference secrets in blueprints as `$SECRET_NAME` — never paste values directly
4. Verify by starting a session and confirming the secret is available as an environment variable

**Example configuration:**

| Secret Name | Scope | Purpose |
|-------------|-------|---------|
| `GITHUB_TOKEN` | Org | Git operations (typically auto-configured via Git connections) |
| `DATABRICKS_TOKEN` | Org | Databricks workspace access for data engineering tasks |
| `SONAR_TOKEN` | Repo | SonarQube/SonarCloud scanning for specific repos |
| `JIRA_API_TOKEN` | Org | Jira MCP server authentication |

**Common mistakes:**
- Using personal tokens instead of service account credentials (creates dependency on individual employees)
- Setting org-wide scope for secrets that should be repo-specific (violates least-privilege)
- Embedding secret values directly in blueprint YAML instead of using `$SECRET_NAME` references
- Not rotating secrets on a schedule — service account tokens expire

### 6.7 Automations

**What it is:** Webhooks, scheduled sessions, and trigger rules configured at the organization level. Automations create Devin sessions in response to external events without human initiation.

**When to configure:** Last. After everything else is in place, add the automation layer that ties it together.

**Configuration steps:**
1. Navigate to Automations in the Devin web app
2. Describe the automation in natural language (trigger, action, safeguards)
3. Configure trigger conditions (which events, which repos, which branches)
4. Set safeguards (invocation limits, ACU caps)
5. Test by triggering the event and verifying the automation creates a session

**Example configuration:**

```
When a security scan check run fails on any repo in the
organization, start a Devin session that reads the scan
findings, triages HIGH and CRITICAL issues, remediates
them, runs tests, and pushes the fix.

Conditions:
- Only trigger on check_run conclusion = failure
- Only for check runs named "security-scan"
- Skip if PR author is devin-ai-integration[bot]

Safeguards:
- Max 3 invocations per PR per hour
- 50 ACU limit per session
- Escalate after 2 failed attempts
```

**Common mistakes:**
- No invocation limits — allows runaway loops
- No ACU caps — unbounded cost per session
- Missing loop prevention — Devin's own pushes re-trigger the automation
- Overly broad trigger conditions (all repos, all events) — creates noise
- No escalation policy — automation retries forever on unfixable issues

### 6.8 Configuration Order Summary

```
1. Git Connections     → Devin can access code
2. Environment         → Sessions boot ready to build
3. Knowledge           → Devin follows your standards
4. Playbooks           → Devin follows your processes
5. MCP Servers         → Devin integrates with your tools
6. Secrets             → Devin authenticates to your systems
7. Automations         → Devin responds to events autonomously
```

Each layer depends on the previous ones. Automations (7) only work if Devin can access code (1), build it (2), follow standards (3), execute processes (4), query tools (5), and authenticate (6).

---

### Knowledge Checks — Platform Configuration

**Knowledge Check 6.1**
Q: A client asks: "We configured a Devin Automation but it is not firing when our CI fails. What should we check?" What is the most likely issue?
A) "Devin is broken"
B) "Check the trigger conditions — the check run name in the automation filter may not match the actual CI job name, or the event type (check_run vs. workflow_run) may be wrong"
C) "Automations do not work with CI"
D) "The secret expired"
*Answer: B — Trigger condition mismatch is the most common automation issue. The check run name, event type, repo filter, or branch filter does not match the actual event being emitted.*

**Knowledge Check 6.2**
Q: Where should database connection passwords be stored in a Devin organization?
A) "Directly in the environment blueprint YAML"
B) "In a Knowledge note"
C) "In the Secrets management layer, referenced in blueprints as $SECRET_NAME"
D) "In the playbook"
*Answer: C — Secrets must be stored in the Secrets management layer and referenced by variable name. Never embed secret values directly in blueprints, Knowledge notes, or playbooks.*

**Knowledge Check 6.3**
Q: What is the difference between `initialize` and `maintenance` in an environment blueprint?
A) "They are the same"
B) "`initialize` runs once at snapshot build time (persistent installs); `maintenance` runs at every session start (ephemeral state refresh)"
C) "`initialize` is for secrets; `maintenance` is for tools"
D) "`maintenance` runs weekly; `initialize` runs once"
*Answer: B — `initialize` installs persistent software (baked into the snapshot); `maintenance` handles per-session startup (e.g., refreshing tokens, starting background services). Putting ephemeral state in `initialize` causes stale data.*

**Knowledge Check 6.4**
Q: In what order should you configure a new Devin organization?
A) "Automations first, then Git connections"
B) "Git Connections → Environment → Knowledge → Playbooks → MCP Servers → Secrets → Automations"
C) "Secrets first, then everything else"
D) "Any order is fine"
*Answer: B — Each layer depends on the previous ones. Automations (last) only work if Devin can access code (first), build it, follow standards, execute processes, query tools, and authenticate.*

### Key Takeaways

- **Configuration is a one-time investment** — one engineer sets up the shared context layer; every subsequent session from any team member inherits it
- **Order matters:** Git access → build environment → knowledge → playbooks → integrations → secrets → automations. Each layer depends on the previous ones
- **Secrets never go in plaintext** — use `$SECRET_NAME` references in blueprints, scope secrets to minimum necessary access, use service accounts over personal tokens
- **Test each layer before proceeding** — a broken blueprint blocks all sessions; a misconfigured MCP server causes silent failures. Verify each component works before building on top of it

---

<a id="handling-unexpected-outcomes"></a>
## 7. Handling Unexpected Outcomes

Recovery patterns for when things go wrong during a client walkthrough or engagement. This section covers the most common failure modes and how to respond.

### 7.1 Agent Gets Stuck

**Symptoms:** Session shows no progress for several minutes. Devin is in a loop (retrying the same action), or waiting for something that will not arrive.

**Recovery patterns:**

| Approach | When to Use | How |
|----------|------------|-----|
| **PR comment** | Devin has opened a PR but is stuck on a specific issue | Leave a comment on the PR with specific guidance: "The test failure is because `DB_URL` is not configured. Add `export DB_URL=postgresql://...` to your setup" |
| **Session message** | Devin has not opened a PR yet; still in the initial phase | Send a message directly in the session UI: "Skip the integration tests for now — focus on getting the unit tests passing first" |
| **Refine the prompt** | Devin is confused about the task scope | Send a follow-up message narrowing the scope: "Focus only on the `/api/users` endpoint. Ignore the other endpoints for now" |
| **Provide missing context** | Devin is searching for something it cannot find | Provide the answer: "The config file is at `config/production.yml`, not `config/prod.yml`" |

**During a client walkthrough:** If Devin gets stuck, use this as a teaching moment — explain: "This is the collaboration model in action. When an agent needs help, it signals via a question or by showing no progress. The human provides context or narrows the scope, and the agent continues." Then demonstrate the recovery live.

### 7.2 CI Fails Unexpectedly

**Symptoms:** Devin's PR has CI failures that are not immediately obvious to resolve.

**Decision tree:**

```
CI fails on Devin's PR
    ↓
Is the failure in Devin's code?
├── Yes → Devin should iterate (it monitors CI and reads logs)
│         If Devin does not iterate: leave a PR comment pointing
│         to the failure: "CI failed on test X — please investigate"
│
└── No (infrastructure / flaky test / pre-existing)
    → Check: does this same failure occur on the base branch?
    ├── Yes → Explain to the client: "This is a pre-existing issue
    │         unrelated to Devin's changes. The CI environment has
    │         [describe issue]."
    └── No → Something about Devin's changes triggers it indirectly.
             Leave a PR comment with context about the failure.
```

**During a client walkthrough:** CI failures are common and expected. Frame them positively: "Devin monitors CI and iterates — this is the same workflow a human developer follows. Watch the session timeline as Devin reads the logs and pushes a fix." If the failure is environmental, explain the difference between code issues (Devin iterates) and infrastructure issues (team resolves).

### 7.3 Walkthrough Goes Off-Script

**Common off-script scenarios and recovery:**

| Scenario | Recovery Pattern |
|----------|-----------------|
| Devin produces a different (but valid) approach | Accept it. Explain: "Devin chose a different implementation pattern. The verification model (tests/CI) confirms it works correctly. This is expected — we specify *what*, not *how*" |
| Devin finishes too quickly (before you finish explaining) | Review the PR together. Use the completed work to walk through what Devin did and why. The artifact is the same; the timing just shifted |
| Devin finishes too slowly (audience gets restless) | Switch to reviewing a pre-completed example. Explain: "Complex tasks take time — the same is true for human engineers. Let me show you the completed result from a previous run" |
| Devin asks a question instead of proceeding | Answer the question live. Explain: "Devin asks for clarification when requirements are ambiguous. This is preferable to guessing — it is the same behavior you want from a human team member" |
| Devin produces output in the wrong repo / branch | Redirect via session message: "Please work in `<correct-repo>` on branch `<correct-branch>`." Explain: prompt specificity prevents this in production |

### 7.4 Client Asks a Question You Cannot Answer

**Defer patterns for questions you cannot answer immediately:**

| Question Type | Defer Pattern |
|--------------|---------------|
| **Detailed technical architecture** (e.g., "What encryption is used for secrets at rest?") | "That is a platform-level security detail I want to answer precisely. Let me get you the exact specification from our engineering team and follow up within 24 hours." |
| **Pricing / cost modeling** | "Pricing depends on your specific usage pattern. Let me connect you with our team who can model this based on your requirements." |
| **Capability you are unsure about** | "I want to verify that before committing to an answer. Let me confirm with the platform team and get back to you with a definitive response." |
| **Comparison to competitors** | "I prefer to focus on what we do rather than characterize other products. Let me show you how Devin handles [their specific use case] and you can evaluate directly." |
| **Future roadmap** | "I cannot commit to specific timelines on unreleased features. What I can tell you is what is available today and how it addresses your requirements." |

**General principles:**
- Never guess or speculate on technical details. Wrong answers erode trust faster than "I do not know, but I will find out"
- Always pair the defer with a follow-up commitment and timeline
- Redirect to what you CAN show: "I am not sure about that specific detail, but let me show you something directly relevant to your use case"

### 7.5 Scenario Exercises

**Exercise 7A: Stuck Agent Recovery**

*Scenario:* You are running a security remediation walkthrough for a client. Devin has been working for 5 minutes but has not opened a PR. The session timeline shows Devin repeatedly trying to run `npm install` but failing with a "network timeout" error.

*What do you do?*

*Reference answer:*
1. Explain to the client: "Devin is hitting a network connectivity issue — this is an infrastructure problem, not a logic problem"
2. Send a session message: "Skip `npm install` — the dependencies are already installed in the environment. Run `npm audit` directly"
3. If that does not resolve it, explain the environment blueprint: "In a configured organization, this would be pre-installed in the VM snapshot. Let me show you what the resolution looks like" — and switch to a pre-completed example
4. Use this as a teaching moment about environment blueprints: "This is why we configure the environment layer — so sessions start ready to build"

**Exercise 7B: Off-Script Migration**

*Scenario:* You are running a MuleSoft → Spring Boot migration walkthrough. Devin converts the endpoint correctly on the first try — the contract test passes without hitting the expected "empty 404 body" bug. The client asks: "So where is the verification value if it just works?"

*What do you do?*

*Reference answer:*
1. Explain: "The verification value is that we *know* it works — not because it looks right, but because the contract tests passed against the OpenAPI spec. Without those tests, we would be relying on 'it compiles and looks reasonable' review"
2. Show the test output and explain what was verified: endpoint existence, schema parity, HTTP status codes, error response format
3. Reference the worked example from the playbook documentation: "In many cases, the contract tests DO catch a divergence — here is a documented example where the 404 response format was wrong"
4. Pivot to the broader point: "The value is not that bugs appear in every walkthrough — it is that you have programmatic proof of parity. When this runs across 50 endpoints in parallel, that proof scales without human review of each one"

**Exercise 7C: Client Questions During Walkthrough**

*Scenario:* During a feature development walkthrough, the client asks: "What happens if Devin writes code that introduces a security vulnerability? How do we catch that?"

*What do you do?*

*Reference answer:*
1. Explain the layered verification model: "Devin's PRs go through the same CI pipeline as human-authored code. If you have SAST scanning in CI, it catches vulnerabilities in Devin's code the same way it catches them in human code"
2. Add Devin Review: "Devin Review is an automated code reviewer that analyzes new PRs for bugs, security issues, and quality problems. It runs automatically on Devin's PRs"
3. Connect to the automation topology: "You can configure an event-driven automation where a SAST finding on Devin's PR triggers a remediation session — the same closed loop we showed in the security walkthrough"
4. Emphasize the PR gate: "Devin never merges its own work. The PR is the review gate. Human reviewers and automated tools verify the code before it reaches production"

---

### Knowledge Checks — Handling Unexpected Outcomes

**Knowledge Check 7.1**
Q: During a walkthrough, Devin's session shows no progress for 3 minutes. What is your first action?
A) "Restart the session from scratch"
B) "Check the session timeline to understand what Devin is doing — is it looping on an error, waiting for something, or making slow progress on a complex task?"
C) "Tell the client Devin is broken"
D) "Switch to a different walkthrough"
*Answer: B — Always diagnose before acting. Check the session timeline to understand the state. Many cases of "no progress" are actually Devin working on a complex step (installing dependencies, running a long build). If it is genuinely stuck, then apply recovery patterns.*

**Knowledge Check 7.2**
Q: CI fails on Devin's PR during a walkthrough. The failure is a flaky test that also fails intermittently on the base branch. How do you explain this to the client?
A) "Devin broke the tests"
B) "Verify the failure exists on the base branch, then explain: 'This is a pre-existing flaky test unrelated to Devin's changes. The same failure occurs on the base branch without Devin's code'"
C) "Ignore it and move on"
D) "Ask Devin to fix the flaky test"
*Answer: B — Always verify before claiming a failure is pre-existing. Check the base branch CI status. If confirmed pre-existing, explain clearly to the client that this is unrelated to Devin's changes.*

**Knowledge Check 7.3**
Q: A client asks "What encryption standard does Devin use for secrets at rest?" and you do not know the answer. What do you do?
A) "Guess AES-256"
B) "Say 'I do not know' and change the subject"
C) "Acknowledge you want to answer precisely, commit to following up within 24 hours with the exact specification from the engineering team"
D) "Say it is proprietary and you cannot share"
*Answer: C — Never guess on technical security details. Acknowledge the question, commit to a specific follow-up timeline, and deliver. Wrong answers erode trust faster than "I will confirm and follow up."*

### Key Takeaways

- **Diagnose before acting** — check the session timeline to understand state before applying recovery patterns
- **Failures during walkthroughs are recoverable** — every failure mode has a known recovery pattern and can be framed as a teaching moment about the platform
- **Never guess on questions you cannot answer** — commit to a specific follow-up timeline instead. Wrong answers damage credibility more than "I will confirm"
- **The PR feedback loop is the universal recovery tool** — when Devin is stuck, a PR comment or session message with specific guidance is almost always sufficient to unblock

---

<a id="design-challenge-end-to-end-engagement-architecture"></a>
## Design Challenge: End-to-End Engagement Architecture

This comprehensive exercise mirrors a real engagement architecture proposal. You must design the full Devin integration architecture for a client scenario.

### Scenario

A systems integrator client has the following environment:
- **80 microservices** across 4 product teams (20 services each), deployed on Kubernetes
- **3 priority initiatives:**
  1. Automated security remediation — SAST findings should be fixed automatically when detected in CI
  2. Quarterly framework upgrades — Spring Boot 2.7 → 3.x across all 80 services
  3. Incident response — when a PagerDuty alert fires, Devin should investigate, triage, and implement a fix or escalate
- **Existing tools:** GitHub (repos + CI), SonarQube (SAST), PagerDuty (alerting), Jira (tickets), Datadog (observability), Confluence (docs)
- **Constraints:** Least-privilege access, no direct production access, all changes through PRs, existing CI/CD pipeline must be preserved

### What You Should Produce

**1. Automation Topology Diagram (describe in text)**

Design the trigger architecture for each initiative:
- What events fire which automations?
- What safeguards are on each automation?
- Where do parent-child patterns apply?
- What is the escalation path for each?

**2. Configuration List**

List every component of the shared context layer you would configure:
- Git connections (which repos/orgs)
- Environment blueprint (what runtimes/tools)
- Knowledge notes (what standards)
- Playbooks (what procedures)
- MCP servers (which integrations)
- Secrets (what credentials)
- Automations (what triggers)

**3. Automation Rules**

For each automation, specify:
- Trigger (event source + conditions)
- Prompt template (what Devin does)
- Safeguards (invocation limits, ACU caps, escalation)
- Expected output (PR, issue, report)

### Reference Answer

**1. Automation Topology**

```
Initiative 1: Security Remediation (Event-Driven)
─────────────────────────────────────────────────
SonarQube scan → GitHub check_run (conclusion: failure)
    ↓ Devin Automation (trigger: check_run fail on "sonar-scan")
Devin Session
    ├── Reads findings from check run
    ├── Triages by severity (CRITICAL, HIGH)
    ├── Remediates in correct manifest/source
    ├── Runs affected tests
    └── Pushes fix → re-scan converges to green
Safeguards: 3 invocations/PR/hour, 50 ACU/session, escalate after 2

Initiative 2: Quarterly Framework Upgrade (Campaign)
────────────────────────────────────────────────────
Manual trigger: Engineer starts parent session with list
    ↓ Parent Agent
Parent analyzes 80 services, creates upgrade playbook
    ├── Spawns Child 1 → service-a → PR
    ├── Spawns Child 2 → service-b → PR
    ├── ... (10 concurrent, 80 total)
    └── Monitors, escalates failures
Safeguards: 10 concurrent children, 100 ACU/child,
            parent summarizes progress weekly

Initiative 3: Incident Response (Event-Driven)
──────────────────────────────────────────────
PagerDuty alert → webhook → Devin Automation
    ↓ Devin Session
Devin Session
    ├── Queries Datadog (logs, traces, metrics) via MCP
    ├── Identifies affected service
    ├── Reads recent commits for potential cause
    ├── Implements fix (if root cause is clear)
    │   └── Pushes PR for review
    └── Escalates to Jira (if complex or unclear)
Safeguards: 1 invocation/alert, 30 ACU/session,
            escalate if fix not found within 15 min
```

**2. Configuration List**

| Component | Configuration |
|-----------|--------------|
| **Git connections** | GitHub org (all 80 service repos + shared libraries) |
| **Environment** | Java 21, Maven, Node.js 20, Docker, kubectl, Helm, SonarQube CLI, Trivy |
| **Knowledge** | Java coding standards, Spring Boot 3 migration patterns, error handling conventions, microservice boundaries, API versioning policy |
| **Playbooks** | `!spring-boot-upgrade` (upgrade procedure), `!security-remediate` (scan-fix-verify), `!incident-triage` (investigate-fix-escalate) |
| **MCP servers** | Jira (tickets), Datadog (logs/traces), Confluence (runbooks), PagerDuty (incident context) |
| **Secrets** | `SONAR_TOKEN`, `JIRA_API_TOKEN`, `DATADOG_API_KEY`, `PAGERDUTY_API_KEY`, `GITHUB_TOKEN` (org), `K8S_KUBECONFIG` (read-only, staging only) |
| **Automations** | Security scan failure → remediation, PagerDuty alert → incident response, Weekly dependency audit (scheduled) |

**3. Automation Rules**

| Automation | Trigger | Prompt | Safeguards | Output |
|-----------|---------|--------|------------|--------|
| Security Remediation | `check_run` fail on "sonar-scan" in any of 80 repos | "Read findings, triage HIGH+CRITICAL, remediate, run tests, push fix" | 3/PR/hr, 50 ACU, escalate after 2 | PR with fix + re-scan |
| Incident Response | PagerDuty webhook (severity P1/P2) | "Query Datadog for logs/traces on affected service, identify root cause, implement fix or escalate to Jira" | 1/alert, 30 ACU, escalate if no fix in 15 min | PR (if fixable) or Jira issue (if complex) |
| Dependency Audit | Weekly schedule (Monday 6 AM) | "Check all dependencies for minor/patch updates, upgrade, run tests, document" | 100 ACU per session | PRs with version bumps |
| Framework Upgrade | Manual (parent session) | "Upgrade Spring Boot 2.7→3.x following playbook" | 10 concurrent children, 100 ACU/child | 80 PRs (one per service) |

---

<a id="self-assessment-rubric-for-walkthrough-delivery"></a>
## Self-Assessment Rubric for Walkthrough Delivery

Use this rubric to evaluate your own walkthrough delivery quality. Score each criterion as: **Strong** (consistently done well), **Adequate** (done but could improve), or **Needs Work** (missed or poorly executed).

### Before the Walkthrough

| Criterion | Strong | Adequate | Needs Work |
|-----------|--------|----------|------------|
| **Pattern framing** | Clearly explained the pattern Devin will follow BEFORE launching | Explained briefly but rushed | Launched without explaining what would happen |
| **Success criteria** | Stated what "done" looks like (e.g., "green contract tests") | Mentioned it vaguely | Did not define success criteria |
| **Environment verification** | Verified setup works (repos accessible, credentials configured, test harness runs) before the audience arrived | Checked during the walkthrough | Discovered issues live with no preparation |

### During the Walkthrough

| Criterion | Strong | Adequate | Needs Work |
|-----------|--------|----------|------------|
| **Narration while running** | Explained what Devin is doing and why at each stage, connecting actions to the pattern | Occasionally narrated but left dead air | Watched silently or read from a script |
| **Verification beat** | Highlighted the moment programmatic verification caught something human review would miss | Mentioned it in passing | Did not emphasize the verification value |
| **Technical accuracy** | All explanations were technically correct; no hand-waving | Mostly correct with minor imprecisions | Made incorrect claims about the platform |
| **Question handling** | Answered clearly, or acknowledged and committed to follow up | Answered but sometimes rambled | Guessed, deflected, or ignored questions |

### When Things Go Off-Script

| Criterion | Strong | Adequate | Needs Work |
|-----------|--------|----------|------------|
| **Recovery speed** | Diagnosed the issue quickly and applied the correct recovery pattern within 1-2 minutes | Took a few minutes but recovered | Got stuck for extended periods or abandoned the walkthrough |
| **Framing failures positively** | Used the failure as a teaching moment about the platform model | Acknowledged the issue neutrally | Appeared flustered or apologized excessively |
| **PR feedback demonstration** | Showed the collaboration model by leaving a comment and watching Devin iterate | Mentioned it was possible | Did not demonstrate recovery via PR comment |

### After the Walkthrough

| Criterion | Strong | Adequate | Needs Work |
|-----------|--------|----------|------------|
| **Takeaway reinforcement** | Summarized the 2-3 key points that connected to the client's specific use case | Gave generic takeaways | No summary or wrap-up |
| **Next steps** | Proposed concrete next steps for the client (e.g., "we could configure this for your repos in a pilot") | Mentioned vague follow-ups | No clear call to action |
| **Follow-up on deferred questions** | Tracked unanswered questions and followed up within committed timeline | Followed up late | Forgot to follow up |

---

<a id="gaps--future-content"></a>
## Gaps & Future Content

Additional materials needed to complete this track:

- [ ] Recorded reference walkthroughs (video of ideal delivery for each walkthrough type — security, migration, data engineering, feature development)
- [ ] Simulated environment setup guide (step-by-step instructions to configure a workshop org for live practice)
- [ ] Integration design templates (editable topology diagrams for common automation patterns)
- [ ] "Handling the hard questions" expanded guide (top 50 client questions with researched, precise answers)
- [ ] Platform configuration lab environment (pre-configured sandbox where attendees practice org setup from scratch)
- [ ] Walkthrough timing guides (suggested pacing for 30-minute, 60-minute, and 90-minute walkthrough slots)
- [ ] Competitive positioning reference (how to redirect conversations about alternative approaches to direct platform capability)
- [ ] Multi-walkthrough sequencing guide (how to chain walkthroughs in a half-day or full-day session for maximum narrative impact)
- [ ] Assessment exercises with automated grading (knowledge checks that report scores and identify weak areas)
- [ ] Client-specific customization templates (how to adapt walkthrough prompts to a specific client's repos and technology stack)
