# Devin Review Guidelines — workshop-metadata

## Repository Context

This repo contains **attendee-facing** workshop content (labs, workshops, events) and shared reference materials for the Cognition-Partner-Workshops organization. All content is Markdown. There is no application code — PRs contain documentation, prompts, and instructional materials. Facilitator-facing content (day-of guides, quality checklist, event templates, module facilitator notes) lives in the [operator](https://github.com/Cognition-Partner-Workshops/operator) repo.

**Exception — `demos/`:** the `demos/` directory holds facilitator-led demo showcases: a single linear thread a presenter runs live while others follow along. Unlike `labs/`/`workshops/` (participant-driven, can branch into choose-your-own-adventure tracks), demo docs walk one path start to finish and read as though a user is reading and following along. They are permitted to live in this repo and to use "demo" verbiage. Apply the demo-specific guidance below to anything under `demos/`.

## Priority Review Areas

### Language and Tone (High Priority)

- **"demo" only under `demos/`** — Flag any use of "demo" in `labs/`, `workshops/`, or `events/` content; the correct terms there are "try", "hands-on", or "walkthrough" (headers say "What to Try" not "What to Demo"). **Do not flag "demo" verbiage in files under the `demos/` directory** — it is expected there.
- **"Key Takeaways" not "Key Talking Points"** — Summary bullets under each lab/activity must use "Key Takeaways" as the header.
- **"Workshops" not "arcs"** — User-facing names must say "workshop" not "arc". Internal structural references to arcs are fine.
- **No overstatements** — Flag guarantee language ("every", "all", "always", "guaranteed", "comprehensive coverage") when describing probabilistic tools like DeepWiki or AI analysis. Prefer "typically", "in most cases", "coverage depends on repo structure".
- **No customer names** — Flag any customer names, venue names tied to customers, product names that identify a customer, or any identifying references. These must be replaced with generic placeholders like "*(customer)*".

### Content Separation (High Priority)

- **Attendee-only content** — This entire repo is for attendees. Flag any facilitator-only content (MCP setup, presales positioning, timing guides, pacing tips for facilitators, post-event checklists). These belong in the [operator](https://github.com/Cognition-Partner-Workshops/operator) repo. **Exception**: Technical comparisons that explain practical differences (e.g., "static analysis requires no environment changes, unlike runtime trace tools") are general guidance that belongs here — only the sales/delivery *framing* of those comparisons belongs in operator.
- **`demos/` content** — Files under `demos/` are facilitator-led demo showcases and are allowed in this repo. They should read as a single linear thread a user can follow along with: minimal preamble, straight into prompts and user instructions, no participant-driven "try this" hands-on framing or choose-your-own-adventure branching. Summary sections still use "Key Takeaways". Flag demo docs that bury the steps under heavy preamble or read as internal facilitator-only notes (pacing/timing/presales) rather than a user-followable walkthrough.

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

### Product Naming (Medium Priority)

- **Devin Local, not Cascade** — Devin Local is the successor to Cascade in Devin Desktop, with an improved model harness and subagent support. Cascade still exists but is on a deprecation path. Flag any use of "Cascade" as the current product name. Acceptable: "Devin Local (successor to Cascade)" for first-mention context to orient readers familiar with the older product.
- **AGENTS.md, not Rules** — Devin Desktop's always-on instructions mechanism should be described as `AGENTS.md`, not "Rules." The `AGENTS.md` convention is shared across Cloud, Desktop, and CLI. Flag references to "Rules" as a distinct feature when `AGENTS.md` is the correct term.

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
