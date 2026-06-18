# Workshop: COBOL Modernization

## Overview

| | |
|---|---|
| **Focus** | End-to-end COBOL mainframe modernization — from system understanding through migration execution |
| **Duration** | ~4 hours (4 labs × 60 min + breaks) |
| **Audience** | Enterprise architects, mainframe modernization teams, application portfolio managers, migration program leads |
| **Key Modules** | [COBOL System Understanding](../../../../../modules/migration-modernization/cobol-system-understanding.md), [COBOL to Java](../../../../../modules/migration-modernization/cobol-to-java.md) |

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

## Getting the Most from This Workshop

> **Devin works asynchronously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin keeps working in the background.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem before Devin executes.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments and Devin will wake up and address them — this is the core feedback loop.
- **Try parallel sessions.** Running multiple sessions simultaneously mirrors real enterprise usage and demonstrates team-based operation at scale.

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

- **Module:** [COBOL System Understanding & Reverse Engineering](../../../../../modules/migration-modernization/cobol-system-understanding.md)
- **Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- **Objective:** Produce a complete system inventory, data dictionary, dependency map, and hotspot report for a COBOL mainframe application

#### What to Try

1. **Start with DeepWiki:** Open the repo's DeepWiki page and explore how Devin auto-generates architecture documentation for COBOL — a language most modern developers cannot read
2. **Run the inventory:** Have Devin catalog all 30+ programs, copybooks, JCL jobs, and sub-applications with classifications
3. **Extract the data dictionary:** Try Devin parsing copybook PIC clauses into a business-friendly data dictionary
4. **Map dependencies:** Explore the call graph and data lineage — which programs call which, which jobs read/write which files
5. **Identify hotspots:** Review the prioritized list of modules by complexity, risk, and business impact

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
- PR with all artifacts

---

### Lab 2 — Migration Planning & Domain Decomposition (60 min)

- **Module:** [COBOL Migration Planning & Domain Decomposition](../../../../../modules/migration-modernization/cobol-migration-planning.md)
- **Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- **Objective:** Produce a modernization blueprint with strategy options, domain decomposition, phased cutover plan, and risk register

#### What to Try

1. **Set the context:** Reference the inventory and dependency map from Lab 1 (or have Devin build context from the codebase directly)
2. **Strategy analysis:** Have Devin evaluate strangler, replatform, refactor, and rewrite options for each functional area — with trade-off reasoning, not just a blanket recommendation
3. **Domain decomposition:** Have Devin identify bounded contexts (account management, transaction processing, customer management, reporting, security) and map them to candidate services
4. **Cutover plan:** Review the phased sequence with dependencies, rollback strategies, and acceptance criteria
5. **Risk register:** Walk through the top risks — tribal knowledge gaps, environment limitations, data coupling

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
- PR with all planning artifacts

---

### Lab 3 — Migration Test Harness & Validation Strategy (60 min)

- **Module:** [Migration Test Harness & Validation Strategy](../../../../../modules/migration-modernization/migration-test-harness.md)
- **Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- **Objective:** Design and implement a test harness that validates migration correctness — golden files, differential testing, batch reconciliation, and contract tests

#### What to Try

1. **Test strategy:** Have Devin produce a comprehensive test plan covering four testing dimensions (golden-file, differential, reconciliation, contract)
2. **Golden-file capture:** Have Devin parse the ASCII data files using copybook layouts and produce structured JSON golden references
3. **Test framework:** Inspect the generated parser utilities, comparison functions, and reconciliation checks — real, runnable code
4. **Reconciliation checks:** Walk through specific checks per batch job: record counts, field totals, cross-reference integrity
5. **Connect to Lab 4:** Explain how this harness will validate the Java migration in the next lab

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

- **Module:** [COBOL to Java](../../../../../modules/migration-modernization/cobol-to-java.md)
- **Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)
- **Objective:** Translate selected COBOL programs to Java 17+ with parity tests that validate against the golden files and reconciliation checks from Lab 3

#### What to Try

