# Dependency Graph Analysis

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
  - [calcom](#calcom)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

1. Pick a repository below
2. Paste the sample prompt into Devin
3. Observe how Devin maps internal dependencies, identifies coupling hotspots, and proposes decoupling strategies
4. Review the PR and leave feedback — Devin will iterate on your comments

---

## Challenge

Map and analyze internal dependency graphs to identify circular dependencies, tightly coupled modules, and opportunities for decoupling. Produce a dependency analysis report with refactoring recommendations and implement quick-win decoupling changes.

## Target Outcomes

- Dependency graph visualization or structured report
- Identified circular dependencies and tight coupling hotspots
- Refactoring recommendations with priority ranking
- PR with any quick-win decoupling changes

## What Participants Will Learn

- How Devin performs static analysis to map module and package dependencies
- How Devin identifies architectural risks like circular dependencies and tight coupling
- Devin's ability to reason about module boundaries and suggest decoupling strategies
- How to use AI-assisted analysis to prioritize refactoring efforts
- How the **PR feedback loop** works — reviewers comment, Devin iterates, CI re-runs (see [Collaboration Model](../../shared/general-themes/collaboration-model.md))

## Devin Features Exercised

- Static analysis and dependency resolution
- Architectural reasoning and pattern detection
- Visualization and structured reporting
- PR creation with refactoring changes
- **Devin Review** — can flag new circular dependencies or coupling regressions in future PRs (see [Platform Capabilities → Devin Review](../../shared/general-themes/platform-capabilities.md#devin-review))

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## Repositories

### <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot monolith with interleaved REST and GraphQL layers sharing service and data access components — a realistic target for dependency analysis in a Java codebase.

#### Step 1: Paste into Devin

```text
Analyze the internal dependency graph of uc-spring-boot-upgrade-microservice-extraction. Map the package-level dependencies across the application — controllers, datafetchers, services, repositories, and domain models. Identify: (1) any circular dependencies between packages, (2) tightly coupled modules where changes in one package would cascade across many others, (3) packages with the highest fan-in and fan-out counts. Produce a dependency analysis report in `docs/dependency-analysis.md` that includes a text-based dependency graph, a list of coupling hotspots, and prioritized refactoring recommendations. Implement the top quick-win decoupling change.
```

#### Step 2: Research with Ask Devin

- *"What are the package-level dependencies in uc-spring-boot-upgrade-microservice-extraction? Which packages have the most incoming and outgoing dependencies?"*
- *"Are there any circular dependencies between the REST controllers, GraphQL datafetchers, and the service layer?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the package structure and how the REST and GraphQL layers interact with shared services. Identify which service classes are used by both API surfaces — these are natural coupling points.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the dependency report match your understanding of the codebase structure?
- **Leave a comment** asking Devin to investigate a specific coupling hotspot in more detail or propose an interface-based decoupling for a tightly coupled pair

---

### <a id="calcom"></a>calcom

**Repository:** [calcom](https://github.com/Cognition-Partner-Workshops/calcom)

Large TypeScript monorepo with dozens of packages under `packages/` and `apps/` — an advanced target for cross-package dependency analysis at scale.

#### Step 1: Paste into Devin

```text
Analyze the dependency graph of the calcom monorepo. Focus on the packages under `packages/` and their relationships to `apps/web/`. Map which packages depend on each other, identify: (1) circular dependencies between packages, (2) packages with excessive fan-out (importing from too many other packages), (3) packages that are tightly coupled and could benefit from clearer interface boundaries. Produce a dependency analysis report in `docs/dependency-analysis.md` with a structured dependency matrix, coupling hotspots, and prioritized refactoring recommendations. If there are quick-win decoupling opportunities (e.g., extracting shared types or breaking a circular import), implement one.
```

#### Step 2: Research with Ask Devin

- *"Which packages in the calcom monorepo have the most cross-package imports? Are there any circular dependency chains?"*
- *"How do the packages under packages/features/ depend on packages/lib/ and packages/prisma/? Is there a clean dependency hierarchy or are there cycles?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the monorepo package architecture. Look at the `turbo.json` task dependencies and `package.json` workspace references to identify the intended dependency hierarchy versus actual import patterns.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the analysis correctly distinguish between intended dependencies (declared in package.json) and accidental coupling (direct file imports)?
- **Leave a comment** asking Devin to propose how a specific circular dependency could be broken with a new shared package or interface

---

## Key Takeaways

- Devin can map internal dependency graphs through static analysis and produce structured reports with coupling metrics
- Circular dependencies and tight coupling are architectural risks that Devin can identify and prioritize for remediation
- The PR feedback loop lets reviewers direct deeper investigation into specific coupling hotspots
- Devin Review can flag new circular dependencies or coupling regressions in future PRs, preventing architectural decay
- Dependency analysis is a prerequisite for safe microservice extraction — understanding coupling before splitting is critical

---

## Going Further

- **Scheduled dependency health checks** — run Devin on a monthly schedule to re-analyze the dependency graph, compare against the previous baseline, and flag new coupling regressions (see [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md#scheduled-sessions))
- **Devin Review for coupling prevention** — enable Devin Review with rules to flag PRs that introduce new cross-package dependencies or circular imports (see [Platform Capabilities → Devin Review](../../shared/general-themes/platform-capabilities.md#devin-review))
- **Parallel decoupling across modules** — use child agents to implement decoupling changes across multiple tightly coupled module pairs simultaneously, each following the same interface-extraction playbook (see [Platform Capabilities → Child Agents](../../shared/general-themes/platform-capabilities.md#child-agents-divide-and-conquer))
- **Dependency analysis as shared context** — add the dependency report to Devin's knowledge notes so future sessions understand the module architecture and avoid introducing new coupling (see [Architecture Strengths → Shared Context Layer](../../shared/general-themes/architecture-strengths.md#shared-context-layer))
