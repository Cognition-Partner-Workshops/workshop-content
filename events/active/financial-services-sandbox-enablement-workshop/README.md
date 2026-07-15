# Workshop: Financial Services Sandbox Enablement — Orchestrating the Full SDLC with Devin

## Event Details

| | |
|---|---|
| **Date** | TBD |
| **Location** | TBD |
| **Host Organization** | *(customer)* |
| **Duration** | ~1 hour (2 hands-on labs, parallel execution) |
| **Format** | Hybrid — short opening walkthrough, then hands-on |
| **Audience** | Consulting partner practitioners (delivery engineers, solution architects, engagement leads) preparing to position Devin with a banking client and run a customer pilot |
| **Industry Focus** | Financial services / banking (payments, retail & internet banking, loan servicing) |
| **Tracks** | Single progressive track: Build (end-to-end SDLC) → Secure (defect & vulnerability resolution) |

## Workshop Overview

This is a hands-on enablement workshop for practitioners who will position Devin with a financial services client. The goal is to leave comfortable running customer sessions that map to a banking client's top use cases, with a sandbox you can keep using internally.

The central message: **Devin orchestrates the entire software development lifecycle — not just writing code.** In most banking organizations the bottleneck is not typing speed; it is turning requirements into designs, designs into tested implementations, and implementations into review-ready, compliant PRs. Devin operates across that whole arc while keeping testing, security, and governance as first-class citizens.

This workshop positions Devin according to a few core principles (see [`reference/general-themes/`](../../../reference/general-themes/) for the full narrative):

- **Devin is a team-based resource, not an individual's assistant.** Configuration, indexed codebases, Knowledge notes, playbooks, and integrations belong to the team and compound over time (see [Architecture Strengths → Shared Context Layer](../../../reference/general-themes/architecture-strengths.md#shared-context-layer)).
- **Devin is an on-demand, cloud-based engineering agent** that does well-scoped work triggered by events (a ticket, a CI failure, a security finding) or run as part of large-scale efforts (modernization, framework migration for licensing cost reduction). See [When to Use Devin](../../../reference/general-themes/when-to-use-devin.md).
- **Devin starts with access to nothing** (clean-room) and receives scoped, service-account credentials for specific systems — a model that maps directly to a bank's security and compliance controls.
- **Devin verifies its own work** by making code locally buildable and testable on its VM, or by connecting to runners and external systems for integration validation (see [Design Patterns](../../../reference/general-themes/design-patterns-for-devin.md)).
- **Value is measured by faster delivery, not just faster coding** — capacity unlocked, cycle time reduced, SDLC standardized, quality and security held constant while throughput rises (see [Value Narratives](../../../reference/general-themes/value-narratives.md)).

The two labs mirror the client's highest-priority use cases:

- **Lab 1 — End-to-End Brownfield Feature Delivery (highest priority):** On a real Java 21 / Spring Boot 3.2 banking microservices platform, Devin runs the full SDLC for a new feature — from a short requirements spec and technical design, through implementation, test generation, and quality gates, to a review-ready PR.
- **Lab 2 — Security & Defect Resolution:** Devin triages findings on a Spring Boot application with known vulnerable dependencies, performs root-cause analysis, proposes and applies fixes, verifies with tests, and produces an auditable, PR-ready remediation.

