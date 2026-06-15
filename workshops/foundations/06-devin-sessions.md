# Devin Sessions

<a id="toc"></a>
## Table of Contents

- [What a Session Is](#what-a-session-is)
- [Session Lifecycle](#session-lifecycle)
- [Starting a Session](#starting-a-session)
- [The Session Interface](#the-session-interface)
- [Context That Flows In](#context-that-flows-in)
- [Outputs and Artifacts](#outputs-and-artifacts)
- [Session Economics](#session-economics)
- [Exploration Activity](#exploration-activity)
- [Key Takeaways](#key-takeaways)

---

<a id="what-a-session-is"></a>
## What a Session Is

A Devin session is the fundamental unit of work. When you give Devin a task, a session is created: an isolated Linux VM boots, the agent begins working, and it continues until the task is complete or it needs your input.

Think of a session as assigning a task to a team member who has their own workstation:
- They have their own machine (isolated VM)
- They have access to your repos, tools, and integrations (shared context layer)
- They work independently until they deliver output or need help
- They keep notes on what they did (session timeline)
- When they're done, they hand you the result (PR, report, or artifact)

Each session is isolated from every other session — separate filesystem, separate processes, separate branch. This means:
- Sessions cannot interfere with each other
- Failures in one session don't affect others
- Multiple sessions can run in parallel safely
- Each session's work is independently reviewable

<a id="session-lifecycle"></a>
## Session Lifecycle

```
┌──────────┐     ┌──────────┐     ┌──────────────────┐     ┌──────────┐
│  Created  │────▶│  Active  │────▶│  Waiting/Hibernated │────▶│  Resumed │
└──────────┘     └──────────┘     └──────────────────┘     └──────────┘
                      │                                          │
                      │            ┌──────────┐                  │
                      └───────────▶│ Completed │◀─────────────────┘
                                   └──────────┘
```

**Created:** Session initialized, VM booting, environment loading.

**Active:** Agent is executing — reading code, running commands, making edits, pushing commits. This is the phase that consumes compute (ACUs).

**Waiting/Hibernated:** Agent has delivered output (opened a PR, asked a question) and is waiting for input. The VM state is snapshot and compute is released. No cost during hibernation.

**Resumed:** New input arrives (PR comment, user message, CI result) and the VM restores from snapshot. Agent picks up exactly where it left off with full context.

**Completed:** Task is finished. The session becomes a read-only record — timeline, artifacts, and learnings are preserved.

### The Hibernation Model

Hibernation is what makes the economics work. The agent doesn't sit idle burning compute while you review a PR or think about feedback. It snapshots its state and releases resources. When you're ready to interact, it resumes in seconds with zero context loss.

This means:
- Long-running tasks (multi-day code reviews) are natural and cost-efficient
- You never feel pressured to respond quickly — the agent waits patiently
- Multiple sessions can be in flight simultaneously, each at different lifecycle stages

<a id="starting-a-session"></a>
## Starting a Session

Sessions can be started from multiple surfaces:

| Method | Best For |
|--------|----------|
| **Web app** (app.devin.ai) | Full-featured prompt composition with repo selection |
| **Slack / Teams** | Quick tasks from a conversation thread |
| **IDE delegation** | Handing off from local work to cloud execution |
| **CLI** (`devin` command) | Terminal-native workflows with cloud handoff |
| **API** (`POST /sessions`) | Programmatic triggering from CI, scripts, or orchestration |
| **Automations** | Event-driven (webhooks, schedules, issue assignment) |
| **Linear / Jira** | Assign a ticket directly to Devin |

The prompt you provide at session start is the agent's initial instruction. The quality of this prompt significantly impacts the quality of the output (see [What to Give AI](04-what-to-give-ai.md)).

<a id="the-session-interface"></a>
## The Session Interface

Once a session is running, you can observe and interact through the web app:

### Session Timeline
A chronological record of every action the agent takes:
- File edits (with diffs)
- Shell commands (with output)
- Browser interactions (with screenshots)
- Decision points (reasoning about approach)
- Errors encountered and how they were resolved

The timeline is your audit trail. After the session completes, you can review exactly what happened — useful for building trust, debugging unexpected output, or understanding the agent's reasoning.

### Desktop View
A live view of the agent's VM desktop. You can:
- Watch it work in real time (though this is usually not the best use of your time)
- Take manual control if needed
- Verify visual output (UI testing, browser-based interactions)

### Chat Interface
Send messages to the agent during the session:
- Provide additional context ("also check the config in `ops/` directory")
- Redirect approach ("don't use library X, use Y instead")
- Answer questions the agent asks

### Session Insights
Post-session analytics:
- ACU usage (how much compute was consumed)
- Timeline breakdown (time spent on each phase)
- Activity summary (files edited, commands run, commits pushed)

<a id="context-that-flows-in"></a>
## Context That Flows In

Every session automatically receives context from the organization's shared layer:

| Context Source | What It Provides |
|---------------|-----------------|
| **Environment Blueprint** | Pre-built VM with dependencies and tools installed — sessions boot ready to build |
| **Knowledge Notes** | Team conventions, coding standards, architecture decisions — retrieved based on task relevance |
| **Secrets** | API keys, tokens, credentials — injected into the environment securely |
| **Git Connections** | Repository access — agent can clone and push immediately |
| **MCP Servers** | Integrations (Jira, Datadog, etc.) — agent can query external tools |
| **AGENTS.md** | Repo-specific instructions — always-on rules for that codebase |
| **DeepWiki** | Architectural documentation — agent understands the system before acting |

This shared context layer means that configuring Devin is a one-time investment. Once set up, every session — from any team member — inherits the same configuration. No per-session setup required.

<a id="outputs-and-artifacts"></a>
## Outputs and Artifacts

What a session produces depends on the task:

| Output Type | When |
|-------------|------|
| **Pull Request** | Code changes — the most common output |
| **Report** | Analysis, research, or investigation findings |
| **Deployed Artifact** | Infrastructure changes, environment setup |
| **Conversation** | Answers to questions (Ask Devin mode) |
| **Status Update** | Progress on long-running tasks |

For code changes, the PR is the primary deliverable. It includes:
- A description explaining what was done and why
- The diff itself (reviewable in your git provider)
- CI results (the agent monitors and fixes failures before requesting review)
- A link back to the session for full context

<a id="session-economics"></a>
## Session Economics

Sessions consume ACUs (Agent Compute Units) during active work:

- **Active execution** — costs ACUs (agent running, building, testing)
- **Hibernation** — costs nothing (VM snapshot stored, compute released)
- **Human review time** — costs nothing (the clock stops when the agent waits)

This consumption model means:
- Short, focused tasks are inexpensive
- Long sessions with lots of human review time are still economical (hibernation periods are free)
- Failed attempts cost ACUs — clear prompts reduce wasted compute
- Child sessions (parallel work) multiply throughput but also multiply ACU consumption proportionally

Organizations set ACU budgets at the org, team, or user level. Budgets provide cost governance without limiting what the agent can accomplish within its allocation.

---

<a id="exploration-activity"></a>
## Exploration Activity

**Try this:** Open the Devin web app and browse recent sessions (yours or shared ones). For each session, notice:

1. **How it started** — what prompt was given, which surface was used
2. **The timeline** — how many steps the agent took, what tools it used, how it handled errors
3. **The lifecycle** — did it complete in one shot, or did it hibernate and resume?
4. **The output** — what was delivered (PR, report, etc.)
5. **The ACU cost** — check Session Insights for how much compute was consumed

Try starting a lightweight session yourself: use Ask Devin to ask a question about one of your team's repos. This creates a session without code changes — a low-commitment way to see the mechanics in action.

---

<a id="key-takeaways"></a>
## Key Takeaways

- A session is the unit of work: one task, one isolated VM, one output
- The lifecycle (active → hibernated → resumed → completed) makes long-running tasks economical — you only pay for active compute
- Sessions receive context automatically from the organization's shared layer (environment, knowledge, secrets, integrations)
- The session timeline provides a complete audit trail of every action
- Multiple sessions can run in parallel safely — each is fully isolated
- Sessions can be started from many surfaces: web app, Slack, IDE, CLI, API, automations, or issue trackers
- The PR is the most common output — it includes the implementation, CI verification, and a link back to the session for full context
