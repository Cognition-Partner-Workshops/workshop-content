# Framework Upgrade

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
- [petclinic-angular](#petclinic-angular)
- [ts-angular-realworld](#ts-angular-realworld)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Quick Start

Paste this prompt into Devin to try upgrading an Angular application:

```
Upgrade petclinic-angular to the latest Angular version.
Handle any breaking changes from the Angular update guide,
update all dependencies, fix deprecated APIs, and ensure
the app builds successfully.
```

---

## Repositories

- [petclinic-angular](#petclinic-angular) — Angular front-end for PetClinic
- [ts-angular-realworld](#ts-angular-realworld) — Angular RealWorld example app
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction) — Spring Boot backend

---

## Challenge

Use Devin to upgrade a real application from an older framework version to the latest. This exercises Devin's ability to read deprecation notices, handle breaking API changes, update build configurations, and verify the result compiles and passes tests. The goal is a working build on the latest framework version with all deprecations resolved.

## What Participants Will Learn

- How Devin reads and follows framework migration guides (Angular update guide, Spring Boot migration notes)
- How Devin handles cascading dependency updates (updating one library triggers changes in others)
- How Devin resolves breaking API changes by finding modern replacements
- The value of running tests after each change to catch regressions early
- How different frameworks (Angular, Spring Boot) present different upgrade challenges

## Devin Features Exercised

- Code modification across many files
- Dependency management (npm, Gradle)
- Build system troubleshooting
- Test execution and failure resolution
- PR creation with structured change descriptions
- Child sessions for parallel upgrades across repos

## Difficulty

Intermediate

## Estimated Time

60 minutes

## Going Further

- **Divide-and-conquer with child sessions**: When upgrading across a portfolio of services, spawn one child session per service. Each child follows the same upgrade playbook (update dependencies, fix breaking changes, run tests, document changes). A 10-service portfolio can be upgraded in parallel.
- **Playbook-driven upgrades**: Encode the upgrade methodology (check migration guide, update core dependency, fix breaking changes, run build, run tests, document delta) as a playbook. Reuse it for every framework upgrade engagement.
- **Scheduled version checking**: Configure a weekly scheduled session that checks for new framework releases and opens an issue or PR when upgrades are available.
- **Event-driven upgrades**: Connect a webhook to Dependabot or Renovate alerts. When a security advisory is published, Devin automatically upgrades the affected dependency and runs tests.
- **Team-based review**: Multiple engineers can review upgrade PRs simultaneously. PR comments requesting specific changes trigger Devin to iterate.

## Notes

- The Angular repos are at different starting versions — participants should check the current version before upgrading
- Angular upgrades often require multiple incremental version bumps (e.g., 12 → 13 → 14 → 15 → 16 → 17)
- The Spring Boot upgrade (2.x → 3.x) involves a major namespace change (javax → jakarta) that affects nearly every file
- For a shorter session, focus on a single repo; for a longer session, try upgrading multiple repos in parallel using child sessions
- See [Repetitive Framework Upgrades](repetitive-framework-upgrades.md) for the parallel-sessions variation of this module

---

## <a id="petclinic-angular"></a>petclinic-angular

**Repository:** [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular)

Angular front-end for the PetClinic application. Uses an older Angular version with outdated dependencies and deprecated APIs.

### Step 1: Paste into Devin — Angular Upgrade

```
Upgrade petclinic-angular to the latest Angular version.
Handle any breaking changes from the Angular update guide,
update all dependencies, fix deprecated APIs, and ensure
the app builds successfully.
```

### Step 2: Research with Ask Devin

- *"What Angular version is petclinic-angular currently on? What are the biggest breaking changes between that version and the latest?"*
- *"What deprecated APIs does petclinic-angular use? What are the modern replacements?"*
- Use the refined plan as your Devin session prompt — compare the result to your first attempt

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the module structure, component tree, and routing configuration. Identify which parts of the codebase will be most affected by the upgrade.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the upgrade look complete? Are there files Devin missed?
- **Leave a comment** asking Devin to fix something (e.g., *"This still uses the deprecated HttpModule — please update to HttpClientModule"* or *"Can you also add standalone components?"*)
- **Watch Devin respond** to your PR comment and push a fix
- Try leaving both general comments and inline code comments to see how Devin handles each

### Key Takeaways

- **Systematic migration**: Devin follows framework migration guides step-by-step, handling cascading changes across the entire codebase
- **Build verification**: Every change is validated against the build system — failures are caught and fixed immediately
- **Repeatable process**: The same upgrade playbook works for any Angular application, making it reusable across a portfolio

---

## <a id="ts-angular-realworld"></a>ts-angular-realworld

**Repository:** [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld)

Angular RealWorld example app with a different module structure. Provides a second Angular upgrade target to compare approaches.

### Step 1: Paste into Devin — Angular Upgrade (Alternative Repo)

```
Upgrade ts-angular-realworld to the latest Angular version.
Handle any breaking changes, update dependencies, fix
deprecated APIs, and ensure the app builds and tests pass.
```

### Step 2: Research with Ask Devin

- *"What Angular version is ts-angular-realworld currently on? What's the upgrade path?"*
- *"Are there any third-party dependencies in ts-angular-realworld that might not support the latest Angular version?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the architecture and dependency graph. Plan the upgrade order.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — did the upgrade complete successfully? Any remaining deprecation warnings?
- **Compare with petclinic-angular** — how did the upgrade experience differ between the two Angular apps?

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 backend with Java 11. Requires upgrading to Java 17 + Spring Boot 3.2 including the javax → jakarta namespace migration.

### Step 1: Paste into Devin — Spring Boot Upgrade

```
Upgrade uc-spring-boot-upgrade-microservice-extraction from
Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2.
Handle the javax to jakarta namespace migration, update
Gradle build configuration, fix any deprecations, and
ensure all tests pass.
```

### Step 2: Research with Ask Devin

- *"What are the biggest risks when upgrading uc-spring-boot-upgrade-microservice-extraction from Spring Boot 2 to 3? Which files will need the most changes?"*
- *"What's the best order to tackle the javax to jakarta migration, the Gradle plugin updates, and the deprecated API removals?"*
- Use the refined plan as your Devin session prompt — compare the result to your first attempt

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to see the architecture, module dependencies, and code explanations. Identify which parts of the codebase will be most affected by the upgrade. Understand the existing test coverage — this is your safety net during the upgrade.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the upgrade look complete? Are there files Devin missed?
- **Leave a comment** asking Devin to fix something (e.g., *"This still uses javax.servlet — please update to jakarta.servlet"* or *"Can you also add a Docker multi-stage build?"*)
- **Watch Devin respond** to your PR comment and push a fix
- Try leaving both general comments and inline code comments to see how Devin handles each

### Key Takeaways

- **Namespace migration at scale**: Devin handles the javax → jakarta migration across the entire codebase — a tedious, error-prone task that touches nearly every Java file
- **Build-driven verification**: Each change is validated with `./gradlew build` — Devin iterates until the build passes
- **Portfolio-scale upgrading**: The same upgrade playbook can be applied to every Spring Boot 2.x service in a portfolio using child sessions
