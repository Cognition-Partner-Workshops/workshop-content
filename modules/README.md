# Workshop Modules

All hands-on modules organized by engineering discipline. Each module is a self-contained challenge with:

1. **Paste into Devin** — a copy-pastable prompt to get started immediately
2. **Research with Ask Devin** — prompts to gather requirements and refine your approach
3. **Read the DeepWiki** *(optional)* — explore the repo's auto-generated architecture docs
4. **Review & Give Feedback** *(optional)* — leave PR comments and iterate with Devin

---

## Quick Navigation

| Category | Discipline / Job Title | Modules |
|----------|----------------------|---------|
| [Application Development](application-development/) | Software Developer, Full-Stack Engineer | 7 modules |
| [Testing & QA](testing-qa/) | QA Engineer, SDET, Test Automation Engineer | 11 modules |
| [Security](security/) | Security Engineer, AppSec Engineer | 7 modules |
| [Compliance & Governance](compliance-governance/) | Compliance Officer, GRC Analyst | 3 modules |
| [DevOps & CI/CD](devops-cicd/) | DevOps Engineer, Release Engineer | 5 modules |
| [Cloud & Infrastructure](cloud-infrastructure/) | Cloud Engineer, Platform Engineer | 6 modules |
| [Observability & SRE](observability-sre/) | SRE, Observability Engineer | 4 modules |
| [Data Engineering](data-engineering/) | Data Engineer, Analytics Engineer | 9 modules |
| [Architecture & Design](architecture-design/) | Solution Architect, Enterprise Architect | 5 modules |
| [AI & ML Engineering](ai-ml-engineering/) | ML Engineer, AI Engineer | 3 modules |
| [Technical Documentation](technical-documentation/) | Technical Writer, Documentation Engineer | 6 modules |
| [Migration & Modernization](migration-modernization/) | Modernization Specialist, Migration Lead | 15 modules |
| [Devin Features](devin-features/) | Cross-cutting Devin platform activities | 1 reference |

---

## All Modules at a Glance

### Application Development

| Module | Difficulty | Time | Repos |
|--------|-----------|------|-------|
| [Gather Requirements](application-development/gather-requirements.md) | Beginner | 30 min | timesheet-app, calcom |
| [Test-Driven Development](application-development/test-driven-development.md) | Intermediate | 60 min | timesheet-app, uc-spring-boot-upgrade-microservice-extraction |
| [Fix Runtime Bug](application-development/fix-runtime-bug.md) | Intermediate | 45 min | timesheet-app, calcom, eventflow-storefront |
| [Fix UI Bug](application-development/fix-ui-bug.md) | Beginner–Intermediate | 30 min | timesheet-app |
| [Fix Data Bug](application-development/fix-data-bug.md) | Intermediate | 45 min | timesheet-app |
| [New Feature Development](application-development/new-feature-development.md) | Intermediate–Advanced | 60 min | timesheet-app, uc-spring-boot-upgrade-microservice-extraction, uc-data-source-migration-jdbc-normalization |
| [Database Schema Evolution](application-development/database-schema-evolution.md) | Intermediate | 45 min | timesheet-app, uc-spring-boot-upgrade-microservice-extraction, uc-data-source-migration-jdbc-normalization |

### Testing & QA

| Module | Difficulty | Time | Repos |
|--------|-----------|------|-------|
| [Linting & Static Analysis](testing-qa/linting-static-analysis.md) | Beginner | 30 min | timesheet-app, timesheet-infra |
| [Unit Testing](testing-qa/unit-testing.md) | Beginner–Intermediate | 45 min | timesheet-app, ts-java-spring-boot-realworld, uc-spring-boot-upgrade-microservice-extraction |
| [End-to-End Testing](testing-qa/end-to-end-testing.md) | Intermediate | 60 min | timesheet-app, calcom, ts-java-selenium-testng |
| [Performance Testing & Optimization](testing-qa/performance-testing.md) | Intermediate–Advanced | 60 min | timesheet-app, calcom, uc-spring-boot-upgrade-microservice-extraction |
| [Accessibility Compliance](testing-qa/accessibility-compliance.md) | Intermediate | 45 min | timesheet-app, calcom |
| [BDD Test Generation](testing-qa/bdd-test-generation.md) | Intermediate | 60 min | uc-bdd-test-generation-cucumber, ts-java-swagger-petstore |
| [Contract Testing](testing-qa/contract-testing.md) | Intermediate–Advanced | 60 min | petclinic-microservices, quickapp-microservices |
| [Mutation Testing](testing-qa/mutation-testing.md) | Intermediate–Advanced | 60 min | Multiple repos |
| [Load Testing & Benchmarking](testing-qa/load-testing-benchmarking.md) | Intermediate–Advanced | 60 min | Multiple repos |
| [Visual Regression Testing](testing-qa/visual-regression-testing.md) | Intermediate | 45 min | Multiple repos |
| [Cross-Service Integration Testing](testing-qa/cross-service-integration-testing.md) | Intermediate | 45 min | quickapp-monolith, quickapp-microservices |