1. **Select a target:** Use the hotspot report from Lab 1 and the blueprint from Lab 2 to choose which program to migrate first
2. **Translate:** Have Devin rewrite the COBOL program as Java 17+ with modern idioms
3. **Validate:** Run the parity tests from Lab 3's test harness against the Java output
4. **Document:** Have Devin produce migration notes covering translation decisions
5. **Trace the full loop:** Inventory (Lab 1) → Blueprint (Lab 2) → Test Harness (Lab 3) → Migration (Lab 4) → Validation (Lab 3 harness)

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

For shorter workshops (2 hours), use [Legacy Modernization Combined](../../../../../modules/migration-modernization/legacy-modernization-combined.md) which compresses the migration execution into a multi-phase hands-on alongside framework upgrade and data source migration. Pair with a 30-minute abbreviated version of Lab 1 (system understanding only) for context.

| Duration | Recommended Format |
|----------|-------------------|
| 4+ hours | Full workshop: Lab 1 → Lab 2 → Lab 3 → Lab 4 |
| 3 hours | Labs 1, 3, 4 (skip planning, focus on understand → test → migrate) |
| 2 hours | Abbreviated Lab 1 (30 min) + MM10 combined hands-on (60 min) + discussion |
| 1 hour | Walkthrough only: show pre-built artifacts from each lab phase |

---

## Additional Challenges

Participants who finish early or want to go deeper may attempt:

| Challenge | Module | Repo | Difficulty | Time |
|-----------|--------|------|-----------|------|
| Combined Modernization (COBOL + Framework + Data) | [Legacy Modernization Combined](../../../../../modules/migration-modernization/legacy-modernization-combined.md) | Multiple repos | Advanced | 60 min |
| Framework Upgrade (Java 11 → 17) | [Framework Upgrade](../../../../../modules/migration-modernization/framework-upgrade.md) | uc-spring-boot-upgrade-microservice-extraction | Intermediate | 60 min |
| Data Source Migration | [Data Source Migration](../../../../../modules/data-engineering/data-source-migration.md) | uc-data-source-migration-jdbc-normalization | Intermediate | 60 min |
| One-Shot Tech Debt Remediation | [One-Shot Tech Debt Remediation](../../../../../modules/migration-modernization/one-shot-tech-debt-remediation.md) | uc-spring-boot-upgrade-microservice-extraction | Advanced | 75 min |

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
- **Narrative progression:** Understand (Lab 1) → Plan (Lab 2) → Safeguard (Lab 3) → Execute (Lab 4)
- **Enterprise value props shown hands-on:**
  - Lab 1: Devin compresses months of discovery into hours — dependency maps, data dictionaries, and hotspot analysis without SME interviews
  - Lab 2: Devin produces decision-ready modernization blueprints with multiple strategy options, not just "rewrite everything"
  - Lab 3: Devin builds the migration safety net (tests, reconciliation) before any code changes — reducing migration risk
  - Lab 4: Devin bridges the COBOL-to-Java language barrier that blocks most development teams
- **Suitability:** Medium → High depending on toolchain access (see notes below)

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

Encourage participants to track their progress on the [Devin Features Appendix](../../../../../modules/devin-features/README.md) throughout the session. Key activities for this track:

- [ ] Use DeepWiki to explore a COBOL codebase (Lab 1)
- [ ] Use AskDevin to ask targeted questions about legacy code (Labs 1, 2)
- [ ] Have Devin produce structured analysis artifacts (Labs 1, 2)
- [ ] Have Devin generate test code from data specifications (Lab 3)
- [ ] Run parallel Devin sessions (translate multiple programs in Lab 4)
- [ ] Review a Devin PR via GitHub (after any lab)
- [ ] Leave PR comments and watch Devin iterate (Labs 3, 4)
- [ ] Create Knowledge from a session (after Lab 1 analysis)
- [ ] Compare results across participants (all labs)

## Post-Event

- [ ] Collect participant feedback — especially: which lab phase was most valuable for their modernization program?
- [ ] Archive any event-specific branches
- [ ] Update challenge modules if issues were discovered
- [ ] Share session recordings/artifacts with participants
