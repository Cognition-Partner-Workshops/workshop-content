# Workshop: Quality Engineering and Assurance

**Other variants:** [Cloud only](README.md) | [Desktop + Cloud](README.platform.md)

## Overview

| | |
|---|---|
| **Focus** | Testing strategy, test automation, code quality, and continuous assurance — building confidence in software through systematic verification |
| **Duration** | Flexible (3 tracks, 3 labs each) |
| **Audience** | QA engineers, test leads, SDETs, development teams focused on quality, and engineering leaders building quality culture |
| **Tracks** | **Track A:** Test Automation & Coverage · **Track B:** End-to-End & Integration Testing · **Track C:** Continuous Quality & Code Review |
| **Surface** | **Devin CLI / Devin Local** |

## Platform Context

This workshop uses **Devin CLI** (install from [cli.devin.ai](https://cli.devin.ai)). You will run `devin` in your terminal, interact with the agent conversationally, and use subagents for parallel subtasks. Code changes happen on your local machine with fast iteration cycles — no cloud VMs or browser tabs required.

> **Tip:** Devin CLI speaks the Agent Client Protocol (ACP) and can run as "Devin Local" inside Devin Desktop for a visual agent management experience alongside terminal work. The same Knowledge items and MCP integrations you configure for Cloud are available locally.

---

## Workshop Narrative

This workshop covers the three dimensions of quality engineering: automating test creation at every level of the pyramid, validating system behavior end-to-end, and building continuous quality practices into the development workflow. Each track is self-contained — participants can follow all three in sequence for a full-day experience, or focus on one or two tracks in a shorter session.

The tracks are designed to show Devin working across the quality spectrum:
- **Track A** shows Devin as a test automation engineer — generating unit tests, BDD scenarios, and measuring coverage to identify gaps
- **Track B** shows Devin as a system tester — running applications end-to-end, writing Playwright/Selenium tests, and validating cross-service integration
- **Track C** shows Devin as a quality advocate — automating code review, performing static analysis, generating documentation, and building continuous quality pipelines

## Getting the Most from This Workshop

> **Devin CLI works locally in your terminal.** Run `devin` in your project directory, describe what you want, and iterate conversationally. Changes happen on your local filesystem — review diffs instantly with `git diff`. Use subagents to parallelize work across files or packages.

A few tips to maximize your hands-on time:

- **Use subagents for parallel work.** When generating tests across multiple modules, spawn subagents to handle each module independently — they work in parallel on your machine.
- **Leverage shell integration.** Devin CLI understands your shell context, recent commands, and terminal state — no need to re-explain your environment.
- **Iterate fast locally.** The local workflow lets you review changes instantly (`git diff`), test them immediately (`npm test`), and refine your instructions in real time.
- **Build up knowledge as you go.** Save testing conventions (framework choices, assertion style, test data patterns) as Knowledge items — they apply to CLI sessions too.
- **Hand off to Cloud when needed.** Some labs (scheduled sessions, long-running tests) benefit from Cloud execution. Use `devin --cloud` or hand off mid-session when the task outgrows local resources.

---

## Table of Contents

- [Track A: Test Automation & Coverage](#track-a-test-automation--coverage)
  - [Lab A1 — Unit Test Generation](#lab-a1--unit-test-generation)
  - [Lab A2 — BDD Test Generation](#lab-a2--bdd-test-generation)
  - [Lab A3 — Mutation Testing & Test Quality](#lab-a3--mutation-testing--test-quality)
- [Track B: End-to-End & Integration Testing](#track-b-end-to-end--integration-testing)
  - [Lab B1 — Playwright E2E Tests](#lab-b1--playwright-e2e-tests)
  - [Lab B2 — Cross-Service Integration Testing](#lab-b2--cross-service-integration-testing)
  - [Lab B3 — Performance & Load Testing](#lab-b3--performance--load-testing)
- [Track C: Continuous Quality & Code Review](#track-c-continuous-quality--code-review)
  - [Lab C1 — Linting & Static Analysis](#lab-c1--linting--static-analysis)
  - [Lab C2 — Code Review & Documentation](#lab-c2--code-review--documentation)
  - [Lab C3 — Continuous Quality Pipeline](#lab-c3--continuous-quality-pipeline)
- [Additional Challenges](#additional-challenges)
- [Suggested Formats](#suggested-formats)
- [Repos Required](#repos-required)
- [Context](#context)
- [Devin Features Checklist](#devin-features-checklist)

---

## Track A: Test Automation & Coverage

Track A demonstrates Devin as a test automation engineer. Participants will generate unit tests to increase coverage, create BDD scenarios from API specifications, and validate test quality using mutation testing.

### Lab A1 — Unit Test Generation

- **Module:** [Unit Testing](../../modules/testing-qa/unit-testing.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot app with existing JUnit infrastructure
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app with Jest (alternative)
- **Objective:** Analyze existing test coverage, identify under-tested modules, and generate meaningful unit tests that verify real behavior — not just padding coverage

#### Step 1: Run Devin in your terminal

Navigate to the cloned repo and start Devin CLI:

```bash
cd uc-spring-boot-upgrade-microservice-extraction
devin
```

Start with exploration — ask Devin to analyze coverage:

*"Analyze the current test coverage. Run the existing tests and generate a coverage report. What are the top 5 modules with the lowest test coverage? What testing patterns does the codebase use?"*

#### Step 2: Generate tests interactively

Once you understand the coverage gaps, ask Devin to generate tests. Choose one approach:

**Option A — JUnit Tests (Spring Boot):**

```
Write JUnit tests for the 5 lowest-coverage modules, following the existing
test patterns: use MockMvc for controller tests, Mockito for service tests,
and integration tests for repository layer. Aim for at least 80% line coverage
on each module. Include negative test cases (invalid inputs, not-found
scenarios, unauthorized access).
```

**Option B — Jest Tests (Node.js):**

```bash
cd timesheet-app
devin
```

```
Run `npm test -- --coverage` to see current coverage. Identify the 5
least-tested API routes and service modules. Write Jest tests for each:
(1) Unit tests for service functions with mocked dependencies,
(2) Integration tests for API routes using supertest, (3) Edge case tests
for error handling, empty inputs, and boundary conditions.
```

#### Step 3: Use subagents for parallel generation

Spawn subagents to generate tests across multiple modules simultaneously:

*"Use subagents to generate tests for each of the 5 modules in parallel. Each subagent should handle one module's test file."*

#### Step 4: Review and iterate locally

Review the generated tests immediately:

```bash
git diff
npm test  # or ./gradlew test
```

Iterate with Devin in real time:
- *"This test just asserts 'not null' — add meaningful assertions about the response content"*
- *"Add edge case tests for when the article slug contains special characters"*
- *"The test depends on a specific database state — use test fixtures or setup methods"*

- **Key Takeaways:**
  - **"Devin writes meaningful tests"** — not just coverage padding. It analyzes the codebase to understand what needs testing and generates tests that verify real behavior
  - **"Fast local iteration"** — see changes instantly, run tests immediately, refine instructions in real time
  - **"Subagents parallelize work"** — generate tests for multiple modules simultaneously without waiting
  - **"Quality at scale"** — the same patterns work across repos; use Knowledge items to maintain consistency

- **Target Outcomes (any of these count):**
  - JaCoCo or Istanbul coverage report showing improvement
  - 5+ new test files with meaningful assertions
  - Edge case and negative test coverage
  - Tests following existing framework conventions
  - Local tests passing with `git diff` showing the changes

---

### Lab A2 — BDD Test Generation

- **Module:** [BDD Test Generation](../../modules/testing-qa/bdd-test-generation.md)
- **Repositories:**
  - [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber) — Spring Boot + Cucumber BDD framework with pre-built step definitions
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot RealWorld app (alternative — add Cucumber to an existing app)
- **Objective:** Generate BDD test scenarios from API specifications — write Gherkin feature files that describe business behavior, implement step definitions, and run the full BDD suite

#### Step 1: Explore with Devin CLI

```bash
cd uc-bdd-test-generation-cucumber
devin
```

Start with exploration:
- *"What's the BDD framework structure here? Show me the existing Gherkin feature files and step definitions."*
- *"Run `mvn test` — how many scenarios pass? What patterns do the existing step definitions follow?"*

#### Step 2: Generate BDD scenarios

**Option A — Extend Existing BDD Suite:**

```
Add new Gherkin feature files that test edge cases for the existing Users
API: (1) Creating a user with missing required fields (expect 400),
(2) Creating a user with duplicate ID (expect 409 or appropriate error),
(3) Pagination and sorting, (4) Input validation (invalid email, too-long
names). Also create a new `OrderController` with endpoints for managing
orders and write corresponding Gherkin feature files. Use Scenario Outlines
with Examples tables for data-driven testing.
```

**Option B — Add BDD to Existing App:**

```bash
cd uc-spring-boot-upgrade-microservice-extraction
devin
```

```
Add Cucumber BDD testing. Set up the Cucumber test infrastructure
(dependencies, test runner, feature file directory). Write Gherkin feature
files for the Articles API: (1) Feature: Create Article — scenarios for
valid creation, missing fields, duplicate slugs, (2) Feature: Article Feed
— scenarios for global feed, user feed, tag filtering, pagination,
(3) Feature: Favorite Article — scenarios for favoriting, unfavoriting,
favorite count. Implement step definitions that call the API. Run all
scenarios and verify they pass.
```

#### Step 3: Iterate on quality

Review the generated Gherkin locally and refine:

```bash
git diff -- src/test/resources/features/
mvn test
```

Ask Devin to improve:
- *"Use Scenario Outline with Examples for the validation tests — test all field types in one scenario"*
- *"The step definitions should use more descriptive method names that read like English"*
- *"Add @smoke tags to the critical path scenarios for fast feedback in CI"*

- **Key Takeaways:**
  - **"BDD bridges dev and business"** — Gherkin scenarios are readable by non-developers, making them a communication and documentation tool
  - **"Scenario Outlines reduce duplication"** — data-driven testing avoids copy-pasting scenarios with minor variations
  - **"Step definitions are reusable"** — well-written steps compose into new scenarios without new code
  - **"Living documentation"** — Cucumber test results serve as up-to-date API documentation that's verified on every run

- **Target Outcomes (any of these count):**
  - Gherkin feature files with meaningful business scenarios
  - Step definitions implemented and tests passing
  - Scenario Outlines with Examples tables for data-driven tests
  - New API endpoints tested via BDD
  - All tests passing locally with `mvn test`

---

### Lab A3 — Mutation Testing & Test Quality

- **Module:** [Mutation Testing](../../modules/testing-qa/mutation-testing.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot app with existing JUnit tests
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — Node.js app with Jest tests (alternative)
- **Objective:** Use mutation testing to evaluate test quality — mutants that survive indicate tests that pass without actually verifying behavior. Kill surviving mutants by adding meaningful assertions.

#### Step 1: Run Devin CLI

```bash
cd uc-spring-boot-upgrade-microservice-extraction
devin
```

Start with understanding:
- *"What is mutation testing? How does it differ from code coverage? Why can 100% coverage still have weak tests?"*
- *"What are the most common mutation operators? Which ones reveal the most meaningful test gaps?"*

#### Step 2: Set up and run mutation testing

**Option A — PIT Mutation Testing (Java):**

```
Set up PIT mutation testing. Add the PIT Gradle plugin and configure it to
run on the Articles domain (io.spring.core.article and
io.spring.application.article packages). Run mutation testing and analyze
the report — identify which mutants survived. For the top 10 surviving
mutants, improve the existing tests or write new ones that kill the mutant.
Document the before/after mutation scores in a
`MUTATION_TESTING_REPORT.md`.
```

**Option B — Stryker Mutation Testing (JavaScript):**

```bash
cd timesheet-app
devin
```

```
Set up Stryker mutation testing for the backend. Configure Stryker to run on
the service layer modules. Run mutation testing and analyze which mutants
survive. For surviving mutants, add or improve test assertions to kill them.
Focus on: (1) Conditional mutations, (2) Return value mutations,
(3) Arithmetic mutations. Document findings in a
`MUTATION_TESTING_REPORT.md`.
```

> **Cloud handoff:** Mutation testing can be resource-intensive. If PIT/Stryker runs are slow on your machine, hand off to Devin Cloud: *"Hand this off to cloud — run the full mutation testing suite and report back."* See the [cloud variant](README.md) for this lab.

#### Step 3: Kill surviving mutants locally

Review the mutation report and iterate:

*"Focus on the conditional mutations first — those reveal the most dangerous test gaps. Kill the top 5 survivors with meaningful behavior-level assertions."*

Run mutation testing again to verify improvement:

*"Re-run mutation testing on the Articles package. Show me the before/after mutation scores."*

- **Key Takeaways:**
  - **"Coverage ≠ quality"** — a test can execute a line without actually verifying its behavior. Mutation testing exposes this gap
  - **"Surviving mutants = weak tests"** — if you change the code and the tests still pass, the tests aren't testing what you think
  - **"Kill the important mutants"** — not every mutant needs to be killed. Focus on mutations in critical business logic
  - **"Mutation testing validates your test suite"** — it's a test of your tests, ensuring they provide real safety

- **Target Outcomes (any of these count):**
  - Mutation testing tool configured and running
  - Mutation report showing before/after scores
  - 10+ surviving mutants killed with meaningful assertions
  - `MUTATION_TESTING_REPORT.md` documenting findings
  - Tests passing locally with improved mutation score

---

## Track B: End-to-End & Integration Testing

Track B demonstrates Devin as a system tester. Participants will write and run E2E tests against running applications, validate cross-service integration, and perform load testing to verify performance characteristics.

### Lab B1 — Playwright E2E Tests

- **Module:** [End-to-End Testing](../../modules/testing-qa/end-to-end-testing.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js full-stack application
  - [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld) — Angular RealWorld app with existing Playwright tests (alternative)
- **Objective:** Write and run E2E tests against a running application — exercise real user workflows through the browser, discover issues through testing, and fix what you find

#### Step 1: Run Devin CLI

```bash
cd timesheet-app
devin
```

Start by running the application locally:

*"Set up and run timesheet-app locally (backend on port 3001, frontend on port 5173). Then tell me the main user workflows that would benefit from E2E tests."*

#### Step 2: Generate E2E tests

**Option A — Full E2E Suite (timesheet-app):**

```
Write Playwright E2E tests for the core user workflows: (1) Login flow —
valid credentials succeed, invalid credentials show error, (2) Client
management — create, edit, delete a client, (3) Work entry lifecycle —
create entry for a client, verify it appears in list, edit hours, delete,
(4) Reporting — verify reports show correct totals after creating entries,
(5) Edge cases — submit empty forms, special characters in names, very long
text. Run the tests. If any tests fail because of application bugs, fix the
bugs too.
```

**Option B — Extend Existing E2E Tests (Angular):**

```bash
cd ts-angular-realworld
devin
```

```
Review the existing Playwright tests in the e2e/ directory. Run them to
verify they pass. Then extend the test suite with: (1) Article lifecycle —
create, read, update, delete an article, (2) Social features — follow a
user, favorite an article, comment on an article, (3) Tag filtering —
create articles with tags and verify tag-based filtering works, (4) Error
scenarios — verify graceful handling of 401, 404, 500 responses.
```

> **Cloud handoff:** E2E tests require a running browser and application. If your local machine lacks the resources, hand off to Devin Cloud which has a built-in browser and desktop. See the [cloud variant](README.md) for this lab.

#### Step 3: Run and iterate locally

Run the tests and review results:

```bash
npx playwright test --reporter=html
```

Iterate with Devin:
- *"Use data-testid attributes instead of CSS selectors for stability"*
- *"Replace the sleep(2000) with waitForSelector — this will be flaky in CI"*
- *"Add a test for submitting the form with missing required fields"*

- **Key Takeaways:**
  - **"E2E tests discover bugs"** — automated browser testing finds issues that unit tests miss (rendering bugs, form validation, navigation)
  - **"Fast local iteration"** — run tests immediately, see failures instantly, fix and re-run in the same session
  - **"Fix bugs during testing"** — when tests find bugs, Devin fixes them in the same conversation
  - **"Local-first for rapid development"** — iterate on test selectors and assertions faster than waiting for cloud roundtrips

- **Target Outcomes (any of these count):**
  - Playwright E2E test suite covering core user workflows
  - Tests running and passing locally
  - Bug fixes discovered during E2E testing
  - Tests using stable selectors and proper waiting strategies
  - All changes visible with `git diff`

---

### Lab B2 — Cross-Service Integration Testing

- **Module:** [Cross-Service Integration Testing](../../modules/testing-qa/cross-service-integration-testing.md)
- **Repositories:**
  - [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices) — .NET microservices (Identity, Customer, Order, Product, Notification)
  - [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices) — Spring Boot microservices (alternative)
- **Objective:** Write integration tests that verify multiple microservices work correctly together — testing the contracts, data flow, and error handling between services

#### Step 1: Run Devin CLI

```bash
cd quickapp-microservices
devin
```

Explore the service architecture:
- *"What services exist here and how do they communicate? REST, events, or both?"*
- *"What's the Docker Compose setup? Can I run all services together locally?"*

#### Step 2: Generate integration tests

**Option A — .NET Microservices Integration:**

```
Write cross-service integration tests. Focus on the Order → Product →
Notification flow: (1) Create a product via the Product service, (2) Place
an order via the Order service referencing that product, (3) Verify the
Notification service receives the order-placed event, (4) Verify the Order
service validates product existence before accepting orders, (5) Test error
scenarios — order for non-existent product, order with invalid customer.
Use Docker Compose to run all services together.
```

**Option B — Spring Boot Microservices Integration:**

```bash
cd petclinic-microservices
devin
```

```
Write cross-service integration tests. Test the full workflow: (1) Register
a new pet owner via the customers-service, (2) Add a pet, (3) Schedule a
visit via the visits-service, (4) Verify the API gateway correctly routes
and aggregates data, (5) Test circuit breaker behavior — what happens when
the visits-service is down? Use Docker Compose and write tests using
RestAssured.
```

> **Cloud handoff:** Running multiple Docker services requires significant local resources. If Docker Compose struggles on your machine, hand off to Devin Cloud which has a full VM with Docker. See the [cloud variant](README.md) for this lab.

#### Step 3: Run and iterate

```bash
docker-compose up -d
# run integration tests
docker-compose down
```

Iterate with Devin:
- *"Add a test for the timeout scenario — what happens if the Product service takes 10 seconds to respond?"*
- *"The event assertion should poll with a timeout rather than using Thread.sleep"*

- **Key Takeaways:**
  - **"Integration tests verify the seams"** — they catch bugs that hide in the boundaries between services (serialization, routing, error propagation)
  - **"Docker Compose enables realistic testing"** — running services together mimics production behavior
  - **"Contract tests prevent drift"** — when services evolve independently, contracts ensure they still speak the same language
  - **"Error paths are the most important tests"** — what happens when a downstream service fails matters more than the happy path

- **Target Outcomes (any of these count):**
  - Integration test suite running against multiple services
  - Docker Compose configuration for test environment
  - Happy path and error scenario coverage
  - Contract tests for service boundaries
  - All tests passing locally

---

### Lab B3 — Performance & Load Testing

- **Modules:** [Performance Testing](../../modules/testing-qa/performance-testing.md) + [Load Testing & Benchmarking](../../modules/testing-qa/load-testing-benchmarking.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app (establish baseline and identify bottlenecks)
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot app (alternative)
- **Objective:** Create performance tests that establish baselines, identify bottlenecks, and verify the application handles expected load — demonstrating Devin building load testing infrastructure

#### Step 1: Run Devin CLI

```bash
cd timesheet-app
devin
```

Research performance patterns:
- *"What are the typical performance bottlenecks in Express + SQLite applications?"*
- *"What performance SLOs are reasonable for a CRUD application — what p95 latency should I target?"*

#### Step 2: Generate load tests

**Option A — k6 Load Testing (Node.js):**

```
Set up k6 load testing. Create load test scripts that: (1) Simulate 50
concurrent users performing typical workflows, (2) Run a ramp-up test from
1 to 100 users over 5 minutes, (3) Establish performance baselines (p95
latency, error rate, throughput), (4) Identify the breaking point. Document
findings in a `PERFORMANCE_REPORT.md`. If you identify a bottleneck (e.g.,
missing database index, N+1 query), fix it and re-run.
```

**Option B — Gatling Load Testing (Java):**

```bash
cd uc-spring-boot-upgrade-microservice-extraction
devin
```

```
Set up Gatling load testing. Create simulation scripts that: (1) Test the
articles API under load, (2) Simulate realistic traffic patterns (80% reads,
15% writes, 5% deletes), (3) Run a sustained load test at 30 req/sec for 5
minutes, (4) Measure p50, p95, and p99 response times. Document findings in
`PERFORMANCE_REPORT.md`.
```

> **Cloud handoff:** Long-running load tests (soak tests, high-concurrency stress tests) benefit from Cloud's dedicated VM resources. Hand off with: *"Hand this off to cloud — run a 30-minute soak test and report the results."* See the [cloud variant](README.md) for this lab.

#### Step 3: Analyze and optimize locally

Review the performance report and iterate:

*"The p95 is too high on the report endpoint. Analyze the query — is there a missing index or N+1 problem? Fix it and re-run the load test to show improvement."*

```bash
k6 run load-test.js
```

- **Key Takeaways:**
  - **"Baseline before optimizing"** — you must know current performance before you can claim improvement
  - **"p95 matters more than average"** — tail latency affects the users who are already frustrated
  - **"Load testing finds different bugs"** — race conditions, connection pool exhaustion, and memory leaks only appear under load
  - **"Performance budgets prevent regression"** — integrating load tests into CI catches performance issues before they reach production

- **Target Outcomes (any of these count):**
  - Load testing tool configured (k6, Gatling, or similar)
  - Performance baseline established with documented metrics
  - Bottleneck identified and (optionally) fixed
  - `PERFORMANCE_REPORT.md` with findings and recommendations
  - Load test scripts ready to run locally

---

## Track C: Continuous Quality & Code Review

Track C demonstrates Devin as a quality advocate. Participants will set up linting and static analysis, automate code review with documentation generation, and build continuous quality pipelines that maintain standards automatically.

### Lab C1 — Linting & Static Analysis

- **Module:** [Linting & Static Analysis](../../modules/testing-qa/linting-static-analysis.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js (ESLint, Prettier)
  - [timesheet-infra](https://github.com/Cognition-Partner-Workshops/timesheet-infra) — Terraform (terraform fmt, tflint)
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Java/Gradle (Spotless, Checkstyle) (alternative)
- **Objective:** Set up and enforce code quality standards through automated linting, formatting, and static analysis — catch issues before they reach code review

#### Step 1: Run Devin CLI with subagents

Use subagents to set up linting for multiple repos in parallel:

```bash
cd timesheet-app
devin
```

**Subagent A — JavaScript/TypeScript Linting:**

```
Set up comprehensive linting: (1) Configure ESLint with TypeScript rules for
both frontend and backend, (2) Add Prettier for consistent formatting,
(3) Create a pre-commit hook (using Husky + lint-staged) that auto-formats
and lints on every commit, (4) Fix existing lint errors and warnings,
(5) Add a CI step that fails the build on lint violations. Document the lint
configuration choices in a `CODING_STANDARDS.md`.
```

**Subagent B — Terraform Linting (in a separate terminal):**

```bash
cd timesheet-infra
devin
```

```
Set up Terraform quality enforcement: (1) Run `terraform fmt -check` and fix
formatting issues, (2) Add tflint with the AWS ruleset, (3) Add checkov or
tfsec for security scanning, (4) Create a CI pipeline that runs fmt check +
tflint + security scan on PRs, (5) Fix any security findings. Document IaC
standards in an `IAC_STANDARDS.md`.
```

#### Step 2: Review and iterate locally

Check the results immediately:

```bash
npx eslint . --format=stylish
npx prettier --check .
git diff
```

Iterate with Devin:
- *"Disable the 'no-console' rule for the backend — server-side logging via console is acceptable"*
- *"Add a rule for maximum function length (30 lines) to encourage refactoring"*

Consider creating a **Knowledge item** capturing the team's lint configuration for future Devin sessions.

- **Key Takeaways:**
  - **"Shift quality left"** — catching issues at commit time is cheaper than catching them in code review or production
  - **"Automated formatting eliminates bikeshedding"** — when Prettier formats code, nobody argues about style
  - **"Pre-commit hooks = zero effort"** — developers don't need to remember to lint; it happens automatically
  - **"Security scanning in CI"** — infrastructure security issues are caught before deployment, not after a breach

- **Target Outcomes (any of these count):**
  - Lint configuration (ESLint, Prettier, tflint, Checkstyle, or similar)
  - Pre-commit hooks enforcing standards
  - Existing lint violations fixed
  - CI step that fails on violations
  - `CODING_STANDARDS.md` documenting configuration choices

---

### Lab C2 — Code Review & Documentation

- **Modules:** [PR Review Automation](../../modules/devops-cicd/pr-review-automation.md) + [Inline Documentation](../../modules/technical-documentation/inline-documentation.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot (Javadoc)
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js (JSDoc/TSDoc) (alternative)
- **Objective:** Use Devin to generate comprehensive documentation for public APIs and key modules, and identify code quality concerns

#### Step 1: Run Devin CLI

```bash
cd uc-spring-boot-upgrade-microservice-extraction
devin
```

Start with analysis:
- *"What documentation conventions does this codebase follow? Are there existing Javadoc patterns?"*
- *"What are the most common code smells? Which areas have the most technical debt?"*

#### Step 2: Generate documentation

**Option A — Javadoc Generation (Spring Boot):**

```
Add Javadoc documentation to all public interfaces, classes, and methods
that are currently undocumented. Focus on: (1) REST controller methods
(describe endpoint, parameters, response, error cases), (2) Service layer
public methods (describe business logic), (3) Repository interfaces
(describe the query). Follow existing documentation style. Also create a
`CODE_REVIEW.md` identifying the top 10 code quality concerns.
```

**Option B — JSDoc/TSDoc Generation (Node.js/React):**

```bash
cd timesheet-app
devin
```

```
Add JSDoc/TSDoc documentation to all exported functions, React components,
and API route handlers that are undocumented. For React components, document
the props interface. For API routes, document request/response shapes and
error codes. Also create a `CODE_REVIEW.md` listing architectural concerns
and tech debt items.
```

#### Step 3: Review locally

```bash
git diff --stat
git diff -- "*.java"  # or "*.ts"
```

Iterate with Devin:
- *"The error documentation is incomplete — add the 403 Forbidden case"*
- *"This method's Javadoc is too long — summarize in one sentence and put details in @param tags"*
- *"Add @throws documentation for the checked exceptions"*

- **Key Takeaways:**
  - **"Documentation is code review"** — generated docs go through review, ensuring accuracy
  - **"Code review findings are actionable"** — the `CODE_REVIEW.md` gives the team a prioritized tech debt backlog
  - **"Living documentation"** — docs in the code stay closer to reality than external wikis
  - **"Local generation is fast"** — iterate on documentation quality without waiting for cloud roundtrips

- **Target Outcomes (any of these count):**
  - Comprehensive inline documentation (Javadoc, JSDoc, TSDoc)
  - `CODE_REVIEW.md` with prioritized quality concerns
  - API documentation generated from code annotations
  - All documentation following existing conventions
  - Changes ready to commit with `git add`

---

### Lab C3 — Continuous Quality Pipeline

- **Module:** [Continuous Quality Engineering](../../modules/testing-qa/continuous-quality-engineering.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app
  - [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular) — Angular frontend (alternative)
- **Objective:** Build a continuous quality pipeline that automatically enforces standards — lint, test, coverage gates, security scan, and documentation checks — so quality is maintained without manual effort

#### Step 1: Run Devin CLI

```bash
cd timesheet-app
devin
```

Research quality pipeline patterns:
- *"What quality gates are most effective at preventing bugs in production?"*
- *"What's the right balance between pipeline speed and thoroughness?"*

#### Step 2: Build the pipeline

```
Build a comprehensive continuous quality pipeline using GitHub Actions. The
pipeline should enforce these quality gates on every PR: (1) Linting passes
(ESLint + Prettier), (2) All unit tests pass, (3) Code coverage does not
drop below the current baseline (fail if new code has < 80% coverage),
(4) No new security vulnerabilities (npm audit), (5) No TypeScript type
errors (tsc --noEmit), (6) Bundle size does not increase by more than 10%.
Add a quality dashboard comment on each PR showing results. Create a
`QUALITY_GATES.md` documenting each gate.
```

> **Cloud handoff:** This lab requires autonomous cloud execution for the scheduled quality report. See the [cloud variant](README.md) for setting up Devin Scheduled Sessions that generate weekly quality reports.

#### Step 3: Test the pipeline locally

Verify each gate individually:

```bash
npx eslint . && npx prettier --check .
npm test -- --coverage
npx tsc --noEmit
npm audit
```

Iterate with Devin:
- *"Add a bypass label ('skip-quality-gates') for emergency hotfixes"*
- *"The coverage gate should only check new code coverage, not total"*
- *"Add a 'quality summary' comment template that shows gate results in a table"*

- **Key Takeaways:**
  - **"Quality gates as code"** — the pipeline defines and enforces team standards automatically
  - **"Ratchet, don't regress"** — coverage and quality metrics should only go up. The pipeline prevents backsliding
  - **"Fast feedback = adoption"** — if quality gates take 10 minutes, developers will ignore them. Keep them under 3 minutes
  - **"Scheduled quality reports"** — weekly Devin sessions that analyze quality trends give engineering leaders visibility

- **Target Outcomes (any of these count):**
  - GitHub Actions quality pipeline with multiple gates
  - PR comment showing quality gate results
  - `QUALITY_GATES.md` documenting standards and thresholds
  - Coverage baseline established and enforced
  - Pipeline tested locally before pushing

---

## Additional Challenges

Participants who finish early or want to explore further can attempt any challenge from the full [module catalog](../../modules/). Recommended extras:

| Challenge | Module | Repo | Track | Difficulty |
|-----------|--------|------|-------|------------|
| Visual Regression Testing | [Visual Regression](../../modules/testing-qa/visual-regression-testing.md) | timesheet-app | B | Intermediate |
| Accessibility Compliance | [Accessibility](../../modules/testing-qa/accessibility-compliance.md) | timesheet-app | B | Intermediate |
| Contract Testing | [Contract Testing](../../modules/testing-qa/contract-testing.md) | quickapp-microservices | B | Advanced |
| Test Framework Migration | [Test Framework Migration](../../modules/testing-qa/test-framework-migration.md) | ts-java-selenium-testng | A | Intermediate |
| API Documentation | [API Documentation](../../modules/technical-documentation/api-documentation.md) | Any | C | Beginner |

## Suggested Formats

| Format | Recommended Approach |
|--------|---------------------|
| Full day | All 3 tracks: Track A (morning) + Track B (midday) + Track C (afternoon) |
| Half day | 2 tracks: Choose any two tracks based on audience interest |
| Short session | 1 full track + 1 lab from another track |
| Sampler | Pick 1 lab from each track (e.g., A1 + B1 + C3) for breadth |
| Single lab | A1 (unit testing) or B1 (E2E testing) recommended for immediate impact |

## Repos Required

### Track A (Test Automation & Coverage)
- [ ] uc-spring-boot-upgrade-microservice-extraction
- [ ] timesheet-app (optional alternative for Labs A1, A3)
- [ ] uc-bdd-test-generation-cucumber (for Lab A2)

### Track B (End-to-End & Integration Testing)
- [ ] timesheet-app (for Lab B1, B3)
- [ ] ts-angular-realworld (optional, for Lab B1 Option B)
- [ ] quickapp-microservices (for Lab B2 Option A)
- [ ] petclinic-microservices (optional, for Lab B2 Option B)

### Track C (Continuous Quality & Code Review)
- [ ] timesheet-app (for Labs C1, C3)
- [ ] timesheet-infra (for Lab C1 Session B)
- [ ] uc-spring-boot-upgrade-microservice-extraction (for Lab C2 Option A)
- [ ] petclinic-angular (optional, for Lab C3 alternative)

## Context

- **Audience:** QA engineers, SDETs, test leads, and development teams building quality into their delivery process
- **Focus:** The full quality spectrum — test automation, system validation, and continuous assurance — demonstrating Devin as a quality engineering force multiplier
- **Devin value themes woven throughout:**
  - Local test generation — fast iteration with instant feedback
  - Subagents for parallel test generation across multiple modules
  - Devin CLI for real-time test strategy analysis and code exploration
  - Knowledge items for capturing testing conventions and patterns
  - Cloud handoff for resource-intensive tasks (long load tests, multi-service orchestration)
  - Shell integration for context-aware assistance

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../modules/devin-features/README.md) throughout the session.
