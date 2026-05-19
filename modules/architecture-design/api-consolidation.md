# API Consolidation

## Repositories

- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Consolidate two API designs into one consistent design pattern. The RealWorld example app exposes both REST and GraphQL (DGS framework) APIs serving the same data — choose a consolidation strategy and execute it.

## Target Outcomes

- Single API pattern retained (REST-only or GraphQL-only)
- Removed API surface fully cleaned up (dependencies, schema files, handlers)
- All functionality accessible via the remaining API
- Tests pass after consolidation
- PR with consolidation changes

## What Participants Will Learn

- How Devin identifies parallel API implementations
- How Devin safely removes code while preserving functionality
- Dependency cleanup in build files
- Test verification after API surface changes

## Devin Features Exercised

- Codebase analysis (identifying what to keep vs. remove)
- Safe code removal
- Build configuration changes
- PR creation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 monolith with both REST controllers and GraphQL mutations/fetchers (DGS framework) serving the same data.

### Step 1: Paste into Devin

> The uc-spring-boot-upgrade-microservice-extraction repo has both REST controllers and GraphQL mutations/fetchers serving the same data. Consolidate to REST only: remove all GraphQL-related code (DGS framework, schema files, datafetchers, mutations), update the build.gradle to remove DGS dependencies, and ensure all functionality is accessible via REST endpoints. Verify tests pass. Open a PR.

### Step 2: Research with Ask Devin

- *"What GraphQL operations exist in uc-spring-boot-upgrade-microservice-extraction? Is there any functionality available only through GraphQL that would be lost if we remove it?"*
- *"What would it take to go the other direction — keep GraphQL and remove REST? Which approach requires fewer changes?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the dual API architecture. Map out which operations are available on each API surface to ensure nothing is lost during consolidation.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the removal clean? Are there any orphaned files or unused dependencies?
- **Leave a comment** asking Devin to verify that all REST endpoints match the original GraphQL operations
- **Watch Devin respond** and push a follow-up commit
