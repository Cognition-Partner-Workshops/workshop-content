# Workshop Metadata

Central index for the **Cognition-Partner-Workshops** GitHub org. This repo contains:

- **Repo Catalog** — inventory of all repositories with cross-references to challenges they support
- **Modular Challenges** — self-contained workshop tasks organized by SDLC domain
- **Workshops** — reusable workshop templates that compose challenges into structured lab sequences
- **Events** — specific workshop instances with date, location, audience, and facilitator overrides
- **Shared Resources** — naming conventions, facilitator guides, runtime resource docs

## Information Architecture

This repo uses a **layered approach** with three discovery routes:

```
┌─────────────────────────────────────────────────────────────┐
│                    WORKSHOP DESIGNER                         │
│                                                              │
│   "I need to build a 4-hour workshop on security + migration"│
│                                                              │
│   Route 1: Browse by Workshop                                │
│   workshops/ → pick a workshop template → customize for event│
│                                                              │
│   Route 2: Browse by Module                                  │
│   modules/ → pick category → pick challenges → see repos     │
│                                                              │
│   Route 3: Browse by Repo                                    │
│   catalog/repos.md → see what challenges each repo supports  │
│                                                              │
│   Compose: events/ → instantiate a workshop for a specific   │
│            date, location, and audience                       │
└─────────────────────────────────────────────────────────────┘
```

**Modules are the reusable atoms.** Workshops compose modules into structured lab sequences. Events instantiate workshops for specific audiences. Repos are the materials they all reference.

## Directory Structure

```
workshop-metadata/
├── README.md                              ← you are here
├── catalog/
│   └── repos.md                           ← master repo inventory + cross-refs
├── modules/                               ← challenge instructions by category
│   ├── README.md                          ← navigation index for all 77 modules
│   ├── application-development/           ← Software Developer, Full-Stack Engineer
│   │   ├── README.md
│   │   ├── gather-requirements.md
│   │   ├── test-driven-development.md
│   │   ├── fix-runtime-bug.md
│   │   ├── fix-ui-bug.md
│   │   ├── fix-data-bug.md
│   │   ├── new-feature-development.md
│   │   └── database-schema-evolution.md
│   ├── testing-qa/                        ← QA Engineer, SDET, Test Automation Engineer
│   │   ├── README.md
│   │   ├── linting-static-analysis.md
│   │   ├── unit-testing.md
│   │   ├── end-to-end-testing.md
│   │   ├── performance-testing.md
│   │   ├── accessibility-compliance.md
│   │   ├── bdd-test-generation.md
│   │   ├── contract-testing.md
│   │   ├── mutation-testing.md
│   │   ├── load-testing-benchmarking.md
│   │   ├── visual-regression-testing.md
│   │   └── cross-service-integration-testing.md
│   ├── security/                          ← Security Engineer, AppSec Engineer
│   │   ├── README.md
│   │   ├── upgrade-dependencies.md
│   │   ├── remediate-vulnerabilities.md
│   │   ├── shift-left-security.md
│   │   ├── security-antipatterns.md
│   │   ├── secrets-management-detection.md
│   │   ├── event-driven-sast-remediation.md
│   │   └── mass-security-backlog-remediation.md
│   ├── compliance-governance/             ← Compliance Officer, GRC Analyst
│   │   ├── README.md
│   │   ├── license-compliance-audit.md
│   │   ├── gdpr-pii-detection.md
│   │   └── regulatory-reporting.md
│   ├── devops-cicd/                       ← DevOps Engineer, Release Engineer
│   │   ├── README.md
│   │   ├── cicd-pipeline.md
│   │   ├── pr-review-automation.md
│   │   ├── ci-failure-resolution.md
│   │   ├── release-management.md
│   │   └── configuration-management-feature-flags.md
│   ├── cloud-infrastructure/              ← Cloud Engineer, Platform Engineer
│   │   ├── README.md
│   │   ├── iac-translation.md
│   │   ├── platform-conformant-microservice-decomposition.md
│   │   ├── gitops-argocd-setup.md
│   │   ├── kubernetes-manifest-generation.md
│   │   ├── terraform-module-extraction.md
│   │   └── cost-optimization-analysis.md
│   ├── observability-sre/                 ← SRE, Observability Engineer
│   │   ├── README.md
│   │   ├── observability-monitoring.md
│   │   ├── incident-response-triage.md
│   │   ├── pod-remediation-credential-rotation.md
│   │   └── volume-anomaly-detection.md
│   ├── data-engineering/                  ← Data Engineer, Analytics Engineer
│   │   ├── README.md
│   │   ├── dw-migration-teradata-to-snowflake.md
│   │   ├── data-source-migration.md
│   │   ├── etl-pipeline-modernization.md
│   │   ├── data-quality-validation.md
│   │   ├── sas-to-python-snowflake.md
│   │   ├── informatica-powercenter-analysis.md       ← NEW
│   │   └── informatica-to-snowflake-migration.md     ← NEW
│   ├── architecture-design/               ← Solution Architect, Enterprise Architect
│   │   ├── README.md
│   │   ├── architecture-decision-records.md
│   │   ├── api-design-review.md
│   │   ├── dependency-graph-analysis.md
│   │   ├── api-consolidation.md
│   │   └── code-refactoring-tech-debt.md
│   ├── ai-ml-engineering/                 ← ML Engineer, AI Engineer
│   │   ├── README.md
│   │   ├── ml-pipeline-setup.md
│   │   ├── model-evaluation-testing.md
│   │   └── llm-integration-patterns.md
│   ├── technical-documentation/           ← Technical Writer, Documentation Engineer
│   │   ├── README.md
│   │   ├── inline-documentation.md
│   │   ├── api-documentation.md
│   │   ├── document-review-automation.md
│   │   ├── runbook-generation.md
│   │   ├── onboarding-guide-generation.md
│   │   └── changelog-release-notes.md
│   ├── migration-modernization/           ← Modernization Specialist, Migration Lead
│   │   ├── README.md
│   │   ├── cobol-to-java.md
│   │   ├── cobol-system-understanding.md
│   │   ├── cobol-migration-planning.md
│   │   ├── migration-test-harness.md
│   │   ├── framework-upgrade.md
│   │   ├── repetitive-framework-upgrades.md
│   │   ├── containerization-microservice-extraction.md
│   │   ├── cloud-native-refactor.md
│   │   ├── legacy-modernization-combined.md
│   │   ├── one-shot-tech-debt-remediation.md
│   │   ├── dotnet-monolith-decomposition.md
│   │   ├── cross-service-bug-investigation.md
│   │   ├── oracle-forms-system-understanding.md
│   │   ├── oracle-forms-migration-planning.md
│   │   └── oracle-forms-to-java.md
│   └── devin-features/
│       └── README.md                      ← Devin-specific activities checklist
├── workshops/                             ← reusable workshop templates
│   ├── README.md
│   ├── legacy-modernization/
│   ├── framework-upgrades/
│   ├── data-source-migration/
│   ├── security-compliance/
│   ├── platform-microservice-decomposition/
│   ├── dotnet-cloud-native-modernization/
│   ├── agentic-ai/
│   ├── feature-development/
│   ├── quality-engineering/
│   ├── general/
│   ├── application-development-maintenance/
│   ├── digital-engineering/
│   ├── cobol-modernization/               ← promoted from events
│   ├── enterprise-security-automation/    ← promoted from events
│   └── quality-engineering-security/      ← promoted from events
├── events/                                ← specific workshop instances
│   ├── README.md
│   ├── _template/
│   ├── active/                            ← upcoming events
│   └── archive/                           ← past events
│       ├── 2026-03-09-oslo/
│       ├── 2026-03-09-san-francisco/
│       ├── 2026-03-13-washington-dc-2/
│       ├── 2026-03-17-zurich/
│       ├── 2026-03-25-remote-workshop/
│       ├── 2026-04-dc/
│       ├── 2026-04-09-virtual-workshop/
│       ├── 2026-05-05-general-workshop/
│       └── 2026-05-07-workshop/
└── shared/
    ├── repo-naming-convention.md
    ├── runtime-resources.md
    └── facilitator-guide.md
```

