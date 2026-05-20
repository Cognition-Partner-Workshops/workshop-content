# Linting & Static Analysis

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
- [timesheet-infra](#timesheet-infra)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [timesheet-infra](#timesheet-infra)

---

<a id="challenge"></a>
## Challenge

Resolve linting issues in a codebase using Devin. This is a great entry-level challenge that shows Devin's ability to read linting configurations, interpret errors, and iteratively fix them. Linting cleanup is a classic capacity-constrained task — valuable but rarely prioritized. Devin's iterative run-fix-verify loop handles it efficiently.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Resolve this GitHub Issue:
https://github.com/Cognition-Partner-Workshops/
timesheet-app/issues/3 — fix all ESLint linting errors
in the codebase, run the linter to verify all issues are
resolved.
```

<a id="target-outcomes"></a>
## Target Outcomes

- All linting errors resolved
- Linter runs cleanly with zero warnings/errors
- PR with fixes and evidence of clean lint run

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin reads and interprets linting configurations (ESLint, Prettier, terraform fmt)
- How Devin navigates GitHub Issues and resolves them
- Observing Devin's iterative approach: run linter → fix → re-run → verify
- How event-driven triggers (CI lint failure → Devin session) can keep codebases clean automatically

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- GitHub Issue resolution
- Iterative feedback loop (lint → fix → lint)
- PR creation with lint evidence

## Difficulty

Beginner

## Estimated Time

30 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

React 19 + Node.js/Express timesheet application with ESLint and Prettier configured.

### Step 1: Paste into Devin

```
Resolve this GitHub Issue:
https://github.com/Cognition-Partner-Workshops/
timesheet-app/issues/3 — fix all ESLint linting errors
in the codebase, run the linter to verify all issues are
resolved.
```

### Step 2: Research with Ask Devin

- *"What linting rules are configured in timesheet-app? Are there any disabled rules that should be re-enabled?"*
- *"Are there any inconsistencies between the ESLint config and the Prettier config?"*
- Use insights to start a second session with a more comprehensive lint cleanup approach

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the codebase structure and identify additional quality improvements beyond linting — dead code, unused imports, or style inconsistencies.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — did Devin fix all lint errors? Did it introduce any behavioral changes?
- **Leave a comment** asking Devin to also add a pre-commit hook that runs the linter automatically
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- Devin's iterative loop (run linter → read output → fix → re-run) mirrors how a developer works, but without context-switching fatigue
- Lint fixes should be purely cosmetic — review that no behavioral changes were introduced
- Pre-commit hooks prevent lint debt from accumulating in the first place

---

## <a id="timesheet-infra"></a>timesheet-infra

**Repository:** [timesheet-infra](https://github.com/Cognition-Partner-Workshops/timesheet-infra)

Terraform infrastructure code for hosting the timesheet application.

### Step 1: Paste into Devin

```
Fix all Terraform formatting issues in timesheet-infra
using `terraform fmt`. Run `terraform validate` to
confirm the configuration is still valid after formatting.
```

### Step 2: Research with Ask Devin

- *"Are there any Terraform best practice violations in timesheet-infra beyond formatting — such as missing descriptions on variables, hardcoded values, or missing tags?"*
- Use insights to start a second session addressing Terraform quality beyond just formatting

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the infrastructure topology and identify any misconfigurations or drift risks.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the formatting changes purely cosmetic? No logic changes?
- **Leave a comment** asking Devin to also add a CI check that runs `terraform fmt -check` on every PR
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- `terraform fmt` is idempotent and safe — Devin can run it confidently
- `terraform validate` confirms no structural issues after formatting changes
- Adding `terraform fmt -check` to CI prevents formatting drift going forward

---

<a id="going-further"></a>
## Going Further

### Event-Driven Lint Enforcement

When CI detects lint failures on a PR, automatically trigger a Devin session to fix them:

```
CI lint check fails on a PR
    → webhook fires
    → Devin session starts: "Fix the lint failures on
       branch feature-xyz in timesheet-app. Run the
       linter to verify all issues are resolved."
    → Devin pushes a fix commit to the same branch
```

This eliminates the "fix lint" back-and-forth that slows down PR review cycles.

### Scheduled Lint Audits

Configure a weekly Devin session to run comprehensive linting across the codebase, including rules that are currently disabled. Devin reports which disabled rules could be re-enabled and opens a PR with the fixes.

### Devin Review for Style Consistency

Enable Devin Review to catch style violations in every PR — complementing the CI lint check with deeper analysis of code patterns, naming conventions, and organizational standards encoded in Knowledge notes.