Application and cloud modernization — a lower priority for this client — is covered in [Going Further](#going-further) so you can extend the sandbox after the session.

## Quick Start

Experienced with Devin? Jump straight in:

1. Open Devin and confirm the two workshop repos are connected: `ts-java-spring-boot-internet-banking` and `uc-cve-remediation-regulatory-compliance`.
2. Paste the [Lab 1 prompt](#lab-1--paste-into-devin) and the [Lab 2 prompt](#lab-2--paste-into-devin) into two separate sessions in the first few minutes.
3. While both run, use [Ask Devin](#lab-1--while-devin-works-try-ask-devin) and DeepWiki to explore the codebases.
4. Review each PR, leave a comment to steer Devin, and watch it respond.

## Getting the Most from This Workshop

> **Devin works autonomously on its own machine.** Once you paste a prompt
> and kick off a session, Devin runs independently — you don't need to watch
> it. Kick off both labs in the first few minutes, then explore Ask Devin and
> DeepWiki while Devin works. PRs typically start arriving within 10–15
> minutes — then you review, comment, and watch Devin respond.

A few tips to maximize hands-on time:

- **Kick off both sessions immediately.** Paste both prompts in rapid succession. Devin sessions run independently on separate machines — there's no reason to wait between them. This mirrors how banking teams run many sessions concurrently across workstreams.
- **Use Ask Devin while sessions run.** Ask questions about the codebases to build context. By the time you finish exploring, a PR is usually ready to review.
- **Leave PR comments to steer Devin.** After Devin opens a PR, leave a comment and Devin wakes up and addresses it — this is the core feedback loop and the same collaboration model your client's engineers will use.
- **Build up Devin's Knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how a team builds a shared context layer that compounds across future sessions.

## Table of Contents

- [Agenda](#agenda)
- [Lab 1 — End-to-End Brownfield Feature Delivery](#lab-1--end-to-end-brownfield-feature-delivery)
- [Lab 2 — Security & Defect Resolution](#lab-2--security--defect-resolution)
- [Devin Features Exercised](#devin-features-exercised)
- [Repos Required](#repos-required)
- [Going Further](#going-further)
- [Workshop Key Takeaways](#workshop-key-takeaways)

---

## Agenda

| Time | Activity |
|------|----------|
| 0:00–0:07 | Welcome, positioning: Devin orchestrates the full SDLC (not just coding) |
| 0:07–0:12 | **Kick off both sessions** (paste Lab 1 and Lab 2 prompts) |
| 0:12–0:25 | Ask Devin exploration + DeepWiki (sessions running) |
| 0:25–0:40 | Review Lab 1 PR — spec → design → code → tests — leave a comment |
| 0:40–0:52 | Review Lab 2 PR — triage → RCA → fix → verify — leave a comment |
| 0:52–1:00 | Wrap-up: differentiation, Going Further, next steps toward the pilot |

> **Why parallel?** In production, banking teams run many Devin sessions
> concurrently across features, fixes, and migrations. This workshop mirrors
> that workflow — kick off, context-switch, review when ready.

---

<a id="lab-1"></a>

## Lab 1 — End-to-End Brownfield Feature Delivery

**Value driver:** *Devin runs the full SDLC on an existing banking platform — turning a one-line feature request into a short spec, a technical design, a tested implementation, and a review-ready PR — showing that the differentiator is orchestrating the whole lifecycle, not typing code faster.*

- **Repository:** [ts-java-spring-boot-internet-banking](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking)
- **Modules:** [Gather Requirements](../../../labs/application-development/gather-requirements.md), [New Feature Development](../../../labs/application-development/new-feature-development.md), [Test-Driven Development](../../../labs/application-development/test-driven-development.md)

This is a Java 21 / Spring Boot 3.2.4 internet banking platform with 6 services: `core-banking-service`, `internet-banking-fund-transfer-service`, `internet-banking-user-service`, `internet-banking-utility-payment-service`, `internet-banking-api-gateway`, and `internet-banking-service-registry`. It uses Gradle, Spring Data JPA, Lombok, Flyway migrations, Keycloak for auth, RabbitMQ for messaging, and Zipkin for tracing. The `core-banking-service` persists transactions in a `banking_core_transaction` table under the `com.javatodev.finance` package.

This lab intentionally spans the full lifecycle so you can narrate each stage to a client: **requirements → technical design → implementation → tests → quality gates → PR-ready output.**

<a id="lab-1--paste-into-devin"></a>

### Paste into Devin

```
Deliver a new "Account Statement & Transaction History" feature
end-to-end in ts-java-spring-boot-internet-banking. This is a
Java 21 / Spring Boot 3.2.4 banking microservices application
using Gradle, Spring Data JPA, Flyway, and Lombok. Work entirely
inside core-banking-service and follow the existing patterns.

Run the full lifecycle and produce artifacts at each stage:

1. Requirements (SPEC.md at the core-banking-service root):
   - Analyze the existing code first: TransactionController
     (/api/v1/transaction), TransactionService,
     TransactionRepository, and the TransactionEntity mapped to
     the banking_core_transaction table.
   - Write user stories and testable acceptance criteria for an
     account statement / transaction history capability
     (list transactions for an account, filter by date range and
     transaction type, paginate results).

2. Technical design (DESIGN.md at the core-banking-service root):
   - Endpoint contracts, request/response DTOs, the repository
     query, the service method, and the schema change needed.
   - Note that TransactionEntity currently has no timestamp
     column and describe the Flyway migration to add one.

3. Implementation (in core-banking-service, matching existing
   conventions — package com.javatodev.finance, Lombok
   @Data/@Builder DTOs, @Getter/@Setter entities):
   - Flyway migration under
     src/main/resources/db/migration/ that adds a
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP column to
     banking_core_transaction; update TransactionEntity.
   - A TransactionHistoryDto (transaction id, amount, type,
     reference number, timestamp).
   - A repository query on TransactionRepository that finds
     transactions by account number, most recent first.
   - A getTransactionHistory method on TransactionService that
     maps entities to the DTO with date-range and type filtering.
   - GET /api/v1/account/{accountNumber}/transactions on the
     controller, returning a paginated list, with @Operation and
     @Tag annotations matching existing style.

4. Tests: add service-layer tests following the pattern in
   TransactionServiceTest, covering the happy path, an empty
   result, and date-range filtering.

5. Quality gates: run ./gradlew test for core-banking-service and
   confirm it is green. Summarize the lifecycle in the PR
   description: what changed at each stage, the acceptance
   criteria met, and how the change was verified.
```

<a id="lab-1--while-devin-works-try-ask-devin"></a>

### While Devin works: try Ask Devin

- *"What communication patterns do these banking microservices use? How does fund-transfer-service talk to core-banking-service?"*
- *"What does the banking_core_transaction schema look like today, and what would need to change to support statements?"*
- *"What annotations and conventions does the existing TransactionController use that a new endpoint should match?"*

Open the repo's **DeepWiki** page to read the auto-generated architecture overview and confirm the service boundaries before reviewing the PR. Coverage depends on repo structure, so treat it as a fast orientation, not a spec.

### Review the PR

When Devin opens a PR:

- Does `SPEC.md` capture testable acceptance criteria, and does `DESIGN.md` match what was actually built?
- Does the endpoint follow existing conventions (package structure, DTO style, annotations)?
- Is the Flyway migration correct, and does the repository query use the real entity field names?
- Do the tests pass, and does the PR description narrate each lifecycle stage?
- **Leave a comment** to steer Devin — for example, *"Add a monthly summary endpoint returning total credits, total debits, and net change for a given month and year."*

### Key Takeaways

- **"Devin orchestrates the whole lifecycle"** — requirements, design, implementation, tests, and quality gates in one session, with an artifact at each stage that a delivery lead can inspect
- **"Requirements and design are where the leverage is"** — the spec and design documents are the differentiator; they turn a vague ask into governed, review-ready work
- **"Pattern-following, not boilerplate"** — Devin analyzes the codebase first and matches its conventions, so the change reads like the team wrote it
- **"Verification is built in"** — Devin runs the build and tests on its own VM before asking for review, keeping quality a first-class citizen

---

<a id="lab-2"></a>

## Lab 2 — Security & Defect Resolution

**Value driver:** *Devin triages security findings, performs root-cause analysis, applies and verifies fixes, and produces an auditable remediation PR — compressing the exposure window from "next sprint" to "next review" without loosening compliance.*

- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Modules:** [Remediate Vulnerabilities](../../../labs/security/remediate-vulnerabilities.md), [Shift Left Security](../../../labs/security/shift-left-security.md), [Upgrade Dependencies](../../../labs/security/upgrade-dependencies.md)
- **MCP integration (optional):** if a SAST tool's MCP server (SonarQube, Snyk, or similar) is connected on the workshop org, Devin can read findings directly instead of analyzing manifests — the lab works either way

This is a Spring Boot 2.6.3 / Java 11 application with known vulnerable dependencies, pre-configured with OWASP Dependency-Check and SonarQube Gradle plugins for local scanning. It stands in for a bank's brownfield service carrying accumulated dependency risk.

<a id="lab-2--paste-into-devin"></a>

### Paste into Devin

```
Triage and remediate the security findings in
uc-cve-remediation-regulatory-compliance. This is a Spring Boot
2.6.3 / Java 11 Gradle application with known vulnerable
dependencies and OWASP Dependency-Check + SonarQube plugins
configured.

1. Triage: review build.gradle and identify the outdated,
   vulnerable dependencies. If a SAST tool MCP (SonarQube, Snyk,
   or similar) is connected, use it to fetch current findings and
   quality-gate status as well. Group findings by severity.

2. Root-cause analysis: for the highest-severity findings,
   explain the root cause and the safe upgrade path. Capture this
   in SECURITY_TRIAGE.md — include the vulnerable component, the
   relevant CVE(s), severity, and the recommended fix. Cover at
   least Spring Boot 2.6.3 (Spring4Shell and related CVEs),
   SnakeYAML (transitive RCE, CVE-2022-1471), and
   sqlite-jdbc 3.36.0.3.

3. Fix: upgrade Spring Boot from 2.6.3 to the latest 2.7.x in
   build.gradle (staying on 2.7.x keeps Java 11 compatibility;
   3.x would require Java 17+). Add version overrides for
   SnakeYAML (2.0+) and sqlite-jdbc (3.42.0.1+). Migrate the
   deprecated WebSecurityConfigurerAdapter to a
   SecurityFilterChain @Bean and fix any other breaking changes.

4. Verify: run ./gradlew test and confirm it is green after the
   upgrades. Write SECURITY_REMEDIATION.md documenting which
   dependencies were upgraded, which CVEs are resolved, and the
   before/after versions.
```

### While Devin works: try Ask Devin

- *"What are the known CVEs in Spring Boot 2.6.3, and which are CRITICAL severity?"*
- *"What is CVE-2022-1471 (SnakeYAML) and why is it dangerous in a banking service?"*
- *"What's the safest upgrade path from Spring Boot 2.6.3 given this app targets Java 11?"*

### Review the PR

When Devin opens a PR:

- Does `SECURITY_TRIAGE.md` correctly identify the root cause and severity of each finding?
- Are the upgrades safe, and do the existing tests still pass?
- Was the `WebSecurityConfigurerAdapter` → `SecurityFilterChain` migration done correctly?
- Does `SECURITY_REMEDIATION.md` give compliance-grade evidence of what was resolved?
- **Leave a comment** to steer Devin — for example, *"Add a GitHub Actions workflow that runs OWASP Dependency-Check on every pull request so this stays green."*

### Key Takeaways

- **"Triage, RCA, fix, verify — in one session"** — Devin doesn't just bump versions; it explains the root cause, fixes breaking API changes, and runs tests to confirm nothing regressed
- **"Evidence-based remediation"** — the triage and remediation documents are auditable artifacts for the bank's compliance and governance teams
- **"MCP connects Devin to your tools"** — where a SAST platform's MCP server is available, Devin reads findings directly, mirroring how enterprise security teams already work
- **"Exposure windows shrink"** — the same pattern runs event-driven (finding detected → remediation session) so risk is addressed as detected, not deferred to a sprint

---

<a id="devin-features"></a>

## Devin Features Exercised

| Feature | Where | What it shows |
|---------|-------|---------------|
| **Ask Devin** | Both labs | Codebase Q&A for orientation and impact analysis without changing code |
| **DeepWiki** | Lab 1 | Auto-generated architecture docs for fast onboarding to a brownfield banking platform |
| **PR feedback loop** | Both labs | Steering Devin via PR comments — the core human-in-the-loop collaboration model |
| **Knowledge notes** | Both labs | Accepting suggested Knowledge builds a shared, compounding team context layer |
| **Local build & test verification** | Both labs | Devin runs `./gradlew test` on its VM to verify before requesting review |
| **MCP integration** | Lab 2 (optional) | Reading SAST findings directly from a connected scanner |
| **Automations & scheduled sessions** | [Going Further](#going-further) | Event-driven and recurring versions of the same work |
| **Child sessions** | [Going Further](#going-further) | Divide-and-conquer for fleet-wide features, fixes, and migrations |

---

<a id="repos-required"></a>

## Repos Required

- [ ] [ts-java-spring-boot-internet-banking](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking) — Lab 1
- [ ] [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) — Lab 2
- [ ] *(optional, Going Further)* [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java), [uc-db-migration-sybase-to-sqlserver](https://github.com/Cognition-Partner-Workshops/uc-db-migration-sybase-to-sqlserver), or [fineract](https://github.com/Cognition-Partner-Workshops/fineract)

Confirm the required repos are connected in Devin's org settings, and trigger DeepWiki indexing, before the session. Facilitator setup, pacing, and positioning notes live in the [workshop-operations](https://github.com/Cognition-Partner-Workshops/workshop-operations) repo.

---

<a id="going-further"></a>

## Going Further

Extend the sandbox into real-world banking workflows after the session:

- **Event-driven automation (webhooks → Devin).** Wire the SDLC to signals your client already produces: a Jira ticket tagged for implementation triggers a Lab 1-style feature session; a failing CI check triggers a diagnose-and-fix session; a new SAST finding triggers a Lab 2-style remediation session. See [Design Patterns → Event-Driven Triggers](../../../reference/general-themes/design-patterns-for-devin.md).
- **Scheduled sessions for recurring O&M.** Run weekly dependency-bump sessions, a monthly compliance/license audit, or a quarterly framework-currency check — capacity-constrained work that rarely gets prioritized. See [Platform Capabilities → Scheduled Sessions](../../../reference/general-themes/platform-capabilities.md).
- **Divide and conquer with child sessions.** For fleet-wide work — the same feature or fix across many services, or a Spring Boot 2→3 upgrade across dozens of repos — a parent session fans work out to child sessions and consolidates the results. See [Platform Capabilities → Child Agents](../../../reference/general-themes/platform-capabilities.md).
- **Encode the methodology as Knowledge and Playbooks.** Turn this workshop's SDLC and remediation flows into org playbooks and Knowledge notes so every future session inherits your client's standards, terminology, and quality bar. See [Platform Capabilities → Playbooks](../../../reference/general-themes/platform-capabilities.md).
- **Run it as a presenter-led showcase.** For a customer-facing walkthrough of the Lab 1 flow — orient → deliver one feature live with verification → catch a real boundary bug → fan out in parallel — follow the single-thread [Brownfield Banking Feature, End-to-End demo](../../../demos/application-development/banking-feature-sdlc-demo.md), which invokes the reusable `!deliver-banking-feature-sdlc` playbook.
- **Modernization (lower priority for this client).** When the conversation turns to application/cloud modernization, extend into a [COBOL → Java](../../../labs/migration-modernization/cobol-to-java.md) migration on `uc-legacy-modernization-cobol-to-java`, a [database migration](https://github.com/Cognition-Partner-Workshops/uc-db-migration-sybase-to-sqlserver) with a reconciliation harness, or a cloud-native refactor on `fineract`. See the [Banking Modernization workshop](../banking-modernization-workshop/README.md) for a modernization-led variant.

---

<a id="workshop-key-takeaways"></a>

## Workshop Key Takeaways

- **The differentiator is the full SDLC.** Devin turns requirements into designs, designs into tested implementations, and implementations into governed PRs — where other AI coding assistants stop at code completion.
- **Requirements and technical design are the real bottlenecks** in most banking delivery, and this is where Devin removes the most friction.
- **Testing, compliance, and governance stay first-class.** Changes ship with tests run on Devin's VM and auditable artifacts (spec, design, triage, remediation) for review.
- **The objective is throughput with quality held constant** — more capacity and faster cycle time without loosening security or compliance controls, keeping headcount flat.
- **You leave with a reusable sandbox** and two flows that map to a banking client's top use cases, ready to customize for a joint customer workshop and next steps toward a pilot.
