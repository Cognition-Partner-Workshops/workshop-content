# Workshop Track: Solutions

## Overview

| | |
|---|---|
| **Focus** | Platform architecture, SDLC integration design, live walkthrough delivery, automation topology, and full platform configuration — everything a Solutions Engineer or Architect needs to design and present Devin in client-facing engagements |
| **Duration** | 8-10 hours (self-paced across multiple sessions) |
| **Audience** | Solutions Engineers and Architects at SI partner firms who design systems and operational processes within the SDLC, and who need to present Devin's full capabilities in client-facing simulated environments |
| **Format** | Reading + knowledge checks + design challenges + live walkthrough practice |
| **Prerequisite** | [Foundations workshop](../../foundations/) (Level 1) |

## Workshop Narrative

This track transforms you from someone who understands Devin's capabilities (Foundations) into someone who can **design** Devin integrations for client environments and **deliver** live walkthroughs that demonstrate real value. Solutions Engineers are the bridge between platform capability and client adoption — you must know the architecture cold when fielding questions, map Devin's patterns to the client's SDLC, and recover gracefully when things go off-script.

The first half covers **knowledge** — platform architecture internals, SDLC phase mapping, and automation topology design. The second half covers **execution** — running live walkthroughs end-to-end, configuring a complete Devin organization from scratch, and handling unexpected outcomes during client-facing sessions.

## Getting the Most from This Workshop

> **This is a design-and-delivery workshop.** Unlike hands-on labs where you paste prompts and review PRs, this workshop builds the knowledge and judgment you need to architect Devin solutions and present them convincingly. Each section ends with knowledge checks and design exercises — take them seriously, as they mirror the questions clients will ask.

Tips:
- **Read the source material first.** Each section is self-contained comprehensive training. Quick-reference cheat sheets in `reference/general-themes/` and walkthrough scripts in `demos/` supplement the material. Read those before attempting the knowledge checks.
- **Practice walkthrough delivery out loud.** Section 3 includes delivery scripts. Practice explaining what is happening while Devin runs — this is the core skill clients evaluate you on.
- **Build the simulated environment.** Section 5 walks you through configuring a complete Devin org. Do this hands-on — the muscle memory matters when you are configuring a client's environment live.
- **Use the design challenges as preparation.** The capstone design challenge mirrors the kind of architecture proposal you will deliver in real engagements.

---

<a id="toc"></a>
## Table of Contents

### Part 1: Knowledge

| # | Topic | What You'll Learn |
|---|-------|-------------------|
| 1 | [Platform Architecture Mastery](01-platform-architecture-mastery.md) | Clean-room execution, shared context layer, session lifecycle, verification model — know it cold for client questions |
| 2 | [SDLC Integration Design](02-sdlc-integration-design.md) | Where Devin fits in the development lifecycle: event-driven triggers, scheduled maintenance, PR-based delivery |
| 3 | [Live Walkthrough Delivery](03-live-walkthrough-delivery.md) | How to run each walkthrough type end-to-end (security remediation, migration, data engineering, feature development) in a simulated environment |

### Part 2: Execution

| # | Topic | What You'll Learn |
|---|-------|-------------------|
| 4 | [Automation Topology Design](04-automation-topology-design.md) | Given client requirements, design the webhook/schedule/trigger architecture |
| 5 | [Full Platform Configuration](05-full-platform-configuration.md) | Configure a complete Devin org from scratch: blueprints, Knowledge, Playbooks, MCP servers, Secrets, Git connections, Automations |
| 6 | [Handling Unexpected Outcomes](06-handling-unexpected-outcomes.md) | Recovery patterns when the agent gets stuck, CI fails, or the walkthrough goes off-script |

### Capstone

| # | Topic | What You'll Learn |
|---|-------|-------------------|
| 7 | [Design Challenge](07-design-challenge.md) | End-to-end engagement architecture — design the full Devin integration for a multi-initiative client |
| 8 | [Self-Assessment Rubric](08-self-assessment-rubric.md) | Criteria to evaluate your own walkthrough delivery quality |

---

## Gaps & Future Content

Additional materials needed to complete this track:

- [ ] Recorded reference walkthroughs (video of ideal delivery for each walkthrough type — security, migration, data engineering, feature development)
- [ ] Simulated environment setup guide (step-by-step instructions to configure a workshop org for live practice)
- [ ] Integration design templates (editable topology diagrams for common automation patterns)
- [ ] "Handling the hard questions" expanded guide (top 50 client questions with researched, precise answers)
- [ ] Platform configuration lab environment (pre-configured sandbox where attendees practice org setup from scratch)
- [ ] Walkthrough timing guides — *operator repo* (suggested pacing for 30-minute, 60-minute, and 90-minute walkthrough slots)
- [ ] Objection handling reference — *operator repo* (how to respond to questions about alternative approaches)
- [ ] Multi-walkthrough sequencing guide (how to chain walkthroughs in a half-day or full-day session for maximum narrative impact)
- [ ] Assessment exercises with automated grading (knowledge checks that report scores and identify weak areas)
- [ ] Client-specific customization templates (how to adapt walkthrough prompts to a specific client's repos and technology stack)
