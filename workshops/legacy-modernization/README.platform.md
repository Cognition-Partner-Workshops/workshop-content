# Workshop: Legacy Modernization

**Other variants:** [Cloud only](README.md)

## Overview

| | |
|---|---|
| **Focus** | Modernizing legacy applications (COBOL, Oracle Forms/PL/SQL) to modern tech stacks with comprehensive testing |
| **Duration** | 2-4 hours per track (configurable — see Duration Variants below) |
| **Audience** | Enterprise architects, modernization teams, application portfolio managers |
| **Tracks** | **COBOL Track** (mainframe → Java) · **Oracle Forms Track** (Forms/PL/SQL → Spring Boot) |
| **Delivery** | **Devin Desktop + Devin Cloud** — IDE-based workflow with cloud delegation |

## Platform Context

This workshop uses **Devin Desktop + Devin Cloud**. You will use Desktop as your primary interface — exploring code locally, delegating tasks to Cloud, and reviewing results without leaving the editor.

The workflow follows the platform continuum: use Cascade or Devin Local for fast code exploration, delegate implementation tasks to Devin Cloud for autonomous execution, then review Cloud-generated PRs with one-click checkout — no browser switching required. The Agent Command Center gives you a Kanban view of all sessions in flight.

> **Tip:** If you prefer a browser-only workflow, see the [Cloud only variant](README.md) which uses the Devin web app directly.

## Workshop Narrative

This workshop follows a progressive modernization sequence that applies to **both** legacy platforms:

1. **Understand** — What does this legacy system do? (system inventory, dependencies, business rules, technical debt)
2. **Plan** — How should we modernize it? (strategy options, component mapping, phased cutover)
3. **Safeguard** — How do we know the migration is correct? (test harness, golden files, reconciliation)
4. **Execute** — Translate legacy code to modern tech stack with test validation

Each phase's output feeds the next, mirroring how real enterprise modernization programs operate. Participants choose the track matching their legacy platform — or try both in a full-day event.

## Getting the Most from This Workshop

> **Devin Desktop is your command center.** Create a Space for this workshop to keep sessions, PRs, and files organized in one view. Delegate tasks to Devin Cloud and monitor progress in the Agent Command Center — a Kanban board showing all your agents by status.

A few tips to maximize your hands-on time:

- **Create a Space first.** Group all your workshop sessions, PRs, and files into a single Space so context carries across labs automatically.
- **Explore locally, execute in the cloud.** Use Cascade or Devin Local for code exploration and requirement refinement, then delegate implementation tasks to Devin Cloud for autonomous execution.
- **Monitor from the Agent Command Center.** The Kanban board shows all your agents (local and cloud) organized by status — see at a glance what is in flight, what is blocked, and what is ready for review.
- **Review PRs in the editor.** When Devin Cloud opens a PR, use one-click checkout to review it directly in Desktop — no manual `git fetch` or branch switching needed.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Leave PR comments to steer Devin.** After Devin opens a PR, leave comments and Devin Cloud will wake up and address them — this is the core feedback loop.
- **Try parallel cloud sessions.** Running multiple cloud sessions simultaneously mirrors real enterprise usage. Monitor them side-by-side in the Agent Command Center.

## Desktop Workflow Overview

Each lab follows a five-step workflow adapted for Desktop + Cloud:

1. **Create a Space** — Open Devin Desktop and create a Space for this workshop (do this once; reuse across labs)
2. **Explore locally** — Use Cascade or Devin Local to explore the codebase, understand the code, and refine requirements before delegating
3. **Delegate to Cloud** — Send the implementation task to Devin Cloud from Desktop. Cloud works autonomously on its own VM while you continue locally
4. **Monitor in the Agent Command Center** — Track cloud session progress on the Kanban board. See status, logs, and intermediate output without leaving Desktop
5. **Review with one-click checkout** — When Cloud opens a PR, check it out directly in the editor. Leave comments to iterate — Devin Cloud responds to feedback

## Table of Contents

