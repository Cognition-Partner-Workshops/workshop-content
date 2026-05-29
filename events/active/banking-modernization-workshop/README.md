# Workshop: Banking Modernization — Hands-On with Devin

## Event Details

| | |
|---|---|
| **Date** | TBD |
| **Location** | TBD |
| **Host Organization** | *(customer)* |
| **Duration** | ~1 hour (3 labs, parallel execution) |
| **Audience** | Banking and financial services teams evaluating AI-assisted development across modernization, feature delivery, and security |
| **Tracks** | Single progressive track: Modernize → Build → Secure |

## Workshop Overview

This is a hands-on workshop demonstrating three high-impact use cases for AI-assisted software engineering in a banking context:

- **Lab 1 — Mainframe UI Modernization:** Devin reads COBOL copybook record layouts and 3270 BMS screen definitions from a real mainframe credit card management application, then generates TypeScript interfaces and an Angular component — showing how decades-old data structures map to modern web technologies.

- **Lab 2 — Banking Feature Development:** Devin analyzes an existing Java 21 / Spring Boot 3.2 banking microservices platform, learns the codebase patterns, and builds a new API endpoint that fits seamlessly into the architecture.

- **Lab 3 — Security Remediation:** Devin identifies vulnerable dependencies in a Spring Boot application, upgrades them, fixes breaking API changes, runs tests to verify, and documents the security impact.

## Getting the Most from This Workshop

> **All three sessions run in parallel.** You kick off all three
> prompts in the first five minutes, then explore Ask Devin and
> DeepWiki while Devin works. PRs start arriving within 10–15
> minutes — then you review, leave comments, and watch Devin respond.

A few tips to maximize your hands-on time:

- **Kick off all sessions immediately.** Paste all three prompts in rapid succession (the first 5 minutes). Devin sessions run independently on separate machines — there's no reason to wait between them.
- **Use Ask Devin while sessions run.** Ask Devin questions about the codebases to build context. By the time you finish exploring, PRs will be ready for review.
- **Leave PR comments to steer Devin.** After Devin opens a PR, leave a comment and Devin will wake up and address it — this is the core feedback loop.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.

## Table of Contents

