# Collaboration Model — Quick Reference

> Comprehensive training: [Foundations — Cloud Agent vs. Local Agent](../../workshops/foundations/concepts/01-cloud-agent-vs-local-agent.md) | [Foundations — Sessions](../../workshops/foundations/product/cloud/06-devin-sessions.md) | [Foundations — Automations](../../workshops/foundations/product/cloud/08-automations.md)

## The PR Feedback Loop

```
Devin opens PR → CI runs
  ├── Pass → Ready for human review
  └── Fail → Devin reads logs, pushes fix, CI re-runs
Human reviews diff
  ├── Approve → Merge (always a human decision)
  └── Request changes → Devin reads comments, iterates → CI re-runs
```

**Key properties:**
- Devin **never merges its own PRs** — the merge decision is always human
- CI is the automated quality gate — Devin iterates until CI passes or escalates
- PR comments are the communication channel — no special tools needed
- The PR provides a complete audit trail of what Devin proposed and how it evolved

<a id="multi-user-communication"></a>

## Multi-User Communication

Multiple team members comment on the same Devin PR. Devin reads the comments and addresses requests in subsequent commits. When any comment arrives, Devin resumes from its hibernated state with full context — no re-reading from scratch.

<a id="ci-check-monitoring"></a>

## CI Check Monitoring

1. **Watches** for CI completion after pushing code
2. **Reads** failure logs and diagnoses the issue
3. **Pushes a fix** for code-level failures
4. **Re-checks** CI results — iterates up to multiple rounds
5. **Escalates** to the user if stuck after repeated attempts

## Working with Existing Tools

| Category | Examples | How Devin Uses It |
|----------|----------|-------------------|
| Issue Trackers | Jira, Linear, Azure DevOps | Read tickets, update status, link PRs |
| Observability | Datadog, New Relic, Azure Monitor | Query logs, traces, metrics |
| Documentation | Confluence, Notion | Read for context, update as part of implementation |
| Communication | Slack, Microsoft Teams | Receive triggers, post status updates |
| Cloud Platforms | AWS, Azure, GCP | Deploy resources, query state |
| Databases | PostgreSQL, MySQL, MongoDB | Query schemas, run migrations |

Integrations are configured once at the org level as part of the [shared context layer](architecture-strengths.md#shared-context-layer).

## Key Rules
- The PR is Devin's primary interface with human engineers — familiar, auditable, fits existing workflows
- Sessions hibernate while waiting for review — no wasted compute
- Context is preserved across the full task lifecycle with no loss between interactions
- Configuring the shared context layer once benefits every future session from any team member
