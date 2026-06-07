# Workshop: COBOL Modernization (Desktop + Cloud)

**Other variants:** [Cloud only](README.md)

## Overview

| | |
|---|---|
| **Focus** | End-to-end COBOL mainframe modernization — from system understanding through migration execution |
| **Duration** | ~4 hours (4 labs x 60 min + breaks) |
| **Delivery Surface** | **Devin Desktop + Devin Cloud** — Desktop is the primary interface; Cloud handles autonomous execution |
| **Audience** | Enterprise architects, mainframe modernization teams, application portfolio managers, migration program leads |
| **Key Modules** | [COBOL System Understanding](../../modules/migration-modernization/cobol-system-understanding.md), [COBOL to Java](../../modules/migration-modernization/cobol-to-java.md) |

## Abstract

> This track walks through the complete COBOL modernization lifecycle in four progressive labs using a real mainframe credit card management application:
>
> **Lab 1 — System Understanding:** Devin reverse-engineers the COBOL estate — builds an application inventory, extracts a data dictionary from copybooks, maps program dependencies and data lineage, and identifies modernization hotspots. This is the discovery phase that normally takes weeks of SME interviews.
>
> **Lab 2 — Migration Planning:** Devin produces a modernization blueprint with multiple strategy options (strangler, replatform, refactor, rewrite) for each subsystem. It identifies domain decomposition candidates with bounded context boundaries and drafts a phased cutover plan with risk analysis.
>
> **Lab 3 — Migration Safety:** Devin builds the test harness that makes migration safe — golden-file tests capturing current behavior, a differential testing framework for old-vs-new comparison, batch reconciliation checks, and interface contract tests. All before a single line of code is rewritten.
>
> **Lab 4 — Code Migration:** Devin translates selected COBOL programs to Java 17+ with parity tests, completing the modernization cycle. The test harness from Lab 3 validates that the Java implementation produces identical results.
>
> **Who should attend:** Enterprise architects evaluating modernization strategies, mainframe teams planning migration programs, application portfolio managers prioritizing legacy estates, and anyone responsible for de-risking large-scale COBOL migration.

## Platform Context

This workshop uses **Devin Desktop + Devin Cloud**. You will use Desktop as your primary interface — exploring code locally, delegating tasks to Cloud, and reviewing results without leaving the editor. Cloud sessions run autonomously on Devin's VM, and you monitor their progress from the Agent Command Center's Kanban board right inside Desktop.

