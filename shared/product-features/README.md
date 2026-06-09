# Product Feature Tours

A guided tour of Devin's three product surfaces — what each one does, when to use it, and how they work together.

| Product | What It Is | Guide |
|---------|-----------|-------|
| [Devin Cloud](devin-cloud.md) | Autonomous cloud agent — runs on its own VM, opens PRs, monitors CI, scales to child sessions | Feature tour |
| [Devin Desktop](devin-desktop.md) | AI-powered IDE (Windsurf) with Cascade, autocomplete, and cloud delegation | Feature tour |
| [Devin CLI](devin-cli.md) | Terminal-based local agent with REPL, permission modes, and cloud handoff | Feature tour |

## How They Relate

```
┌─────────────────────────────────────────────────────────────┐
│                     Devin Platform                           │
├───────────────┬───────────────────────┬─────────────────────┤
│  Devin Cloud  │    Devin Desktop      │     Devin CLI       │
│  (Web App)    │    (IDE / Windsurf)   │     (Terminal)      │
│               │                       │                     │
│  Autonomous   │  Interactive coding   │  Local agent in     │
│  background   │  with AI pair +       │  your terminal +    │
│  agent on     │  cloud delegation     │  cloud delegation   │
│  its own VM   │                       │                     │
└───────┬───────┴───────────┬───────────┴──────────┬──────────┘
        │                   │                      ╎ *
        └───────────────────┼──────────────────────┘
                            │
              Shared: Knowledge, Playbooks, Secrets,
              Git Connections, MCP, Automations, DeepWiki

* CLI currently shares Git connections and MCP directly.
  Knowledge, Playbooks, and Secrets are accessed via Cloud
  delegation (coming natively to CLI soon).
```

- **Devin Cloud** is for autonomous, fire-and-forget tasks — kick off a session and move on
- **Devin Desktop** is for interactive pair programming with an AI that sees your editor context in real time
- **Devin CLI** is for developers who live in the terminal — same agent capabilities, no GUI required

Devin Cloud and Devin Desktop share the same organizational context layer (Knowledge, Secrets, Git connections). Devin CLI shares Git connections and can delegate to Cloud sessions that use the full context layer.
