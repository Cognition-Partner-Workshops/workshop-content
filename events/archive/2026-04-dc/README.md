# Workshop: Hands-on Devin Workshop — Washington, DC

## Event Details

| | |
|---|---|
| **Date** | 2026-04 |
| **Location** | Washington, DC |
| **Duration** | ~5 hours (5 labs + breaks) |
| **Event Site** | https://partner-workshops-no-anthropic.devinenterprise.com |

## Getting the Most from This Workshop

> **Devin works autonomously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Each lab has a "Paste into Devin" step and a separate "Review & Give Feedback" step. Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin will keep working in the background.
- **Try parallel sessions.** Several labs suggest running multiple Devin sessions at once (e.g., upgrading Angular in two repos simultaneously). This mirrors real enterprise usage where Devin handles repetitive work across many services.
- **Use Ask Devin to refine requirements before creating a session.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem first so Devin can execute with less back-and-forth.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item during a session, accept it — this is how teams build a shared context layer that makes Devin smarter over time. You can also create Knowledge manually for project conventions and standards.
- **Leave PR comments to steer Devin.** After Devin opens a PR, the PR Review agent and CI checks provide automatic feedback loops. You can also leave comments directly on the PR and Devin will wake up and address them — this is the core workflow for iterating with Devin in production.

---

## Featured Labs

This event features 5 labs that follow a progressive modernization narrative:

- Lab 1: **End-to-End Modernization** — Modernize a legacy app into a new tech stack, break it into microservices, migrate the data source, and verify with tests
- Lab 2: **Framework Upgrades** — Run parallel Angular and Spring Boot upgrades across multiple repos
- Lab 3: **Data Source Migration & Testing** — Rewrite a legacy data source to a modern schema and rewire the app to use it, with validation testing
- Lab 4: **New Feature Development** — Build a new feature on an existing application (as time permits)
- Lab 5: **BDD Test Case Generation** — Generate Cucumber BDD test scenarios for REST APIs and build new API resources with matching Gherkin tests

### Lab 1 — End-to-End Legacy Modernization: New Tech Stack + Microservices + Data Source (75 min)

