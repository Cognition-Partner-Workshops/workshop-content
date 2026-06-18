# API Consolidation

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Repositories](#repositories)
  - [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

1. Review the repository's dual API architecture (REST + GraphQL)
2. Paste the sample prompt into Devin
3. Observe how Devin identifies and removes the redundant API surface while preserving functionality
4. Review the PR and leave feedback — Devin will iterate on your comments

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
- How the **PR feedback loop** works — reviewers comment, Devin iterates, CI re-runs (see [Collaboration Model](../../shared/general-themes/collaboration-model.md))

## Devin Features Exercised

- Codebase analysis (identifying what to keep vs. remove)
- Safe code removal
- Build configuration changes
- PR creation
- **Devin Review** — can verify that no functionality was lost during consolidation (see [Platform Capabilities → Devin Review](../../shared/general-themes/platform-capabilities.md#devin-review))

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## Repositories

### <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 monolith with both REST controllers and GraphQL mutations/fetchers (DGS framework) serving the same data.

#### Step 1: Paste into Devin

```text
The uc-spring-boot-upgrade-microservice-extraction repo has both REST controllers and GraphQL mutations/fetchers serving the same data. Consolidate to REST only: remove all GraphQL-related code (DGS framework, schema files, datafetchers, mutations), update the build.gradle to remove DGS dependencies, and ensure all functionality is accessible via REST endpoints. Verify tests pass.
```

#### Step 2: Research with Ask Devin

- *"What GraphQL operations exist in uc-spring-boot-upgrade-microservice-extraction? Is there any functionality available only through GraphQL that would be lost if we remove it?"*
- *"What would it take to go the other direction — keep GraphQL and remove REST? Which approach requires fewer changes?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the dual API architecture. Map out which operations are available on each API surface to ensure nothing is lost during consolidation.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the removal clean? Are there any orphaned files or unused dependencies?
- **Leave a comment** asking Devin to verify that all REST endpoints match the original GraphQL operations

---

## Key Takeaways

- Devin can map overlapping API surfaces and identify which operations exist on each — ensuring nothing is lost during consolidation
- Safe code removal requires updating build files, removing unused dependencies, and verifying tests still pass
- The PR feedback loop lets reviewers verify completeness (e.g., "are all GraphQL operations covered by REST?") and Devin follows up
- Devin Review can catch orphaned code or unused dependencies that were missed during removal

---

## Going Further

- **Parallel API consolidation across services** — use child agents to consolidate API patterns across multiple microservices simultaneously, each following the same consolidation playbook (see [Platform Capabilities → Child Agents](../../shared/general-themes/platform-capabilities.md#child-agents-divide-and-conquer))
- **Scheduled dead-code detection** — run Devin on a weekly schedule to identify unused API endpoints, orphaned schema files, and unreferenced dependencies, then open cleanup PRs (see [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md#scheduled-sessions))
- **Playbook-driven API standardization** — create a playbook encoding your team's API consolidation process (audit surfaces → map operations → remove redundant layer → verify tests → update docs) so it can be applied consistently across codebases (see [Platform Capabilities → Playbooks](../../shared/general-themes/platform-capabilities.md#playbooks))
