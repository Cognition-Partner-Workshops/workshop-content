# AGENTS.md — workshop-metadata

## Repository Purpose

This repo is the central metadata hub for technical workshops in the Cognition-Partner-Workshops organization. It contains:
- `modules/` — Atomic challenge tasks organized by category (security, migration, data-engineering, etc.)
- `workshops/` — Reusable lab sequences and workshop templates
- `events/` — Point-in-time workshop instances (active and archived)
- `shared/` — Facilitator guide, general-themes narrative, quality checklist, naming conventions
- `catalog/` — Repository inventory and upstream provenance

All content is Markdown. There is no application code in this repo.

## Quality Standards

Read `shared/quality-checklist.md` before creating or editing any workshop content. It is the authoritative quality standard.

## Content Rules

### Language
- Never say "demo" — use "try", "hands-on", or "walkthrough"
- Use "Key Takeaways" not "Key Talking Points"
- Name events as "workshops" not "arcs"
- No overstatement language ("every", "all", "always", "guaranteed") for probabilistic capabilities like DeepWiki or AI analysis. Use "typically", "in most cases", "coverage depends on repo structure"
- US English spelling (modernization, not modernisation)

### Privacy
- Never include customer names, venue names tied to customers, or identifying product references
- Use generic placeholders like "*(customer)*"
- Do not identify the user requester in PR descriptions or commit messages

### Structure
- Modules longer than 3 sections need a Table of Contents with `<a id="..."></a>` anchors
- Include a "Quick Start" section near the top of hands-on modules so experienced users can jump to the first prompt
- Main module files are for the attendee (the person with the problem). Facilitator-only content goes in a sibling `-facilitator.md` file

### Prompts
- All paste-into-Devin prompts use triple-backtick fenced code blocks (for GitHub copy button)
- Line-wrap prompts at ~80 chars for readability
- Do NOT include "Open a PR" in prompts — Devin does this by default
- Prompts must include repo names, file paths, and expected output format

### General Themes
Every module and workshop should incorporate applicable principles from `shared/general-themes/`:
- Devin is a **team-based resource**, not an individual tool
- Position tasks as event-driven, large-scale, or capacity-constrained work
- Include automation narrative: webhooks, scheduled sessions, child sessions for divide-and-conquer
- Reference the shared context layer: Knowledge notes, DeepWiki, MCP integrations, playbooks
- Show the PR feedback loop and collaboration model

### Technical Accuracy
- Verify file counts, directory structures, and artifact descriptions against actual repos
- Every file path in a prompt must exist on the referenced repo's main branch
- Do not claim capabilities the workshop environment doesn't support

## Branch and PR Conventions

- Branch naming: `devin/<timestamp>-<description>`
- All workshop content merges to `main` — do not leave starting-state content on feature branches
- Workshop instructions never point participants to `devin/...` branches
- Participants create `workshop-<attendee_id>` branches from `main`

## Reference Files

- Quality checklist: `shared/quality-checklist.md`
- General themes: `shared/general-themes/` (7 files)
- Facilitator guide: `shared/facilitator-guide.md`
- Repo naming: `shared/repo-naming-convention.md`
- Exemplar module: `modules/data-engineering/sas-migration-analysis.md`
- Exemplar facilitator companion: `modules/data-engineering/sas-migration-analysis-facilitator.md`

## Playbooks

- `!workshop_event` — Create a new workshop or event
- `!quality_sweep` — Apply quality checklist across modules/events
- `!find_demo_code` — Discover and import open source repos
