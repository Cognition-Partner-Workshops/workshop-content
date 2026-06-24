# End-to-End Testing

<a id="table-of-contents"></a>
## Table of Contents
- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Prerequisites](#prerequisites)
- [timesheet-app](#timesheet-app)
- [calcom](#calcom)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [calcom](#calcom)

---

<a id="challenge"></a>
## Challenge

Try Devin's capabilities for end-to-end testing against a locally hosted application. This challenge explores browser-based testing, Playwright/Cypress test authoring, and visual verification. E2E tests are often deprioritized because they are time-consuming to write and maintain — Devin handles the scaffolding and initial authoring so engineers focus on reviewing test reliability.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Set up and run timesheet-app locally (backend on port
3001, frontend on port 5173). Write Playwright E2E tests
for the work entries workflow: login, create a work entry,
verify it appears in the list, edit it, and delete it.
Run the tests and take a screen recording.
```

<a id="target-outcomes"></a>
## Target Outcomes

- E2E test suite created or expanded (Playwright or Cypress)
- Tests run successfully against the running application
- Screen recording or screenshots as test evidence
- PR with test files and evidence

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin interacts with running applications via its browser
- Devin's screen recording capabilities for test evidence
- Writing and running Playwright/Cypress tests with Devin
- How event-driven triggers can re-run E2E suites when code changes land, catching regressions before they reach production

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Browser interaction and screen recording
- Test framework setup and execution
- Runtime application testing
- PR creation with test evidence

## Difficulty

Intermediate

## Estimated Time

60 minutes

## Prerequisites

The application must be running locally on Devin's machine or hosted externally. See [runtime-resources.md](../../reference/runtime-resources.md) for hosted instance details.

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Simpler application — good for first-timers with E2E testing. Backend on port 3001, frontend on port 5173. Login with any email (no password required).

### Step 1: Paste into Devin

```
Set up and run timesheet-app locally (backend on port
3001, frontend on port 5173). Write Playwright E2E tests
for the work entries workflow: login, create a work entry,
verify it appears in the list, edit it, and delete it.
Run the tests and take a screen recording.
```

### Step 2: Research with Ask Devin

- *"What are the main user workflows in timesheet-app that would benefit from E2E tests?"*
- *"Does the app already have any test infrastructure (Playwright config, test utilities) that I should build on?"*
- Use insights to write tests for additional workflows (client management, reporting)

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the frontend routes and components. Identify which pages have the most interactive elements that need E2E coverage.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the tests robust or will they be flaky? Do they use proper selectors?
- **Leave a comment** asking Devin to add a test for an edge case (e.g., submitting a form with missing fields)
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- Devin can launch, interact with, and test a running application end-to-end on its own VM
- Screen recordings provide visual evidence that goes beyond pass/fail — reviewers see exactly what happened
- Playwright's locator strategy matters for test stability — review whether Devin chose resilient selectors

---

## <a id="calcom"></a>calcom

**Repository:** [calcom](https://github.com/Cognition-Partner-Workshops/calcom)

Rich scheduling application with existing Playwright configuration. `yarn dev` starts on port 3000.

### Step 1: Paste into Devin

```
Start calcom locally with `yarn dev`. Write Playwright
E2E tests for the booking flow: navigate to a public
booking page, select a time slot, fill in attendee
details, and confirm the booking. Use the existing
Playwright config in the repo. Run the tests and take
a screen recording.
```

### Step 2: Research with Ask Devin

- *"What Playwright tests already exist in calcom? What workflows are untested?"*
- *"What are the most common user-reported issues in scheduling apps that E2E tests should catch?"*
- Use insights to target gaps in the existing test suite

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the booking engine architecture, event types, and user roles. Use this to write tests that cover different booking scenarios (round-robin, collective, recurring).

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the tests handle loading states and async operations properly?
- **Leave a comment** asking Devin to add a test for a specific booking edge case (e.g., timezone handling, conflicting bookings)
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- Complex applications with existing test infrastructure benefit from Devin extending — not replacing — the existing patterns
- E2E tests for booking flows validate the most revenue-critical user journey
- Devin Review can flag new PRs that lack E2E coverage for user-facing changes

---

<a id="going-further"></a>
## Going Further

### Event-Driven E2E Regression

Connect your CI pipeline to trigger Devin E2E sessions on every PR that touches frontend code:

```
PR opened with changes to src/pages/ or src/components/
    → GitHub Actions webhook fires
    → Devin session starts: "Run the E2E suite against
       this branch. If any tests fail, diagnose and fix."
    → Devin pushes fixes or reports flaky tests
```

This catches UI regressions before they reach the main branch.

### Scheduled E2E Health Checks

Configure a daily Devin scheduled session to run the full E2E suite against your staging environment. If tests fail, Devin opens a PR with the fix or escalates if it cannot resolve the issue — catching environment drift and flaky tests before they block deployments.

### Devin Review for E2E Coverage

Enable Devin Review to flag PRs that change user-facing flows but don't include corresponding E2E test updates. This keeps the test suite in sync with the application as it evolves.
