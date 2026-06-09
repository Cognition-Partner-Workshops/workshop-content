# Devin Cloud — Feature Tour

Devin Cloud is the autonomous AI software engineer that runs on its own virtual machine. You give it a task, it works independently — writing code, running builds, executing tests, browsing the web — and delivers a pull request when done.

<a id="toc"></a>
## Table of Contents

- [Core Execution Model](#core-execution-model)
- [Starting Sessions](#starting-sessions)
- [The Session Interface](#the-session-interface)
- [Context and Configuration](#context-and-configuration)
- [Collaboration and Feedback](#collaboration-and-feedback)
- [Orchestration and Scale](#orchestration-and-scale)
- [Integrations](#integrations)
- [Companion Tools](#companion-tools)
- [Admin and Governance](#admin-and-governance)

---

<a id="core-execution-model"></a>
## Core Execution Model

| Aspect | Detail |
|--------|--------|
| Runtime | Isolated Linux VM per session — full shell, browser, GUI |
| Autonomy | Works independently after prompt; no babysitting required |
| Output | Pull requests, reports, deployed artifacts, research findings |
| Lifecycle | Active → Waiting for feedback → Hibernated → Resumed on new input |
| Verification | Runs tests, linters, type checkers locally before opening PRs |

Each session has:
- A full Linux filesystem with shell access
- A Chrome browser with computer-use capabilities (click, type, navigate)
- Network access to reach APIs, databases, and private systems (VPN/tunnel supported)
- Persistent state across hibernation — no work is lost between interactions

---

<a id="starting-sessions"></a>
## Starting Sessions

| Method | How |
|--------|-----|
| Web app | Paste a prompt at app.devin.ai |
| Slack / Teams | Tag Devin in a thread or DM |
| IDE (Desktop) | Delegate from Cascade to Devin Cloud |
| CLI | `devin` with cloud handoff |
| API | `POST /sessions` with prompt and repo |
| Automation | Trigger on GitHub events, webhooks, or schedules |
| Linear / Jira | Assign a ticket to Devin |

---

<a id="the-session-interface"></a>
## The Session Interface

### Session Timeline
- Real-time view of Devin's actions: file edits, shell commands, browser interactions
- Expandable detail for each step — see exactly what Devin did and why
- Session Insights for post-hoc analysis (ACU usage, timeline, activity breakdown)

### Desktop Takeover
- Connect to Devin's live VM desktop from the web app
- Watch it work, interact with the GUI, or take manual control
- Useful for debugging browser-based issues or verifying UI changes

### Screen Recordings
- Devin can record its own screen while testing
- Videos attached to sessions for review — useful for visual QA and frontend verification

### Slash Commands
- In-session commands to steer Devin's behavior (e.g., pause, switch approach, add context)

---

<a id="context-and-configuration"></a>
## Context and Configuration

### Knowledge
- Persistent notes (coding standards, architecture decisions, team conventions)
- Auto-retrieved based on task relevance — no need to repeat context each session
- Can be created manually or suggested by Devin after sessions
- Scoped to org, repo, or user level

### Playbooks
- Reusable step-by-step procedures encoding proven methodology
- Invoked by name; Devin follows each step in order
- Versionable, shareable across the org
- Composable — playbooks can reference other playbooks

### Secrets
- Scoped credentials (API keys, tokens, passwords) injected at session start
- Org-level, user-level, or repo-level scoping
- Never exposed in logs or code — platform-managed lifecycle
- TOTP/2FA secrets supported (auto-generates current codes)

### Environment Blueprints
- YAML configuration defining the VM setup (dependencies, tools, runtimes)
- `initialize` section: runs once when snapshot is built
- `maintenance` section: runs at session start to keep deps current
- `knowledge` section: commands for lint, test, startup surfaced as context
- Snapshots cache the built environment — sessions boot in seconds

### Skills and AGENTS.md
- `AGENTS.md` files in repos provide always-on instructions for that codebase
- Skills are reusable procedures Devin can invoke (`.devin/skills/` or `.agents/skills/`)

### DeepWiki
- Auto-generated architectural documentation for indexed repositories
- Devin reads DeepWiki to understand unfamiliar codebases before acting
- Includes diagrams, module summaries, dependency graphs
- Updated automatically as code evolves

---

<a id="collaboration-and-feedback"></a>
## Collaboration and Feedback

### PR Feedback Loop
1. Devin opens a PR with its changes
2. Any reviewer comments on the PR (GitHub, GitLab, Bitbucket)
3. Devin wakes from hibernation, reads the feedback, iterates
4. Push → CI runs → Devin monitors results → fixes failures automatically

### Ask Devin
- Conversational Q&A about your codebase without starting a full session
- Three modes:
  - **Fast** — Quick answers from indexed context
  - **Deep** — Thorough research with code exploration
  - **CodeMap** — Structural analysis and dependency mapping
- Can generate a session prompt from the conversation when you are ready to act

### Devin Review
- Proactive code review on new PRs (org- or repo-configurable)
- Looks for bugs, security issues, logic errors, and race conditions in new diffs
- Summarizes large PRs into readable overviews
- Can auto-open fix PRs for critical findings
- Findings appear as standard PR comments — team responds naturally

### Multi-User Collaboration
- Multiple team members can interact with the same session
- Devin responds to feedback from any reviewer, not just the creator
- Sessions are org-visible — anyone on the team can observe progress

---

<a id="orchestration-and-scale"></a>
## Orchestration and Scale

### Automations
- Event-driven session triggers: GitHub events, webhooks, PR comments, issue creation
- Configurable conditions and prompt templates
- Enables patterns like: "On every new vulnerability finding, start a remediation session"

### Scheduled Sessions
- Cron-based recurring sessions (daily, weekly, custom)
- Use cases: dependency bumps, license audits, dead code detection, test coverage reports
- Configured in the UI or via API

### Child Sessions (Divide and Conquer)
- A parent session breaks large tasks into units and spawns child sessions
- Each child runs on its own VM with its own branch and PR
- Parent monitors progress, handles failures, escalates blockers
- Pattern: analyze scope → create playbook → fan out N children → aggregate results

### API
- Full REST API for programmatic session management
- Create sessions, poll status, send messages, list artifacts
- Service user keys with role-based access control
- Powers custom integrations, dashboards, and orchestration layers

---

<a id="integrations"></a>
## Integrations

### Git Providers
- GitHub, GitLab, Bitbucket (cloud and self-hosted)
- Org-level connections — all sessions inherit access
- PR creation, branch management, CI monitoring built-in

### Messaging
- **Slack** — Start sessions from threads, get notifications, interact conversationally
- **Microsoft Teams** — Same pattern as Slack

### Issue Tracking
- **Linear** — Assign tickets to Devin; it reads the ticket, implements, opens a PR
- **Jira** — Same workflow via MCP integration

### MCP Marketplace
- Pre-built integrations via Model Context Protocol
- Devin calls external tools as if local: query Jira, read Datadog logs, search Confluence
- Install from the marketplace with one click — available to all sessions in the org

### Data Analyst Agent (DANA)
- Specialized persona for data analysis tasks
- Connects to databases, runs queries, produces visualizations
- Outputs reports with charts and findings

---

<a id="companion-tools"></a>
## Companion Tools

### Devin MCP Server
- Expose Devin's capabilities (Ask Devin, DeepWiki, session management) to other AI agents
- Other tools can query your codebase knowledge through Devin's MCP interface

### Computer Use
- Devin can interact with any GUI application on its VM
- Click, type, scroll, take screenshots — useful for testing web UIs, filling forms
- Browser automation for login flows, data entry, visual verification

---

<a id="admin-and-governance"></a>
## Admin and Governance

| Capability | Description |
|-----------|-------------|
| Organizations | Logical boundary for teams — separate knowledge, secrets, access |
| Enterprises | Multi-org management with centralized billing and policy |
| Roles and RBAC | Scoped permissions for users and service accounts |
| Service Users | API-only identities for automation (not tied to a human) |
| Audit Trail | Every session action logged — navigable from PRs back to sessions |
| Deployment Models | Cloud-hosted, dedicated VPC (PrivateLink), or hybrid |
| Static Egress IPs | Fixed IPs for firewall allowlisting |
| SSO | SAML/OIDC integration for enterprise authentication |

---

## When to Use Devin Cloud

Devin Cloud is the right choice when:
- The task can run autonomously without real-time human guidance
- You want to fire off work and context-switch to something else
- The task benefits from a clean-room environment (no "works on my machine" issues)
- You need to scale: child sessions, scheduled runs, event-driven automation
- The task requires long-running execution (multi-hour builds, large migrations)
- Multiple team members need visibility into the work
