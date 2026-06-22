# 4. Automation Topology Design

*Reference: [`shared/general-themes/platform-capabilities.md`](../../../shared/general-themes/platform-capabilities.md), [`shared/general-themes/design-patterns-for-devin.md`](../../../shared/general-themes/design-patterns-for-devin.md)*

Given client requirements, design the webhook/schedule/trigger architecture. This section covers the building blocks, design principles, and exercises for constructing automation topologies.

## 4.1 Event-Driven Triggers

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

## 4.2 Scheduled Sessions

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

## 4.3 The Parent-Child Pattern for Campaigns

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

## 4.4 Design Exercises

**Exercise 4A: Security Remediation Topology**

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

**Exercise 4B: Quarterly Framework Upgrade Campaign**

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

## Knowledge Checks

**Knowledge Check 4.1**
Q: A client wants Devin to respond to PagerDuty alerts by investigating and triaging incidents. What type of trigger is this?
A) "Scheduled session"
B) "Event-driven trigger via webhook from PagerDuty"
C) "Parent-child orchestration"
D) "Manual prompt"
*Answer: B — Incident response is event-driven. PagerDuty fires a webhook when an alert occurs, which triggers a Devin Automation that creates a session to investigate.*

**Knowledge Check 4.2**
Q: When designing an event-driven security remediation automation, what prevents the automation from firing in an infinite loop (scanner detects → Devin fixes → push triggers scanner → scanner detects new issue → Devin fixes → ...)?
A) "Devin is smart enough to stop"
B) "Invocation limits cap how many times the automation fires per time window, and loop prevention filters out Devin's own events"
C) "The scanner only runs once"
D) "The automation has a built-in timer"
*Answer: B — Two safeguards work together: invocation limits (max N fires per PR/hour) prevent runaway loops, and trigger conditions filter out events from `devin-ai-integration[bot]` to avoid self-triggering.*

**Knowledge Check 4.3**
Q: A client has 80 microservices that need the same dependency upgrade. What pattern do you recommend?
A) "One session that modifies all 80 services sequentially"
B) "Parent-child orchestration: one parent spawns a child session per microservice, each following the same playbook and producing its own PR"
C) "80 manual sessions launched by hand"
D) "A scheduled session that upgrades one service per week"
*Answer: B — Parent-child orchestration is the correct pattern for large-scale, independent, repeatable work. Each child operates on its own VM/branch/PR, failures are isolated, and the playbook ensures consistency.*

**Knowledge Check 4.4**
Q: What elements must every automation topology include for production safety?
A) "Just a trigger and a prompt"
B) "Invocation limits, ACU budget per session, trigger conditions (filtering), loop prevention, and an escalation policy"
C) "Only ACU limits"
D) "A human watching every session"
*Answer: B — Production automation topologies require all five safeguards: invocation limits (rate control), ACU budget (cost control), trigger conditions (scope control), loop prevention (self-trigger avoidance), and escalation (human fallback).*

## Key Takeaways

- **Three trigger types cover most scenarios:** event-driven (reactive), scheduled (proactive), and parent-child campaigns (one-time large-scale)
- **Safeguards are mandatory, not optional:** invocation limits, ACU caps, loop prevention, and escalation policies are non-negotiable in production topologies
- **Parent-child scales linearly:** one agent becomes N agents, each producing independent PRs. Failures in one child do not affect others
- **Design the escalation path first:** knowing when and how the automation hands off to humans is more important than the happy path
