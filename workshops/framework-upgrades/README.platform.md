# Workshop: Framework Upgrades

**Other variants:** [Cloud only](README.md)

## Overview

| | |
|---|---|
| **Focus** | Angular and Spring Boot version upgrades across multiple repositories at scale |
| **Duration** | 1-2 hours |
| **Audience** | Development teams, tech leads, enterprise platform teams |
| **Key Modules** | [Framework Upgrade](../../modules/migration-modernization/framework-upgrade.md), [Repetitive Framework Upgrades](../../modules/migration-modernization/repetitive-framework-upgrades.md) |

## Platform Context

This workshop uses **Devin Desktop + Devin Cloud**. You will use Desktop as your primary interface — exploring code locally, delegating tasks to Cloud, and reviewing results without leaving the editor. Cloud agents handle the heavy lifting (upgrades, builds, tests) while you stay in your IDE.

> **Tip:** The same labs are available as a [Cloud-only variant](README.md) for participants using the Devin web app directly. You can also explore code with **Devin CLI** (`cli.devin.ai`) from your terminal — it plugs into Desktop via ACP as a "Devin Local" agent.

## Workshop Narrative

Framework upgrades are one of the most common and repetitive tasks in enterprise software. This workshop demonstrates how Devin handles the same upgrade pattern applied consistently across different services — and how the Desktop + Cloud workflow lets you manage parallel upgrade sessions from a single Kanban board without leaving your editor.

## Getting the Most from This Workshop

> **Desktop is your command center.** You will delegate upgrade tasks to Devin Cloud from within Desktop, monitor progress in the Agent Command Center, and review PRs with one-click checkout — no browser switching required.

A few tips to maximize your hands-on time:

- **Create a Space for this workshop.** Spaces group sessions, PRs, files, and context for a task. New sessions in the Space inherit project context automatically.
- **Use Cascade or Devin Local for exploration.** Before delegating to Cloud, use a local agent to explore the codebase and refine requirements — fast iteration without spinning up a cloud VM.
- **Delegate to Cloud for implementation.** Once requirements are clear, hand the task to Devin Cloud. Continue coding locally while Cloud works in the background.
- **Monitor from the Agent Command Center.** The Kanban board shows all your agents (local and cloud) organized by status — see at a glance what is in flight, what is blocked, and what is ready for review.
- **Review PRs with one-click checkout.** When Devin Cloud opens a PR, review it directly in your editor. No manual `git fetch` or branch switching.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Leave PR comments to steer Devin.** After Devin opens a PR, leave comments and Devin will wake up and address them — this is the core feedback loop.
- **Try multiple agents.** Desktop supports ACP-compatible agents (Devin Local, Cascade, Codex CLI, Claude Agent, Gemini CLI, and more). Mix and match for different tasks.

## Table of Contents

- [Lab 1 — Spring Boot + Angular Parallel Upgrades](#lab-1--spring-boot--angular-parallel-upgrades)
- [Repos Required](#repos-required)
- [Key Takeaways](#key-takeaways)

---

## Labs

### Lab 1 — Spring Boot + Angular Parallel Upgrades

- **Modules:** [Framework Upgrade](../../modules/migration-modernization/framework-upgrade.md) + [Repetitive Framework Upgrades](../../modules/migration-modernization/repetitive-framework-upgrades.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot 2.6.3 → 3.x
  - [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular) — Angular version upgrade
  - [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld) — Angular version upgrade (parallel comparison)
- **Objective:** Run parallel Devin Cloud sessions upgrading Angular and Spring Boot across multiple repos — managed from Desktop
- **Duration:** 60 min

#### Step 1: Set Up Your Workspace

1. **Create a Space** in Devin Desktop for this workshop. Name it something like "Framework Upgrades" — this groups all sessions, PRs, and files in one view.
2. Open the workshop repositories in your Space so context carries across sessions.

#### Step 2: Explore the Codebases Locally

Use **Cascade** or **Devin Local** in Desktop to explore the upgrade targets before delegating to Cloud. This is faster than spinning up a cloud session for research.

Try asking your local agent:

- *"What are the biggest risks when upgrading from Spring Boot 2 to 3?"*
- *"What Angular version is petclinic-angular currently on? What breaking changes are expected?"*
- *"Compare the upgrade paths for both Angular repos — are the same breaking changes expected?"*

You can also open each repo's DeepWiki page to review build configuration, dependency graphs, and deprecated patterns.

> **ACP note:** Devin Local speaks the Agent Client Protocol, so it runs inside Desktop alongside any other ACP-compatible agent. You can switch between agents mid-task — for example, use Devin Local for code exploration, then delegate to Cloud for the upgrade.

#### Step 3: Delegate Upgrades to Devin Cloud

From Desktop, delegate the implementation tasks to **Devin Cloud**. Each task runs on its own cloud VM — you can kick off both in parallel.

**Session A — Spring Boot:**
```
Upgrade uc-spring-boot-upgrade-microservice-extraction from
Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2.
Handle the javax to jakarta namespace migration, update
Gradle build configuration, fix any deprecations, and
ensure all tests pass. Document every breaking change.
```

**Session B — Angular:**
```
Upgrade petclinic-angular to the latest Angular version.
Handle breaking changes from the Angular update guide,
update all dependencies, fix deprecated APIs, and ensure
the app builds. Document every breaking change.
```

Continue working locally while Cloud handles both upgrades in the background.

#### Step 4: Monitor Progress in the Agent Command Center

Open the **Agent Command Center** (Kanban view) in Desktop. Both cloud sessions appear as cards — you can see their status at a glance:

- **In Progress** — the agent is actively working
- **Waiting for Review** — the agent has opened a PR and is waiting for feedback
- **Blocked** — the agent needs input (check the session for details)

No need to switch browser tabs or check the Devin web app — the Kanban board shows all sessions in one place.

#### Step 5: Review PRs with One-Click Checkout

When Devin Cloud opens a PR, it appears in your Space. Click **checkout** to load the branch directly in your editor — no manual `git fetch` or branch switching.

- Compare the Spring Boot and Angular upgrade PRs side-by-side in your editor
- Leave inline comments on the PR; Devin Cloud wakes up and addresses them
- Ask Devin to generate a shared upgrade checklist or repeatable runbook

**Target Outcomes:** Upgraded builds with passing tests, upgrade documentation, repeatable runbook

## Repos Required

- [ ] uc-spring-boot-upgrade-microservice-extraction
- [ ] petclinic-angular
- [ ] ts-angular-realworld (optional, for parallel comparison)

## Key Takeaways

- **"Same prompt, multiple repos"** — the same upgrade task applied consistently across repos demonstrates enterprise scale
- **"Parallel sessions save calendar time"** — upgrades that would take weeks sequentially can run simultaneously
- **"Desktop as the command center"** — the Agent Command Center gives you a single view of all running agents, local and cloud, without leaving your editor
- **"Seamless local-to-cloud handoff"** — explore locally with Cascade or Devin Local, then delegate to Cloud for autonomous execution
- **"One-click PR review"** — review cloud-generated PRs directly in the editor, no browser switching or manual git operations
- **"Consistency across upgrades"** — Devin applies the same patterns and catches the same breaking changes
