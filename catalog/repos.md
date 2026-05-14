# Repository Catalog

Master inventory of all repositories in the [Cognition-Partner-Workshops](https://github.com/orgs/Cognition-Partner-Workshops/repositories) GitHub org.

## How to Read This Catalog

Each repo entry includes:
- **Description** — what the repo contains
- **Tech Stack** — languages, frameworks, tools
- **License** — open source license
- **Challenges Supported** — links to challenge modules that use this repo
- **Cluster** — if the repo shares git history with another repo (intentional duplication)

---

## Repo Clusters & Upstream Sources

The full machine-readable mapping lives in [`upstream-map.yaml`](upstream-map.yaml). That file records every repo's upstream source, import method (`github-fork`, `clone-and-push`, or `original`), and cluster membership.

Some repos are intentionally duplicated from the same upstream source so that different workshop labs get isolated starting points. This avoids branch confusion during live events. Others are grouped because they belong to the same application or project ecosystem.

> **Why not mirrors?** We intentionally do NOT set up git mirrors between clustered repos. Each repo is meant to diverge from the upstream (and from each other) — mirrors would overwrite lab-specific changes. Instead, add an `upstream` remote when you need to compare: `git remote add upstream <url> && git fetch upstream`.

| Cluster | Label | Upstream Source | Repos | Reason |
|---------|-------|----------------|-------|--------|
| **C1** | Spring Boot RealWorld | [`gothinkster/spring-boot-realworld-example-app`](https://github.com/gothinkster/spring-boot-realworld-example-app) | `ts-java-spring-boot-realworld`, `uc-framework-upgrade-monolith-to-microservices`, `uc-cve-remediation-regulatory-compliance` | Labs 2 and 3 start from the same Spring Boot 2.6.3 monolith but have different objectives (upgrade vs. CVE remediation). Original import retained with `ts-` prefix. |
| **C2** | AWS CardDemo (COBOL) | [`aws-samples/aws-mainframe-modernization-carddemo`](https://github.com/aws-samples/aws-mainframe-modernization-carddemo) | `ts-cobol-carddemo`, `uc-legacy-modernization-cobol-to-java` | Lab 1 uses a dedicated copy for COBOL-to-Java migration. Original fork retained. |
| **C3** | Spring PetClinic | [`spring-projects/spring-petclinic`](https://github.com/spring-projects/spring-petclinic) (family) | `petclinic-angular`, `petclinic-backend`, `petclinic-microservices`, `petclinic-rest-api` | Four repos for the same application ecosystem (Angular frontend, backend monolith, microservices variant, REST API with OpenAPI spec). |
| **C4** | Modular Monolith DDD | [`kgrzybek/modular-monolith-with-ddd`](https://github.com/kgrzybek/modular-monolith-with-ddd) | `modular-monolith-ddd`, `modular-monolith-ddd-react` | Backend (.NET) + frontend (React) from the same upstream DDD project. |
| **C5** | Cal.com ecosystem | [`calcom/cal.com`](https://github.com/calcom/cal.com) | `calcom`, `calcom-infra`, `calcom-dataeng` | Main app (fork) plus infra and data-eng repos built around it. |
| **C6** | EventFlow demo | *original* | `eventflow-order-service`, `eventflow-payment-service`, `eventflow-storefront`, `eventflow-infra`, `eventflow-devin-integration` | Five-repo event-driven architecture demo (scaffolded from scratch). |
| **C7** | Apache Fineract | [`apache/fineract`](https://github.com/apache/fineract) | `fineract`, `fineract-web-app` | Backend (Fineract) + frontend (Mifos web app) from the same banking platform. |
| **C8** | Client Timesheet | *original* | `timesheet-app`, `timesheet-infra` | The timesheet app and its Terraform hosting/infra repo. |
| **C9** | AngularJS 1.x admins | *different upstreams* | `ts-angularjs-blur-admin`, `ts-angularjs-dashboard-widgets` | Two AngularJS 1.x admin dashboards for framework migration demos. |
| **C10** | SAS to Snowflake | [`scottbass/SAS`](https://github.com/scottbass/SAS) | `ts-sas-legacy-analytics`, `uc-data-migration-sas-to-snowflake` | Legacy SAS source paired with Snowflake migration validation tooling. |
| **C11** | OrderManager Monolith-to-Microservices | *original* | `platform-engineering-shared-services`, `ordermanager-monolith`, `ordermanager-iac`, `ordermanager-microservices` | Platform standard + .NET/Angular monolith + service IaC + microservices landing repo for decomposition demos. Platform repo provides the shared EKS/ArgoCD/monitoring infrastructure; monolith is the source; microservices repo receives all decomposed services and service-level IaC. |
| **C12** | Oracle Forms HRMS Modernization | *original* | `ts-plsql-oracle-forms-hrms`, `uc-legacy-modernization-oracle-forms-to-java` | Legacy Oracle Forms/PL/SQL HRMS application paired with Java/Spring Boot migration artifacts, test harness, and architecture documentation. Legacy repo is the static analysis target; use-case repo holds migration planning, target code, and equivalence tests. |

---

## Use-Case Prefixed Repos (`uc-`)

### uc-legacy-modernization-cobol-to-java
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java |
| **Description** | COBOL mainframe credit card management application (CardDemo). Real COBOL batch programs, JCL, DB2 integration, copybooks. |
| **Tech Stack** | COBOL, JCL, DB2, VSAM |
| **License** | Apache 2.0 |
| **Default Branch** | `main` |
| **Cluster** | C2 (from `ts-cobol-carddemo`) |
| **Challenges** | [COBOL to Java](../modules/migration-modernization/cobol-to-java.md), [Legacy Modernization Combined](../modules/migration-modernization/legacy-modernization-combined.md), [COBOL System Understanding](../modules/migration-modernization/cobol-system-understanding.md), [COBOL Migration Planning](../modules/migration-modernization/cobol-migration-planning.md), [Migration Test Harness](../modules/migration-modernization/migration-test-harness.md) |

### uc-legacy-modernization-oracle-forms-to-java
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-oracle-forms-to-java |
| **Description** | Oracle Forms to Java modernization use case — migration planning artifacts, target Spring Boot 3 structure, equivalence test harness, and architecture documentation for migrating an Oracle Forms 11g/12c HRMS application. |
| **Tech Stack** | Java 17, Spring Boot 3, Spring Data JPA, Spring Batch, Maven, Python (test harness) |
| **License** | MIT |
| **Default Branch** | `main` |
| **Cluster** | C12 (companion to `ts-plsql-oracle-forms-hrms`) |
| **Key Contents** | Migration plan (assessment, strategy, component mapping, risk register, timeline, ADRs), Java target (Spring Boot project with Employee module), test harness (YAML business scenarios, Python result comparator), architecture docs |
| **Challenges** | [Oracle Forms to Java](../modules/migration-modernization/oracle-forms-to-java.md), [Oracle Forms Migration Planning](../modules/migration-modernization/oracle-forms-migration-planning.md), [Migration Test Harness](../modules/migration-modernization/migration-test-harness.md) |

### uc-framework-upgrade-monolith-to-microservices
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-framework-upgrade-monolith-to-microservices |
| **Description** | Spring Boot 2.6.3 REST+GraphQL monolith (articles, users, comments domains). Java 11, Gradle, SQLite, Flyway migrations. |
| **Tech Stack** | Java 11, Spring Boot 2.6.3, Gradle, MyBatis, SQLite, GraphQL (DGS) |
| **License** | MIT |
| **Default Branch** | `initial-code` |
| **Cluster** | C1 (from `spring-boot-realworld-example-app`) |
| **Workflows Removed** | `gradle.yml` (PAT lacked `workflow` scope) |
| **Challenges** | [Framework Upgrade](../modules/migration-modernization/framework-upgrade.md), [Containerization & Microservice Extraction](../modules/migration-modernization/containerization-microservice-extraction.md), [API Consolidation](../modules/architecture-design/api-consolidation.md), [One-Shot Tech Debt Remediation](../modules/migration-modernization/one-shot-tech-debt-remediation.md) |

### uc-cve-remediation-regulatory-compliance
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance |
| **Description** | Same Spring Boot 2.6.3 monolith as Lab 2, but intended for security-focused exercises. Known vulnerable dependencies (Spring Boot 2.6.3 era). Pre-configured with OWASP Dependency-Check and SonarQube Gradle plugins for local SAST scanning. |
| **Tech Stack** | Java 11, Spring Boot 2.6.3, Gradle, MyBatis, SQLite, OWASP Dependency-Check, SonarQube |
| **License** | MIT |
| **Default Branch** | `initial-code` |
| **Cluster** | C1 (from `spring-boot-realworld-example-app`) |
| **Workflows Removed** | `gradle.yml` (PAT lacked `workflow` scope) |
| **Challenges** | [Upgrade Dependencies](../modules/security/upgrade-dependencies.md), [Remediate Vulnerabilities](../modules/security/remediate-vulnerabilities.md), [Shift Left Security](../modules/security/shift-left-security.md), [Security Antipatterns](../modules/security/security-antipatterns.md), [Event-Driven SAST Remediation](../modules/security/event-driven-sast-remediation.md), [Mass Security Backlog Remediation](../modules/security/mass-security-backlog-remediation.md), [One-Shot Tech Debt Remediation](../modules/migration-modernization/one-shot-tech-debt-remediation.md) |

### uc-data-source-migration-legacy-to-modern
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-legacy-to-modern |
| **Description** | Spring Boot 3.2 / Java 17 loan management application reading from legacy CDW-style tables (all-VARCHAR, cryptic column names, denormalized). Includes modern target schema, column mappings, and 5 workshop migration tasks. |
| **Tech Stack** | Java 17, Spring Boot 3.2, Spring Data JPA, H2 |
| **License** | MIT |
| **Default Branch** | `initial-code` |
| **Cluster** | None (scaffolded from scratch) |
| **Key Legacy Characteristics** | All-VARCHAR typing, cryptic column names (BORR_FST_NM, LN_CURR_BAL), denormalized, no FKs, code abbreviations (ACT/CLO/DFT) |
| **Challenges** | [Data Source Migration](../modules/data-engineering/data-source-migration.md), [Legacy Modernization Combined](../modules/migration-modernization/legacy-modernization-combined.md) |

### uc-dw-migration-teradata-to-snowflake
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-dw-migration-teradata-to-snowflake |
| **Description** | Teradata-based retail banking analytics data warehouse. 7 DDL tables, 3 views, 3 stored procedures, 3 macros, 2 BTEQ scripts, seed data (Norwegian locale), validation queries. |
| **Tech Stack** | Teradata SQL, BTEQ |
| **License** | MIT |
| **Default Branch** | `initial-code` |
| **Cluster** | None (scaffolded from scratch) |
| **Key Teradata Features** | SET/MULTISET, PI/PPI, COMPRESS, CASESPECIFIC, QUALIFY, ZEROIFNULL, CSUM, MAVG, HASHROW, VOLATILE TABLE, MACRO |
| **Challenges** | [DW Migration: Teradata to Snowflake](../modules/data-engineering/dw-migration-teradata-to-snowflake.md), [Informatica PowerCenter to Snowflake Migration](../modules/data-engineering/informatica-to-snowflake-migration.md) (reference) |

### uc-data-migration-sas-to-snowflake
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-snowflake |
| **Description** | SAS to Snowflake migration validation app — sample SAS data (.sas7bdat), lineage mapping, transformation scenarios, validation configs. Streamlit UI with Gemini LLM integration. |
| **Tech Stack** | Python, Streamlit, SAS data files |
| **License** | — |
| **Challenges** | General data migration demos |

### uc-data-migration-airflow
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-data-migration-airflow |
| **Description** | Docker Airflow setup for data migration and orchestration demos (Control-M analog). |
| **Tech Stack** | Python, Apache Airflow, Docker |
| **License** | — |
| **Challenges** | General data pipeline/orchestration demos |

### uc-language-upgrade-java-broadleaf
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-language-upgrade-java-broadleaf |
| **Description** | Broadleaf Commerce CE — eCommerce framework based on Java and Spring. For Java language upgrade demos. |
| **Tech Stack** | Java, Spring |
| **License** | Apache 2.0 |
| **Challenges** | General Java upgrade demos |

### uc-product-analysis-loan-modernization
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-product-analysis-loan-modernization |
| **Description** | Loan modernization analysis — input artifacts and output templates for Product Analyst demos. |
| **Tech Stack** | Documents/templates |
| **License** | — |
| **Challenges** | Business analysis/product analysis demos |

### uc-pod-remediation-credential-rotation
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-pod-remediation-credential-rotation |
| **Description** | Automated remediation of pod failures after credential rotations. Multi-agent Python system with rotation monitoring, failure detection, ServiceNow approval workflow, and remediation orchestration. |
| **Tech Stack** | Python, Kubernetes, ServiceNow API |
| **License** | MIT |
| **Default Branch** | `main` |
| **Cluster** | None (scaffolded from scratch) |
| **Challenges** | [Pod Remediation After Credential Rotation](../modules/observability-sre/pod-remediation-credential-rotation.md) |

### uc-document-review-automation
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-document-review-automation |
| **Description** | Automated document review for loan processing. Multi-agent Python system with document extraction (PDF, image, form), field comparison (exact, fuzzy, numeric), confidence-based decisioning, and compliance audit logging. |
| **Tech Stack** | Python |
| **License** | MIT |
| **Default Branch** | `main` |
| **Cluster** | None (scaffolded from scratch) |
| **Challenges** | [Document Review Automation](../modules/technical-documentation/document-review-automation.md) |

### uc-bdd-test-generation-rest-api
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-rest-api |
| **Description** | BDD test case generation for REST APIs. Spring Boot + Cucumber + Gherkin framework for automated Swagger-to-test transformation. Imported from RedFroggy/spring-cucumber-rest-api (MIT license). |
| **Tech Stack** | Java, Spring Boot, Cucumber, Gherkin, Maven |
| **License** | MIT |
| **Default Branch** | `main` |
| **Cluster** | None (imported from RedFroggy/spring-cucumber-rest-api) |
| **Challenges** | [BDD Test Generation](../modules/testing-qa/bdd-test-generation.md), [Continuous Quality Engineering](../modules/testing-qa/continuous-quality-engineering.md) |

### uc-volume-anomaly-detection
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-volume-anomaly-detection |
| **Description** | Volume-based anomaly detection for early issue identification. Multi-agent Python system with z-score and seasonal decomposition detectors, service health correlation, runbook-based recommendation engine, and incident insight reporting. |
| **Tech Stack** | Python |
| **License** | MIT |
| **Default Branch** | `main` |
| **Cluster** | None (scaffolded from scratch) |
| **Challenges** | [Volume Anomaly Detection](../modules/observability-sre/volume-anomaly-detection.md) |

### uc-quality-engineering-assurance
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/uc-quality-engineering-assurance |
| **Description** | Quality engineering and assurance use case repo. |
| **Tech Stack** | — |
| **License** | — |
| **Challenges** | [Unit Testing](../modules/testing-qa/unit-testing.md), [End-to-End Testing](../modules/testing-qa/end-to-end-testing.md) |

---

## Technology Stack Repos (`ts-`)

### ts-informatica-powercenter
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ts-informatica-powercenter |
| **Description** | Informatica PowerCenter 9.6.1 XML exports for a government HR data integration system (EHRP-to-BIIS). 11 mapping exports (CPM, CPM_AFPS, CPM_CDC, CPM_NIH, CPM_OIG, LES, FDA_Leave, EHRP2BIIS_UPDATE, Pay_Calendar, Pseudossn, COMPTIME) totaling 117K lines of XML. Includes Oracle SQL pre/post-load scripts and shell-based `pmcmd` orchestration. |
| **Tech Stack** | Informatica PowerCenter 9.6.1, Oracle, Shell (ksh) |
| **License** | Unlicense (public domain) |
| **Default Branch** | `main` |
| **Cluster** | None (imported from HHS/Informatica) |
| **Key Contents** | PowerCenter XML exports with SOURCE/TARGET/MAPPING/TRANSFORMATION/SESSION/WORKFLOW definitions, Oracle schemas (HISTDBA, NKNIGHT, EHRP), flat-file sources (VSAM/CSV), shell transfer scripts, SQL pre/post-load logic |
| **Also Known As** | Previously `Informatica-Demo` (legacy name) |
| **Challenges** | [Informatica PowerCenter Analysis](../modules/data-engineering/informatica-powercenter-analysis.md), [Informatica PowerCenter to Snowflake Migration](../modules/data-engineering/informatica-to-snowflake-migration.md) |

### ts-plsql-oracle-forms-hrms
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ts-plsql-oracle-forms-hrms |
| **Description** | Oracle Forms 11g/12c HR Management System (HRMS) legacy application. Forms XML exports, PLL shared libraries, menu modules, PL/SQL packages (specs + bodies), database triggers, schema DDL (42 tables, 15 views, 35+ sequences), and seed data. Realistic enterprise patterns with intentional technical debt (SQL injection, MD5 hashing, race conditions, circular dependencies). |
| **Tech Stack** | Oracle Forms 12c, PL/SQL, Oracle Database 19c |
| **License** | MIT |
| **Default Branch** | `main` |
| **Cluster** | C12 (companion to `uc-legacy-modernization-oracle-forms-to-java`) |
| **Key Legacy Characteristics** | Multi-layer architecture (Forms triggers + PLL libraries + PL/SQL packages + DB triggers), validation drift between client/server, circular package dependencies, cursor-loop batch processing, CONNECT BY performance anti-pattern, hard-coded encryption keys |
| **Challenges** | [Oracle Forms System Understanding](../modules/migration-modernization/oracle-forms-system-understanding.md), [Oracle Forms to Java](../modules/migration-modernization/oracle-forms-to-java.md), [Oracle Forms Migration Planning](../modules/migration-modernization/oracle-forms-migration-planning.md) |

### ts-angular-realworld
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ts-angular-realworld |
| **Description** | Real-world application built with Angular. |
| **Tech Stack** | Angular, TypeScript |
| **License** | — |
| **Challenges** | [Framework Upgrade](../modules/migration-modernization/framework-upgrade.md), [Repetitive Framework Upgrades](../modules/migration-modernization/repetitive-framework-upgrades.md), [Test Framework Migration](../modules/testing-qa/test-framework-migration.md) |

### ts-java-angular-jhipster
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ts-java-angular-jhipster |
| **Description** | JHipster sample app (Angular + Spring Boot monolith) for full-stack modernization demos. |
| **Tech Stack** | Angular, Spring Boot, JHipster, Java |
| **License** | Apache 2.0 |
| **Challenges** | General full-stack modernization demos |

### ts-sas-legacy-analytics
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics |
| **Description** | Legacy SAS programs and macros — represents a legacy SAS analytics environment for migration demos. |
| **Tech Stack** | SAS |
| **License** | Unlicense |
| **Challenges** | SAS-to-Snowflake migration demos |

### ts-java-selenium-testng
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ts-java-selenium-testng |
| **Description** | Sample framework based on Page Object Model, Selenium, TestNG using Java. |
| **Tech Stack** | Java, Selenium, TestNG |
| **License** | — |
| **Challenges** | [End-to-End Testing](../modules/testing-qa/end-to-end-testing.md) (alternative repo) |

### ts-java-swagger-petstore
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ts-java-swagger-petstore |
| **Description** | Swagger Petstore sample API. |
| **Tech Stack** | Java, Swagger/OpenAPI |
| **License** | Apache 2.0 |
| **Challenges** | API documentation/testing demos |

### ts-java-spring-boot-internet-banking
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking |
| **Description** | Internet Banking Concept — Java 21 / Spring Boot 3.2.4 microservices architecture with 6 services: core-banking, fund-transfer, user-service, utility-payment, API gateway, and service registry. Keycloak auth, RabbitMQ messaging, MySQL/PostgreSQL, Zipkin tracing, Docker Compose orchestration. |
| **Tech Stack** | Java 21, Spring Boot 3.2.4, Spring Cloud 2023.0.0, Gradle, Keycloak, RabbitMQ, MySQL, PostgreSQL, Docker |
| **License** | MIT |
| **Default Branch** | `main` |
| **Cluster** | None (imported from JavatoDev-com/internet-banking-concept-microservices) |
| **Workflows Removed** | `gradle.yml` (PAT lacked `workflow` scope) |
| **Key Contents** | 6 Spring Boot microservices (131 Java files), Docker Compose with full infra (Keycloak, Zipkin, RabbitMQ, MySQL), Postman collection, Feign inter-service communication, audit configurations |
| **Challenges** | Target application for: Application Knowledge Base Generation, FSE Gap Analysis, Jira Epic/Story Creation, Code Impact Analysis |

### ts-java-spring-boot-realworld
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld |
| **Description** | Original import of RealWorld example Spring Boot app. Java 11, Spring Boot 2.6.3, Gradle. |
| **Tech Stack** | Java 11, Spring Boot 2.6.3, Gradle, MyBatis, SQLite |
| **License** | MIT |
| **Renamed From** | `spring-boot-realworld-example-app` (applied `ts-java-` prefix to clarify tech stack) |
| **Cluster** | C1 (upstream for Labs 2 and 3) |
| **Challenges** | [Unit Testing](../modules/testing-qa/unit-testing.md), [Inline Documentation](../modules/technical-documentation/inline-documentation.md), [PR Review Automation](../modules/devops-cicd/pr-review-automation.md) (prefer using the `uc-` copies for labs) |

### petclinic-rest-api
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/petclinic-rest-api |
| **Description** | Spring PetClinic REST API variant — ships a rich, standalone OpenAPI 3.0 specification (2,168 lines, 35 operations, 15 schemas, 8 domain areas) with bean validation rules, entity relationships, and full CRUD. Input for API-first microservice code generation workshops. |
| **Tech Stack** | Java 17, Spring Boot 3.x, Spring Data JPA, Maven, Hibernate, HSQLDB/PostgreSQL/MySQL, SpringDoc OpenAPI |
| **License** | Apache 2.0 |
| **Default Branch** | `master` |
| **Cluster** | C3 (Spring PetClinic ecosystem) |
| **Workflows Removed** | `docker-build.yml`, `maven-build-master.yml`, `maven-build-pull-request.yml`, `newman-pipeline.yml` (PAT lacked `workflow` scope) |
| **Key Contents** | OpenAPI 3.0 spec (`src/main/resources/openapi.yml`) with 35 operations across 8 domains (owner, pet, vet, visit, pettypes, specialty, user, failing), 15 schemas with minLength/maxLength/pattern validation, Postman test collection, Docker Compose |
| **Challenges** | API-first microservice code generation, contract-first development, OpenAPI-driven scaffolding |

---

## Application Repos (multi-repo apps)

### petclinic-angular
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/petclinic-angular |
| **Description** | Spring PetClinic Angular frontend. |
| **Tech Stack** | Angular, TypeScript |
| **License** | — |
| **Cluster** | C3 (PetClinic ecosystem) |
| **Challenges** | [Framework Upgrade](../modules/migration-modernization/framework-upgrade.md), [Repetitive Framework Upgrades](../modules/migration-modernization/repetitive-framework-upgrades.md), [Test Framework Migration](../modules/testing-qa/test-framework-migration.md), [Continuous Quality Engineering](../modules/testing-qa/continuous-quality-engineering.md) |

### petclinic-backend
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/petclinic-backend |
| **Description** | Spring PetClinic backend — canonical Spring Boot app. |
| **Tech Stack** | Java, Spring Boot |
| **License** | Apache 2.0 |
| **Cluster** | C3 (PetClinic ecosystem) |
| **Challenges** | General Spring Boot demos |

### petclinic-microservices
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/petclinic-microservices |
| **Description** | Spring PetClinic Microservices — distributed version for modernization demos. |
| **Tech Stack** | Java, Spring Boot, Spring Cloud |
| **License** | Apache 2.0 |
| **Cluster** | C3 (PetClinic ecosystem) |
| **Challenges** | [Containerization & Microservice Extraction](../modules/migration-modernization/containerization-microservice-extraction.md) |

### ordermanager-monolith
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ordermanager-monolith |
| **Description** | .NET 8 + Angular 17 monolith application (OrderManager) demonstrating tightly coupled modules (Orders, Products, Customers, Inventory) sharing a single database. Designed as the "before" state for monolith-to-microservices decomposition demos. |
| **Tech Stack** | .NET 8, C#, Angular 17, TypeScript, Entity Framework Core, SQLite |
| **License** | MIT |
| **Default Branch** | `main` |
| **Cluster** | C11 (OrderManager monolith-to-microservices) |
| **Challenges** | [Release Management](../modules/devops-cicd/release-management.md), [Incident Response & Triage](../modules/observability-sre/incident-response-triage.md), [Platform-Conformant Microservice Decomposition](../modules/cloud-infrastructure/platform-conformant-microservice-decomposition.md), [Containerization & Microservice Extraction](../modules/migration-modernization/containerization-microservice-extraction.md) |

### ordermanager-iac
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ordermanager-iac |
| **Description** | Service-specific IaC for the OrderManager monolith. Helm charts, Dockerfile (multi-stage .NET+Angular build), ArgoCD application manifests, CI/CD pipeline, and network policies — all conforming to the platform-engineering-shared-services standard. |
| **Tech Stack** | Helm, Docker, ArgoCD, GitHub Actions, Kubernetes |
| **License** | MIT |
| **Default Branch** | `main` |
| **Cluster** | C11 (OrderManager monolith-to-microservices) |
| **Challenges** | [Release Management](../modules/devops-cicd/release-management.md), [Incident Response & Triage](../modules/observability-sre/incident-response-triage.md), [Platform-Conformant Microservice Decomposition](../modules/cloud-infrastructure/platform-conformant-microservice-decomposition.md) (context), [Containerization & Microservice Extraction](../modules/migration-modernization/containerization-microservice-extraction.md) |

### ordermanager-microservices
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ordermanager-microservices |
| **Description** | Landing repository for all microservices decomposed from the OrderManager monolith. Houses service source code (.NET 8 Web API + Angular 17), service-level IaC (Dockerfile, Helm chart, ArgoCD manifests, CI/CD pipeline) per service. Each participant works on a `workshop-<participant>` branch. |
| **Tech Stack** | .NET 8, C#, Angular 17, TypeScript, Helm, Docker, ArgoCD, GitHub Actions |
| **License** | MIT |
| **Default Branch** | `main` |
| **Cluster** | C11 (OrderManager monolith-to-microservices) |
| **Challenges** | [Platform-Conformant Microservice Decomposition](../modules/cloud-infrastructure/platform-conformant-microservice-decomposition.md) (landing repo) |

### eventflow-storefront
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/eventflow-storefront |
| **Description** | Customer-facing e-commerce storefront for EventFlow demo. Workshop participants experience the zero-decimal currency bug. |
| **Tech Stack** | TypeScript, React |
| **License** | — |
| **Default Branch** | `devin/1772995107-storefront-ui` |
| **Cluster** | C6 (EventFlow multi-service demo) |
| **Challenges** | [Incident Response & Triage](../modules/observability-sre/incident-response-triage.md), [Fix Runtime Bug](../modules/application-development/fix-runtime-bug.md) |

### quickapp-monolith
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/quickapp-monolith |
| **Description** | Before-state monolithic .NET + Angular application (imported from QuickApp) — starting point for containerized decomposition workshop. |
| **Tech Stack** | .NET, C#, Angular, TypeScript |
| **License** | MIT |
| **Default Branch** | `import/initial` |
| **Cluster** | C12 (.NET/Angular Containerized Decomposition) |
| **Challenges** | [Containerization & Microservice Extraction](../modules/migration-modernization/containerization-microservice-extraction.md), [Legacy Modernization Combined](../modules/migration-modernization/legacy-modernization-combined.md), [.NET Monolith Decomposition](../modules/migration-modernization/dotnet-monolith-decomposition.md), [Cross-Service Integration Testing](../modules/testing-qa/cross-service-integration-testing.md) |

### quickapp-microservices
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/quickapp-microservices |
| **Description** | Target-state .NET microservices architecture — decomposed from monolith into Identity, Customer, Order, Product, Notification services with YARP API Gateway. |
| **Tech Stack** | .NET, C#, YARP |
| **License** | — |
| **Default Branch** | `scaffold/initial` |
| **Cluster** | C12 (.NET/Angular Containerized Decomposition) |
| **Challenges** | [Containerization & Microservice Extraction](../modules/migration-modernization/containerization-microservice-extraction.md), [.NET Monolith Decomposition](../modules/migration-modernization/dotnet-monolith-decomposition.md), [Cross-Service Integration Testing](../modules/testing-qa/cross-service-integration-testing.md), [Cross-Service Bug Investigation](../modules/migration-modernization/cross-service-bug-investigation.md) |

### quickapp-microfrontends
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/quickapp-microfrontends |
| **Description** | Target-state Angular micro-frontends — decomposed from monolith using Webpack Module Federation (shell + 4 remote apps). |
| **Tech Stack** | Angular, TypeScript, Webpack Module Federation |
| **License** | — |
| **Default Branch** | `scaffold/initial` |
| **Cluster** | C12 (.NET/Angular Containerized Decomposition) |
| **Challenges** | [Containerization & Microservice Extraction](../modules/migration-modernization/containerization-microservice-extraction.md) |

### quickapp-iac
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/quickapp-iac |
| **Description** | App-specific Helm charts for deploying the decomposed .NET microservices and Angular micro-frontends to Kubernetes. |
| **Tech Stack** | Helm, Kubernetes, Docker |
| **License** | — |
| **Default Branch** | `scaffold/initial` |
| **Cluster** | C12 (.NET/Angular Containerized Decomposition) |
| **Challenges** | [Containerization & Microservice Extraction](../modules/migration-modernization/containerization-microservice-extraction.md), [.NET Monolith Decomposition](../modules/migration-modernization/dotnet-monolith-decomposition.md), [Release Management](../modules/devops-cicd/release-management.md) |

### timesheet-app
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/timesheet-app |
| **Description** | Client timesheet and billable hours tracking application. React 19 + Node.js/Express + SQLite. Full CRUD, auth, reporting, CSV/PDF export. Multi-part app (backend + frontend + Docker). |
| **Tech Stack** | React 19, TypeScript, Node.js, Express, SQLite, Material-UI, Vite |
| **License** | — |
| **Renamed From** | `client-timesheet-app` → `app_timesheet` → `timesheet-app` (identity-first naming) |
| **Challenges** | [Linting & Static Analysis](../modules/testing-qa/linting-static-analysis.md), [Upgrade Dependencies](../modules/security/upgrade-dependencies.md), [Event-Driven SAST Remediation](../modules/security/event-driven-sast-remediation.md), [Mass Security Backlog Remediation](../modules/security/mass-security-backlog-remediation.md), [Fix UI Bug](../modules/application-development/fix-ui-bug.md), [Fix Data Bug](../modules/application-development/fix-data-bug.md), [New Feature Development](../modules/application-development/new-feature-development.md), [CI/CD Pipeline](../modules/devops-cicd/cicd-pipeline.md), [Continuous Quality Engineering](../modules/testing-qa/continuous-quality-engineering.md) |

---

## Non-Prefixed Repos

### timesheet-infra
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/timesheet-infra |
| **Description** | Infrastructure/hosting configuration for the client-timesheet-app. Terraform-based. |
| **Tech Stack** | Terraform, AWS |
| **License** | — |
| **Challenges** | [Linting & Static Analysis](../modules/testing-qa/linting-static-analysis.md) (terraform fmt), [IaC Translation](../modules/cloud-infrastructure/iac-translation.md) |

### calcom
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/calcom |
| **Description** | Open-source scheduling infrastructure. Monorepo with Next.js web app, NestJS API v2, Prisma, PostgreSQL. |
| **Tech Stack** | TypeScript, Next.js, NestJS, Prisma, PostgreSQL, Turborepo |
| **License** | AGPLv3 (open core) |
| **Challenges** | [End-to-End Testing](../modules/testing-qa/end-to-end-testing.md), [Fix Runtime Bug](../modules/application-development/fix-runtime-bug.md), [CI/CD Pipeline](../modules/devops-cicd/cicd-pipeline.md) |

### calcom-infra
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/calcom-infra |
| **Description** | Infrastructure as code for Cal.com deployment. |
| **Tech Stack** | IaC |
| **License** | — |
| **Challenges** | [IaC Translation](../modules/cloud-infrastructure/iac-translation.md) |

### calcom-dataeng
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/calcom-dataeng |
| **Description** | Data engineering setup for Cal.com. |
| **Tech Stack** | Data engineering |
| **License** | — |
| **Challenges** | Data pipeline demos |

### ts-react-coreui-admin
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ts-react-coreui-admin |
| **Description** | Open source admin template based on Bootstrap 5 and React.js. |
| **Tech Stack** | React, Bootstrap 5, JavaScript |
| **License** | MIT |
| **Challenges** | [Framework Upgrade](../modules/migration-modernization/framework-upgrade.md) (Bootstrap → Material UI) |

### ts-cobol-carddemo
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/ts-cobol-carddemo |
| **Description** | Original import of AWS CardDemo COBOL mainframe app. 62 copybooks, 9 ASCII feed files, 30+ batch programs. |
| **Tech Stack** | COBOL, JCL, DB2 |
| **License** | Apache 2.0 |
| **Cluster** | C2 (upstream for Lab 1) |
| **Challenges** | [COBOL Copybook to PySpark/JSON](../modules/data-engineering/cobol-copybook-to-pyspark-json.md), General COBOL demos (prefer using the `uc-` copy for migration labs) |

### jpetstore-6
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/jpetstore-6 |
| **Description** | Web application built on MyBatis 3, Spring MVC 5.3, Stripes 1.6. **Not recommended for Spring Boot upgrade labs** — Stripes framework is dead, already on modern Spring versions. |
| **Tech Stack** | Java, Spring MVC 5.3, MyBatis 3.5, Stripes 1.6, JSP |
| **License** | Apache 2.0 |
| **Challenges** | Legacy Java exploration only (not suitable for upgrade labs) |

### fineract
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/fineract |
| **Description** | Apache Fineract — open banking platform. |
| **Tech Stack** | Java, Spring Boot |
| **License** | Apache 2.0 |
| **Challenges** | Banking domain demos, [Cloud-Native Refactor](../modules/migration-modernization/cloud-native-refactor.md) |

### modular-monolith-ddd / modular-monolith-ddd-react
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/modular-monolith-ddd |
| **Description** | Full Modular Monolith application with DDD approach (.NET backend + React frontend). |
| **Tech Stack** | .NET, C#, React |
| **License** | MIT |
| **Challenges** | .NET modernization demos, DDD architecture demos |

### platform-engineering-shared-services
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/platform-engineering-shared-services |
| **Description** | Shared DevOps platform for all workshop runtime environments. AWS CDK (TypeScript) stacks for EKS, VPC, ECR, Route 53 DNS. Helm values for ingress-nginx, cert-manager, Prometheus/Grafana monitoring, ArgoCD GitOps. Namespace provisioning with resource quotas, limit ranges, and network policies. All resources use RemovalPolicy.DESTROY for clean teardown. |
| **Tech Stack** | AWS CDK (TypeScript), Helm, Kubernetes, AWS EKS, ArgoCD, Prometheus, Grafana |
| **License** | MIT |
| **Default Branch** | `main` |
| **Cluster** | C11 (OrderManager monolith-to-microservices) |
| **Challenges** | [Release Management](../modules/devops-cicd/release-management.md), [Incident Response & Triage](../modules/observability-sre/incident-response-triage.md), [Configuration Management & Feature Flags](../modules/devops-cicd/configuration-management-feature-flags.md), [Platform-Conformant Microservice Decomposition](../modules/cloud-infrastructure/platform-conformant-microservice-decomposition.md) (context) |

### otterworks
| | |
|---|---|
| **URL** | https://github.com/Cognition-Partner-Workshops/otterworks |
| **Description** | Polyglot microservices platform for real-time collaborative document editing and file management. 10 backend services (Go, Java, Rust, Python x2, Node.js, Kotlin, Scala, Ruby, C#), 2 frontends (React, Angular), full observability stack (Prometheus, Grafana, Jaeger), chaos engineering framework, legacy ETL pipelines, and intentionally planted security vulnerabilities. Designed as a multi-challenge advanced workshop repo. |
| **Tech Stack** | Rust (Actix), Python (FastAPI, Flask), Node.js (Socket.IO, Yjs), Java (Spring Boot), Go, Kotlin (Ktor), Scala (Akka), Ruby (Rails), C# (.NET), TypeScript (Next.js, Angular), PostgreSQL, DynamoDB, Redis, MeiliSearch, AWS (S3, SNS, SQS), Terraform, Docker, Prometheus, Grafana, Jaeger |
| **License** | MIT |
| **Default Branch** | `main` |
| **Challenges** | [OtterWorks Workshop](../applications/otterworks/README.md) — ETL modernization (cron to Airflow), framework upgrades (Java 8 to 17), language translation (Flask to FastAPI), incident response (chaos engineering), security remediation (CVE triage), API contract audit, test coverage |

### Other Repos

| Repo | Description | Tech Stack |
|------|-------------|-----------|
| [ts-python-aws-personalize](https://github.com/Cognition-Partner-Workshops/ts-python-aws-personalize) | AWS Personalize ML workshop | Python, AWS |
| [ts-angularjs-blur-admin](https://github.com/Cognition-Partner-Workshops/ts-angularjs-blur-admin) | AngularJS 1.x admin panel | AngularJS, Bootstrap |
| [ts-angularjs-dashboard-widgets](https://github.com/Cognition-Partner-Workshops/ts-angularjs-dashboard-widgets) | AngularJS dashboard widgets | AngularJS |
| [ts-informatica-powercenter](https://github.com/Cognition-Partner-Workshops/ts-informatica-powercenter) | Informatica PowerCenter 9.6.1 XML exports (11 mappings, 117K lines), Oracle SQL, shell orchestration — see [full entry](#ts-informatica-powercenter) | Informatica PowerCenter, Oracle, Shell |
| [ts-katalon-web-automation](https://github.com/Cognition-Partner-Workshops/ts-katalon-web-automation) | Katalon web automation sample | Katalon |
| [keycloak](https://github.com/Cognition-Partner-Workshops/keycloak) | Identity and Access Management | Java |
| [fineract-web-app](https://github.com/Cognition-Partner-Workshops/fineract-web-app) | Mifos X Web App (Fineract frontend) | Angular |
| [ofbiz-framework](https://github.com/Cognition-Partner-Workshops/ofbiz-framework) | Apache OFBiz ERP/CRM | Java |
| [openmrs-core](https://github.com/Cognition-Partner-Workshops/openmrs-core) | OpenMRS medical records | Java |
| [ts-java-real-estate-management](https://github.com/Cognition-Partner-Workshops/ts-java-real-estate-management) | Real estate property management | Java |
| [ruby-redmine](https://github.com/Cognition-Partner-Workshops/ruby-redmine) | Redmine project management | Ruby |
| [ts-serverless-digital-asset-payments](https://github.com/Cognition-Partner-Workshops/ts-serverless-digital-asset-payments) | Serverless digital asset payments | AWS, Serverless |
| [ts-serverless-eda-insurance-claims](https://github.com/Cognition-Partner-Workshops/ts-serverless-eda-insurance-claims) | Event-driven insurance claims | AWS, Serverless |
| [ts-python-streamify-data-engineering](https://github.com/Cognition-Partner-Workshops/ts-python-streamify-data-engineering) | Data engineering with Kafka, Spark, dbt | Python, Kafka, Spark, dbt |
| [traderx](https://github.com/Cognition-Partner-Workshops/traderx) | TraderX fork for Devin demos | Java |
