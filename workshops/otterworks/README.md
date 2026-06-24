# Workshop: OtterWorks

## Overview

| | |
|---|---|
| **Focus** | Directing Devin on messy, interconnected enterprise problems across a polyglot microservices platform |
| **Duration** | 4-8 hours (participants choose a track and complete 2-3 labs) |
| **Audience** | Engineers who have completed a General Devin workshop |
| **Prerequisite** | 200-level Devin proficiency — comfortable with prompting, PR review, iterative feedback, and Ask Devin |
| **Level** | 300 — application-specific, multi-lab composition |
| **Tracks** | **A: Modernization & Migration** · **B: Incident Response & Reliability** · **C: Security & Quality** · **D: DevOps & CI/CD** · **E: Application Development** · **F: Architecture & Documentation** |

## Workshop Narrative

```
General Workshop (200)  ──>  OtterWorks Workshop (300)
(learn Devin basics)         (direct Devin on messy, interconnected
                              enterprise problems in a real codebase)
```

This is a **300-level** workshop. Participants are expected to have completed at least one General Devin workshop (200-level) and be comfortable with core workflows: creating sessions, reviewing PRs, giving feedback, and using Ask Devin for research.

## Getting the Most from This Workshop

> **Devin works asynchronously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Craft your own prompts.** Unlike the General workshop, this workshop does not give you copy-paste prompts. You must decompose the problem and figure out how to direct Devin.
- **Start sessions early, review later.** Kick off the session first, then use the wait time for Ask Devin research — Devin keeps working in the background.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments and Devin will wake up and address them — this is the core feedback loop.
- **Try parallel sessions.** Running multiple sessions simultaneously mirrors real enterprise usage and demonstrates team-based operation at scale.

<a id="table-of-contents"></a>

## Table of Contents

