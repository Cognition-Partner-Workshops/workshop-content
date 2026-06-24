# Platform Capabilities — Quick Reference

> Comprehensive training: [Foundations — Sessions](../../workshops/foundations/product/cloud/06-devin-sessions.md) | [Foundations — Automations](../../workshops/foundations/product/cloud/08-automations.md) | [Foundations — Multi-Agent Workers](../../workshops/foundations/product/cloud/09-multi-agent-workers.md)

## Feature Summary

| Capability | What It Does | Key Detail |
|-----------|-------------|------------|
| **Scheduled Sessions** | Recurring tasks on cron (daily, weekly, custom) | Dep bumps, license audits, dead code detection, doc refresh, security scanning, coverage monitoring |
| **Playbooks** | Reusable step-by-step procedures encoding proven methodology | Repeatable, versionable, shareable, composable across teams |
| **Child Agents** | Parent breaks large task into N independent units, spawns one child per target | Each child: own VM, own branch, own PR. Failures isolated |
| **Team-Based Operation** | Org-wide shared context layer configured once, inherited by every session | Blueprints, Knowledge, Playbooks, MCP servers, Secrets, Git connections, Automations |
| **Devin Review** | Proactive code review on new PRs — bugs, security, quality | Findings as PR comments; can auto-open fix PRs; configurable rules; can block merge via status checks |

<a id="scheduled-sessions"></a>

### Scheduled Sessions

See table above. Comprehensive training: [Foundations — Automations](../../workshops/foundations/product/cloud/08-automations.md).

<a id="playbooks"></a>

### Playbooks

See table above. Comprehensive training: [Foundations — Automations](../../workshops/foundations/product/cloud/08-automations.md).

<a id="child-agents-divide-and-conquer"></a>

### Child Agents

See table above. Comprehensive training: [Foundations — Multi-Agent Workers](../../workshops/foundations/product/cloud/09-multi-agent-workers.md).

<a id="devin-review"></a>

### Devin Review

See table above. Comprehensive training: [Foundations — Sessions § Devin Review](../../workshops/foundations/product/cloud/06-devin-sessions.md#devin-review).

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
- Child agents scale linearly — one becomes N, each producing independent PRs
- The shared context layer is a one-time investment that compounds across every session
