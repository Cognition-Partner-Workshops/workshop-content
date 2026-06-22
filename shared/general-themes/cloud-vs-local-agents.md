# Cloud vs. Local Agents — Quick Reference

> Comprehensive training: [Foundations — Cloud Agent vs. Local Agent](../../workshops/foundations/concepts/01-cloud-agent-vs-local-agent.md) | [Engineering Track — Platform Fundamentals](../../workshops/training-tracks/engineering/01-platform-fundamentals.md)

## Comparison Table

| Dimension | Cloud Devin | Local Agents (Desktop / CLI) |
|-----------|-------------|------------------------------|
| **Where** | Isolated VM in Cognition's cloud | Your machine (laptop, workstation) |
| **Lifecycle** | Persistent — hibernates/resumes across days | Tied to your terminal or IDE session |
| **Autonomy** | Fully autonomous — works while you sleep | Interactive — you're in the loop |
| **Context** | Org-wide shared layer (Knowledge, Playbooks, Secrets, MCP, blueprints) | Local files, env vars, tools, running services |
| **Best for** | Long-running tasks, parallel campaigns, scheduled ops | Quick fixes, exploration, iterative coding |
| **Parallelism** | Unlimited — child agents across repos | Single session (your machine) |
| **Output** | PRs, deployed artifacts, reports | Direct file edits in your working tree |
| **Cost** | ACU-based (active time only) | ACU-based (active time only) |

## Decision Tree

```
Is the task quick and local?
  YES → Local agent (Devin Desktop / Devin CLI)
  NO ↓
Does it need your real-time judgment at every step?
  YES → Local agent
  NO ↓
Will it take more than a few minutes of active execution?
  YES → Cloud Devin
Should it run on a schedule or respond to events?
  YES → Cloud Devin
Do you need parallel execution across repos?
  YES → Cloud Devin (child agents)
```

## The Handoff

Local agents support `/handoff` (CLI) or **Delegate to Cloud** (Desktop) to transfer work to a cloud session mid-task. Context and git branch carry over.

| Scenario | Start With | Then |
|----------|-----------|------|
| Bug report | CLI: reproduce locally | `/handoff` to cloud: fix + tests + PR |
| Large migration | — | Cloud: parent + child agents in parallel |
| New feature design | Desktop: explore + prototype | Delegate to cloud: full implementation |
| Quick typo fix | CLI: fix + push | (No handoff needed) |
| Scheduled dep bump | — | Cloud on cron: weekly bump + PR |

## Key Rules
- Cloud sessions hibernate when idle — no wasted compute while waiting for review
- Local and cloud agents share the same agent harness — consistent behavior
- The merge decision is always human — Devin proposes via PR, you approve
