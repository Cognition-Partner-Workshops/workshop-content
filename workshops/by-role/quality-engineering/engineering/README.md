# Workshop: Quality Engineering and Assurance

## Overview

| | |
|---|---|
| **Focus** | Testing strategy, test automation, code quality, and continuous assurance — building confidence in software through systematic verification |
| **Duration** | Flexible (3 tracks, 3 labs each) |
| **Audience** | QA engineers, test leads, SDETs, development teams focused on quality, and engineering leaders building quality culture |
| **Tracks** | **Track A:** Test Automation & Coverage · **Track B:** End-to-End & Integration Testing · **Track C:** Continuous Quality & Code Review |

## Workshop Narrative

This workshop covers the three dimensions of quality engineering: automating test creation at every level of the pyramid, validating system behavior end-to-end, and building continuous quality practices into the development workflow. Each track is self-contained — participants can follow all three in sequence for a full-day experience, or focus on one or two tracks in a shorter session.

The tracks are designed to show Devin working across the quality spectrum:
- **Track A** shows Devin as a test automation engineer — generating unit tests, BDD scenarios, and measuring coverage to identify gaps
- **Track B** shows Devin as a system tester — running applications end-to-end, writing Playwright/Selenium tests, and validating cross-service integration
- **Track C** shows Devin as a quality advocate — automating code review, performing static analysis, generating documentation, and building continuous quality pipelines

## Getting the Most from This Workshop

> **Devin works autonomously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Each lab has a "Paste into Devin" step and a separate "Review & Give Feedback" step. Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin will keep working in the background.
- **Try parallel sessions.** Several labs suggest running multiple Devin sessions at once — for example, generating unit tests on one repo while running E2E tests on another.
- **Use Ask Devin to understand test coverage gaps.** Before writing tests, use Ask Devin to analyze existing coverage and identify the highest-risk untested code.
- **Build up Devin's knowledge as you go.** Save testing conventions (framework choices, assertion style, test data patterns) as Knowledge items so future test generation follows the same patterns.
- **Leave PR comments to steer Devin.** After Devin opens a test PR, review the assertions for quality — are they testing behavior or just padding coverage? Leave comments to improve.

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

