# Workshop: Agentic SDLC — Self-Healing QA & Autonomous Engineering at Scale

## Overview

| | |
|---|---|
| **Focus** | Multi-agent orchestration across the SDLC: code intelligence, self-healing regression, event-driven quality pipelines, and reverse/forward engineering |
| **Duration** | 1 day (4 sessions, ~90 min each) |
| **Audience** | Engineering leads, SDETs, platform engineers, and architects designing Devin integration into enterprise SDLC workflows |
| **Prerequisites** | Familiarity with CI/CD concepts, basic Devin session experience recommended |

## Learning Objectives

By the end of this workshop, participants will be able to:

1. Design multi-agent orchestration patterns using parent-child sessions and playbooks
2. Use DeepWiki, Ask Devin, and dependency graph analysis for code intelligence and impact analysis
3. Build self-healing regression pipelines using event-driven automations, scheduled sessions, and quality playbooks
4. Reverse-engineer legacy systems into modernization pipelines and forward-engineer new features end-to-end
5. Configure automations (event-driven triggers + scheduled sessions) for continuous quality and security

## Workshop Narrative

This workshop covers the full SDLC through an agentic lens: how Devin operates as a team-based engineering resource across planning, implementation, testing, and operations. Each session builds on the previous — starting with foundational architecture concepts, progressing through code intelligence and analysis, applying those concepts to self-healing quality pipelines, and culminating in a full agentic development lifecycle from reverse engineering through forward engineering.

The sessions are designed to show Devin working across the engineering spectrum:
- **Session 1** establishes the architecture: orchestration patterns, event-driven triggers, and the shared context layer
- **Session 2** builds the intelligence layer: how Devin understands codebases, maps dependencies, and performs impact analysis
- **Session 3** applies intelligence to quality: self-healing regression, flaky test detection, and CI/CD-triggered remediation
- **Session 4** ties it together: reverse-engineering a legacy system and forward-engineering new features using the patterns from Sessions 1-3

## Getting the Most from This Workshop

> **Devin works autonomously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin keeps working in the background.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem before Devin executes.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments and Devin will wake up and address them — this is the core feedback loop.
- **Try parallel sessions.** Running multiple sessions simultaneously mirrors real enterprise usage and demonstrates team-based operation at scale.

### Quick Start (experienced attendees)

Already comfortable with Devin basics? Jump to the hands-on labs:

