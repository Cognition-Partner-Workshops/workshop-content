# Code Refactoring & Tech Debt

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Repositories](#repositories)
  - [timesheet-app](#timesheet-app)
  - [calcom](#calcom)
  - [ts-java-spring-boot-realworld](#ts-java-spring-boot-realworld)
  - [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

1. Pick a repository below
2. Paste the sample prompt into Devin
3. Observe how Devin identifies code smells and applies refactoring patterns while keeping tests green
4. Review the PR and leave feedback — Devin will iterate on your comments

---

## Challenge

Identify and resolve code smells, tech debt, and structural issues in a codebase. This challenge focuses on improving code quality through refactoring — extracting methods, reducing complexity, removing duplication, and improving naming — without changing external behavior.

## Target Outcomes

- Code smells identified and cataloged (complexity, duplication, long methods, etc.)
- Refactoring applied with no behavioral changes
- All existing tests still pass after refactoring
- PR with clear description of each refactoring and its rationale

## What Participants Will Learn

- How Devin identifies code smells and structural issues
- How Devin applies refactoring patterns (Extract Method, Rename, Move, etc.)
- How to verify refactoring safety through existing tests
- The difference between cosmetic cleanup and meaningful structural improvement
- How the **PR feedback loop** works — reviewers comment, Devin iterates, CI re-runs (see [Collaboration Model](../../shared/general-themes/collaboration-model.md))

## Devin Features Exercised

- Deep codebase analysis
- Multi-file refactoring
- Test verification (ensuring no regressions)
- PR creation with refactoring rationale
- **Devin Review** — can catch code smells in every future PR, preventing new tech debt from accumulating (see [Platform Capabilities → Devin Review](../../shared/general-themes/platform-capabilities.md#devin-review))

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## Repositories

### <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

React + Express application with opportunities for backend route handler refactoring and frontend component decomposition.

#### Step 1: Paste into Devin

```text
Analyze timesheet-app for code smells and tech debt. Focus on the backend route handlers and service layer — identify long functions, duplicated logic, and poor naming. Refactor the top 5 most impactful issues without changing any external behavior. Run all existing tests to verify no regressions.
```

#### Step 2: Research with Ask Devin

- *"What are the most complex functions in timesheet-app? Which ones have the highest cyclomatic complexity?"*
- *"Are there any duplicated patterns across the backend route handlers that could be extracted into shared utilities?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the architecture. Identify modules with the tightest coupling or most dependencies — these are candidates for structural refactoring.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does each refactoring preserve behavior? Are the new names more descriptive?
- **Leave a comment** asking Devin to also extract a specific repeated pattern you notice

---

### <a id="calcom"></a>calcom

**Repository:** [calcom](https://github.com/Cognition-Partner-Workshops/calcom)

Large TypeScript monorepo with many opportunities for component decomposition and utility extraction.

#### Step 1: Paste into Devin

```text
Analyze the calcom codebase for code smells in the packages/features/ directory. Identify components that are too large (>300 lines), functions with high cyclomatic complexity, and duplicated patterns. Refactor the top 5 most impactful issues. Run tests to verify.
```

#### Step 2: Research with Ask Devin

- *"Which packages in calcom have the most code duplication across them?"*
- *"Are there any circular dependencies between packages that should be resolved?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the package dependency graph. Target refactoring at the most tangled dependencies.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the refactorings scoped appropriately for a large monorepo?
- **Leave a comment** asking Devin to refactor a specific large component you identify

---

### <a id="ts-java-spring-boot-realworld"></a>ts-java-spring-boot-realworld

**Repository:** [ts-java-spring-boot-realworld](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld)

Spring Boot application with potential for service layer refactoring, DTO pattern improvements, and exception handling cleanup.

#### Step 1: Paste into Devin

```text
Analyze ts-java-spring-boot-realworld for code smells. Focus on: long service methods, duplicated data mapping logic, inconsistent error handling patterns, and missing separation of concerns. Refactor the top 5 issues. Ensure all tests pass.
```

#### Step 2: Research with Ask Devin

- *"Are there any God classes or services that handle too many responsibilities?"*
- *"Is the exception handling consistent across all controllers?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the layered architecture. Identify where layers are leaking (e.g., database concerns in controllers).

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the refactorings improve separation of concerns?
- **Leave a comment** asking Devin to extract a specific cross-cutting concern

---

### <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 monolith with both REST and GraphQL APIs — opportunities for reducing duplication between the two API surfaces.

#### Step 1: Paste into Devin

```text
Analyze uc-spring-boot-upgrade-microservice-extraction for code smells. The app has both REST controllers and GraphQL datafetchers serving the same data — identify duplicated logic between them. Also find long methods, inconsistent patterns, and poor naming. Refactor the top 5 issues. Ensure all tests pass.
```

#### Step 2: Research with Ask Devin

- *"How much logic is duplicated between the REST and GraphQL layers? Could a shared service layer reduce this?"*
- *"Are there any MyBatis mappers that could be simplified or consolidated?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the dual API architecture and identify the shared service layer that both APIs should use.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the refactoring reduce duplication without breaking either API surface?
- **Leave a comment** asking Devin to verify both REST and GraphQL responses are unchanged

---

## Key Takeaways

- Devin can systematically identify code smells (complexity, duplication, poor naming) and apply standard refactoring patterns
- Refactoring safety is verified by running existing tests — Devin does not change external behavior
- The PR feedback loop lets reviewers direct Devin to address additional code smells discovered during review
- Devin Review can catch new code smells in every future PR, preventing tech debt from accumulating
- Multi-file refactoring across layers (controllers, services, repositories) is well within Devin's capabilities

---

## Going Further

- **Scheduled code health analysis** — run Devin on a weekly schedule to scan for new code smells, complexity hotspots, and duplicated patterns, then open cleanup PRs (see [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md#scheduled-sessions))
- **Devin Review for tech debt prevention** — enable Devin Review to flag code smells in every incoming PR, preventing new tech debt before it merges (see [Platform Capabilities → Devin Review](../../shared/general-themes/platform-capabilities.md#devin-review))
- **Parallel refactoring across repos** — use child agents to apply the same refactoring pattern (e.g., extracting shared utilities, standardizing error handling) across multiple repositories simultaneously (see [Platform Capabilities → Child Agents](../../shared/general-themes/platform-capabilities.md#child-agents-divide-and-conquer))
- **Playbook-driven refactoring** — create a playbook encoding your team's refactoring process (analyze → prioritize → refactor → verify → document) so every tech debt session follows a consistent methodology (see [Platform Capabilities → Playbooks](../../shared/general-themes/platform-capabilities.md#playbooks))
