# Continuous Quality Engineering

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-bdd-test-generation-cucumber](#uc-bdd-test-generation-cucumber)
- [petclinic-angular](#petclinic-angular)

---

## Challenge

Set up Devin as a continuous quality engineering agent — scheduled sessions that monitor test coverage, detect flaky tests, audit test quality, and open PRs to remediate gaps automatically. This is the capstone QA challenge: participants move from "Devin writes tests on demand" to "Devin maintains test quality continuously as a team member."

## Target Outcomes

- Scheduled Devin session configured for recurring test coverage monitoring
- Playbook capturing the team's test quality standards (coverage thresholds, forbidden patterns, required test types)
- Knowledge items encoding project-specific testing conventions
- Flaky test detection run with identified flaky tests and proposed fixes
- Test smell audit identifying anti-patterns (shared state, over-mocking, missing assertions)
- PR(s) with coverage improvements and/or test quality fixes

## What Participants Will Learn

- How to configure Devin scheduled sessions for continuous QA hygiene
- How Playbooks encode repeatable QA methodology that any team member can trigger
- How Knowledge items build a shared context layer that makes Devin's test output better over time
- How Devin's VM lifecycle enables persistent state between scheduled runs (coverage baselines, trend data)
- How multi-repo workspace allows Devin to monitor quality across an entire service portfolio

## Devin Features Exercised

- Scheduled sessions (recurring QA automation)
- Playbooks (encode test methodology)
- Knowledge layer (project-specific test conventions)
- Multi-repo workspace (cross-repo quality monitoring)
- Child sessions (fan out coverage work across repos)
- VM lifecycle (persistent state between runs)
- Ask Devin (plan the QA strategy before automating)

## Difficulty

Advanced

## Estimated Time

45 minutes

## Notes

- This lab works best as a capstone after participants have completed other QA labs (Unit Testing, E2E Testing, BDD Test Generation). The Knowledge items and Playbooks created in this lab build on patterns discovered in earlier labs.
- Participants don't need to wait for a scheduled session to actually execute — the goal is to set up the schedule and Playbook, then manually trigger a session to demonstrate the workflow.

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

React + Node.js full-stack application — good target for continuous QA monitoring with both frontend and backend test surfaces.

### Step 1: Start with Ask Devin (recommended)

Before creating a session, use **Ask Devin** to plan the QA strategy:
- *"What is the current test coverage of timesheet-app? Which areas have the weakest coverage and would benefit most from automated monitoring?"*
- *"What recurring QA tasks would benefit from weekly automation — coverage checks, dependency audits, lint enforcement, test smell detection?"*
- *"What test quality standards should we encode in a Playbook for this project? What coverage threshold, which test types are required for new code, what anti-patterns should be flagged?"*

Use the answers to craft a Playbook and Knowledge items before proceeding.

### Step 2: Paste into Devin (copy-paste this prompt into Devin)

> Set up a continuous quality engineering practice for timesheet-app.
>
> Part 1 — Test coverage baseline:
> Run the existing test suite and generate a coverage report. Identify the 5 files/modules with the lowest coverage. Write tests to bring the worst offender above 80% coverage.
>
> Part 2 — Flaky test detection:
> Run the test suite 10 times in sequence. Log pass/fail results for each test across all runs. Identify any tests that produce inconsistent results. For each flaky test found, diagnose the root cause (timing dependency, shared state, environment sensitivity) and propose a fix.
>
> Part 3 — Test smell audit:
> Scan the test suite for common anti-patterns: tests that depend on execution order, tests with no assertions, tests that mock too many dependencies (>3 mocks), tests with hardcoded dates/timestamps, and tests that write to shared state. Document findings in a `TEST_QUALITY_REPORT.md`.
>
> Open a PR with coverage improvements, flaky test fixes, and the quality report.

### Step 3: Create a Playbook

After reviewing Devin's output from Step 2, create a **Playbook** that encodes the QA methodology:

> **Playbook: Weekly QA Audit**
> 1. Run test suite and generate coverage report
> 2. Compare coverage against baseline (80% threshold per module)
> 3. If any module drops below threshold, generate tests to close the gap
> 4. Run flaky test detection (10 consecutive runs)
> 5. Report flaky tests with root cause analysis
> 6. Scan for test smells and anti-patterns
> 7. Open a PR with improvements and a summary report

This Playbook can be triggered by any team member or scheduled to run weekly.

### Step 4: Set Up a Scheduled Session

Configure a **Devin Scheduled Session** that runs the Weekly QA Audit Playbook:
- **Frequency:** Weekly (e.g., every Monday at 6 AM)
- **Playbook:** Weekly QA Audit (from Step 3)
- **Repository:** timesheet-app
- **Expected outcome:** PR opened with any coverage improvements and a quality report

This demonstrates how teams use Devin for continuous code hygiene — the QA equivalent of automated dependency bumps.

### Step 5 (Optional): Review & Give Feedback

- **Review the quality report** — are the identified test smells real issues or false positives?
- **Leave a comment** asking Devin to prioritize fixing a specific anti-pattern
- **Create a Knowledge item** capturing a project-specific testing convention (e.g., "All API route tests must include both happy path and 400/404 error cases")

---

## <a id="uc-bdd-test-generation-cucumber"></a>uc-bdd-test-generation-cucumber

**Repository:** [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber)

Spring Boot + Cucumber BDD framework — use for continuous BDD coverage monitoring and scenario gap detection.

### Step 1: Paste into Devin

> Analyze the BDD test coverage in uc-bdd-test-generation-cucumber. Map every REST API endpoint to its corresponding Gherkin feature file. Identify endpoints that have no BDD scenarios, endpoints with only happy-path coverage (no error scenarios), and endpoints missing boundary condition tests.
>
> Generate a `BDD_COVERAGE_MATRIX.md` that shows: endpoint → feature file → scenarios (happy path, error, boundary). Fill the gaps by writing new Gherkin scenarios for uncovered endpoints.
>
> Open a PR with the coverage matrix and new scenarios.

### Step 2: Create a Playbook

After reviewing results, create a Playbook for recurring BDD coverage auditing:

> **Playbook: BDD Coverage Audit**
> 1. List all REST API endpoints from controllers
> 2. Map each endpoint to its Gherkin feature file
> 3. For each endpoint, check: has happy path scenario? has error scenario? has boundary scenario?
> 4. Generate missing scenarios following existing Cucumber step definition patterns
> 5. Run `mvn test` to verify all scenarios pass
> 6. Update the BDD_COVERAGE_MATRIX.md
> 7. Open a PR with new scenarios and updated matrix

### Step 3 (Optional): Review & Give Feedback

- **Review the coverage matrix** — does it accurately reflect which endpoints need more BDD coverage?
- **Leave a comment** asking Devin to add data-driven Scenario Outlines for the most complex endpoints
- **Create a Knowledge item** about the project's Gherkin style conventions

---

## <a id="petclinic-angular"></a>petclinic-angular

**Repository:** [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular)

Angular frontend — use for continuous frontend test quality monitoring across component tests and E2E tests.

### Step 1: Paste into Devin

> Analyze the test quality in petclinic-angular. For each Angular component, check whether it has a corresponding `.spec.ts` file. For each existing spec file, audit: does it test user-visible behavior (not implementation details)? Does it cover error states? Does it test input validation? Are the mocks minimal and realistic?
>
> Generate a `COMPONENT_TEST_AUDIT.md` that shows: component → has spec? → tests behavior? → tests errors? → tests validation? → mock quality.
>
> For the 3 components with the weakest test quality, rewrite or improve their tests following Angular testing best practices. Open a PR with the audit report and improved tests.

### Step 2: Create a Knowledge Item

Based on the audit, create a Knowledge item that captures the project's testing standards:

> **Knowledge: PetClinic Angular Testing Standards**
> - Every component must have a `.spec.ts` file
> - Tests should assert on user-visible behavior (rendered text, element visibility), not implementation details (private methods, internal state)
> - Error states must be tested: what does the component show when the API returns an error?
> - Forms must have validation tests: required fields, min/max length, pattern matching
> - Mocks should be minimal: prefer HttpClientTestingModule over mocking the entire service

This Knowledge item will automatically inform future Devin sessions working on this repo.

### Step 3 (Optional): Review & Give Feedback

- **Review the audit** — do the quality scores match your intuition about which components have weak tests?
- **Leave a comment** asking Devin to apply the testing standards to a specific untested component
