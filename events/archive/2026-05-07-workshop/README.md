# Workshop: Java/Spring Boot — Hands-On with Devin

## Event Details

| | |
|---|---|
| **Date** | 2026-05-07 |
| **Location** | Virtual |
| **Host Organization** | *(customer)* |
| **Duration** | 2 hours |
| **Audience** | Development teams experiencing Devin hands-on for the first time |
| **Tracks** | Single progressive track: Analyze → Generate → Detect & Migrate |
| **Event Site** | TBD |

## Workshop Overview

This is a hands-on workshop for teams getting their first experience with Devin. The labs are structured as a progressive ramp — starting with low-risk analysis, building to code generation, and finishing with data quality and migration. By the end, participants will have used Devin to analyze a codebase, generate a microservice from an API spec, detect data anomalies, and migrate a legacy data layer to a modern schema.

The workshop uses Java/Spring Boot repositories throughout. Additional use cases (COBOL copybook config generation, security remediation, payment gap analysis) are provided as post-session exercises participants can try on their own.

> **Note:** This workshop runs against an external GitHub organization. All repos listed below must be forked or cloned into the target org before the event.

## Getting the Most from This Workshop

> **Devin works autonomously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Each lab has a "Paste into Devin" step and a separate "Review & Give Feedback" step. Kick off the session first, then use the wait time for Ask Devin research — Devin will keep working in the background.
- **Overlap sessions.** Kick off Lab 2's Devin session while reviewing Lab 1's PR. Devin works in the background — there's no reason to wait. This is how teams use Devin in production.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem first so Devin can execute with less back-and-forth.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments directly and Devin will wake up and address them — this is the core workflow for iterating with Devin in production.

---

## Agenda

| Time | Activity | Lab |
|------|----------|-----|
| 0:00 | Welcome, Devin overview, platform walkthrough | — |
| 0:15 | **Lab 1:** Gap Analysis on Banking Microservices | Lab 1 |
| 0:45 | **Lab 2:** API-Spec-Driven Microservice Generation | Lab 2 |
| 1:15 | **Lab 3a:** Data Anomaly Detection | Lab 3a |
| 1:30 | **Lab 3b:** Legacy CDW to Databricks Migration | Lab 3b |
| 1:45 | Wrap-up, showcase results, Q&A | — |

> **Pacing tip for facilitators:** Each lab is designed so participants kick off the Devin session in the first 5 minutes, then use Ask Devin / DeepWiki while waiting. When transitioning to the next lab, participants should start the new session immediately — they can review earlier PRs during any downtime. Lab 3 has two parts (3a and 3b) — participants should kick off both sessions back-to-back, then use the remaining time to review PRs from all labs. By the wrap-up, participants will have 4 Devin sessions running or completed.

---

## Lab 1 — Gap Analysis on Banking Microservices (30 min)

**Value driver:** *Devin reads an entire codebase and produces structured technical documentation — the kind of assessment that normally takes a senior engineer a week.*

- **Repository:** [ts-java-spring-boot-internet-banking-microservices](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking-microservices)
- **Modules:** [API Design Review](../../../modules/architecture-design/api-design-review.md), [Dependency Graph Analysis](../../../modules/architecture-design/dependency-graph-analysis.md)

This is your first Devin session. The task is pure analysis — Devin reads code and produces documents, no risky code changes. This lets you focus on learning the platform workflow: paste a prompt, watch Devin plan and execute, review the PR.

The repo is a Java 21 / Spring Boot 3.2.4 banking application with 6 microservices: core-banking, fund-transfer, user-service, utility-payment, API gateway, and service registry. It uses Keycloak auth, RabbitMQ messaging, and Zipkin tracing.

### Paste into Devin

