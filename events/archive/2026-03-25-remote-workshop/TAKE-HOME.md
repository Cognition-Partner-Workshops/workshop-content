# Take-Home Labs — Self-Paced Devin Exercises

## Overview

| | |
|---|---|
| **Companion To** | [60-Minute Live Workshop](README.md) (Platform-Conformant Microservice Decomposition) |
| **Format** | Self-paced, asynchronous |
| **Estimated Time** | 4 hours total (~60 min per lab) |
| **Labs** | 4 labs across 4 use case categories |
| **Prerequisites** | Devin account, repos set up on Devin's machine |

## Purpose

These labs extend the live workshop with four additional use cases. Each lab is self-contained and can be completed independently in any order. Together they demonstrate the breadth of Devin's capabilities across legacy modernization, framework upgrades, security remediation, and data migration.

> **No PR comment access?** Each lab includes an alternative "In-Session Feedback" step you can use instead of leaving GitHub PR comments.

---

## Lab 1 — Legacy Modernization: COBOL → Java (60 min)

- **Module:** [COBOL to Java](../../../modules/migration-modernization/cobol-to-java.md#uc-legacy-modernization-cobol-to-java)
- **Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- **Objective:** Explore a real COBOL mainframe application and use Devin to modernize part of it — you choose the scope, target, and approach

### Step 1: Get Started Fast (copy-paste this prompt into Devin)

> Analyze the COBOL program CBACT01C.cbl in uc-legacy-modernization-cobol-to-java. Understand its business logic, data structures (copybooks), and I/O operations. Rewrite it as a Java 17+ application using modern idioms. Create JUnit tests that verify the Java version produces identical results to the COBOL version for a set of sample inputs. Open a PR with the Java code and tests.

### Step 2: Level Up with AskDevin

Open a new AskDevin conversation and try:
- *"What are the most complex COBOL programs in uc-legacy-modernization-cobol-to-java and what do they do?"*
- *"What would be the best Java architecture for migrating CBTRN01C.cbl? Consider Spring Boot, plain Java, or Kotlin."*
- Use the refined understanding to start a **second session** with a more targeted prompt

### Step 3: Explore with DeepWiki

Open the repo's **DeepWiki** page to browse the auto-generated architecture diagrams and module explanations. Use what you learn to try something different:
- Pick a different COBOL program and compare how Devin handles it
- Try migrating to a different target (Kotlin, Python, Spring Boot service)
- Ask Devin to reverse-engineer business rules or generate a data dictionary
- Run parallel sessions migrating the same program to two different targets

### Step 4: Interact and Give Feedback

Once Devin opens a PR from step 1:
- **Review the diff** — does the Java code faithfully represent the COBOL business logic?
- **Steer Devin in-session** — send feedback directly in the session chat:
  - *"The packed decimal conversion doesn't handle negative values — please fix"*
  - *"Can you also generate a data dictionary for the copybooks?"*
  - *"Add integration tests that compare COBOL output fixtures with the Java output"*
- **Watch Devin incorporate your feedback** and push updates without starting over

### Target Outcomes (any of these count)

- Java/Kotlin/Python source code + tests with a working build
- Parity tests: modern output matches COBOL output for provided fixtures
- `MIGRATION_NOTES.md` describing field mappings and decisions
- Technical documentation, data dictionary, or migration plan for the repo

---

## Lab 2 — Framework Upgrade & Refactor: Monolith → Microservices (60 min)

- **Module:** [Framework Upgrade](../../../modules/migration-modernization/framework-upgrade.md#uc-spring-boot-upgrade-microservice-extraction) + [Containerization & Microservice Extraction](../../../modules/migration-modernization/containerization-microservice-extraction.md#uc-spring-boot-upgrade-microservice-extraction)
- **Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)
- **Objective:** Take an older Java monolith (Java 11 + Spring Boot 2.6.3) and modernize it — you choose whether to focus on the upgrade, the microservice extraction, or both

### Step 1: Get Started Fast (copy-paste this prompt into Devin)

> Upgrade uc-spring-boot-upgrade-microservice-extraction from Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2. Handle the javax to jakarta namespace migration, update Gradle build configuration, fix any deprecations, and ensure all tests pass. Open a PR with the changes.

### Step 2: Level Up with AskDevin

Open a new AskDevin conversation and try:
- *"What are the domain boundaries in uc-spring-boot-upgrade-microservice-extraction? Which bounded context would be easiest to extract as a microservice?"*
- *"What's the best order to tackle the javax to jakarta migration, the Gradle plugin updates, and the deprecated API removals?"*
- Use the refined understanding to start a **second session** — try extracting a microservice, or upgrade with a different strategy

### Step 3: Explore with DeepWiki

Open the repo's **DeepWiki** page to browse the architecture, domain model, and dependency graph. Use what you learn to try something different:
- Extract a different bounded context (Articles, Users, or Comments) as a containerized microservice
- Have Devin produce an architecture decision record (ADR) for how the monolith should be decomposed
- Run parallel sessions — one upgrading the framework, one extracting a service

### Step 4: Interact and Give Feedback

Once Devin opens a PR from step 1:
- **Review the diff** — does the upgrade look complete? Are there files Devin missed?
- **Steer Devin in-session** — send feedback directly in the session chat:
  - *"This still uses javax.servlet — please update to jakarta.servlet"*
  - *"Can you also add a Dockerfile for the application?"*
  - *"Extract the Articles bounded context as a separate microservice with its own REST API"*
- Try leaving multiple pieces of feedback to see how Devin prioritizes and sequences the work

### Target Outcomes (any of these count)

- Application builds and tests on Java 17+ / Spring Boot 3.x
- One extracted microservice with its own Dockerfile and REST API
- Docker Compose to run the full stack locally
- Architecture decision record or migration report

---

## Lab 3 — CVE Remediations & Regulatory Code Standards (60 min)

- **Module:** [Upgrade Dependencies](../../../modules/security/upgrade-dependencies.md#uc-cve-remediation-regulatory-compliance) + [Remediate Vulnerabilities](../../../modules/security/remediate-vulnerabilities.md#uc-cve-remediation-regulatory-compliance) + [Shift Left Security](../../../modules/security/shift-left-security.md#uc-cve-remediation-regulatory-compliance)
- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Objective:** A Spring Boot 2.6.3 service has accumulated vulnerable dependencies (Spring4Shell, SnakeYAML RCE, SQLite JDBC RCE, and more). Scan, remediate, and add automated compliance checks
- **Known CVEs:** See the [full CVE findings report](../../../modules/security/remediate-vulnerabilities.md#uc-cve-remediation-regulatory-compliance) for a breakdown of all 18+ known vulnerabilities by severity (5 Critical, 8 High, 5 Medium)

### Step 1: Get Started Fast (copy-paste this prompt into Devin)

> Run `./gradlew dependencyCheckAnalyze` on uc-cve-remediation-regulatory-compliance to identify dependency CVEs. Remediate the top 5 most critical findings (CVSS >= 7.0) — start with Spring Boot 2.6.3, SnakeYAML 1.29, and sqlite-jdbc 3.36.0.3. Re-run the scan to verify the fixes. Create a `SECURITY_REMEDIATION.md` documenting the before/after results and open a PR.

### Step 2: Level Up with AskDevin

Open a new AskDevin conversation and try:
- *"What are the known CVEs in uc-cve-remediation-regulatory-compliance's dependencies? Which ones are CRITICAL severity?"*
- *"What's the safest upgrade path for Spring Boot 2.6.3 — should we go to 2.7.x first or jump straight to 3.x?"*
- Use the analysis to start a **second session** — try adding a GitHub Actions security scanning workflow, or upgrade Spring Boot to 3.x instead of just patching

### Step 3: Explore with DeepWiki

Open the repo's **DeepWiki** page to understand the codebase architecture. Use what you learn to try different approaches:
- Have Devin add **SBOM generation** (CycloneDX) to the build
- Ask Devin to add a **GitHub Actions workflow** that runs Trivy or OWASP Dependency-Check on every PR
- Try adding **SonarQube scanning** — the repo has a pre-configured `docker-compose.sonarqube.yml`
- Ask Devin to add **pre-commit hooks** for secrets detection (gitleaks)

### Step 4: Interact and Give Feedback

Once Devin opens a PR from step 1:
- **Review the diff** — did Devin upgrade the right dependencies? Are there any it missed?
- **Steer Devin in-session** — send feedback directly in the session chat:
  - *"Can you also add a CI workflow that fails on CRITICAL CVEs?"*
  - *"The SnakeYAML version still has CVE-2022-1471 — please upgrade to 2.x"*
  - *"Add an SBOM generation step using CycloneDX"*
- Watch how Devin handles security-specific feedback and re-runs scans to verify fixes

### Target Outcomes (any of these count)

- OWASP Dependency-Check report with high/critical CVEs remediated
- SBOM generated (CycloneDX or SPDX)
- Secure coding checks added: SAST via SonarQube or Trivy
- CI gating workflow: builds fail on policy violations
- `SECURITY_REMEDIATION.md` with before/after evidence

---

## Lab 4 — DW Migration: Teradata → Snowflake (60 min)

- **Module:** [DW Migration: Teradata to Snowflake](../../../modules/data-engineering/dw-migration-teradata-to-snowflake.md#uc-dw-migration-teradata-to-snowflake)
- **Repository:** [uc-dw-migration-teradata-to-snowflake](https://github.com/Cognition-Partner-Workshops/uc-dw-migration-teradata-to-snowflake)
- **Objective:** A Teradata-based data warehouse needs to be migrated to Snowflake. Convert DDL/DML, build a migration runbook, and set up validation

### Step 1: Get Started Fast (copy-paste this prompt into Devin)

> Analyze all Teradata DDL in uc-dw-migration-teradata-to-snowflake/ddl/. Convert every table and view to Snowflake-compatible SQL, handling all Teradata-specific features (SET/MULTISET, PRIMARY INDEX, PARTITION BY RANGE_N, COMPRESS, CASESPECIFIC, FORMAT, QUALIFY, CSUM, MAVG). Place converted files in a new snowflake/ directory mirroring the original structure. Create a MIGRATION_RUNBOOK.md documenting every translation decision. Open a PR.

### Step 2: Level Up with AskDevin

Open a new AskDevin conversation and try:
- *"What Teradata-specific features are used in uc-dw-migration-teradata-to-snowflake and what are the Snowflake equivalents?"*
- *"What's the best approach for converting the BTEQ scripts in this repo to Snowflake-compatible SnowSQL or stored procedures?"*
- Use the analysis to start a **second session** — try converting the stored procedures and macros, or generate a full asset inventory

### Step 3: Explore with DeepWiki

Open the repo's **DeepWiki** page to browse the schema structure and SQL patterns. Use what you learn to try different approaches:
- Have Devin convert the **stored procedures** (SCD Type 2, daily load, monthly snapshot) to Snowflake stored procedures
- Ask Devin to convert the **BTEQ scripts** to SnowSQL or Snowflake Tasks
- Try having Devin generate **validation queries** — Snowflake equivalents of the Teradata checksum queries in `data/validation/`
- Ask Devin to produce a **complete asset inventory** with migration status for every object

### Step 4: Interact and Give Feedback

Once Devin opens a PR from step 1:
- **Review the diff** — does the SQL translation look correct? Are there Teradata features Devin missed?
- **Steer Devin in-session** — send feedback directly in the session chat:
  - *"The CSUM() function wasn't translated — please convert to SUM() OVER()"*
  - *"Can you also convert the macros in dml/macros/?"*
  - *"Generate validation queries that compare row counts and checksums between Teradata and Snowflake"*
- Try leaving inline feedback on specific SQL statements to see how Devin handles dialect-level corrections

See the full challenge details in [DW Migration: Teradata to Snowflake](../../../modules/data-engineering/dw-migration-teradata-to-snowflake.md) for more ideas — the repo includes a complete [Teradata features reference](https://github.com/Cognition-Partner-Workshops/uc-dw-migration-teradata-to-snowflake/blob/initial-code/docs/teradata_features_reference.md) with Snowflake equivalents.

### Target Outcomes (any of these count)

- Converted Snowflake DDL/DML for tables and views
- Converted stored procedures or BTEQ scripts
- `MIGRATION_RUNBOOK.md` with translation decisions documented
- Validation queries: Snowflake equivalents of Teradata checksums
- `SQL_TRANSLATION_NOTES.md` mapping Teradata→Snowflake differences

---

## Repos Required on Devin's Machine

Before starting, verify all repos are set up via **Settings > Machine configuration > Add repository**:

- [ ] uc-legacy-modernization-cobol-to-java
- [ ] uc-spring-boot-upgrade-microservice-extraction
- [ ] uc-cve-remediation-regulatory-compliance
- [ ] uc-dw-migration-teradata-to-snowflake

All repos are in the [Cognition-Partner-Workshops](https://github.com/Cognition-Partner-Workshops) GitHub org.

## Repo Duplication Notes

Labs 2 and 3 both originate from `spring-boot-realworld-example-app` (Cluster C1 in [catalog](../../../catalog/repos.md)). They are intentionally separate repos so each lab gets an isolated starting point with no risk of cross-contamination.

---

## Suggested Order

These labs can be completed in any order. If you're not sure where to start:

| Priority | Lab | Why |
|----------|-----|-----|
| 1st | **Lab 3 — CVE Remediation** | Fastest visible results — scan output + remediation in ~30 min |
| 2nd | **Lab 2 — Framework Upgrade** | Broadly applicable — most teams have upgrade backlogs |
| 3rd | **Lab 1 — COBOL → Java** | Impressive but niche — great if you have mainframe legacy |
| 4th | **Lab 4 — Teradata → Snowflake** | Specialized — most relevant if you have DW migration needs |

---

## Devin Features Checklist

Track how many Devin features you exercise across all four labs:

- [ ] Submit a Devin session prompt
- [ ] Use AskDevin to explore a codebase before starting a session
- [ ] Start a Devin session directly from an AskDevin conversation
- [ ] Explore a repo with DeepWiki
- [ ] Steer Devin mid-session with in-chat feedback
- [ ] Run multiple Devin sessions in parallel
- [ ] Review a Devin-generated PR
- [ ] Create a Knowledge item to guide future sessions
- [ ] Review Session Insights (ACU consumption, timeline)
- [ ] Create a Playbook for a repeatable workflow

---

## Related Resources

| Resource | Link |
|----------|------|
| Live workshop (60 min) | [README.md](README.md) |
| Module catalog | [modules/](../../../modules/) |
| Devin Features Appendix | [modules/devin-features](../../../modules/devin-features/README.md) |
