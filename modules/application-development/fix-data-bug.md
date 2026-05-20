# Fix Data Bug

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
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

1. Start timesheet-app locally (backend + frontend)
2. Paste the sample prompt into Devin
3. Observe how Devin traces the data flow from API to database to identify the root cause
4. Review the PR and leave feedback — Devin will iterate on your comments

---

## Challenge

Fix a known data persistence bug in the application. This challenge requires investigating the data flow from API to database to understand multi-tenancy boundaries.

## Target Outcomes

- Root cause identified (data model / query filtering issue)
- Fix implemented preserving correct data isolation (work entries per-user, clients shared)
- Verification that the fix doesn't break work entry isolation
- PR with fix and explanation

## What Participants Will Learn

- How Devin investigates data flow (API → database → query → response)
- Understanding multi-tenancy boundaries (what's shared vs. user-scoped)
- Database schema and query analysis
- The importance of testing data bugs with multiple user scenarios
- How the **PR feedback loop** works — reviewers comment, Devin iterates, CI re-runs (see [Collaboration Model](../../shared/general-themes/collaboration-model.md))

## Devin Features Exercised

- Database schema analysis
- Backend code investigation
- Data model reasoning
- PR creation with explanation
- **Devin Review** — can catch data isolation issues in the fix PR (see [Platform Capabilities → Devin Review](../../shared/general-themes/platform-capabilities.md#devin-review))

## Difficulty

Intermediate

## Estimated Time

45 minutes

## Prerequisites

Runtime helpful but not required — the bug can be found by code analysis alone. Backend: `cd backend && npm run dev` (port 3001). Frontend: `cd frontend && npm run dev` (port 5173).

## Notes

- This pairs well with the UI bug challenge (fix-ui-bug) for a "bug bash" session
- The fix requires careful thought about which data is shared vs. user-scoped:
  - Clients = shared (org-wide)
  - Work entries = per-user
- Participants should verify the fix doesn't break work entry isolation
- Multiple team members can comment on the same PR — Devin reads and responds to all reviewers (see [Collaboration Model → Multi-User Communication](../../shared/general-themes/collaboration-model.md#multi-user-communication))

---

## Repositories

### <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

**Bug:** Clients do not persist when you log out and log back in with a different email. Clients are intended to be shared by all users of the application.

- **Symptom:** User A creates clients. User B logs in and doesn't see User A's clients.
- **Expected:** Clients should be shared/visible to all users (they are org-wide, not per-user).
- **Root Cause Area:** Likely in the database query filtering — clients are probably filtered by `user_email` when they shouldn't be, or the data model needs adjusting.

#### Step 1: Paste into Devin

```text
There's a data bug in timesheet-app: clients created by one user are not visible to other users after logging out and back in with a different email. Clients should be shared across all users. Investigate the database schema and queries, find the root cause, and fix it. Make sure work entries still belong to individual users.
```

#### Step 2: Research with Ask Devin

- *"How is the client data model related to users in timesheet-app? Is there a user_email foreign key that shouldn't be there?"*
- *"What other data in the app should be shared vs. user-scoped?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the data model relationships. Map out which entities are user-scoped and which should be organization-wide.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the fix correctly scope clients as shared while keeping work entries per-user?
- **Leave a comment** asking Devin to add a test that verifies clients are visible across users

---

## Key Takeaways

- Devin can trace data flow end-to-end (API → service → query → database) to identify root causes
- Data bug fixes require careful reasoning about ownership and isolation boundaries
- The PR feedback loop lets reviewers ask for additional verification (e.g., "add a multi-user test") and Devin follows up
- Devin Review can catch data isolation regressions in future PRs

---

## Going Further

- **Ticket-driven data bug fixes** — tag a Jira or GitHub Issue with `Devin:Implementation` and Devin picks it up automatically, traces the data flow, implements a fix, and opens a PR linked back to the ticket (see [Design Patterns → Event-Driven Triggers](../../shared/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers))
- **Scheduled data integrity checks** — run Devin on a weekly schedule to analyze database queries for common multi-tenancy issues (missing user scoping, incorrect joins, data leakage risks) and open remediation PRs (see [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md#scheduled-sessions))
- **Shared context for data models** — add knowledge notes describing which entities are user-scoped vs. shared so Devin applies the correct scoping on every future task (see [Architecture Strengths → Shared Context Layer](../../shared/general-themes/architecture-strengths.md#shared-context-layer))
