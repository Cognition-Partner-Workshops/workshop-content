# Workshop: Engineering Track

## Overview

| | |
|---|---|
| **Focus** | Tactical platform usage, prompt engineering, orchestration patterns, and troubleshooting — everything a delivery engineer needs to be an effective Devin operator |
| **Duration** | 6-8 hours (self-paced; sections are independent after Platform Fundamentals) |
| **Audience** | Engineers and developers at SI partner firms who use Devin to execute their already-defined jobs |
| **Prerequisite** | [Foundations workshop](../../foundations/) (Level 1) |
| **Format** | Reading + knowledge checks + challenge-mode labs (write your own prompts — no copy-paste) + debug exercises |

## Workshop Narrative

You have completed the Foundations workshop and understand the theory: cloud vs. local agents, event-driven patterns, orchestration models, and the framework for deciding what to give AI. This workshop makes it operational.

The Engineering track is built for the practitioner — the person who opens Devin sessions every day, writes prompts, configures environments, reviews PRs, and troubleshoots when things go sideways. By the end, you will know how to:

- Write prompts that produce high-quality output on the first attempt
- Choose the right host OS, blueprint configuration, and network connectivity pattern for any project
- Select the correct orchestration pattern (single agent, parent-child, event-driven, scheduled) for any task
- Review AI-generated code effectively and give feedback that Devin can act on
- Diagnose and resolve common failure modes without starting over

## Getting the Most from This Workshop

> **This is a practice-oriented workshop.** Each section includes knowledge checks to test your understanding and ends with guidance you can apply immediately. The Challenge-Mode Labs at the end require you to write your own prompts from scratch — no copy-paste provided.

Tips:
- **Read Platform Fundamentals first.** It establishes the session lifecycle and feedback loop that every other section builds on.
- **After that, jump to what matters most for your current project.** If you are configuring a new environment, start with Section 3. If you are writing prompts daily, start with Section 2.
- **Try the knowledge checks honestly.** They are designed to surface gaps in understanding before you hit them in production.
- **Use the Challenge-Mode Labs as a self-assessment.** If you can write a complete, well-structured prompt for each scenario, you are ready to operate Devin effectively on real engagements.

---

<a id="toc"></a>
## Table of Contents

| # | Section | What You'll Learn |
|---|---------|-------------------|
| 1 | [Platform Fundamentals](01-platform-fundamentals.md) | Session lifecycle, cloud vs. local decision tree, PR feedback loop, verification model |
| 2 | [Prompt Engineering for Agents](02-prompt-engineering.md) | Components of a good prompt, progressive examples, common anti-patterns |
| 3 | [Environment & Architecture Decisions](03-environment-architecture.md) | Host OS selection, blueprints, network connectivity, secrets, MCP servers |
| 4 | [The Task Selection Framework](04-task-selection-framework.md) | Verifiability test, clarity test, judgment test, gray-zone decomposition |
| 5 | [Orchestration Patterns in Practice](05-orchestration-patterns.md) | Single agent, parent-child, event-driven, scheduled — when and how |
| 6 | [Working with Agent Output](06-working-with-agent-output.md) | PR review techniques, effective feedback, iteration patterns |
| 7 | [Tactical Tips & Troubleshooting](07-tactical-tips-troubleshooting.md) | Common failure modes, Knowledge notes, Playbook design |
| — | [Challenge-Mode Labs](08-challenge-labs.md) | Write your own prompts for real-world scenarios |
| — | [Debug Exercises](09-debug-exercises.md) | Diagnose what went wrong in failing sessions |
| — | [Gaps & Future Content](10-gaps-future-content.md) | What additional materials are needed |

---

## After This Workshop

Once you have built the tactical skills covered here, you are ready for domain-specific deep dives:

| Next Step | What You'll Do |
|-----------|---------------|
| [General Workshop](../../general/) | Hands-on labs across security, modernization, and feature dev — apply what you learned here |
| [By Technical Domain](../../by-tech-domain/) | Deep dives into COBOL modernization, .NET cloud-native, framework upgrades, data migration |
| [By Technical Role](../../by-tech-role/) | Role-specific lab sequences for AppDev, Digital Engineering, QA |

These workshops assume you have the platform fundamentals, prompt engineering skills, and troubleshooting techniques covered in this track.