```
Perform a comprehensive technical assessment of ts-java-spring-boot-internet-banking-microservices. This is a Java 21 / Spring Boot 3.2.4 banking application with 6 microservices. Produce the following deliverables:

1. **Application Knowledge Base** (`docs/KNOWLEDGE_BASE.md`):
   - Architecture overview (services, communication patterns, infrastructure components)
   - Data model documentation (entities, relationships, key fields per service)
   - API surface map (all endpoints across all services with methods, request/response shapes)
   - Key business logic inventory (fund transfer rules, payment processing, user management)
   - Integration points (Keycloak, RabbitMQ, Zipkin, database connections)
   - Build and deployment pipeline summary (Docker Compose, Gradle)

2. **Engineering Standards Gap Analysis** (`docs/GAP_ANALYSIS.md`):
   Compare the codebase against these engineering best practices and document gaps:
   - **Code organization:** Consistent project structure across services, separation of concerns, shared library usage
   - **Error handling:** Centralized error handling, consistent error response format, proper HTTP status codes
   - **Testing:** Unit test coverage, integration tests, contract tests between services
   - **Security:** Input validation, authentication/authorization patterns, secrets management, dependency vulnerabilities
   - **API design:** RESTful conventions, pagination, filtering, versioning, OpenAPI documentation
   - **Observability:** Logging consistency, health checks, metrics endpoints, distributed tracing coverage
   - **Resilience:** Circuit breakers, retry policies, timeout configuration, fallback behavior

   For each gap, rate severity (Critical/High/Medium/Low) and estimate effort to remediate (Small/Medium/Large).

3. **Remediation Roadmap** (`docs/REMEDIATION_ROADMAP.md`):
   Prioritize the gaps into a phased plan: Phase 1 (quick wins), Phase 2 (important), Phase 3 (polish). Include sample Devin prompts for each remediation item.

Open a PR with all three documents.
```

### While Devin works: try Ask Devin

Open **Ask Devin** and explore the repo:
- *"What communication patterns do the banking microservices use? Are there any single points of failure?"*
- *"How does the fund-transfer service validate transactions? Are there any edge cases that could cause incorrect transfers?"*

### Review the PR

When Devin opens a PR:
- Does the knowledge base accurately describe the architecture?
- Are the gap analysis findings legitimate? Would your team agree with the severity ratings?
- **Leave a comment** asking Devin to add a specific gap you care about — watch Devin respond and push a follow-up commit

### Key Takeaways

- **"Instant technical knowledge base"** — Devin reads the entire codebase and produces structured documentation that would take days to write manually
- **"Standards-based gap analysis"** — Devin evaluates the codebase against engineering best practices and quantifies the gaps
- **"Actionable roadmap"** — the remediation plan includes effort estimates and ready-to-use Devin prompts, turning the analysis into immediate next steps

---

## Lab 2 — API-Spec-Driven Microservice Generation (30 min)

**Value driver:** *Give Devin an API specification, get back a complete production-ready Spring Boot microservice with controllers, services, validation, configuration, and 90% test coverage.*