### Security

| Module | Difficulty | Time | Repos |
|--------|-----------|------|-------|
| [Upgrade Dependencies](security/upgrade-dependencies.md) | Beginner–Intermediate | 45 min | timesheet-app, uc-cve-remediation-regulatory-compliance |
| [Remediate Vulnerabilities](security/remediate-vulnerabilities.md) | Intermediate | 60 min | timesheet-app, uc-cve-remediation-regulatory-compliance |
| [Shift Left Security](security/shift-left-security.md) | Intermediate–Advanced | 60 min | timesheet-app, uc-cve-remediation-regulatory-compliance |
| [Security Antipatterns](security/security-antipatterns.md) | Intermediate | 45 min | timesheet-app, uc-cve-remediation-regulatory-compliance, Online-Banking-System-using-Java |
| [Secrets Management & Detection](security/secrets-management-detection.md) | Intermediate | 45 min | timesheet-app, uc-cve-remediation-regulatory-compliance, uc-spring-boot-upgrade-microservice-extraction |
| [Event-Driven SAST Remediation](security/event-driven-sast-remediation.md) | Advanced | 90 min | timesheet-app, uc-cve-remediation-regulatory-compliance |
| [Mass Security Backlog Remediation](security/mass-security-backlog-remediation.md) | Advanced | 90 min | timesheet-app, uc-cve-remediation-regulatory-compliance |

### Compliance & Governance

| Module | Difficulty | Time | Repos |
|--------|-----------|------|-------|
| [License Compliance Audit](compliance-governance/license-compliance-audit.md) | Intermediate | 45 min | Multiple repos |
| [GDPR/PII Detection](compliance-governance/gdpr-pii-detection.md) | Intermediate–Advanced | 60 min | Multiple repos |
| [Regulatory Reporting](compliance-governance/regulatory-reporting.md) | Intermediate | 45 min | Multiple repos |

### DevOps & CI/CD

| Module | Difficulty | Time | Repos |
|--------|-----------|------|-------|
| [CI/CD Pipeline](devops-cicd/cicd-pipeline.md) | Intermediate | 45 min | timesheet-app, uc-spring-boot-upgrade-microservice-extraction |
| [PR Review Automation](devops-cicd/pr-review-automation.md) | Beginner–Intermediate | 30 min | timesheet-app, ts-java-spring-boot-realworld, uc-spring-boot-upgrade-microservice-extraction |
| [CI Failure Resolution](devops-cicd/ci-failure-resolution.md) | Intermediate | 45 min | timesheet-app, uc-spring-boot-upgrade-microservice-extraction |
| [Release Management](devops-cicd/release-management.md) | Intermediate | 45 min | timesheet-app, uc-spring-boot-upgrade-microservice-extraction, ordermanager-monolith |
| [Configuration Mgmt & Feature Flags](devops-cicd/configuration-management-feature-flags.md) | Intermediate | 45 min | timesheet-app, uc-spring-boot-upgrade-microservice-extraction, platform-engineering-shared-services |

### Cloud & Infrastructure

| Module | Difficulty | Time | Repos |
|--------|-----------|------|-------|
| [IaC Translation](cloud-infrastructure/iac-translation.md) | Intermediate | 45 min | timesheet-infra, calcom-infra |
| [Platform-Conformant Microservice Decomposition](cloud-infrastructure/platform-conformant-microservice-decomposition.md) | Advanced | 75 min | ordermanager-monolith, ordermanager-microservices, platform-engineering-shared-services |
| [GitOps & ArgoCD Setup](cloud-infrastructure/gitops-argocd-setup.md) | Advanced | 75 min | Multiple repos |
| [Kubernetes Manifest Generation](cloud-infrastructure/kubernetes-manifest-generation.md) | Intermediate–Advanced | 60 min | Multiple repos |
| [Terraform Module Extraction](cloud-infrastructure/terraform-module-extraction.md) | Intermediate–Advanced | 60 min | timesheet-infra |
| [Cost Optimization Analysis](cloud-infrastructure/cost-optimization-analysis.md) | Intermediate | 45 min | timesheet-infra, calcom-infra |

### Observability & SRE

