# Workshop: Enterprise Modernization — Hands-On with Devin

## Event Details

| | |
|---|---|
| **Date** | 2026-06 (TBD) |
| **Location** | TBD |
| **Host Organization** | *(customer)* |
| **Duration** | 2 hours |
| **Audience** | Development teams experiencing Devin hands-on for the first time |
| **Tracks** | Single progressive track: Migrate → Harden → Decompose → Build |
| **Event Site** | TBD |

## Workshop Overview

This is a hands-on workshop for teams getting their first experience with Devin. The labs are structured as a progressive ramp — starting with a test-driven frontend migration, building to security remediation and a monolith decomposition, and finishing with greenfield feature development. By the end, participants will have used Devin to write migration tests first and then migrate an Angular frontend to React (TDD style), remediate critical CVEs, extract a microservice from a monolith, and take a feature idea from requirements through implementation.

The workshop uses a mix of frontend, backend, and full-stack repositories. Each lab builds on a different Devin capability — test-driven migration, security remediation, architectural decomposition, and end-to-end feature development — so participants see the breadth of what Devin can do in a real engineering workflow.

> **Note:** This workshop runs against an external GitHub organization. The repos listed below will be available in your organization's GitHub workspace.

## Getting the Most from This Workshop

> **Devin works autonomously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Each lab has a "Paste into Devin" step and a separate "Review & Give Feedback" step. Kick off the session first, then use the wait time for Ask Devin research — Devin will keep working in the background.
- **Overlap sessions.** Kick off Lab 2's Devin session while reviewing Lab 1's PR. Devin works in the background — there's no reason to wait. This is how teams use Devin in production.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem first so Devin can execute with less back-and-forth.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments directly and Devin will wake up and address them — this is the core workflow for iterating with Devin in production.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item during a session, accept it — this is how teams build a shared context layer that makes Devin smarter over time.

### Quick Start (experienced attendees)

Already comfortable with Devin basics? Jump straight to the labs:

