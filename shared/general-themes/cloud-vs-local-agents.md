# Cloud vs. Local Agents

Devin is a unified AI engineering platform with two complementary execution tiers: a **cloud agent** that runs autonomously on its own VM, and **local agents** that run directly on your machine. Understanding when and how to use each — and how they work together — is key to getting maximum value from the platform.

## The Two Tiers at a Glance

| Dimension | Cloud Devin | Local Agents (Devin Desktop / Devin CLI) |
|-----------|-------------|------------------------------------------|
| **Where it runs** | Isolated VM in Cognition's cloud | Your machine (laptop, workstation) |
| **Lifecycle** | Persistent — survives laptop close, hibernates/resumes across days | Tied to your terminal or IDE session |
| **Autonomy** | Fully autonomous — works while you sleep | Interactive — you're in the loop |
| **Startup context** | Org-wide shared context layer (Knowledge, Playbooks, Secrets, MCP servers, VM blueprints) | Your local files, env vars, tools, running services |
| **Best for** | Long-running tasks, background work, parallel campaigns, scheduled ops | Quick fixes, exploration, iterative coding, tasks needing local env |
| **Parallelism** | Unlimited — spin up child agents across repos | Single session (your machine) |
| **Output** | PRs, deployed artifacts, reports, linked tickets | Direct file edits in your working tree |
| **Feedback model** | PR comments, Slack/Teams threads, web UI | Inline in your terminal or IDE |
| **Compute cost** | ACU-based (pay for active time) | Your machine's resources (credits for model calls) |

## Cloud Devin (The Autonomous Tier)

Cloud Devin is the platform described in the other docs in this folder — the fully autonomous agent that runs on its own VM with a shell, browser, desktop, and full Linux environment.

**Key characteristics:**
- Each session gets an isolated VM that boots from a pre-configured snapshot (environment blueprint)
- Accesses the org-wide shared context layer: Knowledge notes, Playbooks, Secrets, MCP servers, Git connections
- Works asynchronously — you can close your laptop and come back later
- Hibernates when idle, resumes instantly when feedback arrives (PR comment, CI result, user message)
- Can spawn child agents for divide-and-conquer parallelism
- Runs on schedules (cron) for proactive maintenance
- Responds to event-driven triggers (webhooks, alerts, ticket assignments)
- Communicates through PRs — never merges its own work

**When to use Cloud Devin:**
- Tasks that take more than a few minutes of active execution
- Work you want done while you focus on something else (or sleep)
- Multi-repo campaigns that benefit from parallelism
- Scheduled/recurring maintenance (dependency bumps, security remediation, docs refresh)
- Tasks triggered by external events (CI failure, incident alert, ticket assignment)
- Work that requires Devin's full platform capabilities (Playbooks, Knowledge, child agents)

## Local Agents (The Interactive Tier)

Local agents run on your machine and operate on your local files, tools, and environment. There are two form factors:

### Devin Desktop (IDE)

Devin Desktop is a next-generation AI IDE (built on the VS Code platform) that provides both local and cloud agent management in a single interface.

- **Devin Local** — The primary local agent inside Devin Desktop. Shares the same agent harness as Devin CLI. Runs on your machine with access to your local files, tools, and environment. Supports subagents for exploring code and MCP integrations
- **Agent Command Center** — A Kanban-style view that manages all agents (local and cloud) in one place. Start a task locally, hand it off to cloud, and track both from the same UI
- **Delegate to Cloud** — With a single click, send a task from your local agent to a cloud Devin session for autonomous execution

### Devin CLI (Terminal)

Devin CLI is a local coding agent that runs directly in your terminal:

```bash
# Install
curl -fsSL https://cli.devin.ai/install.sh | bash

# Start coding
cd your-project && devin
```

- Fast, interactive assistance right where you code
- Works with your local files, branches, running services, and environment
- Supports `/handoff` to seamlessly transfer work to a cloud Devin session
- Same agent harness as Devin Local in Devin Desktop

**When to use Local Agents:**
- Quick fixes and iterative development where you want immediate feedback
- Code exploration and understanding ("explain this module", "find where X is called")
- Tasks that benefit from your local environment (running services, VPN-connected databases, hardware access)
- Planning and scoping before handing off to cloud for execution
- Interactive debugging where you want to stay in the loop on every step
- Reviewing and iterating on small changes without waiting for a full PR cycle

## How They Work Together: The Handoff Model

The most powerful workflow combines both tiers — plan and scope locally, delegate to cloud for execution:

