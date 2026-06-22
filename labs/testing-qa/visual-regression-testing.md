# Visual Regression Testing

<a id="table-of-contents"></a>
## Table of Contents
- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [timesheet-app](#timesheet-app)
- [calcom](#calcom)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [calcom](#calcom)

---

<a id="challenge"></a>
## Challenge

Set up Playwright visual comparison tests for UI components. Capture baseline screenshots and configure visual diff detection for key pages and components. Visual regression testing catches unintended UI changes that functional tests miss — Devin handles the tedious setup of baseline screenshots, viewport configurations, and diff thresholds.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Set up Playwright visual regression tests for the
timesheet-app frontend. Create visual comparison tests
that capture baseline screenshots for: the login page,
dashboard with stats cards, clients table, work entries
form, and reports page with export buttons. Configure
tests for both desktop (1280x720) and mobile (375x667)
viewports. Set a pixel diff threshold of 0.2%.
```

<a id="target-outcomes"></a>
## Target Outcomes

- Playwright visual test suite with baseline screenshots
- Visual comparison tests for key pages and components
- CI configuration for visual regression checks
- PR with visual test setup and baseline images

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin identifies key UI surfaces for visual regression coverage
- How Devin configures Playwright's visual comparison API with appropriate thresholds
- Devin's ability to structure visual tests for maintainability across viewport sizes
- How to manage baseline screenshot updates as the UI evolves
- How Devin Review flags UI changes in PRs that lack corresponding visual test updates

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Browser automation and screenshot capture
- UI surface identification and prioritization
- Test framework setup (Playwright visual comparisons)
- PR creation with baseline images

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

React + Material-UI frontend with multiple pages (Dashboard, Clients, Work Entries, Reports) — suitable for visual regression testing.

### Step 1: Paste into Devin

```
Set up Playwright visual regression tests for the
timesheet-app frontend. Create visual comparison tests
that capture baseline screenshots for: the login page,
dashboard with stats cards, clients table, work entries
form, and reports page with export buttons. Configure
tests for both desktop (1280x720) and mobile (375x667)
viewports. Set a pixel diff threshold of 0.2%.
```

### Step 2: Research with Ask Devin

- *"What are the key pages and UI states in timesheet-app that should have visual regression coverage? Are there any dynamic elements that need to be masked or stabilized?"*
- *"How should we handle authentication in the visual tests — should we use a test fixture that pre-authenticates, or capture the login flow?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the frontend page structure and Material-UI component usage. Identify pages with the most visual complexity (e.g., the Reports page with charts and export buttons).

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the screenshots stable across runs? Check that dynamic content (dates, timestamps) is properly masked or frozen
- **Leave a comment** asking Devin to add visual tests for the dialog modals (add client, add work entry)
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- Visual regression tests catch CSS regressions, layout shifts, and styling issues that functional tests miss
- Dynamic content (timestamps, user names) must be masked or frozen for stable baselines
- Multiple viewports (desktop + mobile) catch responsive design regressions

---

## <a id="calcom"></a>calcom

**Repository:** [calcom](https://github.com/Cognition-Partner-Workshops/calcom)

Full-featured scheduling platform with a complex UI — extensive visual surfaces including booking flows, event type configuration, and availability management.

### Step 1: Paste into Devin

```
Set up Playwright visual regression tests for the calcom
web application. Create visual comparison tests covering:
the public booking page (date picker and time slot
selection), the event types listing page, the
availability/schedule editor, and the settings page.
Configure viewport testing for desktop and mobile. Use
Playwright's toHaveScreenshot with a maxDiffPixelRatio
threshold.
```

### Step 2: Research with Ask Devin

- *"What are the most visually complex pages in calcom that would benefit from visual regression testing? Which components change most frequently?"*
- *"Does calcom already have Playwright configured? How should we integrate visual tests with the existing E2E test setup?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the Booker component and booking flow UI. The public booking page with date picker, time slots, and form fields is the highest-value surface for visual regression coverage.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the baseline screenshots clean and representative? Check that timezone-dependent elements are stabilized
- **Leave a comment** asking Devin to add visual tests for the embed widget variants (inline, floating popup)
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- Public-facing pages (booking flow) are the highest-priority surfaces for visual regression — they affect end users directly
- Timezone-dependent UI elements need special handling to produce stable baselines
- Existing Playwright configuration should be extended, not replaced

---

<a id="going-further"></a>
## Going Further

### Event-Driven Visual Regression in CI

Run visual regression tests automatically on every PR that touches frontend code:

```
PR modifies CSS, component files, or layout templates
    → CI runs Playwright visual comparison tests
    → If visual diffs exceed threshold, Devin session
       starts: "Visual regression detected on the booking
       page. Review the diff and fix or update baselines."
    → Devin opens a follow-up commit with the fix
```

### Scheduled Baseline Refresh

Configure a monthly Devin session to review and update visual baselines after intentional UI changes have accumulated. Devin identifies which baselines are stale, captures new screenshots, and opens a PR for team review.

### Devin Review for Visual Impact

Enable Devin Review to flag PRs that modify CSS or layout files but don't include updated visual baselines — catching potential regressions before they merge.
