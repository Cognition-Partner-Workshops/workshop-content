# Bug Hunt — MERN Ecommerce

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Repository](#repository)
- [Target Outcomes](#target-outcomes)
- [Sample Prompt](#sample-prompt)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Prerequisites](#prerequisites)
- [Notes](#notes)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

1. Create a branch `workshop-<attendee_id>` from the `buggy` branch
2. Paste the sample prompt into Devin
3. Observe how Devin sets up the app, reproduces bugs, and pushes fixes
4. Review the PR and leave feedback — Devin iterates on comments

---

## Challenge

The `buggy` branch of `i-retail-mern-ecommerce` contains 8 intentionally
planted bugs across the frontend (React/Redux) and backend (Express/MongoDB)
layers of a B2C ecommerce store. Participants ask Devin to find and fix as
many bugs as possible. The bugs span cart logic, authentication, search,
tax calculation, inventory management, wishlists, and order placement.

<a id="repository"></a>
## Repository

**Repository:** [i-retail-mern-ecommerce](https://github.com/Cognition-Partner-Workshops/i-retail-mern-ecommerce)

| | |
|---|---|
| **Branch** | `buggy` |
| **Tech Stack** | JavaScript, React, Redux, Express, Node.js, MongoDB |
| **License** | MIT |
| **Local Setup** | `npm install` at root installs client + server. `npm run dev` starts both. Requires MongoDB (or use `docker-compose up`). |

### Bug Summary

| # | Layer | Area | Symptom |
|---|-------|------|---------|
| 1 | Frontend | Cart total | Cart total is wrong — adds price + quantity instead of multiplying |
| 2 | Backend | Product search | Search returns no results — queries only inactive products |
| 3 | Backend | Tax calculation | Sales tax is 100× too low — missing multiplier in tax formula |
| 4 | Backend | Login | Correct passwords are rejected — negated comparison |
| 5 | Frontend | Cart removal | Removing a cart item also removes the next item (off-by-one) |
| 6 | Backend | Inventory | Product quantity increases after purchase instead of decreasing |
| 7 | Backend | Wishlist | Wishlist shows all users' items instead of just the current user |
| 8 | Frontend | Order placement | Orders are placed with a total of $0 |

---

## Target Outcomes

- At least 5 of 8 bugs identified and fixed
- Each fix addresses the root cause, not just the symptom
- PR with clear descriptions of what was wrong and what was changed
- Application runs without errors after fixes are applied

---

<a id="sample-prompt"></a>
## Sample Prompt

### Option A: Open-ended bug hunt

```text
Check out the buggy branch of the
Cognition-Partner-Workshops/i-retail-mern-ecommerce
repository. This MERN ecommerce app has several bugs planted
across the React/Redux frontend and Express/MongoDB backend.

Key directories to audit:
- client/app/containers/ (React/Redux actions and reducers)
- server/routes/api/ (Express route handlers)
- server/utils/store.js (tax and pricing logic)
- server/middleware/ (auth middleware)

Set up the app locally using docker-compose, then review the
code systematically — look at cart operations, authentication,
product search, tax calculations, inventory management,
wishlist queries, and order placement. Identify and fix every
bug you find.

Expected output: a PR where each commit fixes one bug, with
a summary table in the PR description listing each bug
(file path, line, root cause, and fix description).
```

### Option B: Targeted fix (single bug)

```text
Check out the buggy branch of the
Cognition-Partner-Workshops/i-retail-mern-ecommerce
repository. Users report that the cart total is wrong — when
they add items, the total doesn't match the expected
price × quantity calculation.

Investigate the cart total logic in
client/app/containers/Cart/actions.js (the
calculateCartTotal function) and fix the bug.

Expected output: a PR with the fix and a brief explanation
of the root cause in the PR description.
```

---

## What Participants Will Learn

- How Devin performs systematic code review to find bugs across a full-stack
  application
- How Devin traces data flow from frontend Redux actions through API routes
  to database operations
- The difference between fixing symptoms vs. root causes
- How the **PR feedback loop** works — reviewers comment, Devin iterates,
  CI re-runs (see [Collaboration Model](../../reference/general-themes/collaboration-model.md))

## Devin Features Exercised

- Multi-file code analysis across frontend and backend
- Pattern recognition for common bug categories (off-by-one, sign errors,
  negated conditions, missing filters)
- Docker-based local environment setup
- PR creation with structured fix explanations
- **Devin Review** — can proactively catch related issues in the fix PR
  (see [Platform Capabilities → Devin Review](../../reference/general-themes/platform-capabilities.md#devin-review))

## Difficulty

Beginner to Intermediate

## Estimated Time

30–60 minutes (depends on how many bugs are targeted)

## Prerequisites

- Docker and Docker Compose (for local MongoDB)
- Node.js 18+ (for running the app directly)

## Notes

- The `buggy` branch is based on the `init` branch with a single commit
  containing all 8 bugs — participants can compare the two branches to
  verify their fixes
- Each bug is a single-line change, making them ideal for demonstrating
  precise, targeted fixes
- If you have limited time, start with Option B targeting a single bug
- For a deeper exercise, try Option A and aim to find all 8
- Multiple team members can comment on the same PR — Devin reads and responds
  to all reviewers (see [Collaboration Model → Multi-User Communication](../../reference/general-themes/collaboration-model.md#multi-user-communication))

---

## Key Takeaways

- Devin can systematically audit a full-stack codebase and identify bugs
  across frontend and backend layers without being told where to look
- Single-line bugs in critical paths (auth, cart, payments) can have
  outsized business impact — Devin catches these through code analysis,
  not just test execution
- The PR feedback loop lets reviewers refine fixes iteratively — comment
  on the PR and Devin pushes follow-up commits
- Bug-hunt exercises translate directly to real-world code review and
  incident response workflows that Devin handles at scale through
  event-driven triggers (see [Design Patterns → Event-Driven Triggers](../../reference/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers))

---

## Going Further

- **Ticket-driven bug fixes** — tag a Jira or GitHub Issue with
  `Devin:Implementation` and Devin picks it up automatically, reproduces
  the bug, implements a fix, and opens a PR linked back to the ticket
  (see [Design Patterns → Event-Driven Triggers](../../reference/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers))
- **Scheduled code health analysis** — run Devin on a weekly schedule to
  scan for common bug patterns (unhandled promise rejections, null reference
  risks, missing error boundaries) and open remediation PRs proactively
  (see [Platform Capabilities → Scheduled Sessions](../../reference/general-themes/platform-capabilities.md#scheduled-sessions))
- **Divide and conquer** — for large codebases, use child sessions to
  audit frontend and backend in parallel, then consolidate findings into
  a single PR (see [Platform Capabilities → Child Sessions](../../reference/general-themes/platform-capabilities.md#child-agents-divide-and-conquer))
