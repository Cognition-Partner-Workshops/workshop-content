# Collaboration Model

Devin integrates into existing engineering workflows through pull requests, CI/CD pipelines, and team communication channels. It participates in the processes your team already uses — no new tools or workflows required.

## The PR Feedback Loop

The pull request is Devin's primary interface with human engineers. Every Devin implementation results in a PR that follows your team's existing review process.

```
Devin opens PR
    ↓
CI checks run automatically
    ├── Pass → PR is ready for human review
    └── Fail → Devin reads logs, pushes fix, CI re-runs
          ↓
Human reviewers inspect the diff
    ├── Approve → Merge (human decision)
    └── Request changes → Devin reads comments, iterates
          ↓
Devin pushes new commits addressing feedback
    ↓
CI re-runs → Review cycle repeats until approved
```

**Key properties:**
- Devin **never merges its own PRs**. The merge decision is always human
- CI serves as an automated quality gate. Devin iterates until CI passes or escalates if it cannot resolve the failure
- PR comments are the communication channel. No special tools or interfaces required
- The PR provides a complete audit trail of what Devin proposed and how it evolved through review

## Multi-User Communication

Multiple team members can interact with the same Devin session through PR comments:

- **Reviewer A** comments: "This function should handle the null case"
- **Reviewer B** comments: "Please add a test for the edge case in line 42"
- Devin reads both comments, addresses both requests in subsequent commits
- Both reviewers see Devin's response and can continue the conversation

This works because Devin monitors its PRs for new comments. When any comment arrives, Devin resumes from its saved state and responds with its full conversation context retained — no re-reading from scratch. The session persists across the entire lifecycle of the task with no context loss between interactions.

## CI Check Monitoring

Devin actively monitors CI pipeline results after pushing code:

1. **Watches for completion** — Devin polls CI status checks until all jobs complete
2. **Reads failure logs** — When a check fails, Devin downloads and analyzes the CI job logs
3. **Diagnoses the issue** — Devin identifies whether the failure is in its code, in the test environment, or in infrastructure
4. **Pushes a fix** — For code-level failures, Devin commits a fix and waits for CI to re-run
5. **Escalates if stuck** — After multiple failed attempts, Devin asks the user for help rather than looping indefinitely

This creates a tight feedback loop where Devin iterates toward a green build without human intervention for straightforward failures.

## Sleep, Resume, and VM Persistence

Devin sessions manage compute resources efficiently through full VM state serialization — not stateless restarts:

- **Active work** — Devin is running on its VM, executing tasks, building code, running tests
- **Waiting for feedback** — After opening a PR or asking a question, Devin enters a monitoring state
- **Sleep** — After inactivity, Devin serializes the **entire VM state** — running processes, filesystem, environment variables, installed packages, build caches, open files, shell history — and releases compute resources. The session appears idle but every byte of state is preserved
- **Resume** — When new input arrives (PR comment, CI result, user message), Devin restores its VM from the serialized state and continues exactly where it left off. No re-cloning repos, no re-installing dependencies, no re-building artifacts. The session resumes mid-thought

**Why full VM persistence matters (vs. stateless restart):**
- **Build cache preservation** — Compiled artifacts, downloaded dependencies, and intermediate build outputs survive across sleep cycles. A resumed session does not pay the cold-start cost of a fresh environment
- **Multi-day workflows are natural** — A migration task that requires three rounds of human review over a week keeps its accumulated state through every cycle. The VM wakes, processes feedback, pushes changes, and goes back to sleep — without ever starting from scratch
- **Complex environment state persists** — Running databases, Docker containers, started dev servers, configured VPN connections, and in-progress test suites survive sleep. When Devin resumes, the environment is exactly as it was left
- **No context reconstruction** — Stateless agents must re-read the repo, re-parse the conversation, and re-derive their plan on every invocation. Devin's VM persistence means the agent resumes with its full working memory intact — the code is already open, the plan is already in progress, the test is already halfway through

**What this means for users:**
- You do not need to respond immediately. Devin waits patiently and resumes when you are ready
- Sessions do not consume resources while waiting. Cost is proportional to active work time
- Long-running tasks (multi-day code reviews, back-and-forth iterations) are natural. The session persists across the entire lifecycle
- A different team member can pick up the conversation. The VM state does not depend on who responds — any reviewer's comment triggers the same seamless resume

## Working with Existing Tools

Devin connects to your team's existing toolchain through MCP servers and API integrations:

| Tool Category | Examples | How Devin Uses It |
|---------------|----------|-------------------|
| **Issue Trackers** | Jira, Linear, Azure DevOps | Read ticket details, update status, link PRs |
| **Observability** | Datadog, New Relic, Azure Monitor | Query logs, traces, and metrics during incident investigation |
| **Documentation** | Confluence, Notion | Read existing docs for context, update docs as part of implementation |
| **Communication** | Slack, Microsoft Teams | Receive trigger messages, post status updates |
| **Cloud Platforms** | AWS, Azure, GCP | Deploy resources, query infrastructure state, manage environments |
| **Databases** | PostgreSQL, MySQL, MongoDB | Query schemas, run migrations, validate data integrity |

These integrations are configured once at the organization level as part of Devin's shared context layer (see [Architecture Strengths → Shared Context Layer](architecture-strengths.md#shared-context-layer)) and are available to every session automatically.

## Continuous Improvement Cycle

Devin's effectiveness compounds over time as the team invests in the shared context layer:

```
Initial Setup
    ├── Connect repos (Git connections)
    ├── Configure environment (VM blueprints with runtimes, tools)
    ├── Add knowledge notes (standards, conventions, domain glossary)
    ├── Provision secrets (API keys, service account credentials)
    └── Set up MCP servers (Jira, Datadog, Confluence, etc.)
        ↓
First Sessions
    ├── Devin learns the codebase through context retrieval
    ├── Team refines prompts based on results
    └── Playbooks are created for recurring tasks
        ↓
Mature Operation
    ├── Scheduled sessions handle routine O&M automatically
    ├── Event-driven triggers respond to incidents and findings in real time
    ├── Playbooks encode institutional knowledge for consistent execution
    └── Child agents parallelize large-scale campaigns
        ↓
Compounding Value
    ├── Less manual O&M overhead → engineers focus on architecture and product
    ├── Faster incident response → reduced MTTR
    ├── Consistent quality enforcement → fewer regressions
    └── Scalable capacity → handle work surges without hiring
```
