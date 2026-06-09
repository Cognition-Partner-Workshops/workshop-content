# Devin CLI — Feature Tour

Devin CLI is a local coding agent that runs directly in your terminal. It reads and modifies your local files, executes shell commands, and works interactively in a REPL — or runs headlessly for scripting and automation.

<a id="toc"></a>
## Table of Contents

- [Core Experience](#core-experience)
- [Getting Started](#getting-started)
- [Permission Modes](#permission-modes)
- [Agent Modes](#agent-modes)
- [Session Management](#session-management)
- [Extensibility](#extensibility)
- [Cloud Integration](#cloud-integration)
- [Key Commands Reference](#key-commands-reference)

---

<a id="core-experience"></a>
## Core Experience

| Aspect | Detail |
|--------|--------|
| Runtime | Local — works on your filesystem and shell environment |
| Interface | Interactive REPL (TUI) or single-turn stdout mode |
| Platforms | macOS, Windows, Linux |
| Install | `curl -fsSL https://cli.devin.ai/install.sh \| bash` |
| Also bundled with | Devin Desktop (Windsurf) 1.9577.24+ |

Devin CLI is for developers who live in the terminal. Same agent capabilities as Devin Desktop's Cascade — code generation, debugging, refactoring, shell execution — but in a keyboard-driven terminal interface.

---

<a id="getting-started"></a>
## Getting Started

```bash
# Start interactive REPL (no initial prompt)
devin

# Start REPL with an initial prompt
devin -- fix the failing test in src/auth.ts

# Single-turn mode — prints response to stdout and exits
devin -p "explain what this function does"

# Single-turn with -- separator
devin -p -- summarize the recent changes in this repo
```

### File Context
- Type `@` in the prompt to autocomplete local file/directory paths
- Selected files are added as context for the conversation
- Paste images with `Ctrl+V` — they are attached to the prompt

---

<a id="permission-modes"></a>
## Permission Modes

Devin CLI has four permission levels that control what the agent can do without asking:

| Mode | Behavior |
|------|----------|
| **Normal** (default) | Auto-approves reads in current directory; asks for writes and shell commands |
| **Accept Edits** | Auto-approves file edits within the workspace; still prompts for shell commands |
| **Bypass** | Auto-approves everything — full trust. Aliases: `/yolo`, `/dangerous` |
| **Autonomous** | Sandbox-only mode — auto-approves within OS-level containment |

### Switching Modes

```bash
/normal              # Default
/accept-edits        # Trust file edits
/bypass              # Trust everything (aliases: /yolo, /dangerous)
Shift+Tab            # Cycle through modes
```

### Bypass vs Autonomous

| | Bypass | Autonomous |
|---|---|---|
| Requires `--sandbox` | No | Yes |
| Shell commands | Unrestricted | Contained by sandbox |
| File writes | Anywhere | Prompts (granting expands sandbox) |
| Network access | Unrestricted | Filtered by domain allow/deny lists |

Start an autonomous sandboxed session:
```bash
devin --sandbox --permission-mode autonomous
```

Admin-enforced team settings **always** override local permission modes.

---

<a id="agent-modes"></a>
## Agent Modes

Beyond permission modes, Devin CLI has agent behavior modes:

| Mode | What It Does |
|------|-------------|
| **Normal** | Standard agent — plans and executes |
| **Plan** (`/plan`) | Creates a plan without executing — review before acting |
| **Ask** (`/ask <question>`) | Answers a question without modifying code (one-shot) |

### Loop Mode

```bash
/loop <prompt>
```

Runs a prompt, reviews the diff, and iterates in a loop. Requires a clean git state to start. Useful for iterative refinement ("keep improving until tests pass").

---

<a id="session-management"></a>
## Session Management

Sessions persist on disk — you can resume where you left off:

```bash
devin -c              # Continue most recent session in this directory
devin --continue
devin -r              # Pick from recent sessions (interactive)
devin --resume
devin -r brisk-otter  # Resume a specific session by ID
```

### Session Operations

| Command | Description |
|---------|-------------|
| `/ls` | List recent sessions in current directory |
| `/ls --all` | List all sessions across all directories |
| `/resume` | Interactive session picker |
| `/resume <id>` | Resume by ID |
| `/rm-session <id>` | Delete a session permanently |
| `/compact` | Force conversation compaction (reduce context size) |

---

<a id="extensibility"></a>
## Extensibility

### MCP (Model Context Protocol)
- Connect external tool servers — same marketplace integrations as Devin Cloud
- Configure per-project or globally
- Extend the agent with custom tools (databases, APIs, internal services)

### Skills
- Reusable prompt+workflow bundles for complex multi-step tasks
- Create custom skills for project-specific patterns
- Skills directory: `.devin/skills/` in your project

### Rules and AGENTS.md
- Always-on instructions loaded from project root or config
- Same `AGENTS.md` convention as Devin Cloud — portable across surfaces

### Lifecycle Hooks
- Execute custom logic on specific events (session start, command execution, etc.)
- Automate project-specific setup or validation

### Configuration

```bash
# Config file locations (checked in precedence order):
# 1. .devin/config.yaml (project-local)
# 2. ~/.config/devin/config.yaml (global)
```

- Import settings from Cursor, Windsurf, Claude Code, OpenCode, VS Code, Zed
- Global vs local precedence — project configs override global

### Model Selection
- Choose from available models via `/model`
- Adaptive routing available — auto-selects based on task complexity
- Configure default model in config file

---

<a id="cloud-integration"></a>
## Cloud Integration

Devin CLI connects to Devin Cloud for tasks that benefit from autonomous execution:

- Hand off long-running tasks to a cloud VM
- Fire-and-forget work while you continue locally
- Same Knowledge, Secrets, and Git connections available in both environments

> **Note:** Devin CLI does not yet support Knowledge, Playbooks, or Secrets retrieval from your Devin Cloud account directly. These are actively being added.

---

<a id="key-commands-reference"></a>
## Key Commands Reference

### Slash Commands

| Command | Description |
|---------|-------------|
| `/help` | See all available commands |
| `/exit` or `/quit` | Exit the application |
| `/clear` or `/new` | Clear conversation history (start fresh) |
| `/mode` | Show or switch current mode |
| `/plan` | Switch to Plan mode |
| `/ask <question>` | Ask without modifying code |
| `/model` | Show model selector |
| `/loop <prompt>` | Iterative prompt-diff-refine loop |
| `/workspace` | List workspace directories |
| `/add-dir <path>` | Add workspace directory |
| `/hooks` | List loaded hooks |
| `/login` | Authenticate with Devin |
| `/logout` | Clear credentials and exit |
| `/update` | Check for and install updates |
| `/bug` | Report a bug |

### Keyboard Shortcuts

| Shortcut | Description |
|----------|-------------|
| `Shift+Tab` | Cycle between permission modes |
| `Ctrl+C` | Clear input or cancel running agent |
| `Esc` | Cancel running agent |
| `Shift+Enter` | Insert newline (multi-line input) |
| `Ctrl+V` | Paste from clipboard (text or images) |
| `Ctrl+G` | Open external editor for long prompts |
| `Ctrl+O` | Full-screen thinking trace viewer |
| `@` | Mention files to add as context |

---

## When to Use Devin CLI

Devin CLI is the right choice when:
- You prefer working in the terminal over an IDE or web app
- You want interactive, real-time control over what the agent does
- You need to script AI-assisted workflows (CI, automation, batch processing)
- You want local execution with immediate filesystem access
- You are working on a task that needs your local environment (VPNs, custom tools, local DBs)
- You want to prototype a change before handing it off to Devin Cloud for full implementation
