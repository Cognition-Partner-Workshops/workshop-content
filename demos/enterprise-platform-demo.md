# Enterprise Platform Modernization — End-to-End Demo

A single threaded demo that walks through how Devin operates across a large
enterprise portfolio: discover an unfamiliar codebase, ship a full-stack
feature, remediate security debt at scale, upgrade a legacy framework, and
modernize mainframe code — each phase building on the same platform
capabilities (DeepWiki, playbooks, child sessions, event-driven automation).

<a id="toc"></a>
## Table of Contents

- [Phase 1 — Discover: Map an Unfamiliar Codebase](#phase-1)
- [Phase 2 — Feature Development: Full-Stack Delivery](#phase-2)
- [Phase 3 — Security Remediation: Event-Driven at Scale](#phase-3)
- [Phase 4 — Framework Upgrade: Spring Boot 2.x → 3.x](#phase-4)
- [Phase 5 — Legacy Modernization: Mainframe COBOL → Java](#phase-5)
- [Phase 6 — Scale: Parallelize with Child Sessions](#phase-6)
- [The Pattern](#the-pattern)
- [Key Takeaways](#key-takeaways)

---

<a id="phase-1"></a>
## Phase 1 — Discover: Map an Unfamiliar Codebase

A developer joins a team that owns a Spring Boot backend with an Angular
frontend and a microservices variant. Before writing a single line of code,
they use DeepWiki and Ask Devin to map the estate.

### Browse DeepWiki

Open DeepWiki for the PetClinic repos:

```
https://partner-workshops.devinenterprise.com/org/internal/wiki
```

Navigate to the `petclinic-rest-api` wiki. DeepWiki has already generated
architectural documentation — the entity relationships, controller layers,
OpenAPI spec summary, and database configuration — without any prompt. This
is the starting point: a pre-generated architectural reference available
for connected repos.

### Investigate with Ask Devin

For deeper questions that DeepWiki doesn't answer out of the box, use
**Ask Devin** (no VM — investigation against static code only):

```
Using the petclinic-rest-api repo, map the OpenAPI spec at
src/main/resources/openapi.yml: how many operations, schemas,
and domain areas? How do the API domains (owner, pet, vet,
visit, pettypes, specialty) relate to each other through
the Spring Data JPA entity relationships? What bean validation
rules constrain the inputs?
```

Ask Devin reads the static code and returns a structured answer — no VM
spun up, no session cost. This is requirements gathering and investigation
before assigning real work.

### Cross-repo discovery

Still in Ask Devin, compare the monolith with the microservices variant:

```
Compare the architecture of petclinic-backend (monolith) with
petclinic-microservices (distributed version). Identify which
domain boundaries were extracted into separate services, what
Spring Cloud components were added (config server, discovery,
API gateway), and what cross-cutting concerns (auth, tracing,
circuit breaking) the microservices variant handles differently.
```

Expected: Ask Devin identifies the bounded contexts (customers, vets, visits)
extracted into separate Spring Boot services, the Spring Cloud Netflix/Gateway
infrastructure, and the trade-offs introduced by the distributed architecture.

The point: before assigning a Devin Session to do real work, the team has an
architectural map from DeepWiki + Ask Devin. This is how you scope work
before committing compute — the same way a lead engineer would investigate
before writing a ticket, but in minutes instead of days.

---

<a id="phase-2"></a>
## Phase 2 — Feature Development: Full-Stack Delivery

The enterprise needs a new feature shipped on an existing full-stack
application. Devin analyzes existing patterns, implements backend + frontend,
writes tests, and opens a PR — the same SDLC flow a human engineer would
follow.

### Add a full-stack feature

Open the timesheet-app (React + Node.js/Express) and paste:

```
Add a "Projects" management feature to timesheet-app.
Users should be able to create, view, edit, and delete
projects. Each project has a name, description, client
assignment, start date, and status (active/completed/
on-hold).

Add both the backend API endpoints and the frontend UI
page. Follow the existing patterns in the codebase for
the data model, API structure, and React components.
Write tests for the backend endpoints.
```

Watch Devin:
1. Read the existing CRUD features (clients, work entries) to learn the
   conventions — routing structure, state management, API design, database
   schema
2. Create the database migration, model, API routes, and React components
   following those same conventions
3. Write backend tests for the new endpoints
4. Open a PR with a description of what was added

### The PR feedback loop

Once the PR is open, leave a review comment:

```
Add validation for the project dates — start date should not be in the
past, and add status transition rules (completed projects cannot be set
back to active).
```

Devin reads the comment, implements the validation, pushes a follow-up
commit, and the CI re-runs. This is the collaboration model: the engineer
reviews and steers; Devin implements and iterates.

### Add a Spring Boot API endpoint

For a Java Spring Boot example, open the loan service and paste:

```
Add a loan payment history API to
uc-data-source-migration-jdbc-normalization. Create a
new endpoint GET /api/loans/:id/payments that returns
a paginated list of payment records for a given loan.
Include filtering by date range and payment type. Add
proper error handling for invalid loan IDs. Write
JUnit tests for the new endpoint.
```

Devin reads the existing Spring Data JPA patterns, the legacy CDW-style
tables (all-VARCHAR, cryptic column names like `BORR_FST_NM`, `LN_CURR_BAL`),
and the column mappings — then implements the endpoint following the same
conventions. The point: Devin adapts to whatever codebase conventions exist,
including legacy data shapes.

---

<a id="phase-3"></a>
## Phase 3 — Security Remediation: Event-Driven at Scale

The enterprise runs a polyglot microservices platform — 10 backend services in
Go, Rust, Python, Java, Kotlin, Scala, Ruby, C#, and Node.js, plus two
frontends. Security findings pile up across 9 languages. No single engineer
owns the full stack.

### The problem: a real vulnerability backlog

Open the SonarCloud dashboard for OtterWorks:

```
https://sonarcloud.io/project/overview?id=Cognition-Partner-Workshops_otterworks
```

| Metric | Count | What it means |
|--------|-------|---------------|
| Vulnerabilities | 29 | Hard-coded secrets, weak crypto, missing S3 bucket ownership checks |
| Security Hotspots | 51 | CSRF disabled, recursive COPY in Dockerfiles, ReDoS-prone regex |
| Bugs | 24 | Logic errors across services |
| Code Smells | 434 | Maintainability issues |
| Lines of Code | 33,857 | Across 10 backend services + 2 frontends |

Manually triaging and fixing these across 9 languages is a multi-day effort
for an engineering team. Devin handles it as a background agent.

### Burn down the backlog

Paste this prompt to start remediating:

```
Review the SonarCloud dashboard for otterworks at
https://sonarcloud.io/project/issues?id=Cognition-Partner-Workshops_otterworks&types=VULNERABILITY&resolved=false

There are 29 open vulnerabilities across the polyglot
monorepo. Triage them by severity (BLOCKER first, then
CRITICAL, then MAJOR). For each one:

1. Read the SonarCloud issue detail to understand the fix.
2. Check out a branch from main.
3. Fix the vulnerability in the correct file.
4. Run the affected service's tests.
5. Push the fix.

Group related fixes per service into single PRs when
possible (e.g., all docker-compose.yml password issues
in one PR, all Dockerfile COPY issues in another).

Skip findings in test files — those are acceptable.
```

Devin reads the SonarCloud API, understands each vulnerability, and fixes them
in the correct language and manifest. It knows that `docker-compose.yml`
passwords need environment variable references, that `seed.py` needs stronger
bcrypt rounds, and that Dockerfiles need `.dockerignore` or targeted COPY
statements. One agent, 9 languages — no human triaging which file goes to
which team.

### The CI gate: event-driven pipeline

The same architecture prevents *new* vulnerabilities from shipping. The
`sast-auto-remediate.yml` workflow on the otterworks repo supports two
scanner paths:

```
Developer opens PR
        ↓
GitHub Actions: sast-auto-remediate.yml
        ↓
┌──────────────────────┬───────────────────────────────┐
│ PATH 1: Trivy        │ PATH 2: SonarCloud            │
│ (pull_request)       │ (check_run)                   │
│                      │                               │
│ Trivy fs scan        │ SonarCloud GitHub App         │
│ HIGH + CRITICAL      │ analyzes PR automatically     │
│     ↓                │     ↓                         │
│ Findings?            │ Quality gate failed?          │
│   No → pass          │   No → pass                  │
│   Yes ↓              │   Yes ↓                       │
│                      │                               │
│ attempts < 2?        │ Already attempted fix?        │
│   No → escalate      │   Yes → skip (one-time)      │
│   Yes ↓              │   No ↓                        │
│                      │                               │
│ Post PR comment      │ Validate PR state             │
│     ↓                │     ↓                         │
├──────────────────────┴───────────────────────────────┤
│           Devin v3 API → remediation session         │
│                                                      │
│  Devin checks out branch, fixes vulnerabilities,     │
│  runs service tests, pushes fix commit               │
│           ↓                                          │
│  Push triggers re-scan (closed loop):                │
│  • Trivy: synchronize event                          │
│  • SonarCloud: new check_run                         │
└──────────────────────────────────────────────────────┘
```

Open a PR to trigger it live:

```bash
cd otterworks
git checkout -b workshop-test-sast main
echo "# Security pipeline test" >> README.md
git add README.md
git commit -m "docs: trigger SAST pipeline"
git push origin workshop-test-sast
```

```bash
gh pr create --title "docs: trigger SAST pipeline" \
  --body "Testing event-driven security remediation" \
  --base main
```

Nobody asked Devin to do anything. The PR event triggered a Trivy scan, the
scan found vulnerabilities, and the workflow called the Devin API. Devin checks
out the branch and upgrades dependencies across Python, Node.js, Ruby, Rust,
and Go — from a single pipeline.

**Guardrails:** Bot-loop prevention (author check + attempt counter),
concurrency groups (no duplicate sessions), one-time remediation (SonarCloud
path), and human escalation after max attempts. If Devin cannot resolve a
finding after two attempts, the workflow opens a GitHub Issue labeled
`security` and `needs-human-review`. The system knows its limits.

---

<a id="phase-4"></a>
## Phase 4 — Framework Upgrade: Spring Boot 2.x → 3.x

The enterprise runs dozens of Spring Boot services. Many are still on 2.6.x
with known CVEs. Upgrading each one is the same well-defined procedure —
update dependencies, handle the `javax` → `jakarta` namespace migration, fix
breaking API changes, run the tests — repeated across the portfolio.

### Upgrade one service

Open the Spring Boot RealWorld monolith and paste:

```
Upgrade uc-spring-boot-upgrade-microservice-extraction from
Spring Boot 2.6.3 to the latest stable 3.x. Handle the
javax → jakarta namespace migration, update all transitive
dependencies, fix any breaking API changes, and ensure the
build and tests pass. Document which CVEs are resolved by
the upgrade.
```

Devin:
1. Reads the Gradle build, identifies Spring Boot 2.6.3, Java 11
2. Updates the Spring Boot starter to 3.x, switches to Java 17
3. Performs the `javax` → `jakarta` namespace migration across the source files
4. Updates MyBatis, Flyway, and other dependencies for compatibility
5. Fixes breaking changes in the REST layer
6. Runs `./gradlew build` and iterates until green
7. Opens a PR with the upgrade diff and CVE resolution report

### Run the CVE analysis before and after

For a more security-focused angle, use the companion repo with OWASP
Dependency-Check pre-configured:

```
Upgrade uc-cve-remediation-regulatory-compliance from
Spring Boot 2.6.3 to the latest stable 2.7.x or 3.x,
updating all transitive dependencies. Run
./gradlew dependencyCheckAnalyze before and after to
document which CVEs are resolved. Verify the build
still passes.
```

The before/after CVE report makes the business case concrete: X critical
CVEs eliminated by the framework upgrade.

### Portfolio-scale upgrades

This is where child sessions transform the economics. Instead of upgrading
one service at a time, a parent session encodes the upgrade methodology as
a playbook, spawns a child session per service, and monitors progress.

The same upgrade that takes weeks sequentially — 50 services × 4 hours each
= 200 engineer-hours — runs in parallel as 50 child sessions, completed in
days. Each child follows the same playbook independently, each producing its
own PR with a green build.

---

<a id="phase-5"></a>
## Phase 5 — Legacy Modernization: Mainframe COBOL → Java

The enterprise still runs mainframe workloads — batch processing, transaction
management, credit card operations. These COBOL programs need to move to
modern Java services. Most engineers cannot read COBOL.

### Orient over the COBOL estate

Open the CardDemo mainframe application:

```
Analyze the COBOL programs in
uc-legacy-modernization-cobol-to-java. Map the
application: the batch programs in app/cbl/ (CBACT01C,
CBACT02C, CBTRN01C, CBSTM03A), the online programs,
the copybooks, the JCL orchestration, and the DB2
integration. For each program, identify the business
logic, the COBOL-specific constructs (PERFORM, EVALUATE,
packed decimal, VSAM file I/O), and rate its migration
complexity (low, medium, high).
```

Expected: Devin maps 30+ COBOL programs, the copybook dependencies,
the JCL batch orchestration, and the DB2 SQL. It rates `CBACT01C.cbl`
(account file processing) as medium complexity and `CBTRN01C.cbl`
(transaction processing) as high.

### Convert one program live

Paste the migration prompt for an account processing program:

```
Analyze the COBOL program CBACT01C.cbl in
uc-legacy-modernization-cobol-to-java. Understand its
business logic, data structures (copybooks), and I/O
operations. Rewrite it as a Java 17+ application using
modern idioms. Create JUnit tests that verify the Java
version produces identical results to the COBOL version
for a set of sample inputs.
```

Devin:
1. Reads the COBOL source — WORKING-STORAGE, PERFORM paragraphs, packed
   decimal fields, file I/O sections
2. Maps COBOL data structures to Java classes (copybooks → POJOs,
   packed decimal → BigDecimal)
3. Translates the PERFORM logic to method calls, EVALUATE to switch
   statements, file I/O to Java NIO or JDBC
4. Generates JUnit parity tests comparing Java output against known COBOL
   behavior
5. Opens a PR with the Java code, tests, and migration notes documenting
   the key translation decisions

### The verification beat

The critical question: does the Java version produce the same results? The
JUnit parity tests compare computed outputs — account balances, transaction
totals, status codes — against the COBOL baseline. A conversion that
"looks reasonable" but silently changes a packed decimal rounding rule gets
caught by the parity tests.

This is the same pattern as every other phase: convert, then verify
programmatically. "Looks right" is not the bar; "passes the parity tests" is.

---

<a id="phase-6"></a>
## Phase 6 — Scale: Parallelize with Child Sessions

Every phase so far converted or fixed one thing at a time. In production, the
enterprise has dozens of services to upgrade, hundreds of vulnerabilities to
remediate, and 30+ COBOL programs to migrate. Child sessions make this
practical.

### COBOL migration at scale

Instead of migrating one program at a time, a parent session orchestrates:

```
Act as the orchestrator for a COBOL-to-Java migration
across multiple programs in
uc-legacy-modernization-cobol-to-java, using child
Devin sessions to parallelize the work.

Spawn one child session per program below. Give each
child the repo and tell it to: analyze the COBOL source,
map data structures to Java equivalents, translate the
business logic, generate JUnit parity tests, and include
migration notes.

Programs:
1. app/cbl/CBACT01C.cbl — Account file batch processing
2. app/cbl/CBACT02C.cbl — Account data processing
3. app/cbl/CBTRN01C.cbl — Transaction processing
4. app/cbl/CBSTM03A.CBL — Statement generation

After launching, monitor the child sessions until each
program is converted with passing parity tests. Summarize
results and flag any programs where the parity tests
revealed behavioral divergences.
```

Four programs, four child sessions, four PRs — each independently verified.
The same orchestration pattern works for security remediation (one child per
service) and framework upgrades (one child per repo).

### Security remediation at scale

The same pattern applies to the polyglot OtterWorks estate:

```
You are coordinating a polyglot security remediation
across the otterworks monorepo.

Run make security-scan and capture the output. Create a
SECURITY_BACKLOG.md that lists all CRITICAL and HIGH
findings organized by service.

Then launch parallel child sessions — one per affected
service — with scoped instructions:
- Each child works only on its assigned service directory
- Each child upgrades the vulnerable dependency, runs
  the service tests, and pushes to the same branch
- After all children complete, summarize results in
  REMEDIATION_SUMMARY.md
```

One scan produces a backlog. Multiple agents remediate in parallel. The
parent monitors and reports.

---

<a id="the-pattern"></a>
## The Pattern

Every phase in this demo follows the same structure:

| Step | Phase 2: Feature Dev | Phase 3: Security | Phase 4: Upgrade | Phase 5: COBOL |
|------|---------------------|-------------------|-----------------|----------------|
| **Orient** | DeepWiki maps the codebase patterns | SonarCloud dashboard shows the backlog | Dependency analysis identifies CVEs | Devin maps 30+ COBOL programs |
| **Convert one** | Add a full-stack feature following existing conventions | Fix one vulnerability in the correct language | Upgrade one service: javax → jakarta + tests | Translate one program: COBOL → Java + parity tests |
| **Verify** | PR feedback loop — reviewer comments, Devin iterates | Closed-loop re-scan: Trivy + SonarCloud re-analyze | `./gradlew dependencyCheckAnalyze` before/after | JUnit parity tests: Java output matches COBOL baseline |
| **Fan out** | Multiple features in parallel sessions | One child per service, 9 languages | One child per repo in the portfolio | One child per COBOL program |
| **Automate** | Devin Review on every PR | Event-driven pipeline: PR → scan → fix → re-scan | Scheduled sessions for weekly version audits | Event-driven: COBOL change → auto-update Java + re-test |

What changes across phases is the technology. What stays the same is the
platform:

- **DeepWiki** maps the unfamiliar estate
- The **playbook** promotes run-to-run consistency
- **Child sessions** parallelize independent units of work
- **Programmatic verification** (contract tests, reconciliation controls,
  parity tests, SAST re-scans) gates every PR — "looks right" is never the
  bar
- **Event-driven automation** (webhooks, scheduled sessions) keeps the
  pipeline running without human initiation

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Devin operates across the full SDLC** — from feature development on
  modern stacks (React, Angular, Spring Boot, Node.js) to security
  remediation across 9 languages to mainframe modernization. The same
  platform, the same collaboration model, applied to different domains.

- **Confidence comes from programmatic verification, not review.** Every
  phase catches a real issue through automated checks — contract tests find
  missing validation, SAST re-scans confirm vulnerability fixes,
  dependency analysis proves CVE resolution, parity tests catch behavioral
  divergences in COBOL translations. "Looks reasonable" review would have
  missed them.

- **Event-driven automation shifts security left.** The dual-scanner pipeline
  (Trivy + SonarCloud) fires on every PR, remediates findings
  autonomously, and escalates to humans when it reaches its limits. The
  vulnerability exposure window shrinks from "next sprint" to "next CI run."

- **Child sessions transform the economics of scale.** Framework upgrades,
  security remediation, and legacy migration are inherently parallelizable.
  A portfolio of 50 services that takes weeks sequentially completes in days
  when each service gets its own agent following the same playbook.

- **The platform compounds over time.** Knowledge notes capture coding
  standards. Playbooks encode proven methodologies. Environment blueprints
  boot sessions ready to build. MCP integrations connect to Jira, Datadog,
  Confluence. Each configuration is a one-time investment that benefits
  future sessions across the organization.
