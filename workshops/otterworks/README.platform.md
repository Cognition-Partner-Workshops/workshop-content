# Workshop: OtterWorks

**Other variants:** [Cloud only](README.md)

## Overview

| | |
|---|---|
| **Focus** | Directing Devin on messy, interconnected enterprise problems across a polyglot microservices platform |
| **Duration** | 4-6 hours (participants choose a track and complete 2-3 labs) |
| **Audience** | Engineers who have completed a General Devin workshop |
| **Prerequisite** | 200-level Devin proficiency — comfortable with prompting, PR review, iterative feedback, and Ask Devin |
| **Level** | 300 — application-specific, multi-lab composition |
| **Tracks** | **Modernization & Migration** - **Incident Response & Reliability** - **Security & Quality** |
| **Delivery** | **Devin Desktop + Devin Cloud** — participants use Desktop as their primary interface, delegating tasks to Cloud and reviewing results in-editor |

## Platform Context

This workshop uses **Devin Desktop + Devin Cloud**. You will use Desktop as your primary interface — exploring code locally, delegating tasks to Cloud, and reviewing results without leaving the editor. Cloud agents work autonomously on their own VMs while you continue working locally. When a Cloud session opens a PR, you review it with one-click checkout right in Desktop.

> **Tip:** The same labs are available in a [Cloud-only variant](README.md) for participants using the Devin web app at app.devin.ai without Desktop installed.

## Workshop Narrative

```
General Workshop (200)  ──>  OtterWorks Workshop (300)
(learn Devin basics)         (direct Devin on messy, interconnected
                              enterprise problems in a real codebase)
```

This is a **300-level** workshop. Participants are expected to have completed at least one General Devin workshop (200-level) and be comfortable with core workflows: creating sessions, reviewing PRs, giving feedback, and using Ask Devin for research.

## Getting the Most from This Workshop

> **Devin Desktop is your command center.** You will use Desktop to explore code locally with Cascade or Devin Local, delegate implementation tasks to Devin Cloud, and review Cloud PRs — without leaving the editor. Cloud agents keep working in the background while you move on to the next lab.

A few tips to maximize your hands-on time:

- **Create a Space for this workshop.** Spaces group sessions, PRs, files, and context for a task. Create one Space for OtterWorks to keep your work organized. New sessions in this Space inherit the project context automatically.
- **Explore locally, delegate to Cloud.** Use Cascade or Devin Local for code exploration and research (replaces Ask Devin in the web app). When you have a clear implementation task, delegate it to Devin Cloud from Desktop.
- **Craft your own prompts.** Unlike the General workshop, this workshop does not give you copy-paste prompts. You must decompose the problem and figure out how to direct Devin.
- **Monitor from the Agent Command Center.** The Kanban board shows the status of your local and cloud agents at a glance — what is in flight, what is blocked, and what is ready for review.
- **Review PRs in-editor.** When Devin Cloud opens a PR, use one-click checkout to review it directly in Desktop — no browser switching or manual `git fetch` required.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments and Devin will wake up and address them — this is the core feedback loop.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Try parallel sessions.** Running multiple cloud sessions simultaneously mirrors real enterprise usage. Track them from the Agent Command Center.

## Desktop Workflow

Each lab in this workshop follows the same five-step pattern using Desktop:

1. **Create a Space** — Group your workshop sessions and PRs in a single Desktop Space for OtterWorks
2. **Explore locally** — Use Cascade or Devin Local in Desktop to research the codebase, understand the problem, and refine your approach (replaces Ask Devin research steps)
3. **Delegate to Cloud** — When you have a clear implementation task, send it to Devin Cloud from Desktop. The Cloud agent works autonomously on its own VM
4. **Monitor in Agent Command Center** — Track your Cloud sessions on the Kanban board. See what is running, what is blocked, and what has completed
5. **Review with one-click checkout** — When Cloud opens a PR, review it directly in the editor. No browser switching, no `git fetch`, no branch juggling

