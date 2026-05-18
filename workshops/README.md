# Workshops

Reusable workshop definitions that can be instantiated as events. Each workshop is a self-contained reference describing the labs, modules, repos, and structure needed to run a specific type of hands-on session.

## Workshop vs. Event

| | Workshop | Event |
|---|---|---|
| **Purpose** | Reusable reference template | Specific instance with date, location, audience |
| **Location** | `workshops/` | `events/` |
| **Contains** | Labs, modules, repos, structure, timing | Links to a workshop + facilitator overrides, audience context, event-specific customizations |
| **Example** | `workshops/legacy-modernization/` | `events/active/2026-06-15-new-york/` |

## How It Works

1. **Workshops define the content.** Each workshop has a set of labs with the 4-step format (Get Started Fast, Level Up with AskDevin, Explore with DeepWiki, Review PR and Give Feedback), required repos, timing guidelines, and target outcomes.

2. **Events instantiate workshops.** An event links to one or more workshops, adds event-specific details (date, location, audience context), and can override any workshop content for that specific instance.

3. **Modules are the atoms.** Both workshops and events reference modules from `modules/` — the individual challenge definitions that contain the detailed instructions.

## Available Workshops

| Workshop | Focus | Duration | Labs | Key Modules |
|----------|-------|----------|------|-------------|
| [Legacy Modernization](legacy-modernization/) | COBOL/legacy → modern tech stack with testing | 2-4 hours | 1-4 | MM1, MM10, MM12-MM14 |
| [Framework Upgrades](framework-upgrades/) | Angular + Spring Boot version upgrades at scale | 1-2 hours | 1-2 | MM2, MM9 |
| [Data Source Migration](data-source-migration/) | Legacy data warehouse → modern schema + app reconnection | 1-2 hours | 1-2 | MM8 |
| [Security & Compliance](security-compliance/) | CVE remediation, SAST, shift-left security | 1-2 hours | 1-3 | SEC1-SEC7 |
| [Platform Microservice Decomposition](platform-microservice-decomposition/) | Monolith → platform-conformant microservices with IaC | 1.5-2 hours | 1 | DA8 |
| [Agentic AI](agentic-ai/) | Multi-agent systems, document processing, anomaly detection | 2-4 hours | 2-4 | DA9, DA10, QE8, QE9 |
| [Application Development & Maintenance](application-development-maintenance/) | Feature development, bug fixing, and code maintenance — full app lifecycle | 4-6 hours | 9 (3 tracks × 3 labs) | Application Development, Architecture Design, Security modules |
| [Digital Engineering](digital-engineering/) | CI/CD, cloud infrastructure, observability, and platform engineering | 4-6 hours | 9 (3 tracks × 3 labs) | DevOps-CICD, Cloud Infrastructure, Observability-SRE modules |
| [Feature Development](feature-development/) | New features on existing applications | 1-2 hours | 1-2 | Application Development modules |
| [Quality Engineering & Assurance](quality-engineering/) | Test automation, E2E testing, continuous quality, and code review | 4-6 hours | 9 (3 tracks × 3 labs) | Testing & QA, DevOps-CICD, Technical Documentation modules |
| [General](general/) | Security, modernization, and feature development — 3-track broad tour | 4-6 hours | 9 (3 tracks × 3 labs) | Security, Migration-Modernization, Application Development, Testing & QA modules |
| [COBOL Modernization](cobol-modernization/) | End-to-end COBOL mainframe modernization — system understanding through migration execution | ~4 hours | 4 | COBOL System Understanding, COBOL to Java modules |
| [Enterprise Security Automation](enterprise-security-automation/) | Event-driven SAST remediation, mass backlog remediation, and one-shot tech debt | ~4 hours | 3 | Event-Driven SAST Remediation, Mass Security Backlog Remediation modules |
| [Quality Engineering & Security](quality-engineering-security/) | Quality engineering + security vulnerability remediation combined | ~3 hours | 4 | Linting, Unit Testing, Upgrade Dependencies, Remediate Vulnerabilities modules |
| [OtterWorks](otterworks/) | Polyglot microservices — modernization, incident response, security & quality (300-level) | 4-6 hours | 9 (3 tracks × 3 labs) | ETL Modernization, Framework Upgrade, Language Translation, Incident Response, Security, Contract Audit, Test Coverage modules |

## Creating a New Workshop

1. Create a new directory under `workshops/` with a descriptive slug (e.g., `workshops/cloud-native-transformation/`)
2. Add a `README.md` following the structure of any existing workshop
3. Define labs using the 4-step format
4. List required modules and repos
5. Add timing guidelines for different duration variants

## Creating an Event from a Workshop

1. Copy `events/_template/` to `events/active/YYYY-MM-DD-<event-id>/`
2. Reference the workshop(s) this event is based on
3. Add event-specific details (date, location, audience context)
4. Override any workshop content as needed for this specific audience
5. List which repos need to be set up on Devin's machine
