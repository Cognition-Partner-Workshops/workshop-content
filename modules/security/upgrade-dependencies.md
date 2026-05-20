# Upgrade Dependencies

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [timesheet-app](#timesheet-app)
- [uc-cve-remediation-regulatory-compliance](#uc-cve-remediation-regulatory-compliance)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-cve-remediation-regulatory-compliance](#uc-cve-remediation-regulatory-compliance)

---

## Challenge

Upgrade outdated and vulnerable dependencies in a project. This is a foundational security task — audit the dependency tree, identify known vulnerabilities, upgrade to secure versions, and verify the build still passes.

## Quick Start

Paste this prompt into Devin to get started immediately:

```
Resolve this GitHub Issue:
https://github.com/Cognition-Partner-Workshops/timesheet-app/issues/2
Audit the npm dependencies for known vulnerabilities,
upgrade all vulnerable packages to their latest secure
versions, and ensure the build and tests still pass.
```

## Target Outcomes

- Dependency audit report generated (npm audit, Gradle dependency check, etc.)
- Critical/high vulnerabilities resolved through upgrades
- Build and tests pass after upgrades
- PR with upgrade changes and audit evidence

## What Participants Will Learn

- How Devin interprets dependency vulnerability reports (npm audit, Gradle dependency check)
- How Devin handles breaking changes during major version upgrades
- Devin's approach to verifying compatibility after upgrades (build, test, run)

## Devin Features Exercised

- GitHub Issue resolution
- Dependency analysis
- Build verification
- PR creation

## Difficulty

Beginner to Intermediate

## Estimated Time

45 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js application with npm dependencies — use `npm audit` to identify and fix vulnerable packages.

### Step 1: Paste into Devin

```
Resolve this GitHub Issue:
https://github.com/Cognition-Partner-Workshops/timesheet-app/issues/2
Audit the npm dependencies for known vulnerabilities,
upgrade all vulnerable packages to their latest secure
versions, and ensure the build and tests still pass.
```

### Step 2: Research with Ask Devin

- *"Which dependencies in timesheet-app have the most severe known vulnerabilities?"*
- *"Are there any dependencies that are no longer maintained and should be replaced entirely?"*
- Use insights to start a second session that also replaces deprecated packages

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand which dependencies are critical to the application's core functionality — these need the most careful upgrade verification.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — did Devin handle breaking changes from major version upgrades?
- **Leave a comment** asking Devin to also add `npm audit` to the CI pipeline so vulnerabilities are caught on every PR
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Issue-driven workflow**: Devin reads a GitHub Issue, interprets the acceptance criteria, and delivers a PR — mirroring how teams already assign work
- **Breaking change resolution**: Major version upgrades often require API changes — Devin identifies and fixes these as part of the upgrade
- **Build verification**: Running the test suite after upgrades confirms compatibility — Devin iterates until the build passes
- **Dependency lifecycle**: Some packages are unmaintained and need replacement, not just version bumps — Devin can identify and suggest alternatives

---

## <a id="uc-cve-remediation-regulatory-compliance"></a>uc-cve-remediation-regulatory-compliance

**Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)

Spring Boot 2.6.3 application with known vulnerable dependencies and OWASP Dependency-Check pre-configured.

### Step 1: Paste into Devin

```
Upgrade uc-cve-remediation-regulatory-compliance from
Spring Boot 2.6.3 to the latest stable 2.7.x or 3.x,
updating all transitive dependencies. Run
./gradlew dependencyCheckAnalyze before and after to
document which CVEs are resolved. Verify the build
still passes.
```

### Step 2: Research with Ask Devin

- *"What's the safest upgrade path for Spring Boot 2.6.3 — should we go to 2.7.x first or jump straight to 3.x?"*
- *"Which transitive dependencies will be automatically resolved by upgrading Spring Boot, and which need manual attention?"*
- Use the analysis to plan a more comprehensive upgrade strategy

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the application's dependency tree. Identify which direct dependencies pull in the most vulnerable transitive dependencies.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — did Devin handle the javax → jakarta namespace change (if upgrading to 3.x)?
- **Leave a comment** asking Devin to also upgrade a specific transitive dependency that wasn't automatically resolved
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Upgrade path strategy**: Choosing between incremental (2.6 → 2.7 → 3.x) and direct (2.6 → 3.x) upgrade paths involves trade-offs between risk and effort — Devin can analyze both
- **Transitive dependency resolution**: Upgrading a framework like Spring Boot resolves many downstream CVEs automatically through managed dependency versions
- **Namespace migration**: The javax → jakarta change in Spring Boot 3 is a non-trivial migration that touches imports across the entire codebase — a task well-suited to Devin's systematic approach
- **Before/after evidence**: Running `./gradlew dependencyCheckAnalyze` before and after provides auditable proof of which CVEs were resolved

---

## Going Further

- **Webhook-driven automation**: Connect Dependabot or Renovate alerts to Devin via webhooks so that new dependency vulnerability notifications automatically trigger an upgrade session. Devin upgrades the dependency, runs the test suite, and opens a PR — closing the loop without human triage.
- **Divide and conquer with child sessions**: When upgrading a shared dependency (e.g., Spring Boot) across multiple microservices, use a parent Devin session to define the upgrade playbook, then spawn child sessions — one per service — to apply the upgrade in parallel. Each child handles its own breaking changes and opens its own PR.
- **Scheduled recurring analysis**: Configure a weekly scheduled Devin session to run `npm audit` or `./gradlew dependencyCheckAnalyze`, upgrade any newly vulnerable dependencies, and open a PR. Over time, this keeps your dependency posture continuously current — preventing the accumulation of a security backlog that requires a dedicated remediation campaign.
