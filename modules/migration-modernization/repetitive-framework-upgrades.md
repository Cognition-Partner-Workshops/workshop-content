# Repetitive Framework Upgrades

## Repositories

- [petclinic-angular](#petclinic-angular)
- [ts-angular-realworld](#ts-angular-realworld)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Demonstrate how Devin handles repetitive upgrade tasks across multiple codebases — the same type of upgrade applied consistently across different services or projects. Run parallel Devin sessions to show the scale advantage.

## Target Outcomes

- Application builds and all tests pass on the upgraded version
- Breaking changes handled systematically (namespace migrations, API changes)
- Upgrade documented: what changed, what broke, how it was fixed
- PR with clean, reviewable diff

## What Participants Will Learn

- How Devin follows established upgrade guides (Spring Boot migration guide, Angular update guide)
- How Devin identifies and resolves breaking changes systematically
- The value of running parallel Devin sessions for repetitive tasks across services
- How to evaluate Devin's upgrade PRs for correctness and completeness

## Devin Features Exercised

- Large-scale refactoring across many files
- Build system modification (Gradle, npm)
- Iterative error resolution (fix → build → fix cycle)
- Parallel session execution
- PR creation with upgrade documentation

## Difficulty

Intermediate

## Estimated Time

60 minutes (per upgrade; parallel sessions can run simultaneously)

## Notes

- This challenge is specifically designed to showcase Devin's value for repetitive, well-defined tasks
- The parallel sessions demonstrate a key Devin advantage: doing the same task across multiple repos simultaneously
- For workshops focused on enterprise scale, emphasize that the same prompt can be reused across 10+ microservices

---

## <a id="petclinic-angular"></a>petclinic-angular

**Repository:** [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular)

Angular frontend for Spring PetClinic — upgrade Angular to the latest major version.

### Step 1: Paste into Devin

> Upgrade petclinic-angular to the latest Angular version. Handle any breaking changes from the Angular update guide, update dependencies, fix deprecated APIs, and ensure the app builds. Document every breaking change you encounter and how you resolved it. Open a PR.

### Step 2: Research with Ask Devin

- *"What breaking changes should I expect when upgrading this Angular version? Which ones are most likely to cause issues?"*
- Run this upgrade in **parallel** with the Spring Boot upgrade on a different repo to demonstrate repetitive upgrades at scale

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the component structure and identify which modules will be most affected by the upgrade.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — compare the upgrade approach with the other Angular repo's upgrade PR
- **Leave a comment** asking Devin to document the upgrade steps as a repeatable runbook

---

## <a id="ts-angular-realworld"></a>ts-angular-realworld

**Repository:** [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld)

Angular RealWorld example app — upgrade Angular to the latest major version.

### Step 1: Paste into Devin

> Upgrade ts-angular-realworld to the latest Angular version. Handle any breaking changes from the Angular update guide, update dependencies, fix deprecated APIs, and ensure the app builds and tests pass. Open a PR.

### Step 2: Research with Ask Devin

- *"How does the upgrade experience differ between this Angular app and petclinic-angular? Are the same breaking changes encountered?"*
- Run this in parallel with other upgrades to compare Devin's consistency

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the routing and state management patterns. These are most likely to change across Angular versions.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — compare the approach with the PetClinic Angular upgrade
- **Leave a comment** asking Devin to create a shared upgrade checklist from both experiences

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 / Java 11 — upgrade to Java 17+ and Spring Boot 3.x.

### Step 1: Paste into Devin

> Upgrade uc-spring-boot-upgrade-microservice-extraction from Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2. Handle the javax to jakarta namespace migration, update Gradle build configuration, fix any deprecations, and ensure all tests pass. Document every breaking change you encounter and how you resolved it. Open a PR.

### Step 2: Research with Ask Devin

- *"What's the most efficient order to tackle the breaking changes — namespace first, then build config, then deprecated APIs?"*
- Run this upgrade in **parallel** with the Angular upgrades to show multi-stack repetitive upgrades

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the build configuration and dependency structure. Plan the upgrade order to minimize cascading failures.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the javax → jakarta migration complete? Any files missed?
- **Leave a comment** asking Devin to also document the upgrade as a repeatable runbook for other Spring Boot 2.x services
