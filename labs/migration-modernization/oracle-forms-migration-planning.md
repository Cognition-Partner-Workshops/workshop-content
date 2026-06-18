# Oracle Forms Migration Planning

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

Paste this prompt into Devin to try modernization planning on the Oracle Forms HRMS application:

```
Analyze the Oracle Forms/PL/SQL application in
ts-plsql-oracle-forms-hrms and produce a modernization plan.
Create the following artifacts:

1. MODERNIZATION_BLUEPRINT.md — For each major functional
   area (employee management, payroll, leave, performance
   reviews, security), evaluate modernization strategies:
   (a) Lift-and-shift to APEX, (b) Rewrite as Spring Boot +
   Angular/React, (c) Rewrite as .NET + Blazor, (d) Hybrid:
   keep PL/SQL packages as API, build new UI layer. For each
   area, recommend the best strategy with justification.
2. COMPONENT_MAPPING.md — Map each Oracle Forms module to its
   target Java/web component. For each mapping: source file,
   target file, business rules to preserve, data access
   patterns to convert, and UI elements to recreate.
3. CUTOVER_PLAN.md — Sequence the migration into phases, with
   dependencies, rollback strategies, and acceptance criteria
   per phase. The first phase should be the lowest-risk form.
4. RISK_REGISTER.md — Document the top 10 migration risks
   including: PL/SQL business logic that may not translate
   cleanly, circular dependency compilation issues, PII
   fields requiring special handling, and environment gaps.
```

---

## Repositories

