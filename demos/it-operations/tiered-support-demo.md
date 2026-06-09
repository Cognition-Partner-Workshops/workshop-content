# IT Operations — Tiered Support (L1 / L2 / L3) Demo

Devin augments each tier of an IT Operations support model differently —
from generating runbooks that L1 operators follow, to on-demand root-cause
investigation for L2, to fully autonomous detection-to-remediation for L3.
The pattern is platform-agnostic: any monitoring stack (Grafana, Datadog,
PagerDuty, Azure Monitor) that can fire a webhook or create a ticket can
trigger the pipeline.

<a id="toc"></a>
## Table of Contents

- [The Model](#the-model)
- [Part 1 — L1: Runbook Generation from Codebase](#part-1)
- [Part 2 — L2: On-Demand Investigation](#part-2)
- [Part 3 — L3: Fully Automated Remediation](#part-3)
- [Scaling with Automations](#scaling)
- [Key Takeaways](#key-takeaways)

---

<a id="the-model"></a>
## The Model

Traditional IT Operations uses tiered support:

```
L1 — Follow documented runbooks, triage, escalate
     (operators, help desk, NOC)
        ↓ escalation
L2 — Deeper investigation requiring engineering knowledge
     (application support, SREs)
        ↓ escalation
L3 — Root-cause analysis, code-level fixes, architecture changes
     (development teams, platform engineers)
```

Devin fits into each tier:

| Tier | Without Devin | With Devin |
|------|---------------|------------|
| **L1** | Operators follow runbooks — but runbooks are incomplete or stale | Devin generates and maintains runbooks from the actual codebase. Operators follow accurate, up-to-date procedures |
| **L2** | Engineers context-switch to investigate. MTTR depends on who's on call | One-click "Launch Devin" from the incident. Devin investigates immediately — reads logs, traces code, identifies root cause |
| **L3** | Engineers diagnose and fix. Time-to-fix depends on availability | Devin auto-detects the issue, investigates, and opens a fix PR — before a human is even paged |

---

<a id="part-1"></a>
## Part 1 — L1: Runbook Generation from Codebase

L1 operators need accurate runbooks. In most organizations, runbooks are
incomplete, outdated, or contain generic boilerplate. Devin generates
runbooks grounded in the actual service code — specific commands, file
paths, Redis keys, and Prometheus metrics.

### The problem

The OtterWorks platform has skeleton runbooks in `docs/runbooks/`. Each
has a title, severity, alert reference, and symptoms — but the
Investigation Steps, Resolution Steps, and Post-Incident sections are
`<!-- TODO -->` placeholders.

### Generate complete runbooks

Paste this into Devin:

```
Read the incomplete runbooks in docs/runbooks/ on the
Cognition-Partner-Workshops/otterworks repo. For each
runbook that has TODO placeholders:

1. Read the corresponding service source code to understand
   the failure mode described in the Symptoms section.
2. Fill in Investigation Steps with specific commands:
   kubectl log queries, Redis key checks, Prometheus
   metric queries, and Grafana dashboard references.
3. Fill in Resolution Steps with the actual fix: which
   file to change, what configuration to update, or what
   service to restart.
4. Fill in Post-Incident with realistic action items:
   monitoring gaps to close, code hardening, and process
   improvements.

Ground every step in the actual codebase — no generic
boilerplate. Reference specific files, functions, Redis
keys, and Prometheus metrics from the service code.

Start with docs/runbooks/search-suggest-500.md (read
services/search-service/app/api/search.py for context)
and docs/runbooks/notification-processing-failure.md
(read services/notification-service/ for context).
```

### What to watch for

- Devin reads the service code, not just the runbook skeleton
- Investigation steps reference actual Prometheus metrics from
  `observability/grafana/provisioning/alerting/alert-rules.yml`
- Resolution steps point to the exact code path that fails
- The completed runbooks are immediately usable by an L1 operator who
  has never seen the codebase

### Keeping runbooks current

Runbooks drift when code changes. Set up a scheduled Devin session (weekly
or after each release) that re-reads the codebase and updates runbooks if
the underlying service code has changed:

```
Review docs/runbooks/ in Cognition-Partner-Workshops/otterworks.
Compare each runbook's Investigation and Resolution steps
against the current service source code. If any referenced
files, functions, endpoints, or Redis keys have changed,
update the runbook to reflect the current state. Add a
"Last verified" timestamp at the bottom of each file.
```

---

<a id="part-2"></a>
## Part 2 — L2: On-Demand Investigation

When L1 cannot resolve an incident using the runbook, it escalates to
L2. Instead of paging an engineer, the operator launches a Devin session
from the incident card — Devin investigates immediately with full
codebase context.

### Trigger an incident

The OtterWorks admin dashboard has a Demo Controls panel with chaos
trigger buttons. These inject real failures into production-like services.

On the admin dashboard Incidents page:

1. Ensure the **Auto-Investigate** toggle is **OFF**
2. Click a chaos trigger button (e.g., "Break Search Autocomplete")
3. Within 30 seconds to 2 minutes, Grafana detects the error rate spike
   and fires an alert
4. An incident is auto-created on the Incidents page — but with **no
   Devin session attached**

This simulates an L1 operator seeing an alert fire and an incident
appear. The runbook (if completed in Part 1) tells them what to check.
If they cannot resolve it, they escalate.

### One-click escalation to Devin

On the incident card, click **"Launch Devin"**. This calls the Devin API
(`services/admin-service/app/services/devin_session_service.rb`) with
the incident context — title, severity, affected service, description —
and creates a session that immediately begins investigating.

Watch the session:

- Devin reads the affected service's source code
- Identifies the failure mode from logs and code paths
- Correlates the error with the chaos flag in Redis
- Opens a fix PR with the resolution

The operator does not need to write a prompt, provide context, or know
which service is failing. The incident metadata is enough for Devin to
start.

### The integration

The admin-service builds a Devin prompt automatically from incident
metadata:

```
services/admin-service/app/services/devin_session_service.rb
  → build_prompt(incident)
  → POST /v3/organizations/:org_id/sessions

services/admin-service/app/controllers/
  api/v1/admin/incidents_controller.rb
  → Manual "Launch Devin" button handler
```

Any ITSM tool (ServiceNow, PagerDuty, Jira Service Management) can
replicate this pattern: read the incident fields, build a prompt, call
the Devin API.

---

<a id="part-3"></a>
## Part 3 — L3: Fully Automated Remediation

L3 is zero-touch. The monitoring system detects an anomaly, creates an
incident, AND launches a Devin session — all automatically. No human
intervention from detection to fix PR.

### Enable Auto-Investigate

On the admin dashboard Incidents page, turn the **Auto-Investigate**
toggle **ON**. This sets a Redis-backed flag
(`services/admin-service/app/services/admin_settings_service.rb`) that
tells the alert ingestion controller to automatically create Devin
sessions for new incidents.

### Trigger and observe

1. Click a chaos trigger button (e.g., "Break File Uploads")
2. Grafana detects the metric anomaly and fires an alert (30s–2m)
3. The alert webhook hits the admin-service AlertsController:
   ```
   services/admin-service/app/controllers/
     api/v1/admin/alerts_controller.rb
   ```
4. The controller creates an incident AND calls
   `DevinSessionService.create_session`
5. The incident appears on the Incidents page with a Devin session
   already attached
6. Click **"View Session"** to watch Devin investigate and open a fix PR

The full event-driven pipeline:

```
Chaos/Production Failure
    → Prometheus metrics spike
    → Grafana alert rule fires (alert-rules.yml)
    → Webhook → contact-points.yml → admin-service
    → AlertsController creates Incident
    → Auto-Investigate ON? → DevinSessionService
    → Devin API session created
    → Devin investigates, identifies root cause
    → Fix PR opened — zero human intervention
```

### What done looks like

- The incident card shows a linked Devin session with status "completed"
- A PR exists on the repo with the fix for the chaos-injected bug
- Total time from failure injection to fix PR: typically under 10 minutes
- No human was paged, no context-switch occurred

---

<a id="scaling"></a>
## Scaling with Automations

The OtterWorks integration is custom (Rails + Devin API), but the same
pattern works out-of-the-box with
[Devin Automations](https://docs.devin.ai/product-guides/automations):

```
Create a Devin Automation that triggers when a GitHub
Issue is created on Cognition-Partner-Workshops/otterworks
with the label "incident":

1. Read the issue body for incident details (affected
   service, severity, symptoms).
2. Check the service's application logs and Grafana
   dashboards for the error pattern.
3. Identify the root cause in the service source code.
4. Implement a fix and run the affected service's tests.
5. Push to a branch and open a PR linking back to the
   incident issue.

Cap at 3 invocations per hour. Set an ACU limit of 50
per session.
```

This replaces the custom Rails integration with a no-code automation
that any team can set up in minutes. The trigger can be:

- A GitHub Issue with a specific label
- A PagerDuty webhook
- A ServiceNow ticket state change
- An Azure Monitor alert
- A Slack message in an incidents channel

### Multi-service incidents

For incidents affecting multiple services, use parent-child
orchestration:

```
You are triaging a multi-service incident on
Cognition-Partner-Workshops/otterworks.

Read the incident description and Grafana dashboards to
identify which services are affected. For each affected
service, launch a child session scoped to that service's
directory with instructions to:
- Read the service's logs and source code
- Identify the root cause within that service
- Implement and test a fix
- Report findings back

After all children complete, write an incident summary
in INCIDENT_REPORT.md covering: timeline, root cause per
service, fixes applied, and recommended follow-up actions.
```

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **Devin augments every support tier** — not a replacement for humans,
   but a force multiplier. L1 gets better runbooks. L2 gets instant
   investigation. L3 gets autonomous remediation.

2. **Runbooks from code, not memory** — Devin generates investigation
   and resolution steps by reading the actual service implementation.
   Runbooks stay accurate because Devin can re-verify them on a schedule.

3. **One-click escalation replaces paging** — when an L1 operator
   cannot resolve an issue, they click a button instead of waiting for
   an engineer to context-switch. Devin starts investigating in seconds.

4. **Event-driven L3 reduces MTTR to minutes** — from alert fire to fix
   PR with zero human intervention. The monitoring system, incident
   management, and Devin form a closed loop.

5. **Platform-agnostic pattern** — the integration is a webhook + API
   call. Any monitoring or ITSM tool that can fire webhooks works:
   Grafana, Datadog, PagerDuty, ServiceNow, Azure Monitor, OpsGenie.

6. **Team-based, not individual** — the automation, playbooks, and
   Knowledge notes are shared across the organization. One engineer sets
   up the pipeline; the entire operations team benefits on every incident.
