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

## Environment Blueprints (Declarative VM Configuration)

Devin's execution environment is configured through a **declarative, layered image builder** — not ad-hoc scripts or manual setup. Blueprints are YAML configurations that define exactly what is installed, configured, and available when a session starts.

**The layering model:**

1. **Base image** — The OS and core system packages (Linux or Windows)
2. **Organization blueprint** — Shared configuration that applies to every repo in the org: language runtimes, shared CLI tools, VPN setup, proxy configuration, corporate CA certificates, and org-wide policy. This layer runs first
3. **Repository blueprint** — Repo-specific configuration layered on top: project dependencies, build toolchain, database fixtures, test infrastructure, and startup scripts. Each repo can have its own blueprint that composes cleanly with the org layer
4. **Snapshot** — A pre-built VM image produced from all layers. Sessions boot from the cached snapshot, so the environment is ready to build in seconds — no waiting for dependency installation

**Blueprint management:**
- Blueprints are edited through the Devin UI or proposed programmatically by Devin itself (during repo setup, Devin can suggest blueprint changes for admin review)
- Changes go through an approval flow — an admin reviews and applies the update, then a new snapshot is built automatically
- Snapshots are versioned. If a blueprint change causes problems, roll back to the previous snapshot
- The `initialize` section runs shell commands during image build. The `post_snapshot` section runs commands at session start (for setup that depends on session-specific state like secrets)

**Why this matters for teams:**
- **Reproducibility** — Every session in the org gets the same base environment. No "works on my machine" drift
- **Speed** — Snapshot boot eliminates cold-start dependency installation. Sessions are productive in seconds
- **Governance** — Environment changes are intentional and auditable. Admins control what is installed
- **Windows and Linux** — The same layered model works for both OS types. Windows blueprints can install Visual Studio Build Tools, .NET SDKs, and Windows-specific toolchains; Linux blueprints handle the standard open-source stack

## Team-Based Operation

Devin is not an individual user's AI assistant — it is a team-based coworker agent that operates as a shared resource with organizational context. This distinction matters: configuration, knowledge, and integrations belong to the team, not to any single user's session.

**Shared configuration (the context layer):**
- **Environment blueprints** — Declarative, layered VM images with dependencies, runtimes, tools, and network configuration baked in. Sessions boot ready to build from cached snapshots (see [Environment Blueprints](#environment-blueprints-declarative-vm-configuration) above)
- **Knowledge notes** — Persistent, human-curated context (coding standards, architecture decisions, conventions) retrieved automatically based on the task
- **Playbooks** — Repeatable procedures encoding institutional methodology, shared across the org
- **MCP servers** — Pre-configured integrations (Jira, Datadog, Confluence, Azure DevOps) available to every session without per-session setup
- **Secrets** — Scoped credentials injected into the environment at session start via the platform's secrets management layer
- **Git connections** — Repository access configured at the org level. All sessions can clone and push to connected repos
- **Automations** — Webhooks, scheduled sessions, and trigger rules are organization-wide. Every team member's events route through the same automation layer

This shared context layer means that configuring Devin is a one-time investment: one engineer sets up the environment, knowledge, and integrations, and every subsequent session — from any team member — inherits that configuration automatically.

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
3. **Sleeping** — After a period of inactivity, Devin puts its VM to sleep. The session state is preserved but compute resources are released
4. **Resumed** — When new feedback arrives (PR comment, CI result, user message), Devin resumes from the saved state with full context. No work is lost

This lifecycle means Devin does not waste compute while waiting for human review. It acts immediately when feedback arrives and sleeps efficiently in between. Long-running tasks — multi-day code reviews, back-and-forth iterations, waiting on a blocked dependency — are natural. The session persists across the entire lifecycle of a task.
