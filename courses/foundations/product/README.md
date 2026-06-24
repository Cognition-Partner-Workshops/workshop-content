# Product Features

A guided tour of Devin's product surfaces — what each one does, when to use it, and how they work together. Content is organized by deployment model: **cloud** (autonomous agents) and **local** (in your editor or terminal).

```
product/
├── cloud/
│   ├── devin-cloud.md             ← Devin Cloud feature tour
│   ├── 05-deepwiki.md             ← How source code becomes consumable wiki pages
│   ├── 06-devin-sessions.md       ← The worker model: task → execution → PR
│   ├── 08-automations.md          ← Triggers, schedules, and event-driven workflows
│   └── 09-multi-agent-workers.md  ← Parallel task distribution across agents
└── local/
    ├── devin-desktop.md           ← Devin Desktop (Windsurf IDE) feature tour
    ├── 07-devin-cli.md            ← Devin CLI foundations
    └── devin-cli-tour.md          ← Devin CLI comprehensive feature tour
```

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
        │                   │                      │
        └───────────────────┼──────────────────────┘
                            │
              Shared context and natural integration points
```

- **Devin Cloud** is for autonomous, fire-and-forget tasks — kick off a session and move on
- **Devin Desktop** is for interactive pair programming with an AI that sees your editor context in real time
- **Devin CLI** is for developers who live in the terminal — same agent capabilities, no GUI required

All three share context through the Devin platform (Knowledge, Secrets, Git connections, MCP) and can delegate work to Devin Cloud for autonomous execution.

## Reading Order

### Cloud

| # | Topic | What You'll Learn |
|---|-------|-------------------|
| — | [Devin Cloud Feature Tour](cloud/devin-cloud.md) | Comprehensive overview of the cloud agent — execution model, sessions, orchestration, integrations, admin |
| 5 | [DeepWiki](cloud/05-deepwiki.md) | How source code is distilled into consumable wiki pages that give the agent codebase understanding |
| 6 | [Devin Sessions](cloud/06-devin-sessions.md) | The worker model — how tasks are received, executed, and delivered |
| 8 | [Automations](cloud/08-automations.md) | Triggers, scheduled routines, and event-driven workflows |
| 9 | [Multi-Agent Workers](cloud/09-multi-agent-workers.md) | Breaking large tasks into subunits distributed across parallel workers |

### Local

| # | Topic | What You'll Learn |
|---|-------|-------------------|
| — | [Devin Desktop Feature Tour](local/devin-desktop.md) | Comprehensive overview of the AI-powered IDE — Devin Local, autocomplete, cloud delegation |
| 7 | [Devin CLI](local/07-devin-cli.md) | The terminal interface that bridges local development with cloud delegation |
| — | [Devin CLI Feature Tour](local/devin-cli-tour.md) | Comprehensive CLI reference — permission modes, agent modes, session management, extensibility |