- [ts-plsql-oracle-forms-hrms](#ts-plsql-oracle-forms-hrms)

---

## Challenge

Use Devin to produce a concrete modernization plan for an Oracle Forms application. Devin analyzes the Forms modules, PL/SQL packages, and database schema to produce a component mapping (what maps to what in the new stack), a phased migration strategy, and risk assessment. The output is an actionable plan, not a vague proposal.

Unlike COBOL, Oracle Forms modernization involves three distinct layers (UI, business logic, data) that can be migrated independently or together. This module explores the strategic decisions around which layers to migrate first and how to preserve business rules.

## Target Outcomes

- `MODERNIZATION_BLUEPRINT.md` — strategy options for each functional area with trade-off analysis and recommendations
- `COMPONENT_MAPPING.md` — detailed source-to-target mapping for every Forms module, PLL library, and PL/SQL package
- `CUTOVER_PLAN.md` — phased migration sequence with rollback strategies
- `RISK_REGISTER.md` — top risks with likelihood, impact, and mitigation strategies
- PR with all planning artifacts

## What Participants Will Learn

- How Devin reasons about multi-layer modernization trade-offs (UI, business logic, data)
- How component mapping creates a traceable migration path from Oracle Forms to modern web
- How phased cutover planning manages risk when migrating from Forms to Java/web
- The difference between lift-and-shift (APEX), full rewrite, and hybrid approaches
- How dependency mapping informs migration sequencing

## Devin Features Exercised

- AskDevin for architecture strategy and trade-off analysis
- Long-form reasoning about complex modernization decisions
- Architecture documentation generation
- Component mapping across technology stacks
- DeepWiki for understanding Forms/PL/SQL architecture

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

## Going Further

- **Child sessions for parallel planning**: Spawn a child session per functional area (employee, payroll, leave, performance, security) to produce detailed component mappings in parallel. The parent session merges them into a consolidated migration plan.
- **Playbook-driven planning**: Encode the 4-artifact planning methodology as a playbook. Reuse it across Oracle Forms estates — each new engagement runs the same proven discovery-to-planning process.
- **Scheduled progress tracking**: After migration begins, configure a scheduled session that compares the current codebase against the component mapping to track which modules have been migrated and which remain.
- **Knowledge notes**: Capture the component mapping and risk register as org-level knowledge notes so every future Devin session working on this migration inherits the planning context.
- **Team-based operation**: Architecture review comments on the planning PR trigger Devin to refine the blueprint — iterating on strategy decisions through PR conversation.

## Notes

- This module works best after [Oracle Forms System Understanding](oracle-forms-system-understanding.md) — the inventory and dependency map inform the planning
- If running standalone, Devin will perform discovery as part of the planning session, which may reduce depth
- The HRMS app has natural functional boundaries: employee management, payroll, leave, performance reviews, security — participants should discover these through the component mapping exercise
- There is no single "right" plan — different participants will recommend different strategies based on their target stack assumptions
- The repo also feeds into [Oracle Forms to Java](oracle-forms-to-java.md) where participants execute the plan

---

## <a id="ts-plsql-oracle-forms-hrms"></a>ts-plsql-oracle-forms-hrms

**Repository:** [ts-plsql-oracle-forms-hrms](https://github.com/Cognition-Partner-Workshops/ts-plsql-oracle-forms-hrms)

Oracle Forms 11g/12c HRMS application. Contains Forms XML exports, PLL libraries, menu modules, PL/SQL packages (specs + bodies), database triggers, schema DDL, and seed data. Natural functional boundaries for component mapping and migration sequencing.

### Step 1: Paste into Devin — Migration Planning

```
Analyze the Oracle Forms/PL/SQL application in
ts-plsql-oracle-forms-hrms and produce a modernization plan.
Create the following artifacts:

1. MODERNIZATION_BLUEPRINT.md — For each major functional
   area (employee management, payroll, leave, performance
   reviews, security), evaluate modernization strategies:
   (a) Lift-and-shift to APEX, (b) Rewrite as Spring Boot +
   Angular/React, (c) Rewrite as .NET + Blazor, (d) Hybrid:
   keep PL/SQL packages as API, build new UI layer. For each
   area, recommend the best strategy with justification
   considering: business logic complexity, data coupling,
   team skill availability, and risk tolerance.
2. COMPONENT_MAPPING.md — Map each Oracle Forms module to its
   target Java/web component. For each mapping specify:
   source Forms module and its PLL/package dependencies,
   target component (controller, service, entity, UI
   template), business rules that must be preserved,
   data access patterns to convert (PL/SQL cursor → JPA
   repository), and UI elements to recreate (LOVs, blocks,
   alerts).
3. CUTOVER_PLAN.md — Sequence the migration into phases with:
   which modules migrate in each phase, data migration
   requirements, integration bridges needed during transition,
   rollback strategies, and acceptance criteria per phase
   gate. The first phase should be the lowest-risk,
   most-isolated form.
4. RISK_REGISTER.md — Document the top 10 migration risks:
   PL/SQL business logic that may not translate cleanly,
   circular dependency compilation issues, PII fields
   requiring special handling, validation drift between PLL
   and PL/SQL packages, environment gaps (no Oracle DB in
   target environment), and any other risks identified.
```

### Step 2: Research with Ask Devin

- *"Which PL/SQL packages in ts-plsql-oracle-forms-hrms contain the most business logic vs. just CRUD operations? What's the migration risk for each?"*
- *"What are the circular dependencies between packages and how do they affect migration sequencing?"*
- *"If I wanted to migrate the Leave Management module first as a pilot, what dependencies would I need to handle?"*
- Use the answers to refine your planning artifacts

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to visualize the Forms-to-package-to-table relationships. Use this to:
- Validate the component mapping — do the relationships match what Devin identified?
- Identify shared packages that create coupling between functional areas
- Understand which Forms modules are most isolated (best candidates for first migration)

### Step 4 (Optional): Review & Give Feedback

- **Review the blueprint** — are the strategy recommendations well-justified? Is the hybrid approach considered?
- **Review the component mapping** — is the mapping complete? Did Devin handle PLL library dependencies correctly?
- **Leave a comment** asking Devin to add effort estimates (T-shirt sizes) per migration phase
- **Leave a comment** asking Devin to identify which modules could be migrated in parallel vs. which must be sequential

### Key Takeaways

- **Multi-layer strategy**: Oracle Forms modernization requires separate decisions for UI, business logic, and data layers — Devin evaluates each independently
- **Component-level traceability**: A detailed source-to-target mapping ensures nothing is lost during migration and creates a verifiable checklist
- **Phased risk management**: Sequencing by isolation and risk means the first migration phase is the safest, building confidence before tackling coupled modules
- **Divide-and-conquer at scale**: Once the mapping identifies independent modules, each can be migrated by a separate child session — parallelizing the work across the entire Forms estate
