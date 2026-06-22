# Oracle Forms to Java

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Going Further](#going-further)
- [Notes](#notes)
- [ts-plsql-oracle-forms-hrms](#ts-plsql-oracle-forms-hrms)
- [uc-legacy-modernization-oracle-forms-to-java](#uc-legacy-modernization-oracle-forms-to-java)

---

## Quick Start

Paste this prompt into Devin to try migrating Oracle Forms PL/SQL to Java:

```
Migrate the Employee Management module from
ts-plsql-oracle-forms-hrms to Java. Analyze
plsql/packages/PKG_EMPLOYEE.pks and PKG_EMPLOYEE.pkb to
understand the business logic. Use the scaffolded project in
uc-legacy-modernization-oracle-forms-to-java/java-target/ as
your starting point.

Create:
1. Spring Boot REST controllers matching the PL/SQL
   package procedures
2. JPA entities matching the schema in schema/tables/
3. Service classes that preserve the business rules from
   PKG_EMPLOYEE
4. Unit tests verifying business logic parity
5. A MIGRATION_NOTES.md documenting translation decisions
```

---

## Repositories

- [ts-plsql-oracle-forms-hrms](#ts-plsql-oracle-forms-hrms) — source PL/SQL estate
- [uc-legacy-modernization-oracle-forms-to-java](#uc-legacy-modernization-oracle-forms-to-java) — scaffolded Java target project

---

## Challenge

Migrate an Oracle Forms PL/SQL module to a modern Java (Spring Boot) application. Starting from the PL/SQL packages and database schema in the source repo, use Devin to generate a clean Spring Boot project with JPA entities, REST APIs matching the PL/SQL procedures, service classes that faithfully translate the business logic, and unit tests that verify parity.

This is the execution phase of Oracle Forms modernization. Devin reads the PL/SQL packages, understands the business rules (validations, calculations, state transitions), and translates them to idiomatic Java — preserving behavior while modernizing the architecture.

## Target Outcomes

- Spring Boot REST controllers with endpoints matching PL/SQL package procedures
- JPA entities with proper relationships and constraints from DDL schemas
- Service classes that faithfully translate PL/SQL business logic to Java
- Unit tests verifying business logic parity with PL/SQL originals
- `MIGRATION_NOTES.md` documenting each translation decision (PL/SQL cursor → JPA query, PL/SQL exception → Java exception, PL/SQL record type → Java DTO, etc.)
- PR with the migration code, tests, and documentation

## What Participants Will Learn

- How Devin translates PL/SQL business logic (procedures, functions, cursors, exceptions) to Java equivalents
- How PL/SQL data access patterns map to JPA/Hibernate (cursors → repositories, DML → JPA methods)
- How PL/SQL validation logic maps to Java Bean Validation or service-layer checks
- The value of migration notes for audit trail and reviewability
- How different PL/SQL packages represent different levels of migration difficulty

## Devin Features Exercised

- Cross-language code comprehension (PL/SQL → Java)
- Code generation with architectural decisions
- Test generation for migration parity
- PR creation with code + documentation
- AskDevin for pre-session planning
- DeepWiki for understanding package dependencies
- Child sessions for parallel migration of multiple packages

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

## Going Further

- **Divide-and-conquer with child sessions**: Each PL/SQL package is an independent migration unit. A parent session can analyze the estate, then spawn one child session per package (PKG_EMPLOYEE, PKG_PAYROLL, PKG_LEAVE, PKG_PERFORMANCE, PKG_SECURITY). Each child follows the same migration playbook and opens its own PR.
- **Playbook-driven migration**: Encode the migration methodology (parse PL/SQL package spec → create JPA entities → build service layer → generate REST endpoints → write parity tests → document decisions) as a playbook. Every child session follows the same steps.
- **Scheduled regression testing**: After initial migration, configure a scheduled session that runs the parity tests weekly to catch regressions.
- **Event-driven migration**: Connect a webhook so that when a PL/SQL package is modified in the source repo, Devin automatically updates the corresponding Java code and re-runs parity tests.
- **Team-based review**: Multiple engineers can review migration PRs simultaneously. PR comments trigger Devin to iterate on specific translation decisions.

## Notes

- The scaffolded project in `uc-legacy-modernization-oracle-forms-to-java/java-target/` contains `pom.xml`, `docker-compose.yml`, and a basic Spring Boot structure — Devin should build on this scaffold
- The `uc-legacy-modernization-oracle-forms-to-java/migration-plan/` directory contains pre-built planning artifacts (`assessment-report.md`, `component-mapping.md`, `decision-log.md`) that can guide the migration
- Participants can choose which PL/SQL package to migrate — PKG_EMPLOYEE is the recommended starting point (moderate complexity)
- PKG_SECURITY contains intentional vulnerabilities (MD5, SQL injection) — the Java version should fix these while preserving functionality
- This module builds on [Oracle Forms System Understanding](oracle-forms-system-understanding.md) and [Oracle Forms Migration Planning](oracle-forms-migration-planning.md) — participants who completed those modules will have richer context

---

## <a id="ts-plsql-oracle-forms-hrms"></a>ts-plsql-oracle-forms-hrms

**Repository:** [ts-plsql-oracle-forms-hrms](https://github.com/Cognition-Partner-Workshops/ts-plsql-oracle-forms-hrms)

Source Oracle Forms estate with PL/SQL packages containing the business logic to migrate. Key packages: `PKG_EMPLOYEE` (employee CRUD + validations), `PKG_PAYROLL` (salary calculations), `PKG_LEAVE` (leave balance management), `PKG_PERFORMANCE` (review workflows), `PKG_SECURITY` (authentication + authorization — contains intentional vulnerabilities).

---

## <a id="uc-legacy-modernization-oracle-forms-to-java"></a>uc-legacy-modernization-oracle-forms-to-java

**Repository:** [uc-legacy-modernization-oracle-forms-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-oracle-forms-to-java)

Scaffolded Spring Boot project for the Java migration target. Contains:
- `java-target/` — Spring Boot project structure with `pom.xml`, `docker-compose.yml`, and basic application setup
- `migration-plan/` — pre-built planning artifacts (`assessment-report.md`, `component-mapping.md`, `decision-log.md`)

Each participant creates a `workshop-<attendee_id>` branch from `main` and pushes their work there.

### Step 1: Paste into Devin — PL/SQL to Java Migration

```
Migrate the Employee Management module from
ts-plsql-oracle-forms-hrms to Java. Analyze
plsql/packages/PKG_EMPLOYEE.pks and PKG_EMPLOYEE.pkb to
understand the business logic, validations, and data access
patterns.

Use the scaffolded project in
uc-legacy-modernization-oracle-forms-to-java/java-target/ as
your starting point. Create:

1. Spring Boot REST controllers matching the PL/SQL package
   procedures (hire, terminate, transfer, promote, etc.)
2. JPA entities matching the schema in schema/tables/
   (employees, departments, job_history, etc.) with proper
   relationships and constraints
3. Service classes that preserve the business rules from
   PKG_EMPLOYEE (validation logic, status transitions,
   department transfer rules)
4. Unit tests verifying business logic parity — the Java
   version should produce identical results to the PL/SQL
   for the same inputs
5. A MIGRATION_NOTES.md documenting each translation
   decision: PL/SQL cursor → JPA query, PL/SQL exception →
   Java exception, PL/SQL record type → Java DTO, etc.
```

### Step 2: Research with Ask Devin

- *"What business rules does PKG_EMPLOYEE enforce? What validations need to be preserved in the Java version?"*
- *"How should PL/SQL cursors in PKG_EMPLOYEE be translated to JPA — should I use Spring Data repositories, JPQL queries, or native SQL?"*
- *"What are the differences between PKG_EMPLOYEE.pks (spec) and PKG_EMPLOYEE.pkb (body)? What private procedures exist?"*
- Use the refined understanding to adjust the migration approach

### Step 3 (Optional): Read the DeepWiki

Open each repo's DeepWiki page to understand:
1. **ts-plsql-oracle-forms-hrms** — PL/SQL package dependencies, Forms-to-package call patterns, and the data model
2. **uc-legacy-modernization-oracle-forms-to-java** — the scaffolded project structure and pre-built migration plan

### Step 4 (Optional): Review & Give Feedback

- **Review the Java code** — does the service layer faithfully translate the PL/SQL business rules? Are there missed validations?
- **Review MIGRATION_NOTES.md** — does it document each translation decision clearly?
- **Leave a comment** asking Devin to fix the security vulnerabilities from PKG_SECURITY when migrating (e.g., *"Replace MD5 with bcrypt, use parameterized queries instead of string concatenation"*)
- **Leave a comment** asking Devin to add integration tests using Testcontainers for the database layer

### Key Takeaways

- **Cross-language translation**: Devin reads PL/SQL package specs and bodies, understanding procedures, cursors, exceptions, and record types, and translates them to idiomatic Spring Boot Java
- **Business rule preservation**: Migration parity tests verify that the Java version enforces the same validations and produces the same results as the PL/SQL original
- **Parallelizable migration**: Each PL/SQL package is an independent migration unit — child sessions can migrate multiple packages simultaneously
- **Audit trail**: Migration notes document every translation decision, creating a reviewable record of how PL/SQL constructs became Java code
