# Mutation Testing

## Repositories

- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [timesheet-app](#timesheet-app)

---

## Challenge

Set up mutation testing (PIT for Java, Stryker for JavaScript) to evaluate test suite effectiveness. Find and fix surviving mutants to improve test quality.

## Target Outcomes

- Mutation testing framework configured and running
- Mutation score report generated
- Tests added or improved to kill surviving mutants
- PR with mutation testing setup and improved tests

## What Participants Will Learn

- How Devin configures mutation testing frameworks for different tech stacks
- How Devin interprets mutation testing reports to identify weak test coverage
- Devin's ability to write targeted tests that kill surviving mutants
- The difference between code coverage and mutation-based test effectiveness

## Devin Features Exercised

- Test quality analysis
- Build tool configuration
- Test generation
- PR creation

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 monolith with an existing JUnit test suite and Gradle build — ideal for PIT mutation testing.

### Step 1: Paste into Devin

> Set up PIT (pitest) mutation testing for uc-spring-boot-upgrade-microservice-extraction. Configure the Gradle pitest plugin, run the mutation analysis against the service and controller layers, and generate a mutation coverage report. Identify the top surviving mutants and add or improve JUnit tests to kill them. Open a PR with the pitest configuration, mutation report summary, and improved tests.

### Step 2: Research with Ask Devin

- *"What is the current test coverage of uc-spring-boot-upgrade-microservice-extraction? Which packages have the weakest coverage that mutation testing would expose?"*
- *"What PIT mutators are most valuable for a Spring Boot REST API? Should we use the default set or add custom mutators?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the service layer logic and identify which methods have complex branching — these are where surviving mutants are most likely.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the new tests target specific surviving mutants, or are they generic? Check that each new test has a clear assertion tied to a mutation
- **Leave a comment** asking Devin to focus on killing mutants in a specific service class
- **Watch Devin respond** and push a follow-up commit

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express backend with Jest tests — suitable for Stryker mutation testing.

### Step 1: Paste into Devin

> Set up Stryker mutation testing for the backend of timesheet-app. Configure Stryker with the Jest test runner, run mutation analysis against the API routes and validation logic, and generate a mutation score report. Identify surviving mutants and add or improve Jest tests to kill them. Open a PR with the Stryker configuration, mutation report summary, and improved tests.

### Step 2: Research with Ask Devin

- *"What test coverage gaps exist in timesheet-app's backend? Which route handlers have the most complex validation logic?"*
- *"What Stryker mutators are most relevant for Express.js API code? Should we focus on conditional boundary mutants or method call mutants?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the API route structure and validation schemas. Focus on routes with business logic like hours validation (0-24 range) and client ownership checks.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Stryker configuration target the right source files? Are the new tests specific enough to kill individual mutants?
- **Leave a comment** asking Devin to improve mutation coverage for a specific route handler
- **Watch Devin respond** and push a follow-up commit
