# Workshop Variant Conventions

Workshop content can target different delivery surfaces of the Cognition Platform. The file suffix indicates which surface(s) a workshop is written for.

## File Suffixes

| Suffix | Surface | Participant Uses | Example |
|--------|---------|-----------------|---------|
| `.md` | **Devin Cloud only** | Cloud agent sessions via the Devin web app (app.devin.ai). All interaction through the browser: paste prompts, review PRs, leave comments | `README.md` |
| `.platform.md` | **Devin Desktop + Cloud** | Devin Desktop as the primary interface. Participants delegate tasks to Cloud from Desktop, review PRs in-editor, use the Agent Command Center and Spaces. Shows the full local→cloud→review loop | `README.platform.md` |
| `.local.md` | **Devin CLI / Devin Local only** | Terminal-based (Devin CLI) or Devin Local in Desktop. All work happens on the participant's local machine — no cloud VM sessions. For quick iterations, code exploration, and local-first workflows | `README.local.md` |

## Which Suffix to Use

### `README.md` (Cloud Agent)
Use when the workshop is designed for participants interacting with Devin's cloud agent through the web interface:
- Participants paste prompts into the Devin web app
- Devin works autonomously on a remote VM
- Output is a PR that participants review on GitHub/GitLab
- The full PR feedback loop (comment → iterate → approve → merge) is the primary interaction model
- Scheduled sessions, child agents, automations, and playbooks are all cloud features

**Platform story to weave in:** Even in cloud-only workshops, reference that the PRs Devin opens can be one-click checked out in Devin Desktop. Mention that teams typically manage multiple cloud sessions from the Agent Command Center. Note that the same playbooks and knowledge that power cloud sessions are also available to local agents.

### `README.platform.md` (Desktop + Cloud)
Use when the workshop demonstrates the full platform continuum:
- Participants use Devin Desktop as their primary environment
- They create Spaces to organize work
- They delegate tasks to Cloud from Desktop and monitor progress in the Agent Command Center
- They review cloud-generated PRs directly in the editor (one-click checkout)
- They may also use Cascade or Devin Local for quick local tasks alongside cloud work
- The narrative emphasizes the handoff between local exploration and cloud execution

**Platform story to weave in:** This IS the platform story. Show the full loop: explore locally → refine requirements → delegate to Cloud → monitor in Kanban → review in editor → iterate → merge. Reference ACP and the ability to use multiple agents. Highlight Spaces as the organizational unit.

### `README.local.md` (CLI / Devin Local)
Use when the workshop is designed for terminal-native or local-first workflows:
- Participants run `devin` in their terminal or use Devin Local in Desktop
- All code changes happen on their local machine
- No cloud VMs or remote sessions are involved
- Ideal for: code exploration, quick prototyping, rapid iteration, pair-programming style work
- Subagents handle parallel subtasks locally

**Platform story to weave in:** Note that tasks can be handed off to Cloud when they outgrow local execution. Mention that Devin CLI is consumable over ACP inside Devin Desktop. Reference that the same skills and MCP servers work across all surfaces.

## Coexistence Rules

1. **Every workshop directory MUST have a `README.md`** — the cloud-agent variant is the baseline
2. Additional variants (`.platform.md`, `.local.md`) are optional and created when the workshop content genuinely benefits from a surface-specific narrative
3. Variants are not copies with find-and-replace edits — each should be written for its delivery surface with appropriate prompts, workflow steps, and platform context
4. All variants share the same lab structure (tracks, lab numbering) for cross-referencing
5. The `README.md` variant should mention the existence of other variants at the top if they exist

## Platform Context Sections

Every workshop variant (regardless of suffix) should include a section near the top called **"Platform Context"** that briefly explains which Cognition Platform surfaces are used and how they connect. Template:

```markdown
## Platform Context

This workshop uses **[surface(s)]**. [1-2 sentences on the workflow].

> **Tip:** [One sentence connecting to other surfaces for participants who want to explore further]
```

## Cross-Surface References

When a workshop variant references capabilities from another surface, use callout blocks:

```markdown
> **Desktop tip:** The PRs Devin opens in this lab can be reviewed directly in Devin Desktop
> with one-click checkout — no manual `git fetch` required.
```

```markdown
> **Cloud handoff:** If this task needs long-running autonomous execution, you can hand it
> off to Devin Cloud. The cloud agent picks up where your local session left off.
```

```markdown
> **CLI alternative:** This same task can be run from your terminal with `devin`. See the
> `.local.md` variant of this workshop for the terminal-native workflow.
```
