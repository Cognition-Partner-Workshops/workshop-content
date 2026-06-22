# COBOL to Java

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [Starting Points](#starting-points)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Going Further](#going-further)
- [Notes](#notes)
- [uc-legacy-modernization-cobol-to-java](#uc-legacy-modernization-cobol-to-java)

---

## Quick Start

Paste this prompt into Devin to try migrating a COBOL program to Java:

```
Analyze the COBOL program CBACT01C.cbl in
uc-legacy-modernization-cobol-to-java. Understand its business
logic, data structures (copybooks), and I/O operations.
Rewrite it as a Java 17+ application using modern idioms.
Create JUnit tests that verify the Java version produces
identical results to the COBOL version for a set of sample
inputs.
```

---

## Repositories

- [uc-legacy-modernization-cobol-to-java](#uc-legacy-modernization-cobol-to-java)

---

## Challenge

Explore a real COBOL mainframe application and use Devin to modernize part of it to Java 17+. You choose what to migrate, how to structure the Java code, and what target language/framework to use — the goal is to see how Devin handles legacy code comprehension and translation.

## Target Outcomes

- Java source code replacing selected COBOL program(s) with parity tests
- Migration documentation covering translation decisions
- Business rules extracted and documented
- PR with Java code, tests, and migration notes

## Starting Points

The repo contains programs of varying complexity:

| Program | Location | Description | Complexity | Good For |
|---------|----------|-------------|-----------|----------|
| `CBACT01C.cbl` | `app/cbl/` | Account file batch processing | Medium | First migration attempt |
| `CBACT02C.cbl` | `app/cbl/` | Account data processing | Medium | Data transformation focus |
| `CBTRN01C.cbl` | `app/cbl/` | Transaction processing | High | Complex business logic |
| `COTRTUPC.cbl` | `app/app-transaction-type-db2/cbl/` | Transaction type update | Low-Medium | Quick win |
| `CBSTM03A.CBL` | `app/cbl/` | Statement generation | Medium-High | Report generation |

## What Participants Will Learn

- How Devin reads and understands COBOL (a language most developers can't read)
- How Devin maps COBOL data structures (copybooks, WORKING-STORAGE) to modern equivalents
- How Devin handles COBOL-specific constructs (PERFORM, EVALUATE, file I/O, packed decimal)
- The importance of parity testing in migration
- How different prompting strategies (direct vs. AskDevin-refined) affect output quality

## Devin Features Exercised

- Multi-language understanding (COBOL to Java/Kotlin/Python)
- Code generation with architectural decisions
- Test generation for functional equivalence
- PR creation with detailed migration notes
- DeepWiki for codebase exploration
- AskDevin for pre-session planning
- Child sessions for parallel migration of multiple programs

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

## Going Further

- **Divide-and-conquer with child sessions**: Migrations are inherently parallelizable. A parent session can analyze the estate, create a migration playbook, then spawn one child session per COBOL program. Each child follows the same playbook (analyze → translate → test → document) and opens its own PR. A 30-program estate that would take weeks sequentially can be processed in parallel.
- **Playbook-driven migration**: Encode the migration methodology (parse copybooks, map data structures, translate business logic, generate parity tests) as a playbook. Every child session follows the same proven steps, ensuring consistent quality across all translations.
- **Scheduled regression testing**: After initial migration, configure a scheduled session that runs the parity tests weekly. If upstream COBOL changes are committed, the tests catch regressions.
- **Event-driven migration**: Connect a webhook so that when a COBOL program is modified, Devin automatically updates the corresponding Java translation and re-runs parity tests.
- **Team-based review**: Multiple engineers can review Devin's migration PRs simultaneously. PR comments requesting changes (e.g., "use an enum for status codes") trigger Devin to resume and iterate.

## Notes

- The repo has no build system (COBOL is compiled on mainframes) — Devin will need to create a project structure from scratch
- There is no single "right answer" — different participants will produce different migrations
- The CardDemo app includes DB2 SQL — the Java version can use JDBC, JPA, or an ORM
- Encourage participants to share and compare their different approaches after the lab

---

## <a id="uc-legacy-modernization-cobol-to-java"></a>uc-legacy-modernization-cobol-to-java

**Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)

AWS Mainframe Modernization CardDemo (Apache 2.0). Contains 30+ COBOL programs (.cbl), JCL, DB2 integration, copybooks, and VSAM file definitions.

### Step 1: Paste into Devin — COBOL to Java Migration

```
Analyze the COBOL program CBACT01C.cbl in
uc-legacy-modernization-cobol-to-java. Understand its business
logic, data structures (copybooks), and I/O operations.
Rewrite it as a Java 17+ application using modern idioms.
Create JUnit tests that verify the Java version produces
identical results to the COBOL version for a set of sample
inputs.
```

### Step 2: Research with Ask Devin

- *"What are the most complex COBOL programs in uc-legacy-modernization-cobol-to-java and what do they do?"*
- *"What would be the best Java architecture for migrating CBTRN01C.cbl? Consider Spring Boot, plain Java, or Kotlin."*
- Use the refined plan as your Devin session prompt — compare the result to your first attempt

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to see the auto-generated architecture diagrams, module relationships, and code explanations. Identify which COBOL programs interest you and what they do. Use this understanding to decide your own migration scope.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Java code faithfully represent the COBOL business logic?
- **Leave a comment** asking Devin to handle a specific COBOL construct differently (e.g., *"Can you use an enum for the status codes instead of string constants?"*)
- Try migrating the **same program to two different targets** (e.g., Java and Python) in parallel sessions and compare results

### Key Takeaways

- **Cross-language comprehension**: Devin reads COBOL — copybooks, WORKING-STORAGE, PERFORM logic, packed decimal arithmetic — and translates it to idiomatic modern Java
- **Parity testing**: Migration without verification is guesswork. Devin generates JUnit tests that compare Java output against known COBOL behavior
- **Parallelizable migration**: Each COBOL program is an independent migration unit. Child sessions can migrate multiple programs simultaneously, each following the same playbook
- **Iterative refinement**: PR comments steer Devin's translation decisions — requesting different patterns, frameworks, or data representations
