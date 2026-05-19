# CI/CD Pipeline

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Create or improve CI/CD pipelines using GitHub Actions. This exercises Devin's ability to author workflow YAML, configure build/test/deploy stages, and integrate quality gates.

## Target Outcomes

- GitHub Actions workflow with build, test, and lint stages
- Branch protection rules documented (main requires passing CI)
- Artifact generation (test reports, coverage reports)
- PR with workflow files and documentation

## What Participants Will Learn

- How Devin authors GitHub Actions workflows from scratch
- How Devin configures multi-stage pipelines with dependencies
- CI best practices: caching, matrix builds, artifact uploads
- How to iterate on workflow files using PR comments

## Devin Features Exercised

- YAML authoring (GitHub Actions syntax)
- Build system understanding (npm, Gradle)
- CI/CD pipeline design
- PR creation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express application — create a CI pipeline with npm install, lint, test, and build steps.

### Step 1: Paste into Devin

> Create a GitHub Actions CI workflow for timesheet-app. The workflow should: trigger on push to main and on pull requests, install dependencies with npm ci, run linting (npm run lint), run tests (npm test) with coverage reporting, and upload the coverage report as an artifact. Add caching for node_modules. Open a PR.

### Step 2: Research with Ask Devin

- *"What CI stages are most valuable for a Node.js/Express application? Should we add security scanning?"*
- *"How should we handle the frontend and backend as separate jobs in the same workflow?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the build system and test framework. Identify what npm scripts are available for the CI pipeline.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the workflow efficient? Does it use caching correctly?
- **Leave a comment** asking Devin to add a deployment stage that deploys to a staging environment on merge to main

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot/Gradle application — create a CI pipeline with Gradle build, test, and coverage steps.

### Step 1: Paste into Devin

> Create a GitHub Actions CI workflow for uc-spring-boot-upgrade-microservice-extraction. The workflow should: trigger on push to main and on pull requests, set up Java 17, run Gradle build with tests, generate JaCoCo coverage report, upload test results and coverage as artifacts, and fail if coverage drops below 60%. Add Gradle caching. Open a PR.

### Step 2: Research with Ask Devin

- *"What Gradle tasks are available in this project? What's the test and coverage setup?"*
- *"Should we add a Docker image build stage for continuous delivery?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the Gradle build configuration and available tasks. Plan the CI pipeline around the existing build system.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the Gradle caching optimal? Are test results properly reported?
- **Leave a comment** asking Devin to add a matrix build that tests against both Java 17 and Java 21
