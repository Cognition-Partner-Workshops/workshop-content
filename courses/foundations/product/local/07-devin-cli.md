# Devin CLI

<a id="toc"></a>
## Table of Contents

- [What Devin CLI Is](#what-devin-cli-is)
- [Core Experience](#core-experience)
- [Permission Modes](#permission-modes)
- [Working Modes](#working-modes)
- [Cloud Handoff](#cloud-handoff)
- [When to Use CLI vs. Cloud](#when-to-use-cli-vs-cloud)
- [Exploration Activity](#exploration-activity)
- [Key Takeaways](#key-takeaways)

---

<a id="what-devin-cli-is"></a>
## What Devin CLI Is

Devin CLI is a local coding agent that runs in your terminal. It reads and modifies your local files, executes shell commands, and works interactively — or headlessly for scripting.

If Cloud Devin is the team member who works in a separate office, Devin CLI is the pair programmer sitting next to you. Same intelligence, different interaction model:

| | Cloud Devin | Devin CLI |
|---|---|---|
| **Where it runs** | Remote VM (cloud) | Your local machine |
| **Interaction** | Asynchronous (delegate → review later) | Synchronous (interactive REPL) |
| **Scope** | Full tasks end-to-end | Single session in your working directory |
| **Output** | PRs, reports, deployments | Local file changes, shell output |
| **Best for** | Autonomous background work | Interactive development, exploration, quick tasks |

<a id="core-experience"></a>
## Core Experience

### Starting a Session

```bash
# Interactive REPL — start a conversation
devin

# Start with a prompt
devin -- fix the failing test in src/auth.ts

# Single-turn mode — answer and exit
devin -p "explain what this function does"
```

The interactive REPL is the primary interface. You type natural language instructions; the agent reads your files, runs commands, and makes changes — asking for approval where configured.

### File Context

Point the agent at specific files for context:
- Type `@` to autocomplete local file/directory paths
- Selected files are added as context for the conversation

### Session Continuity

Sessions persist on disk — resume where you left off:
```bash
devin -c              # Continue most recent session
devin -r              # Pick from recent sessions (interactive)
devin -r brisk-otter  # Resume a specific session by ID
```

<a id="permission-modes"></a>
## Permission Modes

Devin CLI operates within a permission model that gives you control over what it can do without asking:

| Mode | What the Agent Can Do Without Asking |
|------|-------------------------------------|
| **Normal** (default) | Read files in current directory |
| **Accept Edits** | Read + write files within workspace |
| **Bypass** | Everything — full trust (`/yolo`) |
| **Autonomous** | Everything within OS-level sandbox containment |

Switch modes during a session:
```bash
/normal          # Default — asks before writing or running commands
/accept-edits   # Trust file edits, still prompt for shell commands
/bypass          # Trust everything (alias: /yolo)
Shift+Tab        # Cycle through modes
```

The permission model lets you calibrate trust:
- **First time with a new codebase?** Stay in Normal mode — review every change.
- **Familiar codebase, routine task?** Accept Edits mode — let it write freely, approve shell commands.
- **Full trust, well-tested workflow?** Bypass mode — maximum speed.
- **Untrusted environment?** Autonomous mode with sandbox containment.

### Team-Level Controls

Organization admins can enforce permission policies that override local settings. This prevents individuals from accidentally running in bypass mode in sensitive repositories.

<a id="working-modes"></a>
## Working Modes

Beyond permissions, Devin CLI has distinct working modes:

### Normal Mode (Default)
The agent plans and executes. It reads the codebase, forms a plan, makes changes, and runs tests — with approval gates based on the permission level.

### Plan Mode (`/plan`)
The agent creates a detailed plan **without executing**. You review the plan first, then decide whether to proceed. Useful when you want to understand the approach before any changes are made.

### Ask Mode (`/ask <question>`)
The agent answers a question without modifying anything. One-shot — useful for quick explanations, code understanding, or research.

### Loop Mode (`/loop <prompt>`)
The agent runs a prompt iteratively — execute, review the diff, improve, repeat. Requires a clean git state. Useful for iterative refinement: "keep improving until tests pass" or "refactor until no lint warnings remain."

<a id="cloud-handoff"></a>
## Cloud Handoff

The most powerful CLI feature is seamless delegation to Cloud Devin:

```
You're working locally in the CLI
    ↓
Task becomes too large or long-running for local execution
    ↓
Hand off to Cloud Devin — the task continues on a remote VM
    ↓
You continue other work locally
    ↓
Cloud session opens a PR when done
```

This bridges the gap between local and cloud workflows:
- Start exploring a problem locally (fast iteration, immediate feedback)
- Once you understand the scope, delegate the implementation to cloud (autonomous execution)
- Review the output as a PR (asynchronous, at your convenience)

The handoff preserves context — the cloud session knows what you explored locally and continues from that understanding.

### When to Hand Off

| Stay Local | Hand Off to Cloud |
|------------|-------------------|
| Quick edits in a few files | Multi-file refactoring across the codebase |
| Exploring and understanding | Full implementation once approach is clear |
| Debugging interactively | Long-running builds and test suites |
| Prototyping an approach | Applying the same change to many targets |

<a id="when-to-use-cli-vs-cloud"></a>
## When to Use CLI vs. Cloud

| Scenario | Recommendation |
|----------|---------------|
| "I want to fix this one test quickly" | CLI — fast, interactive, immediate |
| "I need to upgrade 20 microservices" | Cloud — autonomous, parallelizable |
| "I'm not sure what the problem is yet" | CLI — explore interactively, then hand off |
| "This will take 2 hours to build and test" | Cloud — runs in the background |
| "I want to refactor while pair-programming" | CLI — real-time feedback loop |
| "I want Devin to handle it overnight" | Cloud — fire and forget |
| "I'm in a terminal SSH session, no GUI" | CLI — works anywhere you have a shell |

The CLI and Cloud are not competing tools — they are complementary surfaces for the same platform. Context (Knowledge, Secrets, AGENTS.md, MCP) is shared across both.

---

<a id="exploration-activity"></a>
## Exploration Activity

**Try this:** If you have Devin CLI installed, try these commands:

1. Navigate to any local git repository
2. Run `devin` to start an interactive session
3. Ask: "What does this project do? Summarize the architecture."
4. Try `/plan -- add a health check endpoint` to see how it plans without executing
5. Try `/ask what testing framework does this project use?` for a quick answer

If you don't have CLI installed yet, review the install command:
```bash
curl -fsSL https://cli.devin.ai/install.sh | bash
```

Notice how the CLI provides the same intelligence as Cloud Devin but with a faster, tighter feedback loop — ideal for the exploration and prototyping phase before delegating implementation.

---

<a id="key-takeaways"></a>
## Key Takeaways

- Devin CLI is a local coding agent in your terminal — same intelligence as Cloud, different interaction model
- Permission modes (Normal → Accept Edits → Bypass → Autonomous) let you calibrate trust
- Working modes (Normal, Plan, Ask, Loop) give you control over execution style
- Cloud handoff lets you start locally and delegate to the cloud when the task outgrows interactive work
- CLI and Cloud share context (Knowledge, Secrets, AGENTS.md, MCP) — they are complementary surfaces
- Use CLI for exploration, quick tasks, and interactive development; use Cloud for autonomous, long-running, or parallelizable work