- [Interact with the System](#interact-with-the-system-5-min)
- [Getting Oriented](#getting-oriented-10-min--do-this-first)
- [Track A: Modernization & Migration](#track-a-modernization--migration)
  - [Lab A1 — ETL Pipeline Modernization](#lab-a1--etl-pipeline-modernization-60-90-min)
  - [Lab A2 — Report Service Framework Upgrade](#lab-a2--report-service-framework-upgrade-60-90-min)
  - [Lab A3 — Language Translation](#lab-a3--language-translation-60-90-min)
- [Track B: Incident Response & Reliability](#track-b-incident-response--reliability)
  - [Lab B1 — Investigate Production Incident](#lab-b1--investigate-production-incident-45-60-min)
  - [Lab B2 — Complete the Runbooks](#lab-b2--complete-the-runbooks-45-60-min)
  - [Lab B3 — Add Observability to Under-Instrumented Services](#lab-b3--add-observability-to-under-instrumented-services-45-60-min)
- [Track C: Security & Quality](#track-c-security--quality)
  - [Lab C1 — Monorepo Security Sprint](#lab-c1--monorepo-security-sprint-60-90-min)
  - [Lab C2 — API Contract Audit](#lab-c2--api-contract-audit-45-60-min)
  - [Lab C3 — Test Coverage Blitz](#lab-c3--test-coverage-blitz-45-60-min)
- [Track D: DevOps & CI/CD](#track-d-devops--cicd)
  - [Lab D1 — Monorepo CI Pipeline Audit](#lab-d1--monorepo-ci-pipeline-audit-45-60-min)
  - [Lab D2 — Infrastructure as Code Review](#lab-d2--infrastructure-as-code-review-45-60-min)
  - [Lab D3 — Event-Driven SAST Pipeline](#lab-d3--event-driven-sast-pipeline-45-60-min)
- [Track E: Application Development](#track-e-application-development)
  - [Lab E1 — Cross-Service Bug Hunt](#lab-e1--cross-service-bug-hunt-45-60-min)
  - [Lab E2 — API Gateway Route Completion](#lab-e2--api-gateway-route-completion-45-60-min)
  - [Lab E3 — Synthetic Test Data Generation](#lab-e3--synthetic-test-data-generation-45-60-min)
- [Track F: Architecture & Documentation](#track-f-architecture--documentation)
  - [Lab F1 — Architecture Decision Records](#lab-f1--architecture-decision-records-45-60-min)
  - [Lab F2 — API Flow Test Expansion](#lab-f2--api-flow-test-expansion-45-60-min)
  - [Lab F3 — Technical Documentation Generation](#lab-f3--technical-documentation-generation-45-60-min)
- [Choosing a Track](#choosing-a-track)
- [Duration Variants](#duration-variants)
- [Lab Quick Reference](#lab-quick-reference)

---

OtterWorks is a polyglot microservices platform for real-time collaborative document editing and file management. It has **10 backend services** (Go, Java, Rust, Python x2, Node.js, Kotlin, Scala, Ruby, C#), **2 frontends** (React, Angular), and a full observability stack (Prometheus, Grafana, Jaeger). It is intentionally messy: legacy ETL scripts, outdated dependencies, incomplete runbooks, drifting API contracts, planted security vulnerabilities, missing API gateway routes, documented bugs, and gaps in CI/CD coverage.

Unlike the General workshop where participants paste provided prompts, this workshop requires participants to **craft their own prompts**. Each lab describes what is wrong, where to look, and what done looks like. Participants must decompose the problem and figure out how to direct Devin to fix it.

## Repository

[otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — Polyglot microservices platform (Rust, Python, Node.js, Java, Go, Kotlin, Scala, Ruby, C#)

---

## Interact with the System (5 min)

Before diving into prompts and labs, get the OtterWorks platform running and click around.

**If a hosted instance is available (live event):**

> Your facilitator will provide the URL. Open the **web app** and the **admin dashboard** in separate tabs. Create a document, upload a file, and try a search to see the system working end-to-end.

**If running locally (self-paced or on Devin's machine):**

```bash
# Clone and start the full stack
git clone https://github.com/Cognition-Partner-Workshops/otterworks.git
cd otterworks
docker compose up -d
```

Once services are healthy (~2 min):
- **Web app:** http://localhost:3000 — register, create documents, upload files, search
- **Admin dashboard:** http://localhost:4200 — view system health, user management, chaos scenarios, **incident management & Devin investigation triggers**
- **Grafana:** http://localhost:3001 — dashboards for service metrics, chaos scenarios, incident overview
- **Jaeger:** http://localhost:16686 — distributed traces across services
- **Prometheus:** http://localhost:9090 — raw metrics and alert rules

Spend a few minutes exploring the web app and admin dashboard. This gives you a feel for what OtterWorks does as a product before you start working on the code.

---

## Getting Oriented (10 min — do this first)

OtterWorks is a large polyglot codebase. Before jumping into a lab, spend 10 minutes using **Ask Devin** to explore the repo and build a mental model. Open Ask Devin, point it at the otterworks repo, and try these prompts:

> **"What does OtterWorks do? Describe the high-level architecture — what are the services, what language is each one written in, and how do they connect?"**

This gives you the map. You will learn that there is an API gateway (Go) routing to backend services, two frontends, and infrastructure like PostgreSQL, Redis, MeiliSearch, and DynamoDB via LocalStack.

> **"Walk me through what happens when a user creates a document. Which services are involved and what does the request flow look like?"**

This grounds the architecture in a concrete flow. You will see the api-gateway route the request to the document-service (Python/FastAPI), which writes to PostgreSQL and publishes an event to SNS. The collab-service (Node.js) picks up the event for real-time sync, and the search-service (Python/Flask) indexes it in MeiliSearch.

> **"What observability tools does OtterWorks use? Where are the Grafana dashboards, Prometheus alerts, and Jaeger tracing configured?"**

This is especially useful if you are planning Track B (Incident Response) or Track D (DevOps), but helps everyone understand how the system is monitored.

> **"What parts of this codebase look like they need work? Are there legacy scripts, outdated dependencies, incomplete docs, or known issues?"**

This is the discovery prompt. Devin will surface the ETL legacy scripts, the outdated report-service, the incomplete runbooks, the planted security vulnerabilities, the drifting API contracts, the missing gateway routes, and the documented bugs — which are exactly the problems the labs address.

Once you have a sense of the codebase, pick your track and start a lab.

---

## Track A: Modernization & Migration

Focus: Taking legacy or outdated parts of the codebase and bringing them to modern standards.

### Lab A1 — ETL Pipeline Modernization (60-90 min)

- **Lab Guide:** [A1-etl-modernization.md](A1-etl-modernization.md)
- **Objective:** Migrate legacy cron-based ETL scripts to Apache Airflow DAGs

**Key Takeaways:**
- Devin can read a legacy script AND a migration guide, then produce a complete Airflow DAG that preserves business logic
- Cron-to-orchestrator migration is a common enterprise pattern that Devin handles well
- The before/after is dramatic: monolithic scripts with hardcoded credentials vs. modular DAGs with proper connection management

---

### Lab A2 — Report Service Framework Upgrade (60-90 min)

- **Lab Guide:** [A2-framework-upgrade.md](A2-framework-upgrade.md)
- **Objective:** Upgrade a Java 8 / Spring Boot 2.5 service to Java 17 / Spring Boot 3.2+

**Key Takeaways:**
- Devin can execute multi-axis framework upgrades (Java version, Spring Boot, javax-to-jakarta, test framework, PDF library, etc.) in a single session
- Having a robust test suite before upgrading is critical — the tests serve as the verification harness
- Enterprise Java upgrades involve cascading changes that Devin tracks systematically

---

### Lab A3 — Language Translation (60-90 min)

- **Lab Guide:** [A3-language-translation.md](A3-language-translation.md)
- **Objective:** Translate the search-service from Flask (synchronous) to FastAPI (async)

**Key Takeaways:**
- Devin can translate between Python web frameworks while preserving API contracts
- OpenAPI specs serve as the contract that must be preserved during translation
- Contract tests provide automated verification that the translation is correct

---

## Track B: Incident Response & Reliability

Focus: Investigating production incidents, building runbooks, and improving observability.

### Lab B1 — Investigate Production Incident (45-60 min)

- **Lab Guide:** [B1-investigate-incident.md](B1-investigate-incident.md)
- **Objective:** Trigger a chaos scenario and experience three levels of incident response automation — manual, one-click, and fully automatic — controlled by an Auto-Investigate toggle

**Key Takeaways:**
- Devin can correlate signals across dashboards, logs, and traces to identify root causes
- Different incident signatures (error spikes vs. latency spikes) require different investigation approaches
- **Event-driven pattern:** Grafana alerts → webhook → auto-created incidents → Devin API sessions show "Devin as an always-on on-call engineer"
- The Auto-Investigate toggle lets teams experience the escalation from manual → one-click → fully automatic, showing how to adopt Devin incrementally

---

### Lab B2 — Complete the Runbooks (45-60 min)

- **Lab Guide:** [B2-complete-runbooks.md](B2-complete-runbooks.md)
- **Objective:** Use Devin to fill in incomplete incident runbooks based on codebase knowledge

**Key Takeaways:**
- Devin can generate investigation and resolution procedures by reading the service code, alert rules, and architecture docs
- Runbooks grounded in actual code are more accurate than runbooks written from memory
- This is a realistic SRE task that Devin accelerates significantly

---

### Lab B3 — Add Observability to Under-Instrumented Services (45-60 min)

- **Lab Guide:** [B3-add-observability.md](B3-add-observability.md)
- **Objective:** Identify services with weak observability coverage and add structured logging, metrics, or tracing

**Key Takeaways:**
- Devin can audit an entire service for observability gaps and add instrumentation systematically
- Polyglot services each have different instrumentation libraries — Devin handles this naturally
- Consistent metric naming and structured logging are critical for cross-service correlation

---

## Track C: Security & Quality

Focus: Finding and fixing security vulnerabilities, contract drift, and test coverage gaps.

### Lab C1 — Monorepo Security Sprint (60-90 min)

- **Lab Guide:** [C1-security-sprint.md](C1-security-sprint.md)
- **Objective:** Run security scans, triage findings, and remediate CRITICAL/HIGH CVEs using parallel Devin sessions

**Key Takeaways:**
- Devin can interpret security scan output and remediate vulnerabilities across multiple languages
- Triaging `.trivyignore` entries for overly broad suppressions is a real-world security hygiene task
- Parallel Devin sessions can tackle different services simultaneously for a monorepo security sprint

---

### Lab C2 — API Contract Audit (45-60 min)

- **Lab Guide:** [C2-contract-audit.md](C2-contract-audit.md)
- **Objective:** Find and fix mismatches between OpenAPI specs, event schemas, and actual service implementations

**Key Takeaways:**
- Contract drift between specs and implementations is a common source of production bugs
- Devin can compare a spec to an implementation and identify exact discrepancies
- Event schema drift (camelCase vs. snake_case, optional vs. required fields) is subtle but impactful

---

### Lab C3 — Test Coverage Blitz (45-60 min)

- **Lab Guide:** [C3-test-coverage.md](C3-test-coverage.md)
- **Objective:** Identify the service with weakest test coverage and add meaningful tests

**Key Takeaways:**
- Devin can analyze test coverage reports and prioritize which code paths to test
- Tests written by Devin follow the existing test patterns in the codebase
- Coverage metrics alone do not measure quality — meaningful assertions matter more than line coverage

---

## Track D: DevOps & CI/CD

Focus: Understanding, auditing, and improving the monorepo CI/CD pipeline, infrastructure as code, and automated security workflows.

### Lab D1 — Monorepo CI Pipeline Audit (45-60 min)

- **Lab Guide:** [D1-ci-pipeline-audit.md](D1-ci-pipeline-audit.md)
- **Objective:** Audit the CI/CD pipeline for documentation drift, change-detection gaps, and inconsistent service coverage

**Key Takeaways:**
- Monorepo CI strategies require careful change-detection to avoid rebuilding everything on every commit
- Documentation drift between CI strategy docs and actual workflows is a real operational risk
- Shared artifacts (`shared/openapi/`, `shared/events/schemas/`) create cross-service dependencies that naive path filters miss
- Devin can compare documentation against implementation and identify discrepancies systematically

---

### Lab D2 — Infrastructure as Code Review (45-60 min)

- **Lab Guide:** [D2-infrastructure-review.md](D2-infrastructure-review.md)
- **Objective:** Audit Terraform modules and Helm charts for misconfigurations, missing best practices, and inconsistencies

**Key Takeaways:**
- IaC grows incrementally and accumulates inconsistencies — Devin can audit across modules for patterns
- Terraform and Helm have distinct best-practice checklists (variable validation, tagging, health probes, security contexts) that Devin checks systematically
- OtterWorks has two Terraform layers (platform + application) and 13 Helm charts — realistic enterprise scale for IaC review

---

### Lab D3 — Event-Driven SAST Pipeline (45-60 min)

- **Lab Guide:** [D3-event-driven-sast.md](D3-event-driven-sast.md)
- **Objective:** Understand the automated Trivy + SonarCloud → Devin remediation pipeline and propose improvements

**Key Takeaways:**
- Event-driven automation is the enterprise pattern — Devin responds to CI signals, not manual prompts
- Closed-loop verification (fix → re-scan → verify) creates autonomous security remediation
- Bot-loop prevention and escalation are essential for production safety
- This pipeline demonstrates Devin as an always-on security engineer that scales to hundreds of PRs

---

## Track E: Application Development

Focus: Fixing real bugs, completing missing features, and building realistic test environments.

### Lab E1 — Cross-Service Bug Hunt (45-60 min)

- **Lab Guide:** [E1-cross-service-bug-hunt.md](E1-cross-service-bug-hunt.md)
- **Objective:** Use the exploratory QA report as a bug backlog and fix real cross-service bugs

**Key Takeaways:**
- Real bugs in microservices architectures often span multiple services — the root cause is rarely where the symptom appears
- Devin can trace request flows across API gateway, backend services, and frontends to pinpoint root causes
- QA reports and bug backlogs are effective starting points for Devin sessions — they provide clear context and success criteria
- This lab uses real (not planted) integration issues documented in `docs/exploratory-qa-report.md`

---

### Lab E2 — API Gateway Route Completion (45-60 min)

- **Lab Guide:** [E2-api-gateway-routes.md](E2-api-gateway-routes.md)
- **Objective:** Wire missing API routes through the Go API gateway so all service endpoints are reachable

**Key Takeaways:**
- API gateways are a common integration bottleneck — services expose endpoints that the gateway does not route
- Devin can read an API route matrix, understand the existing routing pattern, and add new routes consistently
- The `docs/api-route-matrix.md` documents exactly which routes are missing — a realistic internal doc that Devin uses as a task specification
- Go reverse proxy configuration is a pattern Devin handles well when given a working example to follow

---

### Lab E3 — Synthetic Test Data Generation (45-60 min)

- **Lab Guide:** [E3-synthetic-test-data.md](E3-synthetic-test-data.md)
- **Objective:** Generate, load, and validate realistic synthetic test data using the testdata framework

**Key Takeaways:**
- Empty databases make lower environments useless for testing — synthetic data is an enterprise necessity
- Devin can introspect a database schema and generate data that satisfies both schema invariants and business acceptance criteria
- The generate → load → validate loop mirrors real data engineering workflows
- Namespaced schemas prevent test data from polluting production data

---

## Track F: Architecture & Documentation

Focus: Generating architecture artifacts, expanding test suites, and creating documentation grounded in the actual codebase.

### Lab F1 — Architecture Decision Records (45-60 min)

- **Lab Guide:** [F1-architecture-decision-records.md](F1-architecture-decision-records.md)
- **Objective:** Generate ADRs for key OtterWorks design decisions by reading the implementation

**Key Takeaways:**
- ADRs capture the "why" behind architecture — the rationale that gets lost when decisions live in Slack threads or meeting notes
- Devin can reverse-engineer decision rationale by reading the actual codebase, not just architecture diagrams
- Grounded ADRs (referencing specific files and patterns) are more useful than generic ones
- This is a high-value low-risk task that Devin can do at scale across any enterprise codebase

---

### Lab F2 — API Flow Test Expansion (45-60 min)

- **Lab Guide:** [F2-api-flow-test-expansion.md](F2-api-flow-test-expansion.md)
- **Objective:** Write new black-box API flow tests for user flows that lack coverage

**Key Takeaways:**
- Black-box API tests (through the gateway) catch integration issues that unit tests miss
- Devin can read an API route matrix, identify uncovered flows, and write tests that follow existing patterns
- The `tests/api/` suite already covers 10 flows — expanding it is a realistic "more of this, please" task
- Edge cases (cross-user access control, error paths, side-effect verification) are where integration tests add the most value

---

### Lab F3 — Technical Documentation Generation (45-60 min)

- **Lab Guide:** [F3-technical-documentation.md](F3-technical-documentation.md)
- **Objective:** Generate service-level READMEs and developer onboarding guides from the actual codebase

**Key Takeaways:**
- Documentation quality varies wildly across services in real enterprise codebases — Devin can level it up
- Devin generates documentation grounded in real files, commands, and configuration — not generic templates
- Per-service READMEs dramatically reduce onboarding friction for developers new to a specific service
- This is one of the highest-ROI, lowest-risk tasks for Devin — documentation has no production impact but compounds over time

---

<a id="choosing-a-track"></a>

## Choosing a Track

| Participant Background | Recommended Track |
|---|---|
| Backend / full-stack developers, migration experience | Track A: Modernization & Migration |
| SRE, DevOps, platform engineers, on-call experience | Track B: Incident Response & Reliability |
| Security engineers, QA leads, test automation | Track C: Security & Quality |
| Platform engineers, CI/CD specialists, DevOps leads | Track D: DevOps & CI/CD |
| Application developers, feature work, bug fixing | Track E: Application Development |
| Tech leads, architects, documentation-focused | Track F: Architecture & Documentation |
| Mixed audience | Let participants self-select — all tracks use the same repo |
| Short event (2 hours) | Pick one lab from any track |

<a id="duration-variants"></a>

## Duration Variants

| Duration | Recommended Format |
|----------|-------------------|
| 8 hours (full day) | All six tracks available, participants pick 3-4 labs across 2 tracks |
| 6 hours | Three tracks in parallel, participants pick 2 labs from their track + 1 from another |
| 4 hours | Single track: all 3 labs with breaks |
| 3 hours | Single track: 2 labs (skip the third or abbreviate) |
| 2 hours | Pick 1 lab from any track + 30 min discussion |

<a id="lab-quick-reference"></a>

## Lab Quick Reference

| Lab | Track | Difficulty | Time | Languages Involved | Key Docs in Repo |
|-----|-------|-----------|------|-------------------|-----------------|
| A1 — ETL Modernization | Modernization | Medium | 60-90 min | Python | `etl/ETL_UPGRADE_GUIDE.md` |
| A2 — Framework Upgrade | Modernization | Medium | 60-90 min | Java | `services/report-service/UPGRADE_GUIDE.md` |
| A3 — Language Translation | Modernization | Medium | 60-90 min | Python | `services/search-service/TRANSLATION_GUIDE.md` |
| B1 — Investigate Incident | Reliability | Medium | 45-60 min | Ruby, Python, Rust, Kotlin | `docs/runbooks/`, `observability/grafana/` |
| B2 — Complete Runbooks | Reliability | Easy | 45-60 min | All (reading) | `docs/runbooks/` |
| B3 — Add Observability | Reliability | Hard | 45-60 min | Varies | `observability/`, service source |
| C1 — Security Sprint | Security | Medium | 60-90 min | All | `docs/labs/security-sprint-guide.md` |
| C2 — Contract Audit | Security | Medium | 45-60 min | Kotlin, C# | `docs/labs/contract-audit-guide.md` |
| C3 — Test Coverage | Security | Medium | 45-60 min | Varies | `Makefile` (test-coverage target) |
| D1 — CI Pipeline Audit | DevOps | Medium | 45-60 min | YAML, Go | `docs/CI_STRATEGY.md` |
| D2 — IaC Review | DevOps | Hard | 45-60 min | HCL, Helm | `infrastructure/`, `platform/` |
| D3 — Event-Driven SAST | DevOps | Medium | 45-60 min | YAML | `docs/EVENT_DRIVEN_SECURITY.md` |
| E1 — Bug Hunt | App Dev | Medium | 45-60 min | Go, Kotlin, Python | `docs/exploratory-qa-report.md` |
| E2 — Gateway Routes | App Dev | Easy | 45-60 min | Go | `docs/api-route-matrix.md` |
| E3 — Synthetic Test Data | App Dev | Medium | 45-60 min | Python, SQL | `testdata/README.md` |
| F1 — ADRs | Architecture | Easy | 45-60 min | All (reading) | `ARCHITECTURE.md` |
| F2 — API Flow Tests | Architecture | Medium | 45-60 min | Python | `tests/api/`, `docs/api-route-matrix.md` |
| F3 — Technical Docs | Architecture | Easy | 45-60 min | All (reading) | Service source code |

## Repos Required

- [ ] [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks)

## Key Takeaways

- **"Enterprise codebases are messy — Devin thrives in messy"** — real-world problems span multiple languages, services, and concerns. Devin navigates this naturally.
- **"Craft the prompt, don't paste it"** — participants learn to decompose problems and write effective Devin prompts, which is the skill that transfers to their day jobs.
- **"Parallel sessions for parallel problems"** — security sprints, test coverage blitzes, and multi-service changes benefit from running multiple Devin sessions simultaneously.
- **"Guides and specs are force multipliers"** — pointing Devin at an upgrade guide, OpenAPI spec, or runbook template dramatically improves output quality.
- **"Verification is non-negotiable"** — every lab has concrete success criteria. Tests pass, scans are clean, specs match implementations.
- **"Event-driven Devin scales beyond manual sessions"** — CI triggers, incident webhooks, and scheduled sessions show how Devin operates as team-scale infrastructure, not just a personal assistant.
- **"Documentation compounds"** — ADRs, service READMEs, and onboarding guides are high-ROI, low-risk tasks where Devin generates immediate team value.