| Module | Difficulty | Time | Repos |
|--------|-----------|------|-------|
| [Observability & Monitoring](observability-sre/observability-monitoring.md) | Intermediate–Advanced | 60 min | timesheet-app, uc-spring-boot-upgrade-microservice-extraction |
| [Incident Response & Triage](observability-sre/incident-response-triage.md) | Intermediate | 45 min | timesheet-app, uc-spring-boot-upgrade-microservice-extraction, ordermanager-monolith, eventflow-storefront |
| [Pod Remediation After Credential Rotation](observability-sre/pod-remediation-credential-rotation.md) | Advanced | 60 min | uc-pod-remediation-credential-rotation |
| [Volume Anomaly Detection](observability-sre/volume-anomaly-detection.md) | Intermediate–Advanced | 60 min | uc-volume-anomaly-detection |

### Data Engineering

| Module | Difficulty | Time | Repos |
|--------|-----------|------|-------|
| [DW Migration: Teradata to Snowflake](data-engineering/dw-migration-teradata-to-snowflake.md) | Intermediate–Advanced | 60 min | uc-dw-migration-teradata-to-snowflake |
| [Data Source Migration](data-engineering/data-source-migration.md) | Intermediate | 60 min | uc-data-source-migration-jdbc-normalization |
| [ETL Pipeline Modernization](data-engineering/etl-pipeline-modernization.md) | Intermediate–Advanced | 60 min | uc-dw-migration-teradata-to-snowflake |
| [Data Quality & Validation](data-engineering/data-quality-validation.md) | Intermediate | 45 min | uc-dw-migration-teradata-to-snowflake |
| [SAS to Python/Snowflake](data-engineering/sas-to-python-snowflake.md) | Intermediate–Advanced | 60 min | Multiple repos |
| [Informatica PowerCenter Analysis](data-engineering/informatica-powercenter-analysis.md) | Intermediate | 45 min | ts-informatica-powercenter |
| [Informatica PowerCenter to Snowflake Migration](data-engineering/informatica-to-snowflake-migration.md) | Advanced | 75 min | ts-informatica-powercenter, uc-dw-migration-teradata-to-snowflake |
| [SAS Migration Analysis](data-engineering/sas-migration-analysis.md) | Intermediate–Advanced | 75 min | ts-sas-legacy-analytics, uc-data-migration-sas-to-databricks |
| [SAS CI/CD & Operationalization](data-engineering/sas-cicd-operationalization.md) | Intermediate–Advanced | 60 min | ts-sas-legacy-analytics, uc-data-migration-sas-to-databricks |

### Architecture & Design

| Module | Difficulty | Time | Repos |
|--------|-----------|------|-------|
| [Architecture Decision Records](architecture-design/architecture-decision-records.md) | Intermediate | 45 min | Multiple repos |
| [API Design Review](architecture-design/api-design-review.md) | Intermediate | 45 min | Multiple repos |
| [Dependency Graph Analysis](architecture-design/dependency-graph-analysis.md) | Intermediate–Advanced | 60 min | Multiple repos |
| [API Consolidation](architecture-design/api-consolidation.md) | Intermediate | 45 min | uc-spring-boot-upgrade-microservice-extraction |
| [Code Refactoring & Tech Debt](architecture-design/code-refactoring-tech-debt.md) | Intermediate | 45 min | timesheet-app, calcom, ts-java-spring-boot-realworld |

### AI & ML Engineering

| Module | Difficulty | Time | Repos |
|--------|-----------|------|-------|
| [ML Pipeline Setup](ai-ml-engineering/ml-pipeline-setup.md) | Advanced | 75 min | Multiple repos |
| [Model Evaluation & Testing](ai-ml-engineering/model-evaluation-testing.md) | Advanced | 60 min | Multiple repos |
| [LLM Integration Patterns](ai-ml-engineering/llm-integration-patterns.md) | Intermediate–Advanced | 60 min | Multiple repos |

### Technical Documentation

| Module | Difficulty | Time | Repos |
|--------|-----------|------|-------|
| [Inline Documentation](technical-documentation/inline-documentation.md) | Beginner | 30 min | timesheet-app, ts-java-spring-boot-realworld, uc-dw-migration-teradata-to-snowflake |
| [API Documentation](technical-documentation/api-documentation.md) | Intermediate | 45 min | timesheet-app, uc-spring-boot-upgrade-microservice-extraction, uc-data-source-migration-jdbc-normalization |
| [Document Review Automation](technical-documentation/document-review-automation.md) | Intermediate | 45 min | uc-document-review-automation |
| [Runbook Generation](technical-documentation/runbook-generation.md) | Intermediate | 45 min | platform-engineering-shared-services, eventflow-infra |
| [Onboarding Guide Generation](technical-documentation/onboarding-guide-generation.md) | Intermediate | 45 min | Multiple repos |
| [Changelog & Release Notes](technical-documentation/changelog-release-notes.md) | Beginner–Intermediate | 30 min | Multiple repos |

### Migration & Modernization

