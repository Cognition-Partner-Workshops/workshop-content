# Workshop: OtterWorks

## Overview

| | |
|---|---|
| **Focus** | Directing Devin on messy, interconnected enterprise problems across a polyglot microservices platform |
| **Duration** | 4-6 hours (participants choose a track and complete 2-3 labs) |
| **Audience** | Engineers who have completed a General Devin workshop |
| **Prerequisite** | 200-level Devin proficiency — comfortable with prompting, PR review, iterative feedback, and Ask Devin |
| **Level** | 300 — application-specific, multi-lab composition |
| **Tracks** | **Modernization & Migration** - **Incident Response & Reliability** - **Security & Quality** |

## Workshop Narrative

```
General Workshop (200)  ──>  OtterWorks Workshop (300)
(learn Devin basics)         (direct Devin on messy, interconnected
                              enterprise problems in a real codebase)
```

This is a **300-level** workshop. Participants are expected to have completed at least one General Devin workshop (200-level) and be comfortable with core workflows: creating sessions, reviewing PRs, giving feedback, and using Ask Devin for research.

OtterWorks is a polyglot microservices platform for real-time collaborative document editing and file management. It has **10 backend services** (Go, Java, Rust, Python x2, Node.js, Kotlin, Scala, Ruby, C#), **2 frontends** (React, Angular), and a full observability stack (Prometheus, Grafana, Jaeger). It is intentionally messy: legacy ETL scripts, outdated dependencies, incomplete runbooks, drifting API contracts, and planted security vulnerabilities.

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

This grounds the architecture in a concrete flow. You will see the api-gateway route the request to the document-service (Python/FastAPI), which writes to DynamoDB and publishes an event to SNS. The collab-service (Node.js) picks up the event for real-time sync, and the search-service (Python/Flask) indexes it in MeiliSearch.

> **"What observability tools does OtterWorks use? Where are the Grafana dashboards, Prometheus alerts, and Jaeger tracing configured?"**

This is especially useful if you are planning Track B (Incident Response), but helps everyone understand how the system is monitored.

> **"What parts of this codebase look like they need work? Are there legacy scripts, outdated dependencies, incomplete docs, or known issues?"**

This is the discovery prompt. Devin will surface the ETL legacy scripts, the outdated report-service, the incomplete runbooks, the planted security vulnerabilities, and the drifting API contracts — which are exactly the problems the labs address.

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
- **Event-driven pattern:** Grafana alerts → webhook → auto-created incidents → Devin API sessions demonstrate "Devin as an always-on on-call engineer"
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

## Choosing a Track

| Participant Background | Recommended Track |
|---|---|
| Backend / full-stack developers, migration experience | Track A: Modernization & Migration |
| SRE, DevOps, platform engineers, on-call experience | Track B: Incident Response & Reliability |
| Security engineers, QA leads, test automation | Track C: Security & Quality |
| Mixed audience | Let participants self-select — all tracks use the same repo |
| Short event (2 hours) | Pick one lab from any track |

## Duration Variants

| Duration | Recommended Format |
|----------|-------------------|
| 6 hours (full day) | All three tracks in parallel, participants pick 2 labs from their track + 1 from another |
| 4 hours | Single track: all 3 labs with breaks |
| 3 hours | Single track: 2 labs (skip the third or abbreviate) |
| 2 hours | Pick 1 lab from any track + 30 min discussion |

## Repos Required

- [ ] [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks)

## Key Takeaways

- **"Enterprise codebases are messy — Devin thrives in messy"** — real-world problems span multiple languages, services, and concerns. Devin navigates this naturally.
- **"Craft the prompt, don't paste it"** — participants learn to decompose problems and write effective Devin prompts, which is the skill that transfers to their day jobs.
- **"Parallel sessions for parallel problems"** — security sprints, test coverage blitzes, and multi-service changes benefit from running multiple Devin sessions simultaneously.
- **"Guides and specs are force multipliers"** — pointing Devin at an upgrade guide, OpenAPI spec, or runbook template dramatically improves output quality.
- **"Verification is non-negotiable"** — every lab has concrete success criteria. Tests pass, scans are clean, specs match implementations.
