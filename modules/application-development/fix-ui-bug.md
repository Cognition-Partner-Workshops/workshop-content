# Fix UI Bug

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
3. Observe how Devin investigates the CSS issue using its browser and pushes a fix
4. Review the PR and leave feedback — Devin will iterate on your comments

---

## Challenge

Fix a known visual bug in the application. This is a specific, verifiable challenge that shows how Devin investigates CSS/styling issues and visually verifies fixes.

## Target Outcomes

- Bug root cause identified (CSS/styling issue)
- Fix implemented and visually verified
- Before/after screenshots or screen recording as evidence
- PR with fix

## What Participants Will Learn

- How Devin investigates CSS/styling issues
- How Devin uses its browser to visually verify fixes
- Screen recording / screenshot as proof of fix
- Material-UI component styling patterns
- How the **PR feedback loop** works — reviewers comment, Devin iterates, CI re-runs (see [Collaboration Model](../../shared/general-themes/collaboration-model.md))

## Devin Features Exercised

- Browser interaction (visual inspection)
- CSS debugging
- Screenshot/recording for before/after comparison
- PR creation
- **Devin Review** — can catch additional styling regressions in the fix PR (see [Platform Capabilities → Devin Review](../../shared/general-themes/platform-capabilities.md#devin-review))

## Difficulty

Beginner to Intermediate

## Estimated Time

30 minutes

## Prerequisites

Application must be running to see the bug visually. Backend: `cd backend && npm run dev` (port 3001). Frontend: `cd frontend && npm run dev` (port 5173). Login with any email address.

## Notes

- This is a good "quick win" challenge — specific, verifiable, and satisfying to fix
- Pairs well with the data bug challenge (fix-data-bug) for a "bug bash" session
- Multiple team members can comment on the same PR — Devin reads and responds to all reviewers (see [Collaboration Model → Multi-User Communication](../../shared/general-themes/collaboration-model.md#multi-user-communication))

---

## Repositories

### <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

**Bug:** The Client dropdown's label text has a strikethrough when you create an hour entry on the running app's `/work-entries` page.

- **Page:** `/work-entries` (Work Entries page)
- **Component:** Client dropdown / select field
- **Symptom:** The label text appears with a strikethrough decoration
- **Expected:** Label text should appear normally without strikethrough

#### Step 1: Paste into Devin

```text
There's a UI bug in timesheet-app: on the /work-entries page, the Client dropdown label has a strikethrough. Investigate the CSS/styling causing this issue and fix it. Take a screenshot before and after the fix.
```

#### Step 2: Research with Ask Devin

- *"What CSS rules are applied to the Client dropdown label? Is it a Material-UI theme issue or a custom style override?"*
- *"Are there other form fields in the app with similar styling issues?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the component hierarchy and styling approach (Material-UI theme, CSS modules, styled-components, etc.).

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the fix scoped correctly? Does it affect other dropdowns unintentionally?
- **Leave a comment** asking Devin to check all other form fields for similar styling issues

---

## Key Takeaways

- Devin can visually inspect UI bugs using its browser and capture before/after evidence
- Specific, verifiable bugs (with page, component, and symptom) produce faster results than vague descriptions
- The PR feedback loop lets reviewers request broader checks (e.g., "check all form fields") and Devin follows up
- Devin Review can catch additional styling regressions introduced by the fix

---

## Going Further

- **Ticket-driven UI fixes** — tag a Jira or GitHub Issue with `Devin:Implementation` and Devin picks it up automatically, reproduces the visual bug, implements a fix, and opens a PR linked back to the ticket (see [Design Patterns → Event-Driven Triggers](../../shared/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers))
- **Scheduled UI consistency audits** — run Devin on a weekly schedule to scan for common CSS issues (inconsistent spacing, broken layouts at different viewports, accessibility contrast violations) and open remediation PRs (see [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md#scheduled-sessions))
- **Devin Review for styling regressions** — enable Devin Review on the repo to automatically check every PR for visual regressions and CSS anti-patterns (see [Platform Capabilities → Devin Review](../../shared/general-themes/platform-capabilities.md#devin-review))
