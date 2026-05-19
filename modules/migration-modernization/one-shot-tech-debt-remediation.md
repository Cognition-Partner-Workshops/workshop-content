# One-Shot Tech Debt Remediation

## Repositories

- [uc-cve-remediation-regulatory-compliance](#uc-cve-remediation-regulatory-compliance)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Achieve 80-90% one-shot completion of a major tech debt remediation through a single, meticulously crafted prompt that forces a **provable deliverable** with explicit acknowledgement of testing gaps. This exercises Devin's long-term reasoning capabilities — the ability to plan, execute, and self-verify a complex multi-file transformation in a single session without human intervention.

The key insight: the prompt itself is the engineering. A well-structured prompt that demands specific proof artifacts (build output, test results, before/after metrics) and requires Devin to document what it *could not* verify produces dramatically better outcomes than a vague "upgrade this project" instruction.

## The Prompt Engineering Pattern

The prompt must contain five elements to achieve 80-90% one-shot completion:

1. **Explicit scope boundary** — exactly what to upgrade and what to leave alone
2. **Ordered execution plan** — the sequence of changes, not just the end state
3. **Proof requirements** — build output, test results, and diff statistics that Devin must include in the PR
4. **Testing gap acknowledgement** — Devin must document what it could NOT verify (integration tests that need infrastructure, E2E tests that need a running environment, performance tests that need load)
5. **Exit criteria** — specific, measurable conditions that define "done" (e.g., "zero compilation errors, all unit tests pass, no javax imports remaining")

## Target Outcomes

- A single Devin session that completes 80-90% of a major tech debt remediation
- PR with comprehensive proof artifacts: build logs, test results, migration notes
- `TESTING_GAPS.md` documenting what Devin could not verify and why
- `MIGRATION_PROOF.md` with before/after metrics (dependency versions, CVE counts, test coverage)
- Clean build with all unit tests passing after the upgrade
- Explicit documentation of remaining 10-20% work that requires human judgment or infrastructure access

## What Participants Will Learn

- **Prompt engineering for complex tasks** — how structure and specificity in the prompt directly correlate with completion rate
- **Long-term reasoning** — how Devin plans and executes a multi-step transformation without losing context
- **Provable deliverables** — why demanding proof artifacts in the prompt produces better results than reviewing after the fact
- **Testing gap awareness** — the discipline of documenting what you know you don't know
- **The 80/90 principle** — why getting 80-90% done autonomously and documenting the remaining 10-20% is more valuable than claiming 100% with hidden gaps

## Devin Features Exercised

- Long-term session execution (60+ minutes of sustained reasoning)
- Multi-file refactoring with dependency tracking
- Build system modification and verification
- Self-validation (running tests, checking compilation)
- Structured documentation generation
- Honest gap reporting (what Devin knows it can't verify)

## Difficulty

Advanced

## Estimated Time

75 minutes

## Notes

- The quality of the prompt IS the exercise — facilitators should encourage participants to spend 10-15 minutes crafting the prompt before submitting it
- The "80-90% one-shot" framing is deliberate: it sets realistic expectations and values honesty about gaps over false claims of completeness
- Compare results across participants: did different prompt structures produce different completion rates?
- The `TESTING_GAPS.md` is the most important artifact — it demonstrates engineering maturity (knowing what you don't know)
- This module works best as the capstone of a lab track, after participants have seen Devin work in simpler scenarios

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 / Java 11 / Gradle monolith. The tech debt: outdated framework, legacy namespace (javax.*), no containerization, missing CI, outdated dependencies.

### Step 1: Paste into Devin — The One-Shot Prompt

> **Objective:** Upgrade uc-spring-boot-upgrade-microservice-extraction from Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2 in a single session with provable results.
>
> **Execution order:**
> 1. Before making any changes, run `./gradlew build` and capture the output as `build-before.log`. Record: Java version, Spring Boot version, dependency count, test count, test pass/fail.
> 2. Update `build.gradle`: set sourceCompatibility to Java 17, upgrade Spring Boot to 3.2.x, upgrade all Spring dependencies to compatible versions, upgrade Gradle wrapper if needed.
> 3. Migrate all `javax.*` imports to `jakarta.*` across the entire codebase. Use find-and-replace but verify each file compiles.
> 4. Fix all remaining compilation errors from deprecated APIs, removed classes, or changed method signatures. Document each fix.
> 5. Run `./gradlew build` and capture output as `build-after.log`. All tests must pass.
> 6. Run `./gradlew dependencyCheckAnalyze` if the OWASP plugin is configured. Capture the report.
>
> **Required deliverables in the PR:**
> - `MIGRATION_PROOF.md` containing: before/after Java version, before/after Spring Boot version, before/after dependency versions for top 10 dependencies, before/after test results (count, pass, fail), and a diff summary (files changed, insertions, deletions).
> - `TESTING_GAPS.md` listing every test scenario you CANNOT verify in this environment: integration tests requiring a database, E2E tests requiring a running server, performance/load tests, security scanning that requires network access, and any test you skipped or marked @Disabled with a reason.
> - All changes in a single PR with a structured description.
>
> **Exit criteria:** Zero compilation errors. All existing unit tests pass. No `javax.*` imports remaining in `src/`. Build completes successfully. Both proof documents are complete and accurate.

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

---

## <a id="uc-cve-remediation-regulatory-compliance"></a>uc-cve-remediation-regulatory-compliance

**Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)

Same Spring Boot 2.6.3 codebase but with an emphasis on remediating the 18+ known CVEs as tech debt. The one-shot prompt here focuses on security tech debt specifically.

### Step 1: Paste into Devin — The One-Shot Prompt

> **Objective:** Remediate all CRITICAL and HIGH CVEs in uc-cve-remediation-regulatory-compliance in a single session with provable, auditable results.
>
> **Execution order:**
> 1. Run `./gradlew dependencyCheckAnalyze` and capture the full report. Parse it to create a `CVE_INVENTORY_BEFORE.md` listing every finding with: CVE ID, affected dependency, CVSS score, severity, and description. Sort by CVSS descending.
> 2. Remediate all CRITICAL findings (CVSS >= 9.0) first: Spring Boot 2.6.3 (Spring4Shell), SnakeYAML 1.29, sqlite-jdbc 3.36.0.3.
> 3. Remediate all HIGH findings (CVSS >= 7.0): Jackson Databind, Spring Security, any remaining transitive dependencies.
> 4. For each remediation: upgrade the dependency in build.gradle, fix any breaking API changes, and verify compilation passes.
> 5. Run `./gradlew build` — all tests must pass.
> 6. Run `./gradlew dependencyCheckAnalyze` again and capture as `CVE_INVENTORY_AFTER.md`.
>
> **Required deliverables in the PR:**
> - `CVE_INVENTORY_BEFORE.md` — full scan results before remediation
> - `CVE_INVENTORY_AFTER.md` — full scan results after remediation
> - `MIGRATION_PROOF.md` — side-by-side comparison: CVE count before/after, CRITICAL count before/after, HIGH count before/after, dependency version changes
> - `TESTING_GAPS.md` — what security tests you CANNOT verify: runtime vulnerability exploitation tests, network-based scanning (Trivy, Snyk), DAST/penetration tests, compliance policy checks that require organizational context
> - All code changes in a single PR
>
> **Exit criteria:** Zero CRITICAL CVEs remaining. HIGH CVE count reduced by at least 80%. Build passes. All unit tests pass. All four documents are complete.

### Step 2: Research with Ask Devin

- *"Which of the 18 CVEs in uc-cve-remediation-regulatory-compliance are in direct dependencies vs. transitive? Which can be fixed just by upgrading Spring Boot?"*
- *"What's the minimum set of dependency changes that eliminates all CRITICAL CVEs?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the dependency graph. Map which CVEs are connected (e.g., upgrading Spring Boot also fixes Spring Security CVEs).

### Step 4 (Optional): Review & Give Feedback

- **Review `CVE_INVENTORY_BEFORE.md` vs. `CVE_INVENTORY_AFTER.md`** — what's the delta? Did Devin hit the 80% HIGH reduction target?
- **Review `TESTING_GAPS.md`** — is it honest? Are there gaps Devin should have acknowledged but didn't?
- **Leave a comment** asking Devin to add a GitHub Actions workflow that runs OWASP DC on every PR to prevent CVE regression
