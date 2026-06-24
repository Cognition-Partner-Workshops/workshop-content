# New Feature Development

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Notes](#notes)
- [Repositories](#repositories)
  - [timesheet-app](#timesheet-app)
  - [uc-data-source-migration-jdbc-normalization](#uc-data-source-migration-jdbc-normalization)
  - [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

1. Pick a repository below
2. Paste the sample prompt into Devin (or customize the feature requirements)
3. Observe how Devin analyzes existing patterns before implementing the new feature
4. Review the PR and leave feedback — Devin will iterate on your comments

---

## Challenge

Develop a new feature for an existing application — from requirements through implementation, testing, and PR creation. This exercises Devin's ability to understand an existing codebase and add functionality that fits the existing architecture.

## Target Outcomes

- New feature implemented following existing code conventions and architecture
- Unit tests and/or integration tests for the new feature
- Database schema changes (if needed) with migration scripts
- PR with clear description of what was added and why

## What Participants Will Learn

- How Devin analyzes existing code patterns before implementing new features
- How Devin handles full-stack changes (database + API + UI)
- The importance of clear, specific requirements in prompts
- How Devin creates tests for new functionality
- How the **PR feedback loop** works — reviewers comment, Devin iterates, CI re-runs (see [Collaboration Model](../../reference/general-themes/collaboration-model.md))

## Devin Features Exercised

- Codebase analysis and pattern matching
- Full-stack implementation (backend + frontend)
- Test generation
- Database schema design
- PR creation with feature documentation
- **Devin Review** — can catch issues in the new feature PR before human review (see [Platform Capabilities → Devin Review](../../reference/general-themes/platform-capabilities.md#devin-review))

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

## Notes

- This challenge benefits from strong prompt engineering — the more specific the requirements, the better the output
- Encourage participants to use Ask Devin first to gather requirements and scope the feature
- Good follow-up: have another participant review the PR using Devin Review

---

## Repositories

### <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

React + Node.js/Express full-stack application — add a new CRUD feature, management page, or API endpoint with UI.

#### Step 1: Paste into Devin

```text
Add a "Projects" management feature to timesheet-app. Users should be able to create, view, edit, and delete projects. Each project has a name, description, client assignment, start date, and status (active/completed/on-hold). Add both the backend API endpoints and the frontend UI page. Follow the existing patterns in the codebase for the data model, API structure, and React components. Write tests for the backend endpoints.
```

#### Step 2: Research with Ask Devin

- *"What patterns do the existing CRUD features (clients, work entries) follow in timesheet-app? What conventions should a new feature match?"*
- *"What database migration approach does the app use? How should I add a new table?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the existing feature patterns. The new feature should follow the same conventions for routing, state management, API design, and database schema.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the new feature match the existing code style and patterns?
- **Leave a comment** asking Devin to add validation for the project dates or status transitions

---

### <a id="uc-data-source-migration-jdbc-normalization"></a>uc-data-source-migration-jdbc-normalization

**Repository:** [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization)

Spring Boot loan service — add new API endpoints, filtering, pagination, or reporting capabilities.

#### Step 1: Paste into Devin

```text
Add a loan payment history API to uc-data-source-migration-jdbc-normalization. Create a new endpoint GET /api/loans/:id/payments that returns a paginated list of payment records for a given loan. Include filtering by date range and payment type. Add proper error handling for invalid loan IDs. Write JUnit tests for the new endpoint.
```

#### Step 2: Research with Ask Devin

- *"What API patterns does the existing loan service follow? What conventions should new endpoints match?"*
- *"What pagination pattern is used — offset-based or cursor-based? Which is better for this use case?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the existing API contracts and data model. Ensure the new endpoint follows the same patterns.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the pagination implementation follow REST best practices?
- **Leave a comment** asking Devin to add sorting options to the payment history endpoint

---

### <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot RealWorld app — add new API endpoints or enhance existing ones with additional functionality.

#### Step 1: Paste into Devin

```text
Add an "article statistics" feature to uc-spring-boot-upgrade-microservice-extraction. Create a new endpoint GET /api/articles/:slug/stats that returns: view count, favorite count, comment count, and days since published. Add a GET /api/stats/trending endpoint that returns the top 10 most-favorited articles in the last 7 days. Write tests for both endpoints.
```

#### Step 2: Research with Ask Devin

- *"What data is available in the existing schema to support article statistics? Do we need new tables or can we derive from existing data?"*
- *"How do the existing article endpoints handle authentication — should stats be public or require auth?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the article domain model and existing API surface. Identify how to compute statistics from existing data.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the stats computation efficient? Would it cause performance issues at scale?
- **Leave a comment** asking Devin to add caching for the trending endpoint

---

## Key Takeaways

- Devin analyzes existing code patterns (naming, structure, test conventions) before writing new code — the result fits the codebase naturally
- Specific requirements (data model fields, API contracts, validation rules) produce higher-quality implementations
- The PR feedback loop lets reviewers request refinements (e.g., "add validation", "add caching") and Devin pushes follow-up commits
- Devin Review can catch issues in new feature code before human review begins
- Full-stack features (database + API + UI + tests) are well within Devin's scope when the requirements are clear

---

## Going Further

- **Ticket-driven feature development** — tag a Jira or GitHub Issue with `Devin:Implementation` and Devin picks it up automatically, analyzes the requirements, implements the feature, and opens a PR linked back to the ticket (see [Design Patterns → Event-Driven Triggers](../../reference/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers))
- **Playbook-driven feature scaffolding** — create a playbook that encodes your team's feature development process (scaffold model → add migration → implement API → add tests → update docs) so every new feature follows the same structure (see [Platform Capabilities → Playbooks](../../reference/general-themes/platform-capabilities.md#playbooks))
- **Parallel feature development** — use child agents to implement multiple independent features simultaneously, each following the same playbook (see [Platform Capabilities → Child Agents](../../reference/general-themes/platform-capabilities.md#child-agents-divide-and-conquer))
