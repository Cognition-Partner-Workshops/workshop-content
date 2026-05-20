# Release Management

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

Implement release management automation — semantic versioning, changelogs, release branches, and GitHub Releases. This exercises Devin's ability to set up release workflows that automate the tedious parts of shipping software. Release management is often manual and error-prone — Devin sets up the automation infrastructure so releases become predictable and consistent.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Set up automated release management for timesheet-app:
(1) Add conventional commit linting with commitlint and
husky, (2) Configure standard-version for automated
changelog generation and version bumping, (3) Create a
GitHub Actions workflow that creates a GitHub Release with
generated release notes when a version tag is pushed,
(4) Add a CHANGELOG.md with the initial release.
```

<a id="target-outcomes"></a>
## Target Outcomes

- Semantic versioning strategy implemented (major.minor.patch)
- Automated changelog generation from commit messages or PR titles
- GitHub Actions workflow for creating releases on tag push
- Release notes template with categories (features, fixes, breaking changes)
- PR with release automation

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin sets up release automation workflows
- Semantic versioning best practices
- Changelog generation from git history
- GitHub Releases integration with CI/CD
- How scheduled sessions can automate recurring release tasks (dependency updates, changelog drafts)

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- GitHub Actions workflow authoring
- Git tag and release management
- Changelog generation tooling
- PR creation with release infrastructure

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js application — set up semantic-release or standard-version with GitHub Actions.

### Step 1: Paste into Devin

```
Set up automated release management for timesheet-app:
(1) Add conventional commit linting with commitlint and
husky, (2) Configure standard-version for automated
changelog generation and version bumping, (3) Create a
GitHub Actions workflow that creates a GitHub Release with
generated release notes when a version tag is pushed,
(4) Add a CHANGELOG.md with the initial release.
```

### Step 2: Research with Ask Devin

- *"What's the difference between semantic-release and standard-version? Which is better for this project?"*
- *"How should we handle pre-release versions (alpha, beta, rc) in the versioning strategy?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the current versioning approach (if any) and commit message conventions. Plan the release strategy to fit the existing workflow.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the conventional commit configuration appropriate? Are the changelog categories useful?
- **Leave a comment** asking Devin to add a release-please workflow as an alternative approach

### Key Takeaways

- Conventional commits enforce a structured commit format that enables automated changelog generation
- Semantic versioning communicates the impact of each release — breaking changes increment the major version
- GitHub Releases provide a centralized view of what shipped in each version

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot/Gradle application — set up Gradle release plugin with GitHub Actions.

### Step 1: Paste into Devin

```
Set up automated release management for
uc-spring-boot-upgrade-microservice-extraction:
(1) Configure the Gradle release plugin for semantic
versioning, (2) Create a GitHub Actions workflow that
builds a release artifact (JAR), creates a GitHub Release,
and attaches the JAR when a version tag is pushed,
(3) Add a CHANGELOG.md template with categorized sections.
```

### Step 2: Research with Ask Devin

- *"What Gradle plugins are available for release management? Which fits best with this project's build setup?"*
- *"Should the release workflow also build and push a Docker image to GHCR?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the Gradle build configuration and artifact output. Plan the release workflow around the existing build pipeline.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the release workflow handle all artifact types correctly?
- **Leave a comment** asking Devin to add a Docker image build and push to the release workflow

### Key Takeaways

- Gradle's release plugin manages version bumping and tag creation — the CI workflow handles artifact publishing
- Attaching build artifacts (JARs, Docker images) to GitHub Releases creates a single source of truth for each version
- Release workflows should be idempotent — re-running the same tag should not create duplicate releases

---

<a id="going-further"></a>
## Going Further

### Scheduled Release Preparation

Configure a weekly Devin session to prepare for the next release:

- Generate a draft changelog from merged PRs since the last release
- Identify any unreleased breaking changes that require a major version bump
- Check for dependency updates that should be included
- Open a "release preparation" PR for team review

### Event-Driven Release Automation

Trigger releases automatically based on merge patterns:

```
PR with label "release" merged to main
    → GitHub webhook fires
    → Devin session starts: "Create a new release for
       timesheet-app. Generate the changelog from merged
       PRs, bump the version, create a git tag, and
       trigger the release workflow."
    → Devin creates the tag and GitHub Release
```

### Devin Review for Release Readiness

Enable Devin Review to flag PRs that introduce breaking changes without updating the version or changelog — catching release process issues before they accumulate.
