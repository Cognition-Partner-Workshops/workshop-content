# Oracle Forms System Understanding & Reverse Engineering

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

---

## Quick Start

Paste this prompt into Devin to try estate discovery on the Oracle Forms HRMS application:

```
Analyze the entire Oracle Forms/PL/SQL estate in
ts-plsql-oracle-forms-hrms. Produce the following artifacts:

1. APPLICATION_INVENTORY.md — catalog every Forms XML export
   in forms/xml-exports/, PLL library in forms/libraries/,
   menu module in forms/menus/, PL/SQL package in
   plsql/packages/, trigger in plsql/triggers/, and schema
   object in schema/. For each, list: filename, purpose
   (inferred from code), layer (UI/business logic/data
   access/integration/utility), and key dependencies.
2. DATA_DICTIONARY.md — for every table defined in
   schema/tables/, extract: table name, columns with data
   types and constraints, business meaning, relationships
   (foreign keys), and audit columns. Group by business
   domain (employee, payroll, leave, performance, security,
   system).
3. DEPENDENCY_MAP.md — build a multi-layer dependency graph:
   Forms modules → PLL libraries → PL/SQL packages →
   database tables. Identify circular dependencies, shared
   utilities, and the batch job scheduling topology
   (DBMS_SCHEDULER references).
4. TECHNICAL_DEBT_REPORT.md — find all intentional bugs and
   anti-patterns: security vulnerabilities (look for MD5,
   SQL injection, hard-coded keys), race conditions (look
   for missing SELECT FOR UPDATE), performance issues
   (cursor loops, CONNECT BY), validation drift (compare
   PLL vs package validation), and circular dependencies.
   Rank by severity and recommend migration priority.
```

---

## Repositories

