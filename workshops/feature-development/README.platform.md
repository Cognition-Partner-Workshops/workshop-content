# Workshop: Feature Development (Desktop + Cloud)

**Other variants:** [Cloud only](README.md) | [CLI variant](README.local.md)

## Overview

| | |
|---|---|
| **Focus** | Building new features on existing applications — requirements through implementation and testing |
| **Duration** | 1-2 hours |
| **Audience** | Full-stack developers, product engineers, development teams |
| **Key Modules** | [Gather Requirements](../../modules/application-development/gather-requirements.md), [Test-Driven Development](../../modules/application-development/test-driven-development.md), [New Feature Development](../../modules/application-development/new-feature-development.md), [API Documentation](../../modules/technical-documentation/api-documentation.md), [Database Schema Evolution](../../modules/application-development/database-schema-evolution.md) |

## Platform Context

This workshop uses **Devin Desktop + Devin Cloud**. You will use Desktop as your primary interface — exploring code locally, delegating tasks to Cloud, and reviewing results without leaving the editor.

> **Tip:** Desktop connects local and cloud workflows in one view. You can also run these labs from the [cloud web app](README.md) or from the [terminal with Devin CLI](README.local.md).

## Workshop Narrative

Feature development is the most common daily activity for development teams. This workshop demonstrates how Devin analyzes existing code patterns, implements full-stack changes, and creates tests — all from within Devin Desktop. You will explore code locally with Cascade or Devin Local, delegate implementation to Devin Cloud, and review the resulting PRs directly in the editor — no browser switching required.

## Getting the Most from This Workshop

> **Devin Cloud works asynchronously.** Once you delegate a task from Desktop to Cloud, Devin runs independently on its own VM. Continue coding locally while Cloud works in the background — you'll see the session update in the Agent Command Center when it opens a PR.

A few tips to maximize your hands-on time:

- **Create a Space for this workshop.** Spaces group sessions, PRs, files, and context for a specific task into a single view. New sessions in a Space inherit the project context automatically.
- **Explore locally, implement in Cloud.** Use Cascade or Devin Local for quick code exploration, then delegate the heavier implementation to Cloud with one click.
- **Monitor from the Agent Command Center.** The Kanban board shows all your agents (local and cloud) organized by status — see at a glance what is in flight, what is blocked, and what is ready for review.
- **Review PRs with one-click checkout.** When Cloud opens a PR, check it out directly in the editor. No manual `git fetch` or branch switching needed.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.

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

#### Step 1: Set Up a Space in Desktop

1. Open Devin Desktop and create a new **Space** for this workshop (e.g., "Feature Dev Workshop").
2. Add the repository you plan to work with to the Space — this lets all sessions and PRs for this lab stay organized in one view.

#### Step 2: Explore the Codebase with Cascade or Devin Local

Use Cascade or Devin Local inside Desktop to explore the repo before delegating the implementation. Try asking:

- *"What patterns do the existing features follow? What conventions should a new feature match?"*
- *"What database migration approach does the app use?"*

This gives you context to refine the implementation prompt before handing it off to Cloud.

> **ACP note:** Devin Local is one of several agents available in Desktop via the Agent Client Protocol. You can also use third-party ACP agents (Codex CLI, Claude Agent, Gemini CLI, etc.) for this exploration step.

#### Step 3: Delegate to Devin Cloud

From Desktop, delegate the implementation task to Devin Cloud. Choose one prompt:

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

The Cloud session appears immediately in the Agent Command Center. Continue working locally while it runs.

#### Step 4: Monitor Progress in the Agent Command Center

Switch to the Agent Command Center (Kanban view) to track the Cloud session's status. You can see when Devin is working, when it needs input, and when it has opened a PR — without leaving Desktop.

#### Step 5 (Optional): Review & Give Feedback in the Editor

When the Cloud session opens a PR:

1. Use **one-click checkout** to open the PR branch directly in the editor
2. Review for code style consistency with existing patterns
3. Leave PR comments — Devin Cloud wakes up and iterates based on your feedback
4. Merge when satisfied, all from within Desktop

**Target Outcomes:** New feature following existing conventions, tests, database schema changes, PR reviewed and iterated on from Desktop

## Repos Required

Choose one or more:
- [ ] timesheet-app
- [ ] uc-data-source-migration-jdbc-normalization
- [ ] uc-spring-boot-upgrade-microservice-extraction

## Key Takeaways

- **"Devin follows existing patterns"** — analyzes codebase conventions before implementing
- **"Clear requirements produce better results"** — local exploration with Cascade or Devin Local refines the prompt before Cloud executes
- **"Full-stack in one session"** — database, API, UI, and tests in a single Cloud session, reviewed without leaving the editor
- **"One view for local and cloud"** — the Agent Command Center tracks all agents on a single Kanban board
