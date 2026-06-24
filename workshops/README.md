# Workshops

Curated learning paths — reading material, training tracks, and hands-on lab sequences. Pick the workshop your facilitator assigned, or follow the progression model below.

## Progression Model

```
Level 1: foundations/        ← Everyone starts here (concepts + product features)
    ↓
Level 2: training-tracks/    ← Role-specific training (sales / solutions / engineering)
    ↓
Level 3: by-tech-domain/    ← Deep-dives by technology area
         by-tech-role/      ← Deep-dives by job function
    ↓
Practice: general/           ← Broad hands-on tour
          otterworks/        ← Advanced monorepo deep-dive
```

## Directory Structure

```
workshops/
├── foundations/              ← Level 1: Concepts + product features (everyone)
│   ├── concepts/            ← Cloud vs. local, event-driven, orchestration, task selection
│   └── product/             ← Feature tours organized by cloud / local
│       ├── cloud/           ← Devin Cloud, DeepWiki, Sessions, Automations, Multi-Agent
│       └── local/           ← Devin Desktop, Devin CLI
├── training-tracks/         ← Level 2: Role-specific training
│   ├── sales/               ← Operating model, cost structures, engagement capture
│   ├── solutions/           ← System design, SDLC integration, live walkthroughs
│   └── engineering/         ← Tactical platform usage, prompt engineering, orchestration
├── general/                 ← Hands-on broad tour (security, modernization, feature dev)
├── otterworks/              ← Advanced monorepo hands-on (300-level)
├── by-tech-domain/          ← Level 3: Deep-dives by technology area
│   ├── modernization/       ← COBOL, .NET, Framework Upgrades, Data Migration, Platform Decomp
│   ├── security/            ← Compliance, Enterprise Automation
│   └── ai-and-automation/   ← Agentic AI
└── by-tech-role/            ← Level 3: Deep-dives by job function
    ├── development/         ← App Maintenance, Feature Development
    ├── digital-engineering/ ← CI/CD, cloud infrastructure, observability
    └── quality-engineering/ ← Engineering, Engineering + Security
```

## How Labs Work

Every lab follows a 4-step format:

1. **Paste into Devin** — copy the prompt, kick off a session, and move on
2. **Research with Ask Devin** — use Ask Devin to explore the codebase while the session runs
3. **Explore with DeepWiki** *(optional)* — read the repo's auto-generated architecture docs
4. **Review & Give Feedback** — review the PR, leave comments, iterate with Devin

Labs reference individual modules from [`labs/`](../labs/) — the detailed challenge definitions.

---

## Level 1: Foundations

| Workshop | Focus | Duration |
|----------|-------|----------|
| [Foundations](foundations/) | Cloud agent concepts, product features, platform mental model | 2-3 hours |

Everyone starts here. Covers concepts (cloud vs. local, event-driven reactions, orchestration, task selection) and product features (Devin Cloud, Desktop, CLI, DeepWiki, Automations, Multi-Agent Workers).

---

## Level 2: Training Tracks

Role-specific training that builds on Foundations. See the [Tracks README](training-tracks/) for the full progression model.

| Track | Persona | Duration |
|-------|---------|----------|
| [Sales](training-tracks/sales/) | Sales Engineers, AEs, Practice Leads | 4-5 hours |
| [Solutions](training-tracks/solutions/) | Solutions Engineers, Architects | 8-10 hours |
| [Engineering](training-tracks/engineering/) | Engineers, Developers | 6-8 hours |

---

## Hands-On Workshops

### Start Here

| Workshop | Focus | Duration | Labs |
|----------|-------|----------|------|
| [General](general/) | Security, modernization, feature dev — broad tour | 4-6 hours | 9 (3 tracks x 3 labs) |
| [OtterWorks](otterworks/) | Polyglot microservices — modernization, incident response, security & quality (300-level) | 4-6 hours | 9 (3 tracks x 3 labs) |

### By Technical Domain

| Workshop | Focus | Duration |
|----------|-------|----------|
| [COBOL Modernization](by-tech-domain/modernization/cobol/) | End-to-end mainframe modernization | ~4 hours |
| [Legacy Modernization](by-tech-domain/modernization/legacy/) | COBOL/legacy → modern tech stack | 2-4 hours |
| [.NET Cloud-Native Modernization](by-tech-domain/modernization/dotnet-cloud-native/) | .NET monolith → Kubernetes-hosted cloud-native APIs | 2.5-3 hours |
| [Framework Upgrades](by-tech-domain/modernization/framework-upgrades/) | Angular + Spring Boot version upgrades | 1-2 hours |
| [Data Source Migration](by-tech-domain/modernization/data-source-migration/) | Legacy DW → modern schema | 1-2 hours |
| [Platform Microservice Decomposition](by-tech-domain/modernization/platform-decomposition/) | Monolith → platform-conformant microservices | 1.5-2 hours |
| [Security & Compliance](by-tech-domain/security/compliance/) | CVE remediation, SAST, shift-left | 1-2 hours |
| [Enterprise Security Automation](by-tech-domain/security/enterprise-automation/) | Event-driven SAST, mass remediation | ~4 hours |
| [Agentic AI](by-tech-domain/ai-and-automation/agentic-ai/) | Multi-agent systems, document processing | 2-4 hours |

### By Technical Role

| Workshop | Focus | Duration |
|----------|-------|----------|
| [Application Development & Maintenance](by-tech-role/development/app-maintenance/) | Feature dev, bug fixing, code maintenance | 4-6 hours |
| [Feature Development](by-tech-role/development/feature-development/) | New features on existing apps | 1-2 hours |
| [Digital Engineering](by-tech-role/digital-engineering/) | CI/CD, cloud infrastructure, observability | 4-6 hours |
| [Quality Engineering & Assurance](by-tech-role/quality-engineering/engineering/) | Test automation, E2E, continuous quality | 4-6 hours |
| [Quality Engineering & Security](by-tech-role/quality-engineering/engineering-security/) | QE + security remediation combined | ~3 hours |
