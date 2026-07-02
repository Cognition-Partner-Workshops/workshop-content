# Platform Capabilities — Quick Reference

> Comprehensive training: [Foundations — Sessions](../../courses/foundations/product/cloud/06-devin-sessions.md) | [Foundations — Automations](../../courses/foundations/product/cloud/08-automations.md) | [Foundations — Multi-Agent Workers](../../courses/foundations/product/cloud/09-multi-agent-workers.md)

## Feature Summary

| Capability | What It Does | Key Detail |
|-----------|-------------|------------|
| **Automations** | Event-driven + scheduled triggers (Slack, GitHub, Linear, webhooks, cron) | 25+ pre-built templates. Replaces standalone Scheduled Sessions. Invocation and ACU limits for safeguards |
| **Playbooks** | Reusable step-by-step procedures encoding proven methodology | Repeatable, versionable, shareable, composable across teams |
| **Managed Devins** | Coordinator breaks large task into N independent units, spawns one managed Devin per target | Each worker: own VM, own branch, own PR. Coordinator can message, monitor ACU, sleep/terminate. Failures isolated |
| **Security Swarm** | Agentic MapReduce security scanning — builds threat model, investigates vulnerabilities, fixes via PRs | Auto Scan for recurring scans. Profiles for repeatable scan configurations |
| **Team-Based Operation** | Org-wide shared context layer configured once, inherited by every session | Blueprints, Knowledge, Playbooks, MCP servers, Secrets, Git connections, Automations |
| **Devin Review** | Proactive code review on new PRs — bugs, security, quality | Findings as PR comments; can auto-open fix PRs; configurable rules; can block merge via status checks |

<a id="automations"></a>

### Automations

See table above. Automations are the unified product combining event-driven and scheduled triggers — Slack, GitHub (check run/CI, PR, issue, push), Linear, webhooks, and cron schedules. 25+ pre-built templates cover common workflows like CI Failure Fixer, Weekly Dependency Updates, and Security Vulnerability Scan. Comprehensive training: [Foundations — Automations](../../courses/foundations/product/cloud/08-automations.md).

<a id="playbooks"></a>

### Playbooks

See table above. Comprehensive training: [Foundations — Automations](../../courses/foundations/product/cloud/08-automations.md).

<a id="managed-devins"></a>

### Managed Devins

See table above. The coordinator can spawn, message, monitor ACU usage, sleep/terminate managed Devins, and schedule messages to itself. Comprehensive training: [Foundations — Multi-Agent Workers](../../courses/foundations/product/cloud/09-multi-agent-workers.md).

<a id="security-swarm"></a>

### Security Swarm

Security Swarm is Devin's security scanning and remediation product using Agentic MapReduce. It divides the repository among parallel Devins for broad coverage and deep investigation. The workflow builds a threat model, investigates vulnerabilities (RCE, SQLi, SSRF, auth bypasses, memory-safety, DoS, chained exploits), and delivers fixes via PRs. Interactive mode allows threat model review before scanning begins. Auto Scan provides recurring scans on a schedule, and Profiles enable repeatable scan configurations. See [Security Swarm docs](https://docs.devin.ai/work-with-devin/security-swarm).

<a id="devin-review"></a>

### Devin Review

See table above. Comprehensive training: [Foundations — Sessions § Devin Review](../../courses/foundations/product/cloud/06-devin-sessions.md#devin-review).

## Session Lifecycle

```
Active (executing, costs ACUs)
  → Waiting for feedback (PR opened or question asked)
    → Hibernated (VM snapshot stored, compute released, no cost)
      → Resumed (feedback arrives, full context restored instantly)
```

## Shared Context Layer (Configure Once)

| Component | Purpose |
|-----------|---------|
| Environment blueprints | Pre-built VM images — sessions boot ready to build |
| Knowledge notes | Coding standards, architecture decisions, conventions — retrieved automatically |
| Playbooks | Institutional methodology — consistent execution across sessions |
| MCP servers | Pre-configured integrations (Jira, Datadog, Confluence, etc.) |
| Secrets | Scoped credentials injected at session start |
| Git connections | Org-level repo access for all sessions |

## Key Rules
- Devin never merges its own PRs — the merge decision is always human
- Hibernation means cost tracks active work, not wall-clock time
- Managed Devins scale linearly — one becomes N, each producing independent PRs
- The shared context layer is a one-time investment that compounds across every session