- [Agenda](#agenda)
- [Lab 1 — Mainframe UI Modernization](#lab-1--mainframe-ui-modernization-angular-frontend-for-carddemo)
- [Lab 2 — Banking Feature Development](#lab-2--banking-feature-development-account-statements)
- [Lab 3 — Security Remediation with SonarQube](#lab-3--security-remediation-with-sonarqube)
- [Stretch Goals: Full-Scope Prompts](#stretch-goals-full-scope-prompts)
- [Repos Required](#repos-required)
- [Workshop Key Takeaways](#workshop-key-takeaways)

---

## Agenda

| Time | Activity |
|------|----------|
| 0:00–0:05 | Welcome, Devin overview, platform walkthrough |
| 0:05–0:10 | **Kick off all 3 sessions** (paste prompts) |
| 0:10–0:20 | Ask Devin exploration + DeepWiki (sessions running) |
| 0:20–0:35 | Review first PR(s) arriving — leave comments |
| 0:35–0:50 | Review remaining PRs — watch Devin respond to feedback |
| 0:50–1:00 | Wrap-up, showcase stretch-goal prompts, Q&A |

> **Why parallel?** In production, teams run 10–50 Devin sessions
> concurrently across different workstreams. This workshop mirrors
> that workflow — kick off, context-switch, review when ready.

---

<a id="lab-1"></a>

## Lab 1 — Mainframe UI Modernization: Angular Frontend for CardDemo

**Value driver:** *Devin reads COBOL copybook record layouts and 3270 screen definitions from a mainframe credit card application and generates modern TypeScript interfaces — showing how PIC clauses, COMP-3 packed decimals, and FILLER fields map to clean typed models.*

- **Repository:** [aws-mainframe-modernization-carddemo](https://github.com/Cognition-Partner-Workshops/aws-mainframe-modernization-carddemo)
- **Module:** [COBOL System Understanding](../../../modules/migration-modernization/cobol-system-understanding.md)

The CardDemo application is a real mainframe credit card management system with 29 COBOL programs, 30 copybooks defining record layouts (accounts, customers, cards, transactions), and 17 BMS screen maps for 3270 terminal interactions.

### Paste into Devin

```
Analyze the mainframe CardDemo application in
aws-mainframe-modernization-carddemo and generate modern
TypeScript models from the COBOL data structures.

**Step 1 — Extract the data model:** Read the COBOL
copybooks in `app/cpy/` to understand the domain entities:
- `CVACT01Y.cpy` — Account record (balance, credit limit,
  dates, status)
- `CUSTREC.cpy` — Customer record (name, address, SSN,
  FICO score, DOB)
- `CVACT02Y.cpy` — Card record (card number, CVV, expiry,
  embossed name, status)
- `CVACT03Y.cpy` — Card cross-reference (card ↔ customer
  ↔ account mapping)

**Step 2 — Map the screens:** Read 3–4 BMS screen maps in
`app/bms/` (start with `COSGN00.bms`, `COACTVW.bms`, and
`COCRDLI.bms`) to understand the 3270 terminal layouts
and field positions.

**Step 3 — Generate a frontend starter:** Create a new
`frontend/` directory with:

1. **TypeScript interfaces** (`frontend/src/models/`) —
   one interface per copybook record, mapping COBOL PIC
   clauses to TypeScript types (PIC X → string,
   COMP-3 → number, etc.). Include JSDoc comments
   showing the original COBOL field definition.
2. **Mock data** (`frontend/src/data/mock-accounts.ts`) —
   derive 5–10 sample records from the ASCII feed files
   in `app/data/ASCII/` (acctdata.txt, custdata.txt,
   carddata.txt)
3. **One Angular component** — an Account List page
   (`frontend/src/app/accounts/`) using Angular Material
   table that imports and displays the mock account data
4. **Minimal Angular setup** — `package.json`,
   `angular.json`, app module, and routing so the
   component is reachable at `/accounts`

Include a `MODERNIZATION_NOTES.md` at the repo root
documenting:
- Field-level mapping from COBOL copybook → TypeScript
- BMS screen → future Angular component mapping table
- Data type conversion rules used
```

### While Devin works: try Ask Devin

- *"What are the main business entities in the CardDemo COBOL copybooks? How do they relate to each other?"*
- *"How many screens does the CardDemo online application have? What functions does each screen provide?"*
- *"What data types does COBOL use in the copybooks? How would you map COMP-3 to a modern language?"*

### Review the PR

When Devin opens a PR:
- Do the TypeScript interfaces accurately reflect the COBOL copybook field definitions?
- Are PIC clauses correctly mapped (PIC X(30) → string, COMP-3 → number, PIC 9(2) → number)?
- Does the MODERNIZATION_NOTES.md provide a clear traceable mapping?
- **Leave a comment** asking Devin to add a second component — e.g., "Add a Credit Card List component using the card interface and mock data"

### Key Takeaways

- **"From green screen to typed models in minutes"** — Devin reverse-engineers mainframe data structures and generates clean TypeScript interfaces with full traceability
- **"Data model extraction is automated"** — COBOL copybooks with PIC clauses, COMP-3 packed decimals, and FILLER fields become clean TypeScript types with documented conversion rules
- **"The mapping is documented"** — MODERNIZATION_NOTES.md creates a traceable link between legacy COBOL and modern code, critical for migration governance

---

<a id="lab-2"></a>

## Lab 2 — Banking Feature Development: Account Statements

**Value driver:** *Devin analyzes an existing banking microservices codebase, understands the architecture and conventions, then builds a new API endpoint that fits seamlessly into the existing system — following the same patterns, annotations, and package structure.*

- **Repository:** [ts-java-spring-boot-internet-banking](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking)
- **Module:** [New Feature Development](../../../modules/application-development/new-feature-development.md)

This is a Java 21 / Spring Boot 3.2.4 banking application with 6 microservices: core-banking, fund-transfer, user-service, utility-payment, API gateway, and service registry. It uses Keycloak for authentication, RabbitMQ for messaging, and Zipkin for distributed tracing.

### Paste into Devin

```
Add a transaction history endpoint to
ts-java-spring-boot-internet-banking. This is a Java 21 /
Spring Boot 3.2.4 banking microservices application using
Gradle, Spring Data JPA, and Lombok.

First, analyze how the existing services are structured —
look at core-banking-service's controllers, services,
repositories, DTOs, and entities. Follow the same patterns
exactly.

Build the following in core-banking-service:

1. **DTO:** Create `TransactionHistoryDto` in the existing
   DTO package — include transaction ID, amount, type
   (FUND_TRANSFER or UTILITY_PAYMENT), reference number,
   and the existing `transactionDateTime` field. Use
   Lombok `@Data`/`@Builder` matching existing DTO style.

2. **Repository query:** Add a method to the existing
   `TransactionRepository` that finds transactions by
   account number (check `TransactionEntity` for the
   correct field name), ordered by most recent first.

3. **Service method:** Add a `getTransactionHistory`
   method to `TransactionService` that retrieves
   transactions for an account and maps entities to the
   DTO.

4. **Controller endpoint:** Add
   `GET /api/v1/account/{accountNumber}/transactions`
   to the existing controller. Return a list of
   `TransactionHistoryDto`. Include `@Operation` and
   `@Tag` annotations matching the existing style.

5. **One unit test:** Add a test for the service method
   following the pattern in `TransactionServiceTest`.

Follow existing code conventions: Lombok `@Data`/
`@Builder` for DTOs, `@Getter`/`@Setter`/`@Builder`
for entities, the existing package structure under
`com.javatodev.finance`.
```

### While Devin works: try Ask Devin

- *"What communication patterns do the banking microservices use? How does fund-transfer-service talk to core-banking-service?"*
- *"What database schema does core-banking-service use? What tables and relationships exist?"*
- *"What annotations and patterns does the existing TransactionController use?"*

### Review the PR

When Devin opens a PR:
- Does the new endpoint follow the existing code conventions (package structure, DTO style, annotations)?
- Is the repository query correct for the actual entity field names?
- **Leave a comment** asking Devin to add pagination — e.g., "Add `page` and `size` query params with Spring Data Pageable support"

### Key Takeaways

- **"Devin learns your patterns first"** — before writing a single line, Devin analyzes the existing codebase to understand architecture, conventions, and coding style
- **"Pattern-following, not boilerplate"** — the generated code uses the same annotations, package structure, and naming as the rest of the codebase
- **"The PR feedback loop is the workflow"** — reviewing and commenting on the PR is how you steer Devin, just like working with a team member

---

<a id="lab-3"></a>

## Lab 3 — Security Remediation with SonarQube

**Value driver:** *Devin connects to SonarQube via MCP to fetch real findings, remediates the critical vulnerabilities, fixes breaking API changes from the upgrades, and verifies the quality gate — showing the tool-augmented remediation loop.*

- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Module:** [Remediate Vulnerabilities](../../../modules/security/remediate-vulnerabilities.md), [Shift Left Security](../../../modules/security/shift-left-security.md)
- **MCP Integration:** [SonarQube MCP](https://docs.devin.ai/work-with-devin/mcp#sonarqube) (must be installed on the workshop org)

This is a Spring Boot 2.6.3 / Java 11 application with known vulnerable dependencies. The project is connected to SonarCloud, which tracks vulnerabilities, code smells, and security hotspots. Devin uses the SonarQube MCP to read findings directly from SonarCloud — no manual scanning required.

### Paste into Devin

```
Perform a targeted security remediation on
uc-cve-remediation-regulatory-compliance. This is a Spring
Boot 2.6.3 / Java 11 application with known vulnerable
dependencies.

1. **Check SonarQube findings:** Use the SonarQube MCP to
   fetch the current open issues and quality gate status
   for this project. List any vulnerabilities, security
   hotspots, and bugs by severity.

2. **Identify dependency vulnerabilities:** Review
   `build.gradle` and identify the outdated dependencies:
   - Spring Boot 2.6.3 (Spring4Shell, multiple CVEs)
   - SnakeYAML (transitive via Spring Boot — known RCE:
     CVE-2022-1471)
   - sqlite-jdbc 3.36.0.3 (multiple CVEs)
   Document what you find in a brief `SECURITY_TRIAGE.md`.

3. **Upgrade Spring Boot:** Upgrade from 2.6.3 to the
   latest 2.7.x in `build.gradle`. Note: upgrading to
   3.x requires Java 17+ — stay on 2.7.x for this lab.

4. **Fix breaking changes:** Spring Boot 2.7 deprecates
   `WebSecurityConfigurerAdapter`. Migrate the security
   config to use `SecurityFilterChain` @Bean method
   instead. Fix any other compilation errors.

5. **Override transitive vulnerabilities:** Add version
   overrides in `build.gradle` for:
   - SnakeYAML → 2.0+ (fixes CVE-2022-1471)
   - sqlite-jdbc → 3.42.0.1+ (fixes known CVEs)

6. **Verify:** Run `./gradlew test` to confirm all tests
   pass after upgrades. Create `SECURITY_REMEDIATION.md`
   documenting: which dependencies were upgraded, which
   CVEs are resolved, and the before/after versions.
```

### While Devin works: try Ask Devin

- *"What are the known CVEs in Spring Boot 2.6.3? Which ones are CRITICAL severity?"*
- *"What's the safest upgrade path for Spring Boot 2.6.3 — should we go to 2.7.x or jump to 3.x?"*
- *"What is CVE-2022-1471 (SnakeYAML) and why is it dangerous?"*

### Review the PR

When Devin opens a PR:
- Are the dependency upgrades safe? Do existing tests still pass?
- Was the SecurityFilterChain migration done correctly?
- Does the remediation document accurately list resolved CVEs?
- **Leave a comment** asking Devin to add a CI check — e.g., "Add a GitHub Actions workflow that runs OWASP dependency-check on PRs"

### Key Takeaways

- **"MCP connects Devin to your tools"** — Devin reads SonarQube findings through MCP rather than running scans manually, mirroring how enterprise teams work with centralized quality platforms
- **"Upgrade + fix + verify in one session"** — Devin doesn't just bump versions; it fixes the breaking API changes and runs tests to verify nothing regressed
- **"Evidence-based remediation"** — the triage and remediation documents provide auditable evidence for compliance teams

---

<a id="stretch-goals"></a>

## Stretch Goals: Full-Scope Prompts

The workshop prompts above are scoped for ~10-minute execution so you see results during the session. Below are comprehensive versions of each lab that produce larger deliverables (60–90 min execution). Try these after the workshop to see the full capability.

<details>
<summary><strong>Lab 1 Full Scope: Complete Angular Application</strong></summary>

Generates a complete 10-component Angular Material application with full routing, responsive layout, mock data services, and login flow — replacing the entire 3270 terminal interface.

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

</details>

<details>
<summary><strong>Lab 2 Full Scope: Complete Account Statement Feature</strong></summary>

Builds a full account statement feature with Flyway schema migration, two API endpoints, monthly summary aggregation, comprehensive tests, and OpenAPI documentation.

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

</details>

<details>
<summary><strong>Lab 3 Full Scope: Comprehensive Security Audit + CI Gating</strong></summary>

Uses SonarQube MCP to fetch baseline findings, performs full OWASP Dependency-Check scan, comprehensive triage, multi-dependency remediation, before/after verification, and adds CI gating workflow.

```
Perform a comprehensive security remediation on
uc-cve-remediation-regulatory-compliance. This is a Spring
Boot 2.6.3 / Java 11 application with known vulnerable
dependencies and pre-configured security scanning tools.

1. **Check SonarQube baseline:** Use the SonarQube MCP to
   fetch the current quality gate status, open
   vulnerabilities, security hotspots, and bugs. This is
   the "before" snapshot for comparison.

2. **Upgrade and Run OWASP Dependency-Check:**
   The repo ships with dependency-check plugin v7.4.4,
   which uses the retired NVD v1.1 data feed. First
   upgrade the plugin to 10.x+ in `build.gradle` (the
   `org.owasp.dependencycheck` plugin line), then run:
   `./gradlew dependencyCheckAnalyze`
   The report is at
   `build/reports/dependency-check-report.html`.

3. **Triage Findings:**
   Combine the SonarQube MCP findings and OWASP report.
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
   Re-run OWASP and use the SonarQube MCP to check the
   updated quality gate. Document before/after results
   in `SECURITY_REMEDIATION.md` with:
   - Summary table: vulnerability count before vs. after
   - Specific CVEs resolved
   - Any remaining findings with justification for
     deferral

6. **Add CI Gating:**
   Add a GitHub Actions workflow that runs OWASP
   Dependency-Check on every PR and fails the build if
   any CRITICAL severity CVEs are found.
```

</details>

---

## Repos Required

The following repos must be available in the workshop org:

- [ ] [aws-mainframe-modernization-carddemo](https://github.com/Cognition-Partner-Workshops/aws-mainframe-modernization-carddemo) (Lab 1)
- [ ] [ts-java-spring-boot-internet-banking](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking) (Lab 2)
- [ ] [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) (Lab 3)

### MCP Setup (Lab 3)

Lab 3 uses the **SonarQube MCP** so Devin can read findings directly from SonarCloud. Install it before the workshop:

1. Go to **Settings → MCP Marketplace → SonarQube** ([setup link](https://docs.devin.ai/work-with-devin/mcp#sonarqube))
2. Provide:
   - **SonarQube URL:** `https://sonarcloud.io`
   - **SonarQube Org:** the SonarCloud organization name
   - **SonarQube Token:** generate at [My Account → Security](https://sonarcloud.io/account/security)
3. Verify the project appears — Devin should be able to list issues for `uc-cve-remediation-regulatory-compliance`

### Participant Requirements

- [ ] Devin account access
- [ ] GitHub access to the workshop org
- [ ] Browser (Chrome recommended)
- [ ] SonarQube MCP installed on the workshop org (for Lab 3)

## Workshop Key Takeaways

- **"Modernization is more than rewriting code"** — Lab 1 shows Devin extracting business knowledge locked in COBOL copybooks and 3270 screens, then using that understanding to generate modern typed models. The legacy system is the specification.
- **"Devin follows your conventions"** — Lab 2 demonstrates that Devin reads existing patterns before implementing, producing code that fits the codebase — not generic boilerplate.
- **"Security remediation at speed"** — Lab 3 turns a vulnerability backlog into remediated code with passing tests in a single session, handling the breaking changes that make manual upgrades tedious.
- **"MCP extends Devin's reach"** — Lab 3 shows Devin reading SonarQube findings through MCP, demonstrating how tool integrations give Devin access to the same platforms your team already uses.
- **"Asynchronous by design"** — all three labs run in parallel. While reviewing one PR, the others are still working. This mirrors how teams use Devin in production — multiple sessions running concurrently across different workstreams.
- **"The PR is the interface"** — every lab ends with reviewing a PR and leaving a comment. This is the real workflow: Devin proposes, you review, you steer with feedback, Devin iterates.
