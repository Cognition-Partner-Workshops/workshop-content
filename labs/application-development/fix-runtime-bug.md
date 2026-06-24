# Fix Runtime Bug

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Prerequisites](#prerequisites)
- [Notes](#notes)
- [Repositories](#repositories)
  - [timesheet-app](#timesheet-app)
  - [calcom](#calcom)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

1. Pick a repository below and start the application (or use a hosted instance)
2. Paste the sample prompt into Devin
3. Observe how Devin reproduces the bug, diagnoses the root cause, and pushes a fix
4. Review the PR and leave feedback — Devin will iterate on your comments

---

## Challenge

Try running an application and have Devin fix a bug or unexpected behavior you encounter. This challenge is intentionally open-ended — find a bug and report it to Devin.

## Target Outcomes

- Bug identified and documented with reproduction steps
- Root cause analysis performed
- Fix implemented and verified (ideally with before/after screenshots or recordings)
- PR with fix and explanation

## What Participants Will Learn

- How Devin interprets bug reports (screenshots, descriptions, reproduction steps)
- How Devin uses its browser to reproduce and verify bugs
- Devin's debugging approach (logs, code analysis, bisect)
- The value of screen recordings as test evidence
- How the **PR feedback loop** works — reviewers comment, Devin iterates, CI re-runs (see [Collaboration Model](../../reference/general-themes/collaboration-model.md))

## Devin Features Exercised

- Browser interaction for bug reproduction
- Screen recording for test evidence
- Debugging and root cause analysis
- PR creation with fix explanation
- **Devin Review** — can proactively catch related issues in the fix PR (see [Platform Capabilities → Devin Review](../../reference/general-themes/platform-capabilities.md#devin-review))

## Difficulty

Intermediate

## Estimated Time

45 minutes

## Prerequisites

The application must be running (locally or hosted). See [runtime-resources.md](../../reference/runtime-resources.md) for hosted instance details.

## Notes

- Encourage participants to use Devin's browser to interact with the running app
- The bug doesn't have to be a "real" bug — unexpected UX or edge cases count
- If no hosted instance is available, participants can ask Devin to run the app locally
- Multiple team members can comment on the same PR — Devin reads and responds to all reviewers (see [Collaboration Model → Multi-User Communication](../../reference/general-themes/collaboration-model.md#multi-user-communication))

---

## Repositories

### <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Backend on port 3001, frontend on port 5173. Login with any email (no password required).

#### Step 1: Paste into Devin

```text
Start timesheet-app locally (backend: `cd backend && npm run dev`, frontend: `cd frontend && npm run dev`). Explore the application — create work entries, manage clients, try the reporting features. Find and document any bugs or unexpected behavior. Fix the most impactful bug you find. Take before/after screenshots.
```

#### Step 2: Research with Ask Devin

- *"What are the most common types of bugs in Express + React applications? What should I look for?"*
- *"Are there any edge cases in the work entry form that might cause unexpected behavior?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the data flow and identify components that might have bugs (complex state management, API error handling, date/time logic).

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the fix address the root cause or just the symptom?
- **Leave a comment** asking Devin to add a regression test for the bug

---

### <a id="calcom"></a>calcom

**Repository:** [calcom](https://github.com/Cognition-Partner-Workshops/calcom)

Complex scheduling application. `yarn dev` starts on port 3000. See [runtime-resources.md](../../reference/runtime-resources.md) for sample credentials.

#### Step 1: Paste into Devin

```text
Start calcom locally with `yarn dev`. Explore the booking flow — create event types, test the public booking page, try different scheduling options. Find and document any bugs or unexpected behavior. Fix the most impactful bug you find. Take a screen recording of the bug and the fix.
```

#### Step 2: Research with Ask Devin

- *"What are the most complex user flows in calcom that are likely to have edge case bugs?"*
- *"Are there known issues in the GitHub Issues tab that I could try to reproduce and fix?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the booking engine and timezone handling. These are common sources of bugs in scheduling applications.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the fix handle timezone edge cases correctly?
- **Leave a comment** asking Devin to add an E2E test that prevents this bug from regressing

---

## Key Takeaways

- Devin can reproduce bugs visually using its browser and capture evidence (screenshots, screen recordings)
- Bug reports with reproduction steps produce better results than vague descriptions
- The PR feedback loop lets reviewers refine the fix iteratively — comment on the PR and Devin pushes follow-up commits
- Devin Review can catch related issues in the fix PR that a manual review might miss
- Screen recordings serve as both test evidence and documentation for the fix

---

## Going Further

- **Ticket-driven bug fixes** — tag a Jira or GitHub Issue with `Devin:Implementation` and Devin picks it up automatically, reproduces the bug, implements a fix, and opens a PR linked back to the ticket (see [Design Patterns → Event-Driven Triggers](../../reference/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers))
- **Scheduled code health analysis** — run Devin on a weekly schedule to scan for common bug patterns (unhandled promise rejections, null reference risks, missing error boundaries) and open remediation PRs proactively (see [Platform Capabilities → Scheduled Sessions](../../reference/general-themes/platform-capabilities.md#scheduled-sessions))
- **CI failure auto-triage** — connect CI failure webhooks to Devin so it reads failure logs and pushes fixes without human intervention (see [Collaboration Model → CI Check Monitoring](../../reference/general-themes/collaboration-model.md#ci-check-monitoring))