- **Module:** [Legacy Modernization Combined](../../../modules/migration-modernization/legacy-modernization-combined.md)
- **Repositories:**
  - [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java) — COBOL mainframe application (source)
  - [uc-framework-upgrade-monolith-to-microservices](https://github.com/Cognition-Partner-Workshops/uc-framework-upgrade-monolith-to-microservices) — Java monolith to decompose (microservice extraction)
  - [uc-data-source-migration-legacy-to-modern](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-legacy-to-modern) — Legacy data warehouse to modern schema (data source migration)
- **Objective:** Walk through the full modernization journey: migrate legacy code to a modern tech stack, extract microservices from a monolith, migrate the data source — and verify correctness with tests at each phase

This lab demonstrates the combined modernization pipeline that enterprises face: legacy code that needs a new tech stack, a monolith that needs to be decomposed, and a legacy data warehouse that needs to be replaced — all while keeping the system functional and tested.

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

Choose one of the three phases to start with, or run all three as parallel sessions. Each session runs independently on Devin's machine — kick them off and move on to Step 2 while Devin works.

**Phase A — Tech Stack Migration (COBOL → Java):**
> Analyze the COBOL program CBACT01C.cbl in uc-legacy-modernization-cobol-to-java. Understand its business logic, data structures (copybooks), and I/O operations. Rewrite it as a Java 17+ application using modern idioms. Create JUnit tests that verify the Java version produces identical output to the COBOL version for the sample data files in the repo. Document the field mappings in a `MIGRATION_NOTES.md`. Open a PR.

**Phase B — Microservice Extraction:**
> Upgrade uc-framework-upgrade-monolith-to-microservices from Spring Boot 2.6.3 to 3.x, then extract the article management domain into a standalone microservice with its own API contract, Dockerfile, and database. Create a docker-compose.yml that runs both services. Add integration tests that verify the monolith and microservice communicate correctly. Open a PR.

**Phase C — Data Source Migration:**
> Review the legacy CDW schema in uc-data-source-migration-legacy-to-modern. Create modern JPA entities matching data/modern-schema/modern_tables.sql with proper types. Write a migration service that transforms legacy data per data/mappings/column_mappings.md. Rewire LoanService.java to use modern repositories. Add validation tests that compare API responses before and after the migration to verify data integrity. Open a PR.

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore the testing story:
- *"What testing strategy should we use to verify the COBOL-to-Java migration is correct? How do we prove functional equivalence?"*
- *"What integration tests are needed when extracting a microservice from a monolith? How do we test the HTTP communication between the two services?"*
- *"How should we validate that the data source migration didn't corrupt or lose any data? What reconciliation checks should we run?"*
- Use the analysis to start a **second session** — try running Phase B and C in parallel to see how Devin handles multi-repo modernization at scale. Each session gets its own VM, so there's no interference between workstreams

#### Step 3 (Optional): Read the DeepWiki

Open each repo's **DeepWiki** page to understand the architecture before and after modernization:
1. **uc-legacy-modernization-cobol-to-java** — Understand the COBOL program structure, copybook layouts, and I/O operations. Use this to evaluate whether the Java translation preserves all business logic.
2. **uc-framework-upgrade-monolith-to-microservices** — Understand the domain boundaries (Articles, Users, Comments) and dependency graph. Use this to plan which bounded context to extract and where the seams are.
3. **uc-data-source-migration-legacy-to-modern** — Understand the legacy schema (all-VARCHAR, denormalized) vs. the modern schema (proper types, normalized). Use this to evaluate the data transformation logic.

Try different approaches:
- Run **all three phases as parallel Devin sessions** and compare the testing artifacts each produces — this mirrors how enterprise teams would staff multiple workstreams, except each one runs autonomously
- Ask Devin to produce a **combined migration report** that covers all three phases with a unified testing narrative
- Have Devin generate a **test coverage matrix** showing which tests validate which migration phase
- Try creating a **Knowledge item** capturing the migration patterns Devin discovered — this builds a reusable context layer so future sessions start smarter

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, focus your review on the **testing story**:
- **Phase A:** Do the JUnit tests prove functional equivalence? Are there parity tests that compare COBOL output to Java output?
- **Phase B:** Are there integration tests between the monolith and extracted microservice? Does docker-compose start both services and verify communication?
- **Phase C:** Are there validation tests that compare API responses before/after the data migration? Do the tests check record counts, field values, and data type conversions?

**Leave a feedback comment** and watch Devin respond:
- *"Add a golden-file comparison test that captures the API response before the migration and asserts it matches after"*
- *"The integration test between the monolith and microservice should verify the full request-response cycle, not just connectivity"*
- *"Add a data reconciliation report that shows row counts and checksum comparisons between legacy and modern tables"*

See the full challenge details for [Legacy Modernization Combined](../../../modules/migration-modernization/legacy-modernization-combined.md) for more ideas — each phase can also be explored individually via [COBOL to Java](../../../modules/migration-modernization/cobol-to-java.md), [Framework Upgrade](../../../modules/migration-modernization/framework-upgrade.md)/[Containerization & Microservice Extraction](../../../modules/migration-modernization/containerization-microservice-extraction.md), and [Data Source Migration](../../../modules/data-engineering/data-source-migration.md).

- **Key Takeaways:**
  - **"Modernization is a pipeline, not a single step"** — legacy code → new stack → microservices → modern data source, each phase validated with tests
  - **"Testing proves the migration is correct"** — parity tests (COBOL output = Java output), integration tests (services communicate), validation tests (data integrity preserved)
  - **"Parallel sessions show enterprise scale"** — Devin runs all three phases simultaneously on separate VMs, each working autonomously while you focus on review and planning

- **Target Outcomes (any of these count):**
  - Java code replacing COBOL with parity tests proving functional equivalence
  - Extracted microservice with Dockerfile, docker-compose, and integration tests
  - Modern JPA entities with data migration and validation tests
  - Combined migration documentation covering testing strategy across all phases
  - PR(s) with review comments focused on testing and Devin's responses

---

### Lab 2 — Framework Upgrades: Angular + Spring Boot (60 min)

- **Module:** [Repetitive Framework Upgrades](../../../modules/migration-modernization/repetitive-framework-upgrades.md) + [Framework Upgrade](../../../modules/migration-modernization/framework-upgrade.md)
- **Repositories:**
  - [uc-framework-upgrade-monolith-to-microservices](https://github.com/Cognition-Partner-Workshops/uc-framework-upgrade-monolith-to-microservices) — Spring Boot 2.6.3 → 3.x upgrade
  - [app_petclinic-angular](https://github.com/Cognition-Partner-Workshops/app_petclinic-angular) — Angular version upgrade
  - [ts-angular-realworld-example-app](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld-example-app) — Angular version upgrade (second repo for parallel comparison)
- **Objective:** Run parallel Devin sessions upgrading Angular and Spring Boot across multiple repos — demonstrating how Devin handles repetitive upgrade tasks at enterprise scale

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

Run these as **parallel sessions** to see how the same upgrade pattern scales across repos. Each session works independently — kick off all three and let Devin handle the tedious migration work while you explore Ask Devin:

**Session A — Spring Boot Upgrade:**
> Upgrade uc-framework-upgrade-monolith-to-microservices from Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2. Handle the javax to jakarta namespace migration, update Gradle build configuration, fix any deprecations, and ensure all tests pass. Document every breaking change and how you resolved it in the PR description. Open a PR.

**Session B — Angular Upgrade (PetClinic):**
> Upgrade app_petclinic-angular to the latest Angular version. Handle any breaking changes from the Angular update guide, update all dependencies, fix deprecated APIs, and ensure the app builds successfully. Document every breaking change encountered. Open a PR.

**Session C — Angular Upgrade (RealWorld) (optional, for comparison):**
> Upgrade ts-angular-realworld-example-app to the latest Angular version. Handle any breaking changes, update dependencies, fix deprecated APIs, and ensure the app builds and tests pass. Open a PR.

#### Step 2: Research with Ask Devin

While Devin works on the upgrades, open **AskDevin** and explore:
- *"What are the biggest risks when upgrading from Spring Boot 2 to 3? Which javax to jakarta changes are most likely to break?"*
- *"What Angular version is app_petclinic-angular currently on? What are the breaking changes between that version and the latest?"*
- *"Compare the Angular upgrade paths for app_petclinic-angular and ts-angular-realworld-example-app — are the same breaking changes expected?"*
- Use the analysis to plan a **repeatable upgrade runbook** that could be applied across dozens of similar services
- Once you have a runbook you like, consider turning it into a **Devin Playbook** — a reusable set of instructions that any team member can trigger for future upgrades without re-engineering the prompt

#### Step 3 (Optional): Read the DeepWiki

Open each repo's **DeepWiki** page to understand the codebase before the upgrade:
1. **uc-framework-upgrade-monolith-to-microservices** — Understand the build configuration, Spring Security setup, and dependency graph. These are the areas most affected by the Spring Boot 2→3 migration.
2. **app_petclinic-angular** — Understand the component hierarchy and module structure. Identify deprecated Angular patterns (NgModules vs. standalone components).
3. **ts-angular-realworld-example-app** — Compare with the PetClinic Angular app. Different codebases may hit different breaking changes for the same upgrade.

Try different approaches:
- Run both Angular upgrades in **parallel** and compare the upgrade PRs side-by-side
- Ask Devin to generate a **shared upgrade checklist** from both Angular upgrade experiences
- Have Devin create a **repeatable upgrade runbook** — then save it as a **Playbook** so any team member can reuse it for the next round of upgrades
- Think about scheduling: framework upgrades are a great candidate for **Devin Scheduled Sessions** — e.g., run dependency version bumps weekly across all repos to catch issues early

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens PRs from the parallel sessions, compare the upgrade approaches:
- **Spring Boot PR:** Is the javax → jakarta migration complete? Does the build pass with all tests green?
- **Angular PRs:** Did both upgrades follow the Angular update guide? Are deprecated patterns fully removed?
- **Cross-PR comparison:** Did Devin encounter the same issues in both Angular repos? Were they resolved consistently?

**Leave a feedback comment** and watch Devin respond:
- *"This still uses javax.servlet — please update to jakarta.servlet"*
- *"Can you also migrate from NgModules to standalone components?"*
- *"Generate an upgrade report documenting all breaking changes encountered and how they were resolved"*

See the full challenge details for [Repetitive Framework Upgrades](../../../modules/migration-modernization/repetitive-framework-upgrades.md) and [Framework Upgrade](../../../modules/migration-modernization/framework-upgrade.md) for more ideas.

- **Key Takeaways:**
  - **"Same prompt, multiple repos"** — the same upgrade task applied consistently across different services demonstrates enterprise scale
  - **"Parallel sessions save calendar time"** — upgrades that would take weeks sequentially can run simultaneously, each on its own VM with no interference
  - **"Consistency across upgrades"** — Devin applies the same patterns and catches the same breaking changes across repos
  - **"Playbooks turn one-off work into repeatable processes"** — capture the upgrade runbook as a Playbook so the next round of upgrades is a one-click operation for any team member

- **Target Outcomes (any of these count):**
  - Spring Boot app builds and tests on Java 17+ / Spring Boot 3.x
  - Angular app(s) upgraded to latest version with build passing
  - Upgrade documentation listing all breaking changes and resolutions
  - Side-by-side comparison of upgrade PRs across repos
  - PR(s) with review comments and Devin's responses

---

### Lab 3 — Data Source Migration: Rewrite + Reconnect + Test (60 min)

- **Module:** [Data Source Migration](../../../modules/data-engineering/data-source-migration.md)
- **Repository:** [uc-data-source-migration-legacy-to-modern](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-legacy-to-modern)
- **Objective:** Take an existing loan management app connected to a legacy data warehouse (all-VARCHAR, denormalized, cryptic column names), rewrite it to a modern normalized schema, rewire the app to point to the new data source, and prove correctness with comprehensive testing

This lab has two distinct parts:
1. **Rewrite the data source** — transform the legacy schema into a modern, normalized, properly-typed schema
2. **Reconnect the app** — update the application to read from the new data source and verify nothing broke

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

> Review the legacy CDW schema in uc-data-source-migration-legacy-to-modern. This is a loan management application currently reading from a denormalized legacy data warehouse where all columns are VARCHAR.
>
> Part 1 — Rewrite the data source:
> Create modern JPA entities matching the target schema in data/modern-schema/modern_tables.sql with proper types (LocalDate, BigDecimal, Long, enums). Write a migration service that reads from legacy tables, transforms the data (parse dates, convert amounts, expand status codes per data/mappings/column_mappings.md), and inserts into modern tables. Add data reconciliation tests that verify record counts match and field values are correctly transformed.
>
> Part 2 — Reconnect the app:
> Update LoanService.java to read from modern repositories instead of legacy ones. Ensure all existing API endpoints return the same data as before. Add golden-file validation tests that capture API responses before the migration and assert they match after.
>
> Create a `TESTING_REPORT.md` that documents: what tests were added, what they validate, and how to verify the migration is correct. Open a PR.

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore the testing strategy:
- *"What are the riskiest data type conversions in this migration — VARCHAR dates to LocalDate, VARCHAR amounts to BigDecimal? What edge cases could cause data loss?"*
- *"How should we validate that no records were lost or corrupted during the migration? What reconciliation checks should we run?"*
- *"What's the best way to implement a dual-read feature flag that can switch between legacy and modern data sources at runtime for safe rollout?"*
- Use the analysis to start a **second session** — try having Devin implement a dual-read mode with a feature flag, or build a data reconciliation dashboard. Since Devin's VM retains file system state between sessions, follow-up work picks up right where the last session left off

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the loan service domain model and API contracts. Use what you learn to try different approaches:
- Have Devin add a **dual-read feature flag** that can switch between legacy and modern data sources at runtime
- Ask Devin to generate **data reconciliation queries** that compare row counts, checksums, and field totals between legacy and modern tables
- Try having Devin produce a **complete data lineage map** showing which API endpoints read from which tables (before and after migration)
- Ask Devin to add **error handling** for malformed legacy data (null values, bad dates, unparseable amounts) with a detailed error report

> **Tip:** Data migrations are a great example of long-running tasks where Devin shines. Kick off the session and step away — Devin will work through the schema mapping, write the migration service, rewire the app, generate tests, and open a PR. You'll get notified when it's ready for review.

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, focus your review on the **testing and validation story**:
- **Data transformation:** Are the VARCHAR → proper type conversions correct? Do they handle edge cases (nulls, empty strings, malformed dates)?
- **Reconciliation tests:** Do the tests compare record counts between legacy and modern tables? Do they verify field values match after transformation?
- **Golden-file tests:** Do the API-level tests capture responses before the migration and assert they're identical after?
- **App reconnection:** Does the app now read from modern repositories? Are there any leftover references to legacy tables?

**Leave a feedback comment** and watch Devin respond:
- *"Add a dual-read feature flag that can switch between legacy and modern data sources at runtime"*
- *"The date parsing doesn't handle two-digit years — please fix and add a test case"*
- *"Add a data reconciliation report that shows row counts, field checksums, and any mismatches between legacy and modern tables"*

See the full challenge details for [Data Source Migration](../../../modules/data-engineering/data-source-migration.md) for more ideas.

- **Key Takeaways:**
  - **"Rewrite + reconnect + test"** — data source migration is not just schema work; the application must be rewired and the entire pipeline must be validated
  - **"Testing proves the migration didn't break anything"** — golden-file validation (API responses match), reconciliation checks (record counts balance), and type-conversion tests (edge cases handled)
  - **"Feature flags enable safe rollout"** — a dual-read mode lets you switch between data sources at runtime, reducing migration risk
  - **"Devin handles the long tail"** — data migrations involve tedious, detail-heavy work across many files. Devin works through it systematically while you focus on review and decision-making

- **Target Outcomes (any of these count):**
  - Modern JPA entities with proper types and FK relationships
  - Data migration service that transforms and loads all records
  - Service layer rewired to modern repositories (no more string parsing)
  - Golden-file validation: API responses match pre-migration baseline
  - Data reconciliation tests: record counts and field values verified
  - `TESTING_REPORT.md` documenting the validation strategy
  - PR with review comments and Devin's responses

---

### Lab 4 — New Feature Development (45 min, as time permits)

- **Module:** [New Feature Development](../../../modules/application-development/new-feature-development.md)
- **Repositories:**
  - [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet) — React + Node.js full-stack application
  - [uc-data-source-migration-legacy-to-modern](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-legacy-to-modern) — Spring Boot loan service (alternative)
- **Objective:** Build a new feature on an existing application — from requirements through implementation, testing, and PR creation

#### Step 1: Start with Ask Devin (recommended)

Before creating a session, try using **Ask Devin** to scope the feature. The more specific your requirements, the better Devin's output — and Ask Devin helps you think through the details before Devin starts writing code.

For example, ask: *"What existing patterns does app_timesheet use for CRUD features? What data model, API structure, and React component conventions should a new 'Projects' feature follow?"*

Then use what you learn to refine one of these prompts before pasting it into a Devin session:

**Option A — Full-Stack CRUD Feature (app_timesheet):**
> Add a "Projects" management feature to app_timesheet. Users should be able to create, view, edit, and delete projects. Each project has a name, description, client assignment, start date, and status (active/completed/on-hold). Add both the backend API endpoints and the frontend UI page. Follow the existing patterns in the codebase for the data model, API structure, and React components. Write tests for the backend endpoints. Open a PR.

**Option B — API Feature (loan service):**
> Add a loan payment history API to uc-data-source-migration-legacy-to-modern. Create a new endpoint GET /api/loans/:id/payments that returns a paginated list of payment records for a given loan. Include filtering by date range and payment type. Add proper error handling for invalid loan IDs. Write JUnit tests for the new endpoint. Open a PR.

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **Ask Devin** and go deeper:
- *"What validation rules should the Projects feature have? Should project names be unique per client?"*
- *"What API pagination pattern does the loan service use — offset-based or cursor-based? Which is better for payment history?"*
- Use the analysis to refine your requirements — try starting a **second session** with more specific feature requirements based on what you learned

> **Tip:** This is the Ask Devin workflow in action — use it to gather requirements and plan before creating a session. Teams that front-load clarity in Ask Devin get better results from Devin sessions with less back-and-forth.

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the existing feature patterns. Use what you learn to try different approaches:
- Have Devin add **validation rules** for the new feature (e.g., project dates must not overlap, loan payment amounts must match outstanding balance)
- Ask Devin to add **frontend tests** (React Testing Library or Cypress) for the new UI components
- Try having Devin add **audit logging** for all CRUD operations on the new feature
- Ask Devin to generate **API documentation** (Swagger/OpenAPI) for the new endpoints

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, practice the feedback loop:
- **Review the diff** — does the new feature follow the existing code style and patterns?
- **Leave a comment on the PR** asking Devin to add validation, error handling, or additional test cases
- **Watch Devin respond** to your PR comment and push a fix — this is how real teams work with Devin

See the full challenge details for [New Feature Development](../../../modules/application-development/new-feature-development.md) for more ideas.

- **Key Takeaways:**
  - **"Devin follows existing patterns"** — it analyzes the codebase's conventions before implementing, producing code that fits the existing architecture
  - **"Well-defined tasks get the best results"** — Ask Devin helps you front-load requirements and scoping so the session runs with minimal back-and-forth. This is how teams get the most out of Devin.
  - **"Full-stack in one session"** — Devin handles database schema, backend API, frontend UI, and tests in a single session
  - **"Knowledge compounds over time"** — if Devin discovers project conventions during this session, save them as Knowledge items. Future sessions (by you or any team member) will automatically benefit from that context

- **Target Outcomes (any of these count):**
  - New feature implemented following existing code conventions
  - Unit tests and/or integration tests for the new feature
  - Database schema changes with migration scripts
  - Frontend UI components for the new feature
  - PR with review comments and Devin's responses

---

### Lab 5 — BDD Test Case Generation for REST APIs (60 min)
- **Module:** [BDD Test Generation](../../../modules/testing-qa/bdd-test-generation.md)
- **Repository:** [uc-bdd-test-generation-rest-api](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-rest-api)
- **Objective:** Give Devin a Spring Boot + Cucumber BDD framework and prompt it to generate new test scenarios, build a new API resource, and produce executable Cucumber tests

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

> Review the uc-bdd-test-generation-rest-api codebase. This is a Spring Boot + Cucumber BDD framework (from RedFroggy, MIT license) with pre-built step definitions for REST API testing. Run `mvn test` to see the 16 existing scenarios pass.
>
> Then add new Gherkin feature files that test edge cases for the existing Users API:
> - `src/test/resources/features/users-edge-cases.feature` covering:
>   - Creating a user with missing required fields (expect 400)
>   - Creating a user with duplicate ID (expect 409 or appropriate error)
>   - Pagination and sorting of the users list
>   - Input validation (invalid age values, empty name fields)
>
> Also create a new `OrderController` in the test application with endpoints for managing orders (linked to users). Write corresponding Gherkin feature files that test the order lifecycle (create, read, update, delete) and cross-resource relationships (e.g., get orders for a specific user). Open a PR.

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **Ask Devin** and explore:
- *"What Cucumber best practices should be followed for REST API testing — should scenarios be independent or can they share state?"*
- *"How should authentication be handled in the BDD scenarios — per-scenario setup or shared background?"*
- *"How can WireMock be used to test failure modes — what happens when the third-party API returns 500, times out, or returns malformed JSON?"*
- Use the analysis to start a **second session** — have Devin generate tests from a Swagger/OpenAPI spec directly

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the existing Cucumber configuration, step definition patterns, and how the framework maps Gherkin steps to REST API calls. Use what you learn to give Devin more tasks:
- Have Devin generate **data-driven scenarios** using Cucumber Scenario Outlines with Examples tables
- Ask Devin to add **negative test scenarios** for authentication failures and rate limiting
- Try having Devin add **WireMock failure mode tests** — third-party API returns 500, times out, or returns malformed JSON
- Ask Devin to add a **test report generator** that produces HTML results

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, practice the feedback loop:
- **Review the diff** — are the Gherkin scenarios readable by non-developers? Do they describe business behavior rather than implementation details?
- **Leave a comment on the PR** asking Devin to fix something (e.g., *"Add data-driven scenarios using Cucumber Scenario Outlines with Examples tables"* or *"The step definitions should use more descriptive method names"*)
- **Watch Devin respond** to your PR comment and push a fix — this is how real teams work with Devin

See the full challenge details for [BDD Test Generation](../../../modules/testing-qa/bdd-test-generation.md) for more ideas.

- **Key Takeaways:**
  - **"Test generation from patterns, not templates"** — Devin reads the existing test framework and API patterns to generate meaningful BDD scenarios, not boilerplate
  - **"New resources with matching tests"** — Devin builds the API resource and its Gherkin test suite together, keeping implementation and validation in sync
  - **"Framework-aware output"** — Devin produces executable, Maven-integrated Cucumber tests that fit the project's existing conventions
  - **"Quality engineering at scale"** — BDD test generation is a great candidate for scheduled Devin sessions: as new API endpoints are added, schedule weekly test generation runs to maintain coverage automatically

- **Target Outcomes (any of these count):**
  - Edge-case feature files for the Users API with validation and error scenarios
  - New OrderController with Gherkin feature files for order lifecycle
  - Data-driven scenarios using Scenario Outlines with Examples tables
  - WireMock failure mode tests
  - PR with review comments and Devin's responses

## Additional Challenges

Participants may also attempt any challenge from the full [module catalog](../../../modules/) as creative inspiration. Recommended extras:

| Challenge | Module | Repo | Difficulty | Time |
|-----------|--------|------|-----------|------|
| CVE Remediation | [SEC1-SEC4](../../../modules/security/README.md) | uc-cve-remediation-regulatory-compliance | Intermediate | 60 min |
| DW Migration: Teradata → Snowflake | [DW Migration: Teradata to Snowflake](../../../modules/data-engineering/dw-migration-teradata-to-snowflake.md) | uc-dw-migration-teradata-to-snowflake | Intermediate | 60 min |
| Unit Testing | [Unit Testing](../../../modules/testing-qa/unit-testing.md) | Any | Beginner | 30 min |
| COBOL System Understanding | [COBOL System Understanding](../../../modules/migration-modernization/cobol-system-understanding.md) | uc-legacy-modernization-cobol-to-java | Advanced | 60 min |

## Repos Required on Devin's Machine

- [ ] uc-legacy-modernization-cobol-to-java
- [ ] uc-framework-upgrade-monolith-to-microservices
- [ ] uc-data-source-migration-legacy-to-modern
- [ ] app_petclinic-angular
- [ ] ts-angular-realworld-example-app
- [ ] app_timesheet
- [ ] uc-bdd-test-generation-rest-api

### Optional (for extended challenges)

- [ ] uc-cve-remediation-regulatory-compliance
- [ ] uc-dw-migration-teradata-to-snowflake

## Repo Duplication Notes

- `uc-framework-upgrade-monolith-to-microservices` and `uc-cve-remediation-regulatory-compliance` both originate from `spring-boot-realworld-example-app` (Cluster C1 in [catalog](../../../catalog/repos.md)). They are intentionally separate repos so each use case gets an isolated starting point.
- `uc-legacy-modernization-cobol-to-java` originates from `aws-mainframe-modernization-carddemo` (Cluster C2).

## Context

- **Audience:** Technology teams
- **Focus:** End-to-end modernization — legacy tech stack migration, framework upgrades (Angular + Spring Boot), data source rewrite and reconnection, new feature development, and BDD test generation
- **Testing narrative:** Each lab emphasizes how to test and validate the changes — parity tests, integration tests, golden-file validation, data reconciliation, and BDD scenarios
- **Domain Data:** Loan management (borrowers, loan accounts, products, payments)
- **Devin value themes woven throughout:**
  - Autonomous, off-machine execution — kick off sessions and move on
  - Parallel sessions for scaling repetitive work across repos
  - Ask Devin for requirement gathering and task scoping before sessions
  - Knowledge and Playbooks for building reusable team context
  - PR Review agent + CI checks as automatic feedback loops
  - Long-running task handling — Devin works while you're in meetings
  - Scheduled sessions for continuous code hygiene (dependency bumps, test generation)

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../../modules/devin-features/README.md) throughout the session.
