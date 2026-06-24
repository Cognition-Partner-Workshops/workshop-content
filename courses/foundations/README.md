# Workshop: Foundations

## Overview

| | |
|---|---|
| **Focus** | Understanding how cloud AI agents work, when to use them, and how Devin's platform turns concepts into practice |
| **Duration** | 2-3 hours (self-paced reading + light exploration) |
| **Audience** | Engineering teams, technical leaders, and anyone new to autonomous AI agents who wants to build intuition before diving into hands-on labs |
| **Format** | Guided reading with product exploration activities — no coding required |

## Workshop Narrative

This workshop builds the mental model you need before putting an AI agent to work. Most engineers are familiar with local AI assistants — code completion, inline chat, IDE copilots. A cloud-based autonomous agent is a fundamentally different paradigm: you delegate entire tasks, not keystrokes.

The first half covers **concepts** — when a cloud agent is the right tool, how event-driven automation replaces human paging, how agents fit into your team as a workforce, and how to distinguish tasks that belong to AI from those that don't.

The second half covers **product** — the specific Devin features that make these concepts operational, organized by deployment model: cloud (autonomous agents on their own VMs) and local (in your editor or terminal).

## Getting the Most from This Workshop

> **This is a reading-first workshop.** Unlike other workshops where you paste prompts and review PRs, this one is about building understanding. Each section has a short exploration activity — try it, but don't feel pressured to complete every one before moving on.

Tips:
- **Read concepts first.** They build on each other: cloud vs. local → event-driven → orchestration → task selection. The product sections then show how the concepts map to real features.
- **Try the exploration activities.** Each section ends with a lightweight activity: exploring a DeepWiki page, browsing the automations UI, or asking Devin a question. These are optional but help cement the concepts.
- **Connect back to your own work.** As you read, think about which patterns apply to your team's backlog, your current pain points, and the work that never gets prioritized.

---

<a id="toc"></a>
## Table of Contents

### Part 1: [Concepts](concepts/)

| # | Topic | What You'll Learn |
|---|-------|-------------------|
| 1 | [Cloud Agent vs. Local Agent](concepts/01-cloud-agent-vs-local-agent.md) | Why delegating entire tasks to an autonomous cloud agent is fundamentally different from local AI assistance |
| 2 | [Event-Driven Reactions](concepts/02-event-driven-reactions.md) | Why automated triggers produce better outcomes than humans getting paged |
| 3 | [Agent Orchestration](concepts/03-agent-orchestration.md) | How agents fit into your team as a workforce — distributing work intelligently |
| 4 | [What to Give AI vs. Not](concepts/04-what-to-give-ai.md) | A framework for identifying tasks that belong to an AI agent vs. those that don't |

### Part 2: [Product Features](product/)

#### Cloud

| # | Topic | What You'll Learn |
|---|-------|-------------------|
| — | [Devin Cloud Feature Tour](product/cloud/devin-cloud.md) | Comprehensive overview — execution model, sessions, orchestration, integrations |
| 5 | [DeepWiki](product/cloud/05-deepwiki.md) | How source code is distilled into consumable wiki pages that give the agent codebase understanding |
| 6 | [Devin Sessions](product/cloud/06-devin-sessions.md) | The worker model — how tasks are received, executed, and delivered |
| 8 | [Automations](product/cloud/08-automations.md) | Triggers, scheduled routines, and event-driven workflows |
| 9 | [Multi-Agent Workers](product/cloud/09-multi-agent-workers.md) | Breaking large tasks into subunits distributed across parallel workers |

#### Local

| # | Topic | What You'll Learn |
|---|-------|-------------------|
| — | [Devin Desktop Feature Tour](product/local/devin-desktop.md) | AI-powered IDE (Windsurf) — Devin Local, autocomplete, cloud delegation |
| 7 | [Devin CLI](product/local/07-devin-cli.md) | The terminal interface that bridges local development with cloud delegation |
| — | [Devin CLI Feature Tour](product/local/devin-cli-tour.md) | Comprehensive CLI reference — permission modes, agent modes, session management |

---

## After This Workshop

Once you've built this foundation, the next step depends on your role:

| Next Step | Who It's For | What You'll Do |
|-----------|-------------|---------------|
| [Sales Track](../tracks/sales/) | Sales Engineers, AEs, Practice Leads | Operating model, economics, engagement scoping |
| [Solutions Track](../tracks/solutions/) | Solutions Engineers, Architects | Platform architecture, SDLC design, live walkthroughs |
| [Engineering Track](../tracks/engineering/) | Engineers, Developers | Prompt engineering, orchestration, troubleshooting |

After completing your track, dive into hands-on workshops:

| Next Step | What You'll Do |
|-----------|---------------|
| [General Workshop](../../workshops/general/) | Security, modernization, and feature development labs |
| [By Technical Domain](../../workshops/by-tech-domain/) | Deep dives into modernization, security, AI & automation |
| [By Technical Role](../../workshops/by-tech-role/) | Role-specific lab sequences for development, digital engineering, QE |

These workshops assume you understand the concepts covered here — cloud delegation, event-driven patterns, the PR feedback loop, and how Devin's context layer works.
