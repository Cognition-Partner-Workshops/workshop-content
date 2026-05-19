# Release Management

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Implement release management automation — semantic versioning, changelogs, release branches, and GitHub Releases. This exercises Devin's ability to set up release workflows that automate the tedious parts of shipping software.

## Target Outcomes

- Semantic versioning strategy implemented (major.minor.patch)
- Automated changelog generation from commit messages or PR titles
- GitHub Actions workflow for creating releases on tag push
- Release notes template with categories (features, fixes, breaking changes)
- PR with release automation

## What Participants Will Learn

- How Devin sets up release automation workflows
- Semantic versioning best practices
- Changelog generation from git history
- GitHub Releases integration with CI/CD

## Devin Features Exercised

- GitHub Actions workflow authoring
- Git tag and release management
- Changelog generation tooling
- PR creation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js application — set up semantic-release or standard-version with GitHub Actions.

### Step 1: Paste into Devin

> Set up automated release management for timesheet-app: (1) Add conventional commit linting with commitlint and husky, (2) Configure standard-version for automated changelog generation and version bumping, (3) Create a GitHub Actions workflow that creates a GitHub Release with generated release notes when a version tag is pushed, (4) Add a CHANGELOG.md with the initial release. Open a PR.

### Step 2: Research with Ask Devin

- *"What's the difference between semantic-release and standard-version? Which is better for this project?"*
- *"How should we handle pre-release versions (alpha, beta, rc) in the versioning strategy?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the current versioning approach (if any) and commit message conventions. Plan the release strategy to fit the existing workflow.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the conventional commit configuration appropriate? Are the changelog categories useful?
- **Leave a comment** asking Devin to add a release-please workflow as an alternative approach

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot/Gradle application — set up Gradle release plugin with GitHub Actions.

### Step 1: Paste into Devin

> Set up automated release management for uc-spring-boot-upgrade-microservice-extraction: (1) Configure the Gradle release plugin for semantic versioning, (2) Create a GitHub Actions workflow that builds a release artifact (JAR), creates a GitHub Release, and attaches the JAR when a version tag is pushed, (3) Add a CHANGELOG.md template with categorized sections. Open a PR.

### Step 2: Research with Ask Devin

- *"What Gradle plugins are available for release management? Which fits best with this project's build setup?"*
- *"Should the release workflow also build and push a Docker image to GHCR?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the Gradle build configuration and artifact output. Plan the release workflow around the existing build pipeline.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the release workflow handle all artifact types correctly?
- **Leave a comment** asking Devin to add a Docker image build and push to the release workflow
