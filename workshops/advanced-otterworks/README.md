# Workshop: Advanced OtterWorks

## Overview

| | |
|---|---|
| **Focus** | Directing Devin on messy, interconnected enterprise problems across a polyglot microservices platform |
| **Duration** | 4-6 hours (participants choose a track and complete 2-3 labs) |
| **Audience** | Engineers who have completed a General or domain-specific Devin workshop |
| **Prerequisite** | Participants should be comfortable with Devin basics (prompting, PR review, iterative feedback) |
| **Tracks** | **Modernization & Migration** - **Incident Response & Reliability** - **Security & Quality** |

## Workshop Narrative

```
General Workshop --> Domain Workshop --> Advanced OtterWorks Workshop
(learn Devin)       (go deep on one      (direct Devin on messy,
                     area)                interconnected enterprise problems)
```

OtterWorks is a polyglot microservices platform for real-time collaborative document editing and file management. It has **10 backend services** (Go, Java, Rust, Python x2, Node.js, Kotlin, Scala, Ruby, C#), **2 frontends** (React, Angular), and a full observability stack (Prometheus, Grafana, Jaeger). It is intentionally messy: legacy ETL scripts, outdated dependencies, incomplete runbooks, drifting API contracts, and planted security vulnerabilities.

Unlike previous workshops where participants are given prompts to paste, this workshop requires participants to **craft their own prompts**. Each lab describes what is wrong, where to look, and what done looks like. Participants must figure out how to direct Devin to fix it.

## Repository

[otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — Polyglot microservices platform (Rust, Python, Node.js, Java, Go, Kotlin, Scala, Ruby, C#)

---

## Track A: Modernization & Migration

Focus: Taking legacy or outdated parts of the codebase and bringing them to modern standards.

### Lab A1 — ETL Pipeline Modernization (60-90 min)

- **Lab Guide:** [etl-modernization.md](labs/etl-modernization.md)
- **Objective:** Migrate legacy cron-based ETL scripts to Apache Airflow DAGs

**Key Takeaways:**
- Devin can read a legacy script AND a migration guide, then produce a complete Airflow DAG that preserves business logic
- Cron-to-orchestrator migration is a common enterprise pattern that Devin handles well
- The before/after is dramatic: monolithic scripts with hardcoded credentials vs. modular DAGs with proper connection management

---

### Lab A2 — Report Service Dependency Upgrade (60-90 min)

- **Lab Guide:** [dependency-upgrade.md](labs/dependency-upgrade.md)
- **Objective:** Upgrade a Java 8 / Spring Boot 2.5 service to Java 17 / Spring Boot 3.2+

**Key Takeaways:**
- Devin can execute multi-axis dependency upgrades (Java version, Spring Boot, javax-to-jakarta, test framework, PDF library, etc.) in a single session
- Having a robust test suite before upgrading is critical — the tests serve as the verification harness
- Enterprise Java upgrades involve cascading changes that Devin tracks systematically

---

### Lab A3 — Language Translation (60-90 min)

- **Lab Guide:** [language-translation.md](labs/language-translation.md)
- **Objective:** Translate the search-service from Flask (synchronous) to FastAPI (async)

**Key Takeaways:**
- Devin can translate between Python web frameworks while preserving API contracts
- OpenAPI specs serve as the contract that must be preserved during translation
- Contract tests provide automated verification that the translation is correct

---

## Track B: Incident Response & Reliability

Focus: Investigating production incidents, building runbooks, and improving observability.

### Lab B1 — Investigate Production Incident (45-60 min)

- **Lab Guide:** [incident-response.md](labs/incident-response.md)
- **Objective:** Trigger a chaos scenario, investigate the resulting incident using Grafana and Jaeger, and identify the root cause

**Key Takeaways:**
- Devin can correlate signals across dashboards, logs, and traces to identify root causes
- Different incident signatures (error spikes vs. latency spikes) require different investigation approaches
- Having Devin read observability data directly is faster than describing symptoms manually

---

### Lab B2 — Complete the Runbooks (45-60 min)

- **Lab Guide:** [incident-response.md](labs/incident-response.md) (continued)
- **Objective:** Use Devin to fill in incomplete incident runbooks based on codebase knowledge

**Key Takeaways:**
- Devin can generate investigation and resolution procedures by reading the service code, alert rules, and architecture docs
- Runbooks grounded in actual code are more accurate than runbooks written from memory
- This is a realistic SRE task that Devin accelerates significantly

---

### Lab B3 — Add Observability to Under-Instrumented Services (45-60 min)

- **Lab Guide:** [incident-response.md](labs/incident-response.md) (extended)
- **Objective:** Identify services with weak observability coverage and add structured logging, metrics, or tracing

**Key Takeaways:**
- Devin can audit an entire service for observability gaps and add instrumentation systematically
- Polyglot services each have different instrumentation libraries — Devin handles this naturally
- Consistent metric naming and structured logging are critical for cross-service correlation

---

## Track C: Security & Quality

Focus: Finding and fixing security vulnerabilities, contract drift, and test coverage gaps.

### Lab C1 — Monorepo Security Sprint (60-90 min)

- **Lab Guide:** [security-sprint.md](labs/security-sprint.md)
- **Objective:** Run security scans, triage findings, and remediate CRITICAL/HIGH CVEs using parallel Devin sessions

**Key Takeaways:**
- Devin can interpret security scan output and remediate vulnerabilities across multiple languages
- Triaging `.trivyignore` entries for overly broad suppressions is a real-world security hygiene task
- Parallel Devin sessions can tackle different services simultaneously for a monorepo security sprint

---

### Lab C2 — API Contract Audit (45-60 min)

- **Lab Guide:** [test-and-contract-audit.md](labs/test-and-contract-audit.md)
- **Objective:** Find and fix mismatches between OpenAPI specs, event schemas, and actual service implementations

**Key Takeaways:**
- Contract drift between specs and implementations is a common source of production bugs
- Devin can compare a spec to an implementation and identify exact discrepancies
- Event schema drift (camelCase vs. snake_case, optional vs. required fields) is subtle but impactful

---

### Lab C3 — Test Coverage Blitz (45-60 min)

- **Lab Guide:** [test-and-contract-audit.md](labs/test-and-contract-audit.md) (continued)
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
