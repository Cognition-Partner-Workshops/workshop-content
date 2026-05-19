# Test-Driven Development

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Define the functional behavior for a new feature, create the tests first, then have Devin implement the feature following TDD practices. This is a multi-session workflow: one session writes tests, another implements.

## Target Outcomes

- Failing tests committed to a feature branch (red phase)
- Feature implemented to make all tests pass (green phase)
- Tests unmodified — implementation fits the test expectations
- PR with passing tests and implementation

## What Participants Will Learn

- How Devin interprets test expectations to understand requirements
- TDD workflow with Devin: red → green → refactor
- Multi-session workflow (one session writes tests, another implements)
- How Devin respects constraints ("do not modify test files")

## Devin Features Exercised

- Test interpretation
- Implementation from test specifications
- Multi-session workflow
- Branch-based collaboration

## Difficulty

Intermediate

## Estimated Time

60 minutes (30 min per session)

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express application with Jest test infrastructure — ideal for writing backend API tests first, then implementing.

### Step 1: Paste into Devin

**Session 1 — Write Tests:**

> I want to add a "duplicate work entry" feature to timesheet-app. Write failing Jest tests for a new POST /api/work-entries/:id/duplicate endpoint that creates a copy of an existing work entry with today's date. Test: successful duplication, 404 for non-existent entry, 403 for entry owned by another user. Commit the tests to a new branch.

**Session 2 — Implement:**

> The branch feature/duplicate-entry has failing tests for a new "duplicate work entry" feature. Implement the feature so all tests pass. Do not modify the test files. Open a PR.

### Step 2: Research with Ask Devin

- *"What patterns do the existing Jest tests in timesheet-app follow? How should new tests be structured to match?"*
- *"What edge cases should the duplicate endpoint handle beyond the basic scenarios?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the existing API patterns and test conventions. This ensures the new tests fit the existing style.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — did Devin implement exactly what the tests specify? No more, no less?
- **Leave a comment** asking Devin to add error handling for a specific edge case

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot application with JUnit test infrastructure — write API-level tests first, then implement.

### Step 1: Paste into Devin

**Session 1 — Write Tests:**

> I want to add a "bookmark articles" feature to uc-spring-boot-upgrade-microservice-extraction. Write failing JUnit tests for: POST /api/articles/:slug/bookmark (bookmark an article), DELETE /api/articles/:slug/bookmark (remove bookmark), GET /api/articles/bookmarked (list bookmarked articles for current user). Test authentication requirements, duplicate bookmark handling, and 404 for non-existent articles. Commit tests to a new branch.

**Session 2 — Implement:**

> The branch feature/bookmarks has failing JUnit tests for a new "bookmark articles" feature. Implement the feature so all tests pass. Do not modify the test files. Open a PR.

### Step 2: Research with Ask Devin

- *"What data model changes are needed for bookmarks? Should it be a join table or a new entity?"*
- *"How do the existing favorite/follow features work? Can we follow the same pattern for bookmarks?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the existing user interaction patterns (favorites, follows) and use them as a template for the bookmark implementation.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the implementation follow the same patterns as existing features (favorites)?
- **Leave a comment** asking Devin to add pagination to the bookmarked articles endpoint
