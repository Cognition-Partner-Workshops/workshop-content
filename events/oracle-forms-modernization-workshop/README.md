# Oracle Forms Modernization Workshop

## Event Details

| | |
|---|---|
| **Variant Name** | Oracle Forms Modernization Workshop |
| **Focus** | End-to-end Oracle Forms/PL/SQL modernization — from system understanding through Java migration execution |
| **Duration** | ~4 hours (4 labs × 60 min + breaks) |
| **Facilitator** | *(facilitator name and email)* |
| **Event Site** | https://partner-workshops.devinenterprise.com |
| **Audience** | Enterprise architects, Oracle Forms modernization teams, application portfolio managers, migration program leads |

## Table of Contents

- [Abstract](#abstract)
- [Structure](#structure)
- [Featured Labs](#featured-labs)
  - [Lab 1 — System Understanding & Reverse Engineering (60 min)](#lab-1-system-understanding-reverse-engineering-60-min)
  - [Lab 2 — Migration Planning & Strategy (60 min)](#lab-2-migration-planning-strategy-60-min)
  - [Lab 3 — Migration Test Harness & Validation (60 min)](#lab-3-migration-test-harness-validation-60-min)
  - [Lab 4 — Oracle Forms to Java Code Migration (60 min)](#lab-4-oracle-forms-to-java-code-migration-60-min)
- [Accelerated Variant](#accelerated-variant)
- [Additional Challenges](#additional-challenges)
- [Repos Required on Devin's Machine](#repos-required-on-devins-machine)
  - [Optional (for extended challenges)](#optional-for-extended-challenges)
- [Runtime Resources](#runtime-resources)
- [Context](#context)
  - [Suitability Notes](#suitability-notes)
  - [Key Constraints](#key-constraints)
- [Devin Features Checklist](#devin-features-checklist)
- [Post-Event](#post-event)

## Abstract

> This track walks through the complete Oracle Forms modernization lifecycle in four progressive labs using a realistic Oracle Forms 11g/12c HR Management System:
>
> **Lab 1 — System Understanding:** Devin reverse-engineers the Oracle Forms estate — builds an application inventory spanning Forms modules, PLL libraries, PL/SQL packages, and database objects. It extracts a data dictionary, maps the multi-layer dependency graph (Forms → PLL → packages → tables), and produces a technical debt report identifying security vulnerabilities, race conditions, and performance anti-patterns.
>
> **Lab 2 — Migration Planning:** Devin produces a modernization blueprint evaluating strangler fig, big-bang rewrite, and re-platform (APEX) strategies for each functional area. It creates a detailed component mapping (Forms → React, PL/SQL → Spring Boot, triggers → Spring events), defines a safe migration ordering that respects package dependencies, and builds a risk register covering Forms-specific migration hazards.
>
> **Lab 3 — Migration Safety:** Devin builds the test harness that makes migration safe — business scenario definitions capturing current behavior (employee CRUD, payroll calculation, leave workflows, authentication), a result comparison framework for legacy-vs-modern equivalence testing, and reconciliation checks. All before a single line of Java is written.
>
> **Lab 4 — Code Migration:** Devin translates selected PL/SQL packages to Java Spring Boot services with JPA entities, REST APIs, unified validation (fixing the client/server drift), and parity tests. The test harness from Lab 3 validates that the Java implementation produces identical results.
>
> **Who should attend:** Enterprise architects evaluating Oracle Forms modernization strategies, Forms/PL/SQL teams planning migration programs, application portfolio managers prioritizing legacy estates, and anyone responsible for de-risking large-scale Oracle-to-Java migration.

---

## Structure

Four labs that build on each other in a progressive arc:

1. **Lab 1 — System Understanding & Reverse Engineering (60 min):** Discover what the system does
2. **Break (15 min)**
3. **Lab 2 — Migration Planning & Strategy (60 min):** Decide how to modernize it
4. **Break (15 min)**
5. **Lab 3 — Migration Test Harness & Validation (60 min):** Build the safety net
6. **Break (15 min)**
7. **Lab 4 — Oracle Forms to Java Code Migration (60 min):** Execute the migration

**Progression arc:**
- Lab 1: **Understand** — What does this system do? What are the forms, packages, dependencies, and technical debt?
- Lab 2: **Plan** — How should we modernize it? What are the strategies, component mappings, and risks?
- Lab 3: **Safeguard** — How do we know the migration is correct? What tests catch regressions?
- Lab 4: **Execute** — Translate PL/SQL to Java Spring Boot with the test harness validating correctness

**Key narrative:** Each lab produces artifacts that feed the next. The inventory (Lab 1) informs the blueprint (Lab 2). The blueprint prioritizes which modules to test first (Lab 3). The test harness validates the migration (Lab 4). This mirrors how a real enterprise migration program operates.

---

## Featured Labs

### Lab 1 — System Understanding & Reverse Engineering (60 min)

- **Module:** [Oracle Forms System Understanding & Reverse Engineering](../../modules/migration-modernization/oracle-forms-system-understanding.md)
- **Repository:** [ts-plsql-oracle-forms-hrms](https://github.com/Cognition-Partner-Workshops/ts-plsql-oracle-forms-hrms)
- **Objective:** Produce a complete application inventory, data dictionary, multi-layer dependency map, and technical debt report for an Oracle Forms/PL/SQL application

#### What to Try

1. **Start with DeepWiki:** Open the repo's DeepWiki page and explore how Devin auto-generates architecture documentation for Oracle Forms — including PL/SQL package analysis and Forms XML parsing
2. **Run the inventory:** Have Devin catalog all Forms modules, PLL libraries, PL/SQL packages, triggers, views, and schema objects with layer classifications
3. **Extract the data dictionary:** Try Devin parsing DDL schemas and PL/SQL package interfaces into a business-friendly data dictionary
4. **Map dependencies:** Explore the multi-layer call graph — Forms → PLL libraries → PL/SQL packages → database objects. Identify the circular dependency between PKG_EMPLOYEE and PKG_PAYROLL
5. **Identify technical debt:** Review the prioritized list of security vulnerabilities, race conditions, and performance anti-patterns

#### Key Takeaways

- **"Multi-layer discovery in one session"** — Forms UI, PLL shared libraries, PL/SQL business logic, and database schema analyzed together, not in silos
- **"Technical debt made visible"** — SQL injection, MD5 hashing, race conditions, and validation drift discovered automatically
- **"Queryable knowledge surface"** — DeepWiki + the generated artifacts let anyone ask questions about the legacy system without knowing PL/SQL or Oracle Forms
- **"Parallelization"** — Devin can analyze multiple packages concurrently and produce consistent artifacts

#### Target Outcomes

- `APPLICATION_INVENTORY.md` with all Forms modules, PLL libraries, PL/SQL packages, triggers, views, sequences classified
- `DATA_DICTIONARY.md` with business entities extracted from DDL schemas
- `DEPENDENCY_MAP.md` with multi-layer call graph and circular dependency identification
- `TECHNICAL_DEBT_REPORT.md` with security, performance, and maintainability issues ranked
- PR with all artifacts

---

### Lab 2 — Migration Planning & Strategy (60 min)

- **Module:** [Oracle Forms Migration Planning & Strategy](../../modules/migration-modernization/oracle-forms-migration-planning.md)
- **Repositories:** [ts-plsql-oracle-forms-hrms](https://github.com/Cognition-Partner-Workshops/ts-plsql-oracle-forms-hrms), [uc-legacy-modernization-oracle-forms-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-oracle-forms-to-java)
- **Objective:** Produce a modernization blueprint with strategy evaluation, component mapping, migration ordering, and risk register

#### What to Try

1. **Set the context:** Reference the inventory and dependency map from Lab 1 (or have Devin build context from the codebase directly)
2. **Strategy analysis:** Have Devin evaluate strangler fig, big-bang rewrite, and re-platform (APEX) options for each functional area — with trade-off reasoning, not just a blanket recommendation
3. **Component mapping:** Have Devin map every Forms construct to its modern equivalent — triggers to Spring events, LOVs to API-backed autocomplete, tab canvases to React components, PLL functions to Java utilities
4. **Migration ordering:** Review the safe migration sequence — which modules can go first and which have hard dependencies?
5. **Risk register:** Walk through the top risks — validation drift, Forms UX patterns that don't translate cleanly, batch job migration, dual-running data integrity

#### Key Takeaways

- **"Options, not just 'rewrite it'"** — Devin evaluates multiple strategies per functional area; security might use a different approach than payroll
- **"Forms-specific mapping depth"** — triggers, LOVs, canvases, PLL libraries, and menu security all have specific modern equivalents that Devin identifies
- **"Dependency-aware ordering"** — the migration sequence respects the circular dependency between PKG_EMPLOYEE and PKG_PAYROLL
- **"Risk-aware planning"** — the risk register surfaces Forms-specific concerns (validation drift, LOV UX loss, DBMS_SCHEDULER migration) that generic migration plans miss

#### Target Outcomes

- `MIGRATION_STRATEGY.md` with per-area strategy recommendations
- `COMPONENT_MAPPING.md` with Forms → React/Spring Boot mapping
- `MODULE_ORDERING.md` with safe migration sequence
- `RISK_REGISTER.md` with top risks and mitigations
- PR with all planning artifacts

---

### Lab 3 — Migration Test Harness & Validation (60 min)

- **Module:** [Migration Test Harness & Validation Strategy](../../modules/migration-modernization/migration-test-harness.md)
- **Repository:** [uc-legacy-modernization-oracle-forms-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-oracle-forms-to-java)
- **Objective:** Design and implement a test harness that validates migration correctness — business scenarios, differential testing, result comparison, and reconciliation checks

#### What to Try

1. **Review existing scenarios:** Examine the business scenarios in `test-harness/scenarios/` (employee CRUD, payroll calculation, leave workflow, security auth) — understand the YAML format
2. **Add new scenarios:** Have Devin write additional test scenarios covering edge cases: terminated employee payroll exclusion, overlapping leave requests, overtime calculation with holidays, SSN encryption/decryption
3. **Build comparators:** Inspect and extend the `test-harness/comparators/result-comparator.py` — add tolerance rules for financial calculations, wildcard matching for auto-generated IDs
4. **Reconciliation checks:** Have Devin define per-module reconciliation checks: payroll totals must balance, leave balances must sum correctly after accrual, employee counts must match between old and new
5. **Connect to Lab 4:** Explain how this harness will validate the Java migration in the next lab

#### Key Takeaways

- **"Test before you migrate"** — building the safety net first is the single most important risk mitigation in legacy modernization
- **"Business scenarios capture behavior without understanding every rule"** — you don't need to reverse-engineer every PL/SQL business rule to verify that the new system produces the same output
- **"Differential testing enables safe iteration"** — run old and new side-by-side, diff the outputs, fix mismatches
- **"Financial reconciliation provides business-level confidence"** — not just "tests pass" but "payroll totals balance, leave days add up, employee counts match"

#### Target Outcomes

- Extended business scenario definitions covering edge cases
- Comparison utilities with financial tolerance rules
- Reconciliation check specifications per module
- Test strategy document covering all testing dimensions
- PR with test harness extensions

---

### Lab 4 — Oracle Forms to Java Code Migration (60 min)

- **Module:** [Oracle Forms to Java](../../modules/migration-modernization/oracle-forms-to-java.md)
- **Repositories:** [ts-plsql-oracle-forms-hrms](https://github.com/Cognition-Partner-Workshops/ts-plsql-oracle-forms-hrms), [uc-legacy-modernization-oracle-forms-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-oracle-forms-to-java)
- **Objective:** Translate selected PL/SQL packages to Java Spring Boot with REST APIs, JPA entities, unified validation, and parity tests

#### What to Try

1. **Select a target:** Use the technical debt report from Lab 1 and the blueprint from Lab 2 to choose which PL/SQL package to migrate first
2. **Translate:** Have Devin rewrite the PL/SQL package as a Java Spring Boot service with JPA entities, REST controllers, and Bean Validation
3. **Fix technical debt:** Ensure Devin resolves the known issues during migration — MD5 → BCrypt, SQL injection → parameterized queries, cursor loops → bulk operations
4. **Validate:** Run the test scenarios from Lab 3's harness against the Java service
5. **Trace the full loop:** Inventory (Lab 1) → Blueprint (Lab 2) → Test Harness (Lab 3) → Migration (Lab 4) → Validation (Lab 3 harness)

#### Key Takeaways

- **"Informed migration, not blind translation"** — by Lab 4, participants have a complete understanding of the system, a strategy, and a test harness before writing a single line of Java
- **"Technical debt resolved, not preserved"** — Devin doesn't just translate bugs to Java; it fixes security vulnerabilities and performance issues during migration
- **"Validation unified"** — the drifted client-side (PLL) and server-side (package) validation is merged into a single source of truth (Bean Validation)
- **"Composable labs"** — each lab's output feeds the next, just like a real migration program

#### Target Outcomes

- Java Spring Boot service code replacing selected PL/SQL package(s)
- JPA entities mapped to the existing schema
- REST API endpoints with OpenAPI documentation
- Unified validation logic (fixing client/server drift)
- Unit tests verifying functional equivalence
- Migration documentation covering translation decisions
- PR with Java code, tests, and migration notes

---

## Accelerated Variant

For shorter workshops, adjust the lab selection:

| Duration | Recommended Format |
|----------|-------------------|
| 4+ hours | Full arc: Lab 1 → Lab 2 → Lab 3 → Lab 4 |
| 3 hours | Labs 1, 3, 4 (skip planning, focus on understand → test → migrate) |
| 2 hours | Abbreviated Lab 1 (30 min) + Lab 4 (60 min) + discussion |
| 1 hour | Walkthrough only: show pre-built artifacts from each lab phase |

---

## Additional Challenges

Participants who finish early or want to go deeper may attempt:

| Challenge | Module | Repo | Difficulty | Time |
|-----------|--------|------|-----------|------|
| COBOL Modernization (parallel track) | [COBOL to Java](../../modules/migration-modernization/cobol-to-java.md) | uc-legacy-modernization-cobol-to-java | Intermediate–Advanced | 60 min |
| Combined Modernization | [Legacy Modernization Combined](../../modules/migration-modernization/legacy-modernization-combined.md) | Multiple repos | Advanced | 60 min |
| Framework Upgrade (Java 11 → 17) | [Framework Upgrade](../../modules/migration-modernization/framework-upgrade.md) | uc-spring-boot-upgrade-microservice-extraction | Intermediate | 60 min |
| Data Source Migration | [Data Source Migration](../../modules/data-engineering/data-source-migration.md) | uc-data-source-migration-jdbc-normalization | Intermediate | 60 min |

---

## Repos Required on Devin's Machine

- [ ] ts-plsql-oracle-forms-hrms
- [ ] uc-legacy-modernization-oracle-forms-to-java

### Optional (for extended challenges)

- [ ] uc-legacy-modernization-cobol-to-java
- [ ] uc-spring-boot-upgrade-microservice-extraction
- [ ] uc-data-source-migration-jdbc-normalization

## Runtime Resources

- No external runtime needed — Oracle Forms and PL/SQL analysis is static (no Oracle database required)
- Java SDK 17+ for Lab 4 (code migration target)
- Maven 3.8+ for building the Spring Boot project
- Python 3.10+ for test harness comparators (Lab 3)

## Context

- **Audience:** Enterprise modernization teams with Oracle Forms estates
- **Narrative arc:** Understand (Lab 1) → Plan (Lab 2) → Safeguard (Lab 3) → Execute (Lab 4)
- **Enterprise value props shown hands-on:**
  - Lab 1: Devin comprehends multi-layer Oracle Forms architecture (UI + PLL + PL/SQL + DB) in one session — dependency maps, data dictionaries, and technical debt reports without SME interviews
  - Lab 2: Devin produces decision-ready modernization blueprints with Forms-specific component mappings, not generic migration templates
  - Lab 3: Devin builds the migration safety net (business scenarios, reconciliation checks) before any code changes — reducing migration risk
  - Lab 4: Devin bridges the Oracle Forms-to-Java barrier, unifying scattered validation logic and resolving technical debt during translation
- **Suitability:** Medium → High depending on audience familiarity with Oracle Forms

### Suitability Notes

| Scenario | Rating | Why |
|----------|--------|-----|
| Full toolchain access (Oracle DB, Forms Builder) | **High** | Devin can verify outputs against live system and iterate |
| Code access only (no Oracle runtime) | **Medium-High** | Devin reverse-engineers, documents, plans, and generates migration code + tests; humans validate outputs |
| Code not accessible (manual/GUI only) | **Low** | Devin cannot analyze what it cannot access |

### Key Constraints

- **Multi-layer complexity** — business logic spans Forms triggers, PLL libraries, PL/SQL packages, and database triggers; analysis must cross all layers
- **No Oracle Forms runtime** — Forms cannot be compiled or run in Devin's Linux VM; analysis is static based on XML exports and PL/SQL source
- **Validation drift** — client-side (PLL) and server-side (package) validation have diverged; migration must choose which rules to keep

## Related Themes

The following [general themes](../../shared/general-themes/) reinforce the narratives in this workshop:

- [Architecture Strengths](../../shared/general-themes/architecture-strengths.md) — clean-room execution and context retrieval are central to how Devin analyzes multi-layer Oracle Forms codebases
- [Design Patterns](../../shared/general-themes/design-patterns-for-devin.md) — the divide-and-conquer pattern applies directly to analyzing PL/SQL packages independently before cross-referencing
- [Collaboration Model](../../shared/general-themes/collaboration-model.md) — the PR feedback loop is exercised in Labs 3 and 4
- [Value Narratives](../../shared/general-themes/value-narratives.md) — modernization risk reduction and velocity framing apply to the full lab arc

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../modules/devin-features/README.md) throughout the session. Key activities for this track:

- [ ] Use DeepWiki to explore an Oracle Forms/PL/SQL codebase (Lab 1)
- [ ] Use AskDevin to ask targeted questions about PL/SQL packages and Forms triggers (Labs 1, 2)
- [ ] Have Devin produce structured analysis artifacts (Labs 1, 2)
- [ ] Have Devin generate test harness code from business scenarios (Lab 3)
- [ ] Run parallel Devin sessions (migrate multiple PL/SQL packages in Lab 4)
- [ ] Review a Devin PR via GitHub (after any lab)
- [ ] Leave PR comments and watch Devin iterate (Labs 3, 4)
- [ ] Create Knowledge from a session (after Lab 1 analysis)
- [ ] Compare results across participants (all labs)

## Post-Event

- [ ] Collect participant feedback — especially: which lab phase was most valuable for their modernization program?
- [ ] Archive any event-specific branches
- [ ] Update challenge modules if issues were discovered
- [ ] Share session recordings/artifacts with participants
