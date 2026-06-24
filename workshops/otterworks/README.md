# Workshop: OtterWorks

## Overview

| | |
|---|---|
| **Focus** | Directing Devin on messy, interconnected enterprise problems across a polyglot microservices platform — and building automation that scales |
| **Duration** | 4-8 hours (participants choose a track and complete 2-3 labs) |
| **Audience** | Engineers who have completed a General Devin workshop |
| **Prerequisite** | 200-level Devin proficiency — comfortable with prompting, PR review, iterative feedback, and Ask Devin |
| **Level** | 300 — application-specific, multi-lab composition |
| **Tracks** | **A: Modernization & Migration** · **B: Incident Response & Automation** · **C: Security & Quality** · **D: Devin Platform Mastery** |

## Workshop Narrative

```
General Workshop (200)  ──>  OtterWorks Workshop (300)
(learn Devin basics)         (direct Devin on messy, interconnected
                              enterprise problems — then automate
                              the patterns that work)
```

This is a **300-level** workshop. Participants are expected to have completed at least one General Devin workshop (200-level) and be comfortable with core workflows: creating sessions, reviewing PRs, giving feedback, and using Ask Devin for research.

**What makes this workshop different:** Tracks A-C teach Devin on hard engineering problems. Track D teaches Devin's platform capabilities — playbooks, child agents, scheduled sessions, knowledge layer, and event-driven automation. Every lab showcases something Devin does uniquely well, and the curriculum builds toward the full Ask → Session → Playbook → Automation lifecycle.

## Getting the Most from This Workshop

> **Devin works asynchronously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Craft your own prompts.** Unlike the General workshop, this workshop does not give you copy-paste prompts. You must decompose the problem and figure out how to direct Devin.
- **Start sessions early, review later.** Kick off the session first, then use the wait time for Ask Devin research — Devin keeps working in the background.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments and Devin will wake up and address them — this is the core feedback loop.
- **Try parallel sessions.** Running multiple sessions simultaneously mirrors real enterprise usage and demonstrates team-based operation at scale.
- **Think about automation.** After each lab, ask: "Could this run on a schedule? Could it trigger automatically? Could a Playbook encode this methodology?" That is how teams move from ad-hoc sessions to scalable automation.

<a id="table-of-contents"></a>

## Table of Contents

