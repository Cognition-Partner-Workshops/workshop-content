# Dependency Graph Analysis

## Repositories

- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [calcom](#calcom)

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

## Devin Features Exercised

- Static analysis and dependency resolution
- Architectural reasoning and pattern detection
- Visualization and structured reporting
- PR creation with refactoring changes

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot monolith with interleaved REST and GraphQL layers sharing service and data access components — a realistic target for dependency analysis in a Java codebase.

### Step 1: Paste into Devin

> Analyze the internal dependency graph of uc-spring-boot-upgrade-microservice-extraction. Map the package-level dependencies across the application — controllers, datafetchers, services, repositories, and domain models. Identify: (1) any circular dependencies between packages, (2) tightly coupled modules where changes in one package would cascade across many others, (3) packages with the highest fan-in and fan-out counts. Produce a dependency analysis report in `docs/dependency-analysis.md` that includes a text-based dependency graph, a list of coupling hotspots, and prioritized refactoring recommendations. Implement the top quick-win decoupling change and open a PR with the report and any code changes.

### Step 2: Research with Ask Devin

- *"What are the package-level dependencies in uc-spring-boot-upgrade-microservice-extraction? Which packages have the most incoming and outgoing dependencies?"*
- *"Are there any circular dependencies between the REST controllers, GraphQL datafetchers, and the service layer?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the package structure and how the REST and GraphQL layers interact with shared services. Identify which service classes are used by both API surfaces — these are natural coupling points.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the dependency report match your understanding of the codebase structure?
- **Leave a comment** asking Devin to investigate a specific coupling hotspot in more detail or propose an interface-based decoupling for a tightly coupled pair
- **Watch Devin respond** and push a follow-up commit

---

## <a id="calcom"></a>calcom

**Repository:** [calcom](https://github.com/Cognition-Partner-Workshops/calcom)

Large TypeScript monorepo with dozens of packages under `packages/` and `apps/` — an advanced target for cross-package dependency analysis at scale.

### Step 1: Paste into Devin

> Analyze the dependency graph of the calcom monorepo. Focus on the packages under `packages/` and their relationships to `apps/web/`. Map which packages depend on each other, identify: (1) circular dependencies between packages, (2) packages with excessive fan-out (importing from too many other packages), (3) packages that are tightly coupled and could benefit from clearer interface boundaries. Produce a dependency analysis report in `docs/dependency-analysis.md` with a structured dependency matrix, coupling hotspots, and prioritized refactoring recommendations. If there are quick-win decoupling opportunities (e.g., extracting shared types or breaking a circular import), implement one and open a PR with the report and changes.

### Step 2: Research with Ask Devin

- *"Which packages in the calcom monorepo have the most cross-package imports? Are there any circular dependency chains?"*
- *"How do the packages under packages/features/ depend on packages/lib/ and packages/prisma/? Is there a clean dependency hierarchy or are there cycles?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the monorepo package architecture. Look at the `turbo.json` task dependencies and `package.json` workspace references to identify the intended dependency hierarchy versus actual import patterns.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the analysis correctly distinguish between intended dependencies (declared in package.json) and accidental coupling (direct file imports)?
- **Leave a comment** asking Devin to propose how a specific circular dependency could be broken with a new shared package or interface
- **Watch Devin respond** and push a follow-up commit