> **ACP note:** Devin Desktop supports multiple agents via the Agent Client Protocol (ACP). Alongside Devin Local and Cloud, you can run Cascade, Codex CLI, Claude Agent, Gemini CLI, or other ACP-compatible agents — all visible in the Agent Command Center.

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
- [Duration Variants](#duration-variants)

---

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

OtterWorks is a large polyglot codebase. Before jumping into a lab, spend 10 minutes using **Cascade or Devin Local** in Desktop to explore the repo and build a mental model. Open a local agent session in your OtterWorks Space and try these prompts:

```
What does OtterWorks do? Describe the high-level architecture
— what are the services, what language is each one written in,
and how do they connect?
```

This gives you the map. You will learn that there is an API gateway (Go) routing to backend services, two frontends, and infrastructure like PostgreSQL, Redis, MeiliSearch, and DynamoDB via LocalStack.

```
Walk me through what happens when a user creates a document.
Which services are involved and what does the request flow
look like?
```

This grounds the architecture in a concrete flow. You will see the api-gateway route the request to the document-service (Python/FastAPI), which writes to DynamoDB and publishes an event to SNS. The collab-service (Node.js) picks up the event for real-time sync, and the search-service (Python/Flask) indexes it in MeiliSearch.

```
What observability tools does OtterWorks use? Where are the
Grafana dashboards, Prometheus alerts, and Jaeger tracing
configured?
```

This is especially useful if you are planning Track B (Incident Response), but helps everyone understand how the system is monitored.

```
What parts of this codebase look like they need work? Are there
legacy scripts, outdated dependencies, incomplete docs, or
known issues?
```

This is the discovery prompt. Devin will surface the ETL legacy scripts, the outdated report-service, the incomplete runbooks, the planted security vulnerabilities, and the drifting API contracts — which are exactly the problems the labs address.

Once you have a sense of the codebase, pick your track and start a lab. When you are ready to implement, delegate the task to Devin Cloud from Desktop.

---

## Track A: Modernization & Migration

Focus: Taking legacy or outdated parts of the codebase and bringing them to modern standards.

### Lab A1 — ETL Pipeline Modernization (60-90 min)

- **Lab Guide:** [A1-etl-modernization.md](A1-etl-modernization.md)
- **Objective:** Migrate legacy cron-based ETL scripts to Apache Airflow DAGs

**Desktop workflow:**
1. Use Cascade or Devin Local to explore the legacy ETL scripts and understand the current pipeline structure
2. Delegate the migration implementation to Devin Cloud — describe the target Airflow DAG structure in your prompt
3. Monitor the Cloud session in the Agent Command Center
4. Review the PR with one-click checkout in Desktop when Devin Cloud completes

**Key Takeaways:**
- Devin can read a legacy script AND a migration guide, then produce a complete Airflow DAG that preserves business logic
- Cron-to-orchestrator migration is a common enterprise pattern that Devin handles well
- The before/after is dramatic: monolithic scripts with hardcoded credentials vs. modular DAGs with proper connection management

---

### Lab A2 — Report Service Framework Upgrade (60-90 min)

- **Lab Guide:** [A2-framework-upgrade.md](A2-framework-upgrade.md)
- **Objective:** Upgrade a Java 8 / Spring Boot 2.5 service to Java 17 / Spring Boot 3.2+

**Desktop workflow:**
1. Use Devin Local to audit the current dependency versions and identify the upgrade axes (Java, Spring Boot, javax-to-jakarta, test framework, PDF library)
2. Delegate the upgrade to Devin Cloud — reference the Spring Boot migration guide and list the target versions
3. Review the PR in Desktop and verify tests pass through the CI checks in the Agent Command Center

**Key Takeaways:**
- Devin can execute multi-axis framework upgrades (Java version, Spring Boot, javax-to-jakarta, test framework, PDF library, etc.) in a single session
- Having a robust test suite before upgrading is critical — the tests serve as the verification harness
- Enterprise Java upgrades involve cascading changes that Devin tracks systematically

---

### Lab A3 — Language Translation (60-90 min)

- **Lab Guide:** [A3-language-translation.md](A3-language-translation.md)
- **Objective:** Translate the search-service from Flask (synchronous) to FastAPI (async)

**Desktop workflow:**
1. Use Devin Local to review the OpenAPI spec and current Flask routes — understand the contract that must be preserved
2. Delegate the translation to Devin Cloud — include the OpenAPI spec as a contract reference in your prompt
3. Review the PR in Desktop, checking that API contracts are preserved

**Key Takeaways:**
- Devin can translate between Python web frameworks while preserving API contracts
- OpenAPI specs serve as the contract that must be preserved during translation
- Contract tests provide automated verification that the translation is correct

---

## Track B: Incident Response & Reliability

Focus: Investigating production incidents, building runbooks, and improving observability.

> **Agent Command Center:** Track B is especially well-suited to the Desktop workflow. Use the Kanban board to monitor incident investigation sessions alongside runbook and observability work running in parallel.

### Lab B1 — Investigate Production Incident (45-60 min)

- **Lab Guide:** [B1-investigate-incident.md](B1-investigate-incident.md)
- **Objective:** Trigger a chaos scenario and experience three levels of incident response automation — manual, one-click, and fully automatic — controlled by an Auto-Investigate toggle

**Desktop workflow:**
1. Use Cascade or Devin Local to explore the Grafana dashboards, Prometheus alert rules, and Jaeger traces to understand the observability landscape
2. Trigger a chaos scenario from the admin dashboard
3. Delegate the incident investigation to Devin Cloud — describe the symptoms and point Devin at the relevant dashboards and services
4. Monitor the investigation in the Agent Command Center and review Devin's root cause analysis PR

**Key Takeaways:**
- Devin can correlate signals across dashboards, logs, and traces to identify root causes
- Different incident signatures (error spikes vs. latency spikes) require different investigation approaches
- **Event-driven pattern:** Grafana alerts → webhook → auto-created incidents → Devin API sessions show how to adopt Devin incrementally as an on-call assistant
- The Auto-Investigate toggle lets teams experience the escalation from manual → one-click → fully automatic

---

### Lab B2 — Complete the Runbooks (45-60 min)

- **Lab Guide:** [B2-complete-runbooks.md](B2-complete-runbooks.md)
- **Objective:** Use Devin to fill in incomplete incident runbooks based on codebase knowledge

**Desktop workflow:**
1. Use Devin Local to review the existing runbook templates and identify gaps
2. Delegate the runbook completion to Devin Cloud — point it at the service code, alert rules, and architecture docs
3. Review the generated runbooks in Desktop with one-click checkout

**Key Takeaways:**
- Devin can generate investigation and resolution procedures by reading the service code, alert rules, and architecture docs
- Runbooks grounded in actual code are more accurate than runbooks written from memory
- This is a realistic SRE task that Devin accelerates significantly

---

### Lab B3 — Add Observability to Under-Instrumented Services (45-60 min)

- **Lab Guide:** [B3-add-observability.md](B3-add-observability.md)
- **Objective:** Identify services with weak observability coverage and add structured logging, metrics, or tracing

**Desktop workflow:**
1. Use Devin Local to audit instrumentation across the polyglot services — identify which services lack structured logging, metrics, or tracing
2. Delegate the instrumentation work to Devin Cloud — specify the target service and the observability gaps to fill
3. Review the PR in Desktop, verifying metric naming conventions and structured log formats

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

**Desktop workflow:**
1. Use Devin Local to run initial security scans and triage findings across the monorepo
2. Delegate remediation for each service to separate Devin Cloud sessions — one per service for parallel execution
3. Track progress across sessions in the Agent Command Center Kanban board
4. Review each remediation PR with one-click checkout as they complete

**Key Takeaways:**
- Devin can interpret security scan output and remediate vulnerabilities across multiple languages
- Triaging `.trivyignore` entries for overly broad suppressions is a real-world security hygiene task
- Parallel Devin sessions can tackle different services simultaneously for a monorepo security sprint

> **Parallel sessions:** This lab is ideal for running multiple Cloud agents simultaneously. Delegate each service's remediation as a separate Cloud session and watch them work in parallel from the Agent Command Center.

---

### Lab C2 — API Contract Audit (45-60 min)

- **Lab Guide:** [C2-contract-audit.md](C2-contract-audit.md)
- **Objective:** Find and fix mismatches between OpenAPI specs, event schemas, and actual service implementations

**Desktop workflow:**
1. Use Devin Local to compare OpenAPI specs against actual route implementations — identify discrepancies
2. Delegate the contract fixes to Devin Cloud — include the spec file paths and the drift you found
3. Review the PR in Desktop and verify the spec and implementation are now aligned

**Key Takeaways:**
- Contract drift between specs and implementations is a common source of production bugs
- Devin can compare a spec to an implementation and identify exact discrepancies
- Event schema drift (camelCase vs. snake_case, optional vs. required fields) is subtle but impactful

---

### Lab C3 — Test Coverage Blitz (45-60 min)

- **Lab Guide:** [C3-test-coverage.md](C3-test-coverage.md)
- **Objective:** Identify the service with weakest test coverage and add meaningful tests

**Desktop workflow:**
1. Use Devin Local to analyze test coverage across services and identify the weakest areas
2. Delegate test writing to Devin Cloud — specify the target service, the coverage gaps, and the testing patterns to follow
3. Review the tests in Desktop with one-click checkout and verify they pass

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
- **"Explore locally, execute in the cloud"** — use Desktop's local agents to understand the problem, then delegate to Cloud for autonomous implementation. The handoff is seamless.
- **"Craft the prompt, don't paste it"** — participants learn to decompose problems and write effective Devin prompts, which is the skill that transfers to their day jobs.
- **"One view for everything"** — the Agent Command Center gives you a Kanban view of local and cloud agents, so you see the full picture without switching tabs.
- **"Parallel sessions for parallel problems"** — security sprints, test coverage blitzes, and multi-service changes benefit from running multiple Devin sessions simultaneously.
- **"Guides and specs are force multipliers"** — pointing Devin at an upgrade guide, OpenAPI spec, or runbook template dramatically improves output quality.
- **"Verification is non-negotiable"** — every lab has concrete success criteria. Tests pass, scans are clean, specs match implementations.
