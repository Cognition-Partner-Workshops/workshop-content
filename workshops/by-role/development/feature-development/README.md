# Workshop: Feature Development

## Overview

| | |
|---|---|
| **Focus** | Building new features on existing applications — requirements through implementation and testing |
| **Duration** | 1-2 hours |
| **Audience** | Full-stack developers, product engineers, development teams |
| **Key Modules** | [Gather Requirements](../../../../labs/application-development/gather-requirements.md), [Test-Driven Development](../../../../labs/application-development/test-driven-development.md), [New Feature Development](../../../../labs/application-development/new-feature-development.md), [API Documentation](../../../../labs/technical-documentation/api-documentation.md), [Database Schema Evolution](../../../../labs/application-development/database-schema-evolution.md) |

## Workshop Narrative

Feature development is the most common daily activity for development teams. This workshop demonstrates how Devin analyzes existing code patterns, implements full-stack changes, and creates tests — all while following the codebase's established conventions.

## Getting the Most from This Workshop

> **Devin works asynchronously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin keeps working in the background.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem before Devin executes.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments and Devin will wake up and address them — this is the core feedback loop.

## Table of Contents

- [Lab 1 — Full-Stack CRUD Feature](#lab-1--full-stack-crud-feature)
- [Repos Required](#repos-required)
- [Key Takeaways](#key-takeaways)

---

## Labs

### Lab 1 — Full-Stack CRUD Feature

- **Module:** [New Feature Development](../../../../labs/application-development/new-feature-development.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js full-stack application
  - [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization) — Spring Boot loan service (alternative)
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot RealWorld app (alternative)
- **Objective:** Build a new feature from requirements through implementation and testing
- **Duration:** 60 min

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

Choose one:

**Option A — React + Node.js (timesheet-app):**
```
Add a "Projects" management feature to timesheet-app. Users should be able to create, view, edit, and delete projects. Each project has a name, description, client assignment, start date, and status (active/completed/on-hold). Add backend API endpoints and frontend UI. Follow existing patterns. Write tests.
```

**Option B — Spring Boot API (loan service):**
```
Add a loan payment history API to uc-data-source-migration-jdbc-normalization. Create GET /api/loans/:id/payments with pagination, date range filtering, and payment type filtering. Add error handling and JUnit tests.
```

**Option C — Spring Boot API (RealWorld app):**
```
Add an "article statistics" feature to uc-spring-boot-upgrade-microservice-extraction. Create GET /api/articles/:slug/stats (view count, favorite count, comment count, days since published) and GET /api/stats/trending (top 10 most-favorited in last 7 days). Write tests.
```

#### Step 2: Research with Ask Devin

- *"What patterns do the existing features follow? What conventions should a new feature match?"*
- *"What database migration approach does the app use?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page. Try adding validation rules, frontend tests, audit logging, or API documentation.

#### Step 4 (Optional): Review & Give Feedback

- Review for code style consistency with existing patterns
- Ask Devin to add validation, error handling, or additional test cases

**Target Outcomes:** New feature following existing conventions, tests, database schema changes, PR with review

## Repos Required

Choose one or more:
- [ ] timesheet-app
- [ ] uc-data-source-migration-jdbc-normalization
- [ ] uc-spring-boot-upgrade-microservice-extraction

## Key Takeaways

- **"Devin follows existing patterns"** — analyzes codebase conventions before implementing
- **"Clear requirements produce better results"** — specific prompts lead to better output
- **"Full-stack in one session"** — database, API, UI, and tests in a single session
