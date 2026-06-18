# Test-Driven Development

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Repositories](#repositories)
  - [timesheet-app](#timesheet-app)
  - [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

1. Pick a repository below
2. **Session 1:** Paste the "Write Tests" prompt — Devin creates failing tests on a feature branch
3. **Session 2:** Paste the "Implement" prompt — Devin implements the feature to make tests pass
4. Review the PR and leave feedback — Devin will iterate on your comments

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
- How the **PR feedback loop** works — reviewers comment, Devin iterates, CI re-runs (see [Collaboration Model](../../shared/general-themes/collaboration-model.md))

## Devin Features Exercised

- Test interpretation
- Implementation from test specifications
- Multi-session workflow
- Branch-based collaboration
- **Devin Review** — can verify the implementation matches test expectations (see [Platform Capabilities → Devin Review](../../shared/general-themes/platform-capabilities.md#devin-review))

## Difficulty

Intermediate

## Estimated Time

60 minutes (30 min per session)

---

## Repositories

### <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express application with Jest test infrastructure — ideal for writing backend API tests first, then implementing.

#### Step 1: Paste into Devin

**Session 1 — Write Tests:**

```text
I want to add a "duplicate work entry" feature to timesheet-app. Write failing Jest tests for a new POST /api/work-entries/:id/duplicate endpoint that creates a copy of an existing work entry with today's date. Test: successful duplication, 404 for non-existent entry, 403 for entry owned by another user. Commit the tests to a new branch.
```

**Session 2 — Implement:**

```text
The branch feature/duplicate-entry has failing tests for a new "duplicate work entry" feature. Implement the feature so all tests pass. Do not modify the test files.
```

#### Step 2: Research with Ask Devin

- *"What patterns do the existing Jest tests in timesheet-app follow? How should new tests be structured to match?"*
- *"What edge cases should the duplicate endpoint handle beyond the basic scenarios?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the existing API patterns and test conventions. This ensures the new tests fit the existing style.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — did Devin implement exactly what the tests specify? No more, no less?
- **Leave a comment** asking Devin to add error handling for a specific edge case

---

### <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot application with JUnit test infrastructure — write API-level tests first, then implement.

#### Step 1: Paste into Devin

**Session 1 — Write Tests:**

```text
I want to add a "bookmark articles" feature to uc-spring-boot-upgrade-microservice-extraction. Write failing JUnit tests for: POST /api/articles/:slug/bookmark (bookmark an article), DELETE /api/articles/:slug/bookmark (remove bookmark), GET /api/articles/bookmarked (list bookmarked articles for current user). Test authentication requirements, duplicate bookmark handling, and 404 for non-existent articles. Commit tests to a new branch.
```

**Session 2 — Implement:**

```text
The branch feature/bookmarks has failing JUnit tests for a new "bookmark articles" feature. Implement the feature so all tests pass. Do not modify the test files.
```

#### Step 2: Research with Ask Devin

- *"What data model changes are needed for bookmarks? Should it be a join table or a new entity?"*
- *"How do the existing favorite/follow features work? Can we follow the same pattern for bookmarks?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the existing user interaction patterns (favorites, follows) and use them as a template for the bookmark implementation.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the implementation follow the same patterns as existing features (favorites)?
- **Leave a comment** asking Devin to add pagination to the bookmarked articles endpoint

---

## Key Takeaways

- TDD with Devin works as a two-session workflow: one session writes tests (red), another implements (green)
- Devin respects constraints like "do not modify test files" — it adapts its implementation to match the test expectations
- Multi-session workflows demonstrate how Devin maintains context across branches and sessions
- The PR feedback loop lets reviewers refine the implementation iteratively
- Devin Review can verify that the implementation covers all test scenarios without introducing untested behavior

---

## Going Further

- **Scheduled test coverage expansion** — run Devin on a weekly schedule to identify modules with low test coverage, write tests for uncovered code paths, and open PRs (see [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md#scheduled-sessions))
- **Parallel TDD across modules** — use child agents to write tests and implement features across multiple modules simultaneously, each following the same TDD playbook (see [Platform Capabilities → Child Agents](../../shared/general-themes/platform-capabilities.md#child-agents-divide-and-conquer))
- **Playbook-driven TDD** — create a playbook encoding your team's TDD process (analyze requirements → write failing tests → implement → refactor → verify coverage) so every feature follows consistent test-first methodology (see [Platform Capabilities → Playbooks](../../shared/general-themes/platform-capabilities.md#playbooks))