- [Track A: COBOL Modernization](#track-a-cobol-modernization-mainframe--java)
  - [Lab A1 — System Understanding & Reverse Engineering](#lab-a1--system-understanding--reverse-engineering-60-min)
  - [Lab A2 — Migration Planning & Domain Decomposition](#lab-a2--migration-planning--domain-decomposition-60-min)
  - [Lab A3 — Migration Test Harness & Validation Strategy](#lab-a3--migration-test-harness--validation-strategy-60-min)
  - [Lab A4 — Code Migration: COBOL → Java](#lab-a4--code-migration-cobol--java-60-min)
- [Track B: Oracle Forms Modernization](#track-b-oracle-forms-modernization-formsplsql--spring-boot)
  - [Lab B1 — System Understanding & Reverse Engineering](#lab-b1--system-understanding--reverse-engineering-60-min)
  - [Lab B2 — Migration Planning & Strategy](#lab-b2--migration-planning--strategy-60-min)
  - [Lab B3 — Migration Test Harness & Validation](#lab-b3--migration-test-harness--validation-60-min)
  - [Lab B4 — Code Migration: Oracle Forms → Java](#lab-b4--code-migration-oracle-forms--java-60-min)
- [Duration Variants](#duration-variants)
- [Repos Required](#repos-required)
- [Key Takeaways](#key-takeaways)

---

## Track A: COBOL Modernization (Mainframe → Java)

**Key Modules:** [COBOL System Understanding](../../modules/migration-modernization/cobol-system-understanding.md), [COBOL Migration Planning](../../modules/migration-modernization/cobol-migration-planning.md), [Migration Test Harness](../../modules/migration-modernization/migration-test-harness.md), [COBOL to Java](../../modules/migration-modernization/cobol-to-java.md)

**Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java) — AWS CardDemo COBOL mainframe credit card management application

**Full event guide:** [COBOL Modernization Workshop](../../events/cobol-modernization-workshop/README.md)

### Lab A1 — System Understanding & Reverse Engineering (60 min)

- **Module:** [COBOL System Understanding](../../modules/migration-modernization/cobol-system-understanding.md)
- **Objective:** Produce a complete system inventory, data dictionary, dependency map, and hotspot report

#### Step 1: Explore locally with Cascade or Devin Local

Open your workshop Space in Devin Desktop. Use Cascade or Devin Local to explore the COBOL codebase interactively:

- *"What are the most complex COBOL programs in uc-legacy-modernization-cobol-to-java? Which ones have the most copybook dependencies?"*
- *"What business domains does the CardDemo application cover — account management, transaction processing, customer management, or reporting?"*

This local exploration helps you understand the codebase structure before delegating the full analysis to Cloud.

#### Step 2: Delegate to Devin Cloud

From Desktop, delegate the implementation task to Devin Cloud. The cloud agent will work autonomously on its own VM:

```
Analyze the entire COBOL codebase in
uc-legacy-modernization-cobol-to-java. Produce:
(1) `APPLICATION_INVENTORY.md` cataloging all 30+ programs,
copybooks, JCL jobs, and BMS maps with classifications,
(2) `DATA_DICTIONARY.md` extracting business entities from
copybook PIC clauses into a business-friendly format,
(3) `DEPENDENCY_MAP.md` with the call graph and data lineage
showing which programs call which and which jobs read/write
which files,
(4) `HOTSPOT_REPORT.md` with the top 10 modules prioritized
by complexity, risk, and business impact.
```

#### Step 3: Monitor in the Agent Command Center

Switch to the Agent Command Center in Desktop to watch your cloud session's progress on the Kanban board. You can see status updates, intermediate output, and logs without leaving the editor.

> **Tip:** While Cloud works on the analysis, continue exploring the codebase locally with Cascade — for example, try having it generate visual dependency graphs or reverse-engineer business rules from specific programs.

#### Step 4 (Optional): Review with one-click checkout

When Devin Cloud opens a PR, use one-click checkout to review the inventory directly in the editor:

- Review the inventory for completeness
- Leave PR comments to ask Devin to add Mermaid diagrams or expand hotspot analysis for specific programs

**Target Outcomes:** Application inventory, data dictionary, dependency map, hotspot report

---

### Lab A2 — Migration Planning & Domain Decomposition (60 min)

- **Module:** [COBOL Migration Planning](../../modules/migration-modernization/cobol-migration-planning.md)
- **Objective:** Produce a modernization blueprint with strategy options, domain decomposition, phased cutover plan, and risk register

#### Step 1: Explore locally with Cascade or Devin Local

Use Cascade or Devin Local to research migration strategies interactively:

- *"What are the tightest coupling points between subsystems? Would a strangler pattern or a big-bang rewrite be safer?"*
- *"What tribal knowledge risks exist in this codebase?"*

#### Step 2: Delegate to Devin Cloud

From Desktop, delegate the planning task to Devin Cloud:

```
Using the COBOL codebase in
uc-legacy-modernization-cobol-to-java, produce a
modernization blueprint. Create:
(1) `MODERNIZATION_BLUEPRINT.md` evaluating strangler,
replatform, refactor, and rewrite strategies for each
functional area,
(2) `DOMAIN_DECOMPOSITION.md` identifying bounded contexts
with extraction seam analysis,
(3) `CUTOVER_PLAN.md` with a phased migration sequence from
lowest-risk to highest-risk,
(4) `RISK_REGISTER.md` with top risks and mitigations.
```

#### Step 3: Monitor in the Agent Command Center

Track the cloud session on the Kanban board. Use the wait time to continue local exploration or read the repo's DeepWiki page to understand domain structure and coupling patterns.

#### Step 4 (Optional): Review with one-click checkout

When the PR arrives, check it out in the editor:

- Review strategy recommendations for trade-off reasoning
- Leave comments asking Devin to add bounded context diagrams or rollback triggers

**Target Outcomes:** Modernization blueprint, domain decomposition, cutover plan, risk register

---

### Lab A3 — Migration Test Harness & Validation Strategy (60 min)

- **Module:** [Migration Test Harness](../../modules/migration-modernization/migration-test-harness.md)
- **Objective:** Design and implement a test harness — golden files, differential testing, batch reconciliation, and contract tests

#### Step 1: Explore locally with Cascade or Devin Local

Use Cascade or Devin Local to research testing approaches:

- *"What are the most common data format differences between COBOL and Java — packed decimals, zoned decimals, EBCDIC?"*
- *"What reconciliation checks are most important for financial data migration?"*

#### Step 2: Delegate to Devin Cloud

From Desktop, delegate the test harness implementation to Devin Cloud:

```
Build a migration test harness for
uc-legacy-modernization-cobol-to-java. Create:
(1) `TEST_STRATEGY.md` covering four testing dimensions
(golden-file, differential, reconciliation, contract),
(2) Parse ASCII data files using copybook layouts and produce
structured JSON golden references in a `golden-files/`
directory,
(3) Build a `test-harness/` directory with parser utilities,
comparison functions, and reconciliation checks,
(4) `RECONCILIATION_CHECKS.md` with per-job validation
specifications.
```

#### Step 3: Monitor in the Agent Command Center

Track progress on the Kanban board. While waiting, explore the repo's DeepWiki page to understand data file layouts and batch processing patterns.

#### Step 4 (Optional): Review with one-click checkout

When the PR arrives, review the test harness in the editor:

- Review golden files for completeness
- Leave comments asking Devin to add packed decimal handling or batch reconciliation checks

**Target Outcomes:** Test strategy, golden files, test harness code, reconciliation checks

---

### Lab A4 — Code Migration: COBOL → Java (60 min)

- **Module:** [COBOL to Java](../../modules/migration-modernization/cobol-to-java.md)
- **Objective:** Translate selected COBOL programs to Java 17+ with parity tests validating against golden files

#### Step 1: Explore locally with Cascade or Devin Local

Use Cascade or Devin Local to understand specific programs before migrating:

- *"What are the most complex COBOL programs and what do they do?"*
- *"What would be the best Java architecture for migrating CBTRN01C.cbl?"*

#### Step 2: Delegate to Devin Cloud

From Desktop, delegate the migration task to Devin Cloud:

```
Analyze the COBOL program CBACT01C.cbl in
uc-legacy-modernization-cobol-to-java. Understand its business
logic, data structures (copybooks), and I/O operations. Rewrite
it as a Java 17+ application using modern idioms. Create JUnit
tests that verify the Java version produces identical results to
the COBOL version for sample inputs. Produce a
`MIGRATION_NOTES.md` documenting translation decisions.
```

> **Tip:** Try delegating multiple program migrations to separate Devin Cloud sessions in parallel. Monitor them side-by-side in the Agent Command Center — this mirrors how enterprise teams scale migration campaigns with child agents.

#### Step 3: Monitor in the Agent Command Center

Track the migration session on the Kanban board. Open the repo's DeepWiki page to explore additional programs you might want to migrate next.

#### Step 4 (Optional): Review with one-click checkout

When the PR arrives, review the Java code directly in the editor:

- Review the Java code for faithful COBOL logic representation
- Leave comments asking Devin to fix decimal handling or add data dictionary generation

**Target Outcomes:** Java source code, JUnit parity tests, migration notes

---

## Track B: Oracle Forms Modernization (Forms/PL/SQL → Spring Boot)

**Key Modules:** [Oracle Forms System Understanding](../../modules/migration-modernization/oracle-forms-system-understanding.md), [Oracle Forms Migration Planning](../../modules/migration-modernization/oracle-forms-migration-planning.md), [Migration Test Harness](../../modules/migration-modernization/migration-test-harness.md), [Oracle Forms to Java](../../modules/migration-modernization/oracle-forms-to-java.md)

**Repositories:**
- [ts-plsql-oracle-forms-hrms](https://github.com/Cognition-Partner-Workshops/ts-plsql-oracle-forms-hrms) — Oracle Forms 11g/12c HRMS legacy application (source system)
- [uc-legacy-modernization-oracle-forms-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-oracle-forms-to-java) — Migration artifacts, target Spring Boot structure, test harness

**Full event guide:** [Oracle Forms Modernization Workshop](../../events/oracle-forms-modernization-workshop/README.md)

### Lab B1 — System Understanding & Reverse Engineering (60 min)

- **Module:** [Oracle Forms System Understanding](../../modules/migration-modernization/oracle-forms-system-understanding.md)
- **Objective:** Produce a complete application inventory, data dictionary, multi-layer dependency map, and technical debt report for an Oracle Forms/PL/SQL application

#### Step 1: Explore locally with Cascade or Devin Local

Open your workshop Space in Devin Desktop. Use Cascade or Devin Local to explore the Oracle Forms codebase interactively:

- *"What circular dependencies exist in ts-plsql-oracle-forms-hrms and how do they affect compilation order?"*
- *"What security vulnerabilities exist in PKG_SECURITY and how would you fix them in a Java migration?"*

#### Step 2: Delegate to Devin Cloud

From Desktop, delegate the full analysis to Devin Cloud:

```
Analyze the entire Oracle Forms/PL/SQL estate in
ts-plsql-oracle-forms-hrms. Produce:
(1) `APPLICATION_INVENTORY.md` cataloging every Forms XML
export, PLL library, PL/SQL package, trigger, view, and
schema object with layer classifications,
(2) `DATA_DICTIONARY.md` extracting business entities from
DDL schemas grouped by domain,
(3) `DEPENDENCY_MAP.md` with a multi-layer call graph
(Forms -> PLL -> packages -> tables) and circular dependency
identification,
(4) `TECHNICAL_DEBT_REPORT.md` finding security
vulnerabilities, race conditions, performance anti-patterns,
and validation drift ranked by severity.
```

#### Step 3: Monitor in the Agent Command Center

Track the cloud session on the Kanban board. While waiting, explore the repo's DeepWiki page to see how Devin auto-generates architecture documentation for Oracle Forms — including PL/SQL package analysis and Forms XML parsing.

#### Step 4 (Optional): Review with one-click checkout

When the PR arrives, review the inventory in the editor:

- Review the inventory for completeness — did Devin find all Forms modules and their trigger logic?
- Leave comments asking Devin to add a section on PII fields or identify which batch jobs run daily vs. monthly

**Target Outcomes:** Application inventory, data dictionary, dependency map, technical debt report

---

### Lab B2 — Migration Planning & Strategy (60 min)

- **Module:** [Oracle Forms Migration Planning](../../modules/migration-modernization/oracle-forms-migration-planning.md)
- **Objective:** Produce a modernization blueprint with strategy evaluation, component mapping, migration ordering, and risk register

#### Step 1: Explore locally with Cascade or Devin Local

Use Cascade or Devin Local to research migration strategies:

- *"What are the biggest differences between Oracle Forms client-server architecture and a modern React+Spring Boot architecture?"*
- *"How should the circular dependency between PKG_EMPLOYEE and PKG_PAYROLL be resolved in the Java migration?"*

#### Step 2: Delegate to Devin Cloud

From Desktop, delegate the planning task to Devin Cloud:

```
Analyze the Oracle Forms/PL/SQL HRMS application in
ts-plsql-oracle-forms-hrms and produce a migration plan.
Create:
(1) `MIGRATION_STRATEGY.md` evaluating strangler fig,
big-bang rewrite, and re-platform (APEX) approaches for each
functional area,
(2) `COMPONENT_MAPPING.md` mapping every Forms element to
its Java/React equivalent,
(3) `MODULE_ORDERING.md` with a safe migration sequence
respecting package dependencies,
(4) `RISK_REGISTER.md` with top 10 Forms-specific migration
risks.
```

#### Step 3: Monitor in the Agent Command Center

Track progress on the Kanban board. Open both repos' DeepWiki pages to compare the legacy architecture with the target architecture in the migration use-case repo.

#### Step 4 (Optional): Review with one-click checkout

When the PR arrives, review the planning documents in the editor:

- Review the component mapping — are there Forms constructs that don't have clean modern equivalents?
- Leave comments asking Devin to add an ADR for a specific decision point

**Target Outcomes:** Migration strategy, component mapping, module ordering, risk register

---

### Lab B3 — Migration Test Harness & Validation (60 min)

- **Module:** [Migration Test Harness](../../modules/migration-modernization/migration-test-harness.md)
- **Repository:** [uc-legacy-modernization-oracle-forms-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-oracle-forms-to-java)
- **Objective:** Extend the test harness with additional business scenarios, comparison utilities, and reconciliation checks

#### Step 1: Explore locally with Cascade or Devin Local

Use Cascade or Devin Local to research testing approaches:

- *"What are the most common rounding differences between PL/SQL NUMBER and Java BigDecimal?"*
- *"What reconciliation checks are most important for payroll migration?"*

#### Step 2: Delegate to Devin Cloud

From Desktop, delegate the test harness extension to Devin Cloud:

```
Review the existing test harness in
uc-legacy-modernization-oracle-forms-to-java/test-harness/.
Extend it with:
(1) Additional business scenarios covering edge cases —
terminated employee payroll exclusion, overlapping leave
requests, overtime with holidays,
(2) Enhanced result comparator with financial tolerance rules
and wildcard matching,
(3) Per-module reconciliation checks — payroll totals must
balance, leave balances must sum correctly, employee counts
must match.
```

#### Step 3: Monitor in the Agent Command Center

Track progress on the Kanban board. While waiting, explore the repo's DeepWiki page to understand the test harness structure and scenario format.

#### Step 4 (Optional): Review with one-click checkout

When the PR arrives, review the test harness in the editor:

- Review scenarios for edge case coverage
- Leave comments asking Devin to add a stress test scenario or performance benchmark

**Target Outcomes:** Extended business scenarios, enhanced comparators, reconciliation specifications

---

### Lab B4 — Code Migration: Oracle Forms → Java (60 min)

- **Module:** [Oracle Forms to Java](../../modules/migration-modernization/oracle-forms-to-java.md)
- **Objective:** Translate selected PL/SQL packages to Java Spring Boot with REST APIs, JPA entities, unified validation, and parity tests

#### Step 1: Explore locally with Cascade or Devin Local

Use Cascade or Devin Local to understand the specific packages before migrating:

- *"What are the most complex procedures in PKG_EMPLOYEE and what business rules do they implement?"*
- *"How does the HRMS_EMPLOYEE form interact with PKG_EMPLOYEE — what triggers fire and in what order?"*

#### Step 2: Delegate to Devin Cloud

From Desktop, delegate the migration task to Devin Cloud:

```
Analyze PKG_EMPLOYEE in ts-plsql-oracle-forms-hrms (spec +
body). Also review the HRMS_EMPLOYEE Forms XML and
HRMS_VALIDATION_LIB PLL. Migrate the employee management
functionality to Java Spring Boot:
(1) EmployeeService with all CRUD operations, search, org
chart, transfer,
(2) JPA entities for EMPLOYEES and related tables,
(3) REST API endpoints,
(4) Unified validation from PLL and package layers,
(5) Fix SQL injection, race condition, and circular
dependency,
(6) Unit tests for parity,
(7) MIGRATION_NOTES.md.
Use the project in
uc-legacy-modernization-oracle-forms-to-java/java-target/.
```

> **Tip:** Try delegating multiple package migrations to separate Devin Cloud sessions in parallel. Each session can target a different PL/SQL package — monitor them side-by-side in the Agent Command Center. This mirrors how enterprise teams use child agents to parallelize migration campaigns.

#### Step 3: Monitor in the Agent Command Center

Track the migration session on the Kanban board. Open both repos' DeepWiki pages to understand the legacy architecture and target project structure.

#### Step 4 (Optional): Review with one-click checkout

When the PR arrives, review the Java code directly in the editor:

- Review the Java code for faithful PL/SQL logic representation
- Leave comments asking Devin to handle a specific technical debt item differently

**Target Outcomes:** Java Spring Boot service, JPA entities, REST API, unified validation, parity tests, migration notes

---

## Choosing a Track

| Audience Legacy Platform | Recommended Track | Event Guide |
|---|---|---|
| COBOL / Mainframe (CICS, JCL, VSAM, DB2) | Track A: COBOL Modernization | [COBOL Modernization Workshop](../../events/cobol-modernization-workshop/README.md) |
| Oracle Forms / PL/SQL (Forms Builder, Oracle DB) | Track B: Oracle Forms Modernization | [Oracle Forms Modernization Workshop](../../events/oracle-forms-modernization-workshop/README.md) |
| Mixed audience (both platforms represented) | Run both tracks in parallel — Labs 1 & 2 can share a room, split for Labs 3 & 4 | — |
| General modernization (not platform-specific) | Start with Track A (COBOL) — more dramatic "language barrier" story | — |

## Duration Variants

| Duration | Recommended Format |
|----------|-------------------|
| 8 hours (full day) | Both tracks: Track A (morning) + Track B (afternoon) or parallel tracks |
| 4+ hours | Single track: Lab 1 → Lab 2 → Lab 3 → Lab 4 |
| 3 hours | Single track: Labs 1, 3, 4 (skip planning, focus on understand → test → migrate) |
| 2 hours | Single track: Abbreviated Lab 1 (30 min) + Lab 4 (60 min) + discussion |
| 1 hour | Lab 4 only (code migration) or walkthrough with pre-built artifacts |

## Repos Required

### Track A (COBOL)

- [ ] uc-legacy-modernization-cobol-to-java

### Track B (Oracle Forms)

- [ ] ts-plsql-oracle-forms-hrms
- [ ] uc-legacy-modernization-oracle-forms-to-java

### Optional (for extended challenges)

- [ ] uc-spring-boot-upgrade-microservice-extraction
- [ ] uc-data-source-migration-jdbc-normalization

## Key Takeaways

- **"Time-to-understanding drops from months to hours"** — dependency maps and data dictionaries produced in one session, regardless of the legacy platform
- **"Test before you migrate"** — building the safety net first is the single most important risk mitigation
- **"Informed migration, not blind translation"** — by Lab 4, participants have complete understanding, a strategy, and a test harness
- **"Technical debt resolved, not preserved"** — Devin fixes security vulnerabilities and performance issues during migration rather than translating bugs to the new platform
- **"Same methodology, different platforms"** — the Understand → Plan → Safeguard → Execute sequence works for COBOL, Oracle Forms, and any other legacy platform
- **"Local exploration, cloud execution"** — Desktop lets you refine requirements locally before delegating to Cloud, producing higher-quality results with faster iteration
