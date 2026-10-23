# Workshop: SDA Developer Productivity

## Overview

|                 |                                                                                                                                                                                                                                                                                                                                                                           |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Focus**       | Demonstrating how Devin accelerates everyday developer tasks — bug fixing, feature development, testing, and security                                                                                                                                                                                                                                                     |
| **Duration**    | 3-4 hours (half day)                                                                                                                                                                                                                                                                                                                                                      |
| **Audience**    | Saudi Digital Authority (SDA) engineers                                                                                                                                                                                                                                                                                                                                   |
| **Application** | [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet) — React 19 + Node.js/Express + SQLite (full-stack timesheet & billing app)                                                                                                                                                                                                                  |
| **Key Modules** | [Fix UI Bug](../../modules/application-development/fix-ui-bug.md), [Fix Data Bug](../../modules/application-development/fix-data-bug.md), [New Feature Development](../../modules/application-development/new-feature-development.md), [Unit Testing](../../modules/testing-qa/unit-testing.md), [Security Antipatterns](../../modules/security/security-antipatterns.md) |

## Workshop Narrative

Every engineering team deals with the same daily challenges: bugs in production, feature backlogs, insufficient test coverage, and security findings that pile up. 

This workshop uses a real full-stack application (timesheet and billing tracker) to show how Devin handles these tasks end-to-end — from reading code and understanding context, through implementation and testing, to opening a pull request for review.

---

## Lab 1 — Fix Known Bugs (45 min)

- **Modules:** [Fix UI Bug](../../modules/application-development/fix-ui-bug.md) + [Fix Data Bug](../../modules/application-development/fix-data-bug.md)
- **Repository:** [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet)
- **Objective:** Fix two known bugs — a visual CSS issue and a data persistence problem — demonstrating Devin's ability to investigate, diagnose, and fix issues across the frontend and backend

### Bug A: UI Strikethrough Bug (20 min)

#### Step 1: Paste into Devin

> There's a UI bug in app_timesheet: on the /work-entries page, the Client dropdown label has a strikethrough. Investigate the CSS/styling causing this issue and fix it. Take a screenshot before and after the fix. Open a PR.

#### Step 2: Research with Ask Devin

- *"What CSS or Material-UI styling is applied to the Client dropdown on the /work-entries page?"*
- *"Are there any global styles that might be causing the strikethrough?"*

### Bug B: Data Persistence Bug (25 min)

#### Step 1: Paste into Devin

> There's a data bug in app_timesheet: clients created by one user are not visible to other users after logging out and back in with a different email. Clients should be shared across all users. Investigate the database schema and queries, find the root cause, and fix it. Make sure work entries still belong to individual users. Open a PR.

#### Step 2: Research with Ask Devin

- *"How does the app_timesheet database schema handle multi-tenancy? Are clients scoped per-user or org-wide?"*
- *"What queries does the backend use to fetch clients? Are they filtered by user ID?"*

#### Step 3: Read the DeepWiki

