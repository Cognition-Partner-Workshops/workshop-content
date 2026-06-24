# Changelog & Release Notes

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Notes](#notes)
- [Repositories](#repositories)
  - [timesheet-app](#timesheet-app)
  - [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

Pick a repo below, copy the **Step 1** prompt into a new Devin session, and let Devin analyze the git history and generate structured release documentation. No prerequisites beyond repo access. This is a good introductory module — it produces visible, useful output quickly.

---

## Challenge

Generate structured changelogs from git history and pull requests. Produce user-facing release notes that categorize changes by type (features, fixes, breaking changes) and include migration guides where needed.

## Target Outcomes

- Structured CHANGELOG.md following Keep a Changelog format
- Release notes document with categorized changes (features, fixes, breaking changes)
- Migration guide for any breaking changes identified
- Automated generation configuration (script or CI integration)
- PR with changelog, release notes, and generation tooling

## What Participants Will Learn

- How Devin analyzes git history to extract and categorize changes
- How Devin distinguishes between user-facing changes and internal refactors
- How Devin identifies breaking changes and generates migration guidance
- The value of conventional commit messages for automated changelog generation

## Devin Features Exercised

- Git history analysis and commit classification
- Change categorization and impact assessment
- Technical writing for user-facing documentation
- PR creation with documentation and tooling

## Difficulty

Beginner to Intermediate

## Estimated Time

30 minutes

## Notes

- This is a good introductory module for participants new to Devin — it produces visible, useful output quickly
- If the repo has few commits, Devin can still generate a changelog template and automated generation script
- Encourage participants to evaluate whether Devin correctly identified breaking changes versus non-breaking changes

---

## Repositories

### <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

React + Node.js/Express timesheet application with an active git history suitable for changelog generation.

#### Step 1: Paste into Devin

```
Analyze the git history of timesheet-app and generate release documentation. Create: (1) `CHANGELOG.md` in the repo root following the Keep a Changelog format — categorize all changes into Added, Changed, Deprecated, Removed, Fixed, and Security sections, (2) `docs/release-notes/v1.0.md` — user-facing release notes summarizing the current state of the application with highlights and known issues, (3) `scripts/generate-changelog.sh` — a script that can regenerate the changelog from git history using conventional commit parsing, and (4) if any breaking changes are found, add a `MIGRATION.md` with upgrade instructions.
```

#### Step 2: Research with Ask Devin

- *"What does the git history of timesheet-app look like? Are there conventional commit messages, tags, or release branches?"*
- *"What are the most significant changes in recent commits? Are there any breaking API or schema changes?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the application features and API surface. This context helps evaluate whether the generated changelog accurately describes changes from a user's perspective.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the changelog entries meaningful from a user's perspective, or are they just restated commit messages?
- **Leave a comment** asking Devin to improve a specific changelog entry with more context about the user impact
- **Watch Devin respond** and push a follow-up commit

---

### <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot monolith with frontend — a complex application where changelog generation must account for both backend and frontend changes.

#### Step 1: Paste into Devin

```
Analyze the git history of uc-spring-boot-upgrade-microservice-extraction and generate release documentation. Create: (1) `CHANGELOG.md` following Keep a Changelog format — separately categorize backend (API, database, service layer) and frontend (UI, components, routing) changes, (2) `docs/release-notes/v1.0.md` — user-facing release notes organized by feature area, (3) `scripts/generate-changelog.sh` — an automated changelog generation script, and (4) `MIGRATION.md` documenting any breaking changes found in the history with step-by-step upgrade instructions.
```

#### Step 2: Research with Ask Devin

- *"What is the commit history structure of uc-spring-boot-upgrade-microservice-extraction? Are there distinct phases (initial build, feature additions, refactors) visible in the history?"*
- *"Are there any database schema changes or API contract changes in the history that would constitute breaking changes?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the application architecture. Changes to the API layer, database schema, or GraphQL endpoints should be flagged as potentially breaking in the changelog.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the changelog correctly separate backend and frontend changes? Are breaking changes properly identified?
- **Leave a comment** asking Devin to add version tags to the changelog entries based on logical release boundaries in the git history
- **Watch Devin respond** and push a follow-up commit

---

## Key Takeaways

- Devin reads git history semantically — it categorizes changes by user impact rather than just restating commit messages
- Changelog generation is fast, visible output that makes a good first Devin experience for new participants
- The generated `generate-changelog.sh` script provides ongoing value: teams can re-run it as part of their release process

## Going Further

Changelog generation is a natural fit for **documentation-on-code-change triggers** (see [When to Use Devin → Capacity-Constrained](../../reference/general-themes/when-to-use-devin.md)):

- **Release-triggered changelog updates** — When a release tag is pushed or a release branch is created, trigger a Devin session that generates the changelog for the new version and opens a PR with the release notes
- **PR-triggered change classification** — On every merged PR, trigger a lightweight Devin session that classifies the change (feature, fix, breaking) and appends it to a draft changelog — so the release notes are always up-to-date
- **Scheduled release readiness checks** — A recurring session reviews the accumulated changelog entries since the last release and drafts user-facing release notes, flagging any entries that need human review (e.g., breaking changes without migration instructions)
