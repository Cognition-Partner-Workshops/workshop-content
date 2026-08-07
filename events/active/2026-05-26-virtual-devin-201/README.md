# Workshop: Devin 201 — Advanced Hands-On Workshop

## Table of Contents

- [Event Details](#event-details)
- [Workshop Overview](#workshop-overview)
- [Getting the Most from This Workshop](#getting-the-most-from-this-workshop)
- [Agenda](#agenda)
- [Devin 201 Concepts](#devin-201-concepts)
- [Facilitator Walkthrough](#facilitator-walkthrough)
- [Hands-On: Self-Select a Track](#hands-on-self-select-a-track)
  - [Track 1: Microservices](#track-1)
  - [Track 2: SRE](#track-2)
  - [Track 3: Test & QA](#track-3)
  - [Track 4: Version Upgrades](#track-4)
  - [Track 5: Mainframe Modernization](#track-5)
  - [Track 6: IaC](#track-6)
  - [Track 7: Sec Vulns & CVEs](#track-7)
- [Integrations Deep Dive](#integrations-deep-dive)
- [Take-Home Lab Exercise](#take-home-lab-exercise)
- [Prerequisites](#prerequisites)
- [Notes](#notes)
- [Post-Event](#post-event)

---

<a id="event-details"></a>

## Event Details

| | |
|---|---|
| **Date** | 2026-05-26 |
| **Location** | Virtual |
| **Host Organization** | *(customer)* |
| **Duration** | 2 hours |
| **Participants** | ~500 |
| **Audience** | Experienced Devin users ready for advanced scenarios across multiple engineering disciplines |
| **Tracks** | 7 self-select tracks (participants choose during hands-on time) |
| **Event Site** | TBD |

<a id="workshop-overview"></a>

## Workshop Overview

This is a Devin 201 workshop for experienced users who want deeper, more challenging scenarios. The session is facilitator-led with structured hands-on breaks where participants self-select from 7 tracks. Each track targets a different engineering discipline and provides one challenging live module plus additional take-home exercises.

The 7 tracks are:

| Track | Category | Live Module | Primary Repo |
|-------|----------|------------|--------------|
| **1. Microservices** | Architecture | Containerization & Microservice Extraction | `petclinic-microservices` |
| **2. SRE** | Observability | Incident Response & Triage | `timesheet-app` |
| **3. Test & QA** | Quality | BDD Test Generation | `uc-bdd-test-generation-cucumber` |
| **4. Version Upgrades** | Migration | Framework Upgrade | `petclinic-angular` |
| **5. Mainframe Modernization** | Legacy | COBOL System Understanding | `uc-legacy-modernization-cobol-to-java` |
| **6. IaC** | Infrastructure | Terraform Module Extraction | `timesheet-infra` |
| **7. Sec Vulns & CVEs** | Security | Upgrade Dependencies / Remediate Vulnerabilities | `uc-cve-remediation-regulatory-compliance` |

After the live session, participants can complete the take-home modules from their chosen track — and try modules from other tracks.

<a id="getting-the-most-from-this-workshop"></a>

## Getting the Most from This Workshop

> **Devin works autonomously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to exploring Ask Devin, reading DeepWiki, or browsing other tracks while it works. You'll get notified when it opens a PR.

Tips for the hands-on portions:

- **Kick off your session immediately.** The live hands-on window is ~15 minutes. Paste the prompt, then use the wait time for Ask Devin research or reading DeepWiki — Devin works in the background.
- **Try parallel sessions.** If you finish early, start a second session from a different track. Running multiple Devin sessions simultaneously mirrors real enterprise usage.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem before committing to a full session.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments directly and Devin will wake up and address them — this is the core workflow for iterating with Devin in production.
- **Build up Devin's knowledge.** When Devin suggests a Knowledge item during a session, accept it — this is how teams build a shared context layer that makes Devin smarter over time.

---

<a id="agenda"></a>

## Agenda

| Time | Activity | Section |
|------|----------|---------|
| 0:00–0:30 | **Devin 201 Concepts** — Use-case qualification, deployment models (cloud vs. local vs. CLI), comparison with other tools (Copilot, Cursor, etc.), Playbooks, Knowledge, Scheduled Sessions, Architecture overview | Presentation |
| 0:30–0:50 | **Facilitator Walkthrough** — Live walkthrough of a use case end-to-end | Walkthrough |
| 0:50–1:05 | **Hands-On: Self-Select a Track** — Pick a track, paste the prompt, kick off a session | Hands-On |
| 1:05–1:15 | **Debrief** — Discuss results, share observations | Discussion |
| 1:15–1:45 | **Integrations Deep Dive** — Repository permissions, repo indexing, Blueprints, Jira/Confluence/SonarQube/Datadog setup, context engineering, Playbook & Knowledge creation, Analytics | Presentation |
| 1:45–2:00 | **Take-Home Lab Exercise** — Instructions for completing 2–3 additional modules from other tracks after the session | Wrap-Up |

> **Pacing tip for facilitators:** During the hands-on window (0:50–1:05), participants should paste their chosen track's prompt within the first 2 minutes, then use the remaining time for Ask Devin exploration or DeepWiki research. By the debrief, most sessions will be in progress or opening PRs.

---

<a id="devin-201-concepts"></a>

## Devin 201 Concepts (0:00–0:30)

### Use Case Qualification

Help participants identify which tasks are best suited for Devin:

- **Event-driven work** — triggered by alerts, CI failures, ticket creation, or scheduled cadences
- **Large-scale repetitive tasks** — the same pattern applied across many repos, services, or files
- **Capacity-constrained work** — tasks that sit in backlogs because nobody has time (tech debt, test coverage, documentation)
- **Cross-cutting concerns** — security remediation, dependency upgrades, accessibility audits across an org

### Deployment Models

| Model | When to Use | Examples |
|-------|-------------|---------|
| **Cloud (Devin platform)** | Default for most work — autonomous execution on Devin's VM | PR creation, code analysis, test generation |
| **Local development** | When Devin needs access to local services, databases, or VPNs | Integration testing against local Docker Compose |
| **CLI integration** | Triggering Devin from scripts, CI pipelines, or webhooks | `devin-api` calls from GitHub Actions, scheduled cron |

> *Other tools in the space (GitHub Copilot, Cursor, etc.) focus on inline code completion. Devin handles autonomous, multi-step engineering tasks end-to-end — from reading the codebase to opening a PR.*

### Playbooks, Knowledge, Scheduled Sessions

- **Playbooks** — versioned, step-by-step procedures that encode methodology. Reuse across sessions and teams.
- **Knowledge** — persistent cross-session context. Project conventions, architecture decisions, environment details. Devin references these automatically.
- **Scheduled Sessions** — recurring Devin sessions on a cadence (daily dependency checks, weekly security scans, periodic documentation updates).

### Architecture Overview

- **Clean-room execution** — each session runs on an isolated Linux VM with its own filesystem, environment, and tools
- **Context retrieval** — Devin pulls from DeepWiki, Knowledge notes, repo structure, and MCP integrations to understand the problem
- **PR feedback loop** — Devin opens a PR, receives review comments, and iterates. CI checks and Devin Review provide automated feedback layers.
- **Child sessions** — divide-and-conquer for parallel work (e.g., upgrading 10 services simultaneously)

---

<a id="facilitator-walkthrough"></a>

## Facilitator Walkthrough (0:30–0:50)

The facilitator walks through a complete Devin workflow live, showing the audience the end-to-end flow:

1. **Define the task** — Use Ask Devin to research the codebase and refine requirements
2. **Start a session** — Paste a prompt, show Devin planning and executing
3. **Monitor progress** — Show the session timeline, Devin's thought process, tool usage
4. **Review the PR** — Walk through the diff, show how to leave comments for iteration
5. **Show the feedback loop** — Leave a PR comment, watch Devin respond and push updates

> **Facilitator note:** Choose a walkthrough use case that completes in ~10 minutes so there's time to narrate each step. The [Upgrade Dependencies](../../../modules/security/upgrade-dependencies.md) module with `timesheet-app` (resolving GitHub Issue #2) is a good fit — it's self-contained, visible, and finishes fast.

---

<a id="hands-on-self-select-a-track"></a>

## Hands-On: Self-Select a Track (0:50–1:05)

Participants choose one of the 7 tracks below and paste the prompt into a new Devin session. Each track is designed for ~15 minutes of active work (paste prompt + Ask Devin exploration). Devin continues working after the hands-on window ends.

---

<a id="track-1"></a>

### Track 1: Microservices

**Module:** [Containerization & Microservice Extraction](../../../modules/migration-modernization/containerization-microservice-extraction.md)
**Repository:** [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices)

Spring Cloud microservices version of PetClinic — multiple services (customers, visits, vets) with service discovery and API gateway.

#### Paste into Devin

```
Analyze the microservices architecture of
petclinic-microservices. Add Docker health checks to all
services, create a unified Docker Compose file with proper
networking, and add a reverse proxy (nginx or Traefik) in
front of the services. Ensure all services start and
communicate correctly.
```

#### While Devin works: try Ask Devin

- *"What services does petclinic-microservices have? What ports do they use? What's the service discovery mechanism?"*
- *"What health check endpoints does each service expose? If they don't have one, what's the best way to add one?"*

#### Key Takeaways

- Devin analyzes a multi-service architecture and identifies operational gaps (missing health checks, missing Compose, no reverse proxy)
- Containerization improvements are high-value, low-risk tasks — exactly the kind of operational work that sits in backlogs
- Reverse proxy configuration unifies multiple services behind a single entry point

---

<a id="track-2"></a>

### Track 2: SRE

**Module:** [Incident Response & Triage](../../../modules/observability-sre/incident-response-triage.md)
**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express application — create incident response materials by analyzing the application's architecture and failure modes.

#### Paste into Devin

```
Create incident response materials for timesheet-app:
(1) Analyze the application to identify common failure
modes (database issues, API errors, memory leaks,
dependency failures), (2) Create a RUNBOOK.md with
step-by-step response procedures for each failure mode,
(3) Add GitHub Issue templates for P1/P2/P3/P4 incidents
with fields for impact, timeline, and resolution,
(4) Create a health check script that tests all critical
endpoints.
```

#### While Devin works: try Ask Devin

- *"What are the most likely production failure modes for a Node.js/Express + SQLite application?"*
- *"What monitoring alerts should be configured to detect issues before users report them?"*

#### Key Takeaways

- Runbook generation from codebase analysis is a task that typically requires deep application knowledge — Devin compresses this into a single session
- Incident response materials (runbooks, issue templates, health checks) are high-value artifacts that teams rarely have time to create
- This pattern scales: schedule a recurring Devin session to update runbooks when the codebase changes

---

<a id="track-3"></a>

### Track 3: Test & QA

**Module:** [BDD Test Generation](../../../modules/testing-qa/bdd-test-generation.md)
**Repository:** [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber)

Cucumber BDD framework for testing REST APIs — Devin generates Gherkin feature files and executable step definitions.

#### Paste into Devin

```
Review the uc-bdd-test-generation-cucumber codebase.
This is a Cucumber BDD framework for testing REST APIs.
Add new Gherkin feature files that test a Petstore-style
API (pets CRUD: create, read, update, delete, list).
Include scenarios for: successful CRUD operations,
validation errors (missing required fields), not-found
cases, and pagination. Implement the corresponding step
definitions.
```

#### While Devin works: try Ask Devin

- *"What existing step definitions does uc-bdd-test-generation-cucumber have? Can I reuse them for new feature files?"*
- *"What's the best way to organize BDD scenarios — by feature, by endpoint, or by user journey?"*

#### Key Takeaways

- BDD scenario generation is a high-volume, pattern-driven task — Devin's consistency and speed add value by generating comprehensive test coverage
- Devin produces executable Cucumber tests, not just boilerplate — scenarios cover happy paths, error cases, and edge cases
- Encode BDD generation methodology as a Playbook for reuse across API projects

---

<a id="track-4"></a>

### Track 4: Version Upgrades

**Module:** [Framework Upgrade](../../../modules/migration-modernization/framework-upgrade.md)
**Repository:** [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular)

Angular front-end for PetClinic — uses an older Angular version with outdated dependencies and deprecated APIs.

#### Paste into Devin

```
Upgrade petclinic-angular to the latest Angular version.
Handle any breaking changes from the Angular update guide,
update all dependencies, fix deprecated APIs, and ensure
the app builds successfully.
```

#### While Devin works: try Ask Devin

- *"What Angular version is petclinic-angular currently on? What are the biggest breaking changes between that version and the latest?"*
- *"What deprecated APIs does petclinic-angular use? What are the modern replacements?"*

#### Key Takeaways

- Framework upgrades involve cascading dependency changes — Devin reads migration guides and resolves breaking changes systematically
- Angular upgrades often require multiple incremental version bumps — Devin handles the entire chain
- This pattern scales with child sessions: upgrade 10 Angular apps in parallel using the same Playbook

---

<a id="track-5"></a>

### Track 5: Mainframe Modernization

**Module:** [COBOL System Understanding](../../../modules/migration-modernization/cobol-system-understanding.md)
**Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)

COBOL mainframe credit card management application (CardDemo) — real COBOL batch programs, JCL, DB2 integration, copybooks.

#### Paste into Devin

```
Analyze the entire COBOL estate in
uc-legacy-modernization-cobol-to-java. Produce the
following artifacts:

1. APPLICATION_INVENTORY.md — list every program in
   app/cbl/ and sub-application directories with:
   filename, purpose (inferred from code), classification
   (batch/online), key I/O operations (files read/written,
   DB2 tables accessed), and copybooks referenced. Also
   catalog all JCL jobs in app/jcl/ with their step
   sequences.
2. DATA_DICTIONARY.md — for every copybook in app/cpy/,
   extract: field names, PIC clauses, data types, business
   meaning (inferred), and validation rules. Group fields
   by business entity (account, customer, card,
   transaction).
3. DEPENDENCY_MAP.md — build a call graph showing which
   programs CALL or PERFORM other programs. Map dataset
   lineage: which JCL jobs read/write which files, and
   which programs process them. Identify the end-to-end
   batch pipeline flow.
4. HOTSPOT_REPORT.md — rank the top 10 programs by: lines
   of code, number of copybooks referenced, number of I/O
   operations, business logic density (EVALUATE/IF nesting
   depth), and inter-program dependencies. Recommend which
   programs to modernize first and why.
```

#### While Devin works: try Ask Devin

- *"What business domains does the CardDemo COBOL application cover? How are they organized across programs?"*
- *"Which copybooks are shared across the most programs? These are likely core data models."*

#### Key Takeaways

- Understanding the existing system is typically the biggest blocker in COBOL modernization — Devin compresses weeks of SME interviews into a single session
- Copybook analysis reveals the hidden data model of a mainframe application
- Hotspot detection prioritizes where to focus modernization effort — high-complexity, high-dependency modules first

---

<a id="track-6"></a>

### Track 6: IaC

**Module:** [Terraform Module Extraction](../../../modules/cloud-infrastructure/terraform-module-extraction.md)
**Repository:** [timesheet-infra](https://github.com/Cognition-Partner-Workshops/timesheet-infra)

AWS infrastructure for hosting a Node.js application — Terraform configurations covering EC2, ECR, networking, and IAM.

#### Paste into Devin

```
Analyze the Terraform configurations in timesheet-infra
and refactor them into reusable modules. Extract at least
3 modules: networking (VPC, subnets, security groups),
compute (EC2 instance, IAM roles), and container registry
(ECR repository, lifecycle policies). Each module should
have clear input variables, outputs, and a README with
usage examples. Refactor the root module to use the
extracted child modules. Ensure `terraform plan` shows no
changes after refactoring.
```

#### While Devin works: try Ask Devin

- *"What Terraform resources exist in timesheet-infra? Which ones are tightly coupled and which can be cleanly extracted?"*
- *"Are there any Terraform state considerations when extracting modules? How do we ensure the refactoring doesn't trigger resource recreation?"*

#### Key Takeaways

- Devin identifies repeated patterns and natural module boundaries in infrastructure code
- Module extraction preserves behavior while improving reusability — `terraform plan` confirms zero-change refactoring
- This pattern scales: apply the same module structure across an org's Terraform repos using a Playbook

---

<a id="track-7"></a>

### Track 7: Sec Vulns & CVEs

**Modules:** [Upgrade Dependencies](../../../modules/security/upgrade-dependencies.md), [Remediate Vulnerabilities](../../../modules/security/remediate-vulnerabilities.md)
**Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)

Spring Boot 2.6.3 application with 18+ known CVEs — pre-configured with OWASP Dependency-Check and SonarQube Gradle plugins.

#### Paste into Devin

```
Upgrade uc-cve-remediation-regulatory-compliance from
Spring Boot 2.6.3 to the latest stable 2.7.x or 3.x,
updating all transitive dependencies. Run
./gradlew dependencyCheckAnalyze before and after to
document which CVEs are resolved. Verify the build
still passes.
```

#### While Devin works: try Ask Devin

- *"What are the known CVEs in uc-cve-remediation-regulatory-compliance's dependencies? Which are CRITICAL?"*
- *"What's the safest upgrade path for Spring Boot 2.6.3 — 2.7.x first or straight to 3.x?"*

#### Key Takeaways

- Devin interprets CVE databases and SAST output to prioritize and fix the most critical vulnerabilities first
- The scan → fix → re-scan verification pattern proves that remediation actually resolves findings
- Spring Boot 2.x → 3.x involves major namespace changes (javax → jakarta) — Devin handles the migration systematically
- Schedule recurring Devin sessions to scan for new CVEs and auto-remediate, keeping dependencies current

---

<a id="integrations-deep-dive"></a>

## Integrations Deep Dive (1:15–1:45)

### Topics

1. **Repository Permissions** — How to grant Devin access to repos, what permissions are needed
2. **Repo Indexing** — How Devin indexes repos for DeepWiki and Ask Devin context
3. **Blueprints** — Configuring the dev environment (dependencies, tools, services) that Devin starts with each session
4. **Jira, Confluence, SonarQube, Datadog** — MCP integrations for reading tickets, documentation, scan results, and monitoring data
5. **Context Engineering** — Structuring Knowledge notes, AGENTS.md, and repo documentation so Devin has the right context
6. **Playbook & Knowledge Creation** — Building reusable Playbooks for repeatable tasks and Knowledge items for persistent context
7. **Analytics** — Tracking session outcomes, ACU usage, and team productivity metrics

---

<a id="take-home-lab-exercise"></a>

## Take-Home Lab Exercise (1:45–2:00)

After the session, complete **2–3 additional modules** from tracks other than the one you did live. This broadens your experience across different Devin use cases.

### Track 1: Microservices — Additional Modules

| Module | Repo | Difficulty | Time |
|--------|------|-----------|------|
| [.NET Monolith Decomposition](../../../modules/migration-modernization/dotnet-monolith-decomposition.md) | `quickapp-monolith` → `quickapp-microservices` | Intermediate–Advanced | 75 min |
| [Cross-Service Bug Investigation](../../../modules/migration-modernization/cross-service-bug-investigation.md) | `quickapp-microservices` | Intermediate | 45 min |
| [Contract Testing](../../../modules/testing-qa/contract-testing.md) | `petclinic-microservices` | Intermediate–Advanced | 60 min |

### Track 2: SRE — Additional Modules

| Module | Repo | Difficulty | Time |
|--------|------|-----------|------|
| [Pod Remediation After Credential Rotation](../../../modules/observability-sre/pod-remediation-credential-rotation.md) | `uc-pod-remediation-credential-rotation` | Advanced | 60 min |
| [Volume Anomaly Detection](../../../modules/observability-sre/volume-anomaly-detection.md) | `uc-volume-anomaly-detection` | Intermediate–Advanced | 60 min |
| [Observability & Monitoring](../../../modules/observability-sre/observability-monitoring.md) | `timesheet-app` | Intermediate–Advanced | 60 min |

### Track 3: Test & QA — Additional Modules

| Module | Repo | Difficulty | Time |
|--------|------|-----------|------|
| [End-to-End Testing](../../../modules/testing-qa/end-to-end-testing.md) | `timesheet-app` | Intermediate | 60 min |
| [Contract Testing](../../../modules/testing-qa/contract-testing.md) | `petclinic-microservices` | Intermediate–Advanced | 60 min |
| [Test Framework Migration](../../../modules/testing-qa/test-framework-migration.md) | `petclinic-angular` | Intermediate | 60 min |

### Track 4: Version Upgrades — Additional Modules

| Module | Repo | Difficulty | Time |
|--------|------|-----------|------|
| [Repetitive Framework Upgrades](../../../modules/migration-modernization/repetitive-framework-upgrades.md) | `petclinic-angular` + `ts-angular-realworld` | Intermediate | 60 min |
| [Framework Upgrade (Spring Boot)](../../../modules/migration-modernization/framework-upgrade.md#uc-spring-boot-upgrade-microservice-extraction) | `uc-spring-boot-upgrade-microservice-extraction` | Intermediate | 60 min |
| [One-Shot Tech Debt Remediation](../../../modules/migration-modernization/one-shot-tech-debt-remediation.md) | `uc-spring-boot-upgrade-microservice-extraction` | Advanced | 75 min |

### Track 5: Mainframe Modernization — Additional Modules

| Module | Repo | Difficulty | Time |
|--------|------|-----------|------|
| [COBOL Migration Planning](../../../modules/migration-modernization/cobol-migration-planning.md) | `uc-legacy-modernization-cobol-to-java` | Intermediate–Advanced | 60 min |
| [COBOL to Java](../../../modules/migration-modernization/cobol-to-java.md) | `uc-legacy-modernization-cobol-to-java` | Intermediate–Advanced | 60 min |
| [Migration Test Harness](../../../modules/migration-modernization/migration-test-harness.md) | `uc-legacy-modernization-cobol-to-java` | Intermediate–Advanced | 60 min |

### Track 6: IaC — Additional Modules

| Module | Repo | Difficulty | Time |
|--------|------|-----------|------|
| [IaC Translation](../../../modules/cloud-infrastructure/iac-translation.md) | `timesheet-infra` | Intermediate | 45 min |
| [Kubernetes Manifest Generation](../../../modules/cloud-infrastructure/kubernetes-manifest-generation.md) | `timesheet-infra` | Intermediate–Advanced | 60 min |
| [Cost Optimization Analysis](../../../modules/cloud-infrastructure/cost-optimization-analysis.md) | `timesheet-infra` | Intermediate | 45 min |

### Track 7: Sec Vulns & CVEs — Additional Modules

| Module | Repo | Difficulty | Time |
|--------|------|-----------|------|
| [Remediate Vulnerabilities](../../../modules/security/remediate-vulnerabilities.md) | `timesheet-app` | Intermediate | 60 min |
| [Shift Left Security](../../../modules/security/shift-left-security.md) | `uc-cve-remediation-regulatory-compliance` | Intermediate–Advanced | 60 min |
| [Security Antipatterns](../../../modules/security/security-antipatterns.md) | `uc-cve-remediation-regulatory-compliance` | Intermediate | 45 min |

---

<a id="prerequisites"></a>

## Prerequisites

### Repos Required on Devin's Machine

- [ ] [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices)
- [ ] [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- [ ] [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber)
- [ ] [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular)
- [ ] [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- [ ] [timesheet-infra](https://github.com/Cognition-Partner-Workshops/timesheet-infra)
- [ ] [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- [ ] [quickapp-monolith](https://github.com/Cognition-Partner-Workshops/quickapp-monolith) *(take-home)*
- [ ] [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices) *(take-home)*
- [ ] [uc-pod-remediation-credential-rotation](https://github.com/Cognition-Partner-Workshops/uc-pod-remediation-credential-rotation) *(take-home)*
- [ ] [uc-volume-anomaly-detection](https://github.com/Cognition-Partner-Workshops/uc-volume-anomaly-detection) *(take-home)*
- [ ] [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld) *(take-home)*
- [ ] [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) *(take-home)*

### Participant Requirements

- [ ] Devin account access
- [ ] Browser (Chrome recommended)
- [ ] Familiarity with Devin basics (session creation, PR review, Ask Devin) — this is a 201 workshop

<a id="notes"></a>

## Notes

- **Audience context:** These are experienced Devin users (201 level). Skip basic Devin onboarding — jump straight to use-case qualification and advanced patterns.
- **Self-select model:** During the hands-on window, participants choose one track. This works well at scale (~500 attendees) because it avoids bottlenecking everyone on the same repo.
- **Facilitator walkthrough repo:** The [Upgrade Dependencies](../../../modules/security/upgrade-dependencies.md) module with `timesheet-app` (GitHub Issue #2) is recommended for the live walkthrough — it's self-contained, completes in ~10 minutes, and shows the full Devin workflow (Issue → session → PR → review → iterate).
- **Take-home expectation:** Participants should complete 2–3 additional modules from other tracks after the session. The take-home section is designed to encourage exploration across all 7 categories.
- **Parallel sessions at scale:** With ~500 attendees, expect high concurrency. The 7-track model distributes load across different repos.

<a id="post-event"></a>

## Post-Event

- [ ] Collect participant feedback
- [ ] Archive event to `events/archive/`
- [ ] Update module files if issues were discovered during the workshop
- [ ] Share take-home exercise links with participants
- [ ] Track completion metrics across the 7 tracks
