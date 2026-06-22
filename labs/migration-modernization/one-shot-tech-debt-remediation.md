# One-Shot Tech Debt Remediation

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Challenge](#challenge)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Going Further](#going-further)
- [Notes](#notes)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [uc-cve-remediation-regulatory-compliance](#uc-cve-remediation-regulatory-compliance)

---

## Quick Start

Paste this prompt into Devin to try a one-shot Spring Boot upgrade with provable results:

```
Perform a complete one-shot upgrade of
uc-spring-boot-upgrade-microservice-extraction from Java 11
+ Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2.x.

Execution order:
1. Capture current state: run ./gradlew build and save
   output as build-before.log. Record Java version, Spring
   Boot version, dependency versions, and test results.
2. Update build.gradle: set sourceCompatibility to Java 17,
   upgrade Spring Boot to 3.2.x, upgrade all Spring
   dependencies to compatible versions, upgrade Gradle
   wrapper if needed.
3. Migrate all javax.* imports to jakarta.* across the
   entire codebase. Verify each file compiles.
4. Fix all remaining compilation errors from deprecated
   APIs, removed classes, or changed method signatures.
5. Run ./gradlew build and capture as build-after.log.
   All tests must pass.

Required deliverables in the PR:
- MIGRATION_PROOF.md: before/after Java version, Spring
  Boot version, dependency versions, test results, and
  diff summary
- TESTING_GAPS.md: every test scenario you cannot verify
  in this environment (integration tests, E2E tests,
  performance tests, security scans)

Exit criteria: zero compilation errors, all unit tests
pass, no javax.* imports remaining in src/, build
completes successfully.
```

---

## Repositories

- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction) — Spring Boot upgrade target
- [uc-cve-remediation-regulatory-compliance](#uc-cve-remediation-regulatory-compliance) — CVE remediation target

---

## Challenge

Perform a complete tech debt remediation in a single Devin session with provable, auditable results. This is the enterprise pattern: one-shot upgrades or remediations where the deliverables include not just the code changes but also proof documents (before/after metrics, migration evidence, testing gap analysis) that can satisfy audit and compliance requirements.

Two variations:
1. **Spring Boot upgrade** — Upgrade from Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2 with full javax→jakarta migration, plus proof documents
2. **CVE remediation** — Remediate all CRITICAL and HIGH CVEs with OWASP Dependency Check scans before and after, plus proof documents

Both exercises demonstrate Devin's ability to execute long-horizon tasks with structured reasoning and produce enterprise-grade deliverables.

## What Participants Will Learn

- How Devin plans and executes multi-step upgrades with structured reasoning
- How proof documents (before/after metrics, migration evidence) satisfy enterprise audit requirements
- How testing gap analysis creates transparency about what can and cannot be verified in the CI environment
- The difference between "it compiles" and "it's production-ready" — honest documentation of verification limits
- How one-shot remediation compresses weeks of manual work into a single session

## Devin Features Exercised

- Long-horizon task execution (multi-step, sequenced operations)
- Structured document generation (proof documents, gap analysis)
- Build system interaction (Gradle build, dependency analysis)
- Systematic code modification (namespace migration, dependency updates)
- PR creation with enterprise-grade documentation
- Child sessions for parallel remediation across repos

## Difficulty

Advanced

## Estimated Time

75 minutes

## Going Further

- **Divide-and-conquer with child sessions**: When remediating tech debt across a portfolio of services, spawn one child session per service. Each child follows the same remediation playbook (capture baseline, upgrade, verify, document proof, document gaps). A parent session aggregates results into a portfolio-level compliance report.
- **Playbook-driven remediation**: Encode the one-shot remediation methodology (capture before → upgrade → verify → capture after → document proof → document gaps) as a playbook. Apply it to every service in the portfolio.
- **Scheduled compliance scanning**: Configure a weekly scheduled session that runs OWASP Dependency Check across all repos and reports any new CRITICAL or HIGH CVEs.
- **Event-driven remediation**: Connect a webhook to CVE databases or Dependabot. When a new CRITICAL CVE is published for a dependency in use, Devin automatically remediates and opens a PR with proof documents.
- **Knowledge notes**: Capture common upgrade patterns (javax→jakarta mapping rules, Spring Boot 2→3 breaking changes) as knowledge notes so future remediation sessions start with accumulated wisdom.

## Notes

- This is an **enterprise module** — the proof documents and testing gap analysis are what differentiate it from a simple framework upgrade
- The `MIGRATION_PROOF.md` should contain verifiable metrics: exact version numbers, test counts, build timestamps
- The `TESTING_GAPS.md` should be honest — list what cannot be tested, not just what was tested. This transparency builds trust with audit teams
- The CVE remediation variant adds OWASP Dependency Check scans — this requires the Gradle plugin to be configured in the project
- Both repos use the same Spring Boot 2.6.3 codebase, so participants can compare the upgrade-focused vs. security-focused approaches

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 + Java 11 application. Target: Java 17 + Spring Boot 3.2 with full namespace migration and proof documents.

### Step 1: Paste into Devin — The One-Shot Prompt

```
Perform a complete one-shot upgrade of
uc-spring-boot-upgrade-microservice-extraction from Java 11
+ Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2.x.

Execution order:
1. Capture current state: run ./gradlew build and save
   output as build-before.log. Record Java version, Spring
   Boot version, dependency versions, and test results.
2. Update build.gradle: set sourceCompatibility to Java 17,
   upgrade Spring Boot to 3.2.x, upgrade all Spring
   dependencies to compatible versions, upgrade Gradle
   wrapper if needed.
3. Migrate all javax.* imports to jakarta.* across the
   entire codebase. Use find-and-replace but verify each
   file compiles.
4. Fix all remaining compilation errors from deprecated
   APIs, removed classes, or changed method signatures.
   Document each fix.
5. Run ./gradlew build and capture output as
   build-after.log. All tests must pass.
6. Run ./gradlew dependencyCheckAnalyze if the OWASP
   plugin is configured. Capture the report.

Required deliverables in the PR:
- MIGRATION_PROOF.md containing: before/after Java version,
  before/after Spring Boot version, before/after dependency
  versions for top 10 dependencies, before/after test
  results (count, pass, fail), and a diff summary (files
  changed, insertions, deletions).
- TESTING_GAPS.md listing every test scenario you CANNOT
  verify in this environment: integration tests requiring a
  database, E2E tests requiring a running server,
  performance/load tests, security scanning that requires
  network access, and any test you skipped or marked
  @Disabled with a reason.
- All changes in a single PR with a structured description.

Exit criteria: Zero compilation errors. All existing unit
tests pass. No javax.* imports remaining in src/. Build
completes successfully. Both proof documents are complete
and accurate.
```

### Step 2: Research with Ask Devin

- *"What are the riskiest parts of upgrading uc-spring-boot-upgrade-microservice-extraction from Spring Boot 2 to 3? Which files have the most javax imports?"*
- *"What test infrastructure does this project have? Which tests are pure unit tests vs. integration tests that need external services?"*
- Use the analysis to refine the prompt — add specific file paths, known problem areas, or additional proof requirements

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the full architecture. Identify:
- How many files use `javax.*` imports (scope of the namespace migration)
- What test frameworks are in use (JUnit 5? Mockito? Spring Test?)
- What external dependencies might break (MyBatis, SQLite JDBC, etc.)

### Step 4 (Optional): Review & Give Feedback

- **Review `MIGRATION_PROOF.md`** — are the before/after metrics accurate? Do the numbers add up?
- **Review `TESTING_GAPS.md`** — did Devin honestly document what it couldn't verify? Are there gaps it missed?
- **Leave a comment** asking Devin to also extract the Articles domain into a separate microservice as a follow-up task (this tests whether the upgrade created a clean foundation for further work)
- **Compare prompts across participants** — who got the highest one-shot completion rate? What made their prompt better?

### Key Takeaways

- **One-shot execution**: Devin executes a complex, multi-step upgrade in a single session — capture baseline, upgrade, verify, document — without manual intervention
- **Proof documents**: Enterprise-grade deliverables include verifiable before/after metrics, not just code changes
- **Honest gap analysis**: Documenting what cannot be tested is as important as documenting what can — this transparency builds trust with audit teams
- **Repeatable methodology**: The same one-shot pattern works for any framework upgrade across a portfolio

---

## <a id="uc-cve-remediation-regulatory-compliance"></a>uc-cve-remediation-regulatory-compliance

**Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)

Same Spring Boot 2.6.3 codebase but with an emphasis on remediating the 18+ known CVEs as tech debt. The one-shot prompt here focuses on security tech debt specifically.

### Step 1: Paste into Devin — The One-Shot Prompt

```
Remediate all CRITICAL and HIGH CVEs in
uc-cve-remediation-regulatory-compliance in a single
session with provable, auditable results.

Execution order:
1. Run ./gradlew dependencyCheckAnalyze and capture the
   full report. Parse it to create CVE_INVENTORY_BEFORE.md
   listing every finding with: CVE ID, affected dependency,
   CVSS score, severity, and description. Sort by CVSS
   descending.
2. Remediate all CRITICAL findings (CVSS >= 9.0) first:
   Spring Boot 2.6.3 (Spring4Shell), SnakeYAML 1.29,
   sqlite-jdbc 3.36.0.3.
3. Remediate all HIGH findings (CVSS >= 7.0): Jackson
   Databind, Spring Security, any remaining transitive
   dependencies.
4. For each remediation: upgrade the dependency in
   build.gradle, fix any breaking API changes, and verify
   compilation passes.
5. Run ./gradlew build — all tests must pass.
6. Run ./gradlew dependencyCheckAnalyze again and capture
   as CVE_INVENTORY_AFTER.md.

Required deliverables in the PR:
- CVE_INVENTORY_BEFORE.md — full scan results before
  remediation
- CVE_INVENTORY_AFTER.md — full scan results after
  remediation
- MIGRATION_PROOF.md — side-by-side comparison: CVE count
  before/after, CRITICAL count before/after, HIGH count
  before/after, dependency version changes
- TESTING_GAPS.md — what security tests you CANNOT verify:
  runtime vulnerability exploitation tests, network-based
  scanning (Trivy, Snyk), DAST/penetration tests,
  compliance policy checks that require organizational
  context
- All code changes in a single PR

Exit criteria: Zero CRITICAL CVEs remaining. HIGH CVE
count reduced by at least 80%. Build passes. All unit
tests pass. All four documents are complete.
```

### Step 2: Research with Ask Devin

- *"Which of the 18 CVEs in uc-cve-remediation-regulatory-compliance are in direct dependencies vs. transitive? Which can be fixed just by upgrading Spring Boot?"*
- *"What's the minimum set of dependency changes that eliminates all CRITICAL CVEs?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the dependency graph. Map which CVEs are connected (e.g., upgrading Spring Boot also fixes Spring Security CVEs).

### Step 4 (Optional): Review & Give Feedback

- **Review `CVE_INVENTORY_BEFORE.md` vs. `CVE_INVENTORY_AFTER.md`** — what's the delta? Did Devin hit the 80% HIGH reduction target?
- **Review `TESTING_GAPS.md`** — is it honest? Are there gaps Devin should have acknowledged but didn't?
- **Leave a comment** asking Devin to add a GitHub Actions workflow that runs OWASP DC on every PR to prevent CVE regression

### Key Takeaways

- **Security tech debt at scale**: Devin remediates 18+ CVEs in a single session, upgrading dependencies, fixing breaking changes, and producing auditable proof documents
- **Before/after evidence**: OWASP Dependency Check scans provide verifiable evidence of remediation — not just "we upgraded", but "these specific CVEs are resolved"
- **Portfolio-scale remediation**: The same CVE remediation playbook can run against every service in a portfolio via child sessions, producing a consolidated compliance report
- **Continuous compliance**: Scheduled sessions running OWASP DC catch new CVEs as they're published, before they accumulate into tech debt
