# Workshop: Enterprise Modernization & Data Migration — Hands-On with Devin

## Event Details

| | |
|---|---|
| **Date** | 2026-06-16 |
| **Location** | TBD |
| **Duration** | ~3.5 hours |
| **Audience** | Technical engineers experiencing Devin hands-on |
| **Tracks** | Single progressive track: Secure → Modernize → Migrate → Decompose |
| **Event Site** | TBD |

## Workshop Overview

This is a hands-on workshop covering four high-impact enterprise engineering use cases with Devin. The labs are structured as a progressive ramp — starting with security remediation, moving through legacy COBOL modernization and SAS-to-Snowflake data migration, and finishing with monolith-to-microservices extraction. By the end, participants will have used Devin to remediate known CVEs, translate COBOL to Java, migrate SAS analytics to Python/Snowflake, and decompose a monolith into independently deployable services.

Each lab targets a different repository and a different Devin capability — dependency vulnerability remediation, cross-language legacy code comprehension, data-layer translation, and architectural decomposition — so participants see the breadth of what Devin can do across an enterprise engineering portfolio.

> **Note:** This workshop runs against an external GitHub organization. The repos listed below will be available in your organization's GitHub workspace.

## Getting the Most from This Workshop

> **Devin works autonomously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Each lab has a "Paste into Devin" step and a separate "Review & Give Feedback" step. Kick off the session first, then use the wait time for Ask Devin research — Devin will keep working in the background.
- **Overlap sessions.** Kick off Lab 2's Devin session while reviewing Lab 1's PR. Devin works in the background — there's no reason to wait. This is how teams use Devin in production.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem first so Devin can execute with less back-and-forth.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments directly and Devin will wake up and address them — this is the core workflow for iterating with Devin in production.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item during a session, accept it — this is how teams build a shared context layer that makes Devin smarter over time.

### Quick Start (experienced attendees)

Already comfortable with Devin basics? Jump straight to the labs:

