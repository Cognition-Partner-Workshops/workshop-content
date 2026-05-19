# Unit Testing

## Repositories

- [petclinic-backend](#petclinic-backend)
- [timesheet-app](#timesheet-app)
- [ts-java-spring-boot-realworld](#ts-java-spring-boot-realworld)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Add or improve unit test coverage for a repository, create a test coverage report, and remediate any failing tests.

## Target Outcomes

- Test coverage increased to at least 80% (or meaningful improvement from baseline)
- Coverage report generated (JaCoCo, Istanbul/nyc, etc.)
- All tests passing
- PR with coverage evidence

## What Participants Will Learn

- How Devin understands existing test patterns and extends them
- How Devin selects appropriate testing frameworks for a tech stack
- Devin's ability to generate meaningful (not trivial) test cases
- How to evaluate AI-generated tests for quality

## Devin Features Exercised

- Codebase analysis and understanding
- Test generation
- PR creation with coverage reports

## Difficulty

Beginner to Intermediate

## Estimated Time

45 minutes

---

## <a id="petclinic-backend"></a>petclinic-backend

**Repository:** [petclinic-backend](https://github.com/Cognition-Partner-Workshops/petclinic-backend)

Canonical Spring Boot application with an existing JUnit test suite.

### Step 1: Paste into Devin

> Analyze the current test coverage of petclinic-backend. Add missing unit tests to increase coverage to at least 80%. Generate a JaCoCo coverage report and fix any failing tests. Open a PR with the changes.

### Step 2: Research with Ask Devin

- *"Which classes in petclinic-backend have the lowest test coverage? What business logic is most critical to test?"*
- *"Are there any edge cases in the service layer that aren't covered by existing tests?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the domain model and identify which service methods handle the most complex logic — these should be the priority for new tests.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the tests meaningful or just boilerplate? Do they test behavior or implementation details?
- **Leave a comment** asking Devin to add a test for a specific edge case you identify
- **Watch Devin respond** and push a follow-up commit

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

React + Node.js/Express application with Jest tests in `backend/src/__tests__/`.

### Step 1: Paste into Devin

> Analyze the current test coverage of timesheet-app. Add missing Jest unit tests for the backend API routes and service layer. Generate a coverage report and fix any failing tests. Open a PR with the changes.

### Step 2: Research with Ask Devin

- *"What backend routes in timesheet-app have no test coverage? Which ones handle the most critical business logic?"*
- *"Should the frontend React components also have unit tests? What testing library would be appropriate?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the API routes and data model. Identify which endpoints have the most complex validation or business logic.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the tests cover both happy paths and error cases?
- **Leave a comment** asking Devin to add tests for a specific edge case (e.g., invalid date ranges, duplicate entries)

---

## <a id="ts-java-spring-boot-realworld"></a>ts-java-spring-boot-realworld

**Repository:** [ts-java-spring-boot-realworld](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld)

Spring Boot RealWorld example app with existing JUnit tests.

### Step 1: Paste into Devin

> Analyze the current test coverage of ts-java-spring-boot-realworld. Add missing unit tests to increase coverage to at least 80%. Generate a JaCoCo coverage report. Open a PR with the changes.

### Step 2: Research with Ask Devin

- *"Which service classes have the most complex business logic but lowest coverage?"*
- *"Are there integration tests that should supplement the unit tests?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the article/user/comment domain model and identify untested paths.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are tests isolated or do they depend on database state?
- **Leave a comment** asking Devin to refactor any tests that aren't properly isolated

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 monolith with JUnit test infrastructure.

### Step 1: Paste into Devin

> Analyze the current test coverage of uc-spring-boot-upgrade-microservice-extraction. Add missing JUnit tests targeting the service and controller layers. Generate a JaCoCo coverage report and ensure all tests pass. Open a PR.

### Step 2: Research with Ask Devin

- *"What is the current test coverage breakdown by package? Which domain (articles, users, comments) has the weakest coverage?"*
- *"Are there any untested GraphQL datafetchers or mutations?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the REST and GraphQL API surfaces and identify which endpoints lack test coverage.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the tests cover both REST and GraphQL paths?
- **Leave a comment** asking Devin to add coverage for a specific untested endpoint
