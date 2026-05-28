# Workshop: Banking Modernization — Hands-On with Devin

## Event Details

| | |
|---|---|
| **Date** | TBD |
| **Location** | TBD |
| **Host Organization** | *(customer)* |
| **Duration** | ~1 hour (3 labs × 20 min) |
| **Audience** | Banking and financial services teams evaluating AI-assisted development across modernization, feature delivery, and security |
| **Tracks** | Single progressive track: Modernize → Build → Secure |

## Workshop Overview

This is a hands-on workshop demonstrating three high-impact use cases for AI-assisted software engineering in a banking context:

- **Lab 1 — Mainframe UI Modernization:** Devin reads COBOL copybook record layouts and 3270 BMS screen definitions from a real mainframe credit card management application, then generates a modern Angular frontend that replaces the terminal interface — complete with components, services, routing, and responsive design.

- **Lab 2 — Banking Feature Development:** Devin builds a new account statement and transaction history feature on an existing Java 21 / Spring Boot 3.2 banking microservices platform — adding API endpoints, DTOs, service logic, and tests that follow the codebase's established patterns.

- **Lab 3 — Vulnerability Remediation with SonarQube:** Devin runs SonarQube analysis on a Spring Boot application, triages the findings, remediates the critical vulnerabilities, and re-scans to verify the fixes — demonstrating the scan → fix → verify loop.

## Getting the Most from This Workshop

> **Devin works autonomously on its own machine.** Once you paste a
> prompt and kick off a session, Devin runs independently — you don't
> need to watch it. Move on to the next lab, explore Ask Devin, or
> grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin keeps working in the background.
- **Overlap sessions.** Kick off Lab 2's Devin session while reviewing Lab 1's PR. Devin works in the background — there's no reason to wait. This is how teams use Devin in production.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem before Devin executes.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments and Devin will wake up and address them — this is the core feedback loop.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.

## Table of Contents