```
Local (fast, interactive)          Cloud (autonomous, persistent)
┌─────────────────────────┐        ┌─────────────────────────────────┐
│ Developer in IDE/terminal│        │ Devin on its own VM             │
│                         │        │                                 │
│ 1. Explore codebase     │        │                                 │
│ 2. Scope the approach   │        │                                 │
│ 3. Prototype solution   │        │                                 │
│                         │ ─────► │ 4. Full implementation          │
│    /handoff             │        │ 5. Tests, lint, CI              │
│                         │        │ 6. Open PR                      │
│                         │        │ 7. Iterate on review feedback   │
│                         │ ◄───── │ 8. PR ready for merge           │
│ 9. Review & merge       │        │                                 │
└─────────────────────────┘        └─────────────────────────────────┘
```

### The `/handoff` Command

From Devin CLI, transfer work to cloud Devin at any time:

```bash
/handoff fix the flaky integration tests in CI
```

Devin CLI packages up the conversation context and current git branch, then creates a cloud Devin session that picks up where you left off. You can track progress from the terminal or the Devin web app.

From Devin Desktop, delegate to cloud with a single click — the local agent's plan and context transfer to a new cloud session that executes autonomously.

### Complementary Strengths

| Scenario | Start With | Then |
|----------|-----------|------|
| Bug report comes in | Devin CLI: reproduce locally, confirm the issue | `/handoff` to cloud: implement fix, run full test suite, open PR |
| Large migration (50 services) | Cloud Devin: parent agent plans campaign | Cloud child agents: execute in parallel (one per service) |
| New feature design | Devin Local: explore codebase, prototype | Delegate to cloud: implement full feature, iterate on CI |
| Quick typo fix | Devin CLI: fix directly, push | (No handoff needed — stay local) |
| Scheduled dependency bump | (No local involvement) | Cloud Devin on cron: weekly bump + PR |
| Incident response | Devin CLI: triage logs locally while on-call | `/handoff` to cloud: implement and test the fix |

## Architecture: Shared Agent Harness, Different Environments

Devin Local (in Devin Desktop) and Devin CLI share the same underlying agent harness. This means:

- **Consistent behavior** — The same reasoning, tool use, and code generation logic whether running locally or in the cloud
- **Portable skills** — Patterns you teach the agent (AGENTS.md, skills) apply in both contexts
- **Unified identity** — Both local and cloud agents authenticate through the same Devin platform account

The key architectural difference is the **execution environment**:

| | Cloud Devin | Local Agents |
|---|---|---|
| **Environment** | Cognition-managed VM (Linux, from blueprint snapshot) | Your machine (macOS, Linux, Windows) |
| **File access** | Clones repos fresh into the VM | Reads/writes your local working tree directly |
| **Org context** | Full shared context layer (Knowledge, Playbooks, Secrets, MCP) | AGENTS.md, skills; Knowledge/Playbooks coming soon |
| **Network** | Configurable network policies, VPN, PrivateLink | Your machine's network (VPN, local services, etc.) |
| **Persistence** | Hibernates VM state; resumes across days/weeks | Session ends when you close the terminal/IDE |
| **Isolation** | Each session is a clean sandbox | Operates directly on your dev environment |

## When the Workshop Content Applies

The other documents in this folder focus primarily on **Cloud Devin** capabilities:

- **Architecture Strengths** → Describes the cloud VM's clean-room execution and shared context layer
- **Platform Capabilities** → Scheduled sessions, playbooks, child agents — all cloud features
- **Collaboration Model** → PR feedback loop, hibernation/resume — cloud session lifecycle
- **Design Patterns** → Patterns that help Cloud Devin succeed (locally testable code, event-driven triggers)
- **When to Use Devin** → Trigger categories for cloud sessions
- **Value Narratives** → ROI framing for cloud-tier autonomous work

These capabilities are what differentiate Devin from local-only coding assistants. The cloud tier is where:
- **Autonomy** lives — Devin works independently after you give it a task
- **Scale** lives — Child agents parallelize across repos and modules
- **Persistence** lives — Sessions hibernate and resume across the lifecycle of a task
- **Organizational knowledge** lives — Playbooks, Knowledge, and Secrets compound across every session

Local agents complement this by providing the **fast, interactive entry point** — explore, plan, prototype, then hand off to cloud for the heavy lifting.

## Summary: One Platform, Two Modes

Think of it as a team where you have:
- **Local agents** = the pair programmer sitting next to you, responding instantly to your questions and edits
- **Cloud Devin** = the autonomous teammate you delegate entire tasks to, who works independently and delivers PRs

The platform bridges both: unified authentication, shared agent architecture, and seamless handoff between interactive local work and autonomous cloud execution. Most mature teams use both — local agents for the developer's inner loop, cloud Devin for everything that should happen without a human in the chair.
