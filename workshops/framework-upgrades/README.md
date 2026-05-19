# Workshop: Framework Upgrades

## Overview

| | |
|---|---|
| **Focus** | Angular and Spring Boot version upgrades across multiple repositories at scale |
| **Duration** | 1-2 hours |
| **Audience** | Development teams, tech leads, enterprise platform teams |
| **Key Modules** | [Framework Upgrade](../../modules/migration-modernization/framework-upgrade.md), [Repetitive Framework Upgrades](../../modules/migration-modernization/repetitive-framework-upgrades.md) |

## Workshop Narrative

Framework upgrades are one of the most common and repetitive tasks in enterprise software. This workshop demonstrates how Devin handles the same upgrade pattern applied consistently across different services — and how parallel sessions show enterprise scale.

## Labs

### Lab 1 — Spring Boot + Angular Parallel Upgrades

- **Modules:** [Framework Upgrade](../../modules/migration-modernization/framework-upgrade.md) + [Repetitive Framework Upgrades](../../modules/migration-modernization/repetitive-framework-upgrades.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot 2.6.3 → 3.x
  - [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular) — Angular version upgrade
  - [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld) — Angular version upgrade (parallel comparison)
- **Objective:** Run parallel Devin sessions upgrading Angular and Spring Boot across multiple repos
- **Duration:** 60 min

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

Run as **parallel sessions**:

**Session A — Spring Boot:**
> Upgrade uc-spring-boot-upgrade-microservice-extraction from Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2. Handle the javax to jakarta namespace migration, update Gradle build configuration, fix any deprecations, and ensure all tests pass. Document every breaking change. Open a PR.

**Session B — Angular:**
> Upgrade petclinic-angular to the latest Angular version. Handle breaking changes from the Angular update guide, update all dependencies, fix deprecated APIs, and ensure the app builds. Document every breaking change. Open a PR.

#### Step 2: Research with Ask Devin

- *"What are the biggest risks when upgrading from Spring Boot 2 to 3?"*
- *"What Angular version is petclinic-angular currently on? What breaking changes are expected?"*
- *"Compare the upgrade paths for both Angular repos — are the same breaking changes expected?"*

#### Step 3 (Optional): Read the DeepWiki

Open each repo's DeepWiki page to understand the codebase before the upgrade. Focus on build configuration, dependency graphs, and deprecated patterns.

#### Step 4 (Optional): Review & Give Feedback

- Compare upgrade PRs side-by-side across repos
- Ask Devin to generate a shared upgrade checklist or repeatable runbook

**Target Outcomes:** Upgraded builds with passing tests, upgrade documentation, repeatable runbook

## Repos Required

- [ ] uc-spring-boot-upgrade-microservice-extraction
- [ ] petclinic-angular
- [ ] ts-angular-realworld (optional, for parallel comparison)

## Key Takeaways

- **"Same prompt, multiple repos"** — the same upgrade task applied consistently across repos demonstrates enterprise scale
- **"Parallel sessions save calendar time"** — upgrades that would take weeks sequentially can run simultaneously
- **"Consistency across upgrades"** — Devin applies the same patterns and catches the same breaking changes
