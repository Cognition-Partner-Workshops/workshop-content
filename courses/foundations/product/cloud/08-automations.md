# Automations

> **Note:** Automations are now the unified product combining event-driven and scheduled triggers. This replaces the standalone Scheduled Sessions model — all trigger sources (Slack, GitHub, Linear, webhooks, cron) are configured through the single Automations interface.

<a id="toc"></a>
## Table of Contents

- [Two Types of Automation](#two-types-of-automation)
- [Event-Driven Automations](#event-driven-automations)
- [Scheduled Sessions](#scheduled-sessions)
- [Automation Architecture](#automation-architecture)
- [Building Effective Automations](#building-effective-automations)
- [Automation Templates](#automation-templates)
- [Common Automation Recipes](#common-automation-recipes)
- [Exploration Activity](#exploration-activity)
- [Key Takeaways](#key-takeaways)

---

<a id="two-types-of-automation"></a>
## Two Types of Automation

Devin supports two automation paradigms:

| Type | Trigger | Character |
|------|---------|-----------|
| **Event-Driven** | Something happens (webhook, alert, issue created) | Reactive — respond to signals |
| **Scheduled** | Time passes (cron, daily, weekly) | Proactive — perform routine maintenance |

Both remove the need for a human to remember to initiate work. The difference is whether the trigger is an external event or the passage of time.

```
Event-Driven:        Event → Webhook → Filter → Session → PR
Scheduled:           Clock → Cron → Session → PR
```

<a id="event-driven-automations"></a>
## Event-Driven Automations

Event-driven automations connect Devin to your existing toolchain. When a signal arrives — a CI failure, a security finding, a ticket assignment — Devin starts working automatically.

### How It Works

1. **Configure a trigger** — select the event type and source (GitHub, Jira, webhook, etc.)
2. **Define conditions** — filter rules that determine which events warrant a response (severity, repo, label)
3. **Write a prompt template** — the instructions Devin receives, parameterized with event payload data
4. **Set guardrails** — concurrency limits, deduplication rules, loop prevention

### Trigger Types

| Source | Event | Example Use |
|--------|-------|-------------|
| **GitHub** | PR opened | Run Devin Review for bugs and security issues |
| **GitHub** | Issue created with label | Implement feature requests tagged `Devin:Implementation` |
| **GitHub** | Check run / CI failure | Read failure logs and push a fix |
| **GitHub** | Push to branch | Run post-push validation or code generation |
| **Slack** | Message or thread mention | Respond to requests in team channels |
| **Linear** | Ticket assigned or status change | Read acceptance criteria, implement, open PR |
| **Webhook** | Generic HTTP POST | Connect any system that can send webhooks |
| **Schedule (cron)** | Time-based recurrence | Weekly dep bumps, daily scans, monthly audits |

### Prompt Templates

Prompt templates are parameterized instructions. The automation system injects event-specific data into the template before starting the session:

```
Remediate the {{severity}} security finding in {{repo}}.

Finding: {{finding_title}}
Advisory: {{advisory_url}}
Affected file: {{file_path}}:{{line_number}}

Fix the vulnerability and run the SAST scan again to verify
the finding is resolved. Follow existing code patterns.
```

Variables like `{{repo}}`, `{{severity}}`, and `{{finding_title}}` are populated from the event payload. The agent receives a specific, actionable prompt — not a generic instruction.

### Slack-Based Triage and Deduplication

A common pattern for teams using Slack:

```
Slack channel receives alert
    ↓
Devin monitors the channel (automation trigger)
    ↓
Devin deduplicates: "Is this the same issue as the alert 5 minutes ago?"
    ↓
If new: Start investigation session
If duplicate: Thread reply with link to existing session
```

This prevents alert fatigue from spawning redundant sessions and keeps the team's Slack channel organized with status updates threaded under the original alert.

<a id="scheduled-sessions"></a>
## Scheduled Sessions

> **Update:** Automations with Schedule triggers are now the recommended approach for recurring tasks. The standalone Scheduled Sessions feature has been unified into the Automations product — configure a cron-based trigger in the Automations interface to get the same behavior with the full suite of conditions, templates, and guardrails.

Scheduled automations run on a recurring cadence — daily, weekly, or custom cron expressions. They perform proactive maintenance without human initiation.

### Configuration

Each schedule specifies:
- **Prompt** — what the agent should do
- **Repository** — which codebase to work on
- **Recurrence** — cron expression or predefined cadence (daily, weekly)
- **Playbook** (optional) — a standardized procedure to follow

### Common Schedules

| Task | Frequency | Why |
|------|-----------|-----|
| Dependency version bumps | Weekly | Keep dependencies current without sprint capacity |
| Dead code detection | Monthly | Remove unused imports, unreachable functions |
| License compliance audit | Monthly | Catch license policy violations before release |
| Documentation freshness | On code merge | Keep docs in sync with implementation |
| Test coverage monitoring | Weekly | Identify and fill coverage gaps systematically |
| Security scanning + remediation | Weekly | Find and fix vulnerabilities before they accumulate |
| API contract validation | Daily | Verify services still conform to published specs |

### Schedule vs. Event-Driven: When to Use Each

| Use Scheduled When... | Use Event-Driven When... |
|----------------------|-------------------------|
| The task is routine maintenance | The task responds to a specific signal |
| Timing doesn't matter (anytime this week is fine) | Response time matters (fix CI failure now) |
| You want predictable, periodic hygiene | You want immediate reaction to events |
| The trigger is "it's been N days" | The trigger is "something just happened" |

<a id="automation-architecture"></a>
## Automation Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                    Devin Automation Layer                          │
│                                                                    │
│  ┌─────────────┐   ┌────────────────┐   ┌───────────────────┐   │
│  │  Triggers    │   │  Filter/Route   │   │  Session Manager   │   │
│  │             │   │                │   │                   │   │
│  │ • Webhooks  │──▶│ • Conditions   │──▶│ • Spawn session   │   │
│  │ • Cron      │   │ • Deduplication│   │ • Inject prompt   │   │
│  │ • Events    │   │ • Rate limits  │   │ • Monitor output  │   │
│  │ • Slack     │   │ • Loop guard   │   │ • Track status    │   │
│  └─────────────┘   └────────────────┘   └───────────────────┘   │
│                                                                    │
└──────────────────────────────────────────────────────────────────┘
                              │
                              ▼
                    ┌────────────────────┐
                    │   Devin Sessions    │
                    │                    │
                    │  Isolated VMs      │
                    │  executing tasks   │
                    │  and opening PRs   │
                    └────────────────────┘
```

The automation layer sits between your event sources and Devin's session system. It handles the intelligence of deciding *when* to create sessions, *what* prompt to give them, and *how many* can run concurrently.

<a id="building-effective-automations"></a>
## Building Effective Automations

### Start Narrow, Expand Gradually

Begin with one automation:
1. Pick a single, well-understood trigger (e.g., weekly dependency bumps for one repo)
2. Run it for 2-3 cycles and review the output quality
3. If the results are consistently good, expand to more repos or more triggers
4. Add more automation types one at a time

### Prompt Template Best Practices

- **Be specific** — include repo names, file paths, expected behavior
- **Include success criteria** — "all tests pass", "scan shows zero HIGH findings"
- **Reference patterns** — "follow the conventions in `src/services/auth.ts`"
- **Set boundaries** — "do not modify files outside `src/api/`"

### Guardrail Checklist

Before activating any automation:
- [ ] Loop prevention: events from `devin-ai-integration[bot]` are excluded
- [ ] Concurrency limit: maximum simultaneous sessions is capped
- [ ] Idempotency: multiple events from the same underlying issue are recognized and not re-triaged (consider an intermediary queue or deduplication key to prevent duplicate signals from reaching Devin)
- [ ] Escalation: agent stops and surfaces findings if it cannot resolve the issue
- [ ] Scope: only specific repos/branches/severities trigger responses
- [ ] Invocation limit: cap how many times the automation fires per time window (e.g., at most 10 per hour)
- [ ] ACU limit: set a maximum compute budget per session to prevent runaway consumption

<a id="common-automation-recipes"></a>
## Common Automation Recipes

### Recipe 1: Weekly Dependency Hygiene

**Type:** Scheduled (weekly, Monday morning)
**Prompt:**
```
Check all dependencies in [repo] for available updates.
Bump patch and minor versions where tests pass. Bundle
all compatible updates together. Skip major version
bumps — flag those in the PR description for manual
review.
```

### Recipe 2: CI Failure Auto-Fix

**Type:** Event-driven (GitHub Actions check_run failure)
**Condition:** Only for repos in the `backend/` namespace, only for branches with open PRs
**Prompt:**
```
Build failed on branch {{branch}} in {{repo}}. Read the
CI logs, identify the failure, and push a fix commit to
the same branch.
```

### Recipe 3: Security Finding Remediation

**Type:** Event-driven (SAST scan webhook)
**Condition:** Severity >= HIGH
**Prompt:**
```
Remediate {{finding_title}} ({{severity}}) in {{repo}}
at {{file_path}}:{{line}}. Apply the recommended fix from
{{advisory_url}}. Run the scan again to verify the finding
is resolved.
```

### Recipe 4: New Ticket Implementation

**Type:** Event-driven (Jira/Linear ticket assigned to Devin)
**Prompt:**
```
Implement the work described in ticket {{ticket_id}}:
{{ticket_title}}. Read the full description and acceptance
criteria. Work in {{repo}} on a new branch. Link the
resulting PR back to the ticket.
```

### Recipe 5: Security Swarm Auto Scan

**Type:** Scheduled (weekly) or on-demand
**Setup:** Navigate to **Security** in the Devin web app, select the target repository, and enable Auto Scan with a weekly cadence. Security Swarm uses Agentic MapReduce to divide the repo among parallel Devins, build a threat model, and investigate vulnerabilities. Create a Profile to save scan configuration for reuse.

---

<a id="automation-templates"></a>
## Automation Templates

Devin provides 25+ pre-built automation templates covering common workflows. Templates include pre-configured triggers, conditions, and prompt templates — customize them for your repositories.

| Template | Trigger | What It Does |
|----------|---------|-------------|
| **CI Failure Fixer** | GitHub check run failure | Reads CI logs, identifies the failure, pushes a fix |
| **Nightly QA & Smoke Tests** | Schedule (daily) | Runs test suites, reports failures, opens issues |
| **Weekly Dependency Updates** | Schedule (weekly) | Bumps patch/minor versions where tests pass |
| **Daily Sentry Error Fixes** | Schedule (daily) or webhook | Investigates top Sentry errors, proposes fixes |
| **Datadog Alert Investigation** | Webhook | Reads alert context, investigates root cause |
| **Daily Health Digest** | Schedule (daily) | Summarizes repo health: coverage, lint, dep status |
| **Security Vulnerability Scan** | Schedule or GitHub event | Scans for vulnerabilities, remediates findings |
| **OWASP Security Hardening** | Schedule (weekly) | Audits against OWASP Top 10, proposes fixes |
| **Secret Scanner** | GitHub push | Detects committed secrets, opens remediation PR |
| **Code Pattern Enforcer** | GitHub PR opened | Checks PRs against coding standards, flags violations |
| **SonarQube Quality Gate Fix** | Webhook | Reads SonarQube findings, pushes fixes |
| **Figma Design Review** | Webhook | Compares implementation against Figma designs |
| **Jira Ticket to PR** | Linear/Jira assignment | Reads ticket, implements, opens PR |
| **Weekly Changelog** | Schedule (weekly) | Generates changelog from merged PRs |
| **Stale PR Cleanup** | Schedule (weekly) | Identifies and closes stale PRs |

Security Swarm Auto Scan is a specialized automation that uses Agentic MapReduce for deep security analysis — see [Recipe 5](#recipe-5) above.

---

<a id="exploration-activity"></a>
## Exploration Activity

**Try this:** If you have access to the Devin web app, navigate to **Automations** in the left sidebar of the organization page:

1. Browse the available trigger types — notice how they map to the patterns above
2. Look at any existing automations configured for your organization
3. Think about one routine task your team does manually today (weekly dep bumps, responding to CI failures, triaging alerts) — could it be an automation?

If you're not ready to create an automation yet, draft a prompt template on paper:
- What event triggers it?
- What conditions filter it?
- What should the agent do?
- What does success look like?

---

<a id="key-takeaways"></a>
## Key Takeaways

- Two automation types: event-driven (reactive to signals) and scheduled (proactive maintenance)
- Event-driven automations connect Devin to your existing toolchain via webhooks and integrations
- Scheduled sessions handle routine hygiene without consuming sprint capacity
- Prompt templates parameterize instructions with event payload data — the agent receives specific, actionable context
- Guardrails (loop prevention, concurrency limits, deduplication, escalation) are essential for production automation
- Start with one automation, one repo, conservative filters — expand as confidence grows
- Slack-based triage and deduplication prevent alert fatigue from spawning redundant sessions
