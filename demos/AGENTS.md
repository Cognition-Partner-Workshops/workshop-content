# AGENTS.md — `demos/`

Conventions for content in this directory. These **extend and override** the
root `AGENTS.md` for files under `demos/` only. (See also the root `REVIEW.md`
`demos/` rules.)

## What `demos/` is

`demos/` holds **facilitator-led demo showcases**: a presenter runs the demo
live while others follow along. This is different from `labs/` and
`workshops/`, which are participant-driven and may branch into separate
choose-your-own-adventure tracks.

A demo doc is a **single linear thread** — one path, start to finish. It should
read as though a user is reading and following along.

## Rules for `demos/` content

- **"demo" verbiage is allowed here** (file names, titles, headers, body). It is
  permitted **only** under `demos/`; everywhere else in the repo still uses
  "try" / "hands-on" / "walkthrough".
- **Read as a user following along.** Lead straight into the guide with prompts
  and user instructions. Avoid internally-focused preamble (presales framing,
  facilitator logistics). However, architecture context that the reader needs to
  understand the prompts (e.g., how Playbooks/Skills/Automations relate) is
  acceptable before the first executable step — especially when paired with the
  required TOC and Quick Start sections.
- **No participant "try this" framing** and no choose-your-own-adventure
  branching. The reader is following one thread, not selecting a track.
- **Summaries use "Key Takeaways"** (not "Key Talking Points").
- **Not internal facilitator notes.** Avoid pacing/timing/presales framing and
  narration cues ("narrate…", "call out…"). Write the steps so a user can
  execute them directly. (Day-of logistics still live in the
  [operator](https://github.com/Cognition-Partner-Workshops/operator) repo.)

## Inherited from root `AGENTS.md`

Still applies under `demos/`:

- All paste-into-Devin prompts use triple-backtick fenced code blocks; do not
  include "Open a PR" in prompts.
- No customer-identifying content; use generic placeholders.
- Do not identify the user requester in PR descriptions or commit messages.
- US English spelling. Verify file paths and counts against the actual repos.
- Docs longer than 3 sections get a Table of Contents with `<a id="..."></a>`
  anchors.
