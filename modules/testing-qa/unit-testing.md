# Unit Testing

<a id="table-of-contents"></a>
## Table of Contents
- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [petclinic-backend](#petclinic-backend)
- [timesheet-app](#timesheet-app)
- [ts-java-spring-boot-realworld](#ts-java-spring-boot-realworld)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Going Further](#going-further)

## Repositories

- [petclinic-backend](#petclinic-backend)
- [timesheet-app](#timesheet-app)
- [ts-java-spring-boot-realworld](#ts-java-spring-boot-realworld)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

<a id="challenge"></a>
## Challenge

Add or improve unit test coverage for a repository, create a test coverage report, and remediate any failing tests. This is a common capacity-constrained task — teams know test coverage should be higher but deprioritize it against feature work. Devin handles the repetitive test generation so engineers can focus on reviewing test quality and identifying meaningful edge cases.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Analyze the current test coverage of petclinic-backend.
Add missing unit tests to increase coverage to at least 80%.
Generate a JaCoCo coverage report and fix any failing tests.
```

<a id="target-outcomes"></a>
## Target Outcomes

- Test coverage increased to at least 80% (or meaningful improvement from baseline)
- Coverage report generated (JaCoCo, Istanbul/nyc, etc.)
- All tests passing
- PR with coverage evidence

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin understands existing test patterns and extends them
- How Devin selects appropriate testing frameworks for a tech stack
- Devin's ability to generate meaningful (not trivial) test cases
- How to evaluate AI-generated tests for quality
- How scheduled sessions can monitor coverage trends over time and auto-generate tests for newly uncovered code

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Codebase analysis and understanding
- Test generation following existing conventions
- PR creation with coverage reports
- Devin Review for catching test quality issues in PRs

## Difficulty

Beginner to Intermediate

## Estimated Time

45 minutes

---

## <a id="petclinic-backend"></a>petclinic-backend

**Repository:** [petclinic-backend](https://github.com/Cognition-Partner-Workshops/petclinic-backend)

Canonical Spring Boot application with an existing JUnit test suite.

### Step 1: Paste into Devin

```
Analyze the current test coverage of petclinic-backend.
Add missing unit tests to increase coverage to at least 80%.
Generate a JaCoCo coverage report and fix any failing
tests.
```

### Step 2: Research with Ask Devin

- *"Which classes in petclinic-backend have the lowest test coverage? What business logic is most critical to test?"*
- *"Are there any edge cases in the service layer that aren't covered by existing tests?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the domain model and identify which service methods handle the most complex logic — these should be the priority for new tests.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the tests meaningful or just boilerplate? Do they test behavior or implementation details?
- **Leave a comment** asking Devin to add a test for a specific edge case you identify
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- Devin reads the existing JUnit conventions and extends them — it does not impose a new test style
- Coverage reports provide objective evidence of improvement
- PR review lets the team validate that generated tests are meaningful, not just line-coverage padding

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

React + Node.js/Express application with Jest tests in `backend/src/__tests__/`.

### Step 1: Paste into Devin

```
Analyze the current test coverage of timesheet-app.
Add missing Jest unit tests for the backend API routes
and service layer. Generate a coverage report and fix
any failing tests.
```

### Step 2: Research with Ask Devin

- *"What backend routes in timesheet-app have no test coverage? Which ones handle the most critical business logic?"*
- *"Should the frontend React components also have unit tests? What testing library would be appropriate?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the API routes and data model. Identify which endpoints have the most complex validation or business logic.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the tests cover both happy paths and error cases?
- **Leave a comment** asking Devin to add tests for a specific edge case (e.g., invalid date ranges, duplicate entries)

### Key Takeaways

- Devin identifies gaps in existing test suites and fills them following the project's conventions
- Full-stack apps benefit from separate backend and frontend coverage strategies
- PR feedback lets you guide Devin toward the edge cases that matter most to your domain

---

## <a id="ts-java-spring-boot-realworld"></a>ts-java-spring-boot-realworld

**Repository:** [ts-java-spring-boot-realworld](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld)

Spring Boot RealWorld example app with existing JUnit tests.

### Step 1: Paste into Devin

```
Analyze the current test coverage of
ts-java-spring-boot-realworld. Add missing unit tests
to increase coverage to at least 80%. Generate a JaCoCo
coverage report.
```

### Step 2: Research with Ask Devin

- *"Which service classes have the most complex business logic but lowest coverage?"*
- *"Are there integration tests that should supplement the unit tests?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the article/user/comment domain model and identify untested paths.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are tests isolated or do they depend on database state?
- **Leave a comment** asking Devin to refactor any tests that aren't properly isolated

### Key Takeaways

- Devin distinguishes between unit tests (isolated, fast) and integration tests (with Spring context) and generates the appropriate type
- Coverage targets are a starting point — the real value is in testing meaningful behavior

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 monolith with JUnit test infrastructure.

### Step 1: Paste into Devin

```
Analyze the current test coverage of
uc-spring-boot-upgrade-microservice-extraction. Add
missing JUnit tests targeting the service and controller
layers. Generate a JaCoCo coverage report and ensure all
tests pass.
```

### Step 2: Research with Ask Devin

- *"What is the current test coverage breakdown by package? Which domain (articles, users, comments) has the weakest coverage?"*
- *"Are there any untested GraphQL datafetchers or mutations?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the REST and GraphQL API surfaces and identify which endpoints lack test coverage.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the tests cover both REST and GraphQL paths?
- **Leave a comment** asking Devin to add coverage for a specific untested endpoint

### Key Takeaways

- Dual API surfaces (REST + GraphQL) require separate test strategies — Devin handles both
- Legacy codebases with low coverage benefit the most from automated test generation
- The PR feedback loop lets you iteratively improve test quality after the initial generation

---

<a id="going-further"></a>
## Going Further

### Event-Driven Test Generation

Connect Devin to your CI pipeline so that test coverage drops automatically trigger a Devin session:

```
CI pipeline detects coverage below threshold
    → webhook fires
    → Devin session starts with prompt:
      "Coverage dropped below 80% in module X.
       Add unit tests to restore coverage."
    → Devin opens a PR with new tests
    → CI re-runs and verifies coverage
```

This pattern is especially valuable after feature branches merge — coverage can drift downward as new code lands without tests. An event-driven trigger catches the drop immediately.

### Scheduled Coverage Monitoring

Configure a weekly Devin scheduled session to monitor test coverage trends:

- Run the test suite and generate a coverage report
- Compare against the previous week's baseline
- If any module drops below the team's threshold, generate tests to close the gap
- Open a PR with improvements and a trend summary

This turns test coverage from a reactive concern ("we should add tests") into a continuous practice.

### Divide and Conquer Across Repos

For organizations with many repositories, a parent Devin session can fan out test generation across the entire portfolio:

1. Parent scans all repos for coverage below threshold
2. Spawns a child session per repo, each following a shared test-generation Playbook
3. Each child opens a PR; engineers review and merge
4. Parent reports a summary of coverage improvements across the org

### Devin Review for Test Quality

Enable Devin Review on your repositories to catch test quality issues in every PR — not just Devin-generated ones. Devin Review can flag missing test coverage for new code, tests with no assertions, and tests that mock too many dependencies.
