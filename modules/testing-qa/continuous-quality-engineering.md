# Continuous Quality Engineering

<a id="table-of-contents"></a>
## Table of Contents
- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Notes](#notes)
- [timesheet-app](#timesheet-app)
- [uc-bdd-test-generation-cucumber](#uc-bdd-test-generation-cucumber)
- [petclinic-angular](#petclinic-angular)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-bdd-test-generation-cucumber](#uc-bdd-test-generation-cucumber)
- [petclinic-angular](#petclinic-angular)

---

<a id="challenge"></a>
## Challenge

Set up Devin as a continuous quality engineer — using Playbooks, Knowledge, and Scheduled Sessions to perform recurring test coverage audits, flaky test detection, and test quality improvement. This is the most advanced testing module: participants don't just use Devin for a single task, they configure a persistent quality practice.

This exercises Devin's platform capabilities beyond one-shot prompts: Playbooks encode methodology, Knowledge captures project-specific conventions, and Scheduled Sessions run the methodology on a recurring basis.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Run the test suite for timesheet-app and generate a
quality report covering: overall test coverage, tests
that are likely flaky (non-deterministic), test files
that haven't been updated in the last 6 months, and
test anti-patterns (empty assertions, commented-out tests,
tests with no assertions).
```

<a id="target-outcomes"></a>
## Target Outcomes

- Quality audit report identifying coverage gaps, flaky tests, and test anti-patterns
- Playbook created for recurring quality audits
- Knowledge item capturing project-specific testing conventions
- Scheduled Session configured (or documented) for weekly QA runs
- PR with coverage improvements and quality report

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How to create Devin Playbooks that encode repeatable QA methodology
- How to use Knowledge items to capture project-specific testing standards
- How to configure Scheduled Sessions for continuous quality monitoring
- The difference between one-shot Devin tasks and continuous Devin practices
- How event-driven triggers complement scheduled sessions for comprehensive coverage

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Playbook creation and execution
- Knowledge item creation
- Scheduled Session configuration
- Test suite analysis and improvement
- PR creation with quality reports

## Difficulty

Advanced

## Estimated Time

75 minutes

## Notes

- Participants don't need to wait for a scheduled session to actually execute — the goal is to set up the schedule and Playbook, then manually trigger a session to try the workflow
- This module is best run after participants have completed at least one other testing module (unit-testing, e2e-testing, etc.) so they have context for what "quality" means
- The Playbook and Knowledge created in this module can be reused across other repos

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express + React application — use for an initial quality audit and Playbook creation.

### Step 1: Paste into Devin

```
Run the test suite for timesheet-app and generate a
quality report covering: overall test coverage, tests
that are likely flaky (non-deterministic), test files
that haven't been updated in the last 6 months, and
test anti-patterns (empty assertions, commented-out tests,
tests with no assertions).
```

### Step 2: Review and Refine

Review the quality report. Use it to inform the Playbook you'll create in the next step — what checks produced the most actionable findings?

### Step 3: Create a Playbook

Based on the results, create a Playbook for recurring quality audits:

```
Playbook: Weekly QA Audit

1. Run the full test suite and capture pass/fail/skip
   counts
2. Generate coverage report and compare against the
   baseline (80% target)
3. Identify flaky tests (run suite 3x, flag tests that
   pass inconsistently)
4. Scan for test anti-patterns: empty assertions,
   commented-out tests, tests with no assertions,
   tests that never fail
5. Check for stale test files (not updated in 6+ months
   while source files changed)
6. Fix the top 3 highest-priority issues found
7. Open a PR with improvements and a summary report
```

This Playbook can be triggered by any team member or scheduled to run weekly.

### Step 4: Set Up a Scheduled Session

Configure a **Devin Scheduled Session** that runs the Weekly QA Audit Playbook:
- **Frequency:** Weekly (e.g., every Monday at 6 AM)
- **Playbook:** Weekly QA Audit (from Step 3)
- **Repository:** timesheet-app
- **Expected outcome:** PR opened with any coverage improvements and a quality report

This is how teams use Devin for continuous code hygiene — the QA equivalent of automated dependency bumps.

### Step 5 (Optional): Review & Give Feedback

- **Review the quality report** — are the identified test smells real issues or false positives?
- **Leave a comment** asking Devin to prioritize fixing a specific anti-pattern
- **Create a Knowledge item** capturing a project-specific testing convention (e.g., "All API route tests must include both happy path and 400/404 error cases")

### Key Takeaways

- Playbooks encode methodology that any team member can trigger — not just the person who wrote the original prompt
- Scheduled Sessions turn Devin from a reactive tool into a continuous practice
- Knowledge items accumulate project context over time, making each Devin session more effective

---

## <a id="uc-bdd-test-generation-cucumber"></a>uc-bdd-test-generation-cucumber

**Repository:** [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber)

Spring Boot + Cucumber BDD framework — use for continuous BDD coverage monitoring and scenario gap detection.

### Step 1: Paste into Devin

```
Analyze the BDD test coverage in
uc-bdd-test-generation-cucumber. Map every REST API
endpoint to its corresponding Gherkin feature file.
Identify endpoints that have no BDD scenarios, endpoints
with only happy-path coverage (no error scenarios), and
endpoints missing boundary condition tests.

Generate a BDD_COVERAGE_MATRIX.md that shows: endpoint
→ feature file → scenarios (happy path, error, boundary).
Fill the gaps by writing new Gherkin scenarios for
uncovered endpoints.
```

### Step 2: Create a Playbook

After reviewing results, create a Playbook for recurring BDD coverage auditing:

```
Playbook: BDD Coverage Audit

1. List all REST API endpoints from controllers
2. Map each endpoint to its Gherkin feature file
3. For each endpoint, check: has happy path scenario?
   has error scenario? has boundary scenario?
4. Generate missing scenarios following existing Cucumber
   step definition patterns
5. Run `mvn test` to verify all scenarios pass
6. Update the BDD_COVERAGE_MATRIX.md
7. Open a PR with new scenarios and updated matrix
```

### Step 3 (Optional): Review & Give Feedback

- **Review the coverage matrix** — does it accurately reflect which endpoints need more BDD coverage?
- **Leave a comment** asking Devin to add data-driven Scenario Outlines for the most complex endpoints
- **Create a Knowledge item** about the project's Gherkin style conventions

### Key Takeaways

- BDD coverage matrices make test gaps visible to the whole team
- A Playbook ensures the audit methodology stays consistent across runs and team members
- Scenario Outlines with Examples tables are ideal for systematic boundary testing

---

## <a id="petclinic-angular"></a>petclinic-angular

**Repository:** [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular)

Angular frontend — use for continuous frontend test quality monitoring across component tests and E2E tests.

### Step 1: Paste into Devin

```
Analyze the test quality in petclinic-angular. For each
Angular component, check whether it has a corresponding
.spec.ts file. For each existing spec file, audit: does
it test user-visible behavior (not implementation
details)? Does it cover error states? Does it test input
validation? Are the mocks minimal and realistic?

Generate a COMPONENT_TEST_AUDIT.md that shows: component
→ has spec? → tests behavior? → tests errors? → tests
validation? → mock quality.

For the 3 components with the weakest test quality,
rewrite or improve their tests following Angular testing
best practices.
```

### Step 2: Create a Knowledge Item

Based on the audit, create a Knowledge item that captures the project's testing standards:

```
Knowledge: PetClinic Angular Testing Standards

- Every component must have a .spec.ts file
- Tests should assert on user-visible behavior (rendered
  text, element visibility), not implementation details
  (private methods, internal state)
- Error states must be tested: what does the component
  show when the API returns an error?
- Forms must have validation tests: required fields,
  min/max length, pattern matching
- Mocks should be minimal: prefer HttpClientTestingModule
  over mocking the entire service
```

This Knowledge item will automatically inform future Devin sessions working on this repo.

### Step 3 (Optional): Review & Give Feedback

- **Review the audit** — do the quality scores match your intuition about which components have weak tests?
- **Leave a comment** asking Devin to apply the testing standards to a specific untested component

### Key Takeaways

- Knowledge items persist across sessions — once captured, the testing standard applies to all future work on this repo
- Component test audits reveal whether tests are testing behavior or implementation details
- Angular's TestBed patterns can be opinionated — Knowledge items help Devin follow the team's preferences

---

<a id="going-further"></a>
## Going Further

### Full Continuous Quality Pipeline

Combine event-driven and scheduled approaches for comprehensive quality engineering:

```
Scheduled (weekly):
    → Devin runs the QA Audit Playbook
    → Opens a PR with coverage improvements and trend report

Event-driven (per PR):
    → Devin Review checks new PRs for test quality
    → Flags missing tests, weak assertions, test anti-patterns

Event-driven (CI failure):
    → Test failure in CI triggers Devin session
    → Devin diagnoses and fixes the failure
    → Pushes a fix commit
```

### Team-Based Quality Operation

Position Devin as a shared team resource for quality:
- Multiple team members can trigger the same Playbook with different parameters
- Knowledge items accumulate project conventions from different team members
- Scheduled sessions run independently of who is available — quality doesn't depend on a single person's availability

### Cross-Repo Quality Dashboard

Use a parent Devin session to run quality audits across multiple repositories and generate a consolidated quality dashboard showing coverage trends, flaky test counts, and anti-pattern scores for the entire portfolio.
