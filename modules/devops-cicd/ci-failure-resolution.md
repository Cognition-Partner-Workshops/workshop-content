# CI Failure Resolution

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Diagnose and fix failing CI pipelines. This exercises Devin's ability to read CI logs, identify root causes, and implement fixes — a common daily developer task.

## Target Outcomes

- CI failure root cause identified from logs
- Fix implemented and CI passing
- Prevention measures added (better error messages, guards, documentation)
- PR with fix

## What Participants Will Learn

- How Devin reads and interprets CI failure logs
- How Devin traces failures back to root causes (dependency issues, environment mismatches, flaky tests)
- Common CI failure patterns and their solutions
- How to use Devin for CI triage in daily development

## Devin Features Exercised

- CI log analysis
- Root cause diagnosis
- Fix implementation
- PR creation

## Difficulty

Intermediate

## Estimated Time

45 minutes

## Notes

- This challenge works best when there's an actual failing CI pipeline to investigate
- If no real failures exist, facilitators can intentionally break something (dependency version, test, config)
- The goal is to show how Devin handles the "CI is red, help!" workflow

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js application — diagnose npm-related CI failures (dependency conflicts, missing scripts, test failures).

### Step 1: Paste into Devin

> The CI pipeline for timesheet-app is failing. Look at the GitHub Actions workflow logs, identify the root cause, and fix it. If the failure is a dependency issue, update the lockfile. If it's a test failure, investigate and fix the test or the code. If it's a configuration issue, fix the workflow YAML. Open a PR with the fix and add a comment explaining the root cause.

### Step 2: Research with Ask Devin

- *"What are the most common reasons GitHub Actions workflows fail for Node.js projects?"*
- *"How can we make the CI pipeline more resilient to flaky tests or transient failures?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the build system and test configuration. This helps narrow down where CI failures are likely to originate.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the fix address the root cause or just suppress the error?
- **Leave a comment** asking Devin to add a retry strategy for flaky tests or network-dependent steps

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot/Gradle application — diagnose Gradle build failures, test failures, or Java version mismatches.

### Step 1: Paste into Devin

> The CI pipeline for uc-spring-boot-upgrade-microservice-extraction is failing. Examine the GitHub Actions logs, identify the root cause (build failure, test failure, dependency issue, Java version mismatch), and fix it. If multiple issues exist, fix them in priority order. Open a PR with the fixes and document the root cause analysis.

### Step 2: Research with Ask Devin

- *"What are common Gradle CI failures — dependency resolution, Java version mismatches, or test environment issues?"*
- *"How can we add better error reporting to the CI pipeline to make future failures easier to diagnose?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the Gradle build configuration and test setup. This provides context for diagnosing build and test failures.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the fix sustainable or a temporary workaround?
- **Leave a comment** asking Devin to add CI caching improvements to speed up future builds