- [Agenda](#agenda)
- [Lab 1 — Mainframe UI Modernization: Angular Frontend for CardDemo](#lab-1--mainframe-ui-modernization-angular-frontend-for-carddemo-20-min)
- [Lab 2 — Banking Feature Development: Account Statements](#lab-2--banking-feature-development-account-statements-20-min)
- [Lab 3 — Vulnerability Remediation with SonarQube](#lab-3--vulnerability-remediation-with-sonarqube-20-min)
- [Repos Required](#repos-required)
- [Workshop Key Takeaways](#workshop-key-takeaways)

---

## Agenda

| Time | Activity | Lab |
|------|----------|-----|
| 0:00 | Welcome, Devin overview, platform walkthrough | — |
| 0:10 | **Lab 1:** Mainframe UI Modernization | Lab 1 |
| 0:30 | **Lab 2:** Banking Feature Development | Lab 2 |
| 0:50 | **Lab 3:** Vulnerability Remediation | Lab 3 |
| 1:10 | Wrap-up, showcase results, Q&A | — |

> **Tip:** Kick off each lab's Devin session immediately, then use Ask
> Devin or DeepWiki while Devin works. By the time you finish exploring,
> Devin will have a PR ready for review.

---

<a id="lab-1"></a>

## Lab 1 — Mainframe UI Modernization: Angular Frontend for CardDemo (20 min)

**Value driver:** *Devin reads COBOL copybook record layouts and 3270 screen definitions from a mainframe credit card application and generates a modern Angular frontend — replacing decades-old terminal screens with a responsive web UI in a single session.*

- **Repository:** [aws-mainframe-modernization-carddemo](https://github.com/Cognition-Partner-Workshops/aws-mainframe-modernization-carddemo)
- **Module:** [COBOL System Understanding](../../../modules/migration-modernization/cobol-system-understanding.md)

The CardDemo application is a real mainframe credit card management system with 29 COBOL programs, 30 copybooks defining record layouts (accounts, customers, cards, transactions), and 17 BMS screen maps for 3270 terminal interactions. The COBOL programs handle account viewing/updating, credit card management, transaction processing, bill payments, and user administration — all through green-screen terminal interfaces.

In this lab, Devin analyzes the COBOL data structures and screen definitions, then generates a complete Angular application that provides the same functionality through a modern web interface.

### Paste into Devin

```
Analyze the mainframe CardDemo application in
aws-mainframe-modernization-carddemo and generate a modern
Angular frontend that replaces the 3270 terminal screens.

**Step 1 — Understand the data model:** Read the COBOL
copybooks in `app/cpy/` to extract the domain entities:
- `CVACT01Y.cpy` — Account record (balance, credit limit,
  dates, status)
- `CUSTREC.cpy` — Customer record (name, address, SSN,
  FICO score, DOB)
- `CVACT02Y.cpy` — Card record (card number, CVV, expiry,
  embossed name, status)
- `CVACT03Y.cpy` — Card cross-reference (card ↔ customer
  ↔ account mapping)

**Step 2 — Understand the screens:** Read the BMS screen
maps in `app/bms/` to understand the 3270 terminal
layouts, then cross-reference with the online COBOL
programs in `app/cbl/` to identify user-facing functions:

BMS maps (screen layouts):
- `COACTVW.bms` / `COACTUP.bms` — Account screens
- `COCRDLI.bms` / `COCRDSL.bms` / `COCRDUP.bms` —
  Credit card screens
- `COTRN00.bms` / `COTRN01.bms` / `COTRN02.bms` —
  Transaction screens
- `COBIL00.bms` — Bill payment screen
- `COSGN00.bms` — Sign-on screen

COBOL programs (business logic):
- `COACTVWC.cbl` / `COACTUPC.cbl` — Account view/update
- `COCRDLIC.cbl` / `COCRDSLC.cbl` / `COCRDUPC.cbl` —
  Credit card list/view/update
- `COTRN00C.cbl` / `COTRN01C.cbl` / `COTRN02C.cbl` —
  Transaction list/view/add
- `COBIL00C.cbl` — Bill payment
- `COSGN00C.cbl` — Sign-on screen

**Step 3 — Generate the Angular app:** Create a new
`frontend/` directory with a complete Angular 17+
application:

1. **TypeScript interfaces** matching each COBOL copybook
   record layout (Account, Customer, Card, Transaction)
2. **Angular services** with mock data derived from the
   ASCII feed files in `app/data/ASCII/` (acctdata.txt,
   custdata.txt, carddata.txt, cardxref.txt)
3. **Components and routing** for each screen:
   - Dashboard (account summary)
   - Account list and detail view
   - Credit card list and detail view
   - Transaction list with filtering and a transaction
     add form
   - Bill payment form
   - Login page
4. **Angular Material** for UI components (tables, forms,
   cards, navigation)
5. **Responsive layout** with sidebar navigation replacing
   the mainframe menu system

Include a `MODERNIZATION_NOTES.md` documenting the mapping
from COBOL copybook fields to TypeScript interfaces and
from BMS screens to Angular components.
```

### While Devin works: try Ask Devin

- *"What are the main business entities in the CardDemo COBOL copybooks? How do they relate to each other?"*
- *"How many screens does the CardDemo online application have? What functions does each screen provide?"*

### Review the PR

When Devin opens a PR:
- Does the TypeScript interface match the COBOL copybook field definitions?
- Are all the mainframe screens represented in the Angular routing?
- **Leave a comment** asking Devin to add a specific enhancement — e.g., "Add a card activation/deactivation toggle to the credit card detail view"

### Key Takeaways

- **"From green screen to web app in one session"** — Devin reverse-engineers mainframe data structures and screen flows, then generates a modern Angular frontend that preserves the same business functionality
- **"Data model extraction is automated"** — COBOL copybooks with PIC clauses, COMP-3 packed decimals, and FILLER fields are translated into clean TypeScript interfaces
- **"The mapping is documented"** — the modernization notes create a traceable link between legacy COBOL and modern Angular, which is critical for migration governance

---

<a id="lab-2"></a>

## Lab 2 — Banking Feature Development: Account Statements (20 min)

**Value driver:** *Devin analyzes an existing banking microservices codebase, understands the architecture and conventions, then builds a complete new feature — API endpoints, service logic, DTOs, and tests — that fits seamlessly into the existing system.*

- **Repository:** [ts-java-spring-boot-internet-banking](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking)
- **Module:** [New Feature Development](../../../modules/application-development/new-feature-development.md)

This is a Java 21 / Spring Boot 3.2.4 banking application with 6 microservices: core-banking, fund-transfer, user-service, utility-payment, API gateway, and service registry. It uses Keycloak for authentication, RabbitMQ for messaging, and Zipkin for distributed tracing.

The existing API supports account lookup, fund transfers, and utility payments — but has no account statement or transaction history feature. Participants will have Devin build this missing capability end-to-end.

### Paste into Devin

```
Add an account statement and transaction history feature
to ts-java-spring-boot-internet-banking. This is a Java 21
/ Spring Boot 3.2.4 banking microservices application
using Gradle, Spring Data JPA, Flyway, and Lombok.

Analyze the existing codebase architecture first — look at
how core-banking-service, fund-transfer-service, and
utility-payment-service are structured (controllers,
services, repositories, DTOs, entities). Follow the same
patterns.

Build the following in the core-banking-service:

1. **Schema evolution:**
   The existing `banking_core_transaction` table has no
   timestamp column. Add a Flyway migration in
   `src/main/resources/db/migration/` that adds a
   `created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP`
   column and a `description VARCHAR(255)` column to
   `banking_core_transaction`. Update
   `TransactionEntity.java` to include the new fields.

2. **Account Statement API:**
   - `GET /api/v1/account/{account_number}/statement` —
     returns a paginated list of transactions for an
     account, with optional date range filtering
     (`fromDate`, `toDate` query params) and transaction
     type filtering (`type` param: FUND_TRANSFER,
     UTILITY_PAYMENT, or ALL)
   - Response should include: transaction ID, date,
     description, amount, type (credit/debit), running
     balance, and reference number

3. **Monthly Summary API:**
   - `GET /api/v1/account/{account_number}/summary` —
     returns monthly aggregated totals: total credits,
     total debits, net change, opening balance, closing
     balance, and transaction count — for a given month
     (`month` and `year` query params)

4. **Implementation:**
   - DTOs: `StatementResponse`, `TransactionHistoryDto`,
     `MonthlySummaryResponse` (use Lombok `@Data`
     classes to match the existing DTO style)
   - Service layer with proper business logic
   - Repository queries using Spring Data JPA
     `@Query` or Specification for date/type filtering
   - Input validation (`@Valid`, custom validators for
     date ranges)
   - Error handling consistent with the existing
     `GlobalExceptionHandler`
   - OpenAPI `@Operation` and `@Tag` annotations
     matching the existing controller style

5. **Tests:**
   - Unit tests for the service layer (use Mockito,
     follow the pattern in `TransactionServiceTest`)
   - Integration tests for the new API endpoints
   - Note: the existing `TransactionServiceTest` has a
     compilation error (all-args constructor call on a
     `@Data` class) — fix it if encountered

Follow existing code conventions: Lombok `@Data`/
`@Builder` for DTOs, `@Getter`/`@Setter`/`@Builder`
for entities, the existing package structure under
`com.javatodev.finance`.
```

### While Devin works: try Ask Devin

- *"What communication patterns do the banking microservices use? How does fund-transfer-service talk to core-banking-service?"*
- *"What database schema does core-banking-service use? What tables and relationships exist?"*

### Review the PR

When Devin opens a PR:
- Does the new feature follow the existing code conventions (package structure, DTO style, error handling)?
- Are the API contracts well-designed for a banking application?
- **Leave a comment** asking Devin to add CSV/PDF export for the account statement — watch Devin respond and push a follow-up commit

### Key Takeaways

- **"Devin learns your patterns first"** — before writing a single line, Devin analyzes the existing codebase to understand architecture, conventions, and coding style
- **"Full-stack feature in one session"** — from API design through service logic, persistence, validation, and tests — following the codebase's established patterns
- **"The PR feedback loop is the workflow"** — reviewing and commenting on the PR is how you steer Devin, just like working with a team member

---

<a id="lab-3"></a>

## Lab 3 — Vulnerability Remediation with SonarQube (20 min)

**Value driver:** *Devin runs SonarQube analysis, interprets the findings, remediates the critical vulnerabilities, and re-scans to verify — turning a security backlog into remediated code with evidence.*

- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Module:** [Remediate Vulnerabilities](../../../modules/security/remediate-vulnerabilities.md), [Shift Left Security](../../../modules/security/shift-left-security.md)

This is a Spring Boot 2.6.3 / Java 11 application with known vulnerable dependencies and pre-configured SonarQube and OWASP Dependency-Check Gradle plugins. The repo includes a `docker-compose.sonarqube.yml` for local SonarQube setup. The combination of an outdated Spring Boot version (2.6.3), transitive SnakeYAML vulnerabilities, and sqlite-jdbc 3.36.0.3 creates a realistic vulnerability landscape for remediation.

### Paste into Devin

```
Perform a comprehensive security remediation on
uc-cve-remediation-regulatory-compliance. This is a Spring
Boot 2.6.3 / Java 11 application with known vulnerable
dependencies and pre-configured security scanning tools.

1. **Upgrade and Run OWASP Dependency-Check:**
   The repo ships with dependency-check plugin v7.4.4,
   which uses the retired NVD v1.1 data feed. First
   upgrade the plugin to 10.x+ in `build.gradle` (the
   `org.owasp.dependencycheck` plugin line), then run:
   `./gradlew dependencyCheckAnalyze`
   The report is at
   `build/reports/dependency-check-report.html`.

2. **Run SonarQube Analysis:**
   Start the pre-configured SonarQube instance:
   `docker compose -f docker-compose.sonarqube.yml up -d`
   Wait for SonarQube to be ready at http://localhost:9000
   (default credentials: admin/admin), create a project
   token, then run:
   `./gradlew sonar -Dsonar.token=<YOUR_TOKEN>`
   Note: the task is `sonar`, not `sonarqube`.

3. **Triage Findings:**
   Create `SECURITY_TRIAGE.md` documenting findings with:
   - CVE ID (for dependency vulnerabilities)
   - Severity (Critical/High/Medium/Low)
   - Affected component (dependency name + version, or
     source file + line)
   - Description and recommended fix
   - Priority for remediation (fix now vs. accept risk)

4. **Remediate Critical and High Findings:**
   Focus on the top findings:
   - Upgrade Spring Boot from 2.6.3 to latest 2.7.x
     (address Spring4Shell and related CVEs). Note:
     upgrading to 3.x requires Java 17+ and significant
     API changes — 2.7.x is the safer path.
   - Upgrade SnakeYAML (transitive dependency via Spring
     Boot — override version to fix CVE-2022-1471)
   - Upgrade sqlite-jdbc from 3.36.0.3 (known CVEs)
   - Fix any code-level security issues SonarQube
     reports (SQL injection, hardcoded credentials,
     insecure crypto, etc.)

5. **Verify:**
   Re-run both scans after remediation. Document the
   before/after results in `SECURITY_REMEDIATION.md`
   with:
   - Summary table: vulnerability count before vs. after
   - Specific CVEs resolved
   - Any remaining findings with justification for
     deferral

6. **Add CI Gating:**
   Add a GitHub Actions workflow that runs OWASP
   Dependency-Check on every PR and fails the build if
   any CRITICAL severity CVEs are found.
```

### While Devin works: try Ask Devin

- *"What are the known CVEs in the Spring Boot 2.6.3 dependency tree? Which ones are CRITICAL?"*
- *"What's the safest upgrade path for Spring Boot 2.6.3 — should we go to 2.7.x first or jump to 3.x?"*

### Review the PR

When Devin opens a PR:
- Are the dependency upgrades safe? Do the existing tests still pass?
- Does the triage document accurately describe the vulnerabilities?
- **Leave a comment** asking Devin to add a pre-commit hook for secrets detection using gitleaks

### Key Takeaways

- **"Scan, triage, fix, verify"** — Devin follows the complete security remediation lifecycle, not just blindly upgrading versions
- **"Evidence-based remediation"** — before/after scan results provide auditable evidence for compliance teams
- **"CI gating prevents regression"** — the added workflow ensures new vulnerabilities are caught before merge, shifting security left

---

## Repos Required

The following repos must be available in the workshop org:

- [ ] [aws-mainframe-modernization-carddemo](https://github.com/Cognition-Partner-Workshops/aws-mainframe-modernization-carddemo) (Lab 1)
- [ ] [ts-java-spring-boot-internet-banking](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking) (Lab 2)
- [ ] [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) (Lab 3)

### Participant Requirements

- [ ] Devin account access
- [ ] GitHub access to the workshop org
- [ ] Browser (Chrome recommended)

## Workshop Key Takeaways

- **"Modernization is more than rewriting code"** — Lab 1 shows Devin extracting business knowledge locked in COBOL copybooks and 3270 screens, then using that understanding to generate a modern UI. The legacy system is the specification.
- **"Devin follows your conventions"** — Lab 2 demonstrates that Devin reads existing patterns before implementing, producing code that fits the codebase — not generic boilerplate.
- **"Security remediation at speed"** — Lab 3 turns a vulnerability backlog into remediated code with auditable evidence in a single session, using the scan → triage → fix → verify → gate workflow.
- **"Asynchronous by design"** — all three labs run in parallel. While reviewing Lab 1's PR, Lab 2 and Lab 3 are already working. This mirrors how teams use Devin in production — multiple sessions running concurrently across different workstreams.
