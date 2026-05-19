# Legacy Modernization Combined

## Repositories

- [uc-data-source-migration-jdbc-normalization](#uc-data-source-migration-jdbc-normalization)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [uc-legacy-modernization-cobol-to-java](#uc-legacy-modernization-cobol-to-java)

---

## Challenge

A combined modernization challenge that mirrors a real enterprise scenario: take a legacy application, rewrite it to a modern tech stack, break it into microservices, and migrate its data source from a legacy data warehouse to a modern schema — all while keeping the system functional.

This module combines three separate concerns into one end-to-end narrative:
1. **Legacy → Modern Tech Stack** (language/framework migration)
2. **Monolith → Microservices** (architectural decomposition)
3. **Legacy Data Source → Modern Data Source** (schema migration + app reconnection)

## Target Outcomes

- Java source code replacing COBOL with parity tests
- Spring Boot 3.x microservice extracted from monolith with OpenAPI contract
- Modern database entities with proper types replacing all-VARCHAR legacy tables
- API endpoints returning identical data after migration
- Migration documentation covering all three phases

## What Participants Will Learn

- How Devin handles multi-phase modernization projects
- The pattern of "migrate → verify → proceed" applied across tech stack, architecture, and data
- How data source migration requires both schema work AND application rewiring
- Testing strategies: parity tests, golden-file comparison, API contract verification

## Devin Features Exercised

- Multi-language understanding (COBOL → Java)
- Large-scale refactoring (monolith → microservices)
- Schema comprehension and data transformation
- Parallel session execution (run all 3 phases simultaneously)
- PR creation with comprehensive migration documentation

## Difficulty

Advanced (combined); Individual phases are Intermediate

## Estimated Time

60 minutes (all 3 phases) or 20 minutes per phase individually

## Notes

- For a 1-hour demo, focus on Phase 3 (data source migration) as it's the most visually demonstrable
- Phases can be run as parallel Devin sessions to show the scale advantage
- Each phase can also be done standalone using the individual modules: [COBOL to Java](cobol-to-java.md), [Framework Upgrade](framework-upgrade.md)/[Containerization & Microservice Extraction](containerization-microservice-extraction.md), [Data Source Migration](data-source-migration.md)

---

## <a id="uc-data-source-migration-jdbc-normalization"></a>uc-data-source-migration-jdbc-normalization

**Repository:** [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization)

**Phase 3 — Data Source Migration (20 min)**

### Step 1: Paste into Devin

> Review the legacy CDW schema in uc-data-source-migration-jdbc-normalization. The current app reads from denormalized tables where everything is VARCHAR. Create modern JPA entities matching data/modern-schema/modern_tables.sql with proper types (LocalDate, BigDecimal, Long). Write a migration service that transforms legacy data per data/mappings/column_mappings.md. Rewire LoanService.java to use modern repositories. Verify API responses match. Open a PR.

### Step 2: Research with Ask Devin

- *"What are the riskiest data type conversions in the legacy-to-modern migration? Which columns are most likely to have malformed data?"*
- *"How should we handle records that fail transformation — skip, log, or fail the migration?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the loan service domain model and data access patterns. Map out which API responses depend on which legacy tables.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the data transformations correct? Does the migration handle edge cases?
- **Leave a comment** asking Devin to add a rollback mechanism

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

**Phase 2 — Microservice Extraction (20 min)**

### Step 1: Paste into Devin

> Upgrade uc-spring-boot-upgrade-microservice-extraction from Spring Boot 2.6.3 to 3.x, then extract the article management domain into a standalone microservice with its own API contract, Dockerfile, and database. Create a docker-compose.yml that runs both services. Open a PR.

### Step 2: Research with Ask Devin

- *"What's the minimum viable extraction — which classes need to move and which can stay in the monolith?"*
- *"What API contract should the extracted service expose? Should we use OpenAPI spec?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand domain boundaries and shared code. Plan the extraction to minimize changes to the monolith.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the extraction clean? Does the monolith still work without the extracted domain?
- **Leave a comment** asking Devin to add integration tests between the services

---

## <a id="uc-legacy-modernization-cobol-to-java"></a>uc-legacy-modernization-cobol-to-java

**Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)

**Phase 1 — Tech Stack Migration (20 min)**

### Step 1: Paste into Devin

> Select the COBOL program CBACT01C.cbl from uc-legacy-modernization-cobol-to-java. Analyze its business logic, data structures, and I/O operations. Rewrite it as a Java 17+ application with JUnit tests that verify functional equivalence. Open a PR.

### Step 2: Research with Ask Devin

- *"What data structures does CBACT01C.cbl use? What's the best Java representation for COBOL copybook fields?"*
- *"Should the Java version use Spring Boot or plain Java? What are the trade-offs?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the COBOL program's dependencies and data flow. Identify which copybooks and files it references.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Java code faithfully represent the COBOL business logic?
- **Leave a comment** asking Devin to add more edge case tests for the data transformations
