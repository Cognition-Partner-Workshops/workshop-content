# Workshop: Foundations

## Overview

| | |
|---|---|
| **Focus** | Understanding how cloud AI agents work, when to use them, and how Devin's platform turns theory into practice |
| **Duration** | 2-3 hours (self-paced reading + light exploration) |
| **Audience** | Engineering teams, technical leaders, and anyone new to autonomous AI agents who wants to build intuition before diving into hands-on labs |
| **Format** | Guided reading with product exploration activities — no coding required |

## Workshop Narrative

This workshop builds the mental model you need before putting an AI agent to work. Most engineers are familiar with local AI assistants — code completion, inline chat, IDE copilots. A cloud-based autonomous agent is a fundamentally different paradigm: you delegate entire tasks, not keystrokes.

The first half covers **theory** — when a cloud agent is the right tool, how event-driven automation replaces human paging, how agents fit into your team as a workforce, and how to distinguish tasks that belong to AI from those that don't.

The second half covers **product** — the specific Devin features that make these concepts operational: how DeepWiki gives agents codebase understanding, how sessions execute work, how the CLI bridges local and cloud workflows, how automations create event-driven triggers, and how multi-agent orchestration scales work across many targets.

## Getting the Most from This Workshop

> **This is a reading-first workshop.** Unlike other workshops where you paste prompts and review PRs, this one is about building understanding. Each section has a short exploration activity — try it, but don't feel pressured to complete every one before moving on.

Tips:
- **Read in order.** The theory sections build on each other: cloud vs. local → event-driven → orchestration → task selection. The product sections then show how the theory maps to real features.
- **Try the exploration activities.** Each section ends with a lightweight activity: exploring a DeepWiki page, browsing the automations UI, or asking Devin a question. These are optional but help cement the concepts.
- **Connect back to your own work.** As you read, think about which patterns apply to your team's backlog, your current pain points, and the work that never gets prioritized.

---

<a id="toc"></a>
## Table of Contents

### Part 1: Theory

| # | Topic | What You'll Learn |
|---|-------|-------------------|
| 1 | [Cloud Agent vs. Local Agent](01-cloud-agent-vs-local-agent.md) | Why delegating entire tasks to an autonomous cloud agent is fundamentally different from local AI assistance |
| 2 | [Event-Driven Reactions](02-event-driven-reactions.md) | Why automated triggers produce better outcomes than humans getting paged |
| 3 | [Agent Orchestration](03-agent-orchestration.md) | How agents fit into your team as a workforce — distributing work intelligently |
| 4 | [What to Give AI vs. Not](04-what-to-give-ai.md) | A framework for identifying tasks that belong to an AI agent vs. those that don't |

### Part 2: Product Features

| # | Topic | What You'll Learn |
|---|-------|-------------------|
| 5 | [DeepWiki](05-deepwiki.md) | How source code is distilled into consumable wiki pages that give the agent codebase understanding |
| 6 | [Devin Sessions](06-devin-sessions.md) | The worker model — how tasks are received, executed, and delivered |
| 7 | [Devin CLI](07-devin-cli.md) | The terminal interface that bridges local development with cloud delegation |
| 8 | [Automations](08-automations.md) | Triggers, scheduled routines, and event-driven workflows |
| 9 | [Multi-Agent Workers](09-multi-agent-workers.md) | Breaking large tasks into subunits distributed across parallel workers |

---

## After This Workshop

Once you've built this foundation, you're ready for any of the hands-on technical workshops:

| Next Step | What You'll Do |
|-----------|---------------|
| [General Workshop](../general/) | Security, modernization, and feature development labs |
| [Application Development & Maintenance](../specialties/job-families/development/app-maintenance/) | Build features, fix bugs, iterate on PRs |
| [Enterprise Security Automation](../specialties/technical-domains/security/enterprise-automation/) | Event-driven SAST and mass remediation |
| [Digital Engineering](../specialties/job-families/digital-engineering/) | CI/CD, cloud infrastructure, observability |

These workshops assume you understand the concepts covered here — cloud delegation, event-driven patterns, the PR feedback loop, and how Devin's context layer works.