- **Module:** [Unit Testing](../../../../../modules/testing-qa/unit-testing.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot app with existing JUnit infrastructure
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app with Jest (alternative)
- **Objective:** Analyze existing test coverage, identify under-tested modules, and generate meaningful unit tests that verify real behavior — not just padding coverage

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

Choose one or run multiple in parallel:

**Option A — JUnit Tests (Spring Boot):**
```
Analyze the current test coverage of uc-spring-boot-upgrade-microservice-extraction. Run the existing tests and generate a JaCoCo coverage report. Identify the top 5 modules with the lowest test coverage. Write JUnit tests for each, following the existing test patterns: use MockMvc for controller tests, Mockito for service tests, and integration tests for repository layer. Aim for at least 80% line coverage on each module. Include negative test cases (invalid inputs, not-found scenarios, unauthorized access).
```

**Option B — Jest Tests (Node.js):**
```
Analyze the test coverage of timesheet-app's backend. Run `npm test -- --coverage` to see current coverage. Identify the 5 least-tested API routes and service modules. Write Jest tests for each: (1) Unit tests for service functions with mocked dependencies, (2) Integration tests for API routes using supertest, (3) Edge case tests for error handling, empty inputs, and boundary conditions. Follow existing test patterns.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What is the current test coverage breakdown by package? Which domain (articles, users, comments) has the weakest coverage?"*
- *"What testing patterns does the codebase use? JUnit with Mockito? TestContainers? What conventions should new tests follow?"*
- *"What's the most impactful code to test — high complexity, high change frequency, or high business value?"*
- Use the analysis to start a **second session** — try generating property-based tests or data-driven test scenarios

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand which service methods handle the most complex logic — these should be the priority for new tests. Try:
- Have Devin generate **property-based tests** that verify invariants across random inputs
- Ask Devin to add **parameterized tests** for data-driven scenarios
- Try having Devin create a **test coverage matrix** mapping tests to business requirements
- Ask Devin to identify **dead code** — code that exists but has no execution path from tests or production

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR, focus your review on **test quality**:
- **Meaningful assertions:** Are the tests checking behavior, or just padding coverage with trivial assertions?
- **Edge cases:** Do the tests cover error cases, boundary conditions, and null/empty inputs?
- **Independence:** Are tests isolated, or do they depend on each other or shared mutable state?
- **Readability:** Can a new team member understand what's being tested from the test name alone?

**Leave a feedback comment** and watch Devin respond:
- *"This test just asserts 'not null' — add meaningful assertions about the response content"*
- *"Add edge case tests for when the article slug contains special characters"*
- *"The test depends on a specific database state — use test fixtures or setup methods"*

- **Key Takeaways:**
  - **"Devin writes meaningful tests"** — not just coverage padding. It analyzes the codebase to understand what needs testing and generates tests that verify real behavior
  - **"Test generation from patterns"** — Devin reads the existing test framework and style conventions to generate tests that fit the project
  - **"Coverage is a metric, not a goal"** — 80% coverage with meaningful assertions is better than 100% coverage with trivial ones
  - **"Quality at scale"** — test generation is a great candidate for parallel Devin sessions across multiple repos

- **Target Outcomes (any of these count):**
  - JaCoCo or Istanbul coverage report showing improvement
  - 5+ new test files with meaningful assertions
  - Edge case and negative test coverage
  - Tests following existing framework conventions
  - PR with test files and coverage evidence

---

### Lab A2 — BDD Test Generation

- **Module:** [BDD Test Generation](../../../../../modules/testing-qa/bdd-test-generation.md)
- **Repositories:**
  - [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber) — Spring Boot + Cucumber BDD framework with pre-built step definitions
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot RealWorld app (alternative — add Cucumber to an existing app)
- **Objective:** Generate BDD test scenarios from API specifications — write Gherkin feature files that describe business behavior, implement step definitions, and run the full BDD suite

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

**Option A — Extend Existing BDD Suite:**
```
Review the uc-bdd-test-generation-cucumber codebase. This is a Spring Boot + Cucumber BDD framework with pre-built step definitions for REST API testing. Run `mvn test` to see the existing 16 scenarios pass. Then add new Gherkin feature files that test edge cases for the existing Users API: (1) Creating a user with missing required fields (expect 400), (2) Creating a user with duplicate ID (expect 409 or appropriate error), (3) Pagination and sorting, (4) Input validation (invalid email, too-long names). Also create a new `OrderController` with endpoints for managing orders and write corresponding Gherkin feature files. Use Scenario Outlines with Examples tables for data-driven testing.
```

**Option B — Add BDD to Existing App:**
```
Add Cucumber BDD testing to uc-spring-boot-upgrade-microservice-extraction. Set up the Cucumber test infrastructure (dependencies, test runner, feature file directory). Write Gherkin feature files for the Articles API: (1) Feature: Create Article — scenarios for valid creation, missing fields, duplicate slugs, (2) Feature: Article Feed — scenarios for global feed, user feed, tag filtering, pagination, (3) Feature: Favorite Article — scenarios for favoriting, unfavoriting, favorite count. Implement step definitions that call the API. Run all scenarios and verify they pass.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What Gherkin best practices should be followed — should scenarios be independent? How should test data be managed?"*
- *"What's the difference between Scenario and Scenario Outline in Cucumber? When should each be used?"*
- *"How do Cucumber step definitions map to REST API calls? What assertion patterns work best for JSON responses?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin generate **Scenario Outlines** with large Examples tables for combinatorial testing
- Ask Devin to add **background steps** for common setup (user creation, authentication)
- Try having Devin create a **living documentation** HTML report from the Cucumber results
- Ask Devin to add **tag-based test organization** (@smoke, @regression, @api, @negative)

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR, focus your review on **BDD quality**:
- **Business readability:** Can a non-developer understand the scenarios?
- **Independence:** Does each scenario clean up after itself?
- **Completeness:** Are happy path, sad path, and edge cases all covered?
- **Data-driven:** Are Scenario Outlines used where appropriate to avoid duplication?

**Leave a feedback comment:**
- *"Use Scenario Outline with Examples for the validation tests — test all field types in one scenario"*
- *"The step definitions should use more descriptive method names that read like English"*
- *"Add @smoke tags to the critical path scenarios for fast feedback in CI"*

- **Key Takeaways:**
  - **"BDD bridges dev and business"** — Gherkin scenarios are readable by non-developers, making them a communication and documentation tool
  - **"Scenario Outlines reduce duplication"** — data-driven testing avoids copy-pasting scenarios with minor variations
  - **"Step definitions are reusable"** — well-written steps compose into new scenarios without new code
  - **"Living documentation"** — Cucumber test results serve as up-to-date API documentation that's always verified

- **Target Outcomes (any of these count):**
  - Gherkin feature files with meaningful business scenarios
  - Step definitions implemented and tests passing
  - Scenario Outlines with Examples tables for data-driven tests
  - New API endpoints tested via BDD
  - PR with feature files, step definitions, and test execution evidence

---

### Lab A3 — Mutation Testing & Test Quality

- **Module:** [Mutation Testing](../../../../../modules/testing-qa/mutation-testing.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot app with existing JUnit tests
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — Node.js app with Jest tests (alternative)
- **Objective:** Use mutation testing to evaluate test quality — mutants that survive indicate tests that pass without actually verifying behavior. Kill surviving mutants by adding meaningful assertions.

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

**Option A — PIT Mutation Testing (Java):**
```
Set up PIT mutation testing for uc-spring-boot-upgrade-microservice-extraction. Add the PIT Gradle plugin and configure it to run on the Articles domain (io.spring.core.article and io.spring.application.article packages). Run mutation testing and analyze the report — identify which mutants survived (tests that pass even when code is changed). For the top 10 surviving mutants, improve the existing tests or write new ones that kill the mutant by adding assertions that would fail if the mutation were present. Document the before/after mutation scores in a `MUTATION_TESTING_REPORT.md`.
```

**Option B — Stryker Mutation Testing (JavaScript):**
```
Set up Stryker mutation testing for timesheet-app's backend. Configure Stryker to run on the service layer modules. Run mutation testing and analyze which mutants survive. For surviving mutants, add or improve test assertions to kill them. Focus on: (1) Conditional mutations (if statements changed), (2) Return value mutations (return true changed to return false), (3) Arithmetic mutations (+ changed to -). Document findings in a `MUTATION_TESTING_REPORT.md`.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What is mutation testing and how does it differ from code coverage? Why can 100% coverage still have weak tests?"*
- *"What are the most common mutation operators? Which ones reveal the most meaningful test gaps?"*
- *"What's a good mutation score target? Is 100% mutation kill rate realistic or necessary?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add **mutation testing to CI** with a minimum mutation score gate
- Ask Devin to identify **equivalent mutants** (mutations that don't change behavior) and exclude them
- Try having Devin compare **mutation scores across packages** to find the weakest-tested areas

#### Step 4 (Optional): Review & Give Feedback

Focus on **test improvement quality**:
- **Meaningful kills:** Do the new assertions verify important behavior, not trivial details?
- **No test pollution:** Do the improved tests still pass on the unmutated code?
- **Sustainable:** Can the mutation tests run in CI without being too slow?

**Leave a feedback comment:**
- *"This assertion kills the mutant but it's testing an implementation detail — find a behavior-level assertion"*
- *"Add mutation testing to the CI pipeline with a 60% minimum score threshold"*
- *"Focus on the conditional mutations first — those reveal the most dangerous test gaps"*

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
  - PR with improved tests and mutation evidence

---

## Track B: End-to-End & Integration Testing

Track B demonstrates Devin as a system tester. Participants will write and run E2E tests against running applications, validate cross-service integration, and perform load testing to verify performance characteristics.

### Lab B1 — Playwright E2E Tests

- **Module:** [End-to-End Testing](../../../../../modules/testing-qa/end-to-end-testing.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js full-stack application
  - [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld) — Angular RealWorld app with existing Playwright tests (alternative)
- **Objective:** Write and run E2E tests against a running application — exercise real user workflows through the browser, discover issues through testing, and fix what you find

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

**Option A — Full E2E Suite (timesheet-app):**
```
Set up and run timesheet-app locally (backend on port 3001, frontend on port 5173). Write Playwright E2E tests for the core user workflows: (1) Login flow — valid credentials succeed, invalid credentials show error, (2) Client management — create, edit, delete a client, (3) Work entry lifecycle — create entry for a client, verify it appears in list, edit hours, delete, (4) Reporting — verify reports show correct totals after creating entries, (5) Edge cases — submit empty forms, special characters in names, very long text. Run the tests and take a screen recording. If any tests fail because of application bugs, fix the bugs too.
```

**Option B — Extend Existing E2E Tests (Angular):**
```
Review the existing Playwright tests in ts-angular-realworld (in the e2e/ directory). Run the existing tests to verify they pass. Then extend the test suite with: (1) Article lifecycle — create, read, update, delete an article, (2) Social features — follow a user, favorite an article, comment on an article, (3) Tag filtering — create articles with tags and verify tag-based filtering works, (4) Error scenarios — verify graceful handling of 401, 404, 500 responses. Take a screen recording of the full test run.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the main user workflows in timesheet-app that would benefit from E2E tests?"*
- *"What Playwright best practices should be followed — proper selectors, waiting strategies, test isolation?"*
- *"What are the most common causes of flaky E2E tests and how can they be prevented?"*
- Use insights to write tests for additional workflows (CSV/PDF export, bulk operations)

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the frontend routes, components, and API contracts. Try:
- Have Devin add **visual regression tests** with screenshot comparisons
- Ask Devin to add **accessibility tests** (axe-core integration with Playwright)
- Try having Devin add **performance assertions** (page load time < 2 seconds)
- Ask Devin to generate a **test report** with screenshots and timings for each test

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR, focus your review on **test robustness**:
- **Selectors:** Are tests using stable selectors (data-testid, role) or fragile ones (CSS class, xpath)?
- **Waiting:** Do tests use proper waiting strategies or unreliable sleep/timeout?
- **Isolation:** Does each test clean up after itself and not depend on other tests?
- **Bug fixes:** If Devin found and fixed bugs during testing, does the fix address the root cause?

**Leave a feedback comment:**
- *"Use data-testid attributes instead of CSS selectors for stability"*
- *"Replace the sleep(2000) with waitForSelector — this will be flaky in CI"*
- *"Add a test for submitting the form with missing required fields — it should show validation errors"*

- **Key Takeaways:**
  - **"Devin uses its browser"** — it interacts with the running application via its built-in browser, clicking through flows just like a human tester
  - **"E2E tests discover bugs"** — automated browser testing finds issues that unit tests miss (rendering bugs, form validation, navigation)
  - **"Screen recordings as evidence"** — Devin's recordings show exactly what was tested, providing visual proof for stakeholders
  - **"Fix bugs during testing"** — when E2E tests find bugs, Devin fixes them in the same session

- **Target Outcomes (any of these count):**
  - Playwright E2E test suite covering core user workflows
  - Screen recording of test execution
  - Bug fixes discovered during E2E testing
  - Tests using stable selectors and proper waiting strategies
  - PR with test files, bug fixes, and screen recording evidence

---

### Lab B2 — Cross-Service Integration Testing

- **Module:** [Cross-Service Integration Testing](../../../../../modules/testing-qa/cross-service-integration-testing.md)
- **Repositories:**
  - [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices) — .NET microservices (Identity, Customer, Order, Product, Notification)
  - [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices) — Spring Boot microservices (alternative)
- **Objective:** Write integration tests that verify multiple microservices work correctly together — testing the contracts, data flow, and error handling between services

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

**Option A — .NET Microservices Integration:**
```
Write cross-service integration tests for quickapp-microservices. Focus on the Order → Product → Notification flow: (1) Create a product via the Product service, (2) Place an order via the Order service referencing that product, (3) Verify the Notification service receives the order-placed event with correct data, (4) Verify the Order service correctly validates product existence before accepting orders, (5) Test error scenarios — order for non-existent product, order with invalid customer. Use Docker Compose to run all services together.
```

**Option B — Spring Boot Microservices Integration:**
```
Write cross-service integration tests for petclinic-microservices. Test the full workflow: (1) Register a new pet owner via the customers-service, (2) Add a pet via the customers-service, (3) Schedule a visit via the visits-service, (4) Verify the API gateway correctly routes and aggregates data from both services, (5) Test circuit breaker behavior — what happens when the visits-service is down? Use Docker Compose to run the services and write tests using RestAssured or similar.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What services exist in the microservices repo and how do they communicate? REST, events, or both?"*
- *"What's the best approach for integration testing microservices — Docker Compose, TestContainers, or mock services?"*
- *"How should integration tests handle asynchronous communication (events)? Polling, callbacks, or test containers?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add **contract tests** (Pact or Spring Cloud Contract) for each service boundary
- Ask Devin to add **chaos testing** — what happens when one service is slow or returns errors?
- Try having Devin set up **test containers** for isolated integration test environments

#### Step 4 (Optional): Review & Give Feedback

Focus on **integration quality**:
- **Real services:** Are tests running against actual services or mocks? Both have value for different purposes
- **Async handling:** For event-driven flows, are tests waiting properly for events to propagate?
- **Cleanup:** Do tests clean up created data to avoid interfering with other tests?

**Leave a feedback comment:**
- *"Add a test for the timeout scenario — what happens if the Product service takes 10 seconds to respond?"*
- *"The event assertion should poll with a timeout rather than using Thread.sleep"*
- *"Add a contract test that verifies the Order service's expected request shape matches what Product service accepts"*

- **Key Takeaways:**
  - **"Integration tests verify the seams"** — they catch bugs that hide in the boundaries between services (serialization, routing, error propagation)
  - **"Docker Compose enables realistic testing"** — running all services together mimics production behavior
  - **"Contract tests prevent drift"** — when services evolve independently, contracts ensure they still speak the same language
  - **"Error paths are the most important tests"** — what happens when a downstream service fails matters more than the happy path

- **Target Outcomes (any of these count):**
  - Integration test suite running against multiple services
  - Docker Compose configuration for test environment
  - Happy path and error scenario coverage
  - Contract tests for service boundaries
  - PR with integration tests and test execution evidence

---

### Lab B3 — Performance & Load Testing

- **Modules:** [Performance Testing](../../../../../modules/testing-qa/performance-testing.md) + [Load Testing & Benchmarking](../../../../../modules/testing-qa/load-testing-benchmarking.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app (establish baseline and identify bottlenecks)
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot app (alternative)
- **Objective:** Create performance tests that establish baselines, identify bottlenecks, and verify the application handles expected load — demonstrating Devin building load testing infrastructure

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

**Option A — k6 Load Testing (Node.js):**
```
Set up k6 load testing for timesheet-app. Create load test scripts that: (1) Simulate 50 concurrent users performing typical workflows (login, create entry, view reports), (2) Run a ramp-up test from 1 to 100 users over 5 minutes, (3) Establish performance baselines (p95 latency, error rate, throughput), (4) Identify the breaking point — at what concurrency does the application start failing? Document findings in a `PERFORMANCE_REPORT.md` including baseline metrics, bottleneck analysis, and recommendations. If you identify a performance bottleneck (e.g., missing database index, N+1 query), fix it and re-run to show improvement.
```

**Option B — Gatling Load Testing (Java):**
```
Set up Gatling load testing for uc-spring-boot-upgrade-microservice-extraction. Create simulation scripts that: (1) Test the articles API under load (list, create, read single article), (2) Simulate realistic traffic patterns (80% reads, 15% writes, 5% deletes), (3) Run a sustained load test at 30 req/sec for 5 minutes, (4) Measure p50, p95, and p99 response times. Establish SLO-style thresholds (p95 < 200ms, error rate < 1%). Document findings and any optimizations in `PERFORMANCE_REPORT.md`.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the typical performance bottlenecks in Express + SQLite applications? What should I watch for?"*
- *"What's the difference between load testing, stress testing, and soak testing? Which is most appropriate for this app?"*
- *"What performance SLOs are reasonable for a CRUD application — what p95 latency should I target?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add **database query analysis** to identify N+1 queries and missing indexes
- Ask Devin to add **performance budgets to CI** that fail the build if latency regresses
- Try having Devin compare **before and after** performance for an optimization

#### Step 4 (Optional): Review & Give Feedback

Focus on **test realism**:
- **Realistic scenarios:** Do the load profiles reflect actual usage patterns?
- **Meaningful metrics:** Are p95/p99 being measured, not just averages?
- **Actionable findings:** Do the results tell you what to fix?

**Leave a feedback comment:**
- *"Add a soak test that runs for 30 minutes to detect memory leaks"*
- *"The load test should include think time between requests to be realistic"*
- *"Add the performance test to CI as a nightly job that alerts on regression"*

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
  - PR with load test scripts and performance evidence

---

## Track C: Continuous Quality & Code Review

Track C demonstrates Devin as a quality advocate. Participants will set up linting and static analysis, automate code review with documentation generation, and build continuous quality pipelines that maintain standards automatically.

### Lab C1 — Linting & Static Analysis

- **Module:** [Linting & Static Analysis](../../../../../modules/testing-qa/linting-static-analysis.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js (ESLint, Prettier)
  - [timesheet-infra](https://github.com/Cognition-Partner-Workshops/timesheet-infra) — Terraform (terraform fmt, tflint)
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Java/Gradle (Spotless, Checkstyle) (alternative)
- **Objective:** Set up and enforce code quality standards through automated linting, formatting, and static analysis — catch issues before they reach code review

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

Run these as **parallel sessions** for different tech stacks:

**Session A — JavaScript/TypeScript Linting:**
```
Set up comprehensive linting for timesheet-app: (1) Configure ESLint with TypeScript rules for both frontend and backend, (2) Add Prettier for consistent formatting, (3) Create a pre-commit hook (using Husky + lint-staged) that auto-formats and lints on every commit, (4) Fix all existing lint errors and warnings, (5) Add a CI step that fails the build on lint violations. Document the lint configuration choices in a `CODING_STANDARDS.md`.
```

**Session B — Terraform Linting:**
```
Set up Terraform quality enforcement for timesheet-infra: (1) Run `terraform fmt -check` and fix any formatting issues, (2) Add tflint with the AWS ruleset for Terraform best practices, (3) Add checkov or tfsec for security scanning of Terraform configurations, (4) Create a CI pipeline that runs fmt check + tflint + security scan on PRs, (5) Fix any security findings. Document IaC standards in an `IAC_STANDARDS.md`.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What ESLint rules are most valuable for catching real bugs vs. just enforcing style?"*
- *"What's the difference between linting (static analysis) and formatting (style enforcement)? Should they use the same tool?"*
- *"What Terraform security checks are most important — what does tfsec/checkov catch?"*
- Consider creating a **Devin Knowledge item** capturing the team's lint configuration

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add **SonarQube analysis** for deeper code quality metrics
- Ask Devin to configure **auto-fix rules** that Devin can apply automatically
- Try having Devin set up **code complexity thresholds** that flag functions exceeding limits

#### Step 4 (Optional): Review & Give Feedback

Focus on **enforcement value**:
- **Rule selection:** Are the rules catching real issues or just bikeshedding?
- **Auto-fix:** Can most issues be fixed automatically, or do they require manual intervention?
- **CI integration:** Does the pipeline fail fast on lint issues?

**Leave a feedback comment:**
- *"Disable the 'no-console' rule for the backend — server-side logging via console is acceptable"*
- *"Add a rule for maximum function length (30 lines) to encourage refactoring"*
- *"The Terraform security scan found a public S3 bucket — please fix that finding"*

- **Key Takeaways:**
  - **"Shift quality left"** — catching issues at commit time is cheaper than catching them in code review or production
  - **"Automated formatting eliminates bikeshedding"** — when Prettier formats code, nobody argues about style
  - **"Pre-commit hooks = zero effort"** — developers don't need to remember to lint; it happens automatically
  - **"Security scanning in CI"** — infrastructure security issues are caught before deployment, not after a breach

- **Target Outcomes (any of these count):**
  - Lint configuration (ESLint, Prettier, tflint, Checkstyle, or similar)
  - Pre-commit hooks enforcing standards
  - All existing lint violations fixed
  - CI step that fails on violations
  - PR with lint config and `CODING_STANDARDS.md`

---

### Lab C2 — Code Review & Documentation

- **Modules:** [PR Review Automation](../../../../../modules/devops-cicd/pr-review-automation.md) + [Inline Documentation](../../../../../modules/technical-documentation/inline-documentation.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot (Javadoc)
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js (JSDoc/TSDoc) (alternative)
- **Objective:** Use Devin to generate comprehensive documentation for public APIs and key modules, and observe how PR Review automatically provides quality feedback on the documentation PR itself

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

**Option A — Javadoc Generation (Spring Boot):**
```
Review the codebase of uc-spring-boot-upgrade-microservice-extraction and add Javadoc documentation to all public interfaces, classes, and methods that are currently undocumented. Focus on: (1) All REST controller methods (describe the endpoint, parameters, response, and error cases), (2) All service layer public methods (describe business logic), (3) All repository interfaces (describe the query). Follow existing documentation style. Also create a `CODE_REVIEW.md` that identifies the top 10 code quality concerns (missing validation, error handling gaps, coupling issues, naming problems).
```

**Option B — JSDoc/TSDoc Generation (Node.js/React):**
```
Review timesheet-app's codebase and add JSDoc/TSDoc documentation to all exported functions, React components, and API route handlers that are currently undocumented. For React components, document the props interface. For API routes, document the request/response shapes and error codes. For utility functions, document parameters, return values, and edge cases. Also create a `CODE_REVIEW.md` listing architectural concerns and tech debt items.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What documentation conventions does this codebase follow? Are there any existing Javadoc/JSDoc patterns?"*
- *"What are the most common code smells in this repo? Which areas have the most technical debt?"*
- *"How should API documentation describe error responses — should every possible error code be documented?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin generate **API documentation** (OpenAPI/Swagger) from the controller annotations
- Ask Devin to create an **architecture diagram** (Mermaid) showing module dependencies
- Try having Devin add **ADR (Architecture Decision Records)** for major design choices

#### Step 4 (Optional): Review PR & Observe PR Review Agent

Once Devin opens a PR, observe how **PR Review** handles documentation changes:
- Does PR Review flag any inaccurate documentation?
- Does it suggest additional areas that need documentation?
- **Leave your own feedback:**
  - *"The error documentation is incomplete — add the 403 Forbidden case"*
  - *"This method's Javadoc is too long — summarize in one sentence and put details in @param tags"*
  - *"Add @throws documentation for the checked exceptions"*

- **Key Takeaways:**
  - **"Documentation is code review"** — Devin's generated docs go through the same PR review process, ensuring accuracy
  - **"PR Review reviews docs too"** — the automated review agent catches inaccurate or incomplete documentation
  - **"Code review findings are actionable"** — the `CODE_REVIEW.md` gives the team a prioritized tech debt backlog
  - **"Living documentation"** — docs in the code stay closer to reality than external wikis

- **Target Outcomes (any of these count):**
  - Comprehensive inline documentation (Javadoc, JSDoc, TSDoc)
  - `CODE_REVIEW.md` with prioritized quality concerns
  - PR Review agent's feedback on the documentation PR
  - API documentation generated from code annotations
  - PR with documentation and review iterations

---

### Lab C3 — Continuous Quality Pipeline

- **Module:** [Continuous Quality Engineering](../../../../../modules/testing-qa/continuous-quality-engineering.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app
  - [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular) — Angular frontend (alternative)
- **Objective:** Build a continuous quality pipeline that automatically enforces standards — lint, test, coverage gates, security scan, and documentation checks — so quality is maintained without manual effort

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Build a comprehensive continuous quality pipeline for timesheet-app using GitHub Actions. The pipeline should enforce these quality gates on every PR: (1) Linting passes (ESLint + Prettier), (2) All unit tests pass, (3) Code coverage does not drop below the current baseline (fail if new code has < 80% coverage), (4) No new security vulnerabilities (npm audit), (5) No TypeScript type errors (tsc --noEmit), (6) Bundle size does not increase by more than 10% (for frontend). Add a quality dashboard comment on each PR showing the results of each gate (pass/fail with metrics). Create a `QUALITY_GATES.md` documenting each gate, its threshold, and how to fix failures.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What quality gates are most effective at preventing bugs in production?"*
- *"How do you set coverage thresholds that encourage improvement without blocking emergency fixes?"*
- *"What's the right balance between pipeline speed and thoroughness for quality gates?"*
- Consider creating a **Devin Scheduled Session** that generates a weekly quality report

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add **trend tracking** that shows quality metrics over time
- Ask Devin to add **quality badges** (coverage, lint status) to the repo README
- Try having Devin set up **PR size limits** (warn on PRs with more than 500 changed lines)
- Ask Devin to integrate **SonarCloud** for comprehensive code quality tracking

#### Step 4 (Optional): Review & Give Feedback

Focus on **pipeline effectiveness**:
- **Fast feedback:** Does the pipeline complete quickly enough for developers to act on?
- **Actionable output:** When a gate fails, does the developer know exactly what to fix?
- **Not too strict:** Are the thresholds reasonable, or will they block legitimate work?

**Leave a feedback comment:**
- *"Add a bypass label ('skip-quality-gates') for emergency hotfixes — but require team lead approval"*
- *"The coverage gate should only check new code coverage, not total — otherwise old uncovered code blocks all PRs"*
- *"Add a 'quality summary' comment template that shows gate results in a nice table"*

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
  - PR with quality pipeline and documentation

---

## Additional Challenges

Participants who finish early or want to explore further can attempt any challenge from the full [module catalog](../../../../../modules/). Recommended extras:

| Challenge | Module | Repo | Track | Difficulty |
|-----------|--------|------|-------|------------|
| Visual Regression Testing | [Visual Regression](../../../../../modules/testing-qa/visual-regression-testing.md) | timesheet-app | B | Intermediate |
| Accessibility Compliance | [Accessibility](../../../../../modules/testing-qa/accessibility-compliance.md) | timesheet-app | B | Intermediate |
| Contract Testing | [Contract Testing](../../../../../modules/testing-qa/contract-testing.md) | quickapp-microservices | B | Advanced |
| Test Framework Migration | [Test Framework Migration](../../../../../modules/testing-qa/test-framework-migration.md) | ts-java-selenium-testng | A | Intermediate |
| API Documentation | [API Documentation](../../../../../modules/technical-documentation/api-documentation.md) | Any | C | Beginner |

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
  - Autonomous test generation — kick off coverage sessions and review later
  - Parallel sessions for testing across multiple repos/services simultaneously
  - Ask Devin for test strategy analysis and coverage gap identification
  - Knowledge and Playbooks for capturing testing conventions and patterns
  - Scheduled sessions for continuous quality monitoring (weekly coverage reports, regression detection)
  - Screen recordings as test evidence for stakeholder communication
  - PR Review as an automatic quality gate for all changes

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../../../../modules/devin-features/README.md) throughout the session.