Open the [app_timesheet DeepWiki](https://deepwiki.com/Cognition-Partner-Workshops/app_timesheet) page. Explore the database schema and data flow sections. Ask questions right there.

#### Step 4 (Optional): Review & Give Feedback

- Review the PR diff — does the fix handle edge cases (e.g., deleting a shared client that has work entries)?
- Ask Devin to add a test that verifies clients are visible across users

**Target Outcomes:** Two merged PRs with before/after evidence, understanding of how Devin traces bugs through frontend and backend layers

---

## Lab 2 — New Feature Development (60 min)

- **Module:** [New Feature Development](../../modules/application-development/new-feature-development.md)
- **Repository:** [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet)
- **Objective:** Build a complete new feature (backend API + frontend UI + database changes + tests) in a single Devin session

### Step 1: Paste into Devin

> Add a "Projects" management feature to app_timesheet. Users should be able to create, view, edit, and delete projects. Each project has a name, description, client assignment, start date, and status (active/completed/on-hold). Add both the backend API endpoints and the frontend UI page. Follow the existing patterns in the codebase for the data model, API structure, and React components. Write tests for the backend endpoints. Open a PR.

### Step 2: Research with Ask Devin

- *"What patterns do the existing features follow? What conventions should a new feature match?"*
- *"What database migration approach does the app use?"*
- *"How are React components structured in the frontend — what state management pattern is used?"*

### Step 3 (Optional): Read the DeepWiki

Open the [app_timesheet DeepWiki](https://deepwiki.com/Cognition-Partner-Workshops/app_timesheet) page. Try adding validation rules, audit logging, or API documentation to the new feature.

### Step 4 (Optional): Review & Give Feedback

- Review for code style consistency with existing patterns
- Ask Devin to add input validation, error handling, or additional test cases
- Try: *"Add a filter on the Projects page to filter by status and client"*

**Target Outcomes:** Full-stack feature (DB schema + API routes + React UI + tests) delivered via PR, following existing codebase conventions

---

## Lab 3 — Unit Testing & Coverage (45 min)

- **Module:** [Unit Testing](../../modules/testing-qa/unit-testing.md)
- **Repository:** [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet)
- **Objective:** Analyze test coverage gaps and generate meaningful unit tests for the backend API

### Step 1: Paste into Devin

> Analyze the current test coverage of app_timesheet. Add missing Jest unit tests for the backend API routes and service layer. Generate a coverage report and fix any failing tests. Open a PR with the changes.

### Step 2: Research with Ask Devin

- *"What's the current test coverage in app_timesheet? Which modules are most at risk?"*
- *"What testing patterns does the codebase use? Jest, Supertest, mocking?"*
- *"Which API endpoints have no test coverage at all?"*

### Step 3 (Optional): Read the DeepWiki

Open the [app_timesheet DeepWiki](https://deepwiki.com/Cognition-Partner-Workshops/app_timesheet) page. Try generating edge case tests, integration tests, or adding test data factories.

### Step 4 (Optional): Review & Give Feedback

- Review tests for meaningful assertions (not just coverage padding)
- Ask Devin to add edge case tests: *"Add tests for invalid input, empty results, and concurrent access"*
- Ask Devin to add integration tests using Supertest for the full HTTP request/response cycle

**Target Outcomes:** Improved test coverage with meaningful assertions, coverage report, understanding of Devin's test generation quality

---

## Lab 4 — Security Audit & Remediation (45 min)

- **Module:** [Security Antipatterns](../../modules/security/security-antipatterns.md)
- **Repository:** [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet)
- **Objective:** Run a security code review against the OWASP Top 10 and have Devin fix the most critical findings

### Step 1: Paste into Devin

> Perform a security code review of app_timesheet against the OWASP Top 10. Focus on: authentication weaknesses (email-only, no password), SQL injection risks in the SQLite queries, XSS vulnerabilities in the React frontend, and CSRF protection. For each finding, explain the vulnerability, its severity, and provide a recommended fix. Implement fixes for the top 3 most critical findings. Open a PR.

### Step 2: Research with Ask Devin

- *"What authentication mechanism does app_timesheet use? What are its weaknesses?"*
- *"Are the SQLite queries parameterized or vulnerable to injection?"*
- *"Does the Express backend use helmet, CORS, or rate limiting?"*

### Step 3 (Optional): Read the DeepWiki

Open the [app_timesheet DeepWiki](https://deepwiki.com/Cognition-Partner-Workshops/app_timesheet) page. Explore the authentication flow and API security sections.

### Step 4 (Optional): Review & Give Feedback

- Review security fixes for completeness — did Devin address the root cause or just a symptom?
- Ask Devin to add: *"Run npm audit and fix any critical dependency vulnerabilities too"*
- Try: *"Add rate limiting to the login endpoint and document the security improvements in a SECURITY.md"*

**Target Outcomes:** Security audit report with OWASP Top 10 findings, fixes for critical vulnerabilities, PR with before/after evidence

---

## Duration Variants

| Duration | Labs | Format | Best For |
|----------|------|--------|----------|
| **3.5-4 hours** | Labs 1 + 2 + 3 + 4 | Full workshop | Half-day sessions with all four labs |
| **3 hours** | Labs 1 + 2 + 4 | Bug fix → feature → security | Skip testing to focus on dev + security |
| **2.5 hours** | Labs 1 + 2 + 3 | Bug fix → feature → testing | Development-focused, skip security |
| **2 hours** | Labs 1 + 2 | Bug fix → feature | Quick productivity demo |
| **1 hour** | Lab 2 only | Feature development showcase | Executive/stakeholder demo |

## Repos Required

- [ ] [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet)

## Module Reference

All modules used in this workshop, with links to the full challenge instructions:

| Lab | Module | Category | Time | Difficulty |
|-----|--------|----------|------|------------|
| 1A | [Fix UI Bug](../../modules/application-development/fix-ui-bug.md) | Application Development | 20 min | Beginner |
| 1B | [Fix Data Bug](../../modules/application-development/fix-data-bug.md) | Application Development | 25 min | Intermediate |
| 2 | [New Feature Development](../../modules/application-development/new-feature-development.md) | Application Development | 60 min | Intermediate-Advanced |
| 3 | [Unit Testing](../../modules/testing-qa/unit-testing.md) | Testing & QA | 45 min | Beginner-Intermediate |
| 4 | [Security Antipatterns](../../modules/security/security-antipatterns.md) | Security | 45 min | Intermediate |

## Additional Modules (for extended or follow-up sessions)

These modules also support `app_timesheet` and can be swapped in or added:

| Module | Category | Time | Use Case |
|--------|----------|------|----------|
| [Gather Requirements](../../modules/application-development/gather-requirements.md) | App Dev | 30 min | Pre-development planning exercise |
| [Test-Driven Development](../../modules/application-development/test-driven-development.md) | App Dev | 60 min | Two-session TDD workflow |
| [Fix Runtime Bug](../../modules/application-development/fix-runtime-bug.md) | App Dev | 45 min | Open-ended bug hunting |
| [End-to-End Testing](../../modules/testing-qa/end-to-end-testing.md) | Testing | 60 min | Playwright E2E test authoring |
| [Linting & Static Analysis](../../modules/testing-qa/linting-static-analysis.md) | Testing | 30 min | Quick beginner-friendly lab |
| [CI/CD Pipeline](../../modules/devops-cicd/cicd-pipeline.md) | DevOps | 45 min | GitHub Actions workflow creation |
| [Upgrade Dependencies](../../modules/security/upgrade-dependencies.md) | Security | 45 min | npm audit + dependency upgrades |
| [Remediate Vulnerabilities](../../modules/security/remediate-vulnerabilities.md) | Security | 60 min | SAST scanning with Trivy |
| [Observability & Monitoring](../../modules/observability-sre/observability-monitoring.md) | SRE | 60 min | Winston logging + Prometheus metrics |
| [API Documentation](../../modules/technical-documentation/api-documentation.md) | Docs | 45 min | OpenAPI/Swagger generation |
| [Code Refactoring & Tech Debt](../../modules/architecture-design/code-refactoring-tech-debt.md) | Architecture | 45 min | Identify and resolve code smells |
| [Cloud-Native Refactor](../../modules/migration-modernization/cloud-native-refactor.md) | Migration | 60 min | Dockerize + K8s manifests |

## Key Takeaways

- **"Devin understands your codebase"** — it reads existing patterns (API structure, component conventions, database schema) before making changes
- **"Bug fix to feature to security in one session"** — participants see the full spectrum of daily development tasks accelerated
- **"Review, don't rewrite"** — the developer's role shifts from writing code to reviewing Devin's PRs and providing feedback
- **"One prompt, full-stack result"** — a single natural language prompt produces database changes, API routes, React UI, and tests
