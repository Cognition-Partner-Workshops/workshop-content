# Migration Test Harness & Validation Strategy

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
- [uc-legacy-modernization-cobol-to-java](#uc-legacy-modernization-cobol-to-java)

---

## Quick Start

Paste this prompt into Devin to try building a migration test harness for the CardDemo application:

```
Design and implement a migration test harness for
uc-legacy-modernization-cobol-to-java. Create the following:

1. TEST_STRATEGY.md — Document the testing approach:
   - Golden-file tests: which programs to capture outputs
     for, what inputs to use, how to compare
   - Differential tests: how to run old (COBOL) and new
     (Java) implementations side-by-side
   - Batch reconciliation: what totals, counts, and
     checksums to verify after each batch run
   - Contract tests: what file formats, record layouts,
     and interface contracts to codify

2. golden-files/ — Parse the ASCII data files in
   app/data/ASCII/ using the field layouts defined in the
   copybooks (app/cpy/). For each data file, produce a
   structured JSON representation that serves as the golden
   reference.

3. test-harness/ — Create a Java or Python project with:
   - A parser utility that reads fixed-width COBOL data
     files based on copybook PIC clause definitions
   - A comparison utility that diffs two outputs
     field-by-field and reports mismatches
   - Reconciliation check functions: record count
     validation, numeric field sum validation,
     cross-reference integrity checks

4. RECONCILIATION_CHECKS.md — For each batch job in
   app/jcl/, document: what it reads, what it writes, what
   reconciliation checks should pass, and what business
   rules it enforces.
```

---

## Repositories

- [uc-legacy-modernization-cobol-to-java](#uc-legacy-modernization-cobol-to-java)

---

## Challenge

Use Devin to design and implement a migration test harness that makes COBOL-to-modern migration safe. Before any code is rewritten, Devin creates the safety net: golden-file tests that capture current behavior, a differential testing framework for old-vs-new comparison, batch reconciliation checks, and contract tests around file/table/message interfaces.

Migration without tests is a gamble. This module shows how Devin builds the verification infrastructure that catches regressions before they reach production — even when the legacy codebase has zero existing tests.

## Target Outcomes

- `TEST_STRATEGY.md` — comprehensive test plan covering: golden-file/snapshot tests, differential testing approach, batch reconciliation methodology, and contract test specifications
- `golden-files/` directory — captured reference outputs for representative inputs from the CardDemo data files, organized by program and scenario
- `test-harness/` directory — a Java/Python test framework with: (a) utilities to parse COBOL-format data files (fixed-width fields per copybook layouts), (b) comparison functions for old vs. new output, (c) reconciliation checks (record counts, field totals, hash comparisons)
- `RECONCILIATION_CHECKS.md` — specific checks for each batch job: input record count = output record count, transaction totals balance, cross-reference integrity, and business rule validations
- PR with the test strategy, golden files, test framework code, and reconciliation documentation

## What Participants Will Learn

- Why testing infrastructure must be built **before** migration begins, not after
- How golden-file testing captures legacy behavior without understanding every business rule
- How differential testing enables safe side-by-side validation of old and new implementations
- How batch reconciliation checks provide business-level confidence (totals balance, records match)
- How to create tests for a system that has no existing test suite

## Devin Features Exercised

- Code generation (test framework in Java or Python)
- Data file analysis (parsing fixed-width COBOL data formats)
- Multi-file comprehension (copybook layouts → test utilities)
- PR creation with code + documentation
- AskDevin for test strategy design
- DeepWiki for understanding data flows to test

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

## Going Further

- **Child sessions for parallel test generation**: Spawn one child session per batch job or data file to generate golden files and reconciliation checks in parallel. Each child produces its own test artifacts; the parent merges them into a consolidated test suite.
- **Scheduled parity testing**: After migration begins, configure a scheduled session that runs the full reconciliation suite weekly. Catch regressions as soon as they appear.
- **Event-driven validation**: Connect a webhook so that when a migration PR is merged, Devin automatically runs the parity tests against the new Java code and reports results.
- **Playbook-driven harness creation**: Encode the test harness methodology (parse copybooks → generate golden files → build comparison utilities → document reconciliation checks) as a playbook. Reuse it across multiple COBOL estates.
- **Team-based operation**: The test strategy and reconciliation checks become shared knowledge notes. Every Devin session working on this migration inherits the validation criteria.

## Notes

- This module produces **test infrastructure and documentation, plus some generated code** — the test framework is real, runnable code even though the COBOL programs themselves cannot be executed in this environment
- The golden-file approach works even without a running COBOL environment: capture known-good outputs from the ASCII data files and use them as reference
- The CardDemo `app/data/ASCII/` directory contains human-readable sample data that can be used for golden-file baselines
- Copybook PIC clauses define the exact field layouts — Devin should use these to build parsers
- This module feeds directly into [COBOL to Java](cobol-to-java.md) (COBOL to Java migration) — the test harness validates that the Java rewrite produces identical output
- If time is limited, focus on golden-file capture + reconciliation checks rather than a full differential testing framework

---

## <a id="uc-legacy-modernization-cobol-to-java"></a>uc-legacy-modernization-cobol-to-java

**Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)

AWS Mainframe Modernization CardDemo (Apache 2.0). Contains ASCII data files (account, customer, card, transaction data), copybooks defining field layouts, and batch JCL jobs whose outputs need validation.

### Step 1: Paste into Devin — Test Harness Design

```
Design and implement a migration test harness for
uc-legacy-modernization-cobol-to-java. Create the following:

1. TEST_STRATEGY.md — Document the testing approach for
   migrating this COBOL application:
   - Golden-file tests: which programs to capture outputs
     for, what inputs to use, how to compare
   - Differential tests: how to run old (COBOL) and new
     (Java) implementations side-by-side
   - Batch reconciliation: what totals, counts, and
     checksums to verify after each batch run
   - Contract tests: what file formats, record layouts,
     and interface contracts to codify

2. golden-files/ — Parse the ASCII data files in
   app/data/ASCII/ using the field layouts defined in the
   copybooks (app/cpy/). For each data file, produce a
   structured JSON representation that serves as the golden
   reference. Document what each field means based on the
   copybook definitions.

3. test-harness/ — Create a Java or Python project with:
   - A parser utility that reads fixed-width COBOL data
     files based on copybook PIC clause definitions
   - A comparison utility that diffs two outputs
     field-by-field and reports mismatches with field
     names and positions
   - Reconciliation check functions: record count
     validation, numeric field sum validation,
     cross-reference integrity checks (e.g., every card
     in cardxref.txt has a matching account in
     acctdata.txt)

4. RECONCILIATION_CHECKS.md — For each batch job in
   app/jcl/, document: what it reads, what it writes, what
   reconciliation checks should pass (record counts, totals,
   referential integrity), and what business rules it
   enforces.
```

### Step 2: Research with Ask Devin

- *"What are the field layouts for the account data file (acctdata.txt)? Which copybook defines the record structure?"*
- *"What reconciliation checks would catch a data corruption in the transaction processing pipeline? What totals should balance?"*
- *"If the Java rewrite of CBACT01C produces slightly different output (e.g., trailing spaces, decimal precision), how should the comparison utility handle it?"*
- Use the answers to refine the test harness — add tolerance rules, edge case handling, and business-specific validations

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand data flows:
- Which programs read which data files (trace I/O operations)
- What transformations happen between input and output (the logic the tests need to verify)
- Where cross-references exist between data files (these need integrity checks)

### Step 4 (Optional): Review & Give Feedback

- **Review the golden files** — are the JSON representations accurate? Do the field names match the copybook definitions?
- **Review the test harness code** — does the parser handle all PIC clause types (X, 9, S9, V, COMP-3)?
- **Leave a comment** asking Devin to add a specific reconciliation check: "verify that the sum of all transaction amounts in dailytran.txt matches the sum of all balance changes in acctdata.txt"
- **Leave a comment** asking Devin to add edge case golden files for: empty input, single record, maximum field values

### Key Takeaways

- **Tests before migration**: Building the verification infrastructure before rewriting any code creates a safety net that catches regressions from day one
- **Golden-file testing**: Capturing known-good outputs from legacy data files provides a behavioral baseline without needing to understand every business rule
- **Reconciliation checks**: Business-level validations (totals balance, record counts match, referential integrity holds) provide confidence that no data is lost or corrupted during migration
- **Reusable across programs**: The test harness, once built, validates every subsequent COBOL-to-Java translation — each child session's migration PR runs against the same parity tests
