# Devin Review Guidelines — workshop-metadata

## Repository Context

This repo contains **attendee-facing** workshop content (modules, workshops, events) and shared reference materials for the Cognition-Partner-Workshops organization. All content is Markdown. There is no application code — PRs contain documentation, prompts, and instructional materials. Facilitator-facing content (day-of guides, quality checklist, event templates, module facilitator notes) lives in the [operator](https://github.com/Cognition-Partner-Workshops/operator) repo.

## Priority Review Areas

### Language and Tone (High Priority)

- **Never "demo"** — Flag any use of "demo" in workshop/module content. The correct terms are "try", "hands-on", or "walkthrough". Section headers should say "What to Try" not "What to Demo".
- **"Key Takeaways" not "Key Talking Points"** — Summary bullets under each lab/activity must use "Key Takeaways" as the header.
- **"Workshops" not "arcs"** — User-facing names must say "workshop" not "arc". Internal structural references to arcs are fine.
- **No overstatements** — Flag guarantee language ("every", "all", "always", "guaranteed", "comprehensive coverage") when describing probabilistic tools like DeepWiki or AI analysis. Prefer "typically", "in most cases", "coverage depends on repo structure".
- **No customer names** — Flag any customer names, venue names tied to customers, product names that identify a customer, or any identifying references. These must be replaced with generic placeholders like "*(customer)*".

### Content Separation (High Priority)

- **Attendee-only content** — This entire repo is for attendees. Flag any facilitator-only content (MCP setup, presales positioning, timing guides, pacing tips for facilitators, post-event checklists). These belong in the [operator](https://github.com/Cognition-Partner-Workshops/operator) repo. **Exception**: Technical comparisons that explain practical differences (e.g., "static analysis requires no environment changes, unlike runtime trace tools") are general guidance that belongs here — only the sales/delivery *framing* of those comparisons belongs in operator.

### Prompt Formatting (Medium Priority)

- **Fenced code blocks** — All paste-into-Devin prompts must use triple-backtick fenced code blocks so GitHub renders the copy button. Flag prompts in blockquote format (`> ...`).
- **No "Open a PR"** — Devin opens PRs by default. Flag explicit "Open a PR" instructions in prompts unless the task specifically requires non-default PR behavior.
- **Prompt specificity** — Prompts should include repo names, file paths, and expected output format. Flag vague prompts that lack concrete paths or deliverables.

### Structure (Medium Priority)

- **Table of Contents** — Modules and workshops with 3+ sections should have a TOC with anchor links at the top.
- **Quick Start** — Hands-on modules should have a "Quick Start" section near the top so experienced users can jump straight to the first prompt.
- **Anchor placement** — `<a id="..."></a>` anchors must be placed directly before the heading they target, not before a parent heading.

### General Themes Integration (Medium Priority)

- **Team-based resource** — Devin should be positioned as a team-based resource, not an individual user's tool. Flag language that implies single-user operation.
- **Automation narrative** — Modules should include discussion of webhook-driven automation, scheduled sessions, and child sessions for scale where applicable.
- **Shared context layer** — Knowledge notes, DeepWiki, MCP integrations, and playbooks should be referenced where relevant.

### Technical Accuracy (Medium Priority)

- **File paths must exist** — Every file path referenced in a prompt should exist on the referenced repo's main branch.
- **Repo descriptions must match reality** — File counts, directory structures, and artifact types must be accurate (e.g., don't say "92 macros" if the count has changed).
- **No assumed capabilities** — Don't claim Devin can do something (execute SAS, connect to a database) unless the workshop environment supports it.

## Files to Ignore

- `catalog/` — Repository inventory metadata, not workshop content
- `*.json` — Configuration files
- `events/archive/` — Historical records, lower priority for review

## Common Pitfalls

- Including facilitator/presales content in this repo (it belongs in operator)
- Using blockquote format for prompts instead of fenced code blocks (loses GitHub copy button)
- Including "Open a PR" in prompts
- Absolute statements about DeepWiki coverage or AI analysis completeness
- Customer-identifying information in event directories or content

## PR Conventions

- Do not identify the original user requester in PR descriptions
- Do not list user emails — this is a multi-tenant environment
- Use US English spelling (modernization, not modernisation)
- Branch naming: `devin/<timestamp>-<description>`
