# Framework Upgrade

## Repositories

- [petclinic-angular](#petclinic-angular)
- [ts-angular-realworld](#ts-angular-realworld)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Upgrade a project's framework, language version, or design library to a current version. This exercises Devin's ability to handle complex, cross-cutting changes: namespace migrations, deprecated API removal, build system updates, and breaking change resolution.

## Target Outcomes

- Application builds and all tests pass after the upgrade
- Framework upgraded to target version (Spring Boot 3.x, Angular latest, etc.)
- PR with clear description of what changed and why
- Upgrade documentation or migration notes in the PR description

## What Participants Will Learn

- How Devin handles complex, cross-cutting framework upgrades
- How Devin identifies and resolves breaking changes (namespace migrations, API changes)
- Devin's ability to iteratively fix build/test failures after an upgrade
- The importance of having tests before upgrading (safety net)
- How to use PR comments to steer Devin's work after the initial session

## Devin Features Exercised

- Large-scale refactoring across many files
- Build system modification (Gradle/npm)
- Iterative error resolution
- PR creation and PR comment responses
- DeepWiki for codebase exploration
- AskDevin for pre-session planning
- Parallel sessions (if trying multiple upgrades)
- GitHub PR feedback loop

## Difficulty

Intermediate

## Estimated Time

60 minutes

## Notes

- For Spring Boot 2 to 3 migration: key changes are javax.* to jakarta.*, Spring Security config changes, deprecated API removal
- There is no single right answer — different upgrade strategies are valid and comparing approaches is valuable
- After Devin opens the PR, spend time reviewing and commenting — the feedback loop is a key part of the learning

---

## <a id="petclinic-angular"></a>petclinic-angular

**Repository:** [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular)

Angular frontend for the Spring PetClinic application.

### Step 1: Paste into Devin

> Upgrade petclinic-angular to the latest Angular version. Handle any breaking changes from the Angular update guide, update all dependencies, fix deprecated APIs, and ensure the app builds successfully. Open a PR with the changes.

### Step 2: Research with Ask Devin

- *"What Angular version is petclinic-angular currently on? What breaking changes should I expect when upgrading to the latest?"*
- *"Are there any deprecated patterns (e.g., NgModules vs. standalone components) that should be modernized during the upgrade?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the component hierarchy and module structure. Identify which parts of the codebase will be most affected by the Angular upgrade.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the upgrade look complete? Are deprecated APIs fully removed?
- **Leave a comment** asking Devin to also migrate from NgModules to standalone components if not already done
- **Watch Devin respond** and push a follow-up commit

---

## <a id="ts-angular-realworld"></a>ts-angular-realworld

**Repository:** [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld)

Angular RealWorld example application.

### Step 1: Paste into Devin

> Upgrade ts-angular-realworld to the latest Angular version. Handle any breaking changes, update dependencies, fix deprecated APIs, and ensure the app builds and tests pass. Open a PR.

### Step 2: Research with Ask Devin

- *"What's the current Angular version in ts-angular-realworld and how many major versions behind is it?"*
- *"Should this upgrade be done incrementally (one major version at a time) or can we jump directly to the latest?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the application architecture. Focus on the routing, state management, and HTTP interceptor patterns — these are most likely to change across Angular versions.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — did Devin follow the Angular update guide? Are there leftover deprecated patterns?
- **Leave a comment** asking Devin to generate an upgrade report documenting all breaking changes encountered

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 / Java 11 monolith. Upgrade to Java 17+ and Spring Boot 3.x — handling javax to jakarta namespace migration, Gradle plugin updates, and deprecated API removal.

### Step 1: Paste into Devin

> Upgrade uc-spring-boot-upgrade-microservice-extraction from Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2. Handle the javax to jakarta namespace migration, update Gradle build configuration, fix any deprecations, and ensure all tests pass. Open a PR with the changes.

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