## Module Categories (79 modules across 12 disciplines)

| Category | Count | Discipline / Job Title |
|----------|-------|----------------------|
| [Application Development](modules/application-development/) | 7 | Software Developer, Full-Stack Engineer |
| [Testing & QA](modules/testing-qa/) | 11 | QA Engineer, SDET, Test Automation Engineer |
| [Security](modules/security/) | 7 | Security Engineer, AppSec Engineer |
| [Compliance & Governance](modules/compliance-governance/) | 3 | Compliance Officer, GRC Analyst |
| [DevOps & CI/CD](modules/devops-cicd/) | 5 | DevOps Engineer, Release Engineer |
| [Cloud & Infrastructure](modules/cloud-infrastructure/) | 6 | Cloud Engineer, Platform Engineer |
| [Observability & SRE](modules/observability-sre/) | 4 | SRE, Observability Engineer |
| [Data Engineering](modules/data-engineering/) | 7 | Data Engineer, Analytics Engineer |
| [Architecture & Design](modules/architecture-design/) | 5 | Solution Architect, Enterprise Architect |
| [AI & ML Engineering](modules/ai-ml-engineering/) | 3 | ML Engineer, AI Engineer |
| [Technical Documentation](modules/technical-documentation/) | 6 | Technical Writer, Documentation Engineer |
| [Migration & Modernization](modules/migration-modernization/) | 15 | Modernization Specialist, Migration Lead |
| [Devin Features](modules/devin-features/) | 1 | Cross-cutting Devin platform activities |

Browse all modules: [modules/README.md](modules/README.md)

## Quick Start for Facilitators

1. **Browse workshops** in `workshops/` to find a pre-built workshop that matches your audience
2. **Or pick modules** from `modules/` to build a custom workshop — see [modules/README.md](modules/README.md) for the full index
3. **Check repo requirements** in `catalog/repos.md` to see what needs to be set up
4. **Copy `events/_template/`** to `events/active/YYYY-MM-DD-<event-id>/` and fill in event details
5. **Review `shared/facilitator-guide.md`** for runtime setup and logistics

## Contributing

To add a new challenge module:
1. Create a markdown file in the appropriate `modules/<category>/` directory
2. Follow the 4-step format: Paste into Devin → Research with Ask Devin → Read the DeepWiki → Review & Give Feedback
3. Add cross-references in `catalog/repos.md` for any repos the challenge uses
4. Update the category `README.md` with the new challenge entry
5. Update `modules/README.md` navigation index