| Module | Difficulty | Time | Repos |
|--------|-----------|------|-------|
| [COBOL to Java](migration-modernization/cobol-to-java.md) | Intermediate–Advanced | 60 min | uc-legacy-modernization-cobol-to-java |
| [COBOL System Understanding](migration-modernization/cobol-system-understanding.md) | Intermediate | 60 min | uc-legacy-modernization-cobol-to-java |
| [COBOL Migration Planning](migration-modernization/cobol-migration-planning.md) | Intermediate–Advanced | 60 min | uc-legacy-modernization-cobol-to-java |
| [Migration Test Harness](migration-modernization/migration-test-harness.md) | Intermediate–Advanced | 60 min | uc-legacy-modernization-cobol-to-java |
| [Framework Upgrade](migration-modernization/framework-upgrade.md) | Intermediate | 60 min | uc-spring-boot-upgrade-microservice-extraction, petclinic-angular, ts-angular-realworld |
| [Repetitive Framework Upgrades](migration-modernization/repetitive-framework-upgrades.md) | Intermediate | 60 min | Multiple repos (parallel sessions) |
| [Containerization & Microservice Extraction](migration-modernization/containerization-microservice-extraction.md) | Intermediate–Advanced | 60 min | uc-spring-boot-upgrade-microservice-extraction, petclinic-microservices |
| [Cloud-Native Refactor](migration-modernization/cloud-native-refactor.md) | Intermediate–Advanced | 60 min | uc-spring-boot-upgrade-microservice-extraction, timesheet-app |
| [Legacy Modernization Combined](migration-modernization/legacy-modernization-combined.md) | Advanced | 60 min | Multiple repos |
| [One-Shot Tech Debt Remediation](migration-modernization/one-shot-tech-debt-remediation.md) | Advanced | 75 min | uc-spring-boot-upgrade-microservice-extraction, uc-cve-remediation-regulatory-compliance |
| [.NET Monolith Decomposition](migration-modernization/dotnet-monolith-decomposition.md) | Intermediate–Advanced | 75 min | quickapp-monolith, quickapp-microservices |
| [Cross-Service Bug Investigation](migration-modernization/cross-service-bug-investigation.md) | Intermediate | 45 min | quickapp-microservices |
| [Oracle Forms System Understanding](migration-modernization/oracle-forms-system-understanding.md) | Intermediate | 60 min | ts-plsql-oracle-forms-hrms |
| [Oracle Forms Migration Planning](migration-modernization/oracle-forms-migration-planning.md) | Intermediate–Advanced | 60 min | ts-plsql-oracle-forms-hrms, uc-legacy-modernization-oracle-forms-to-java |
| [Oracle Forms to Java](migration-modernization/oracle-forms-to-java.md) | Intermediate–Advanced | 60 min | ts-plsql-oracle-forms-hrms, uc-legacy-modernization-oracle-forms-to-java |

---

## Composing Workshops from Modules

Modules are the building blocks. Combine them into workshops:

| Workshop Theme | Recommended Modules | Duration |
|----------------|-------------------|----------|
| **COBOL Modernization** | COBOL System Understanding → COBOL Migration Planning → Migration Test Harness → COBOL to Java | 4 hours |
| **Security & Compliance** | Upgrade Dependencies + Remediate Vulnerabilities → Shift Left Security + License Compliance Audit | 3 hours |
| **Enterprise Security at Scale** | Event-Driven SAST Remediation → Mass Security Backlog Remediation → One-Shot Tech Debt Remediation | 4 hours |
| **Framework Upgrades** | Framework Upgrade + Repetitive Framework Upgrades (parallel sessions) | 2 hours |
| **Platform Engineering** | Platform-Conformant Microservice Decomposition + GitOps & ArgoCD + K8s Manifest Generation | 3 hours |
| **Testing & Quality** | Linting & Static Analysis → Unit Testing → End-to-End Testing → Contract Testing | 3 hours |
| **Feature Development** | Gather Requirements → Test-Driven Development → New Feature Development | 3 hours |
| **Agentic AI** | Pod Remediation → Volume Anomaly Detection → Document Review Automation → BDD Test Generation | 4 hours |
| **.NET Cloud-Native** | .NET Monolith Decomposition → Cross-Service Integration Testing → Cross-Service Bug Investigation | 3 hours |
| **Data Modernization** | DW Migration: Teradata to Snowflake → ETL Pipeline Modernization → Data Quality & Validation | 3 hours |
| **Informatica to Snowflake** | Informatica PowerCenter Analysis → Informatica PowerCenter to Snowflake Migration | 2 hours |
| **AI/ML Workshop** | ML Pipeline Setup → Model Evaluation & Testing → LLM Integration Patterns | 3 hours |
| **Documentation Sprint** | Inline Documentation → API Documentation → Runbook Generation → Changelog & Release Notes | 2.5 hours |

See [workshops/](../workshops/) for pre-built workshop templates and [events/](../events/) for event-specific instances.