- [ts-plsql-oracle-forms-hrms](#ts-plsql-oracle-forms-hrms)

---

## Challenge

Use Devin to reverse-engineer an Oracle Forms 11g/12c HR Management System and produce concrete system-understanding artifacts: an application inventory, a data dictionary extracted from PL/SQL packages and schema DDL, a dependency and data-flow model mapping Forms → PL/SQL → database objects, and a technical debt report identifying the highest-risk components. This is the discovery phase that normally takes weeks of SME interviews and manual code reading — Devin can compress it into a single session.

In most Oracle Forms modernization programs, **understanding the existing system is the biggest blocker**. Business logic is split across Forms triggers, PLL libraries, and PL/SQL packages with no clear boundaries. Client-side validation in PLL libraries has drifted from server-side validation in packages. Circular dependencies between packages create compilation nightmares. This module shows how Devin turns an opaque Oracle Forms estate into a documented, queryable knowledge surface.

## Target Outcomes

- `APPLICATION_INVENTORY.md` — complete catalog of Forms modules (.fmb XML exports), PLL libraries, menu modules, PL/SQL packages (specs + bodies), triggers, views, sequences, and seed data with classification (UI, business logic, data access, integration, utility)
- `DATA_DICTIONARY.md` — business entities, fields, data types, constraints, and relationships extracted from DDL schemas and PL/SQL package interfaces
- `DEPENDENCY_MAP.md` — call graph (Forms → PLL → PL/SQL packages → database objects), circular dependency identification, batch job scheduling map, and integration data flows
- `TECHNICAL_DEBT_REPORT.md` — security vulnerabilities (MD5 hashing, SQL injection, hard-coded keys), race conditions, performance anti-patterns (cursor loops, CONNECT BY), validation drift, and migration priority recommendations
- PR with all artifacts and a structured summary

## What Participants Will Learn

- How Devin reads and navigates an Oracle Forms codebase that spans UI, PL/SQL, and database layers
- How Forms XML exports reveal the UI structure, triggers, and LOV definitions without needing Oracle Forms Builder
- How PL/SQL package analysis exposes business rules, dependencies, and technical debt
- How dependency mapping across layers (Forms → PLL → packages → tables) reveals the true architecture
- The value of DeepWiki as a persistent knowledge surface for legacy codebases

## Devin Features Exercised

- DeepWiki for codebase exploration and architecture visualization
- AskDevin for targeted questions about legacy code
- Multi-layer analysis across Forms XML, PLL libraries, PL/SQL packages, and DDL schemas
- Documentation generation (structured markdown with tables and diagrams)
- Technical debt identification and prioritization

## Difficulty

Intermediate

## Estimated Time

60 minutes

## Going Further

- **Child sessions for parallel discovery**: Spawn a child session per functional area (employee management, payroll, leave, performance reviews, security) to produce detailed inventories in parallel. The parent session merges them into a consolidated view.
- **Scheduled re-analysis**: Configure a weekly scheduled session that re-runs the technical debt report. As remediation progresses, the report reflects which issues have been resolved.
- **Knowledge notes**: Capture the dependency map and technical debt findings as org-level knowledge notes so every future Devin session working on this migration starts with full context.
- **Playbook-driven discovery**: Encode the 4-artifact analysis as a playbook. Reuse it across Oracle Forms estates — each new customer engagement runs the same proven process.
- **Team-based operation**: Multiple engineers can review the inventory artifacts simultaneously. PR comments requesting additional analysis trigger Devin to resume and deepen the investigation.

## Notes

- This module produces **analysis artifacts, not code changes** — it is the discovery phase before any migration begins
- The outputs from this module feed directly into [Oracle Forms Migration Planning](oracle-forms-migration-planning.md) and the migration use case repo's test harness
- Encourage participants to use DeepWiki first to get oriented, then use Devin sessions for deep-dive analysis
- The HRMS app has multiple functional areas (employee management, payroll, leave, performance reviews, security) — participants should discover these through the inventory exercise
- Compare inventories across participants — did everyone find the same technical debt items and dependencies?
- The repo contains intentional bugs and anti-patterns documented in code comments — these simulate real legacy discovery

---

## <a id="ts-plsql-oracle-forms-hrms"></a>ts-plsql-oracle-forms-hrms

**Repository:** [ts-plsql-oracle-forms-hrms](https://github.com/Cognition-Partner-Workshops/ts-plsql-oracle-forms-hrms)

Oracle Forms 11g/12c HRMS legacy application. Contains Forms XML exports, PLL libraries, menu modules, PL/SQL packages (specs + bodies), database triggers, schema DDL (tables, views, sequences), and seed data. Realistic enterprise patterns with intentional technical debt.

### Step 1: Paste into Devin — Estate Discovery

```
Analyze the entire Oracle Forms/PL/SQL estate in
ts-plsql-oracle-forms-hrms. Produce the following artifacts:

1. APPLICATION_INVENTORY.md — catalog every Forms XML export
   in forms/xml-exports/, PLL library in forms/libraries/,
   menu module in forms/menus/, PL/SQL package in
   plsql/packages/, trigger in plsql/triggers/, and schema
   object in schema/. For each, list: filename, purpose
   (inferred from code), layer (UI/business logic/data
   access/integration/utility), and key dependencies.
2. DATA_DICTIONARY.md — for every table defined in
   schema/tables/, extract: table name, columns with data
   types and constraints, business meaning, relationships
   (foreign keys), and audit columns. Group by business
   domain (employee, payroll, leave, performance, security,
   system).
3. DEPENDENCY_MAP.md — build a multi-layer dependency graph:
   Forms modules → PLL libraries → PL/SQL packages →
   database tables. Identify circular dependencies, shared
   utilities, and the batch job scheduling topology
   (DBMS_SCHEDULER references).
4. TECHNICAL_DEBT_REPORT.md — find all intentional bugs and
   anti-patterns: security vulnerabilities (look for MD5,
   SQL injection, hard-coded keys), race conditions (look
   for missing SELECT FOR UPDATE), performance issues
   (cursor loops, CONNECT BY), validation drift (compare
   PLL vs package validation), and circular dependencies.
   Rank by severity and recommend migration priority.
```

### Step 2: Research with Ask Devin

- *"What are the circular dependencies in ts-plsql-oracle-forms-hrms and how do they affect compilation order?"*
- *"What security vulnerabilities exist in PKG_SECURITY and how would you fix them in a Java migration?"*
- *"Which PL/SQL packages contain the most business logic? Which are mostly CRUD wrappers?"*
- Use the answers to enrich your inventory and identify gaps in the initial analysis

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to see auto-generated architecture diagrams and module relationships. Use DeepWiki to:
- Identify the Forms-to-package call patterns
- Understand the PL/SQL package dependency graph
- Trace data flows from Forms UI through packages to database tables
- This becomes the **queryable knowledge surface** that accelerates all subsequent modernization work

### Step 4 (Optional): Review & Give Feedback

- **Review the inventory** — did Devin find all Forms modules and their trigger logic? Are the layer classifications accurate?
- **Review the technical debt report** — did Devin find the race condition in `generate_emp_number`? The SQL injection in `search_employees`? The MD5 hashing?
- **Leave a comment** asking Devin to add a section on PII fields (which tables contain personally identifiable information like SSNs, dates of birth)
- **Leave a comment** asking Devin to identify which batch jobs (DBMS_SCHEDULER) are likely run daily vs. monthly vs. on-demand

### Key Takeaways

- **Multi-layer discovery**: Devin traces the full Oracle Forms architecture — UI triggers, PLL libraries, PL/SQL packages, database objects — producing a comprehensive dependency map that reveals the true system structure
- **Technical debt surfacing**: Automated analysis finds security vulnerabilities, race conditions, and validation drift that manual code review often misses
- **Queryable knowledge surface**: The inventory and dependency map persist as artifacts that inform every subsequent migration decision
- **Foundation for planning**: These artifacts feed directly into migration strategy, component mapping, and risk assessment