> **Tip:** If you prefer a browser-only workflow, see the [Cloud only variant](README.md) which uses the Devin web app ([app.devin.ai](https://app.devin.ai)) directly. You can also use `devin` in your terminal (install from [cli.devin.ai](https://cli.devin.ai)) for additional local exploration — the CLI speaks ACP and appears as "Devin Local" in Desktop.

## Desktop + Cloud Workflow

In this variant, the interaction model shifts from the web app's paste-and-review cycle to a Desktop-native loop:

1. **Create a Space** — Open Devin Desktop and create a Space for this workshop. A Space groups sessions, PRs, files, and context for a single initiative. New sessions you launch from this Space inherit the project context automatically.
2. **Explore locally with Cascade or Devin Local** — Use Cascade (Desktop's built-in local agent) or Devin Local (CLI running over ACP) for code exploration and research. This replaces the Ask Devin research steps — you get fast, interactive, local-first answers without spinning up a cloud VM.
3. **Delegate to Devin Cloud** — When you are ready for autonomous implementation, send the task to Devin Cloud from Desktop with one click. Devin Cloud spins up a VM and works independently while you continue coding or exploring locally.
4. **Monitor in the Agent Command Center** — The Kanban board in Desktop shows all your agents (local and cloud) organized by status. See at a glance what is in flight, what is blocked, and what is ready for review — no browser tab juggling.
5. **Review PRs with one-click checkout** — When Devin Cloud opens a PR, review it directly in the editor. Desktop checks out the branch automatically — no manual `git fetch` or branch switching required.

## Getting the Most from This Workshop

> **Devin works asynchronously on its own machine.** Once you delegate a task to Cloud, Devin runs independently — you don't need to watch it. Move on to local exploration, start the next lab's research in Cascade, or review a previous PR while Cloud works in the background.

A few tips to maximize your hands-on time:

- **Delegate early, review later.** Send the implementation task to Cloud first, then use Cascade or Devin Local for research and exploration while Cloud works.
- **Use Cascade to refine requirements.** The better-defined a task is, the better Devin's output. Explore the codebase locally with Cascade before delegating to Cloud.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time. Knowledge persists across local and cloud sessions.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments directly in Desktop and Devin will wake up and address them — this is the core feedback loop.
- **Try parallel sessions.** Run multiple Cloud sessions simultaneously and monitor them from the Agent Command Center. This mirrors real enterprise usage and shows team-based operation at scale.
- **Mix local and cloud agents.** Use Devin Local for quick exploration and Cloud for heavy implementation — the Agent Command Center tracks both. Any ACP-compatible agent (Codex CLI, Claude Agent, Gemini CLI, and others) can plug into Desktop alongside Devin.

## Table of Contents

- [Structure](#structure)
- [Featured Labs](#featured-labs)
  - [Lab 1 — System Understanding & Reverse Engineering](#lab-1--system-understanding--reverse-engineering-60-min)
  - [Lab 2 — Migration Planning & Domain Decomposition](#lab-2--migration-planning--domain-decomposition-60-min)
  - [Lab 3 — Migration Test Harness & Validation Strategy](#lab-3--migration-test-harness--validation-strategy-60-min)
  - [Lab 4 — COBOL to Java Code Migration](#lab-4--cobol-to-java-code-migration-60-min)
- [Accelerated Variant](#accelerated-variant)
- [Repos Required](#repos-required-on-devins-machine)

---

## Structure

Four labs that build on each other in a progressive sequence:

1. **Lab 1 — System Understanding & Reverse Engineering (60 min):** Discover what the system does
2. **Break (15 min)**
3. **Lab 2 — Migration Planning & Domain Decomposition (60 min):** Decide how to modernize it
4. **Break (15 min)**
5. **Lab 3 — Migration Test Harness & Validation (60 min):** Build the safety net
6. **Break (15 min)**
7. **Lab 4 — COBOL to Java Code Migration (60 min):** Execute the migration

**Progression:**
- Lab 1: **Understand** — What does this system do? What are the programs, data, dependencies, and risks?
- Lab 2: **Plan** — How should we modernize it? What are the domains, strategies, and phases?
- Lab 3: **Safeguard** — How do we know the migration is correct? What tests catch regressions?
- Lab 4: **Execute** — Translate COBOL to Java with the test harness validating correctness

**Key narrative:** Each lab produces artifacts that feed the next. The inventory (Lab 1) informs the blueprint (Lab 2). The blueprint prioritizes which programs to test first (Lab 3). The test harness validates the migration (Lab 4). This mirrors how a real enterprise migration program operates.

---

## Featured Labs

### Lab 1 — System Understanding & Reverse Engineering (60 min)

- **Module:** [COBOL System Understanding & Reverse Engineering](../../modules/migration-modernization/cobol-system-understanding.md)
- **Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- **Objective:** Produce a complete system inventory, data dictionary, dependency map, and hotspot report for a COBOL mainframe application

#### Desktop Workflow

1. **Create a Space:** In Devin Desktop, create a Space called "COBOL Modernization" — this will group all sessions, PRs, and files for the workshop
2. **Explore with Cascade:** Open the repo in Desktop and use Cascade to ask questions about the COBOL codebase — explore program structure, copybook layouts, and JCL job flows interactively. This gives you fast, local-first answers without waiting for a cloud session
3. **Delegate the inventory to Cloud:** Once you have a feel for the codebase, delegate the full inventory task to Devin Cloud from Desktop:

```
Analyze the COBOL codebase in
Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java
and produce:
1. APPLICATION_INVENTORY.md — catalog of all programs, copybooks,
   JCL jobs, and BMS maps with classifications
2. DATA_DICTIONARY.md — business entities extracted from copybook
   PIC clauses
3. DEPENDENCY_MAP.md — call graph and data lineage (which programs
   call which, which jobs read/write which files)
4. HOTSPOT_REPORT.md — top 10 modules prioritized by complexity,
   risk, and business impact
```

4. **Monitor in Agent Command Center:** Watch the cloud session progress on the Kanban board. Continue exploring the codebase locally with Cascade while Cloud works
5. **Review the PR in Desktop:** When Devin Cloud opens a PR, use one-click checkout to review the artifacts directly in the editor — no browser switching needed

#### Key Takeaways

- **"Time-to-understanding drops from months to hours"** — dependency maps and data lineage that would take weeks of SME interviews are produced in one session
- **"Queryable knowledge surface"** — DeepWiki + the generated artifacts let anyone ask questions about the legacy system without reading COBOL
- **"Reduced key-person risk"** — captures legacy semantics before SMEs retire or rotate
- **"Parallelization"** — Devin can analyze many modules concurrently and produce consistent artifacts

#### Target Outcomes

- `APPLICATION_INVENTORY.md` with all programs, copybooks, JCL, BMS maps classified
- `DATA_DICTIONARY.md` with business entities extracted from copybooks
- `DEPENDENCY_MAP.md` with call graph and data lineage
- `HOTSPOT_REPORT.md` with top 10 modernization priority modules
- PR with all artifacts (reviewed in Desktop via one-click checkout)

---

### Lab 2 — Migration Planning & Domain Decomposition (60 min)

- **Module:** [COBOL Migration Planning & Domain Decomposition](../../modules/migration-modernization/cobol-migration-planning.md)
- **Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- **Objective:** Produce a modernization blueprint with strategy options, domain decomposition, phased cutover plan, and risk register

#### Desktop Workflow

1. **Explore with Cascade:** Use Cascade in Desktop to review the Lab 1 artifacts (inventory, dependency map, hotspot report). Ask questions like "which subsystems have the most cross-cutting dependencies?" to refine your understanding before planning
2. **Delegate planning to Cloud:** Send the planning task to Devin Cloud from Desktop:

```
Using the COBOL codebase in
Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java,
produce a modernization plan:
1. MODERNIZATION_BLUEPRINT.md — evaluate strangler, replatform,
   refactor, and rewrite strategies for each functional area with
   trade-off reasoning
2. DOMAIN_DECOMPOSITION.md — identify bounded contexts (account
   management, transaction processing, customer management,
   reporting, security) and map to candidate services
3. CUTOVER_PLAN.md — phased migration sequence with dependencies,
   rollback strategies, and acceptance criteria
4. RISK_REGISTER.md — top risks with mitigations (tribal knowledge
   gaps, environment limitations, data coupling)
```

3. **Monitor and continue:** Track the cloud session in the Agent Command Center. Use Cascade locally to start exploring the codebase for Lab 3 topics while Cloud works on the blueprint
4. **Review in Desktop:** One-click checkout the PR when it arrives. Review the blueprint and decomposition side-by-side with the COBOL source in your editor

#### Key Takeaways

- **"Options, not just 'rewrite it'"** — Devin evaluates multiple strategies per subsystem; different domains may warrant different approaches
- **"Domain decomposition from code, not whiteboards"** — the bounded contexts emerge from analyzing actual program dependencies and shared copybooks, not abstract architecture diagrams
- **"Phased cutover de-risks the program"** — the plan sequences extractions from lowest-risk to highest-risk, with rollback at each phase gate
- **"Risk-aware planning"** — the risk register surfaces concerns (tribal knowledge, environment gaps, data representativeness) that typically derail migration programs

#### Target Outcomes

- `MODERNIZATION_BLUEPRINT.md` with per-subsystem strategy recommendations
- `DOMAIN_DECOMPOSITION.md` with bounded context map and extraction seam analysis
- `CUTOVER_PLAN.md` with phased migration sequence
- `RISK_REGISTER.md` with top risks and mitigations
- PR with all planning artifacts (reviewed in Desktop)

---

### Lab 3 — Migration Test Harness & Validation Strategy (60 min)

- **Module:** [Migration Test Harness & Validation Strategy](../../modules/migration-modernization/migration-test-harness.md)
- **Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- **Objective:** Design and implement a test harness that validates migration correctness — golden files, differential testing, batch reconciliation, and contract tests

#### Desktop Workflow

1. **Research locally with Cascade:** Use Cascade to explore the ASCII data files and copybook layouts in the repo. Ask questions about data formats and record structures — this is fast, interactive research that informs the test strategy
2. **Delegate the test harness to Cloud:** Send the implementation task to Devin Cloud:

```
In Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java,
build a migration test harness:
1. TEST_STRATEGY.md — comprehensive test plan covering golden-file,
   differential, reconciliation, and contract testing dimensions
2. golden-files/ — parse ASCII data files using copybook layouts and
   produce structured JSON golden references
3. test-harness/ — parser utilities, comparison functions, and
   reconciliation checks (runnable code)
4. RECONCILIATION_CHECKS.md — per-job validation specifications
   (record counts, field totals, cross-reference integrity)
```

3. **Iterate locally:** While Cloud builds the harness, use Devin Local to prototype test utilities or explore edge cases in the data files. When the PR arrives, review the test code in Desktop
4. **Refine with PR comments:** If the harness needs adjustments, leave comments on the PR directly in Desktop — Devin Cloud will wake up and iterate

#### Key Takeaways

- **"Test before you migrate"** — building the safety net first is the single most important risk mitigation in legacy modernization
- **"Golden files capture behavior without understanding every rule"** — you don't need to reverse-engineer every business rule to verify that the new system produces the same output
- **"Differential testing enables safe iteration"** — run old and new side-by-side, diff the outputs, fix mismatches
- **"Batch reconciliation provides business-level confidence"** — not just "tests pass" but "totals balance, records match, cross-references are intact"

#### Target Outcomes

- `TEST_STRATEGY.md` covering all four testing dimensions
- `golden-files/` directory with structured reference outputs
- `test-harness/` directory with parser, comparison, and reconciliation code
- `RECONCILIATION_CHECKS.md` with per-job validation specifications
- PR with test strategy, golden files, and test framework code

---

### Lab 4 — COBOL to Java Code Migration (60 min)

- **Module:** [COBOL to Java](../../modules/migration-modernization/cobol-to-java.md)
- **Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- **Objective:** Translate selected COBOL programs to Java 17+ with parity tests that validate against the golden files and reconciliation checks from Lab 3

#### Desktop Workflow

1. **Select a target with Cascade:** Use Cascade to review the hotspot report (Lab 1), blueprint (Lab 2), and test harness (Lab 3). Ask Cascade to help you decide which program to migrate first based on complexity and risk
2. **Delegate the migration to Cloud:** Send the translation task to Devin Cloud:

```
In Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java,
translate the selected COBOL program(s) to Java 17+:
- Use modern Java idioms (records, pattern matching, text blocks)
- Produce JUnit tests that validate against the golden files from
  the test harness
- Document translation decisions in MIGRATION_NOTES.md covering
  data type mappings, control flow transformations, and edge cases
```

3. **Try parallel migrations:** For extra depth, delegate multiple COBOL programs to separate Cloud sessions — one per program. Monitor them simultaneously in the Agent Command Center's Kanban view
4. **Review and validate in Desktop:** One-click checkout each PR. Run the parity tests from Lab 3's harness against the Java output. Leave PR comments for Devin to iterate on any mismatches
5. **Trace the full loop:** Inventory (Lab 1) -> Blueprint (Lab 2) -> Test Harness (Lab 3) -> Migration (Lab 4) -> Validation (Lab 3 harness)

#### Key Takeaways

- **"Informed migration, not blind translation"** — by Lab 4, participants have a complete understanding of the system, a strategy, and a test harness before writing a single line of Java
- **"The test harness catches what code review misses"** — field-level comparison detects subtle differences (decimal precision, trailing spaces, date formats) that human reviewers would miss
- **"Devin handles the language barrier"** — COBOL to Java is a translation most developers cannot do; Devin bridges the gap
- **"Composable labs"** — each lab's output feeds the next, just like a real migration program

#### Target Outcomes

- Java source code replacing selected COBOL program(s)
- JUnit tests verifying functional equivalence
- Migration documentation covering translation decisions
- PR with Java code, tests, and migration notes

---

## Accelerated Variant

For shorter workshops (2 hours), use [Legacy Modernization Combined](../../modules/migration-modernization/legacy-modernization-combined.md) which compresses the migration execution into a multi-phase hands-on alongside framework upgrade and data source migration. Pair with a 30-minute abbreviated version of Lab 1 (system understanding only) for context.

| Duration | Recommended Format |
|----------|-------------------|
| 4+ hours | Full workshop: Lab 1 -> Lab 2 -> Lab 3 -> Lab 4 |
| 3 hours | Labs 1, 3, 4 (skip planning, focus on understand -> test -> migrate) |
| 2 hours | Abbreviated Lab 1 (30 min) + MM10 combined hands-on (60 min) + discussion |
| 1 hour | Walkthrough only: show pre-built artifacts from each lab phase |

---

## Additional Challenges

Participants who finish early or want to go deeper may attempt:

| Challenge | Module | Repo | Difficulty | Time |
|-----------|--------|------|-----------|------|
| Combined Modernization (COBOL + Framework + Data) | [Legacy Modernization Combined](../../modules/migration-modernization/legacy-modernization-combined.md) | Multiple repos | Advanced | 60 min |
| Framework Upgrade (Java 11 -> 17) | [Framework Upgrade](../../modules/migration-modernization/framework-upgrade.md) | uc-spring-boot-upgrade-microservice-extraction | Intermediate | 60 min |
| Data Source Migration | [Data Source Migration](../../modules/data-engineering/data-source-migration.md) | uc-data-source-migration-jdbc-normalization | Intermediate | 60 min |
| One-Shot Tech Debt Remediation | [One-Shot Tech Debt Remediation](../../modules/migration-modernization/one-shot-tech-debt-remediation.md) | uc-spring-boot-upgrade-microservice-extraction | Advanced | 75 min |

---

## Repos Required on Devin's Machine

- [ ] uc-legacy-modernization-cobol-to-java

### Optional (for extended challenges)

- [ ] uc-spring-boot-upgrade-microservice-extraction
- [ ] uc-data-source-migration-jdbc-normalization

## Runtime Resources

- No external runtime needed — COBOL analysis is static (no mainframe required)
- Java SDK 17+ for Lab 4 (code migration target)
- Python 3.10+ as alternative for test harness (Lab 3) if participants prefer Python over Java

## Context

- **Audience:** Enterprise modernization teams with COBOL estates
- **Narrative progression:** Understand (Lab 1) -> Plan (Lab 2) -> Safeguard (Lab 3) -> Execute (Lab 4)
- **Enterprise value props shown hands-on:**
  - Lab 1: Devin compresses months of discovery into hours — dependency maps, data dictionaries, and hotspot analysis without SME interviews
  - Lab 2: Devin produces decision-ready modernization blueprints with multiple strategy options, not just "rewrite everything"
  - Lab 3: Devin builds the migration safety net (tests, reconciliation) before any code changes — reducing migration risk
  - Lab 4: Devin bridges the COBOL-to-Java language barrier that blocks most development teams
- **Desktop + Cloud value props:**
  - Cascade enables fast, interactive research before delegating heavy tasks to Cloud
  - Agent Command Center provides a single view of all sessions — local and cloud — especially useful when running parallel migrations
  - One-click checkout keeps the review loop inside the editor, eliminating context switches
  - Spaces group all workshop sessions, PRs, and files into a single organizational unit
  - ACP extensibility means teams can bring additional agents (Codex CLI, Claude Agent, Gemini CLI, and others) into Desktop alongside Devin
- **Suitability:** Medium -> High depending on toolchain access (see notes below)

### Suitability Notes

| Scenario | Rating | Why |
|----------|--------|-----|
| Full toolchain access (CI, emulator, batch runs) | **High** | Devin can verify outputs automatically and iterate on migration |
| Code access only (no COBOL runtime) | **Medium-High** | Devin reverse-engineers, documents, plans, and generates migration code + tests; humans validate outputs |
| Code not accessible (manual/GUI only) | **Low** | Devin cannot analyze what it cannot access |

### Key Constraints

- **Hidden tribal knowledge** — ops runbooks, scheduler configs, dataset naming conventions, and exception handling may not be in the code
- **Environment gaps** — mainframe-specific runtimes (CICS, IMS, JES) and data access (VSAM, DB2) cannot run in Devin's Linux VM; analysis is static
- **Data representativeness** — migration safety depends on realistic test inputs and reconciliation rules, not just happy paths

## Devin Features Checklist

Track your progress on the [Devin Features Appendix](../../modules/devin-features/README.md) throughout the session. Key activities for this track:

- [ ] Create a Space in Devin Desktop for the workshop
- [ ] Use Cascade or Devin Local for codebase exploration (Labs 1, 2)
- [ ] Delegate a task to Devin Cloud from Desktop (any lab)
- [ ] Monitor sessions in the Agent Command Center (any lab)
- [ ] One-click checkout a cloud PR in Desktop (after any lab)
- [ ] Use DeepWiki to explore a COBOL codebase (Lab 1)
- [ ] Have Devin produce structured analysis artifacts (Labs 1, 2)
- [ ] Have Devin generate test code from data specifications (Lab 3)
- [ ] Run parallel Cloud sessions from Desktop (translate multiple programs in Lab 4)
- [ ] Leave PR comments in Desktop and watch Devin iterate (Labs 3, 4)
- [ ] Create Knowledge from a session (after Lab 1 analysis)
- [ ] Compare results across participants (all labs)

## Post-Event

- [ ] Collect participant feedback — especially: which lab phase was most valuable for their modernization program?
- [ ] Archive any event-specific branches
- [ ] Update challenge modules if issues were discovered
- [ ] Share session recordings/artifacts with participants
