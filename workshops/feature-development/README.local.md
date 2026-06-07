# Workshop: Feature Development (CLI / Devin Local)

**Other variants:** [Cloud only](README.md) | [Desktop + Cloud](README.platform.md)

## Overview

| | |
|---|---|
| **Focus** | Building new features on existing applications — requirements through implementation and testing |
| **Duration** | 1-2 hours |
| **Audience** | Full-stack developers, product engineers, development teams |
| **Key Modules** | [Gather Requirements](../../modules/application-development/gather-requirements.md), [Test-Driven Development](../../modules/application-development/test-driven-development.md), [New Feature Development](../../modules/application-development/new-feature-development.md), [API Documentation](../../modules/technical-documentation/api-documentation.md), [Database Schema Evolution](../../modules/application-development/database-schema-evolution.md) |

## Platform Context

This workshop uses **Devin CLI / Devin Local**. You will run `devin` in your terminal, provide prompts interactively, and use subagents for parallel subtasks — working directly with your local files and dev environment.

> **Tip:** Devin CLI is also consumable as "Devin Local" inside Devin Desktop via ACP. If you prefer a visual agent management experience alongside your terminal work, try the [Desktop + Cloud variant](README.platform.md).

## Workshop Narrative

Feature development is the most common daily activity for development teams. This workshop demonstrates how Devin analyzes existing code patterns, implements full-stack changes, and creates tests — all from your terminal. You will use Devin CLI for code exploration and implementation, leveraging shell integration for context-aware assistance and subagents for parallel subtasks.

## Getting the Most from This Workshop

> **Devin CLI works locally in your terminal.** It has access to your local files, git repos, and dev environment. Changes happen on your machine — fast, interactive, and right where you already work.

A few tips to maximize your hands-on time:

- **Install Devin CLI first.** Visit [cli.devin.ai](https://cli.devin.ai) to install. Run `devin` to start an interactive session.
- **Use shell integration for context.** Devin CLI understands your shell context, recent commands, and terminal state — it picks up on what you are working on.
- **Leverage subagents for parallel work.** Spawn independent subagents to handle subtasks in the foreground or background while you continue working.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Hand off to Cloud when needed.** If a task outgrows local execution (long-running builds, large-scale changes), hand it off to Devin Cloud. The cloud agent picks up where the CLI left off.

## Table of Contents

- [Lab 1 — Full-Stack CRUD Feature](#lab-1--full-stack-crud-feature)
- [Repos Required](#repos-required)
- [Key Takeaways](#key-takeaways)

---

## Labs

### Lab 1 — Full-Stack CRUD Feature

- **Module:** [New Feature Development](../../modules/application-development/new-feature-development.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js full-stack application
  - [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization) — Spring Boot loan service (alternative)
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot RealWorld app (alternative)
- **Objective:** Build a new feature from requirements through implementation and testing
- **Duration:** 60 min

#### Step 1: Explore the Codebase with Devin CLI

Clone the repo you want to work with and navigate into it. Start Devin CLI:

```
devin
```

Ask Devin to explore the codebase before you start building:

- *"What patterns do the existing features follow? What conventions should a new feature match?"*
- *"What database migration approach does the app use?"*

Devin CLI has access to your local files and shell context, so it can inspect the codebase directly — no cloud round-trip needed.

#### Step 2: Implement the Feature with Devin CLI

In the same Devin CLI session (or a new one), provide the implementation prompt interactively. Choose one:

**Option A — React + Node.js (timesheet-app):**
```
Add a "Projects" management feature to timesheet-app. Users should
be able to create, view, edit, and delete projects. Each project
has a name, description, client assignment, start date, and status
(active/completed/on-hold). Add backend API endpoints and frontend
UI. Follow existing patterns. Write tests.
```

**Option B — Spring Boot API (loan service):**
```
Add a loan payment history API to
uc-data-source-migration-jdbc-normalization. Create
GET /api/loans/:id/payments with pagination, date range filtering,
and payment type filtering. Add error handling and JUnit tests.
```

**Option C — Spring Boot API (RealWorld app):**
```
Add an "article statistics" feature to
uc-spring-boot-upgrade-microservice-extraction. Create
GET /api/articles/:slug/stats (view count, favorite count, comment
count, days since published) and GET /api/stats/trending (top 10
most-favorited in last 7 days). Write tests.
```

Devin CLI works with your local files, running builds and tests directly in your environment. You can use subagents to handle parallel subtasks (e.g., one subagent for the API layer while another writes tests).

> **Desktop integration:** Devin CLI runs as "Devin Local" inside Devin Desktop via ACP. Use Desktop for a visual agent management experience alongside terminal work.

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page in your browser. Try asking Devin CLI to add validation rules, frontend tests, audit logging, or API documentation based on what you find.

#### Step 4 (Optional): Review & Iterate

- Review the changes Devin made to your local files
- Ask Devin to add validation, error handling, or additional test cases
- Run your project's test suite locally to verify everything passes

> **Cloud handoff:** If the implementation requires long-running autonomous execution (e.g., a large-scale migration or multi-repo change), you can hand the task off to Devin Cloud. The cloud agent picks up where your local session left off. See the [cloud variant](README.md) for the full cloud workflow.

**Target Outcomes:** New feature following existing conventions, tests, database schema changes, all verified locally

## Repos Required

Choose one or more:
- [ ] timesheet-app
- [ ] uc-data-source-migration-jdbc-normalization
- [ ] uc-spring-boot-upgrade-microservice-extraction

## Key Takeaways

- **"Devin follows existing patterns"** — analyzes codebase conventions before implementing
- **"Clear requirements produce better results"** — interactive CLI sessions let you refine prompts in real time
- **"Full-stack in one session"** — database, API, UI, and tests in a single local session
- **"Start local, go cloud when needed"** — Devin CLI handles fast iteration; hand off to Cloud for long-running work
