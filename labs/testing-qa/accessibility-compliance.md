# Accessibility Compliance

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

Audit and improve the accessibility (a11y) compliance of a web application. This challenge covers WCAG 2.1 conformance, screen reader compatibility, keyboard navigation, color contrast, and ARIA attributes. Accessibility audits are often deferred until late in development — Devin can run automated audits, fix common violations, and open PRs so engineers focus on reviewing the nuanced cases that require human judgment.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Run timesheet-app locally (frontend on port 5173). Run
a Lighthouse accessibility audit on the main pages
(/work-entries, /clients, /reports). Fix all critical
and serious accessibility violations: missing form labels,
insufficient color contrast, missing alt text, and
keyboard navigation issues.
```

<a id="target-outcomes"></a>
## Target Outcomes

- Accessibility audit report generated (axe-core, Lighthouse, or manual)
- Critical accessibility violations fixed (missing alt text, color contrast, keyboard traps)
- ARIA attributes added where needed
- PR with fixes and audit evidence

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin identifies accessibility violations using automated tools
- How Devin applies WCAG 2.1 guidelines to fix common issues
- The limitations of automated a11y testing vs. manual testing
- How to use Devin's browser to verify screen reader behavior
- How scheduled accessibility audits keep compliance current as the UI evolves

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Browser interaction for visual inspection
- Automated tool execution (axe-core, Lighthouse)
- Frontend code modification (ARIA, semantic HTML)
- PR creation with audit evidence

## Difficulty

Intermediate

## Estimated Time

45 minutes

## Prerequisites

The application should be running locally for browser-based auditing.

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

React application with Material-UI components — good candidate for ARIA attribute improvements and keyboard navigation fixes.

### Step 1: Paste into Devin

```
Run timesheet-app locally (frontend on port 5173). Run
a Lighthouse accessibility audit on the main pages
(/work-entries, /clients, /reports). Fix all critical
and serious accessibility violations: missing form labels,
insufficient color contrast, missing alt text, and
keyboard navigation issues.
```

### Step 2: Research with Ask Devin

- *"What WCAG 2.1 Level AA violations exist in timesheet-app's React components?"*
- *"Are the Material-UI components configured with proper ARIA attributes for screen readers?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the component hierarchy. Identify forms and interactive elements that need accessibility improvements.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the ARIA attributes semantically correct? Are labels associated with the right inputs?
- **Leave a comment** asking Devin to also test keyboard-only navigation through the main workflow

### Key Takeaways

- Automated tools (Lighthouse, axe-core) catch ~30-50% of accessibility issues — the rest require human review
- Material-UI components have built-in accessibility support, but custom usage often breaks it
- Before/after audit scores provide measurable evidence of improvement

---

## <a id="calcom"></a>calcom

**Repository:** [calcom](https://github.com/Cognition-Partner-Workshops/calcom)

Complex scheduling application where accessibility is critical for public-facing booking pages.

### Step 1: Paste into Devin

```
Start calcom locally with `yarn dev`. Run a Lighthouse
accessibility audit on the public booking page. Fix all
critical accessibility issues: ensure the date/time
picker is keyboard-navigable, all form fields have proper
labels, and color contrast meets WCAG 2.1 AA.
```

### Step 2: Research with Ask Devin

- *"Is the booking calendar widget keyboard-navigable? Can a screen reader user complete a booking?"*
- *"What ARIA live regions should exist to announce availability changes?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the booking flow components. Focus on the public-facing pages that external users interact with.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the changes improve real usability for assistive technology users?
- **Leave a comment** asking Devin to add skip navigation links and focus management for the booking flow

### Key Takeaways

- Public-facing pages have the highest accessibility impact — they affect the broadest user base
- Calendar/date-picker widgets are among the hardest components to make accessible
- ARIA live regions are essential for dynamic content that updates without page reload

---

<a id="going-further"></a>
## Going Further

### Scheduled Accessibility Audits

Configure a weekly Devin session to run Lighthouse accessibility audits against your staging environment:

- Run audits on key pages and compare scores against the previous week
- If any page drops below the team's accessibility threshold, fix the violations and open a PR
- Generate a trend report showing accessibility score progression

This turns accessibility from a periodic compliance exercise into continuous maintenance.

### Event-Driven Accessibility Checks

Trigger accessibility audits on every PR that modifies frontend components:

```
PR modifies React components or CSS
    → CI runs Lighthouse accessibility check
    → If score drops below threshold, Devin session
       starts: "Accessibility score dropped on /booking.
       Fix the violations introduced in this PR."
    → Devin pushes a fix commit
```

### Devin Review for Accessibility

Enable Devin Review to flag PRs that introduce accessibility regressions — missing alt text, removed ARIA attributes, or new form fields without labels.
