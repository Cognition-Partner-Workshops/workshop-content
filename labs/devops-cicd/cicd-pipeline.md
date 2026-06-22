# CI/CD Pipeline

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
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

<a id="challenge"></a>
## Challenge

Create or improve CI/CD pipelines using GitHub Actions. This exercises Devin's ability to author workflow YAML, configure build/test/deploy stages, and integrate quality gates. CI/CD pipeline setup is a foundational task that benefits from Devin's knowledge of GitHub Actions best practices — caching strategies, matrix builds, artifact management, and security hardening.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Create a GitHub Actions CI workflow for timesheet-app.
The workflow should: trigger on push to main and on pull
requests, install dependencies with npm ci, run linting
(npm run lint), run tests (npm test) with coverage
reporting, and upload the coverage report as an artifact.
Add caching for node_modules.
```

<a id="target-outcomes"></a>
## Target Outcomes

- GitHub Actions workflow with build, test, and lint stages
- Branch protection rules documented (main requires passing CI)
- Artifact generation (test reports, coverage reports)
- PR with workflow files and documentation

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin authors GitHub Actions workflows from scratch
- How Devin configures multi-stage pipelines with dependencies
- CI best practices: caching, matrix builds, artifact uploads
- How to iterate on workflow files using PR comments
- How CI pipelines become the foundation for event-driven Devin automation

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- YAML authoring (GitHub Actions syntax)
- Build system understanding (npm, Gradle)
- CI/CD pipeline design
- PR creation with CI configuration

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express application — create a CI pipeline with npm install, lint, test, and build steps.

### Step 1: Paste into Devin

```
Create a GitHub Actions CI workflow for timesheet-app.
The workflow should: trigger on push to main and on pull
requests, install dependencies with npm ci, run linting
(npm run lint), run tests (npm test) with coverage
reporting, and upload the coverage report as an artifact.
Add caching for node_modules.
```

### Step 2: Research with Ask Devin

- *"What CI stages are most valuable for a Node.js/Express application? Should we add security scanning?"*
- *"How should we handle the frontend and backend as separate jobs in the same workflow?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the build system and test framework. Identify what npm scripts are available for the CI pipeline.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the workflow efficient? Does it use caching correctly?
- **Leave a comment** asking Devin to add a deployment stage that deploys to a staging environment on merge to main

### Key Takeaways

- Devin understands GitHub Actions conventions — proper caching keys, artifact upload patterns, and job dependencies
- Separating lint, test, and build into distinct steps provides clear failure diagnostics
- The CI pipeline becomes the foundation for event-driven automation patterns

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot/Gradle application — create a CI pipeline with Gradle build, test, and coverage steps.

### Step 1: Paste into Devin

```
Create a GitHub Actions CI workflow for
uc-spring-boot-upgrade-microservice-extraction. The
workflow should: trigger on push to main and on pull
requests, set up Java 17, run Gradle build with tests,
generate JaCoCo coverage report, upload test results and
coverage as artifacts, and fail if coverage drops below
60%. Add Gradle caching.
```

### Step 2: Research with Ask Devin

- *"What Gradle tasks are available in this project? What's the test and coverage setup?"*
- *"Should we add a Docker image build stage for continuous delivery?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the Gradle build configuration and available tasks. Plan the CI pipeline around the existing build system.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the Gradle caching optimal? Are test results properly reported?
- **Leave a comment** asking Devin to add a matrix build that tests against both Java 17 and Java 21

### Key Takeaways

- Gradle caching significantly reduces CI build times — Devin configures the correct cache key based on the `gradle-wrapper.properties` and `build.gradle` files
- JaCoCo coverage thresholds turn CI into a quality gate — PRs cannot merge if coverage drops
- Matrix builds validate compatibility across Java versions without duplicating workflow files

---

<a id="going-further"></a>
## Going Further

### Webhook-Driven Automation

Once the CI pipeline is in place, it becomes the trigger for event-driven Devin sessions:

```
CI pipeline fails on a PR
    → GitHub webhook fires (check_run.completed, conclusion=failure)
    → Devin session starts: "CI failed on PR #42 in
       timesheet-app. Read the workflow logs, diagnose
       the root cause, and push a fix."
    → Devin pushes a fix commit to the same branch
    → CI re-runs automatically
```

This creates a self-healing CI loop where many failures are resolved without human intervention.

### Scheduled Pipeline Maintenance

Configure a monthly Devin session to audit and optimize CI pipelines:

- Check for outdated GitHub Actions (e.g., `actions/setup-node@v3` → `@v4`)
- Review caching effectiveness (cache hit rates)
- Identify slow stages and optimize them
- Update security scanning actions to latest versions
- Open a PR with pipeline improvements

### Devin Review for CI Changes

Enable Devin Review on workflow file changes to catch common CI pitfalls — missing caching, insecure permissions, hardcoded secrets, or inefficient job dependencies.

### Team-Based CI Operation

Position the CI pipeline as a shared team resource:
- Devin Playbooks encode CI troubleshooting methodology
- Knowledge items capture project-specific CI patterns (required environment variables, test database setup)
- Any team member can trigger Devin to diagnose and fix CI failures