- [Interact with the System](#interact-with-the-system-5-min)
- [Getting Oriented](#getting-oriented-10-min--do-this-first)
- [Track A: Modernization & Migration](#track-a-modernization--migration)
  - [Lab A1 — ETL Pipeline Modernization](#lab-a1--etl-pipeline-modernization-60-90-min)
  - [Lab A2 — Report Service Framework Upgrade](#lab-a2--report-service-framework-upgrade-60-90-min)
  - [Lab A3 — Language Translation](#lab-a3--language-translation-60-90-min)
- [Track B: Incident Response & Automation](#track-b-incident-response--automation)
  - [Lab B1 — Investigate Production Incident](#lab-b1--investigate-production-incident-45-60-min)
  - [Lab B2 — From Ask to Playbook](#lab-b2--from-ask-to-playbook-45-60-min)
  - [Lab B3 — Dependency Lifecycle Campaign](#lab-b3--dependency-lifecycle-campaign-45-60-min)
- [Track C: Security & Quality](#track-c-security--quality)
  - [Lab C1 — Monorepo Security Sprint](#lab-c1--monorepo-security-sprint-60-90-min)
  - [Lab C2 — API Contract Audit](#lab-c2--api-contract-audit-45-60-min)
  - [Lab C3 — Scheduled Security & Coverage Automation](#lab-c3--scheduled-security--coverage-automation-45-60-min)
- [Track D: Devin Platform Mastery](#track-d-devin-platform-mastery)
  - [Lab D1 — Event-Driven SAST Pipeline](#lab-d1--event-driven-sast-pipeline-45-60-min)
  - [Lab D2 — Knowledge Layer & Devin Review](#lab-d2--knowledge-layer--devin-review-45-60-min)
  - [Lab D3 — Full Automation Pipeline (Capstone)](#lab-d3--full-automation-pipeline-capstone-60-90-min)
- [Choosing a Track](#choosing-a-track)
- [Duration Variants](#duration-variants)
- [Lab Quick Reference](#lab-quick-reference)

---

OtterWorks is a polyglot microservices platform for real-time collaborative document editing and file management. It has **10 backend services** (Go, Java, Rust, Python x2, Node.js, Kotlin, Scala, Ruby, C#), **2 frontends** (React, Angular), and a full observability stack (Prometheus, Grafana, Jaeger). It is intentionally messy: legacy ETL scripts, outdated dependencies, incomplete runbooks, drifting API contracts, planted security vulnerabilities, and gaps in CI/CD coverage.

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

This is especially useful if you are planning Track B (Incident Response) or Track D (Platform Mastery), but helps everyone understand how the system is monitored.

> **"What parts of this codebase look like they need work? Are there legacy scripts, outdated dependencies, incomplete docs, or known issues?"**

This is the discovery prompt. Devin will surface the ETL legacy scripts, the outdated report-service, the incomplete runbooks, the planted security vulnerabilities, the drifting API contracts, and the documented bugs — which are exactly the problems the labs address.

Once you have a sense of the codebase, pick your track and start a lab.

---

## Track A: Modernization & Migration

Focus: Taking legacy or outdated parts of the codebase and bringing them to modern standards. These labs showcase Devin on large-scale migration and upgrade tasks — the "capacity-constrained" work that teams defer because other priorities always win.

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
- This is exactly the kind of EOL/dependency upgrade work that teams defer and Devin automates

---

### Lab A3 — Language Translation (60-90 min)

- **Lab Guide:** [A3-language-translation.md](A3-language-translation.md)
- **Objective:** Translate the search-service from Flask (synchronous) to FastAPI (async)

**Key Takeaways:**
- Devin can translate between Python web frameworks while preserving API contracts
- OpenAPI specs serve as the contract that must be preserved during translation
- Contract tests provide automated verification that the translation is correct

---

## Track B: Incident Response & Automation

Focus: Investigating production incidents with increasing levels of automation, then learning the Devin workflow progression from ad-hoc asks to reusable playbooks to parallel campaigns.

### Lab B1 — Investigate Production Incident (45-60 min)

- **Lab Guide:** [B1-investigate-incident.md](B1-investigate-incident.md)
- **Objective:** Trigger a chaos scenario and experience three levels of incident response automation — manual, one-click, and fully automatic — controlled by an Auto-Investigate toggle

**Key Takeaways:**
- **Event-driven pattern:** Grafana alerts → webhook → auto-created incidents → Devin API sessions show "Devin as an always-on on-call engineer"
- The Auto-Investigate toggle lets teams experience the escalation from manual → one-click → fully automatic, showing how to adopt Devin incrementally
- Devin can correlate signals across dashboards, logs, and traces to identify root causes

---

### Lab B2 — From Ask to Playbook (45-60 min)

- **Lab Guide:** [B2-from-ask-to-playbook.md](B2-from-ask-to-playbook.md)
- **Objective:** Experience the Ask → Session → Playbook progression: start with an ad-hoc ask, refine it, then codify the methodology as a reusable Devin Playbook

**Key Takeaways:**
- Ad-hoc asks work but don't scale — every team member writes a different prompt and gets different quality
- Playbooks encode institutional methodology — the senior engineer's approach runs every time
- The progression is: try it once (ask) → refine it (session) → codify it (playbook) → automate it (scheduled/event-driven)
- Playbooks are a one-time investment that compounds across every session in the org

---

### Lab B3 — Dependency Lifecycle Campaign (45-60 min)

- **Lab Guide:** [B3-dependency-lifecycle.md](B3-dependency-lifecycle.md)
- **Objective:** Use parent/child agents to audit all OtterWorks services for outdated/EOL dependencies in parallel — one child session per service, each producing an upgrade PR

**Key Takeaways:**
- Divide-and-conquer turns weeks of dependency auditing into parallel hours
- Playbooks ensure consistency across child sessions — every service gets the same thorough audit
- The pattern scales linearly: 11 services today, 50 tomorrow, same approach
- This same campaign could run on a weekly schedule — dependency lifecycle never drifts again

---

## Track C: Security & Quality

Focus: Finding and fixing security vulnerabilities, contract drift, and automating recurring quality checks.

### Lab C1 — Monorepo Security Sprint (60-90 min)

- **Lab Guide:** [C1-security-sprint.md](C1-security-sprint.md)
- **Objective:** Run security scans, triage findings, and remediate CRITICAL/HIGH CVEs using parallel Devin sessions

**Key Takeaways:**
- Devin can interpret security scan output and remediate vulnerabilities across multiple languages
- Triaging `.trivyignore` entries for overly broad suppressions is a real-world security hygiene task
- Parallel Devin sessions can tackle different services simultaneously — divide-and-conquer for security

---

### Lab C2 — API Contract Audit (45-60 min)

- **Lab Guide:** [C2-contract-audit.md](C2-contract-audit.md)
- **Objective:** Find and fix mismatches between OpenAPI specs, event schemas, and actual service implementations

**Key Takeaways:**
- Contract drift between specs and implementations is a common source of production bugs
- Devin can compare a spec to an implementation and identify exact discrepancies across languages
- Event schema drift (camelCase vs. snake_case, optional vs. required fields) is subtle but impactful

---

### Lab C3 — Scheduled Security & Coverage Automation (45-60 min)

- **Lab Guide:** [C3-scheduled-automation.md](C3-scheduled-automation.md)
- **Objective:** Set up a recurring Devin scheduled session for weekly security scans and coverage checks — automating "should-do" maintenance work

**Key Takeaways:**
- Scheduled sessions automate recurring work that teams defer — security scans, coverage checks, dependency audits
- The prompt is the methodology: a well-written scheduled prompt is effectively a Playbook that runs on cron
- Scheduled scans complement event-driven scans (D1): one catches PR-level issues, the other catches ecosystem-level drift
- Weekly automation catches drift before it accumulates — better than quarterly fire drills

---

## Track D: Devin Platform Mastery

Focus: Learning the Devin platform capabilities that turn individual sessions into team-scale automation — event-driven triggers, the shared context layer, and the full automation lifecycle.

### Lab D1 — Event-Driven SAST Pipeline (45-60 min)

- **Lab Guide:** [D1-event-driven-sast.md](D1-event-driven-sast.md)
- **Objective:** Trace, understand, and extend the OtterWorks event-driven security pipeline where SAST findings automatically trigger Devin sessions for remediation

**Key Takeaways:**
- Event-driven automation is the enterprise pattern — Devin responds to CI signals, not manual prompts
- Closed-loop verification (fix → re-scan → verify) creates autonomous security remediation
- Every automation needs safeguards: bot-loop prevention, max retries, escalation to humans
- The pattern is toolchain-agnostic: `[Scanner] → webhook → Devin → PR` — swap scanners freely

---

### Lab D2 — Knowledge Layer & Devin Review (45-60 min)

- **Lab Guide:** [D2-knowledge-and-devin-review.md](D2-knowledge-and-devin-review.md)
- **Objective:** Configure Knowledge notes and Devin Review for OtterWorks, then observe how this one-time investment improves every subsequent session and PR review

**Key Takeaways:**
- The shared context layer (Knowledge, Playbooks, Blueprints) is the highest-leverage investment for Devin adoption
- Knowledge notes encode what you would tell a new team member — coding standards, architecture decisions, conventions
- Devin Review applies your standards automatically to every PR — consistent quality without manual review effort
- Configure once, benefit everywhere — every team member's sessions inherit the same context

---

### Lab D3 — Full Automation Pipeline (Capstone) (60-90 min)

- **Lab Guide:** [D3-full-automation-pipeline.md](D3-full-automation-pipeline.md)
- **Objective:** Design and implement a complete Devin automation strategy with three layers: event-driven (real-time), scheduled (recurring), and on-demand (playbooks)

**Key Takeaways:**
- A complete Devin automation strategy has three layers: event-driven (seconds), scheduled (weekly/monthly), on-demand (human-initiated)
- The Ask → Session → Playbook → Automation progression is how teams mature their Devin adoption
- The shared context layer is the foundation that makes all three layers effective
- Devin is a team-based resource, not an individual tool — the automation strategy serves the whole team

---

<a id="choosing-a-track"></a>

## Choosing a Track

| Participant Background | Recommended Track |
|---|---|
| Backend / full-stack developers, migration experience | Track A: Modernization & Migration |
| SRE, DevOps, platform engineers, on-call experience | Track B: Incident Response & Automation |
| Security engineers, QA leads, test automation | Track C: Security & Quality |
| Engineering managers, architects, Devin champions | Track D: Devin Platform Mastery |
| Mixed audience | Let participants self-select — all tracks use the same repo |
| Short event (2 hours) | Pick one lab from any track |

**Recommended progression for new Devin adopters:** Start with one lab from Tracks A-C (hands-on engineering), then do D2 (Knowledge & Review) to see how the platform amplifies results. Finish with D3 (Capstone) if time allows.

<a id="duration-variants"></a>

## Duration Variants

| Duration | Recommended Format |
|----------|-------------------|
| 8 hours (full day) | All four tracks available, participants pick 3-4 labs across 2 tracks |
| 6 hours | Two tracks in depth, participants complete all 3 labs in their primary track + 1 from another |
| 4 hours | Single track: all 3 labs with breaks |
| 3 hours | Single track: 2 labs (skip the third or abbreviate) |
| 2 hours | Pick 1 lab from any track + 30 min discussion |

<a id="lab-quick-reference"></a>

## Lab Quick Reference

| Lab | Track | Difficulty | Time | Devin Pattern Showcased |
|-----|-------|-----------|------|------------------------|
| A1 — ETL Modernization | Modernization | Medium | 60-90 min | Large-scale migration |
| A2 — Framework Upgrade | Modernization | Medium | 60-90 min | EOL/dependency upgrade campaign |
| A3 — Language Translation | Modernization | Medium | 60-90 min | Framework translation with contract preservation |
| B1 — Investigate Incident | Automation | Medium | 45-60 min | Event-driven triggers (alert → Devin) |
| B2 — From Ask to Playbook | Automation | Easy | 45-60 min | Ask → Session → Playbook progression |
| B3 — Dependency Lifecycle | Automation | Medium | 45-60 min | Divide-and-conquer with child agents |
| C1 — Security Sprint | Security | Medium | 60-90 min | Parallel remediation across services |
| C2 — Contract Audit | Security | Medium | 45-60 min | Cross-service schema analysis |
| C3 — Scheduled Automation | Security | Easy | 45-60 min | Scheduled sessions for recurring maintenance |
| D1 — Event-Driven SAST | Platform | Medium | 45-60 min | Event-driven automation topology |
| D2 — Knowledge & Review | Platform | Easy | 45-60 min | Shared context layer configuration |
| D3 — Full Automation (Capstone) | Platform | Hard | 60-90 min | Complete automation lifecycle |

## Repos Required

- [ ] [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks)

## Key Takeaways

- **"Enterprise codebases are messy — Devin thrives in messy"** — real-world problems span multiple languages, services, and concerns. Devin navigates this naturally.
- **"Craft the prompt, don't paste it"** — participants learn to decompose problems and write effective Devin prompts, which is the skill that transfers to their day jobs.
- **"Ask → Session → Playbook → Automation"** — the maturity progression for Devin adoption. Start manual, codify what works, automate the proven patterns.
- **"Parallel sessions for parallel problems"** — dependency campaigns, security sprints, and multi-service changes benefit from child agents executing simultaneously.
- **"Guides and specs are force multipliers"** — pointing Devin at an upgrade guide, OpenAPI spec, or runbook template dramatically improves output quality.
- **"The shared context layer compounds"** — Knowledge notes, Playbooks, and Blueprints are a one-time investment that improves every future session for every team member.
- **"Event-driven Devin scales beyond manual sessions"** — CI triggers, incident webhooks, and scheduled sessions show how Devin operates as team-scale infrastructure, not just a personal assistant.
