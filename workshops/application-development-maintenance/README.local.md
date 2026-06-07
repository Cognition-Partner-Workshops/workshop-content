# Workshop: Application Development and Maintenance

**Other variants:** [Cloud only](README.md) | [Desktop + Cloud](README.platform.md)

## Overview

| | |
|---|---|
| **Focus** | Feature development, bug fixing, code maintenance, and database evolution — the full lifecycle of keeping applications healthy and growing |
| **Duration** | Flexible (3 tracks, 3 labs each) |
| **Audience** | Development teams, full-stack engineers, application owners, and team leads responsible for shipping features and maintaining production applications |
| **Tracks** | **Track A:** Feature Development · **Track B:** Bug Fixing & Root Cause Analysis · **Track C:** Maintenance & Evolution |

## Platform Context

This workshop uses **Devin CLI / Devin Local** for a terminal-native workflow. You will run `devin` in your terminal, provide prompts interactively, and work with your local files and dev environment. For tasks that need autonomous long-running execution, you can hand off to Devin Cloud.

> **Tip:** Devin CLI is consumable over ACP (Agent Client Protocol) inside Devin Desktop, where it appears as "Devin Local." If you prefer a visual agent management experience alongside terminal work, see the [Desktop + Cloud variant](README.platform.md).

## Workshop Narrative

This workshop covers the three pillars of application development and maintenance: building new capabilities, fixing production issues, and evolving the codebase over time. Each track is self-contained — participants can follow all three in sequence for a full-day experience, or focus on one or two tracks in a shorter session.

The tracks are designed to show Devin working across the application lifecycle:
- **Track A** shows Devin as a feature developer — gathering requirements, implementing full-stack changes, and iterating on feedback — directly in your terminal
- **Track B** shows Devin as a debugger and investigator — reproducing bugs, tracing root causes, and fixing issues with local file access and shell integration
- **Track C** shows Devin as a maintenance engineer — upgrading dependencies, evolving schemas, and refactoring tech debt with rapid local iteration

## Getting the Most from This Workshop

