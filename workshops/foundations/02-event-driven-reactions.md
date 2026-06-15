# Event-Driven Reactions

<a id="toc"></a>
## Table of Contents

- [The Paging Problem](#the-paging-problem)
- [The Event-Driven Alternative](#the-event-driven-alternative)
- [How Triggers Work](#how-triggers-work)
- [Common Trigger Patterns](#common-trigger-patterns)
- [Why Automation Beats Paging](#why-automation-beats-paging)
- [Safeguards and Guardrails](#safeguards-and-guardrails)
- [Exploration Activity](#exploration-activity)
- [Key Takeaways](#key-takeaways)

---

<a id="the-paging-problem"></a>
## The Paging Problem

Today, most engineering event responses follow this pattern:

```
Event occurs (CI failure, security finding, alert)
    ↓
Notification sent to a human (Slack, PagerDuty, email)
    ↓
Human reads the notification (minutes to hours later)
    ↓
Human context-switches from current work
    ↓
Human investigates (reads logs, reproduces, understands)
    ↓
Human implements a fix
    ↓
Human opens a PR, waits for CI, iterates
```

Every step in this chain introduces delay. The notification sits unread while the engineer finishes their current task. The context-switch tax is real — studies consistently show 15-25 minutes to regain deep focus after an interruption. The investigation phase often involves reading the same logs the agent could read programmatically.

**The cost is not just time — it is attention.** Every page fragments an engineer's focus, even if the response itself is quick. Multiply this across a team handling dozens of events per week and you get a workforce in constant reactive mode, unable to sustain deep work on complex problems.

<a id="the-event-driven-alternative"></a>
## The Event-Driven Alternative

An event-driven agent model inverts this:

```
Event occurs (CI failure, security finding, alert)
    ↓
Webhook fires → Devin session starts automatically
    ↓
Agent investigates (reads logs, queries systems)
    ↓
Agent implements a fix
    ↓
Agent opens a PR, monitors CI
    ↓
Human reviews the PR (minutes of effort)
```

The human's role shifts from **first responder** to **reviewer**. The investigation, diagnosis, and initial fix happen without human involvement. The engineer's attention is only required at the point where their judgment adds value: reviewing the proposed solution.

<a id="how-triggers-work"></a>
## How Triggers Work

The technical architecture for event-driven reactions:

```
┌─────────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│   Event Source       │     │  Trigger Layer    │     │  Devin Session   │
│                     │     │                  │     │                 │
│  • CI pipeline      │────▶│  • Webhook URL   │────▶│  • Reads context │
│  • SAST scanner     │     │  • Filter rules  │     │  • Investigates  │
│  • Alert system     │     │  • Prompt template│     │  • Implements fix│
│  • Issue tracker    │     │  • Deduplication  │     │  • Opens PR      │
│  • Scheduler (cron) │     │                  │     │                 │
└─────────────────────┘     └──────────────────┘     └─────────────────┘
```

The trigger layer is the intelligence between the event and the response. It:
- **Filters** — Not every event warrants a response. Only HIGH/CRITICAL findings, only certain repos, only specific failure types.
- **Templates** — Constructs a specific, actionable prompt from the event payload. Instead of "a CI failure happened," the agent receives "Build failed in repo X on branch Y. The failing test is Z. Error log attached."
- **Deduplicates** — Prevents the same event from spawning multiple sessions.
- **Rate-limits** — Caps the number of concurrent sessions to avoid runaway costs.

<a id="common-trigger-patterns"></a>
## Common Trigger Patterns

| Trigger | Event Source | Agent Response | Human Touchpoint |
|---------|-------------|----------------|-----------------|
| **CI failure** | GitHub Actions / Jenkins / Azure DevOps | Read failure logs, diagnose, push fix to same branch | Review the fix commit |
| **Security finding** | SonarQube / Snyk / Checkmarx scan | Read advisory, apply remediation, run re-scan | Review the remediation PR |
| **Incident alert** | PagerDuty / OpsGenie / Azure Monitor | Query logs via MCP, identify root cause, open fix PR | Review the diagnosis and fix |
| **Ticket assignment** | Jira / Linear / Azure DevOps | Read acceptance criteria, implement, open PR | Review implementation |
| **PR opened** | GitHub webhook | Run Devin Review for bugs, security, and quality | Read review comments |
| **Dependency advisory** | GitHub Dependabot / Renovate | Bump version, run tests, verify compatibility | Merge if green |

### The Key Difference from Scheduled Work

Event-driven reactions are **responsive** — they fire in real time when something happens. This is different from scheduled sessions (covered in [Automations](08-automations.md)) which run on a cadence regardless of events.

Both patterns reduce human toil, but event-driven reactions are uniquely valuable for **time-sensitive** responses: security vulnerabilities, production incidents, and CI failures that block other engineers.

<a id="why-automation-beats-paging"></a>
## Why Automation Beats Paging

| Dimension | Human-Paged Response | Event-Driven Agent |
|-----------|---------------------|-------------------|
| **Response time** | Minutes to hours (depends on availability) | Seconds (webhook → session start) |
| **Consistency** | Varies by engineer, time of day, workload | Same process every time |
| **Context-switch cost** | 15-25 min of disrupted focus per page | Zero — human reviews asynchronously |
| **After-hours coverage** | On-call rotation, burnout risk | 24/7 without staffing concerns |
| **Scale** | Degrades as event volume grows | Handles N events in parallel |
| **Audit trail** | Ad-hoc (Slack threads, scattered commits) | Complete session timeline + PR |

The goal is not to remove humans from the loop — it is to **move humans to the point where their judgment matters most**. Investigation and initial implementation are mechanical. Review and approval require human judgment.

<a id="safeguards-and-guardrails"></a>
## Safeguards and Guardrails

Event-driven automation requires guardrails to prevent runaway behavior:

**Loop prevention:** Filter out events caused by the agent itself. If Devin opens a PR that triggers CI, and CI failure triggers another Devin session, you get an infinite loop. The fix: check the PR author and skip events from `devin-ai-integration[bot]`.

**Concurrency limits:** Cap the maximum number of simultaneous sessions per trigger type. If a security scan surfaces 200 findings at once, you probably want 10 parallel sessions, not 200.

**Idempotency:** Use event IDs or deduplication keys to prevent the same event from spawning multiple sessions if the webhook fires more than once.

**Escalation thresholds:** If the agent fails to resolve an event after N attempts, escalate to a human rather than retrying indefinitely.

**Scope boundaries:** Restrict which repos, branches, and finding severities trigger automated responses. Start narrow, expand as confidence grows.

---

<a id="exploration-activity"></a>
## Exploration Activity

**Try this:** Think about the last three times you (or your team) were paged or interrupted by a reactive task — a CI failure, a security finding, an incident alert.

For each one, ask:
1. How much time elapsed between the event and the start of human investigation?
2. How much of the investigation was mechanical (reading logs, querying dashboards)?
3. Could the initial diagnosis and fix attempt have been automated?
4. What would you have been doing instead if the agent handled it?

If you have access to the Devin web app, navigate to **Settings → Automations** and browse the available trigger types. Notice how each one maps to the patterns described above.

---

<a id="key-takeaways"></a>
## Key Takeaways

- Paging humans for every event is expensive — not just in time, but in attention and focus
- Event-driven agents respond in seconds, work 24/7, and handle events at scale without degrading
- The human role shifts from first responder to reviewer — your judgment is applied where it matters most
- The trigger layer (filter → template → deduplicate → rate-limit) is what makes automation safe and controlled
- Start narrow: one trigger type, one repo, conservative filters. Expand as trust builds
- Guardrails (loop prevention, concurrency limits, escalation thresholds) are non-negotiable for production automation
