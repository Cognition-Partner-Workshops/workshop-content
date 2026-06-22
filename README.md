# Hands-On Workshop Labs

Welcome to the Devin hands-on workshop. This repo contains everything you need to get started: lab instructions, prompts to paste into Devin, and links to the repositories you will work with.

## Quick Start

1. **Find your workshop** — check `workshops/` for your session's lab guide, or ask your facilitator for the direct link
2. **Pick a lab** — each lab has a prompt you copy-paste into a new Devin session
3. **Kick off and move on** — Devin works autonomously. Start the next lab while it runs
4. **Review the PR** — when Devin finishes, review its pull request and leave comments to iterate

> **Devin works autonomously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

## How to Navigate

```
workshop-instructions/
├── foundations/        ← theory, product features, platform mental model
│   ├── theory/        ← cloud agent concepts, event-driven patterns, task selection
│   └── product/       ← DeepWiki, Sessions, CLI, Automations, Multi-Agent Workers
├── labs/              ← individual lab instructions organized by discipline
│   └── README.md      ← full index of all labs
├── workshops/         ← structured lab sequences (your facilitator will point you here)
│   ├── by-tech-domain/    ← deep dives by technical domain (modernization, security, AI)
│   ├── by-technical-role/  ← deep dives by job family (development, QE, digital engineering)
│   ├── general/       ← broad multi-repo hands-on tour (start here)
│   └── otterworks/    ← advanced monorepo deep-dive (300-level)
├── demos/             ← facilitator-led showcases (follow along, single linear thread)
├── events/            ← event-specific agendas and customizations
├── catalog/           ← reference inventory of all available repositories
└── shared/
    ├── general-themes/       ← how Devin works: architecture, patterns, collaboration
    └── runtime-resources.md  ← how to run apps locally if needed
```

### Find Labs by Interest

| Category | What You'll Try | Labs |
|----------|----------------|------|
| [Application Development](labs/application-development/) | Build features, fix bugs, evolve schemas | 7 |
| [Testing & QA](labs/testing-qa/) | Linting, unit tests, E2E tests, performance, accessibility | 13 |
| [Security](labs/security/) | Dependency upgrades, vulnerability remediation, SAST | 7 |
| [Compliance & Governance](labs/compliance-governance/) | License audits, PII detection, regulatory reporting | 3 |
| [DevOps & CI/CD](labs/devops-cicd/) | Pipelines, PR automation, CI failure resolution | 5 |
| [Cloud & Infrastructure](labs/cloud-infrastructure/) | IaC translation, Kubernetes, Terraform, GitOps | 6 |
| [Observability & SRE](labs/observability-sre/) | Monitoring, incident response, anomaly detection | 4 |
| [Data Engineering](labs/data-engineering/) | DW migration, ETL modernization, data quality | 10 |
| [Architecture & Design](labs/architecture-design/) | ADRs, API review, dependency analysis, refactoring | 5 |
| [AI & ML Engineering](labs/ai-ml-engineering/) | ML pipelines, model evaluation, LLM patterns | 3 |
| [Technical Documentation](labs/technical-documentation/) | Inline docs, API docs, runbooks, changelogs | 6 |
| [Migration & Modernization](labs/migration-modernization/) | COBOL-to-Java, framework upgrades, containerization | 15 |

Browse all labs: [labs/README.md](labs/README.md)

### Find Labs by Workshop

Your facilitator may have directed you to a specific workshop. Each workshop bundles labs into a structured sequence.

#### Start Here

| Workshop | Focus | Duration |
|----------|-------|----------|
| [Foundations](foundations/) | Cloud agent theory, product features, platform mental model | 2-3 hours |
| [General](workshops/general/) | Security, modernization, feature dev — broad multi-repo tour | 4-6 hours |
| [OtterWorks](workshops/otterworks/) | Polyglot microservices — 300-level monorepo deep-dive | 4-6 hours |

#### By Technical Domain

Collections of labs targeting specific tech stacks and engineering challenges.

| Workshop | Focus | Duration |
|----------|-------|----------|
| [COBOL Modernization](workshops/by-tech-domain/modernization/cobol/) | End-to-end mainframe modernization | ~4 hours |
| [Legacy Modernization](workshops/by-tech-domain/modernization/legacy/) | COBOL/legacy → modern tech stack | 2-4 hours |
| [.NET Cloud-Native Modernization](workshops/by-tech-domain/modernization/dotnet-cloud-native/) | .NET monolith → Kubernetes-hosted cloud-native APIs | 2.5-3 hours |
| [Framework Upgrades](workshops/by-tech-domain/modernization/framework-upgrades/) | Angular + Spring Boot version upgrades | 1-2 hours |
| [Data Source Migration](workshops/by-tech-domain/modernization/data-source-migration/) | Legacy DW → modern schema | 1-2 hours |
| [Platform Microservice Decomposition](workshops/by-tech-domain/modernization/platform-decomposition/) | Monolith → platform-conformant microservices | 1.5-2 hours |
| [Security & Compliance](workshops/by-tech-domain/security/compliance/) | CVE remediation, SAST, shift-left | 1-2 hours |
| [Enterprise Security Automation](workshops/by-tech-domain/security/enterprise-automation/) | Event-driven SAST, mass remediation | ~4 hours |
| [Agentic AI](workshops/by-tech-domain/ai-and-automation/agentic-ai/) | Multi-agent systems, document processing | 2-4 hours |

#### By Technical Role

One workshop per job family — broad coverage of the role's engineering concerns.

| Workshop | Focus | Duration |
|----------|-------|----------|
| [Application Development & Maintenance](workshops/by-technical-role/development/app-maintenance/) | Feature dev, bug fixing, code maintenance | 4-6 hours |
| [Feature Development](workshops/by-technical-role/development/feature-development/) | New features on existing apps | 1-2 hours |
| [Digital Engineering](workshops/by-technical-role/digital-engineering/) | CI/CD, cloud infrastructure, observability | 4-6 hours |
| [Quality Engineering & Assurance](workshops/by-technical-role/quality-engineering/engineering/) | Test automation, E2E, continuous quality | 4-6 hours |
| [Quality Engineering & Security](workshops/by-technical-role/quality-engineering/engineering-security/) | QE + security remediation combined | ~3 hours |

## Tips for Getting the Most from Your Workshop

- **Start sessions early, review later.** Each lab has a "Paste into Devin" step and a separate "Review & Give Feedback" step. Kick off the session first, then use the wait time for Ask Devin research — Devin keeps working in the background.
- **Run parallel sessions.** Kick off the next lab while reviewing the previous one. This mirrors real enterprise usage where Devin handles work across many services simultaneously.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem before creating a session.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item during a session, accept it — this builds a shared context layer that makes Devin more effective over time.
- **Leave PR comments to steer Devin.** After Devin opens a PR, leave comments directly on the diff and Devin will wake up and address them — this is the core workflow for iterating with Devin.

## Running Apps Locally

Some labs require a running application. See [shared/runtime-resources.md](shared/runtime-resources.md) for local run instructions. Your facilitator will let you know if hosted instances are available.

## Repository Catalog

Looking for a specific repo? The [catalog](catalog/repos.md) lists all repositories in the workshop org with descriptions, tech stacks, and links to the labs that use them.

## Devin Features Checklist

Track your progress on the [Devin Features Appendix](labs/devin-features/README.md) — try to discover as many Devin capabilities as you can during the session.
