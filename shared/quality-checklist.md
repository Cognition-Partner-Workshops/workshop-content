# Quality Checklist

Standards for workshop content across events, modules, and shared materials. Reference this checklist when creating or reviewing any workshop file.

## Language & Tone

| Rule | Correct | Incorrect |
|------|---------|-----------|
| Use participant-centered language | "try", "hands-on", "walkthrough" | "demo", "demonstration" |
| Section header for summaries | **Key Takeaways** | ~~Key Talking Points~~ |
| Event naming | "workshop" | "arc" |
| Soften absolute claims | "Devin typically handles…", "In most cases…" | "Devin always…", "Devin will…" (when outcome varies) |
| US English spelling | "modernization", "organization" | "modernisation", "organisation" |

## Structure

- **Table of Contents** — every event README must include a ToC after the Event Details table.
- **Prompts in fenced code blocks** — all "Paste into Devin" prompts must use fenced code blocks (` ``` `), not blockquotes (`>`). Blockquotes are reserved for informational callouts and abstracts.
- **No "Open a PR" in prompts** — Devin opens PRs by default; including the instruction adds noise. Remove `Open a PR` sentences from prompt blocks.
- **4-Step lab format** — where applicable, labs follow: Start → AskDevin → Explore (DeepWiki) → Review & Give Feedback.

## Privacy & PII

- **No customer names** — replace organization names, venue names tied to customers, and customer-specific product names with `*(customer)*`.
- **No participant PII** — do not include participant email addresses, full names of non-Cognition staff, or identifying metadata in committed files.
- **No credentials in plaintext** — use placeholders like `<TOKEN>`, `${DB_PASSWORD}`, or reference the event site for credential distribution.

## Content Quality

- **Weave in general themes** — reference documents in [`shared/general-themes/`](general-themes/) where they reinforce a lab's narrative (e.g., link to [collaboration-model.md](general-themes/collaboration-model.md) when discussing the PR feedback loop, or [platform-capabilities.md](general-themes/platform-capabilities.md) when introducing parallel sessions).
- **Avoid overstatements** — claims about Devin's capabilities should be grounded. Use hedged language when outcomes depend on codebase complexity or prompt specificity.
- **Target Outcomes** — every lab must list concrete, verifiable deliverables.
- **Key Takeaways** — every lab should end with 2–4 concise takeaway bullets.

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