- **Repository:** [ts-java-spring-petclinic-rest-api](https://github.com/Cognition-Partner-Workshops/ts-java-spring-petclinic-rest-api)
- **Modules:** [Containerization & Microservice Extraction](../../../modules/migration-modernization/containerization-microservice-extraction.md)

Now you'll see Devin write real code. The PetClinic REST API repo ships a rich OpenAPI 3.0 specification (2,168 lines, 35 operations, 15 schemas, 8 domain areas). You'll give Devin this spec and have it generate a complete microservice from scratch.

### Paste into Devin

```
Using the OpenAPI specification at `src/main/resources/openapi.yml` in ts-java-spring-petclinic-rest-api as the source of truth, generate a brand new Spring Boot microservice that implements the "Vet" domain (veterinarians and specialties). The new service should be fully standalone — not a modification of the existing monolith.

Generate all components following Spring Boot conventions:
1. **Controller** with proper REST mappings matching the OpenAPI spec, request validation (@Valid), and error handling (@ControllerAdvice)
2. **Service layer** with business logic for vet CRUD, specialty assignment, and search/filtering
3. **DTOs and mappers** — separate request/response DTOs from the JPA entity, with manual mapping or MapStruct
4. **JPA Entities** with audit fields (createdAt, updatedAt) and proper relationship mapping (Vet ↔ Specialty many-to-many)
5. **Repository** with custom query methods for filtering by specialty and name search
6. **Configuration** — application.yml with profiles (dev/test/prod), Flyway migration scripts for the schema
7. **Exception handling** — global handler with RFC 7807 Problem Details responses
8. **JUnit tests** — aim for 90%+ line coverage. Include unit tests for service logic, integration tests for the controller layer (@WebMvcTest), and repository tests (@DataJpaTest)

Use an H2 in-memory database for dev/test. Structure the project in a new `vet-service/` directory. Open a PR.
```

### While Devin works: try Ask Devin

- *"How many domain areas does the PetClinic OpenAPI spec cover? Which domain would be the best candidate for extraction as a standalone microservice and why?"*
- *"What validation rules does the OpenAPI spec define for vet and specialty entities?"*

### Review the PR

When Devin opens a PR:
- Does the generated code follow Spring Boot conventions your team uses?
- Are the tests meaningful, or just boilerplate?
- **Leave a comment** asking Devin to add Swagger UI configuration and a Docker Compose file — watch Devin respond

### Key Takeaways

- **"Spec-to-service in one session"** — Devin generates a fully layered microservice from an API contract, complete with validation, error handling, and database migrations
- **"Test coverage is built in"** — generating tests alongside the code ensures quality from the start, not as an afterthought
- **"Iterative refinement via PR comments"** — the first pass isn't the final pass; PR comments let you steer Devin toward your team's conventions

---

## Lab 3a — Data Anomaly Detection (15 min)

**Value driver:** *Devin analyzes legacy data for quality issues, documents every anomaly with business impact, and implements validation code that prevents them — turning a week of data profiling into a single session.*

- **Repository:** [uc-data-source-migration-legacy-to-modern](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-legacy-to-modern)
- **Module:** [Data Quality & Validation](../../../modules/data-engineering/data-quality-validation.md)

The repo is a Spring Boot 3.2 / Java 17 loan management application that reads from legacy CDW (Corporate Data Warehouse) tables with known data quality issues: all-VARCHAR typing, cryptic column names, no foreign keys, code abbreviations. Devin will analyze the data for anomalies, trace root causes through the code, and implement validation to catch them.

### Paste into Devin

```
Analyze the data layer in uc-data-source-migration-legacy-to-modern for data quality anomalies. This Spring Boot service reads from legacy CDW (Corporate Data Warehouse) tables with known quality issues.

1. **Anomaly Detection:** Examine the legacy seed data in `src/main/resources/data-legacy.sql` and the schema in `src/main/resources/schema-legacy.sql`. Identify anomalies: null values in required fields, date format inconsistencies, invalid status codes, orphaned records (foreign key violations), numeric values stored as strings with parsing risks, and duplicate or near-duplicate records.

2. **Issue Documentation:** For each anomaly found, create a structured entry in `docs/DATA_ANOMALY_REPORT.md` with: title, severity (Critical/High/Medium/Low), affected table and column, example bad records, business impact, and recommended fix.

3. **Root Cause Analysis:** For the top 3 most critical anomalies, trace through the code (LoanService.java, the repository layer, and the column mappings in `data/mappings/column_mappings.md`) to identify where the anomaly would cause a runtime failure or incorrect API response. Document the root cause in `docs/ROOT_CAUSE_ANALYSIS.md`.

4. **Fix:** Implement data validation in the service layer that catches these anomalies at ingestion time — add input validation, type coercion with error handling, and fallback defaults where appropriate. Add tests that verify the validation catches each anomaly type.

Open a PR with the anomaly report, root cause analysis, validation code, and tests.
```

> **Tip:** Kick off this session and immediately move to Lab 3b below — both use the same repo but are independent Devin sessions.

### Key Takeaways

- **"Detect, document, triage, fix"** — Devin follows the full data quality workflow, from finding anomalies to implementing validation that prevents them
- **"Data issues have code root causes"** — Devin traces data quality problems through the application layers to find where they originate

---

## Lab 3b — Legacy CDW to Databricks Migration (15 min)

**Value driver:** *Your loan data is trapped in a legacy Corporate Data Warehouse with all-VARCHAR columns and cryptic names. Devin generates a complete Databricks-ready migration — PySpark ingestion scripts, Delta Lake table definitions, transformation logic, and a data quality validation framework.*

- **Repository:** [uc-data-source-migration-legacy-to-modern](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-legacy-to-modern)
- **Module:** [Data Source Migration](../../../modules/data-engineering/data-source-migration.md)

Same repo, different story. Where Lab 3a finds what's wrong with the data, Lab 3b generates the migration pipeline to move it to a modern platform. Devin will read the legacy schema and column mappings, then produce PySpark scripts, Delta Lake table definitions, and validation code — everything a data engineering team needs to execute the migration in Databricks.

### Paste into Devin

```
Analyze the legacy CDW schema in uc-data-source-migration-legacy-to-modern. This loan management application reads from legacy tables with all-VARCHAR columns, cryptic names (BORR_FST_NM, LN_CURR_BAL, PMT_ESCROW_AMT), no foreign keys, and status code abbreviations (ACT, CLO, DFT, FRB). Review the schema in `src/main/resources/schema-legacy.sql`, the seed data in `src/main/resources/data-legacy.sql`, and the column mappings in `data/mappings/column_mappings.md`.

Generate a complete Databricks/PySpark migration pipeline:

1. **Delta Lake Table Definitions** (`databricks/ddl/`): Create Delta Lake `CREATE TABLE` statements for the modern target schema. Use proper Spark SQL types (DATE, DECIMAL, STRING with constraints), meaningful column names mapped from the legacy cryptic names, and partitioning strategy appropriate for loan data (e.g., partition by status or origination year).

2. **PySpark Ingestion Scripts** (`databricks/ingestion/`): Write PySpark scripts that read from the legacy tables (simulate as CSV/Parquet source files) and transform the data:
   - Parse date strings (MM/DD/YYYY) to DateType
   - Parse amount strings ("285,000") to DecimalType
   - Expand status code abbreviations (ACT→Active, CLO→Closed, DFT→Default, FRB→Forbearance) per the column mappings
   - Split denormalized borrower fields into a separate borrower dimension table
   - Handle nulls, malformed values, and edge cases with logging (don't silently drop records)

3. **Data Quality Framework** (`databricks/quality/`): Create a PySpark-based validation module that runs after ingestion and checks:
   - Row count reconciliation (source vs. target)
   - Null checks on required fields
   - Referential integrity between loan and borrower tables
   - Business rule validation (e.g., loan balance > 0 for active loans, closed date required for closed loans)
   - Generate a `DATA_QUALITY_REPORT.md` summarizing pass/fail results

4. **Migration Runbook** (`docs/DATABRICKS_MIGRATION_RUNBOOK.md`): Document every transformation decision, the mapping from legacy column names to modern names, type conversion choices, partitioning rationale, and the recommended execution order in Databricks.

Open a PR with all generated artifacts.
```

### While Devin works on 3a and 3b: try Ask Devin

- *"What data quality issues are most common in CDW-to-modern migrations? What validation patterns should the ingestion pipeline implement?"*
- *"How does the column mapping in uc-data-source-migration-legacy-to-modern translate legacy column names to modern field names? Which transformations are riskiest?"*
- *"What's the best Delta Lake partitioning strategy for loan data — by status, origination date, or something else?"*

### Review the PRs

When Devin opens PRs for 3a and 3b:
- **Lab 3a:** Are the anomalies legitimate? Check a few against the actual seed data. Does the validation code handle edge cases?
- **Lab 3b:** Do the PySpark transformations correctly handle the type conversions? Is the Delta Lake schema well-designed? Does the data quality framework catch real issues?
- **Leave a comment** on the 3b PR asking Devin to add a Databricks notebook version of the ingestion scripts with markdown cells explaining each transformation step

### Key Takeaways

- **"Legacy to modern platform"** — Devin generates a complete migration pipeline from a legacy CDW to Databricks, including ingestion, transformation, and validation
- **"Cross-language generation"** — Devin reads a Java/SQL application and produces PySpark/Delta Lake artifacts, showing it works across technology boundaries
- **"Two sessions, one repo, two stories"** — Labs 3a and 3b show how different Devin sessions can tackle complementary aspects of the same codebase independently

---

## Post-Session Exercises

Participants who want to keep exploring after the workshop can try these additional use cases on their own. Each is self-contained with a copy-pasteable prompt.

### Exercise A: COBOL Copybook to PySpark/JSON Config Generation

- **Repository:** [aws-mainframe-modernization-carddemo](https://github.com/Cognition-Partner-Workshops/aws-mainframe-modernization-carddemo)
- **Module:** [COBOL Copybook to PySpark/JSON](../../../modules/data-engineering/cobol-copybook-to-pyspark-json.md)
- **Shows:** Devin reading legacy COBOL data definitions and generating modern data engineering artifacts

#### Paste into Devin

```
Analyze the COBOL copybook `app/cpy/CVACT01Y.cpy` in aws-mainframe-modernization-carddemo. This defines the ACCOUNT-RECORD layout (300 bytes) with fields like ACCT-ID (PIC 9(11)), ACCT-CURR-BAL (PIC S9(10)V99), dates (PIC X(10)), and FILLER.

Generate:
1. A PySpark script that reads `app/data/ASCII/acctdata.txt` as a fixed-width file using the copybook-derived schema
2. A JSON schema file describing each field's name, COBOL PIC clause, PySpark type, byte offset, and length
3. Validation output comparing parsed PySpark DataFrame row counts and sample values against the raw feed file

Then repeat for `CUSTREC.cpy` → `custdata.txt` and `CVACT02Y.cpy` → `carddata.txt`.

Open a PR with all generated artifacts and a `COPYBOOK_PARSING_NOTES.md` documenting your type-mapping decisions (e.g., COMP-3 → DecimalType, PIC X → StringType).
```

---

### Exercise B: Automated Security Remediation & Vulnerability Reporting

- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Modules:** [Remediate Vulnerabilities](../../../modules/security/remediate-vulnerabilities.md), [Shift Left Security](../../../modules/security/shift-left-security.md)
- **Shows:** Devin running SAST/SCA analysis, producing an executive security report, and remediating critical findings

#### Paste into Devin

```
Perform a comprehensive security assessment of uc-cve-remediation-regulatory-compliance. Run three types of analysis:

1. **Dependency scanning (SCA):** Run `./gradlew dependencyCheckAnalyze` to identify known CVEs in third-party libraries. Categorize findings by CVSS severity.
2. **Static code analysis (SAST):** Review the source code for security antipatterns — hardcoded credentials, SQL injection risks, insecure deserialization, missing input validation, and unsafe cryptographic usage.
3. **Configuration review:** Check application.properties, build.gradle, and any CI/security configs for misconfigurations — debug mode settings, overly permissive CORS, missing security headers.

Produce a unified `SECURITY_POSTURE_REPORT.md` that includes:
- Executive summary with overall risk rating (Critical / High / Medium / Low)
- Findings table organized by category (SCA, SAST, Configuration) with severity, description, and recommended fix
- A prioritized remediation roadmap — what to fix first and why
- Fix the top 3 most critical findings and re-verify

Open a PR with the report and the fixes.
```

---

### Exercise C: Payment Payload Gap Analysis & Business Capability Decomposition

- **Repository:** [ts-java-spring-boot-internet-banking-microservices](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking-microservices)
- **Modules:** [API Design Review](../../../modules/architecture-design/api-design-review.md), [Dependency Graph Analysis](../../../modules/architecture-design/dependency-graph-analysis.md)
- **Shows:** Devin analyzing payment domain code against industry standards and recommending a service decomposition strategy

#### Paste into Devin

```
Analyze ts-java-spring-boot-internet-banking-microservices and produce a payment domain assessment. This banking application has 6 microservices including fund-transfer and utility-payment services.

Deliverables:

1. **Payment Payload Gap Analysis** (`docs/PAYMENT_GAP_ANALYSIS.md`):
   - Document the current payment payload structure (fund transfer request/response, utility payment request/response)
   - Compare against ISO 20022 payment message standards (pain.001, pain.002, pacs.008) — identify which standard fields are missing, which are present but named differently, and which are extra
   - Assess payment validation rules: are amount limits enforced? Currency handling? Duplicate detection? Idempotency?
   - Rate each gap by severity and business risk

2. **Business Capability Map** (`docs/BUSINESS_CAPABILITY_MAP.md`):
   - Map each microservice to business capabilities (e.g., Account Management, Fund Transfer, Payment Processing, User Administration, Service Discovery, API Routing)
   - Identify capability overlaps (same business logic in multiple services)
   - Identify capability gaps (business functions not covered by any service)
   - Assess service boundaries: are they aligned to business capabilities or to technical layers?

3. **Decomposition Recommendations** (`docs/DECOMPOSITION_RECOMMENDATIONS.md`):
   - Based on the capability map, recommend which services should be split, merged, or restructured
   - For each recommendation, explain the business justification and the technical approach
   - Include a dependency diagram (in text/markdown) showing current vs. proposed service interactions
   - Prioritize recommendations by impact and feasibility

Open a PR with all three documents.
```

---

### Exercise D: SAS to Python/Snowflake Migration

- **Repositories:** [ts-sas-legacy-codebase](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-codebase), [uc-data-migration-sas-to-snowflake](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-snowflake)
- **Module:** [SAS to Python/Snowflake](../../../modules/data-engineering/sas-to-python-snowflake.md)
- **Shows:** Devin reading legacy SAS programs and producing equivalent Python (pandas) functions and Snowflake SQL — cross-language ETL migration
- **Audience:** SAS developers, ETL engineers, data platform teams

#### Paste into Devin

```
Analyze the SAS macros in ts-sas-legacy-codebase/Macro/ — focus on the data transformation macros: transpose.sas, subset_data.sas, compare.sas, dedup_string.sas, dedup_mstring.sas, and the export family (export_csv.sas, export_xlsx.sas, export_dbms.sas).

For each macro:
1. Understand the SAS logic (DATA steps, PROC operations, macro variables, BY-group processing)
2. Translate into an equivalent Python function using pandas, preserving the same function signature (input dataset, parameters, output dataset)
3. Create pytest tests that validate the Python functions produce the same results as the SAS originals for sample inputs

Then analyze the sample datasets in uc-data-migration-sas-to-snowflake/sample_data/ (CUST_ACCOUNTS.csv, DAILY_BALANCE.csv, MONTHLY_AMB.csv). Create Snowflake-compatible DDL for each dataset with proper column types, constraints, and clustering keys. Write `COPY INTO` statements for bulk loading and validation queries comparing row counts and checksums.

Document all translation decisions in `SAS_MIGRATION_NOTES.md` — especially how SAS-specific constructs (macro variables, formats, informats, missing value handling) map to Python/Snowflake equivalents.

Open a PR with the Python functions, tests, Snowflake DDL, and migration notes.
```

---

### Exercise E: Monolith-to-Microservices Extraction (Spring Boot Upgrade)

- **Repository:** [uc-framework-upgrade-monolith-to-microservices](https://github.com/Cognition-Partner-Workshops/uc-framework-upgrade-monolith-to-microservices)
- **Module:** [Framework Upgrade](../../../modules/migration-modernization/framework-upgrade.md), [Containerization & Microservice Extraction](../../../modules/migration-modernization/containerization-microservice-extraction.md)
- **Shows:** Devin upgrading a Spring Boot 2.x monolith to 3.x (javax→jakarta namespace migration) and extracting a bounded context into a standalone microservice with Docker Compose
- **Audience:** Java/Spring Boot developers, monolith-to-microservices teams

#### Paste into Devin

```
Analyze uc-framework-upgrade-monolith-to-microservices — this is a Spring Boot monolith implementing the RealWorld blogging platform (articles, users, comments, tags, favorites).

Perform a two-phase modernization:

1. **Framework Upgrade (Spring Boot 2 → 3):**
   - Upgrade Spring Boot from 2.x to 3.x in build.gradle
   - Migrate all javax.* imports to jakarta.* (JPA, Servlet, Validation)
   - Update Spring Security configuration to the new SecurityFilterChain pattern
   - Resolve any deprecated API usage
   - Ensure all tests pass after the upgrade

2. **Microservice Extraction:**
   - Identify the "Article" bounded context (articles, tags, favorites, comments)
   - Extract it as a standalone Spring Boot 3 microservice in a new `article-service/` directory
   - Create proper DTOs and a REST client for cross-service communication (article-service ↔ user-service)
   - Add a Docker Compose configuration that runs both the main app and the extracted article-service
   - Add health check endpoints and document the API contracts between services

3. **Documentation:**
   - Create `docs/UPGRADE_NOTES.md` with every breaking change encountered and how it was resolved
   - Create `docs/EXTRACTION_DECISIONS.md` explaining the domain boundary choices

Open a PR with the upgraded monolith, extracted microservice, Docker Compose, and documentation.
```

---

### Exercise F: Ab Initio ETL Framework → Databricks Migration

- **Repository:** [ts-python-abinitio-etl-framework](https://github.com/Cognition-Partner-Workshops/ts-python-abinitio-etl-framework)
- **Shows:** Devin reading an enterprise Ab Initio ETL estate (DML schemas, graph patterns, PSET configs, KornShell orchestration, CDC processing) and generating a complete Databricks Lakehouse migration — PySpark notebooks, Delta Lake DDL, Databricks Workflows, and Delta MERGE for CDC
- **Audience:** Ab Initio developers, ETL engineers, data platform migration teams

#### Paste into Devin

```
Analyze the Ab Initio ETL framework in ts-python-abinitio-etl-framework. This is an enterprise ETL estate with:
- DML record layouts in `dml/` (Ab Initio schema definitions)
- Graph execution patterns in `graphs/` (parallel loader, CDC processor)
- PSET templates in `psets/pset_templates/` (environment-aware configuration)
- KornShell orchestration in `scripts/` (AutoSys-triggered batch pipelines)
- Monitoring in `monitoring/` (job status, SLA tracking)

Migrate this estate to Databricks Lakehouse architecture:

1. **DML → Delta Lake Schemas** (`databricks/schemas/`): Convert each `.dml` file in `dml/` to:
   - A PySpark StructType definition
   - A Delta Lake `CREATE TABLE` DDL statement with proper types (map Ab Initio decimal/string/packed to Spark types)
   - Document the type mapping decisions

2. **Graphs → PySpark Notebooks** (`databricks/notebooks/`): Convert the graph patterns:
   - `parallel_loader.py` → PySpark notebook with partition-based parallel ingestion
   - `cdc_processor.py` → Delta Lake MERGE statement using Change Data Feed
   - Add proper Spark session management, error handling, and logging

3. **KornShell → Databricks Workflows** (`databricks/workflows/`): Convert the orchestration scripts:
   - `run_daily_orders.ksh` → Databricks Workflow JSON definition with task dependencies (extract → CDC → staging → production)
   - `run_customer_cdc.ksh` → Scheduled workflow running every 4 hours
   - Map PSET parameters to Databricks job parameters and widget defaults

4. **Monitoring → Databricks Alerts** (`databricks/monitoring/`): Convert the AutoSys monitoring to:
   - Databricks SQL alert definitions for job failure detection
   - SLA compliance dashboard query

5. **Migration Runbook** (`docs/ABINITIO_TO_DATABRICKS_RUNBOOK.md`): Document:
   - Complete concept mapping table (Graph→Notebook, DML→StructType, PSET→Parameters, etc.)
   - Execution order for the migration
   - Risks and mitigations (e.g., packed decimal handling, partition strategy differences)

Open a PR with all Databricks artifacts and the migration runbook.
```

---

## Prerequisites

### Repos Required (must be available in the target GitHub org)

**Core labs (required):**
- [ ] ts-java-spring-boot-internet-banking-microservices (Lab 1)
- [ ] ts-java-spring-petclinic-rest-api (Lab 2)
- [ ] uc-data-source-migration-legacy-to-modern (Lab 3a & 3b)

**Post-session exercises (optional):**
- [ ] aws-mainframe-modernization-carddemo (Exercise A)
- [ ] uc-cve-remediation-regulatory-compliance (Exercise B)
- [ ] ts-sas-legacy-codebase (Exercise D)
- [ ] uc-data-migration-sas-to-snowflake (Exercise D)
- [ ] uc-framework-upgrade-monolith-to-microservices (Exercise E)
- [ ] ts-python-abinitio-etl-framework (Exercise F)

### Integration Requirements

- [ ] Devin installed on the target GitHub org (GitHub App)
- [ ] Participant Devin accounts provisioned

### Participant Requirements

- [ ] Devin account access
- [ ] GitHub access to the target org
- [ ] Browser (Chrome recommended)

## External GitHub Considerations

This workshop runs against an external GitHub organization (not Cognition-Partner-Workshops). Before the event:

1. **Fork or clone repos** — All repos listed above must be available in the target org. Use `git clone --bare` + `git push --mirror` to preserve full history, or fork if the target org has access to the source.
2. **Remove workflows** — If the target org's GitHub Actions runners differ, review `.github/workflows/` in each repo and adjust or remove as needed.
3. **Devin GitHub App** — Ensure the Devin GitHub App is installed on the target org with access to the relevant repos.
4. **Branch protection** — Ensure Devin can create branches and open PRs. If branch protection rules require reviews, configure Devin as an allowed bypass or ensure facilitators can approve quickly during labs.

## Notes

- **First-time audience:** Labs are ordered from lowest risk (analysis/documentation) to highest complexity (code generation, data migration). This builds confidence before asking participants to evaluate generated code.
- **2-hour pacing:** The key to fitting the labs in 2 hours is overlapping sessions. Participants kick off Lab N+1 while reviewing Lab N. Labs 3a and 3b are kicked off back-to-back — they use the same repo but are independent Devin sessions. By the wrap-up, all 4 sessions should be running or completed. Facilitators should keep transitions tight — 2 minutes max between labs.
- **Ask Devin throughout:** Every lab includes Ask Devin prompts. Emphasize that Ask Devin is a research tool for scoping tasks before creating sessions — better prompts lead to better results.
- **Post-session exercises:** Six additional exercises cover the full audience: COBOL copybook (Exercise A), security remediation (B), payment gap analysis (C), SAS→Snowflake (D), monolith→microservices (E), Ab Initio→Databricks (F). Share the workshop README with participants so they can try the ones most relevant to their role.
- **Jira integration (facilitator talking point):** Devin supports Jira ticket creation via the Atlassian MCP — for example, Lab 3a could be extended to have Devin create a Jira ticket for each anomaly found. This is a good topic to mention during the wrap-up or when discussing production workflows, but it is not included in the hands-on prompts for this workshop.

## Post-Event

- [ ] Collect participant feedback
- [ ] Archive any event-specific branches in the target org
- [ ] Share session recordings and PR links with participants
- [ ] Review Devin Knowledge items created during the workshop — these persist and help future sessions
