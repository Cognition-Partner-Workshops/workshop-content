# Devin Desktop — Feature Tour

Devin Desktop is the AI-powered IDE (built on Windsurf) that brings agentic coding directly into your editor. It combines real-time code intelligence, an autonomous local agent (Devin Local, successor to Cascade), and seamless delegation to Devin Cloud — all in one window.

<a id="toc"></a>
## Table of Contents

- [Core Experience](#core-experience)
- [Devin Local — The Agentic Assistant](#devin-local)
- [Autocomplete](#autocomplete)
- [Context Awareness](#context-awareness)
- [Cloud Delegation](#cloud-delegation)
- [Extensibility](#extensibility)
- [Collaboration Features](#collaboration-features)
- [Admin and Enterprise](#admin-and-enterprise)

---

<a id="core-experience"></a>
## Core Experience

| Aspect | Detail |
|--------|--------|
| Foundation | Full-featured IDE based on VS Code architecture |
| AI Layer | Devin Local (agentic assistant) + in-house autocomplete |
| Execution | Local — works on your filesystem and environment |
| Cloud bridge | Delegate tasks to Devin Cloud directly from the IDE |
| Platforms | macOS, Windows, Linux |

Devin Desktop is where you spend your day coding. The AI layer augments your workflow throughout — suggesting completions, running multi-step tasks, answering questions, and handing off heavy work to the cloud.

---

<a id="devin-local"></a>
## Devin Local — The Agentic Assistant

Devin Local is the agentic AI assistant built into Devin Desktop. Open it with `Cmd/Ctrl+L`.

### Modes

| Mode | Purpose |
|------|---------|
| **Code** | Devin Local creates and modifies files in your workspace |
| **Chat** | Optimized for questions — no file modifications, but can propose code to accept |

### Key Capabilities

| Feature | Description |
|---------|-------------|
| Tool calling | Up to 20 tool calls per prompt — shell commands, file ops, web search, MCP |
| Plans and Todo lists | Built-in planning agent refines long-term strategy while the action model executes |
| Real-time awareness | Knows what you just did in the editor — no need to re-explain context |
| Checkpoints and reverts | Named snapshots of project state; revert to any checkpoint instantly |
| Voice input | Speak instructions — transcribed to text in the prompt box |
| Web and Docs search | Search the internet and documentation sites for reference material |
| Linter integration | Reads your linter output and fixes issues automatically |
| Explain and Fix | Highlight an error → one-click fix with explanation |
| Send Problems to Devin Local | Route editor diagnostics directly into a Devin Local conversation |
| Auto-Continue | Devin Local automatically resumes if it hits a tool-call limit |
| Queued messages | Queue follow-up prompts while Devin Local works on the current one |

### Workflows
- Automate repetitive multi-step tasks (formatting, refactoring patterns, code generation)
- Reusable — define once, trigger repeatedly

### Skills
- Structured multi-step procedures for complex tasks
- Similar to Devin Cloud playbooks but for the local agent

### Arena Mode
- Run multiple models on the same prompt simultaneously
- Compare outputs side-by-side to pick the best result

### Worktrees
- Work on multiple branches simultaneously in separate worktrees
- Each worktree gets its own Devin Local context

---

<a id="autocomplete"></a>
## Autocomplete

| Aspect | Detail |
|--------|--------|
| Engine | In-house model trained from scratch for speed and accuracy |
| Types | Single-line and multi-line suggestions |
| Accept | `Tab` to accept, `Cmd/Ctrl+→` to accept word-by-word |
| Cycle | `Alt+]` / `Alt+[` to see alternative suggestions |
| Speed settings | Configurable — Fast mode available on Pro/Teams/Enterprise |
| Context | Reads surrounding code, imports, and project structure |

Autocomplete works alongside Devin Local — completions appear inline as you type, independent of any active Devin Local conversation.

---

<a id="context-awareness"></a>
## Context Awareness

### Indexing
- **Local indexing** — RAG-based retrieval over your workspace files
- **Fast Context** — Lightweight context retrieval for speed-critical completions

### DeepWiki
- Access auto-generated architecture documentation for indexed repos
- Symbol-level navigation and AI-powered explanations of unfamiliar code and dependencies

### Memories and AGENTS.md
- Persistent context that carries across Devin Local conversations
- `AGENTS.md` files in repos provide always-on instructions — same convention as Devin Cloud, portable across surfaces
- Memories: learned context Devin Local retains between sessions

### .codeiumignore
- Exclude files/directories from AI processing
- Works like `.gitignore` — patterns in the root of your workspace

### Codemaps
- Hierarchical visualization of your codebase structure
- Shareable — export and send to teammates

---

<a id="cloud-delegation"></a>
## Cloud Delegation

Devin Cloud is built directly into Devin Desktop:

1. Work on a plan locally with Devin Local
2. Click to delegate the implementation to Devin Cloud
3. Devin Cloud spins up its own VM and works autonomously
4. Review the resulting PR without leaving the IDE

### Agent Command Center
- Kanban view of all your agents — both local Devin Local sessions and Devin Cloud sessions
- Monitor progress, switch between tasks, manage workload

### Spaces
- Group related sessions, PRs, files, and context for a project
- Organize local and cloud work together

---

<a id="extensibility"></a>
## Extensibility

### MCP (Model Context Protocol)
- Connect external tool servers to Devin Local
- Same marketplace integrations available in Devin Cloud (Jira, Datadog, etc.)
- Configure per-workspace or globally

### Hooks
- Execute custom shell commands at key lifecycle points (pre/post actions)
- Automate project-specific workflows triggered by Devin Local actions

### Agent Client Protocol (ACP)
- Run third-party agents inside Devin Desktop
- Build custom agents that integrate with the IDE

### Adaptive Model Routing
- Intelligent model selection based on task complexity
- Routes simpler tasks to faster models, complex tasks to more capable ones

### Command Palette
- Quick actions, navigation, and AI-powered features via `Cmd/Ctrl+Shift+P`
- Code lenses for inline AI actions
- Plugins for language-specific and framework-specific intelligence

---

<a id="collaboration-features"></a>
## Collaboration Features

| Feature | Description |
|---------|-------------|
| AI Commit Messages | Auto-generated meaningful commit messages from your diff |
| Code Lenses | Inline suggestions and actions overlaid on your code |
| Chat (non-agentic) | Conversational Q&A about your codebase with citations |
| Terminal integration | Enhanced terminal with AI assistance |

---

<a id="admin-and-enterprise"></a>
## Admin and Enterprise

| Capability | Description |
|-----------|-------------|
| Team Settings | Org-wide configuration for model access, permissions, and policies |
| Domain Verification | Verify company domain for automatic team invitations |
| RBAC | Role-based access control for feature and model access |
| Enterprise Policies | Managed settings pushed to all team members |
| Usage Analytics | Individual and team metrics — completions, chat, commands |
| Quota Management | Credit-based usage with configurable limits |
| SSO | Enterprise authentication integration |

---

## When to Use Devin Desktop

Devin Desktop is the right choice when:
- You are actively coding and want AI assistance in real time
- You need the AI to see your exact editor context (open files, cursor position, terminal)
- You want to iterate interactively — accept/reject changes one by one
- You prefer working in an IDE rather than a web app or terminal
- You want to start locally and delegate heavy work to the cloud without switching tools
