# The Cognition Platform

Cognition offers a unified platform for AI-assisted engineering that spans three surfaces: **Devin Desktop** (IDE + agent manager), **Devin CLI** (terminal agent), and **Devin Cloud** (autonomous VM-based agent). These surfaces are designed to work together — not as isolated tools — forming a continuous workflow from local exploration to cloud-scale execution.

## Platform Surfaces

### Devin Cloud (Autonomous Agent)

The cloud agent that runs on its own VM with a full desktop, browser, shell, and computer-use capabilities. Each session is isolated, sandboxed, and operates independently — it keeps working after you close your laptop.

**Core capabilities:**
- **Autonomous execution** — Devin handles complex tasks end to end: debugging, deployment, testing, migrations, feature implementation
- **PR-based collaboration** — Every implementation produces a pull request. Humans review, comment, and approve. Devin iterates on feedback
- **Child agents for scale** — A parent session can spawn child sessions to parallelize work across many repos, modules, or targets
- **Scheduled sessions** — Recurring tasks (dependency updates, security scans, dead code cleanup) run on cron without human initiation
- **Shared context layer** — Knowledge notes, playbooks, MCP integrations, secrets, and environment blueprints are configured once and inherited by every session
- **Hibernation and resume** — Sessions sleep when idle and resume instantly on new input (PR comments, CI results, messages). No wasted compute

### Devin Desktop (IDE + Agent Manager)

Devin Desktop is a next-generation AI IDE that doubles as a command center for managing both local and cloud agents. It combines code editing with agent orchestration.

**Core capabilities:**
- **Agent Command Center** — A Kanban-style board showing all your agents (local and cloud) organized by status. See at a glance what is in flight, what is blocked, and what is ready for review
- **Spaces** — Group all sessions, PRs, files, and context for a specific task into a single view. New sessions in a Space inherit the project context automatically
- **One-click checkout** — When Devin Cloud opens a PR, review it directly in the editor without leaving Desktop. No manual `git fetch` or branch switching
- **Local agents** — Run Cascade or Devin Local directly in the editor for fast, interactive, local-first coding assistance
- **Delegate to Cloud** — Plan locally with a Cascade session, then send the task to Devin Cloud with one click for autonomous implementation. Continue coding locally while Cloud works in the background
- **ACP extensibility** — Any agent that speaks the Agent Client Protocol can plug into Desktop (Codex CLI, Claude Agent, Gemini CLI, Junie, OpenCode, or custom agents)

### Devin CLI (Terminal Agent)

A local command-line coding agent with deep Devin Cloud integration. Installed via `cli.devin.ai`, it runs directly in your terminal with access to your local files, tools, and environment.

**Core capabilities:**
- **Local-first** — Works with your local files, git repos, and dev environment. Fast, interactive, and runs where you already work
- **Subagents** — Spawns independent subagents to handle subtasks in the foreground or background
- **Shell integration** — Understands your shell context, recent commands, and terminal state
- **ACP-consumable** — Devin CLI speaks the Agent Client Protocol, so it can be embedded inside Devin Desktop as the "Devin Local" agent or consumed by any ACP-compatible editor
- **Cloud handoff** — Start a task locally, then hand it off to Devin Cloud when it needs autonomous long-running execution. The cloud agent picks up where the CLI left off

## The Platform Continuum

The three surfaces form a continuum from local exploration to cloud-scale execution:

```
┌─────────────────────────────────────────────────────────────────┐
│                   THE COGNITION PLATFORM                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  LOCAL                                          CLOUD            │
│  ─────                                          ─────            │
│                                                                  │
│  Devin CLI          Devin Desktop          Devin Cloud           │
│  (terminal)         (IDE + manager)        (autonomous VM)       │
│                                                                  │
│  ● Explore code     ● Edit code            ● Long-running tasks  │
│  ● Quick fixes      ● Manage agents        ● Parallel execution  │
│  ● Prototype        ● Review cloud PRs     ● Scheduled work      │
│  ● Hand off →       ● Delegate to cloud →  ● Child agents        │
│                     ● One-click checkout ←  ● PR collaboration    │
│                                                                  │
│  ◄──── ACP (Agent Client Protocol) ────►                        │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Workflow Examples

**Developer flow (Desktop + Cloud):**
1. Developer opens a task in Devin Desktop, creates a Space for it
2. Uses Cascade locally to prototype a solution or refine requirements
3. Delegates the implementation to Devin Cloud with one click
4. Cloud agent works autonomously — opens a PR when done
5. Developer reviews the PR in Desktop, leaves comments
6. Devin Cloud iterates based on feedback
7. Developer merges when satisfied

**CLI flow (terminal + Cloud handoff):**
1. Developer runs `devin` in their terminal, asks it to analyze a module
2. CLI explores the local code, suggests an approach
3. Developer approves: "hand this off to cloud"
4. Devin Cloud spins up a VM, picks up the task, works autonomously
5. Cloud agent opens a PR; developer reviews in their preferred tool

**Team-scale flow (Cloud + child agents):**
1. Engineering lead creates a Devin Cloud session with a migration plan
2. Parent agent analyzes scope (e.g., 50 services need upgrading)
3. Parent spawns 50 child agents — one per service — each following the same playbook
4. Children work in parallel, each opening a PR
5. Team reviews PRs in Desktop's Agent Command Center (Kanban view of all agents)
6. Merge decisions stay with humans

## Agent Client Protocol (ACP)

ACP is an open protocol that standardizes communication between code editors and coding agents — similar to how LSP standardized language server integration.

**What this means:**
- Devin CLI is consumable as an ACP agent inside any ACP-compatible editor
- Devin Desktop can run any ACP-compatible agent (first-party or third-party)
- Teams can mix and match agents: use Devin Local for most work, bring in specialized agents for specific tasks
- Custom enterprise agents can be built and registered in the ACP registry

**Supported agents in Devin Desktop:**
- Devin Local (Cognition — shared harness with CLI)
- Cascade (Cognition — legacy local agent)
- Codex CLI (OpenAI)
- Claude Agent (Anthropic)
- Gemini CLI (Google)
- Junie (JetBrains)
- OpenCode (open source)
- Custom agents via registry configuration

## Why the Platform Matters

### For Individual Developers
- **Seamless transitions** — Start local, go cloud when needed. No context loss, no re-explaining
- **One view for all agents** — Agent Command Center shows everything: local sessions, cloud sessions, PRs, all in one Kanban board
- **Work continues when you step away** — Cloud agents keep working after you close your laptop. Come back and review the PR

### For Engineering Teams
- **Shared configuration scales** — Knowledge, playbooks, secrets, and MCP integrations configured once benefit every team member's sessions
- **Parallel capacity on demand** — Spin up 50 cloud agents for a migration campaign without pulling engineers off feature work
- **Consistent execution** — Playbooks encode team methodology. Every agent (local or cloud) follows the same process
- **Review-centric workflow** — Engineers spend time reviewing PRs (minutes) instead of doing repetitive implementation (hours)

### For Enterprise Organizations
- **Governance and control** — Admin portals, ACU budgets, sandbox enforcement, domain filtering
- **Multi-surface deployment** — Desktop for developers, Cloud for automation, CLI for terminal-native teams
- **Open ecosystem** — ACP means no vendor lock-in on the local agent. Bring any compatible agent into Desktop
- **Audit trail** — Every cloud session produces PRs with full change history. Nothing happens outside version control