1. [Session 2 Lab — Dependency Graph Analysis](#lab-2a--dependency-graph-analysis)
2. [Session 3 Lab — Continuous Quality Engineering](#lab-3a--continuous-quality-engineering)
3. [Session 3 Lab — Event-Driven Regression Healing](#lab-3b--event-driven-regression-healing)
4. [Session 4 Lab — Legacy Reverse Engineering](#lab-4a--legacy-reverse-engineering)
5. [Session 4 Lab — Forward Engineering a New Feature](#lab-4b--forward-engineering-a-new-feature)

---

<a id="toc"></a>
## Table of Contents

- [Session 1 — Foundations: Agentic Architecture & the Devin Platform](#session-1--foundations-agentic-architecture--the-devin-platform)
  - [1A: Agent Orchestration Patterns](#1a-agent-orchestration-patterns)
  - [1B: Event-Driven Reactions & Automations](#1b-event-driven-reactions--automations)
  - [1C: The Shared Context Layer](#1c-the-shared-context-layer)
  - [1D: Exploration — Platform Walkthrough](#1d-exploration--platform-walkthrough)
- [Session 2 — Code Intelligence & Impact Analysis](#session-2--code-intelligence--impact-analysis)
  - [2A: DeepWiki & Ask Devin for Codebase Understanding](#2a-deepwiki--ask-devin-for-codebase-understanding)
  - [Lab 2A: Dependency Graph Analysis](#lab-2a--dependency-graph-analysis)
  - [Lab 2B: Cross-Repo Impact Mapping](#lab-2b--cross-repo-impact-mapping)
- [Session 3 — Self-Healing Regression & Continuous Quality](#session-3--self-healing-regression--continuous-quality)
  - [3A: The Self-Healing Pattern](#3a-the-self-healing-pattern)
  - [Lab 3A: Continuous Quality Engineering](#lab-3a--continuous-quality-engineering)
  - [Lab 3B: Event-Driven Regression Healing](#lab-3b--event-driven-regression-healing)
  - [Lab 3C: Feature Flags for Variant Testing](#lab-3c--feature-flags-for-variant-testing)
- [Session 4 — Agentic Development Lifecycle](#session-4--agentic-development-lifecycle)
  - [4A: Reverse Engineering with Devin](#4a-reverse-engineering-with-devin)
  - [Lab 4A: Legacy Reverse Engineering](#lab-4a--legacy-reverse-engineering)
  - [4B: Forward Engineering with Devin](#4b-forward-engineering-with-devin)
  - [Lab 4B: Forward Engineering a New Feature](#lab-4b--forward-engineering-a-new-feature)
  - [Lab 4C: Parallel Modernization Pipeline (Capstone)](#lab-4c--parallel-modernization-pipeline-capstone)
- [Suggested Formats](#suggested-formats)
- [Repos Required](#repos-required)
- [Deliverables & Takeaways](#deliverables--takeaways)
- [Devin Features Checklist](#devin-features-checklist)

---

<a id="session-1--foundations-agentic-architecture--the-devin-platform"></a>
## Session 1 — Foundations: Agentic Architecture & the Devin Platform

**Duration:** 90 minutes | **Type:** Concepts + platform exploration

This session establishes the mental model: how agents distribute work, how orchestration patterns apply to real SDLC workflows, and how the shared context layer (Knowledge, Playbooks, Automations) creates persistent intelligence across sessions.

<a id="1a-agent-orchestration-patterns"></a>
### 1A: Agent Orchestration Patterns

**Reference:** [Agent Orchestration](../../../../workshops/foundations/concepts/03-agent-orchestration.md) | [Orchestration Patterns in Practice](../../../../workshops/training-tracks/engineering/05-orchestration-patterns.md)

Devin operates within four orchestration patterns that map to different SDLC needs:

| Pattern | SDLC Role | Agent Analogy |
|---------|-----------|---------------|
| **Single Agent** | One task, one PR — bug fixes, features, test generation | Executor |
| **Parent-Child** | Campaign decomposition — migrations, security remediation across N repos | Planner + N Executors |
| **Event-Driven** | Reactive response — CI failure fix, SAST remediation, incident triage | Validator + Repair |
| **Scheduled** | Proactive maintenance — dependency bumps, coverage monitoring, compliance | Recurring Validator |

The user's original "Planner, Executor, Validator, Repair" agent taxonomy maps directly to these patterns:

- **Planner Agent** = Parent session that analyzes scope, creates a playbook, and spawns children
- **Executor Agent** = Child sessions (or single sessions) that implement changes
- **Validator Agent** = Devin Review on PRs + scheduled quality audits + event-driven test runs
- **Repair Agent** = Event-driven automations that respond to CI failures, scan findings, and test regressions

**Try this:** Open [Agent Orchestration](../../../../workshops/foundations/concepts/03-agent-orchestration.md) and work through the exploration activity — categorize your team's current sprint items by which orchestration pattern fits best.

<a id="1b-event-driven-reactions--automations"></a>
### 1B: Event-Driven Reactions & Automations

**Reference:** [Event-Driven Reactions](../../../../workshops/foundations/concepts/02-event-driven-reactions.md) | [Automations](../../../../workshops/foundations/product/cloud/08-automations.md)

Event-driven triggers are the "hooks" that connect Devin to your SDLC toolchain. The architecture:

```
Event Source → Webhook → Filter/Route → Devin Session → PR
```

Common SDLC hooks:

| Hook Point | Trigger | Devin's Response |
|------------|---------|-----------------|
| Merge to main | CI pipeline webhook | Run regression suite, fix failures |
| PR opened | GitHub webhook | Devin Review for quality/security |
| SAST scan complete | Scanner webhook | Remediate HIGH/CRITICAL findings |
| Ticket assigned | Jira/ADO webhook | Read acceptance criteria, implement |
| Schedule (cron) | Weekly timer | Dependency bumps, coverage audits |

**Safeguards** are non-negotiable:
- Filter out `devin-ai-integration[bot]` events (prevent infinite loops)
- Set concurrency limits and retry caps
- Use idempotency keys for deduplication
- Define escalation thresholds

<a id="1c-the-shared-context-layer"></a>
### 1C: The Shared Context Layer

**Reference:** [Platform Capabilities](../../../../shared/general-themes/platform-capabilities.md)

The shared context layer is what makes Devin's "memory" persist across sessions:

| Component | SDLC Role | Persistence |
|-----------|-----------|-------------|
| **Knowledge notes** | Coding standards, architecture decisions, test conventions | Permanent — retrieved automatically in every session |
| **Playbooks** | Repeatable methodology (QA audit, security remediation, migration) | Permanent — triggered manually, by schedule, or by event |
| **DeepWiki** | Auto-generated architectural documentation for every connected repo | Auto-updated — always reflects current codebase |
| **Environment blueprints** | Pre-built VM images — sessions boot ready to build/test | Permanent — every session starts with the same tooling |
| **MCP integrations** | Pre-configured connections to Jira, Datadog, Confluence, etc. | Permanent — available in every session |

This is the "memory layer" that the original agenda describes. Short-term context lives within a single session (the codebase, the PR, the CI logs). Long-term context lives in the shared layer and compounds across every session in the organization.

**Key insight:** Investing in the shared context layer is a one-time cost that pays off in every future session. A Knowledge note capturing "our test conventions" improves test generation quality for every engineer who uses Devin going forward.

<a id="1d-exploration--platform-walkthrough"></a>
### 1D: Exploration — Platform Walkthrough

**Try this:** Navigate the Devin web app and explore:

1. **Automations** — browse trigger types. Identify one event your team responds to manually today (CI failure, security finding, ticket triage). Draft a prompt template for it.
2. **Knowledge** — create a Knowledge item capturing a testing or coding convention from your team.
3. **Ask Devin** — pick a connected repo and ask: *"What are the main architectural components? What testing patterns does this codebase use?"*

---

<a id="session-2--code-intelligence--impact-analysis"></a>
## Session 2 — Code Intelligence & Impact Analysis

**Duration:** 90 minutes | **Type:** Concepts + hands-on labs

This session covers how Devin builds understanding of codebases — the intelligence layer that powers every downstream task. The original agenda's "Knowledge Graph for Code Intelligence" maps to Devin's combination of DeepWiki, Ask Devin, and dependency graph analysis.

<a id="2a-deepwiki--ask-devin-for-codebase-understanding"></a>
### 2A: DeepWiki & Ask Devin for Codebase Understanding

Devin's code intelligence operates at three levels:

```
┌─────────────────────────────────────────────────────────┐
│  Level 1: DeepWiki (Auto-Generated)                      │
│                                                         │
│  • Architecture overview and component relationships     │
│  • API surface documentation                            │
│  • Data model and schema mappings                       │
│  • Dependency relationships between modules              │
│  • Auto-updated when code changes                       │
└─────────────────────────────────────────────────────────┘
                         ↕
┌─────────────────────────────────────────────────────────┐
│  Level 2: Ask Devin (Interactive Analysis)               │
│                                                         │
│  • Impact analysis: "What breaks if I change X?"        │
│  • Dependency mapping: "What depends on this service?"  │
│  • Architecture queries: "How does auth flow work?"     │
│  • Feasibility assessment: "Can this be safely refactored?" │
└─────────────────────────────────────────────────────────┘
                         ↕
┌─────────────────────────────────────────────────────────┐
│  Level 3: Session-Level Analysis (Deep Dive)             │
│                                                         │
│  • Static analysis and dependency graph extraction       │
│  • Cross-file impact tracing                            │
│  • Test coverage mapping to code paths                  │
│  • Architecture decision documentation                  │
└─────────────────────────────────────────────────────────┘
```

This three-level intelligence model covers the "nodes and relationships" from the original knowledge graph concept:

| KG Concept | Devin Equivalent |
|------------|-----------------|
| Nodes: repos, APIs, DB, test cases | DeepWiki auto-documents all of these per repo |
| Relationships: `depends_on`, `tested_by` | Dependency graph analysis + test coverage mapping |
| Extracting graph from code/SQL/SOA | Ask Devin + session-level static analysis |
| Impact analysis queries | Ask Devin: *"What breaks if I change this?"* |

---

<a id="lab-2a--dependency-graph-analysis"></a>
### Lab 2A — Dependency Graph Analysis

- **Module:** [Dependency Graph Analysis](../../../../labs/architecture-design/dependency-graph-analysis.md)
- **Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)
- **Objective:** Map internal dependencies, identify coupling hotspots, and produce an impact analysis report
- **Duration:** 45 min

#### Step 1: Paste into Devin

```
Analyze the internal dependency graph of
uc-spring-boot-upgrade-microservice-extraction.
Map the package-level dependencies across the
application — controllers, datafetchers, services,
repositories, and domain models. Identify:
(1) any circular dependencies between packages,
(2) tightly coupled modules where changes in one
package would cascade across many others,
(3) packages with the highest fan-in and fan-out.

Produce a dependency analysis report in
docs/dependency-analysis.md that includes a
text-based dependency graph, a list of coupling
hotspots, and prioritized refactoring
recommendations. Implement the top quick-win
decoupling change.
```

#### Step 2: Research with Ask Devin

While Devin works, explore code intelligence interactively:

- *"What are the package-level dependencies in uc-spring-boot-upgrade-microservice-extraction? Which packages have the most incoming and outgoing dependencies?"*
- *"If I change the UserRepository interface, what other packages and test files would be affected?"*
- *"What database tables does the articles domain access? Map the SQL/MyBatis relationships."*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page. Compare the auto-generated architecture documentation against the dependency analysis Devin produces. Note how DeepWiki captures the high-level structure while the session-level analysis digs into coupling metrics.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the dependency report identify real coupling hotspots?
- **Leave a comment** asking Devin to trace the impact of changing a specific service interface: *"What would break if we renamed the slugify method in ArticleService?"*

#### Key Takeaways

- DeepWiki provides always-available architectural context that typically maps the major "nodes" (repos, APIs, models) and their relationships automatically
- Ask Devin enables interactive impact analysis — the "query" interface into the codebase intelligence
- Session-level dependency analysis produces detailed coupling metrics and actionable refactoring recommendations
- The combination of all three levels covers the intent behind a code intelligence knowledge graph

---

<a id="lab-2b--cross-repo-impact-mapping"></a>
### Lab 2B — Cross-Repo Impact Mapping

- **Repository:** [calcom](https://github.com/Cognition-Partner-Workshops/calcom)
- **Objective:** Analyze a large monorepo to map cross-package dependencies at scale
- **Duration:** 35 min

#### Step 1: Paste into Devin

```
Analyze the dependency graph of the calcom monorepo.
Focus on the packages under packages/ and their
relationships to apps/web/. Map which packages depend
on each other, identify:
(1) circular dependencies between packages,
(2) packages with excessive fan-out (importing from
too many other packages),
(3) packages that are tightly coupled and could
benefit from clearer interface boundaries.

Produce a dependency analysis report in
docs/dependency-analysis.md with a structured
dependency matrix, coupling hotspots, and prioritized
refactoring recommendations.
```

#### Step 2: Research with Ask Devin

- *"Which packages in the calcom monorepo have the most cross-package imports? Are there any circular dependency chains?"*
- *"If we refactored packages/prisma/, which other packages would need updating?"*

#### Key Takeaways

- Cross-package dependency analysis at monorepo scale reveals the real architecture vs. the intended architecture
- Impact analysis queries ("what breaks if X changes?") are critical prerequisites before any refactoring or modernization campaign
- This analysis directly informs Session 3 (what tests need updating when code changes) and Session 4 (which modules to extract first)

---

<a id="session-3--self-healing-regression--continuous-quality"></a>
## Session 3 — Self-Healing Regression & Continuous Quality

**Duration:** 90 minutes | **Type:** Hands-on labs

This session applies the orchestration patterns and code intelligence from Sessions 1-2 to build self-healing quality pipelines. The "Self-Healing Regression" concept maps to Devin's combination of event-driven automations, continuous quality playbooks, and feature flag-gated testing.

<a id="3a-the-self-healing-pattern"></a>
### 3A: The Self-Healing Pattern

The self-healing regression flow connects multiple Devin capabilities:

```
Code merges to main
    ↓
CI runs test suite (existing pipeline)
    ↓
Tests fail? → Event-driven automation fires
    ↓
Devin session starts automatically:
    ├── Reads CI failure logs
    ├── Performs impact analysis (which changes caused failure?)
    ├── Determines fix strategy:
    │   ├── Test needs updating (behavior changed intentionally)
    │   ├── Code has a bug (revert or fix)
    │   └── Test is flaky (stabilize or quarantine)
    ├── Implements fix
    ├── Pushes fix commit
    └── CI re-runs → verifies fix
    ↓
PR ready for human review
```

This maps to the original agenda's agent flow:

| Original Agent | Devin Implementation |
|---------------|---------------------|
| Trigger via merge hook | Event-driven automation on CI failure webhook |
| Planner Agent → impact analysis | Ask Devin + dependency graph from Session 2 |
| Test Healing Agent | Single agent session: diagnose failure, update/add/delete tests |
| Feature Flag Agent | Configuration management lab: gate variants behind flags |
| Validation Agent → execute and repair loop | CI re-run → Devin iterates until green |

---

<a id="lab-3a--continuous-quality-engineering"></a>
### Lab 3A — Continuous Quality Engineering

- **Module:** [Continuous Quality Engineering](../../../../labs/testing-qa/continuous-quality-engineering.md)
- **Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Objective:** Set up Devin as a continuous quality engineer — Playbooks for recurring audits, flaky test detection, and scheduled quality sessions
- **Duration:** 40 min

This is the foundational lab for self-healing regression. Participants build the quality infrastructure (Playbook + Knowledge + Schedule) that makes ongoing test healing automatic.

#### Step 1: Paste into Devin

```
Run the test suite for timesheet-app and generate a
quality report covering: overall test coverage, tests
that are likely flaky (non-deterministic), test files
that haven't been updated in the last 6 months, and
test anti-patterns (empty assertions, commented-out
tests, tests with no assertions).
```

#### Step 2: Create a Playbook

After reviewing the quality report, create a Playbook that encodes the self-healing methodology:

```
Playbook: Self-Healing QA Audit

1. Run the full test suite and capture pass/fail/skip
   counts
2. Generate coverage report and compare against the
   baseline (80% target)
3. Identify flaky tests (run suite 3x, flag tests
   that pass inconsistently)
4. Scan for test anti-patterns: empty assertions,
   commented-out tests, tests with no assertions,
   tests that never fail
5. Check for stale test files (not updated in 6+
   months while source files changed)
6. Fix the top 3 highest-priority issues found
7. Commit improvements and include a summary report
```

#### Step 3: Configure a Scheduled Session

Set up a Devin Scheduled Session that runs the Self-Healing QA Audit Playbook:
- **Frequency:** Weekly (e.g., every Monday at 6 AM)
- **Playbook:** Self-Healing QA Audit (from Step 2)
- **Repository:** timesheet-app
- **Expected outcome:** PR opened with coverage improvements and quality report

This turns quality from a one-time effort into a continuous practice — the "continuous learning loop" from the original agenda.

#### Step 4: Create a Knowledge Item

Capture project-specific testing conventions:

```
Knowledge: timesheet-app Testing Standards

- All API route tests must include both happy path
  and 400/404 error cases
- Use supertest for integration tests, Jest mocks
  for unit tests
- Coverage target: 80% line coverage minimum
- Flaky tests should be quarantined, not deleted
```

#### Key Takeaways

- Playbooks encode the "self-healing" methodology — consistent, repeatable quality audits that any team member (or schedule) can trigger
- Scheduled sessions make regression healing automatic — quality doesn't depend on a single person's availability
- Knowledge items accumulate testing conventions over time, making each session's output higher quality
- The combination of Playbook + Schedule + Knowledge is the foundation for autonomous quality engineering

---

<a id="lab-3b--event-driven-regression-healing"></a>
### Lab 3B — Event-Driven Regression Healing

- **Module:** [Event-Driven SAST Remediation](../../../../labs/security/event-driven-sast-remediation.md) (adapted for test regression)
- **Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Objective:** Build an event-driven pipeline where CI test failures automatically trigger Devin to diagnose and fix regressions
- **Duration:** 30 min

This lab adapts the event-driven SAST remediation pattern to test regression healing.

#### Step 1: Paste into Devin

```
Analyze the existing CI workflows in timesheet-app.
Create a new GitHub Actions workflow called
test-regression-healer.yml that:
(1) triggers on push to any branch when the test
    suite fails,
(2) filters out pushes from devin-ai-integration[bot]
    to prevent infinite loops,
(3) captures the test failure output (which tests
    failed, error messages, stack traces),
(4) posts a PR comment summarizing the failures,
(5) documents the architecture in a
    REGRESSION_HEALER.md explaining the event-driven
    flow, trigger conditions, and escalation policy.

Include a circuit breaker: if the same test has failed
3 times in a row across Devin fix attempts, escalate
to a human reviewer via a GitHub Issue instead of
retrying.
```

#### Step 2: Research with Ask Devin

- *"What test patterns in timesheet-app are most likely to be flaky? Which tests depend on timing, external services, or shared state?"*
- *"How should the regression healer distinguish between intentional behavior changes (test needs updating) and actual bugs (code needs fixing)?"*

#### Step 3 (Optional): Review & Give Feedback

- **Review the diff** — does the workflow correctly filter bot authors? Is the escalation policy sound?
- **Leave a comment** asking Devin to add Slack notification when the healer fires: *"Add a step that posts to a webhook URL when regression healing starts and when it completes"*

#### Key Takeaways

- Event-driven test healing responds to regressions in real time — no human paging required
- Bot-loop prevention (filtering `devin-ai-integration[bot]`) is essential for any self-healing pipeline
- Circuit breakers prevent infinite retry loops — escalate to humans after N failed attempts
- The scan → diagnose → fix → re-verify pattern applies identically to test regressions and security findings

---

<a id="lab-3c--feature-flags-for-variant-testing"></a>
### Lab 3C — Feature Flags for Variant Testing

- **Module:** [Configuration Management & Feature Flags](../../../../labs/devops-cicd/configuration-management-feature-flags.md)
- **Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Objective:** Add feature flag support for gating new functionality and testing variant scenarios
- **Duration:** 20 min

#### Step 1: Paste into Devin

```
Implement configuration management for timesheet-app:
(1) Create a config module that reads settings from
environment variables with sensible defaults (PORT,
DATABASE_URL, LOG_LEVEL, etc.),
(2) Add dotenv support with .env.example documenting
all variables,
(3) Implement a simple feature flag system using a
JSON config file or environment variables,
(4) Use a feature flag to gate a new "dark mode" UI
toggle — when the flag is off, the toggle is hidden.
```

#### Key Takeaways

- Feature flags enable variant testing without code branches — the "Feature Flag Agent" from the original agenda
- Devin externalizes hardcoded configuration, following 12-factor app methodology
- Flags can gate incomplete features, A/B tests, or gradual rollouts — reducing regression risk during releases

---

<a id="session-4--agentic-development-lifecycle"></a>
## Session 4 — Agentic Development Lifecycle

**Duration:** 90 minutes | **Type:** Hands-on labs (capstone)

This session ties everything together: reverse-engineering a legacy system (code understanding + documentation + analysis) and forward-engineering new features (planning + implementation + testing + review). This is the full agentic development lifecycle — from understanding existing systems to building new ones.

<a id="4a-reverse-engineering-with-devin"></a>
### 4A: Reverse Engineering with Devin

The "reverse engineering" phase maps the original agenda's agent taxonomy to Devin workflows:

| Original Agent | Devin Workflow |
|---------------|---------------|
| Code Miner Agent | DeepWiki + Ask Devin for codebase exploration |
| Semantic Analyzer Agent | Dependency graph analysis + architecture mapping |
| Documentation Agent | Automated documentation generation (API contracts, architecture docs) |

<a id="lab-4a--legacy-reverse-engineering"></a>
### Lab 4A — Legacy Reverse Engineering

- **Module:** [Legacy Modernization Combined](../../../../labs/migration-modernization/legacy-modernization-combined.md) — Phase 1 (COBOL to Java)
- **Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- **Objective:** Reverse-engineer a legacy COBOL program — understand its business logic, data structures, and I/O operations — then translate it to modern Java
- **Duration:** 35 min

#### Step 1: Research with Ask Devin (the "Code Miner")

Start with codebase understanding before any code generation:

- *"What data structures does CBACT01C.cbl use? What's the best Java representation for COBOL copybook fields?"*
- *"What are the I/O operations in this program? What files does it read and write?"*
- *"Should the Java version use Spring Boot or plain Java? What are the trade-offs?"*

#### Step 2: Paste into Devin (the "Semantic Analyzer" + "Documentation Agent")

```
Select the COBOL program CBACT01C.cbl from
uc-legacy-modernization-cobol-to-java. Analyze its
business logic, data structures, and I/O operations.

First, produce docs/SYSTEM_ANALYSIS.md documenting:
- Business logic flow (what the program does)
- Data structures (copybook fields → Java types)
- I/O operations (file reads/writes → Java I/O)
- Dependencies on other programs or copybooks
- Edge cases and error handling paths

Then rewrite the program as a Java 17+ application
with JUnit tests that verify functional equivalence.
```

#### Step 3 (Optional): Review & Give Feedback

- **Review the analysis** — does `SYSTEM_ANALYSIS.md` accurately capture the COBOL program's behavior?
- **Leave a comment** asking Devin to add more edge case tests for the data transformations

#### Key Takeaways

- Reverse engineering starts with understanding, not coding — Ask Devin and DeepWiki provide the "Code Miner" and "Semantic Analyzer" roles
- Documentation generation (the "Documentation Agent") produces reviewable analysis before any translation begins
- JUnit parity tests verify that the translation preserves business logic — the "Validation Agent" from the original agenda

---

<a id="4b-forward-engineering-with-devin"></a>
### 4B: Forward Engineering with Devin

The "forward engineering" phase maps to:

| Original Agent | Devin Workflow |
|---------------|---------------|
| Planner Agent → roadmap | Ask Devin for requirements analysis + technical design |
| Design Agent → L3/L4 artifacts | Session: produce architecture docs, API contracts, data models |
| Dev Agent → code generation | Session: implement following existing patterns |
| Test Agent → unit & progression tests | Session: generate tests (unit, integration, E2E) |
| Review Agent → quality checks | Devin Review on the PR + PR comment iteration |

<a id="lab-4b--forward-engineering-a-new-feature"></a>
### Lab 4B — Forward Engineering a New Feature

- **Module:** [New Feature Development](../../../../labs/application-development/new-feature-development.md)
- **Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Objective:** Take a feature from requirements through implementation — planning, design, code generation, testing, and review
- **Duration:** 30 min

#### Step 1: Research with Ask Devin (the "Planner Agent")

- *"What's the current architecture of timesheet-app? What patterns does it use for backend routes and frontend components?"*
- *"What would the data model look like for a Projects feature? How should it relate to existing clients and work entries?"*

#### Step 2: Paste into Devin (the "Design + Dev + Test Agents")

```
Add a "Projects" management feature to timesheet-app.
This is a React 19 + Node.js/Express + SQLite app for
tracking billable hours.

Build the full feature following existing patterns —
backend Express routes, SQLite migration (Projects
table: id, name, description, client_id FK,
start_date, end_date, status
[active/completed/on-hold], budget_hours), frontend
React components with MUI styling. Link projects to
existing clients and work entries. Include backend
API tests.
```

#### Step 3: Review & Give Feedback (the "Review Agent")

When Devin opens a PR:
- Does the implementation follow existing codebase patterns?
- Are the tests meaningful — do they cover both happy path and error cases?
- **Leave a comment:** *"Add input validation for budget_hours — it should be a positive number. Add a test for negative values."*
- **Watch Devin respond** and push a follow-up commit

#### Key Takeaways

- The full forward engineering lifecycle (plan → design → implement → test → review) runs in a single Devin session
- Ask Devin serves as the "Planner Agent" — exploring requirements and architecture before execution
- The PR feedback loop is the "Review Agent" — humans evaluate quality and steer iteration
- Devin follows existing patterns automatically — the session-level intelligence adapts to the codebase's conventions

---

<a id="lab-4c--parallel-modernization-pipeline-capstone"></a>
### Lab 4C — Parallel Modernization Pipeline (Capstone)

- **Module:** [Legacy Modernization Combined](../../../../labs/migration-modernization/legacy-modernization-combined.md)
- **Objective:** Run a full modernization pipeline using parent-child orchestration — reverse engineer, upgrade, and modernize in parallel
- **Duration:** 25 min (kick off; PRs arrive asynchronously)

This capstone ties together every pattern from the day: parent-child orchestration (Session 1), code intelligence (Session 2), quality validation (Session 3), and the full development lifecycle (Session 4).

#### Paste into Devin

```
Run a parallel modernization pipeline across three
repositories using child sessions:

Child 1 — uc-legacy-modernization-cobol-to-java:
Analyze CBACT01C.cbl business logic and translate to
Java 17+ with JUnit parity tests.

Child 2 — uc-spring-boot-upgrade-microservice-extraction:
Upgrade from Spring Boot 2.6.3 to 3.x, migrate javax
to jakarta, and extract the article management domain
into a standalone microservice with its own
Dockerfile.

Child 3 — uc-data-source-migration-jdbc-normalization:
Create modern JPA entities matching
data/modern-schema/modern_tables.sql with proper
types (LocalDate, BigDecimal, Long). Write a migration
service that transforms legacy data per
data/mappings/column_mappings.md. Rewire
LoanService.java to use modern repositories.

After all children complete, produce a
MODERNIZATION_SUMMARY.md documenting: which phases
completed, artifacts produced per repo, and any
issues requiring human review.
```

#### Key Takeaways

- Parent-child orchestration parallelizes the full modernization lifecycle — three repos, three patterns, running simultaneously
- Each child follows its own methodology independently — failures in one don't affect others
- The consolidated summary provides a single-pane view of the entire modernization program
- This is the "autonomous SDLC" at scale: Devin plans, decomposes, executes in parallel, validates, and reports

---

<a id="suggested-formats"></a>
## Suggested Formats

| Format | Duration | Sessions |
|--------|----------|----------|
| **Full day** | ~6 hours (4 × 90 min with breaks) | All four sessions in order |
| **Half day (orchestration focus)** | ~3 hours | Sessions 1 + 3 (architecture → self-healing QA) |
| **Half day (lifecycle focus)** | ~3 hours | Sessions 2 + 4 (code intelligence → development lifecycle) |
| **Executive overview** | ~2 hours | Session 1 (30 min) + one lab from each of Sessions 2-4 |

---

<a id="repos-required"></a>
## Repos Required

- [ ] uc-spring-boot-upgrade-microservice-extraction
- [ ] calcom
- [ ] timesheet-app
- [ ] uc-legacy-modernization-cobol-to-java
- [ ] uc-data-source-migration-jdbc-normalization (capstone only)
- [ ] uc-cve-remediation-regulatory-compliance (optional extension)

---

<a id="deliverables--takeaways"></a>
## Deliverables & Takeaways

Participants leave with concrete artifacts from each session:

| Deliverable | Source |
|-------------|--------|
| **Orchestration pattern reference** | Session 1 — [Design Patterns for Devin](../../../../shared/general-themes/design-patterns-for-devin.md) |
| **Dependency analysis report** | Lab 2A — `docs/dependency-analysis.md` produced by Devin |
| **Self-Healing QA Playbook** | Lab 3A — Playbook created in Devin Settings |
| **Regression healer workflow** | Lab 3B — `test-regression-healer.yml` + `REGRESSION_HEALER.md` |
| **Feature flag integration** | Lab 3C — config module + flag system in timesheet-app |
| **System analysis document** | Lab 4A — `docs/SYSTEM_ANALYSIS.md` from COBOL reverse engineering |
| **Full-stack feature PR** | Lab 4B — Projects feature with backend, frontend, tests |
| **Parallel modernization summary** | Lab 4C — `MODERNIZATION_SUMMARY.md` across three repos |

### Business Value Context

These hands-on deliverables demonstrate the enterprise value drivers:

- **Regression effort reduction:** Labs 3A-3B show how playbook-driven quality audits and event-driven regression healing reduce manual test maintenance — teams typically see significant reduction in regression cycle time
- **Faster releases:** Lab 3B's event-driven pipeline and Lab 3C's feature flags enable continuous delivery with automated quality gates
- **Accelerated modernization:** Lab 4C's parallel pipeline compresses multi-repo modernization from sequential weeks to parallel days
- **Foundation for autonomous SDLC:** The combination of automations, playbooks, knowledge, and parent-child orchestration creates a persistent engineering practice — not a one-time task

---

<a id="devin-features-checklist"></a>
## Devin Features Checklist

Encourage participants to track their progress throughout the day:

- [ ] Use Ask Devin for codebase research (Sessions 2, 4)
- [ ] Read a repo's DeepWiki documentation (Session 2)
- [ ] Create a Knowledge item (Session 1, Lab 3A)
- [ ] Create a Playbook (Lab 3A)
- [ ] Configure or document a Scheduled Session (Lab 3A)
- [ ] Use parent-child session orchestration (Lab 4C)
- [ ] Review a Devin PR and leave feedback comments (all labs)
- [ ] Watch Devin iterate on PR feedback (all labs)
- [ ] Explore Automations configuration (Session 1)
- [ ] Run parallel Devin sessions (Labs 3A + 3B simultaneously)
- [ ] Create a GitHub Actions workflow via Devin (Lab 3B)
- [ ] Use Devin for cross-language translation (Lab 4A)
- [ ] Build a full-stack feature end-to-end (Lab 4B)
