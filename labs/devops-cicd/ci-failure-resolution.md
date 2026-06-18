# CI Failure Resolution

<a id="table-of-contents"></a>
## Table of Contents
- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Notes](#notes)
- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

<a id="challenge"></a>
## Challenge

Diagnose and fix failing CI pipelines. This exercises Devin's ability to read CI logs, identify root causes, and implement fixes — a common daily developer task. CI failure triage is one of the highest-value use cases for event-driven Devin automation: when CI fails, a webhook can automatically trigger Devin to diagnose and fix, reducing the time from red build to green.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
The CI pipeline for timesheet-app is failing. Look at the
GitHub Actions workflow logs, identify the root cause, and
fix it. If the failure is a dependency issue, update the
lockfile. If it's a test failure, investigate and fix the
test or the code. If it's a configuration issue, fix the
workflow YAML. Add a comment to the PR explaining the root
cause.
```

<a id="target-outcomes"></a>
## Target Outcomes

- CI failure root cause identified from logs
- Fix implemented and CI passing
- Prevention measures added (better error messages, guards, documentation)
- PR with fix and root cause explanation

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin reads and interprets CI failure logs
- How Devin traces failures back to root causes (dependency issues, environment mismatches, flaky tests)
- Common CI failure patterns and their solutions
- How to use Devin for CI triage in daily development
- How webhook-driven automation turns CI failure resolution into an automatic process

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- CI log analysis and interpretation
- Root cause diagnosis across multiple failure categories
- Fix implementation with prevention measures
- PR creation with root cause documentation

## Difficulty

Intermediate

## Estimated Time

45 minutes

## Notes

- This challenge works best when there's an actual failing CI pipeline to investigate
- If no real failures exist, facilitators can intentionally break something (dependency version, test, config)
- The goal is to show how Devin handles the "CI is red, help!" workflow — the most common trigger for event-driven Devin sessions

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js application — diagnose npm-related CI failures (dependency conflicts, missing scripts, test failures).

### Step 1: Paste into Devin

```
The CI pipeline for timesheet-app is failing. Look at the
GitHub Actions workflow logs, identify the root cause, and
fix it. If the failure is a dependency issue, update the
lockfile. If it's a test failure, investigate and fix the
test or the code. If it's a configuration issue, fix the
workflow YAML. Add a comment to the PR explaining the root
cause.
```

### Step 2: Research with Ask Devin

- *"What are the most common reasons GitHub Actions workflows fail for Node.js projects?"*
- *"How can we make the CI pipeline more resilient to flaky tests or transient failures?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the build system and test configuration. This helps narrow down where CI failures are likely to originate.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the fix address the root cause or just suppress the error?
- **Leave a comment** asking Devin to add a retry strategy for flaky tests or network-dependent steps

### Key Takeaways

- Devin reads CI logs the same way a developer would — scanning for error messages, exit codes, and stack traces
- Root cause fixes are more valuable than symptom suppression — review that the fix prevents recurrence
- Adding retry strategies for transient failures (network timeouts, npm registry issues) improves CI reliability

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot/Gradle application — diagnose Gradle build failures, test failures, or Java version mismatches.

### Step 1: Paste into Devin

```
The CI pipeline for
uc-spring-boot-upgrade-microservice-extraction is failing.
Examine the GitHub Actions logs, identify the root cause
(build failure, test failure, dependency issue, Java
version mismatch), and fix it. If multiple issues exist,
fix them in priority order. Document the root cause
analysis in the PR description.
```

### Step 2: Research with Ask Devin

- *"What are common Gradle CI failures — dependency resolution, Java version mismatches, or test environment issues?"*
- *"How can we add better error reporting to the CI pipeline to make future failures easier to diagnose?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the Gradle build configuration and test setup. This provides context for diagnosing build and test failures.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the fix sustainable or a temporary workaround?
- **Leave a comment** asking Devin to add CI caching improvements to speed up future builds

### Key Takeaways

- Java version mismatches are a frequent CI failure — the workflow must match the project's `sourceCompatibility`
- Gradle dependency resolution failures often require cache invalidation or lockfile updates
- Root cause documentation in the PR helps the team learn from failures

---

<a id="going-further"></a>
## Going Further

### Webhook-Driven CI Failure Resolution

This is the flagship pattern for event-driven Devin automation:

```
CI check fails (check_run.completed, conclusion=failure)
    → GitHub webhook fires
    → Devin session starts automatically with prompt:
       "CI failed on PR #N in repo-name. Read the GitHub
        Actions logs, diagnose the root cause, and push
        a fix to the same branch."
    → Devin reads logs, identifies the failure, pushes fix
    → CI re-runs automatically
    → If CI passes, Devin adds a comment explaining
       the root cause and fix
```

This pattern reduces mean-time-to-recovery for CI failures from hours (waiting for a developer to look at it) to minutes (Devin responds immediately).

### Playbook for CI Triage

Create a Playbook that encodes your team's CI triage methodology:

```
Playbook: CI Failure Triage

1. Read the GitHub Actions workflow logs for the failed run
2. Categorize the failure: build error, test failure,
   dependency issue, environment mismatch, or flaky test
3. For build errors: check dependency versions, lockfiles,
   and compiler configuration
4. For test failures: run the failing test locally,
   check for environment dependencies
5. For flaky tests: run 3x to confirm non-determinism,
   add retry or fix the root cause
6. Push a fix and document the root cause
```

### Scheduled CI Health Monitoring

Configure a daily Devin session to check CI health across repositories:

- Identify repos with persistently failing CI
- Track flaky test trends (tests that fail intermittently)
- Report CI run duration trends to catch pipeline performance regressions
- Open PRs to fix any issues found

### Team-Based CI Operation

Position Devin as the team's first responder for CI failures:
- Any team member can trigger the CI Triage Playbook when they see a red build
- Knowledge items capture repo-specific CI patterns (required secrets, environment variables, test database setup)
- Devin's CI triage is consistent regardless of who triggers it — no knowledge silos
