# Repetitive Framework Upgrades

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Challenge](#challenge)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Going Further](#going-further)
- [Notes](#notes)
- [Angular Upgrades](#angular-upgrades)
- [Spring Boot Upgrade](#spring-boot-upgrade)

---

## Quick Start

Paste this prompt into Devin to try a multi-repo parallel upgrade:

```
Upgrade petclinic-angular to the latest Angular version.
Handle any breaking changes from the Angular update guide,
update all dependencies, fix deprecated APIs, and ensure
the app builds successfully.
```

Then start a **second Devin session** (or child session) with:

```
Upgrade ts-angular-realworld to the latest Angular version.
Handle any breaking changes, update dependencies, fix
deprecated APIs, and ensure the app builds and tests pass.
```

Run both upgrades in parallel to see how Devin handles the same type of task across different codebases simultaneously.

---

## Repositories

- [petclinic-angular](#angular-upgrades) — Angular front-end for PetClinic
- [ts-angular-realworld](#angular-upgrades) — Angular RealWorld example app
- [uc-spring-boot-upgrade-microservice-extraction](#spring-boot-upgrade) — Spring Boot backend

---

## Challenge

Perform the same type of upgrade (Angular version bump or Spring Boot 2→3) across multiple repositories simultaneously. This module is designed to showcase Devin's ability to handle **repetitive tasks at scale** — the same upgrade methodology applied across different codebases in parallel child sessions.

The key insight: a framework upgrade is a well-defined, repeatable process. Once the methodology works for one repo, it can be applied to many repos via child sessions — turning a month of sequential upgrade work into a day of parallel execution.

## What Participants Will Learn

- How to run multiple Devin sessions in parallel for the same type of task
- How the same upgrade methodology produces different results on different codebases
- How child sessions enable portfolio-scale upgrades
- The value of playbooks for encoding a repeatable upgrade process

## Devin Features Exercised

- **Child sessions** — spawning parallel sessions for the same task across different repos
- **Playbooks** — encoding the upgrade process as a reusable playbook
- Code modification across many files
- Dependency management (npm, Gradle)
- Build system troubleshooting
- Test execution and failure resolution

## Difficulty

Intermediate

## Estimated Time

60 minutes

## Going Further

- **Playbook-driven portfolio upgrades**: Encode the upgrade methodology as a playbook, then spawn a child session per service in the portfolio. Each child follows the playbook independently. A parent session collects results and reports overall portfolio status.
- **Scheduled version audits**: Configure a weekly scheduled session that checks all repos in the portfolio for outdated framework versions and opens tracking issues.
- **Event-driven upgrades**: Connect webhooks to Dependabot or Renovate. When a security advisory is published, Devin automatically upgrades the affected dependency across all affected repos.
- **Team-based review**: Each parallel upgrade creates its own PR. Multiple engineers can review different PRs simultaneously, with PR comments triggering Devin to iterate.
- **Knowledge notes for upgrade patterns**: Capture common breaking-change patterns (e.g., "javax → jakarta always requires updating the Gradle plugin version too") as knowledge notes so future upgrade sessions start with accumulated wisdom.

## Notes

- This module is a variation of [Framework Upgrade](framework-upgrade.md) focused on the **parallel execution** pattern
- The Angular repos are at different starting versions — the same "upgrade to latest" prompt produces different amounts of work
- The Spring Boot upgrade involves a major namespace change (javax → jakarta) — a different class of upgrade from Angular's incremental version bumps
- For the strongest effect, have participants start all sessions simultaneously and compare progress
- Run the Angular upgrades in parallel with the Spring Boot upgrade to show multi-stack repetitive upgrades

---

## <a id="angular-upgrades"></a>Angular Upgrades

### petclinic-angular

**Repository:** [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular)

Angular front-end for the PetClinic application. Uses an older Angular version with outdated dependencies.

#### Step 1: Paste into Devin

```
Upgrade petclinic-angular to the latest Angular version.
Handle any breaking changes from the Angular update guide,
update all dependencies, fix deprecated APIs, and ensure
the app builds successfully.
```

#### Step 2: Research with Ask Devin

- *"What Angular version is petclinic-angular currently on? What are the breaking changes between that version and the latest?"*

### ts-angular-realworld

**Repository:** [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld)

Angular RealWorld example app. A different Angular codebase to upgrade in parallel.

#### Step 1: Paste into Devin

```
Upgrade ts-angular-realworld to the latest Angular version.
Handle any breaking changes, update dependencies, fix
deprecated APIs, and ensure the app builds and tests pass.
```

#### Step 2: Research with Ask Devin

- *"What Angular version is ts-angular-realworld currently on? What's the fastest upgrade path?"*
- Run this upgrade **in parallel** with petclinic-angular to compare how Devin handles the same task on different codebases

### Step 3 (Optional): Read the DeepWiki

Open each repo's DeepWiki page to understand the module structure and dependency graph. Compare the architectures to predict which upgrade will be more complex.

### Step 4 (Optional): Review & Give Feedback

- **Compare the two PRs** — how did Devin's approach differ between the repos? Which had more breaking changes?
- **Leave comments** on both PRs to see how Devin handles feedback across parallel sessions

---

## <a id="spring-boot-upgrade"></a>Spring Boot Upgrade

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 + Java 11 backend. Requires upgrading to Java 17 + Spring Boot 3.2, including the javax → jakarta namespace migration.

### Step 1: Paste into Devin

```
Upgrade uc-spring-boot-upgrade-microservice-extraction from
Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2.
Handle the javax to jakarta namespace migration, update
Gradle build configuration, fix any deprecations, and
ensure all tests pass.
```

### Step 2: Research with Ask Devin

- *"What's the most efficient order to tackle the breaking changes — namespace first, then build config, then deprecated APIs?"*
- Run this upgrade **in parallel** with the Angular upgrades to show multi-stack repetitive upgrades

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the build configuration and dependency structure. Plan the upgrade order to minimize cascading failures.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the javax → jakarta migration complete? Any files missed?
- **Leave a comment** asking Devin to also document the upgrade as a repeatable runbook for other Spring Boot 2.x services

### Key Takeaways

- **Repeatable methodology**: The same upgrade playbook works across different repos and frameworks — encode it once, apply it many times
- **Parallel execution**: Child sessions enable portfolio-scale upgrades. A 10-service upgrade that would take weeks sequentially can run in parallel
- **Consistent quality**: Playbook-driven upgrades ensure every service gets the same thoroughness — no services are "forgotten" or partially upgraded
- **Cross-framework upgrades**: Angular and Spring Boot upgrades can run simultaneously, showing that the parallel pattern works across technology stacks