1. Pick a lab: [Lab 1 (Security)](#lab-1), [Lab 2 (COBOL→Java)](#lab-2), [Lab 3 (SAS→Snowflake)](#lab-3), or [Lab 4 (Microservice Extraction)](#lab-4)
2. Copy the prompt from the lab and paste it into a new Devin session
3. While Devin works, try the Ask Devin prompts to explore the codebase
4. Review the PR when Devin finishes, leave comments, and iterate

---

## Table of Contents

- [Agenda](#agenda)
- [Lab 1 — Security Remediation: Dependency CVE Fix](#lab-1)
- [Lab 2 — COBOL to Java Modernization](#lab-2)
- [Lab 3 — SAS to Snowflake Data Migration](#lab-3)
- [Lab 4 — Monolith-to-Microservices Extraction](#lab-4)
- [Follow-On Activities](#follow-on-activities)
- [Known Limitations](#known-limitations)
- [Repos Required](#repos-required)
- [Devin Features Checklist](#devin-features-checklist)

---

## Agenda

| Time | Activity | Lab |
|------|----------|-----|
| 0:00 | Welcome, Devin overview, platform walkthrough | — |
| 0:15 | **Kick off Labs 1–4** — paste all prompts back-to-back (~10 min) | [Labs 1](#lab-1)–[4](#lab-4) |
| 0:25 | **While Devin works:** Ask Devin prompts + DeepWiki exploration | — |
| 0:45 | **Review first PRs arriving**, leave comments, iterate with Devin | — |
| 1:15 | Break | — |
| 1:30 | Continue reviewing PRs + second-round feedback | — |
| 2:00 | Group discussion — compare results, Q&A on labs | — |
| 2:15 | Break | — |
| 2:30 | **Follow-on activities** (participants choose) | [Follow-On](#follow-on-activities) |
| 3:15 | Wrap-up, showcase results, Q&A | — |

> **Timing note:** Kick off all four labs in the first 10 minutes — each is a copy-paste prompt (~2 min each). While Devin works on all four simultaneously, use Ask Devin to explore the codebases. PRs start arriving around 0:30–0:45. The bulk of hands-on time (0:45–2:00) is spent reviewing PRs, leaving comments, and watching Devin iterate. By wrap-up, most participants will have 4 PRs to showcase.

---

<a id="lab-1"></a>

## Lab 1 — Security Remediation: Dependency CVE Fix (15 min)

**Value driver:** *Devin identifies known CVEs in project dependencies, upgrades to patched versions, fixes breaking API changes, and documents remediation — compressing a manual security review into a single session.*

- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Modules:** [Remediate Vulnerabilities](../../../modules/security/remediate-vulnerabilities.md), [Upgrade Dependencies](../../../modules/security/upgrade-dependencies.md)

This Spring Boot 2.6.3 application ships with known CVEs including Spring4Shell (CVSS 9.8), SnakeYAML unsafe deserialization (CVSS 9.8), and multiple Spring Security bypasses. OWASP Dependency-Check and SonarQube are pre-configured as Gradle plugins.

### Paste into Devin

```
Remediate the critical dependency CVEs in
uc-cve-remediation-regulatory-compliance.

1. **Identify known CVEs:** This Spring Boot 2.6.3 app has
   Spring4Shell (CVE-2022-22965, CVSS 9.8), SnakeYAML
   unsafe deserialization (CVE-2022-1471, CVSS 9.8), and
   sqlite-jdbc vulnerabilities.

2. **Remediate:** Upgrade Spring Boot from 2.6.3 to 2.7.18,
   SnakeYAML from 1.29 to 2.0+, and sqlite-jdbc from
   3.36.0.3 to 3.42+. Fix any breaking API changes from
   the upgrades and ensure `./gradlew build` passes.

3. **Document:** Create `SECURITY_REMEDIATION.md` with a
   findings table (CVE ID, severity, old version, new
   version, status) and a summary of what was fixed.
```

### While Devin works: try Ask Devin

- *"What are the known CVEs in uc-cve-remediation-regulatory-compliance's dependencies? Which ones are CRITICAL severity?"*
- *"What's the safest upgrade path for Spring Boot 2.6.3 — should we go to 2.7.x first or jump straight to 3.x?"*
- *"Beyond dependency CVEs, are there any code-level security issues in this repo (SQL injection, insecure deserialization, hardcoded credentials)?"*

### Review the PR

When Devin opens a PR:
- Did Devin address both CRITICAL and HIGH findings?
- Does `SECURITY_REMEDIATION.md` show before/after evidence for each CVE?
- **Leave a comment:** *"Add a GitHub Actions workflow that runs OWASP Dependency-Check on every PR and fails on CVSS >= 7.0"* — watch Devin add shift-left security to the repo
- **Leave a comment:** *"Also add SBOM generation in CycloneDX format to the CI pipeline"*

### Key Takeaways

- **"Known CVE → targeted fix"** — Devin identifies which dependencies have published CVEs, upgrades them, and fixes breaking API changes in a single pass
- **"Shift left via PR feedback"** — attendees ask Devin to add CI scanning as a PR comment, showing how security automation can be added iteratively
- **"Evidence-based compliance"** — the `SECURITY_REMEDIATION.md` provides auditable proof that remediation was effective

### Target Outcomes (any of these count)

- Critical CVEs remediated (Spring4Shell, SnakeYAML, sqlite-jdbc)
- `SECURITY_REMEDIATION.md` with findings table
- Build passing after dependency upgrades
- GitHub Actions CI workflow added via PR comment follow-up
- PR with remediations and Devin's responses to review comments

---

<a id="lab-2"></a>

## Lab 2 — COBOL to Java Modernization (15 min)

**Value driver:** *Devin reads a real mainframe COBOL application that most modern engineers can't understand, reverse-engineers the data model and business logic, and translates a selected program to Java 17+ with JUnit parity tests — compressing weeks of SME interviews into a single session.*

- **Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- **Modules:** [COBOL to Java](../../../modules/migration-modernization/cobol-to-java.md), [COBOL System Understanding](../../../modules/migration-modernization/cobol-system-understanding.md)

The CardDemo application is a real mainframe credit card management system with 29 COBOL programs, 30 copybooks defining record layouts (accounts, customers, cards, transactions), and 17 BMS screen maps for 3270 terminal interactions. Participants will ask Devin to analyze the COBOL estate and translate a batch processing program to Java.

### Paste into Devin

```
Analyze the COBOL program CBACT01C.cbl in
uc-legacy-modernization-cobol-to-java. This is an account
file batch processing program from a mainframe credit card
management system (CardDemo).

1. **Understand the COBOL:** Read CBACT01C.cbl in app/cbl/
   and the copybooks it references in app/cpy/ (especially
   CVACT01Y.cpy for account record layout). Document the
   business logic: what data it reads, what processing it
   performs, and what outputs it produces.

2. **Translate to Java 17+:** Rewrite the program as a
   modern Java application using records for data
   structures, proper date/decimal handling for COBOL
   COMP-3 and PIC types, and standard file I/O. Preserve
   the same business logic and processing flow.

3. **Add parity tests:** Create JUnit tests that verify the
   Java version produces identical results to the COBOL
   version for sample inputs. Use the data files in
   app/data/ as test fixtures.

4. **Document decisions:** Create MIGRATION_NOTES.md
   covering: COBOL-to-Java type mappings, business rules
   extracted, and any ambiguities in the original code.
```

### While Devin works: try Ask Devin

- *"What are the main business entities in uc-legacy-modernization-cobol-to-java? What do the copybooks in app/cpy/ define?"*
- *"How does CBACT01C.cbl handle error conditions? What happens when a record can't be processed?"*
- *"What's the data flow through the CardDemo batch pipeline? Which JCL jobs in app/jcl/ orchestrate the COBOL programs?"*

### Review the PR

When Devin opens a PR:
- Do the Java data structures correctly map COBOL PIC clauses (e.g., `PIC S9(10)V99` → `BigDecimal`)?
- Do the JUnit tests use the actual data files from `app/data/` as test fixtures?
- **Leave a comment:** *"Also translate CBACT02C.cbl (account data processing) using the same patterns"* — watch Devin apply the same migration methodology to a second program
- **Leave a comment:** *"Add a DATA_DICTIONARY.md extracting all field definitions from the copybooks in app/cpy/"*

### Key Takeaways

- **"Devin reads what you can't"** — COBOL is unreadable to most modern engineers, but Devin comprehends PIC clauses, PERFORM statements, copybook layouts, and batch processing patterns
- **"Parity testing proves correctness"** — JUnit tests validate that the Java translation produces identical outputs, building confidence in the migration
- **"Migration documentation as a deliverable"** — `MIGRATION_NOTES.md` captures translation decisions that would otherwise live only in a developer's head
- **"Parallel migration via child sessions"** — in production, a parent session can spawn one child per COBOL program, migrating an entire estate in parallel

### Target Outcomes (any of these count)

- Java 17+ application replacing `CBACT01C.cbl` with modern idioms
- JUnit parity tests verifying functional equivalence
- `MIGRATION_NOTES.md` documenting COBOL-to-Java type mappings and business rules
- PR with Java code, tests, and migration documentation
- Second program migrated via PR comment follow-up

---

<a id="lab-3"></a>

## Lab 3 — SAS to Snowflake Data Migration (15 min)

**Value driver:** *Devin reads legacy SAS analytics code and sample datasets without requiring a SAS license, translates macro logic to Python/pandas, and generates Snowflake-compatible DDL and loading scripts — a non-invasive migration approach that works from static analysis alone.*

- **Repositories:** [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics) + [uc-data-migration-sas-to-snowflake](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-snowflake)
- **Module:** [SAS to Python/Snowflake](../../../modules/data-engineering/sas-to-python-snowflake.md)

The SAS codebase contains 90+ macros covering data export, transformation, deduplication, and formatting operations. The Snowflake migration repo includes sample banking datasets (CUST_ACCOUNTS, DAILY_BALANCE, MONTHLY_AMB) in both SAS7BDAT and CSV formats, lineage metadata, and two migration scenarios with before/after data snapshots.

### Paste into Devin

**Session A — SAS macro translation (ts-sas-legacy-analytics):**

```
Analyze the SAS macros in ts-sas-legacy-analytics/Macro/ —
focus on the data transformation macros: transpose.sas,
subset_data.sas, compare.sas, dedup_string.sas,
dedup_mstring.sas, and the export family (export_csv.sas,
export_xlsx.sas, export_dbms.sas). For each macro, translate
the SAS logic into equivalent Python functions using pandas.
Preserve the same function signatures (input dataset,
parameters, output dataset). Create pytest tests that
validate the Python functions produce the same results as
the SAS originals for sample inputs. Document each
translation decision in a SAS_TO_PYTHON_TRANSLATION.md.
```

**Session B — Snowflake DDL + loading scripts (uc-data-migration-sas-to-snowflake):**

```
Analyze the sample datasets in
uc-data-migration-sas-to-snowflake/sample_data/ — examine
the CSV files (CUST_ACCOUNTS.csv, DAILY_BALANCE.csv,
MONTHLY_AMB.csv) and the two migration scenarios in
Scenario1/ and Scenario2/. Create Snowflake-compatible DDL
for each dataset with proper column types, constraints, and
clustering keys. Then write Python scripts that: (1) read
the SAS7BDAT files using the sas7bdat or pyreadstat library,
(2) apply any necessary data type conversions (dates,
decimals, string encoding), (3) generate Snowflake COPY INTO
statements for bulk loading, and (4) create validation
queries comparing row counts and checksums between source and
target. Include a SAS_TO_SNOWFLAKE_MIGRATION.md documenting
the schema mapping and loading strategy.
```

### While Devin works: try Ask Devin

- *"What SAS-specific features are used in the Macro/ directory of ts-sas-legacy-analytics? Which macros use SAS-only constructs like PROC TRANSPOSE or BY-group processing?"*
- *"What are the schema differences between Scenario1 and Scenario2 in uc-data-migration-sas-to-snowflake/sample_data/? What data changes do the scenarios represent?"*
- *"What SAS-specific data types and formats in the SAS7BDAT files need special handling when loading into Snowflake? How should SAS date values (days since 1960-01-01) be converted?"*

### Review the PR(s)

When Devin opens PRs (you may see 2 — one per repo):
- Do the Python functions handle SAS-specific edge cases (missing values, empty datasets)?
- Are the Snowflake DDL types correct for the SAS source data?
- **Leave a comment on Session A:** *"Add type hints and docstrings to all converted functions, following NumPy docstring conventions"*
- **Leave a comment on Session B:** *"Add an incremental load strategy that handles Scenario1-to-Scenario2 as a delta migration instead of full reload"*

### Key Takeaways

- **"Non-invasive analysis"** — Devin reads SAS macro source files and SAS7BDAT datasets to understand transformation logic — no SAS license, no running SAS environment needed
- **"Construct-level mapping"** — targeted SAS patterns (PROC TRANSPOSE, BY-group processing, macro variable resolution) map to documented Python equivalents
- **"SAS date handling"** — SAS stores dates as days since January 1, 1960 — Devin handles this conversion to standard date types automatically
- **"Parallel conversion potential"** — with 90+ macros, a child-session pattern can convert groups of related macros in parallel

### Target Outcomes (any of these count)

- Python/pandas equivalents of SAS data transformation macros with pytest validation
- `SAS_TO_PYTHON_TRANSLATION.md` documenting translation decisions
- Snowflake DDL for banking datasets with proper column types
- Python loading scripts with SAS7BDAT reading and data type conversion
- Validation queries comparing source and target
- `SAS_TO_SNOWFLAKE_MIGRATION.md` documenting schema mapping
- PR(s) with converted code and migration documentation

---

<a id="lab-4"></a>

## Lab 4 — Monolith-to-Microservices Extraction (15 min)

**Value driver:** *Devin analyzes a monolith's domain boundaries, documents extraction decisions, extracts a bounded context into a standalone service, and wires up cross-service communication — typically in a single session.*

- **Repository:** [uc-framework-upgrade-monolith-to-microservices](https://github.com/Cognition-Partner-Workshops/uc-framework-upgrade-monolith-to-microservices)
- **Modules:** [Containerization & Microservice Extraction](../../../modules/migration-modernization/containerization-microservice-extraction.md)

This is a Spring Boot 2.6.3 / Java 11 monolith implementing the RealWorld blogging platform (Conduit) with 4 domain contexts: articles/tags, comments, favorites, and users/profiles. It has REST and GraphQL (DGS) APIs, MyBatis persistence with SQLite, Flyway migrations, and a Next.js frontend. The repo has pre-cached Gradle dependencies for fast builds. Participants will ask Devin to analyze domain boundaries, document extraction decisions, and extract a bounded context into a standalone service.

### Paste into Devin

```
Extract the Article bounded context from
uc-framework-upgrade-monolith-to-microservices — a Spring
Boot 2.6.3 monolith with REST and GraphQL APIs, MyBatis
persistence, and a Next.js frontend.

1. **Read the codebase:** Identify the 4 bounded contexts
   (articles/tags, comments, favorites, users/profiles)
   and their coupling points.

2. **Document extraction decisions:** Create
   docs/EXTRACTION_DECISIONS.md documenting: which domain
   objects move to the Article service (articles, tags),
   what stays (comments, favorites, users/profiles),
   coupling points between domains, and the cross-service
   communication strategy.

3. **Extract:** Create article-service/ — a standalone
   Spring Boot service with its own build configuration,
   database migrations, and MyBatis persistence. Include
   articles and tags. Replace direct User domain calls
   with a REST client and DTOs. ./gradlew build
   -x jacocoTestCoverageVerification must pass for both
   services.
```

### While Devin works: try Ask Devin

- *"What are the domain boundaries in uc-framework-upgrade-monolith-to-microservices? Which packages would form a natural microservice?"*
- *"What coupling exists between the Article domain and the User domain? How would you handle cross-service queries after extraction?"*
- *"What are the risks of extracting the Article bounded context? What shared infrastructure (security, database, Flyway) needs to be duplicated?"*

### Review the PR

When Devin opens a PR:
- Does `EXTRACTION_DECISIONS.md` clearly justify which domain objects moved and which stayed?
- Does the article-service have its own independent build that compiles?
- Is cross-service communication implemented via REST client (not shared database queries)?
- **Leave a comment:** *"Add a docker-compose.yml that runs both the main app and article-service with container networking"* — watch Devin containerize the decomposed system
- **Leave a comment:** *"Add a circuit breaker pattern to the REST client calls between services — what happens if article-service is down?"*

### Key Takeaways

- **"Analysis before extraction"** — Devin reads the monolith, identifies bounded contexts, and writes a reviewable extraction decisions document before cutting any code
- **"Existing tests as a safety net"** — the monolith's test suite verifies that extraction didn't break functionality
- **"Service contracts, not shared databases"** — the extracted service communicates via REST DTOs, not direct database access — the right pattern for real microservices
- **"Containerization via PR feedback"** — attendees ask Devin to add Docker Compose as a follow-up, showing iterative architecture work through PR comments

### Target Outcomes (any of these count)

- `docs/EXTRACTION_DECISIONS.md` documenting domain boundary analysis and trade-offs
- Standalone `article-service/` with its own build configuration, migrations, and persistence
- REST client + DTOs for cross-service communication
- `./gradlew build -x jacocoTestCoverageVerification` passing for both services
- `docker-compose.yml` added via PR comment follow-up
- PR with extraction artifacts and Devin's responses to review comments

---

## Follow-On Activities

Participants who want to keep exploring after the main labs can try these additional use cases. Each is self-contained with a copy-pasteable prompt.

### Activity 1: Java Framework Upgrade (Spring Boot 2.x → 3.x)

- **Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)
- **Module:** [Framework Upgrade](../../../modules/migration-modernization/framework-upgrade.md)
- **Shows:** Devin handling a major framework upgrade — javax→jakarta namespace migration, Spring Security 6 lambda DSL changes, and dependency compatibility fixes across a monolith with 80% test coverage

#### Paste into Devin

```
Upgrade uc-spring-boot-upgrade-microservice-extraction from
Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2.

Handle the full upgrade checklist:
1. Update build.gradle — Spring Boot plugin, Java target
   compatibility, and dependency versions
2. Migrate javax.* imports to jakarta.* across all source
   files
3. Update Spring Security configuration to the new lambda
   DSL (Spring Security 6.x)
4. Fix any deprecated MyBatis or DGS (GraphQL) APIs
5. Update Flyway configuration for Spring Boot 3
   compatibility
6. Run ./gradlew build and ./gradlew test — fix any
   compilation errors or test failures
7. Document the breaking changes encountered and how each
   was resolved in the PR description
```

---

### Activity 2: COBOL Estate Discovery (Full Reverse Engineering)

- **Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- **Module:** [COBOL System Understanding](../../../modules/migration-modernization/cobol-system-understanding.md)
- **Shows:** Devin reverse-engineering a complete COBOL estate — application inventory, data dictionary, dependency map, and hotspot report — the discovery phase that normally takes weeks of SME interviews

#### Paste into Devin

```
Analyze the entire COBOL estate in
uc-legacy-modernization-cobol-to-java. Produce the following
artifacts:

1. APPLICATION_INVENTORY.md — list every program in app/cbl/
   and sub-application directories with: filename, purpose,
   classification (batch/online), key I/O operations, and
   copybooks referenced.
2. DATA_DICTIONARY.md — for every copybook in app/cpy/,
   extract: field names, PIC clauses, data types, business
   meaning, and validation rules.
3. DEPENDENCY_MAP.md — build a call graph showing which
   programs CALL or PERFORM other programs. Map dataset
   lineage from JCL jobs.
4. HOTSPOT_REPORT.md — rank the top 10 programs by
   complexity and recommend which to modernize first.
```

---

### Activity 3: SAS → dbt/Databricks Assessment

- **Repositories:** [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics) + [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks)
- **Module:** [SAS Migration Analysis](../../../modules/data-engineering/sas-migration-analysis.md)
- **Shows:** Devin performing broad estate discovery on a SAS codebase and mapping constructs to dbt/Databricks equivalents

#### Paste into Devin

```
Analyze the SAS codebase in ts-sas-legacy-analytics to
produce a comprehensive migration assessment. Start with
Config/autoexec.sas to understand the library assignments
and database connections. Then analyze all programs in
Programs/Banking/, Programs/Insurance/, and Programs/Reports/
— for each, document: (1) data sources read, (2) outputs
produced, (3) macro dependencies, (4) SAS constructs used,
and (5) a complexity rating. Produce a
SAS_MIGRATION_ASSESSMENT.md with: artifact inventory, data
lineage diagram, macro dependency graph, complexity scores,
risk areas, and a recommended migration sequence targeting
dbt on Databricks.
```

---

### Activity 4: Angular to React UI Migration

- **Repository:** [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular)
- **Module:** [Framework Upgrade](../../../modules/migration-modernization/framework-upgrade.md)
- **Shows:** Devin mapping a backend API contract, writing a migration test suite as a safety net, then rewriting an Angular frontend in React — using parent-child session orchestration to parallelize the work

#### Paste into Devin

```
Migrate the petclinic-angular frontend from Angular to React
using a test-driven approach with parallel child sessions.
Scope to the three interconnected modules: owners, pets,
and visits.

1. **Map the API contract:** Read the Angular service files
   and TypeScript interfaces for owners, pets, and visits.
   Produce docs/API_CONTRACT.md documenting each REST
   endpoint. Base URL is
   http://localhost:9966/petclinic/api/. Commit this to a
   new branch.

2. **Spin up two child sessions in parallel:**

   Child A — "In petclinic-angular, create React Testing
   Library + MSW tests in react-frontend/src/__tests__/
   validating owner CRUD, pet management, and visit
   creation against docs/API_CONTRACT.md."

   Child B — "In petclinic-angular, migrate the owners,
   pets, and visits modules to React 18+/TypeScript/Vite
   in react-frontend/. Preserve routing, form validation,
   error handling. npm run build must pass."

3. **When both children finish:** Merge both PRs into a
   combined branch. Run the tests from Child A against
   the React app from Child B. Report which tests pass
   and which fail.
```

---

### Activity 5: OtterWorks Polyglot Microservices

- **Repository:** [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks)
- **Shows:** Hands-on with a polyglot microservices platform (Rust, Go, Java, Kotlin, Python, Ruby, Node.js, TypeScript). Choose from security sprints, incident investigation, framework upgrades, observability, or language translation tasks.
- **Workshop Reference:** See the [OtterWorks workshop](../../../workshops/otterworks/README.md) for the full menu of 9 labs across 3 tracks.

#### Paste into Devin (pick one)

**Security sprint:**
```
Run a security audit on the otterworks repository. Start
with the dependency manifests in each service directory:
services/collab-service/package.json (Node.js),
services/search-service/requirements.txt (Python),
services/admin-service/Gemfile (Ruby), and the Java/Kotlin
services' build.gradle files. Check .trivyignore for
suppressed CVEs. Scan for hardcoded secrets or credentials,
check for insecure API endpoints, and produce a
SECURITY_AUDIT.md with findings prioritized by severity.
```

**Incident investigation:**
```
Investigate the recent performance degradation in otterworks.
Start with the service architecture in docker-compose.yml
and the observability config in observability/. Analyze the
API gateway in services/gateway-service/ and the file
service in services/file-service/. Trace cross-service
request flows using the OpenTelemetry instrumentation, and
produce an INCIDENT_REPORT.md with root cause analysis and
recommended fixes.
```

---

## Known Limitations

A few things to be aware of as you work through the labs:

- **Lab 1 (Security):** The repo uses Gradle and Spring Boot 2.6.3. The `./gradlew build` step downloads dependencies on first run (~1-2 min).
- **Lab 2 (COBOL→Java):** The repo has no build system (COBOL is compiled on mainframes) — Devin will create a Java project structure from scratch. There is no single "right answer" — different participants will produce different migrations.
- **Lab 3 (SAS→Snowflake):** This is a non-invasive analysis — no SAS license or Snowflake account is needed. Devin reads source files and sample datasets statically. Two separate sessions (A and B) target different repos.
- **Lab 4 (Microservice Extraction):** The monolith has a JaCoCo coverage gate. The prompt uses `-x jacocoTestCoverageVerification` to skip coverage checks after extraction. This repo has pre-cached Gradle dependencies for faster builds.
- **Each lab uses a different repository.** You'll work across five separate codebases (Lab 3 uses two repos) rather than a single unified application.

---

## Repos Required

| Lab | Repository | Purpose |
|-----|-----------|---------|
| Lab 1 | [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) | Spring Boot 2.6.3 with known CVEs |
| Lab 2 | [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java) | COBOL CardDemo application — migration source |
| Lab 3 | [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics) | Legacy SAS analytics codebase |
| Lab 3 | [uc-data-migration-sas-to-snowflake](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-snowflake) | SAS-to-Snowflake migration toolkit |
| Lab 4 | [uc-framework-upgrade-monolith-to-microservices](https://github.com/Cognition-Partner-Workshops/uc-framework-upgrade-monolith-to-microservices) | Spring Boot 2.6.3 monolith — extraction source (pre-cached) |

**Follow-on activities (optional):**
- [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot 2.6.3 monolith (Activity 1)
- [ts-cobol-carddemo](https://github.com/Cognition-Partner-Workshops/ts-cobol-carddemo) — COBOL CardDemo (fork for COBOL-specific labs)
- [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks) — dbt/Databricks target for SAS migration
- [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular) — Angular 16 frontend (Activity 4)
- [ts-java-spring-petclinic-rest-api](https://github.com/Cognition-Partner-Workshops/ts-java-spring-petclinic-rest-api) — REST API backend (Activity 4 reference)
- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — Polyglot microservices platform (Activity 5)
- [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js full-stack app

## Devin Features Checklist

Track your progress throughout the session — try to discover as many Devin capabilities as you can.

| Feature | Lab(s) Where You'll See It |
|---------|---------------------------|
| Dependency vulnerability remediation | Lab 1 |
| Dependency management | Lab 1 |
| Cross-language code comprehension (COBOL) | Lab 2 |
| Code generation (Java from COBOL) | Lab 2 |
| Parity test generation | Lab 2 |
| Legacy data format analysis (SAS) | Lab 3 |
| Cross-language translation (SAS → Python/SQL) | Lab 3 |
| Domain boundary analysis | Lab 4 |
| Microservice extraction | Lab 4 |
| Build & test verification | All labs |
| PR creation with documentation | All labs |
| PR feedback loop (comment → iterate) | All labs |
| Ask Devin research | All labs |
| DeepWiki exploration | All labs |
| Parallel sessions | Labs 1+2 overlap, Lab 3 dual sessions |
| Knowledge items | Labs 2, 4 |
| CI/CD workflow generation (via PR comment) | Lab 1 |
| Migration documentation | Labs 2, 3 |
| Parent-child session orchestration | Activity 4 |
| Polyglot microservices analysis | Activity 5 |

## Context

This workshop combines modules from the [Security & Compliance](../../../workshops/security-compliance/README.md), [COBOL Modernization](../../../workshops/cobol-modernization/README.md), [Data Source Migration](../../../workshops/data-source-migration/README.md), and [Legacy Modernization](../../../workshops/legacy-modernization/README.md) workshops into a single half-day event covering four enterprise engineering domains: security, mainframe modernization, data migration, and architecture decomposition.

For deeper dives into individual domains, see the linked workshops above.
