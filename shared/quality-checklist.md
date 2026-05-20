# Workshop Content Quality Checklist

Use this checklist when authoring or reviewing modules, workshops, and event materials. Each item maps to a quality principle — not all will apply to every file, but all should be considered.

## Structure & Navigation

- [ ] **Table of Contents with anchor links** — Every module longer than 3 sections has a TOC at the top. Use `<a id="..."></a>` anchors so links work on GitHub
- [ ] **Quick Start / short circuit** — Readers who want to jump straight into hands-on work can skip background sections. Provide a "Quick Start" section near the top with the first paste-into-Devin prompt
- [ ] **Attendee vs. Facilitator separation** — The main module file is written for the end audience (the person with the problem). Facilitator-only content (MCP setup, presales positioning, timing guides) goes in a sibling `-facilitator.md` file
- [ ] **4-Step lab format** — where applicable, labs follow: Start → AskDevin → Explore (DeepWiki) → Review & Give Feedback

## Prompts & Code Blocks

- [ ] **Code fence formatting** — All paste-into-Devin prompts use triple-backtick fenced code blocks (` ``` `) so GitHub renders the copy button. Multi-line prompts should be readable (line-wrapped at ~80 chars) but still copy-pasteable
- [ ] **No "Open a PR" in prompts** — Devin opens PRs by default. Omit explicit "Open a PR" instructions unless the task specifically requires a non-default PR behavior
- [ ] **Prompt specificity** — Prompts include repo names, file paths, and expected output format. Vague prompts produce vague results

## Language & Tone

- [ ] **Participant-centered language** — Use "try", "hands-on", "walkthrough" — never "demo". Section headers say "What to Try" not "What to Demo"
- [ ] **"Key Takeaways" not "Key Talking Points"** — Summary bullets under each lab use this header
- [ ] **No overstatements or guarantees** — Avoid "every", "all", "always", "guaranteed", "comprehensive coverage" when describing probabilistic tools (DeepWiki, AI analysis). Use "typically", "in most cases", "coverage depends on repo structure"
- [ ] **No customer names** — Never mention customer names, venue names tied to customers, or identifying product names. Use `*(customer)*` as a placeholder
- [ ] **Event naming** — Use "workshop" not "arc" in user-facing content
- [ ] **US English spelling** — "modernization", "organization" (not "modernisation", "organisation")

## Privacy & PII

- [ ] **No customer names** — replace organization names, venue names tied to customers, and customer-specific product names with `*(customer)*`
- [ ] **No participant PII** — do not include participant email addresses, full names of non-Cognition staff, or identifying metadata in committed files
- [ ] **No credentials in plaintext** — use placeholders like `<TOKEN>`, `${DB_PASSWORD}`, or reference the event site for credential distribution

## Content Quality

- [ ] **Target Outcomes** — every lab must list concrete, verifiable deliverables
- [ ] **Key Takeaways** — every lab should end with 2–4 concise takeaway bullets

## General Themes Integration

Every module should incorporate applicable principles from `shared/general-themes/`. Not every theme applies to every module — use judgment.

- [ ] **When to use Devin** (`when-to-use-devin.md`) — Is the module's use case positioned as event-driven, large-scale, or capacity-constrained work? Does it explain why Devin is suited for this type of task?
- [ ] **Architecture strengths** (`architecture-strengths.md`) — Does the module reference clean-room execution, the shared context layer, or context retrieval where relevant?
- [ ] **Design patterns** (`design-patterns-for-devin.md`) — Does the module demonstrate applicable patterns? Key ones:
  - Locally buildable and testable code
  - Event-driven triggers (webhooks → Devin sessions)
  - Divide and conquer with child agents
  - Human-in-the-loop via PR feedback
  - Context layer configuration
- [ ] **Platform capabilities** (`platform-capabilities.md`) — Does the module reference applicable platform features?
  - Scheduled sessions for recurring work
  - Playbooks for repeatable methodology
  - Child agents for parallel execution
  - Team-based operation (shared config, multi-user collaboration)
  - Devin Review for PR quality
- [ ] **Collaboration model** (`collaboration-model.md`) — Does the module show the PR feedback loop, multi-user interaction, CI monitoring, or hibernation/resume?
- [ ] **Value narratives** (`value-narratives.md`) — Are the takeaways framed in terms of capacity unlocking, velocity, quality, risk reduction, or cost optimization?

## Automation & Scale Narrative

- [ ] **Webhooks and event-driven automation** — Does the module explain how the task could be triggered automatically (CI failure → Devin session, ticket tagged → Devin implements, schedule → recurring analysis)?
- [ ] **Divide and conquer** — For tasks that apply to multiple targets, does the module show how child sessions parallelize the work?
- [ ] **Scheduled sessions** — Could this task run on a recurring schedule? If so, mention it
- [ ] **Team-based operation** — Does the module position Devin as a shared team resource, not an individual tool? Multiple reviewers on the same PR, shared Knowledge notes, org-level playbooks

## Event Template Compliance

New events should start from [`events/_template/README.md`](../events/_template/README.md) and include:

1. Event Details table (date, location, host, duration, facilitator, event site)
2. Table of Contents
3. Workshop overview or abstract
4. Lab sections with prompts in fenced code blocks
5. Target Outcomes per lab
6. Key Takeaways per lab
7. Repos Required on Devin's Machine checklist
8. Devin Features Checklist reference

## Facilitator Companion

For events that need facilitator-specific guidance beyond the main README, create a `FACILITATOR_COMPANION.md` in the event directory with:

- Pacing notes per lab
- Common pitfalls and recovery steps
- Suggested talking points for transitions
- Backup plans if primary repos are unavailable

Reference the [Facilitator Guide](facilitator-guide.md) for the universal pre-event, day-of, and post-event checklists.

## Third-Party Tool References

- [ ] **Neutral positioning** — When referencing third-party tools (Alchemist, SonarQube, etc.), position them as complementary options, not direct competitors. Devin can work with these tools, not replace them
- [ ] **Generic where possible** — If the module references a specific tool category (SAST scanner, ETL platform, scheduling system), mention 2–3 examples or use a generic term rather than singling one out

## Technical Accuracy

- [ ] **Repo contents match descriptions** — Verify file counts, directory structures, and artifact types against the actual repo. Don't say "92 macros" if there are 87
- [ ] **Prompts reference real paths** — Every file path in a prompt exists in the repo's main branch
- [ ] **No assumed capabilities** — Don't claim Devin can do something (execute SAS, connect to a database) unless the workshop environment actually supports it
