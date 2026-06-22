# Legacy Modernization Combined

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Going Further](#going-further)
- [Notes](#notes)
- [Phase 1 — COBOL to Java](#phase-1--cobol-to-java)
- [Phase 2 — Framework Upgrade + Containerization](#phase-2--framework-upgrade--containerization)
- [Phase 3 — Data Source Migration](#phase-3--data-source-migration)

---

## Quick Start

Paste this prompt into Devin to try the data source migration phase (the most self-contained phase):

```
Review the legacy CDW schema in
uc-data-source-migration-jdbc-normalization. The current
app reads from denormalized tables where everything is
VARCHAR. Create modern JPA entities matching
data/modern-schema/modern_tables.sql with proper types
(LocalDate, BigDecimal, Long). Write a migration service
that transforms legacy data per
data/mappings/column_mappings.md. Rewire LoanService.java
to use modern repositories. Verify API responses match.
```

---

## Repositories

- [uc-legacy-modernization-cobol-to-java](#phase-1--cobol-to-java) — COBOL to Java migration
- [uc-spring-boot-upgrade-microservice-extraction](#phase-2--framework-upgrade--containerization) — Framework upgrade + containerization
- [uc-data-source-migration-jdbc-normalization](#phase-3--data-source-migration) — Data source migration

---

## Challenge

Execute a full-stack legacy modernization across three phases and three repos, each demonstrating a different modernization pattern:

1. **Tech stack migration** (COBOL → Java) — Translate COBOL business logic to modern Java
2. **Framework upgrade + containerization** (Spring Boot 2 → 3 + Docker) — Upgrade frameworks and extract into microservices
3. **Data source migration** (denormalized VARCHAR → normalized typed entities) — Modernize the data access layer

This is the capstone module that ties together all the individual migration skills into a single end-to-end narrative: migrate the code, upgrade the framework, modernize the data layer.

## Target Outcomes

- **Phase 1**: Java translation of a COBOL program with JUnit parity tests
- **Phase 2**: Spring Boot 3 upgrade with javax→jakarta migration, plus article management domain extracted as a separate microservice with Dockerfile and docker-compose
- **Phase 3**: Modern JPA entities with proper types, migration service for data transformation, and rewired application layer using modern repositories with API parity verification
- Three PRs (one per repo), each demonstrating a different modernization pattern

## What Participants Will Learn

- How the three modernization patterns (tech stack migration, framework upgrade, data normalization) work together in a real modernization program
- How each phase builds on the previous one's foundation
- How Devin handles different kinds of modernization in succession
- How to evaluate trade-offs between incremental and big-bang modernization

## Devin Features Exercised

- Multi-repo code comprehension and generation
- Cross-language translation (COBOL → Java)
- Namespace migration (javax → jakarta)
- Domain extraction and containerization
- Data modeling and type-safe entity generation
- PR creation across multiple repos
- Child sessions for parallel phases
- AskDevin for architecture decisions

## Difficulty

Advanced

## Estimated Time

60 minutes (one phase) or 120+ minutes (all three phases)

## Going Further

- **Divide-and-conquer with child sessions**: Run all three phases in parallel using child sessions — one child per repo. Each child follows its phase-specific playbook independently. A parent session tracks overall progress and reports on the combined modernization status.
- **Playbook-driven modernization**: Encode each phase as a separate playbook. When onboarding a new modernization engagement, run all three playbooks to assess which phases apply and in what order.
- **Scheduled progress tracking**: After each phase completes, configure a scheduled session that runs regression tests across all three repos to verify nothing broke.
- **Knowledge notes**: Capture modernization patterns and decisions from each phase as knowledge notes. Future engagements benefit from accumulated wisdom about common COBOL patterns, Spring Boot migration pitfalls, and data normalization strategies.
- **Team-based execution**: Different teams can own different phases. The shared context layer ensures consistency even when multiple engineers work on different repos simultaneously.

## Notes

- For a 1-hour walkthrough, focus on Phase 3 (data source migration) as it is the most visually demonstrable — you can see the before/after of VARCHAR → typed entities and verify API responses match
- Phases can be run independently (each repo stands alone) or sequentially (the narrative builds)
- For a full-day workshop, run all three phases with discussion between each
- The three repos are intentionally different technology stacks to show Devin's breadth

---

## <a id="phase-1--cobol-to-java"></a>Phase 1 — COBOL to Java

**Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)

Migrate a COBOL batch program to Java. See [COBOL to Java](cobol-to-java.md) for the full module details.

### Step 1: Paste into Devin

```
Select the COBOL program CBACT01C.cbl from
uc-legacy-modernization-cobol-to-java. Analyze its business
logic, data structures, and I/O operations. Rewrite it as a
Java 17+ application with JUnit tests that verify functional
equivalence.
```

### Step 2: Research with Ask Devin

- *"What data structures does CBACT01C.cbl use? What's the best Java representation for COBOL copybook fields?"*
- *"Should the Java version use Spring Boot or plain Java? What are the trade-offs?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the COBOL program's dependencies and data flow. Identify which copybooks and files it references.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Java code faithfully represent the COBOL business logic?
- **Leave a comment** asking Devin to add more edge case tests for the data transformations

---

## <a id="phase-2--framework-upgrade--containerization"></a>Phase 2 — Framework Upgrade + Containerization

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Upgrade from Spring Boot 2 to 3, then extract a domain into a microservice. See [Framework Upgrade](framework-upgrade.md) and [Containerization & Microservice Extraction](containerization-microservice-extraction.md) for full module details.

### Step 1: Paste into Devin

```
Upgrade uc-spring-boot-upgrade-microservice-extraction from
Spring Boot 2.6.3 to 3.x, then extract the article
management domain into a standalone microservice with its
own API contract, Dockerfile, and database. Create a
docker-compose.yml that runs both services.
```

### Step 2: Research with Ask Devin

- *"What's the fastest path to upgrade uc-spring-boot-upgrade-microservice-extraction from Spring Boot 2.6 to 3.x? Which changes are mechanical vs. architectural?"*
- *"After upgrading, which domain is the best candidate for extraction? What's the least-coupled module?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand domain boundaries and shared code. Plan the extraction to minimize changes to the monolith.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the extraction clean? Does the monolith still work without the extracted domain?
- **Leave a comment** asking Devin to add integration tests between the services

---

## <a id="phase-3--data-source-migration"></a>Phase 3 — Data Source Migration

**Repository:** [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization)

Modernize a legacy data access layer from denormalized VARCHAR tables to properly typed JPA entities.

### Step 1: Paste into Devin

```
Review the legacy CDW schema in
uc-data-source-migration-jdbc-normalization. The current
app reads from denormalized tables where everything is
VARCHAR. Create modern JPA entities matching
data/modern-schema/modern_tables.sql with proper types
(LocalDate, BigDecimal, Long). Write a migration service
that transforms legacy data per
data/mappings/column_mappings.md. Rewire LoanService.java
to use modern repositories. Verify API responses match.
```

### Step 2: Research with Ask Devin

- *"What are the biggest data quality risks when migrating from VARCHAR to typed columns? What edge cases should the migration service handle?"*
- *"What business logic in LoanService.java makes assumptions about the legacy data format? What needs to change?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the legacy schema structure, data flow, and application layer. Plan the migration order.

### Step 4 (Optional): Review & Give Feedback

- **Review the JPA entities** — do the types match the modern schema? Are the relationships correct?
- **Review the migration service** — does it handle edge cases (nulls, malformed dates, overflow)?
- **Leave a comment** asking Devin to add integration tests that verify API response parity

### Key Takeaways

- **End-to-end modernization**: The three phases demonstrate the full modernization lifecycle — migrate code, upgrade frameworks, modernize data — showing how each phase builds on the previous
- **Divide-and-conquer**: Each phase can run as an independent child session, parallelizing the full modernization program
- **Multiple modernization patterns**: Different parts of a legacy system require different modernization strategies (rewrite, upgrade, normalize) — Devin adapts its approach to each
- **Verification at every stage**: Parity tests, build verification, and API response matching ensure nothing breaks during modernization