> **Devin CLI works in your terminal.** Run `devin` (install from [cli.devin.ai](https://cli.devin.ai)) and provide prompts interactively. Devin has access to your local files, git repos, and dev environment — making it fast and interactive for exploration and quick fixes.

A few tips to maximize your hands-on time:

- **Use Devin CLI for exploration.** Instead of Ask Devin in the browser, run `devin` in your terminal and ask questions directly. It has access to your local files and can read code, run commands, and suggest changes interactively.
- **Spawn subagents for parallel work.** Devin CLI can spawn independent subagents to handle subtasks in the foreground or background — useful when you want to explore one area while Devin works on another.
- **Leverage shell integration.** Devin CLI understands your shell context, recent commands, and terminal state. It can pick up context from your current working directory and recent git history.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item during a session, accept it — this is how teams build a shared context layer that makes Devin smarter over time. The same knowledge works across CLI, Desktop, and Cloud.
- **Hand off to Cloud when needed.** Some tasks (scheduled sessions, child agents at scale, long-running autonomous execution) require Devin Cloud. You can start locally and hand off to Cloud when the task outgrows local execution.

### CLI Workflow (replaces the web-app workflow)

Each lab in this workshop follows a terminal-native workflow:

1. **Run `devin` in your terminal** — Navigate to the repo directory and run `devin`
2. **Provide the prompt interactively** — Type or paste the task description
3. **Iterate in conversation** — Devin works with your local files. Ask follow-up questions, refine the approach, review changes in real time
4. **Use subagents for parallel work** — Spawn background subagents for independent subtasks
5. **Hand off to Cloud if needed** — For long-running or autonomous tasks, hand off to Devin Cloud

---

## Table of Contents

- [Track A: Feature Development](#track-a-feature-development)
  - [Lab A1 — Full-Stack CRUD Feature](#lab-a1--full-stack-crud-feature)
  - [Lab A2 — API Feature with TDD](#lab-a2--api-feature-with-tdd)
  - [Lab A3 — Requirements to Implementation](#lab-a3--requirements-to-implementation)
- [Track B: Bug Fixing & Root Cause Analysis](#track-b-bug-fixing--root-cause-analysis)
  - [Lab B1 — Exploratory Bug Hunting](#lab-b1--exploratory-bug-hunting)
  - [Lab B2 — Cross-Service Bug Investigation](#lab-b2--cross-service-bug-investigation)
  - [Lab B3 — Data Bug & Schema Fix](#lab-b3--data-bug--schema-fix)
- [Track C: Maintenance & Evolution](#track-c-maintenance--evolution)
  - [Lab C1 — Dependency Upgrades & Scheduled Maintenance](#lab-c1--dependency-upgrades--scheduled-maintenance)
  - [Lab C2 — Database Schema Evolution](#lab-c2--database-schema-evolution)
  - [Lab C3 — Code Refactoring & Tech Debt](#lab-c3--code-refactoring--tech-debt)
- [Additional Challenges](#additional-challenges)
- [Suggested Formats](#suggested-formats)
- [Repos Required](#repos-required)
- [Context](#context)
- [Devin Features Checklist](#devin-features-checklist)

---

## Track A: Feature Development

Track A demonstrates Devin as a feature developer. Participants will build full-stack CRUD features, implement API endpoints with test-driven development, and turn vague requirements into working implementations — working directly in the terminal.

### Lab A1 — Full-Stack CRUD Feature

- **Module:** [New Feature Development](../../modules/application-development/new-feature-development.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React 19 + Node.js/Express + SQLite full-stack application
  - [ts-java-angular-jhipster](https://github.com/Cognition-Partner-Workshops/ts-java-angular-jhipster) — Angular + Spring Boot JHipster monolith (alternative)
- **Objective:** Build a complete new feature — backend API, database schema, frontend UI, and tests — following existing codebase conventions

#### Step 1: Explore with Devin CLI

Open your terminal, navigate to the repo, and run `devin` to scope the feature:
- *"What existing patterns does timesheet-app use for CRUD features? What data model, API structure, and React component conventions should a new feature follow?"*
- *"What database migration approach does the app use? How are new tables created?"*

Devin CLI has direct access to your local files, so exploration is fast and interactive.

#### Step 2: Implement with Devin CLI

Provide the implementation prompt to `devin` in your terminal:

**Option A — React + Node.js (timesheet-app):**
```
Add a "Projects" management feature to timesheet-app. Users should be able to create, view, edit, and delete projects. Each project has a name, description, client assignment, start date, and status (active/completed/on-hold). Add both the backend API endpoints and the frontend UI page. Follow the existing patterns in the codebase for the data model, API structure, and React components. Write tests for the backend endpoints.
```

**Option B — Angular + Spring Boot (JHipster):**
```
Add an "Invoice" management feature to ts-java-angular-jhipster. Users should be able to create, view, edit, and delete invoices. Each invoice has a number, date, due date, amount, status (draft/sent/paid/overdue), and is linked to a bank account. Add both the Spring Boot backend (JPA entity, REST controller, service layer) and the Angular frontend (list view, create/edit form, detail view). Follow JHipster conventions for entity generation patterns. Write JUnit tests for the backend.
```

Devin will work with your local files — you can watch changes appear in real time and iterate interactively.

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the existing feature patterns. Use what you learn to try different approaches:
- Have Devin add **validation rules** for the new feature (e.g., project dates must not overlap, invoice amounts must be positive)
- Ask Devin to add **frontend tests** (React Testing Library or Angular TestBed) for the new UI components
- Try having Devin add **audit logging** for all CRUD operations
- Ask Devin to generate **API documentation** (Swagger/OpenAPI) for the new endpoints

#### Step 4 (Optional): Review & iterate

Review the changes Devin made directly in your editor or terminal:
- Check the git diff: `git diff`
- Run tests locally: `npm test` or `./gradlew test`
- Ask Devin follow-up questions to refine the implementation

**Iterate interactively:**
- *"PR Review flagged that the project name field has no length validation — please add max length checking"*
- *"Add error handling for the case where a client is deleted while it has active projects"*
- *"The React component doesn't handle loading states — add a spinner while the API call is in flight"*

See the full challenge details for [New Feature Development](../../modules/application-development/new-feature-development.md) for more ideas.

> **Desktop integration:** Devin CLI runs as "Devin Local" inside Devin Desktop via ACP. Use Desktop for a visual agent management experience alongside terminal work.

- **Key Takeaways:**
  - **"Devin follows existing patterns"** — it analyzes the codebase's conventions before implementing, producing code that fits the existing architecture
  - **"Local iteration is fast"** — see changes immediately, run tests locally, iterate in conversation without waiting for a cloud VM
  - **"The feedback loop is interactive"** — ask follow-up questions, refine the approach, review changes in real time
  - **"Knowledge compounds over time"** — if Devin discovers project conventions during this session, save them as Knowledge items. Future sessions will automatically benefit

- **Target Outcomes (any of these count):**
  - New feature implemented following existing code conventions
  - Unit tests and/or integration tests for the new feature
  - Database schema changes with migration scripts
  - Frontend UI components for the new feature
  - Iterative refinement through interactive CLI conversation

---

### Lab A2 — API Feature with TDD

- **Module:** [Test-Driven Development](../../modules/application-development/test-driven-development.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot 2.6.3 RealWorld app with JUnit infrastructure
  - [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber) — Spring Boot + Cucumber BDD framework (alternative)
- **Objective:** Implement a new API feature using test-driven development — write failing tests first, then implement until they pass

#### Step 1: Implement with Devin CLI

Run `devin` in your terminal and provide the TDD prompt:

**Option A — TDD with JUnit (Spring Boot RealWorld):**
```
Using test-driven development, add an "article statistics" feature to uc-spring-boot-upgrade-microservice-extraction. First, write failing JUnit tests for two new endpoints: GET /api/articles/:slug/stats (returns view count, favorite count, comment count, days since published) and GET /api/stats/trending (returns top 10 most-favorited articles in the last 7 days). Then implement the endpoints to make the tests pass. Follow existing code patterns. showing the TDD progression (tests written first, then implementation).
```

**Option B — BDD-First (Cucumber):**
```
Using behavior-driven development, add an order management feature to uc-bdd-test-generation-cucumber. First, write Gherkin feature files describing the behavior: create an order, list orders, get order by ID, update order status, delete order. Then implement an OrderController, OrderService, and Order entity to make all scenarios pass. Follow existing project patterns for step definitions and entity structure.
```

Watch the TDD cycle happen locally — tests fail, implementation gets added, tests pass.

#### Step 2: Explore test patterns with Devin CLI

While Devin works, ask follow-up questions interactively:
- *"What are the existing test patterns in uc-spring-boot-upgrade-microservice-extraction? How are controller tests structured?"*
- *"What's the difference between MockMvc tests and integration tests in this codebase? Which is more appropriate for API endpoint testing?"*
- *"How does the Cucumber framework in uc-bdd-test-generation-cucumber handle test data setup and teardown?"*

Use subagents to explore in parallel: spawn a background subagent to research test patterns while the main agent continues TDD implementation.

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the test infrastructure. Try:
- Have Devin add **negative test cases** (invalid inputs, unauthorized access, not-found scenarios)
- Ask Devin to add **data-driven tests** using parameterized JUnit or Cucumber Scenario Outlines
- Try having Devin add **contract tests** that verify the API response shape

#### Step 4 (Optional): Review & iterate

Review the TDD progression locally:
- **Test-first evidence:** Check the git log to verify tests were written before implementation
- **Test quality:** Are the tests checking real behavior or just trivially asserting?
- **Edge cases:** Does the implementation handle boundary conditions discovered during TDD?

**Iterate interactively with Devin:**
- *"Add a test for when the article slug doesn't exist — should return 404"*
- *"The trending endpoint should handle the case where there are fewer than 10 articles"*
- *"Add an integration test that verifies the database query performance"*

- **Key Takeaways:**
  - **"Tests-first produces better APIs"** — writing tests before code forces you to think about the API contract from the consumer's perspective
  - **"Local TDD is interactive"** — watch the red-green-refactor cycle happen in real time in your terminal
  - **"BDD bridges dev and business"** — Gherkin scenarios are readable by non-developers, making test suites a communication tool
  - **"Subagents parallelize exploration"** — research test patterns in a background subagent while TDD continues

- **Target Outcomes (any of these count):**
  - Failing tests written before implementation
  - All tests passing after implementation
  - Clear TDD commit progression visible in git history
  - Edge case and negative test coverage
  - Interactive iteration through CLI conversation

---

### Lab A3 — Requirements to Implementation

- **Modules:** [Gather Requirements](../../modules/application-development/gather-requirements.md) + [New Feature Development](../../modules/application-development/new-feature-development.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js full-stack application
  - [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization) — Spring Boot loan management service (alternative)
- **Objective:** Start from vague business requirements, use Devin CLI to refine them into technical specifications, then have Devin implement the feature — the full requirements-to-code pipeline

#### Step 1: Gather requirements with Devin CLI

This lab starts with requirements gathering. Run `devin` in your terminal and work through these prompts interactively:

1. *"A manager wants to see which team members are overloaded this week. What data does timesheet-app already have that could power a 'team workload' dashboard? What's missing?"*
2. *"Based on the existing data model, design a 'Team Workload Dashboard' feature. What API endpoints are needed? What calculations should the backend perform? What should the UI show?"*
3. *"Write a detailed technical specification for the workload dashboard — include the data model, API contract (request/response shapes), and UI wireframe description."*

Devin CLI explores your local files directly, making requirements gathering fast and grounded in the actual codebase.

#### Step 2: Implement with Devin CLI (using the refined spec)

Continue the conversation with Devin CLI, providing the implementation prompt:

```
Implement the following feature for timesheet-app based on this specification:

[Reference the technical spec from the conversation above]

Follow existing code patterns. Add backend API endpoints, frontend React components, and tests.
```

Alternatively, if you want to try a less-refined approach:

```
A manager wants a "Team Workload Dashboard" in timesheet-app. They should be able to see: which team members have logged the most hours this week, who has upcoming deadlines, and which clients have the most active work entries. Figure out the best way to implement this given the existing data model and codebase patterns. Add backend calculations, an API endpoint, and a frontend dashboard page.
```

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the data model. Compare what Devin CLI suggested with what's actually available in the database.

#### Step 4 (Optional): Review & iterate

Review the implementation locally and iterate:
- **Requirements fit:** Does the feature solve the manager's original problem?
- **Data accuracy:** Are the workload calculations correct?
- **UX quality:** Is the dashboard useful and intuitive?

**Iterate interactively:**
- *"Add a date range picker so managers can look at workload for different weeks"*
- *"The overload threshold should be configurable, not hard-coded at 40 hours"*
- *"Add a CSV export for the workload summary"*

- **Key Takeaways:**
  - **"CLI exploration → CLI implementation"** — the same conversational session flows from requirements to implementation without switching tools
  - **"Vague requirements still work"** — Devin can infer reasonable implementations from business-level descriptions, but refined specs produce better output
  - **"Local files make exploration grounded"** — Devin CLI reads the actual codebase, so feasibility checks happen against real data models
  - **"DeepWiki validates feasibility"** — checking the data model before implementation catches impossible requirements early

- **Target Outcomes (any of these count):**
  - Technical specification produced via Devin CLI exploration
  - Feature implemented matching the specification
  - Backend API with calculations and frontend dashboard
  - Tests verifying the feature logic
  - Iterative refinement through CLI conversation

---

## Track B: Bug Fixing & Root Cause Analysis

Track B demonstrates Devin as a debugger and investigator. Participants will hunt for bugs in running applications, trace issues across service boundaries, and fix data-related bugs with proper root cause analysis — working directly in the terminal.

### Lab B1 — Exploratory Bug Hunting

- **Module:** [Fix Runtime Bug](../../modules/application-development/fix-runtime-bug.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js full-stack application
  - [calcom](https://github.com/Cognition-Partner-Workshops/calcom) — Open-source scheduling platform (alternative, more complex)
- **Objective:** Start a running application, explore it to find bugs or unexpected behavior, then have Devin fix what it finds — demonstrating exploratory testing and debugging

#### Step 1: Run the bug hunt with Devin CLI

> **Cloud handoff:** This lab requires starting the application and using a browser for exploratory testing. For full browser-based bug hunting with screenshots, see the [cloud variant](README.md) for this lab. Devin CLI can still analyze the code and fix bugs it identifies through code analysis.

Run `devin` in your terminal and provide the prompt:

```
Analyze timesheet-app for potential bugs. Start by reading the codebase — check error handling patterns, edge cases in date handling, input validation, and API response handling. Focus on the backend API routes and the React frontend state management. Identify the most impactful issues and fix them. Write a `ROOT_CAUSE_ANALYSIS.md` explaining each bug, why it happened, and how you fixed it. Add regression tests.
```

#### Step 2: Explore interactively

While Devin works, ask follow-up questions:
- *"What are the most common types of bugs in Express + React applications? What should I look for?"*
- *"What error handling patterns does timesheet-app use? Are there any gaps?"*
- *"What edge cases are most likely to have bugs — date handling, number formatting, empty states, concurrent access?"*

Use a subagent to investigate a second area in parallel.

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the application architecture:
- Identify components that might have bugs (complex state management, API error handling, date/time logic)
- Look at the data flow from frontend form submission to database write
- Find areas with minimal test coverage — bugs are more likely here

Try different approaches:
- Have Devin add a **regression test** for any bug it finds
- Ask Devin to check for **similar bugs** elsewhere in the codebase
- Try having Devin produce a **debugging narrative** that traces the issue from symptom to root cause

#### Step 4 (Optional): Review & iterate

Review the fixes locally:
- **Root cause:** Does the analysis explain *why* the bug happened, not just *what* was changed?
- **Fix quality:** Does the fix address the root cause or just the symptom?
- **Regression prevention:** Is there a test that will catch this bug if it's reintroduced?

**Iterate interactively:**
- *"Add a regression test for this bug"*
- *"Are there any other places in the codebase that make the same assumption?"*
- *"The fix handles the immediate case but what happens if the input is null?"*

- **Key Takeaways:**
  - **"CLI analyzes code directly"** — Devin CLI reads local files and identifies bugs through code analysis, static checking, and test execution
  - **"Subagents parallelize investigation"** — investigate multiple areas simultaneously with background subagents
  - **"Root cause analysis prevents recurrence"** — understanding *why* a bug happened is more valuable than just fixing it
  - **"Local iteration is fast"** — fix, test, iterate — without waiting for a cloud VM

- **Target Outcomes (any of these count):**
  - Bug identified with root cause analysis
  - Root cause analysis documented in `ROOT_CAUSE_ANALYSIS.md`
  - Fix implemented with regression tests
  - Interactive iteration through CLI conversation
  - Subagent used for parallel investigation

---

### Lab B2 — Cross-Service Bug Investigation

- **Module:** [Fix Runtime Bug](../../modules/application-development/fix-runtime-bug.md) + [Cross-Service Bug Investigation](../../modules/migration-modernization/cross-service-bug-investigation.md)
- **Repositories:**
  - [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices) — decomposed .NET microservices with a planted cross-service bug
  - [eventflow-order-service](https://github.com/Cognition-Partner-Workshops/eventflow-order-service) + [eventflow-payment-service](https://github.com/Cognition-Partner-Workshops/eventflow-payment-service) — EventFlow multi-service application (alternative)
- **Objective:** Investigate a bug that manifests in one service but has its root cause in another — demonstrating Devin's ability to trace issues across service boundaries

#### Step 1: Investigate with Devin CLI

> **Cloud handoff:** This lab benefits from running services locally and testing with HTTP requests. For full service orchestration with browser-based verification and screenshots, see the [cloud variant](README.md) for this lab.

Run `devin` in your terminal and provide the prompt:

**Option A — .NET Microservices (notification amount bug):**
```
Order confirmation notification emails are showing wrong amounts after the microservice decomposition. A $149.99 order shows as $1.50 in the email preview. Investigate this bug in quickapp-microservices by tracing the data flow from the order-service through the shared contracts to the notification-service. Find where the monetary amount gets transformed incorrectly. Check the OrderPlacedEvent.TotalAmount field — is it dollars or cents? Look at the shared contract definition and the notification-service's FormatCurrency logic. Fix the root cause, add unit tests, and document the fix in a root cause analysis.
```

**Option B — EventFlow (currency precision bug):**
```
The EventFlow payment service is incorrectly processing JPY (Japanese Yen) orders. JPY is a zero-decimal currency but the payment service is dividing by 100 like other currencies, resulting in incorrect amounts. Investigate the payment processing logic in eventflow-payment-service, identify where zero-decimal currency handling should be added, fix the bug, and add unit tests covering JPY, USD, and EUR.
```

#### Step 2: Trace data contracts interactively

While Devin works, ask follow-up questions to trace the data flow:
- *"Trace the data flow from when an OrderPlacedEvent is received to when the notification email is rendered. Where does the monetary amount get transformed?"*
- *"What does the OrderPlacedEvent.TotalAmount field represent — dollars or cents? Check the shared contract definition."*
- *"What are zero-decimal currencies and which payment APIs handle them differently? How should JPY amounts be processed?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the event flow between services:
- Trace the data contract from Order service to Notification/Payment service
- Identify shared libraries and their assumptions about data formats
- Look for misleading comments that might be hiding the bug

#### Step 4 (Optional): Review & iterate

Review the fix locally and focus on completeness:
- **Root cause identified:** Is it a data format mismatch, unit conversion error, or shared contract issue?
- **Fix scope:** Does the fix only change the symptom service, or does it address the source of truth?
- **All affected paths:** Are there other services that might have the same bug?

**Iterate interactively:**
- *"Are there any other notification types that format monetary amounts? Check those too."*
- *"Add a contract test that verifies both services agree on the amount format"*
- *"Add a unit test for FormatCurrency that verifies $149.99 renders as '$149.99' and not '$1.50'"*

- **Key Takeaways:**
  - **"Cross-service tracing"** — Devin follows data across service boundaries and shared contracts to find root causes that live outside the symptom's service
  - **"Don't trust comments"** — the cross-service bug includes a misleading code comment. Devin learns to verify against the actual data contract
  - **"Shared contracts are the source of truth"** — when services disagree, the contract definition is authoritative
  - **"Contract tests prevent regression"** — adding tests that verify inter-service agreements catches future mismatches early

- **Target Outcomes (any of these count):**
  - Root cause identified across service boundaries
  - Fix applied to the correct service (source of the bug, not just the symptom)
  - Contract test or regression test added
  - Interactive trace of the data flow through CLI conversation
  - Root cause analysis documenting the data flow

---

### Lab B3 — Data Bug & Schema Fix

- **Modules:** [Fix Data Bug](../../modules/application-development/fix-data-bug.md) + [Database Schema Evolution](../../modules/application-development/database-schema-evolution.md)
- **Repositories:**
  - [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization) — Spring Boot loan service with legacy CDW-style tables
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app with SQLite (alternative)
- **Objective:** Investigate and fix a data-related bug — incorrect query results, type conversion errors, or schema inconsistencies — and evolve the schema to prevent recurrence

#### Step 1: Investigate with Devin CLI

Run `devin` in your terminal and provide the prompt:

**Option A — Legacy Data Types (loan service):**
```
The loan service in uc-data-source-migration-jdbc-normalization is reading from legacy CDW-style tables where all columns are VARCHAR (including amounts, dates, and IDs). Some loan balance queries return incorrect results because string comparison is used instead of numeric comparison — for example, a loan with balance "99.50" appears as greater than one with balance "1000.00" because string sort puts "9" after "1". Identify all repository queries that compare or sort numeric VARCHAR fields. Fix the queries to properly cast to numeric types. Add JUnit tests that verify correct ordering and comparison. Document the fix in a `DATA_BUG_ANALYSIS.md`.
```

**Option B — SQLite Type Issues (timesheet app):**
```
Investigate the date handling in timesheet-app's backend. SQLite stores dates as text, which can cause issues with date range queries, sorting, and timezone handling. Find any bugs related to date storage or retrieval (e.g., work entries on boundary dates not appearing in reports, timezone-related off-by-one errors). Fix the issues and add tests that verify correct behavior across date boundaries.
```

#### Step 2: Explore the data layer interactively

While Devin works, ask follow-up questions:
- *"What are the most common bugs when reading from all-VARCHAR legacy tables? How do type coercion issues manifest?"*
- *"What's the correct way to handle numeric comparison in JPA/JPQL when the underlying column is VARCHAR?"*
- *"What SQLite-specific date handling gotchas should I be aware of?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the data layer. Try:
- Have Devin add **database constraints** that prevent invalid data from being stored
- Ask Devin to create a **data validation migration** that checks existing data for type issues
- Try having Devin add **query-level assertions** that verify return types match expectations

#### Step 4 (Optional): Review & iterate

Review the fixes locally and focus on **data correctness**:
- **Query fix:** Are all numeric comparisons now using proper casting?
- **Migration safety:** Will the schema change work with existing data?
- **Test coverage:** Do tests cover the boundary conditions that triggered the original bug?

**Iterate interactively:**
- *"What happens if a VARCHAR field contains non-numeric data like 'N/A'? Add error handling."*
- *"Add a test with the specific values that triggered the original bug"*
- *"Create a database migration that adds CHECK constraints to prevent future invalid data"*

- **Key Takeaways:**
  - **"Data bugs are subtle"** — they produce wrong results without throwing errors, making them harder to detect than crashes
  - **"Legacy schemas require defensive coding"** — when the database doesn't enforce types, the application must
  - **"Schema evolution is part of the fix"** — fixing the query is necessary, but adding constraints prevents recurrence
  - **"Tests must use realistic data"** — test data that doesn't hit boundary conditions won't catch the bugs that matter

- **Target Outcomes (any of these count):**
  - Data bug identified with specific examples of incorrect behavior
  - Query or schema fix applied with proper type handling
  - `DATA_BUG_ANALYSIS.md` documenting the issue and fix
  - Tests with boundary-condition data proving the fix
  - Interactive investigation through CLI conversation

---

## Track C: Maintenance & Evolution

Track C demonstrates Devin as a maintenance engineer. Participants will set up automated dependency upgrades, evolve database schemas safely, and refactor tech debt — with fast local iteration in the terminal.

### Lab C1 — Dependency Upgrades & Scheduled Maintenance

- **Modules:** [Upgrade Dependencies](../../modules/security/upgrade-dependencies.md)
- **Repositories:**
  - [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) — Spring Boot app with Gradle
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — Node.js app with npm (alternative)
- **Objective:** Upgrade dependencies to latest minor/patch versions, verify the build passes, and set up a recurring Devin scheduled session for automated weekly maintenance

#### Step 1: Upgrade with Devin CLI

Run `devin` in your terminal and provide the prompt:

```
Check all dependencies in uc-cve-remediation-regulatory-compliance for available minor and patch version updates. Upgrade each dependency to the latest minor version (do not jump major versions). Run `./gradlew build` and `./gradlew test` to verify the build still passes after each upgrade. If any upgrade breaks the build, revert that specific upgrade and document it.md` listing what was upgraded, from which version to which version, and any upgrades that were skipped (with reasons). Title the PR "chore: weekly dependency version bump".
```

Devin CLI runs the build and tests locally, giving you immediate feedback on each upgrade.

#### Step 2: Research interactively

While Devin works, ask follow-up questions:
- *"What dependencies in uc-cve-remediation-regulatory-compliance are the most out of date? Which ones have the most available minor/patch updates?"*
- *"What's the difference between minor and patch version upgrades in terms of risk? When is it safe to auto-upgrade vs. requiring human review?"*
- *"How should a team handle dependency upgrades that break the build — auto-revert and flag, or block and notify?"*
- Consider creating a **Devin Knowledge item** capturing the dependency upgrade policy

#### Step 3 (Optional): Set Up a Scheduled Session

> **Cloud handoff:** Scheduled sessions require Devin Cloud. See the [cloud variant](README.md) for this lab to set up recurring automated maintenance.

Once you're happy with the output from step 1, you can hand off the recurring task to Devin Cloud:

```
Create a Devin scheduled session that runs weekly on Monday mornings against uc-cve-remediation-regulatory-compliance. The schedule should use this prompt: "Check all dependencies for available minor and patch version updates. Upgrade to the latest minor versions. Run the full test suite and build to verify nothing is broken. If any upgrade breaks the build, revert that specific upgrade and note it.md."
```

#### Step 4 (Optional): Extend to Multiple Repos

Try running the same pattern for **timesheet-app** with an npm-flavored prompt — use a subagent to run both upgrades in parallel:

```
Check all npm dependencies in timesheet-app for available minor and patch version updates. Run `npm update` to upgrade to latest minor versions. Run `npm test` and `npm run build` to verify everything still works.md`.
```

- **Key Takeaways:**
  - **"Automate the boring stuff"** — dependency upgrades are repetitive, low-risk, and high-volume. Devin handles them without manual intervention
  - **"Local iteration is fast"** — run builds and tests locally for immediate feedback on each upgrade
  - **"Subagents scale locally"** — run upgrades across multiple repos in parallel with background subagents
  - **"Cloud for recurring tasks"** — hand off the schedule to Devin Cloud for automated weekly maintenance

- **Target Outcomes (any of these count):**
  - Dependencies upgraded with local build verification
  - PR with dependency upgrades and `DEPENDENCY_UPDATES.md`
  - Knowledge item capturing the team's dependency upgrade policy
  - Subagent used for parallel upgrades across repos

---

### Lab C2 — Database Schema Evolution

- **Module:** [Database Schema Evolution](../../modules/application-development/database-schema-evolution.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot with Flyway migrations and SQLite
  - [ts-java-angular-jhipster](https://github.com/Cognition-Partner-Workshops/ts-java-angular-jhipster) — JHipster app with Liquibase (alternative)
- **Objective:** Evolve a database schema safely — add new tables, modify existing columns, migrate data, and ensure backward compatibility with the application layer

#### Step 1: Implement with Devin CLI

Run `devin` in your terminal and provide the prompt:

**Option A — Flyway Migrations (Spring Boot):**
```
Evolve the database schema of uc-spring-boot-upgrade-microservice-extraction to support article tags as a first-class entity. Currently tags are stored as strings in the articles table. Create Flyway migration scripts that: (1) Create a new `tags` table with id and name columns, (2) Create a junction table `article_tags` for the many-to-many relationship, (3) Migrate existing tag data from the articles table to the new structure, (4) Update the MyBatis mappers and Java domain model to use the new schema. Ensure existing tests still pass and write new tests for the tag CRUD operations.
```

**Option B — Liquibase Changesets (JHipster):**
```
Add a "transaction categories" feature to ts-java-angular-jhipster. Create Liquibase changesets that: (1) Add a `category` table with id, name, color, icon, and user_id, (2) Add a category_id foreign key to the operation table, (3) Create seed data with default categories (Income, Groceries, Transport, Entertainment). Update the JPA entities, Spring Data repositories, and REST controllers to support CRUD on categories and category assignment to operations.
```

Run tests locally to verify the migration: `./gradlew test` or `./mvnw test`.

#### Step 2: Explore the schema interactively

While Devin works, ask follow-up questions:
- *"What's the current database schema? What tables and relationships exist?"*
- *"What's the safest way to add a foreign key to an existing table with data — should we use a nullable FK first?"*
- *"What are the best practices for data migration scripts — how do we handle failures and rollbacks?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add a **rollback migration** that reverses the schema change
- Ask Devin to add **database-level constraints** (CHECK, UNIQUE, NOT NULL) with appropriate handling
- Try having Devin create a **schema documentation** file showing the current ERD

#### Step 4 (Optional): Review & iterate

Focus on **migration safety** and iterate interactively:
- **Ordering:** Are migrations numbered correctly and idempotent?
- **Data preservation:** Does the data migration handle edge cases (null tags, duplicate tags)?
- **Backward compatibility:** Would the old application code still work during the migration window?

**Iterate interactively:**
- *"What happens if the migration fails halfway through? Add transaction boundaries."*
- *"The data migration should handle articles with no tags — verify with a test"*
- *"Add an index on the junction table for query performance"*

- **Key Takeaways:**
  - **"Schema changes require migrations, not DDL"** — Devin creates proper versioned migration scripts, not ad-hoc SQL
  - **"Data migration is part of schema evolution"** — restructuring a column means migrating existing data, not just changing the DDL
  - **"Tests verify the full path"** — both the migration script and the application code must work together
  - **"Backward compatibility matters"** — in production, zero-downtime deployments require migrations that work with both old and new application versions

- **Target Outcomes (any of these count):**
  - Versioned migration scripts (Flyway or Liquibase)
  - Data migration handling existing records
  - Updated domain model and repository layer
  - Tests verifying both migration and application behavior
  - Local build and test verification

---

### Lab C3 — Code Refactoring & Tech Debt

- **Module:** [Code Refactoring & Tech Debt](../../modules/architecture-design/code-refactoring-tech-debt.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot monolith with identifiable tech debt
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app (alternative)
- **Objective:** Identify and refactor tech debt in an existing codebase — extract methods, improve naming, reduce duplication, and improve testability — while preserving behavior

#### Step 1: Refactor with Devin CLI

Run `devin` in your terminal and provide the prompt:

**Option A — Spring Boot Refactoring:**
```
Analyze uc-spring-boot-upgrade-microservice-extraction for code quality issues. Focus on the Articles domain: identify long methods, duplicated code, unclear naming, missing error handling, and tight coupling. Refactor the top 5 most impactful issues. For each refactoring: (1) explain what the problem was, (2) describe the refactoring approach, (3) verify existing tests still pass after the change. Document all changes in a `REFACTORING_LOG.md`.
```

**Option B — React/Node.js Refactoring:**
```
Analyze timesheet-app for code quality issues. Focus on: duplicated API call patterns that could use a shared utility, React components that are too large (> 200 lines) that should be split, inconsistent error handling across API routes, and any remaining TODO comments in the code. Refactor the top 5 most impactful issues while keeping all existing behavior intact. Run tests to verify nothing broke. Document changes in a `REFACTORING_LOG.md`.
```

Watch refactorings happen locally — run tests after each change to verify behavior preservation.

#### Step 2: Explore code quality interactively

While Devin works, ask follow-up questions:
- *"What are the most common code smells in this codebase? Which files have the highest complexity?"*
- *"What refactoring patterns are most applicable — Extract Method, Replace Conditional with Polymorphism, Introduce Parameter Object?"*
- *"Which refactorings are safest to do without comprehensive test coverage?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add **tests before refactoring** (characterization tests) to ensure behavior preservation
- Ask Devin to measure **cyclomatic complexity** before and after refactoring
- Try having Devin produce a **dependency graph** showing coupling between modules

#### Step 4 (Optional): Review & iterate

Focus on **behavior preservation** and iterate:
- **Same behavior:** Do all existing tests still pass? Run them locally
- **Meaningful improvement:** Does the refactoring make the code genuinely more maintainable?
- **Right scope:** Was each refactoring small enough to be easily reviewed?

**Iterate interactively:**
- *"Can you split this into separate commits — one refactoring per commit?"*
- *"This extracted method should be tested independently"*
- *"The naming improvement is good but let's also add a brief doc comment explaining the business logic"*

- **Key Takeaways:**
  - **"Refactoring preserves behavior"** — every change must pass existing tests. No new functionality, just improved structure
  - **"Small, atomic changes"** — each refactoring should be reviewable in isolation. Devin commits them separately
  - **"Tech debt compounds"** — small improvements over time prevent the codebase from becoming unmaintainable
  - **"Local testing is immediate"** — run tests after each refactoring without waiting for CI

- **Target Outcomes (any of these count):**
  - 5+ refactorings applied with tests still passing
  - `REFACTORING_LOG.md` documenting each change and rationale
  - Measurable improvement (reduced complexity, fewer duplications)
  - Separate commits for each refactoring (reviewable individually)
  - Interactive refinement through CLI conversation

---

## Additional Challenges

Participants who finish early or want to explore further can attempt any challenge from the full [module catalog](../../modules/). Recommended extras:

| Challenge | Module | Repo | Track | Difficulty |
|-----------|--------|------|-------|------------|
| Fix UI Bug | [Fix UI Bug](../../modules/application-development/fix-ui-bug.md) | timesheet-app | B | Beginner |
| Test-Driven Development | [TDD](../../modules/application-development/test-driven-development.md) | uc-spring-boot-upgrade-microservice-extraction | A | Intermediate |
| CI/CD Pipeline | [CI/CD Pipeline](../../modules/devops-cicd/cicd-pipeline.md) | timesheet-app | C | Intermediate |
| PR Review Automation | [PR Review](../../modules/devops-cicd/pr-review-automation.md) | Any | A | Beginner |
| API Documentation | [API Documentation](../../modules/technical-documentation/api-documentation.md) | Any | C | Beginner |

## Suggested Formats

| Format | Recommended Approach |
|--------|---------------------|
| Full day | All 3 tracks: Track A (morning) + Track B (midday) + Track C (afternoon) |
| Half day | 2 tracks: Choose any two tracks based on audience interest |
| Short session | 1 full track + 1 lab from another track |
| Sampler | Pick 1 lab from each track (e.g., A1 + B1 + C1) for breadth |
| Single lab | A1 (feature dev) or B1 (bug hunting) recommended for immediate impact |

## Repos Required

### Track A (Feature Development)
- [ ] timesheet-app
- [ ] uc-spring-boot-upgrade-microservice-extraction
- [ ] ts-java-angular-jhipster (optional, for Lab A1 Option B)
- [ ] uc-bdd-test-generation-cucumber (optional, for Lab A2 Option B)
- [ ] uc-data-source-migration-jdbc-normalization (optional, for Lab A3)

### Track B (Bug Fixing & Root Cause Analysis)
- [ ] timesheet-app
- [ ] quickapp-microservices (for Lab B2 Option A)
- [ ] eventflow-order-service + eventflow-payment-service (optional, for Lab B2 Option B)
- [ ] uc-data-source-migration-jdbc-normalization (for Lab B3)

### Track C (Maintenance & Evolution)
- [ ] uc-cve-remediation-regulatory-compliance
- [ ] timesheet-app (optional, for Lab C1 npm variant)
- [ ] uc-spring-boot-upgrade-microservice-extraction (for Labs C2, C3)
- [ ] ts-java-angular-jhipster (optional, for Lab C2 Option B)

## Context

- **Audience:** Development teams responsible for shipping features and maintaining production applications
- **Focus:** The full application lifecycle — building, fixing, and maintaining — using Devin CLI for fast, interactive, terminal-native workflows
- **Delivery surface:** Devin CLI / Devin Local — participants work directly in their terminal with local file access, shell integration, and subagents for parallel work
- **Devin value themes woven throughout:**
  - Terminal-native workflow — fast, interactive, local-first
  - Subagents for parallel subtask execution
  - Shell integration for context-aware assistance
  - Cloud handoff for long-running autonomous tasks
  - Knowledge and Playbooks for capturing team conventions
  - ACP compatibility — CLI plugs into Desktop as "Devin Local"
  - Same skills and MCP integrations work across CLI, Desktop, and Cloud

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../modules/devin-features/README.md) throughout the session.