1. Pick a lab: [Lab 1 (Angular → React)](#lab-1--angular-to-react-ui-migration-15-min), [Lab 2 (Security)](#lab-2--security-vulnerabilities-remediation-15-min), [Lab 3 (Monolith Extraction)](#lab-3--monolith-to-microservices-extraction-15-min), or [Lab 4 (New Product Dev)](#lab-4--new-product-development-ideas-to-deployment-10-min)
2. Copy the prompt from the lab and paste it into a new Devin session
3. While Devin works, try the Ask Devin prompts to explore the codebase
4. Review the PR when Devin finishes, leave comments, and iterate

---

## Table of Contents

- [Agenda](#agenda)
- [Lab 1 — Angular to React UI Migration](#lab-1--angular-to-react-ui-migration-15-min)
- [Lab 2 — Security Vulnerabilities Remediation](#lab-2--security-vulnerabilities-remediation-15-min)
- [Lab 3 — Monolith-to-Microservices Extraction](#lab-3--monolith-to-microservices-extraction-15-min)
- [Lab 4 — New Product Development (Ideas to Deployment)](#lab-4--new-product-development-ideas-to-deployment-10-min)
- [Post-Session Exercises](#post-session-exercises)
- [Known Limitations](#known-limitations)
- [Repos Required](#repos-required)
- [Devin Features Checklist](#devin-features-checklist)

---

## Agenda

| Time | Activity | Lab |
|------|----------|-----|
| 0:00 | Welcome, Devin overview, platform walkthrough | — |
| 0:15 | **Kick off Labs 1–4** — paste all prompts back-to-back (~8 min) | [Labs 1](#lab-1--angular-to-react-ui-migration-15-min)–[4](#lab-4--new-product-development-ideas-to-deployment-10-min) |
| 0:23 | **While Devin works:** Ask Devin prompts + DeepWiki exploration | — |
| 0:40 | **Review PRs**, leave comments, iterate with Devin | — |
| 1:10 | Continue reviewing + second-round PR comments | — |
| 1:40 | Wrap-up, showcase results, Q&A | — |

> **Timing note:** Kick off all four labs in the first 8 minutes — each is a copy-paste prompt (~2 min each). While Devin works on all four simultaneously, use Ask Devin to explore the codebases. PRs start arriving around 0:25–0:35. The bulk of hands-on time (0:40–1:40) is spent reviewing PRs, leaving comments, and watching Devin iterate. By wrap-up, most participants will have 3–4 PRs to showcase.

---

<a id="lab-1"></a>
## Lab 1 — Angular to React UI Migration (15 min)

**Value driver:** *Devin maps a backend API contract, writes a migration test suite as the specification, then rewrites an Angular frontend in React to pass those tests — a true test-driven migration. Attendees then drive the iterative verification through PR feedback.*

- **Repository:** [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular)
- **Modules:** [Framework Upgrade](../../../labs/migration-modernization/framework-upgrade.md)

The PetClinic Angular frontend is a full-featured veterinary clinic management UI with owners, pets, visits, vets, and specialties modules. It uses Angular 16, Angular Material, Bootstrap, RxJS, and template-driven forms. The app consumes a REST API at `localhost:9966/petclinic/api/` with 6 service files defining the full endpoint surface. Participants will ask Devin to map that API, write migration tests that define the expected behavior, and then migrate the React app to pass those tests — a true TDD workflow.

> **Why TDD for migration?** Writing tests first defines the "contract" the React app must satisfy. The migration is then guided by concrete, failing tests rather than subjective interpretation of the Angular code. This produces fewer integration mismatches and gives reviewers a clear pass/fail signal.

### Paste into Devin

```
Migrate the petclinic-angular frontend from Angular to React
using a test-driven approach. Scope to the three
interconnected modules: owners, pets, and visits.

1. **Map the API contract:** Read the Angular service files
   and TypeScript interfaces for owners, pets, and visits.
   Produce docs/API_CONTRACT.md documenting each REST
   endpoint: HTTP method, URL pattern, request/response
   shapes, and error handling. Base URL is
   http://localhost:9966/petclinic/api/. Commit this to a
   new branch.

2. **Write migration tests first (Red phase):** Create
   React Testing Library + MSW tests in
   react-frontend/src/__tests__/ validating owner CRUD,
   pet management, and visit creation against the API
   contract. Tests must use the base URL
   http://localhost:9966/petclinic/api/ in MSW handlers.
   Use route params :ownerId, :petId, :visitId. Commit
   the tests — they should fail (no React app yet).

3. **Migrate to pass the tests (Green phase):** Migrate
   the owners, pets, and visits modules to React 18+/
   TypeScript/Vite in react-frontend/. Configure axios
   baseURL to http://localhost:9966/petclinic/api/.
   Use the same route param names as the tests. Follow
   the AGENTS.md coding standards in the repo. Run the
   tests from step 2 — fix the React components until
   all tests pass. npm run build must also pass.
```

### While Devin works: try Ask Devin

Open **Ask Devin** and explore the Angular codebase:
- *"What REST API endpoints does petclinic-angular consume? Map the service files and the HTTP methods each uses."*
- *"What Angular-specific patterns does petclinic-angular use that don't have direct React equivalents (e.g., NgModules, resolvers, template-driven forms)?"*
- *"What would be the riskiest parts of migrating this app from Angular to React? Where would functional parity be hardest to achieve?"*

### Review the PR

When Devin opens a PR:
- Does `API_CONTRACT.md` accurately reflect the endpoints in the Angular service files?
- Were the tests written before the migration code? Check the commit history for the TDD sequence (tests committed first, then React components)
- Do all migration tests pass? If not, leave a comment: *"Fix the failing migration tests — adjust the React components until all tests pass"*
- Does the React app preserve the same routes and page structure as the Angular original?
- **Leave a comment** asking Devin to migrate the vets and specialties modules to complete the full app
- **Leave a comment** asking Devin to add accessibility attributes (aria-labels) to the migrated forms

### Key Takeaways

- **"Map before you migrate"** — Devin's `API_CONTRACT.md` documents the backend surface before any code is rewritten, making the migration auditable and reviewable
- **"Tests drive the migration"** — the MSW-backed test suite is written first, defining the contract the React app must satisfy. The migration is then guided by concrete, failing tests — true TDD
- **"Red → Green → Review"** — Devin writes failing tests (red), implements until they pass (green), and attendees review the result. This is TDD applied to frontend migration
- **"Iterative verification via PR feedback"** — attendees drive the fix cycle by leaving PR comments when tests fail, showing how the PR feedback loop works in production

### Target Outcomes (any of these count)

- `docs/API_CONTRACT.md` documenting the REST endpoints the Angular app consumes
- Migration test suite in `react-frontend/src/__tests__/` with MSW handlers
- React 18+ app in `react-frontend/` with Vite + TypeScript
- Migration tests passing against the React app
- Owners, pets, and visits modules migrated (vets and specialties added via PR comment)
- `npm run build` passing with zero TypeScript errors
- PR with migration artifacts and Devin's responses to review comments

---

<a id="lab-2"></a>
## Lab 2 — Security Vulnerabilities Remediation (15 min)

**Value driver:** *Devin identifies vulnerable dependencies, upgrades them, fixes breaking API changes, runs tests to verify, and documents the security impact — the triage-fix-verify loop that normally takes a security engineer days.*

- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Modules:** [Remediate Vulnerabilities](../../../labs/security/remediate-vulnerabilities.md), [Shift Left Security](../../../labs/security/shift-left-security.md)

This Spring Boot 2.6.3 application ships with known CVEs including Spring4Shell (CVSS 9.8), SnakeYAML unsafe deserialization (CVSS 9.8), and multiple Spring Security bypasses. OWASP Dependency-Check and SonarQube are pre-configured as Gradle plugins.

> **Timing note:** The full OWASP `dependencyCheckAnalyze` scan downloads the NVD database on first run and can take 15–20+ minutes. The core prompt below skips the scan and has Devin analyze `build.gradle` directly for faster results. You can request a full scan as a PR comment follow-up.

### Paste into Devin

```
Perform a security assessment of
uc-cve-remediation-regulatory-compliance and remediate the
most critical findings.

1. **Triage:** Review `build.gradle` to identify the
   outdated dependencies and their known CVEs. The key
   vulnerabilities are:
   - Spring Boot 2.6.3 (Spring4Shell CVE-2022-22965,
     CVSS 9.8)
   - SnakeYAML 1.29 (CVE-2022-1471, CVSS 9.8 —
     transitive via Spring Boot)
   - sqlite-jdbc 3.36.0.3 (multiple CVEs)
   - Spring Security 5.6.1 (CVE-2022-22978,
     CVE-2022-31692, CVSS 9.8)
   Create `SECURITY_TRIAGE.md` documenting each finding
   with CVE ID, severity, affected version, and fix
   version.

2. **Remediate:** Upgrade Spring Boot from 2.6.3 to 2.7.18
   (resolves Spring4Shell CVE-2022-22965), override
   SnakeYAML to 2.0+ (resolves CVE-2022-1471), and
   sqlite-jdbc to 3.42+. Fix any breaking API changes
   from the upgrades (note: Spring Boot 2.7 deprecates
   WebSecurityConfigurerAdapter — migrate to
   SecurityFilterChain @Bean method). Ensure
   `./gradlew build` passes.

3. **Document:** Create `SECURITY_REMEDIATION.md` with a
   before/after findings table (CVE ID, severity, old
   version, new version, status).
```

### While Devin works: try Ask Devin

- *"What are the known CVEs in uc-cve-remediation-regulatory-compliance's dependencies? Which ones are CRITICAL severity?"*
- *"What's the safest upgrade path for Spring Boot 2.6.3 — should we go to 2.7.x first or jump straight to 3.x?"*
- *"Beyond dependency CVEs, are there any code-level security issues in this repo (SQL injection, insecure deserialization, hardcoded credentials)?"*

### Review the PR

When Devin opens a PR:
- Did Devin address both CRITICAL and HIGH findings?
- Does `SECURITY_REMEDIATION.md` show before/after evidence for each CVE?
- **Leave a comment:** *"Add a GitHub Actions workflow that runs OWASP Dependency-Check on every PR and fails on CVSS >= 7.0"* — watch Devin add shift-left security to the repo
- **Leave a comment:** *"Also add SBOM generation in CycloneDX format to the CI pipeline"*

### Key Takeaways

- **"Triage → fix → verify"** — Devin identifies the vulnerable dependencies, upgrades them, fixes the breaking API changes, and runs the test suite to verify nothing regressed
- **"Shift left via PR feedback"** — attendees ask Devin to add CI scanning as a PR comment, showing how security automation can be added iteratively
- **"Evidence-based compliance"** — the `SECURITY_REMEDIATION.md` provides auditable proof that remediation was effective

### Target Outcomes (any of these count)

- `SECURITY_TRIAGE.md` documenting known CVEs with severity and fix versions
- `SECURITY_REMEDIATION.md` with before/after evidence
- Build and tests passing after dependency upgrades
- SecurityFilterChain migration completed (WebSecurityConfigurerAdapter removed)
- GitHub Actions CI workflow added via PR comment follow-up
- PR with remediations and Devin's responses to review comments

---

<a id="lab-3"></a>
## Lab 3 — Monolith-to-Microservices Extraction (15 min)

**Value driver:** *Devin analyzes a monolith's domain boundaries, documents extraction decisions, extracts a bounded context into a standalone service, and wires up cross-service communication — typically in a single session.*

- **Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)
- **Modules:** [Containerization & Microservice Extraction](../../../labs/migration-modernization/containerization-microservice-extraction.md)

This is a Spring Boot 2.6.3 / Java 11 monolith implementing the RealWorld blogging platform (Conduit) with 4 domain contexts: articles/tags, comments, favorites, and users/profiles. It has REST and GraphQL (DGS) APIs, MyBatis persistence with SQLite, Flyway migrations, 27 test files with an 80% JaCoCo coverage gate, and a Next.js frontend. Participants will ask Devin to analyze domain boundaries, document extraction decisions, and extract a bounded context into a standalone service.

> **Scope note:** The prompt extracts articles and tags only (not comments and favorites). This keeps the extraction focused and reduces coupling complexity. Comments and favorites can be extracted as a PR comment follow-up.

### Paste into Devin

```
Extract the Article bounded context from
uc-spring-boot-upgrade-microservice-extraction — a Spring
Boot 2.6.3 monolith with REST and GraphQL APIs, MyBatis
persistence, and a Next.js frontend.

1. **Read the codebase:** Identify the 4 bounded contexts
   (articles/tags, comments, favorites, users/profiles)
   and their coupling points. The repo has an AGENTS.md
   with extraction standards — follow those patterns.

2. **Document extraction decisions:** Create
   docs/EXTRACTION_DECISIONS.md documenting: which domain
   objects move to the Article service, what stays, coupling
   points between domains, and the cross-service
   communication strategy.

3. **Extract articles and tags:** Create article-service/ —
   a standalone Spring Boot service with its own
   build.gradle, Flyway migrations (V1__create_tables.sql),
   and MyBatis persistence. Include articles and tags.
   Replace direct User domain calls with a REST client
   and DTOs (see AGENTS.md for patterns). The extracted
   service runs on port 8081 with its own SQLite database.
   Run `./gradlew build -x jacocoTestCoverageVerification`
   for both services to verify they compile independently.
```

### While Devin works: try Ask Devin

- *"What are the domain boundaries in uc-spring-boot-upgrade-microservice-extraction? Which packages would form a natural microservice?"*
- *"What coupling exists between the Article domain and the User domain? How would you handle cross-service queries after extraction?"*
- *"What are the risks of extracting the Article bounded context? What shared infrastructure (security, database, Flyway) needs to be duplicated?"*

### Review the PR

When Devin opens a PR:
- Does `EXTRACTION_DECISIONS.md` clearly justify which domain objects moved and which stayed?
- Does the article-service have its own independent build that compiles?
- Is cross-service communication implemented via REST client (not shared database queries)?
- **Leave a comment:** *"Also extract the comments and favorites domains into article-service — add the database migrations, update the REST client, and ensure both services still build"* — watch Devin expand the extraction scope iteratively
- **Leave a comment:** *"Add a docker-compose.yml that runs both the main app and article-service with container networking"* — watch Devin containerize the decomposed system
- **Leave a comment:** *"Add a circuit breaker pattern to the REST client calls between services — what happens if article-service is down?"*

### Key Takeaways

- **"Analysis before extraction"** — Devin reads the monolith, identifies bounded contexts, and writes a reviewable extraction decisions document before cutting any code
- **"Existing tests as a safety net"** — the monolith's test suite verifies that extraction didn't break functionality
- **"Service contracts, not shared databases"** — the extracted service communicates via REST DTOs, not direct database access — the right pattern for real microservices
- **"Containerization via PR feedback"** — attendees ask Devin to add Docker Compose as a follow-up, showing iterative architecture work through PR comments

### Target Outcomes (any of these count)

- `docs/EXTRACTION_DECISIONS.md` documenting domain boundary analysis and trade-offs
- Standalone `article-service/` with its own build configuration, migrations, and persistence
- REST client + DTOs for cross-service communication (articles + tags extracted)
- `./gradlew build -x jacocoTestCoverageVerification` passing for both services
- Comments and favorites extraction added via PR comment follow-up
- `docker-compose.yml` added via PR comment follow-up
- PR with extraction artifacts and Devin's responses to review comments

---

<a id="lab-4"></a>
## Lab 4 — New Product Development: Ideas to Deployment (10 min)

**Value driver:** *Devin takes a feature specification and builds a full-stack feature end-to-end — backend API, database migration, frontend UI, and tests — following existing codebase conventions.*

- **Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Modules:** [Gather Requirements](../../../labs/application-development/gather-requirements.md), [New Feature Development](../../../labs/application-development/new-feature-development.md), [Shift Left Security](../../../labs/security/shift-left-security.md)

The timesheet app is a React 19 + Node.js/Express + SQLite application for tracking billable hours. It has existing CRUD features for clients and work entries. Participants will ask Devin to build a new feature following existing patterns.

### Paste into Devin

```
Add a "Projects" management feature to timesheet-app. This
is a React 19 + Node.js/Express + SQLite app for tracking
billable hours.

Build the full feature following existing patterns — backend
Express routes, SQLite migration (Projects table: id, name,
description, client_id FK, start_date, end_date, status
[active/completed/on-hold], budget_hours), frontend React
components with MUI styling. Link projects to existing
clients and work entries. Include backend API tests.
Run `npm test` to verify all tests pass.
```

**Optional: Session B — Requirements for a Second Feature (kick off in a separate session if time permits):**
```
Analyze timesheet-app and propose a "Dashboard & Analytics"
feature. Create a detailed GitHub Issue with:
- User stories for a dashboard showing: total hours this week/
  month, hours by client breakdown (pie chart), hours trend
  over time (line chart), top projects by hours, utilization
  rate (logged hours vs. available hours)
- Technical design: which charting library to use (evaluate
  Recharts vs. Chart.js vs. Nivo), API endpoints needed for
  aggregation queries, database query designs
- Acceptance criteria for each dashboard widget
- Estimated implementation complexity for each component

Do not implement — just produce the requirements document as a
GitHub Issue. This shows how Devin can do technical discovery
and requirements gathering.
```

### While Devin works: try Ask Devin

- *"What patterns do the existing CRUD features (clients, work entries) follow in timesheet-app? What conventions should a new feature match?"*
- *"What database migration approach does the app use? How should I add a new table?"*
- *"What would a production deployment of timesheet-app look like? What infrastructure would it need?"*

### Review the PR

When Devin opens a PR:
- Does the Projects feature follow the same patterns as existing features (clients, work entries)?
- **Leave a comment:** *"Add input validation for budget_hours — it should be a positive number. Also add a project status filter to the list view."*
- **Leave a comment:** *"Add a Dockerfile (multi-stage build) and a GitHub Actions CI/CD workflow that builds, tests, and produces a container image"* — watch Devin add deployment artifacts iteratively
- **Leave a comment:** *"Create a GitHub Issue titled 'Feature: Project Management' documenting the user stories, acceptance criteria, and API specifications for this feature"* — shows Devin generating requirements docs from implemented code

### Key Takeaways

- **"Specification to implementation"** — Devin takes a feature spec with a data model and builds the full stack in a single session
- **"Pattern matching across features"** — Devin analyzes existing CRUD features and replicates the conventions for the new feature, typically maintaining consistency
- **"Requirements as a deliverable"** — the optional Session B shows Devin doing pure technical discovery and requirements gathering without writing code
- **"Deployment via PR feedback"** — attendees ask Devin to add a Dockerfile and CI workflow as a follow-up comment, showing how deployment artifacts are added iteratively

### Target Outcomes (any of these count)

- New "Projects" feature with backend API + frontend UI
- Database migration script for the Projects table
- Backend API tests (Jest + Supertest)
- GitHub Issue with requirements (Session B or via PR comment)
- Dockerfile and CI/CD workflow added via PR comment follow-up
- PR with feature implementation and Devin's responses to review comments

---

## Post-Session Exercises

Participants who want to keep exploring after the workshop can try these additional use cases on their own. Each is self-contained with a copy-pasteable prompt.

### Exercise A: Test-Driven Development (TDD)

- **Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Module:** [Test-Driven Development](../../../labs/application-development/test-driven-development.md)
- **Shows:** Devin writing failing tests from a feature specification, then implementing the feature to make them pass — a two-session TDD workflow

#### Paste into Devin

**Session 1 — Write Tests:**

```
I want to add a "duplicate work entry" feature to
timesheet-app. Write failing Jest tests for a new
POST /api/work-entries/:id/duplicate endpoint that
creates a copy of an existing work entry with today's
date.

Test: successful duplication, 404 for non-existent
entry, 403 for entry owned by another user. Commit
the tests to a new branch.
```

**Session 2 — Implement:**

```
The branch feature/duplicate-entry in timesheet-app has
failing tests for a new "duplicate work entry" feature.
Implement the feature so all tests pass. Do not modify
the test files.
```

---

### Exercise B: Java Upgrades/Modernization

- **Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)
- **Module:** [Framework Upgrade](../../../labs/migration-modernization/framework-upgrade.md)
- **Shows:** Devin handling a major framework upgrade — javax→jakarta namespace migration, Spring Security 6 lambda DSL changes, and dependency compatibility fixes across a monolith with 80% test coverage

#### Paste into Devin

```
Upgrade uc-spring-boot-upgrade-microservice-extraction from
Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2.

Handle the full upgrade checklist:
1. Update build.gradle — Spring Boot plugin, Java target
   compatibility, and dependency versions
2. Migrate javax.* imports to jakarta.* across all source
   files
3. Update Spring Security configuration to the new lambda
   DSL (Spring Security 6.x)
4. Fix any deprecated MyBatis or DGS (GraphQL) APIs
5. Update Flyway configuration for Spring Boot 3
   compatibility
6. Run ./gradlew build and ./gradlew test — fix any
   compilation errors or test failures
7. Document the breaking changes encountered and how each
   was resolved in the PR description
```

---

### Exercise C: COBOL Copybook to PySpark/JSON Config Generation

- **Repository:** [ts-cobol-carddemo](https://github.com/Cognition-Partner-Workshops/ts-cobol-carddemo)
- **Module:** [COBOL Copybook to PySpark/JSON](../../../labs/data-engineering/cobol-copybook-to-pyspark-json.md)
- **Shows:** Devin reading legacy COBOL data definitions and generating modern data engineering artifacts — cross-language translation at the schema level

#### Paste into Devin

```
Analyze the COBOL copybook `app/cpy/CVACT01Y.cpy` in
ts-cobol-carddemo. This defines the ACCOUNT-RECORD
layout (300 bytes) with fields like ACCT-ID
(PIC 9(11)), ACCT-CURR-BAL (PIC S9(10)V99), dates
(PIC X(10)), and FILLER.

Generate:
1. A PySpark script that reads
   `app/data/ASCII/acctdata.txt` as a fixed-width file
   using the copybook-derived schema
2. A JSON schema file describing each field's name,
   COBOL PIC clause, PySpark type, byte offset, and
   length
3. Validation output comparing parsed PySpark DataFrame
   row counts and sample values against the raw feed
   file

Then repeat for `CUSTREC.cpy` → `custdata.txt` and
`CVACT02Y.cpy` → `carddata.txt`.

Create a `COPYBOOK_PARSING_NOTES.md` documenting your
type-mapping decisions (e.g., COMP-3 → DecimalType,
PIC X → StringType).
```

---

## Known Limitations

A few things to be aware of as you work through the labs:

- **Lab 1 (Angular → React):** This lab uses a test-driven migration approach — Devin writes tests first as the migration specification, then migrates the React app to pass those tests. The tests use MSW (Mock Service Worker) to simulate the backend API. They verify the React app makes the correct API calls with the right data shapes — but they do not test against a running backend. To verify end-to-end, you can optionally start the PetClinic backend (petclinic-rest-api) locally and point the React app at it.
- **Lab 3 (Monolith Extraction):** The extraction starts from the monolith's current state (Spring Boot 2.6.3 / Java 11). The extracted article-service will also be on Spring Boot 2.6.3. For a Java 17 / Spring Boot 3.2 upgrade, see Exercise B in the post-session exercises.
- **Lab 4 (Ideas to Deployment):** Deployment artifacts (Dockerfile, CI workflow) are requested as PR comment follow-ups. There is no live deployment environment — the lab focuses on producing deployment-ready artifacts iteratively.
- **Each lab uses a different repository.** You'll work across four separate codebases rather than a single unified application.

---

## Repos Required

| Lab | Repository | Purpose |
|-----|-----------|---------|
| Lab 1 | [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular) | Angular 16 frontend — migration source |
| Lab 2 | [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) | Spring Boot 2.6.3 with known CVEs |
| Lab 3 | [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) | Spring Boot 2.6.3 monolith — microservice extraction source |
| Lab 4 | [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) | React + Node.js full-stack app |

**Post-session exercises (optional):**
- [ts-cobol-carddemo](https://github.com/Cognition-Partner-Workshops/ts-cobol-carddemo) — COBOL CardDemo application (Exercise C)

**Reference repos:**
- [petclinic-rest-api](https://github.com/Cognition-Partner-Workshops/petclinic-rest-api) — REST API backend for the PetClinic Angular frontend (Lab 1 reference)
- [petclinic-backend](https://github.com/Cognition-Partner-Workshops/petclinic-backend) — Spring Boot backend for PetClinic (Lab 1 reference)

## Devin Features Checklist

Track your progress on the [Devin Features Appendix](../../../labs/devin-features/README.md) throughout the session — try to discover as many Devin capabilities as you can.

| Feature | Lab(s) Where You'll See It |
|---------|---------------------------|
| Codebase analysis & planning | Labs 1, 3, 4 |
| API contract mapping | Lab 1 |
| Test-driven migration (TDD: red → green) | Lab 1 |
| Code generation (full-stack) | Labs 1, 4 |
| Dependency management | Lab 2 |
| SAST/SCA tool execution (via PR comment) | Lab 2 |
| Domain boundary analysis | Lab 3 |
| Microservice extraction | Lab 3 |
| Build & test verification | All labs |
| PR creation with documentation | All labs |
| PR feedback loop (comment → iterate) | All labs |
| Ask Devin research | All labs |
| DeepWiki exploration | All labs |
| AGENTS.md coding standards | Labs 1, 3 (repo-level conventions guide Devin) |
| Parallel sessions | Labs 1+2 overlap, Lab 4 optional dual sessions |
| Knowledge items | Lab 3 (extraction patterns), Lab 4 (feature patterns) |
| GitHub Issue creation | Lab 4 |
| CI/CD workflow generation (via PR comment) | Labs 2, 4 |
| Dockerfile generation (via PR comment) | Labs 3, 4 |
| Migration documentation | Labs 1, 3 |
| Multi-session TDD workflow | Exercise A |
| Java framework upgrade (javax→jakarta) | Exercise B |
| Cross-language translation (COBOL → PySpark) | Exercise C |

## Context

This workshop is a customized variant of the [General Workshop](../../../workshops/general/README.md), drawing from Tracks A (Security), B (Modernization), and C (Feature Development) and adding the Angular → React migration and monolith-to-microservices extraction use cases.

For a full-day experience with more labs and deeper dives, see the [General Workshop](../../../workshops/general/README.md).
