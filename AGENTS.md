# AGENTS.md — workshop-content

## Repository Purpose

This repo is the **attendee-facing** content hub for Devin enablement and hands-on workshops. It contains reading courses, lab instructions, prompts, and reference material that participants use.

Content is organized by **how it is consumed**:

- `courses/` — **Reading/learning enablement** (sequential, low-optionality, by role/track — no coding):
  - `courses/foundations/` — Level 1: Concepts (`concepts/`) + product features (`product/cloud/`, `product/local/`). Everyone starts here.
  - `courses/tracks/` — Level 2: Role-specific training (sales, solutions, engineering)
- `workshops/` — **Hands-on, choose-your-own-adventure lab collections** (no solutions/assessments):
  - `workshops/general/` — Broad hands-on tour
  - `workshops/otterworks/` — Flagship polyglot monorepo deep-dive (300-level)
  - `workshops/by-tech-domain/` — Level 3: Deep-dive labs by technology area (modernization, security, AI)
  - `workshops/by-tech-role/` — Level 3: Deep-dive labs by job function (development, digital engineering, QE)
- `labs/` — Individual hands-on lab modules organized by discipline (the reusable building blocks workshops compose)
- `demos/` — Facilitator-led demo showcases (single linear thread, persona-targeted, read for a user following along; not participant-driven)
- `events/` — Event-specific, non-reusable agendas and customizations (active and archived)
- `reference/` — Shared lookup material: general Devin themes (`general-themes/`) and runtime resource docs (`runtime-resources.md`)
- `catalog/` — Machine-readable repository inventory and upstream provenance (for AI agents and humans)

All content is Markdown. There is no application code in this repo.

**Courses vs. workshops:** `courses/` is sequential reading you complete in order for your track. `workshops/` and `labs/` are hands-on and branch freely — pick the labs that fit your interest. Keep reading/learning material in `courses/`; keep paste-into-Devin hands-on material in `workshops/` and `labs/`.

**Facilitator-facing content** (day-of logistics, quality checklist, repo naming conventions, module facilitator notes, event templates, provisioning scripts) lives in the [operator](https://github.com/Cognition-Partner-Workshops/operator) repo.

## Content Rules

### Audience
This repo serves two attendee modes. Most content targets the **hands-on lab attendee** — the person sitting at the keyboard, pasting prompts into Devin, and reviewing PRs; this content should be action-oriented and help them succeed in the lab. Content under `courses/` targets the **reader/learner** building intuition before hands-on work — reading-first, sequential, with light exploration activities rather than copy-paste prompts.

**Exception — `demos/`:** demo docs are a single linear thread a presenter runs live while others follow along, not a participant-driven lab. They differ from `labs/` and `workshops/` (which can offer separate choose-your-own-adventure tracks) by walking one path start to finish. Write them to read as though a user is reading and following along: lead straight into the guide with prompts and user instructions, keep preamble minimal, and avoid hands-on "try this"-style framing (the reader is following, not branching). Summary sections still use **"Key Takeaways"**.

### Language
- Never say "demo" in `courses/`, `labs/`, `workshops/`, or `events/` content — use "try", "hands-on", or "walkthrough". **Exception:** content under the `demos/` directory is facilitator-led showcase material and may use "demo" verbiage (file names, titles, headers, and body). "demo" verbiage is permitted **only** under `demos/`.
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

### Prompts
- All paste-into-Devin prompts use triple-backtick fenced code blocks (for GitHub copy button)
- Line-wrap prompts at ~80 chars for readability
- Do NOT include "Open a PR" in prompts — Devin does this by default
- Prompts must include repo names, file paths, and expected output format

### General Themes
Every module and workshop should incorporate applicable principles from `reference/general-themes/`:
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

- General themes: `reference/general-themes/` (8 files)
- Product feature tours: `courses/foundations/product/` (cloud and local surfaces)
- Runtime resources: `reference/runtime-resources.md`
- Exemplar module: `labs/data-engineering/sas-migration-analysis.md`
- Facilitator content (quality checklist, facilitator guide, repo naming, module facilitator notes): [operator repo](https://github.com/Cognition-Partner-Workshops/operator)

## Playbooks

- `!workshop_event` — Create a new workshop or event
- `!quality_sweep` — Apply quality checklist across labs/events
- `!find_demo_code` — Discover and import open source repos
