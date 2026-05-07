# Platform Capabilities

Devin is more than a single-session code generator. It is a team-based engineering platform with scheduling, orchestration, review, and collaboration features that compound in value over time.

## Scheduled Sessions

Devin sessions can run on a recurring schedule — daily, weekly, or custom cron expressions — to perform proactive operations and maintenance without human initiation.

**Use cases:**
- **Dependency version bumps** — Weekly scan for outdated packages, open PRs with bumped versions and passing tests
- **License compliance audits** — Monthly check that all dependencies meet organizational license policies
- **Dead code detection** — Periodic analysis to identify unused imports, unreachable functions, and deprecated API usage
- **Documentation freshness** — After code merges, update READMEs, API docs, and changelogs automatically
- **Security scanning** — Scheduled SAST/SCA runs that remediate findings before they accumulate
- **Test coverage monitoring** — Weekly reports on coverage trends, with auto-generated tests for newly uncovered code

**Configuration:** Scheduled sessions are configured in the Devin UI or via the API. Each schedule specifies the prompt, target repository, and recurrence pattern.

## Playbooks

Playbooks are reusable, step-by-step procedures that encode a proven methodology. When Devin follows a playbook, every session applies the same process — regardless of which team member triggers it.

**Characteristics:**
- **Repeatable** — The same playbook produces consistent results across different repos and teams
- **Versionable** — Playbooks evolve over time as the team refines the methodology
- **Shareable** — One engineer writes the playbook; the entire team benefits
- **Composable** — Playbooks can reference other playbooks for multi-phase workflows

**Examples:**
- Framework upgrade playbook: check compatibility → update dependencies → fix breaking changes → run tests → update docs
- Incident response playbook: query logs → identify root cause → implement fix → verify → update runbook
- Microservice extraction playbook: identify bounded context → extract interfaces → create new service → migrate data → set up CI/CD

## Child Agents (Divide and Conquer)

Devin can create child sessions to parallelize work. A parent agent breaks a large task into independent units and spawns a child agent for each one.

**How it works:**
1. Parent agent analyzes the scope (e.g., "50 microservices need Spring Boot 3 upgrade")
2. Parent creates a playbook encoding the upgrade procedure
3. Parent spawns child agents — one per microservice — each following the same playbook
4. Children work in parallel on their own VMs, each opening a PR
5. Parent monitors progress and handles failures or escalations

**Scale:** This pattern enables work that would otherwise require a dedicated team. Each child agent is independent — its own VM, its own branch, its own PR. Failures in one child do not affect others.

## Team-Based Operation

Devin is not a per-user tool. It operates as a shared team resource with organizational context.

**Shared configuration:**
- **Context layer** — Knowledge notes, environment setup, and MCP connections apply to all sessions in the organization. Configure once, benefit everywhere
- **Automations** — Webhooks, scheduled sessions, and trigger rules are organization-wide. Every team member's events route through the same automation layer
- **Git connections** — Repository access is configured at the org level. All sessions can clone and push to connected repos

**Multi-user collaboration:**
- Multiple team members can comment on the same Devin-created PR
- Devin reads and responds to feedback from any reviewer — not just the session creator
- Teams can share playbooks, knowledge notes, and session templates

## Devin Review

Devin Review is a proactive code review capability that analyzes new pull requests for bugs, security issues, and quality problems.

**Capabilities:**
- **Bug detection** — Finds logic errors, null pointer risks, race conditions, and edge cases in new diffs
- **PR digestion** — Summarizes large PRs into readable overviews, highlighting the most important changes and potential risks
- **Proactive remediation** — When Devin Review finds a bug, it can automatically open a fix PR rather than just commenting
- **Configurable rules** — Teams can customize what Devin Review checks for, adjusting sensitivity and focus areas

**Workflow integration:**
- Devin Review runs automatically on new PRs (configurable per repo or org)
- Findings appear as PR comments, indistinguishable from human review feedback
- Developers respond to Devin Review comments just as they would to any reviewer
- Critical findings can block merge via required status checks

## Session Lifecycle

Devin sessions have a deliberate lifecycle optimized for efficiency:

1. **Active** — Devin is executing tasks, running builds, writing code
2. **Waiting for feedback** — Devin has opened a PR or asked a question. It monitors for PR comments and CI check results
3. **Hibernated** — After a period of inactivity, Devin hibernates its VM. The session state is preserved but compute resources are released
4. **Resumed** — When new feedback arrives (PR comment, CI result, user message), Devin resumes from the hibernated state with full context. No work is lost

This lifecycle means Devin does not waste compute while waiting for human review. It acts immediately when feedback arrives and sleeps efficiently in between.
