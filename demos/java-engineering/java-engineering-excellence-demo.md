# Java Engineering Excellence — Full-Lifecycle Demo

A single linear demo showing Devin executing **four distinct engineering tasks**
against a Java/Spring Boot codebase — feature development, security remediation,
major version upgrade, and test generation — each gated by a programmatic
verification loop. The narrative: a single engineer amplifies their scope,
velocity, and quality by treating Devin as a team-based resource across many
classes of engineering work.

The prompts below invoke the `!java-engineering-excellence` Devin Playbook —
the reusable procedure — whose source lives in the code repo at
[`ts-java-spring-boot-realworld/.workshop/playbooks/java-engineering-excellence.devin.md`](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld/blob/main/.workshop/playbooks/java-engineering-excellence.devin.md).
The repo-specific build commands and dependency coordinates come from that repo's
Skill (`.agents/skills/java-engineering-excellence/SKILL.md`).

## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [Before, After, and the Verification Loop](#before-after)
- [Act 1 — Feature Development](#act-1)
- [Act 2 — Issue Triage and Security Remediation (Automations)](#act-2)
- [Act 3 — App Modernization (Java 11 → 21, Spring Boot 2.6 → 3.5)](#act-3)
- [Act 4 — Test Generation and Coverage](#act-4)
- [Parallel Fan-Out — Modernization at Scale](#fan-out)
- [Scheduled Devins — Continuous Engineering Excellence](#scheduled)
- [Confirming Completion](#confirm)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Pick any act and paste its prompt into a Devin session with access to
`Cognition-Partner-Workshops/ts-java-spring-boot-realworld`:

- **[Act 1 — Feature Dev](#act-1)**: add a bookmark article feature
- **[Act 2 — Security](#act-2)**: remediate a CVE in jackson-databind
- **[Act 3 — Modernization](#act-3)**: upgrade Java 11 → 21, Boot 2.6 → 3.5
- **[Act 4 — Testing](#act-4)**: generate tests to meet 80% JaCoCo coverage

The verification gate for all tasks: `./gradlew clean test spotlessCheck`

---

<a id="repository"></a>
## Repository

- [ts-java-spring-boot-realworld](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld)
  — Spring Boot 2.6.3 / Java 11 / Gradle / MyBatis + DGS GraphQL. A full-stack
  "RealWorld" app (CRUD, auth, pagination, GraphQL) with 120 source files, 26
  test classes, JaCoCo 80% coverage gate, and Spotless formatting enforcement.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | State |
|---|---|
| **Before** | `main`: Java 11, Spring Boot 2.6.3, deprecated `WebSecurityConfigurerAdapter`, `javax.*` namespace, `joda-time`, known CVEs in transitive dependencies. Working test suite (87 tests green). The playbook source and Skill are on `main`. |
| **After** | Feature branches (one per task) — each with a verified PR. The upgrade branch runs on Java 21 / Spring Boot 3.5.x with `jakarta.*`, lambda Security DSL, and all 87+ tests green. |

The **verification gate** is the same for every task:

```bash
./gradlew clean test spotlessCheck
# Must output: BUILD SUCCESSFUL, 0 failures, no Spotless violations
```

For coverage tasks, the gate extends to:

```bash
./gradlew jacocoTestCoverageVerification
# Must output: line coverage ≥ 80%
```

The before-state is durable. Every task produces a feature branch and PR — never
merged to `main` — so the demo is safe to repeat.

---

<a id="act-1"></a>
## Act 1 — Feature Development

An engineer asks Devin to implement a new feature — a data model change with
API endpoints and tests. Devin follows existing patterns, writes tests, and
gates on the verification loop.

```
!java-engineering-excellence

Task: feature-dev
Repository: Cognition-Partner-Workshops/ts-java-spring-boot-realworld

Add a "bookmark article" feature:
- Users can bookmark/unbookmark articles (similar to favorites but
  separate — a personal reading list rather than a public like)
- New domain entity: ArticleBookmark (userId, articleId, createdAt)
- MyBatis mapper + Flyway migration for the bookmark table
- REST endpoints: POST/DELETE /articles/{slug}/bookmark
- GraphQL mutation: bookmarkArticle / unbookmarkArticle
- Include unit tests for the service and API layer
- Follow the existing patterns in core/favorite/ as a reference
```

**What to observe:**

1. Devin reads the existing `ArticleFavorite` implementation to learn the
   pattern — entity, mapper, repository, API, tests.
2. Implements the full vertical slice following conventions.
3. Runs `./gradlew clean test spotlessCheck` — all green including new tests.
4. Opens a PR with the feature and test results.

**The value**: a complete, tested feature implemented in minutes, following the
codebase's existing architecture. Not a prototype — production-shaped code with
tests and formatting that passes CI on first push.

---

<a id="act-2"></a>
## Act 2 — Issue Triage and Security Remediation (Automations)

A security scanner (or Devin Automation triggered by a Dependabot alert)
identifies critical CVEs in the dependency tree. Devin triages, remediates, and
proves the fix — automatically.

### Setting up the Automation

Before running this act, configure a Devin Automation that triggers on
security-related GitHub events:

**Trigger**: GitHub webhook — Dependabot alert created (or `issues` labeled
`security`)

**Action**: Start a Devin session with the following prompt:

```
!java-engineering-excellence

Task: issue-triage
Repository: Cognition-Partner-Workshops/ts-java-spring-boot-realworld
Issue: {event.alert.summary}
CVE: {event.alert.cve_id}

Remediate this CVE by upgrading the affected dependency. Verify the
fix by confirming the vulnerable version is no longer in the
dependency tree and that all tests pass.
```

### Running the remediation manually

```
!java-engineering-excellence

Task: issue-triage
Repository: Cognition-Partner-Workshops/ts-java-spring-boot-realworld
Issue: Critical CVE in jackson-databind transitive dependency
CVE: CVE-2022-42003 (jackson-databind polymorphic deserialization RCE)

Remediate this CVE: identify the vulnerable version in the dependency
tree, upgrade to a patched version, and verify that the full test
suite still passes. Include before/after dependency tree output in
the PR description.
```

**What to observe:**

1. Devin runs `./gradlew dependencies` to identify the vulnerable version.
2. Pins the safe version in `build.gradle` (or upgrades the parent that pulls
   it transitively).
3. Runs verification gate — tests still green.
4. PR includes the before/after dependency tree as evidence.

**The Automation angle**: in production, this entire flow fires automatically
when Dependabot opens an alert. No human triage step. The PR lands in the
team's review queue already verified and ready to merge. Navigate to Settings →
Automations to see the event-driven trigger configuration.

**The value**: security remediation at machine speed. CVE disclosed at 2 AM →
Devin has a verified fix PR waiting in the morning. The team reviews and merges
instead of triaging, researching, patching, and testing.

---

<a id="act-3"></a>
## Act 3 — App Modernization (Java 11 → 21, Spring Boot 2.6 → 3.5)

The centerpiece. A major framework upgrade that touches nearly every file —
`javax` → `jakarta`, deprecated security APIs removed, third-party libraries
with breaking changes. Devin upgrades layer by layer, gating each step on the
test suite. The tests catch a real silent regression.

```
!java-engineering-excellence

Task: app-modernization
Repository: Cognition-Partner-Workshops/ts-java-spring-boot-realworld

Upgrade from Java 11 / Spring Boot 2.6.3 to Java 21 / Spring Boot
3.5.x. This is a major upgrade involving:
- Gradle plugin versions (spring-boot, dependency-management, DGS codegen)
- Java sourceCompatibility/targetCompatibility → 21
- javax.* → jakarta.* namespace migration (validation, servlet, annotation)
- WebSecurityConfigurerAdapter → SecurityFilterChain bean with lambda DSL
- antMatchers → requestMatchers, authorizeRequests → authorizeHttpRequests
- MyBatis starter 2.x → 3.x
- DGS framework 4.x → 8.x (Spring Boot 3 compatible)
- jjwt 0.11 → 0.12
- REST-Assured 4.x → 5.x (test scope)
- Remove joda-time → java.time.Instant/OffsetDateTime

Upgrade in layers. Run the verification gate (./gradlew clean test
spotlessCheck) after each layer. The test suite is the contract —
fix any regressions before proceeding.
```

**The verification beat (the real bug).** After upgrading imports from
`javax.validation` → `jakarta.validation`, the build compiles cleanly. But an
explicit `javax.validation:validation-api:2.0.1.Final` dependency left in
`build.gradle` puts two validation providers on the classpath. Spring's
`MethodValidationPostProcessor` binds to `jakarta`, but the `@Valid` annotation
on controllers still resolves from the leftover `javax` jar — validation is
silently skipped.

The test suite catches it immediately:

```
UsersApiTest > should_get_error_with_invalid_registration_data FAILED
    Expected status code <422> but was <200>

ArticlesApiTest > should_get_error_message_with_wrong_parameter FAILED
    Expected status code <422> but was <200>
```

Devin removes the stale `javax.validation:validation-api` dependency, re-runs,
and the full suite goes green:

```
BUILD SUCCESSFUL in 47s
87 tests completed, 0 failed
```

**What to observe:**

1. Devin works through the upgrade layer by layer — build config first, then
   namespace, then security, then infrastructure, then GraphQL.
2. After the namespace migration, the tests expose the validation regression.
3. Devin diagnoses the dual-provider classpath issue, removes the stale dep.
4. Full gate goes green. PR includes the complete upgrade changelog.

**The value**: a human doing this upgrade would spend days. A "find and replace"
approach would silently break validation (the tests catch it — a reviewer would
not). Devin does the upgrade in layers with continuous verification, catching
the regression the moment it's introduced and fixing it before it reaches
review.

---

<a id="act-4"></a>
## Act 4 — Test Generation and Coverage

After the upgrade lands, Devin fills coverage gaps — generating tests for
classes that dropped below the 80% JaCoCo threshold. No production code
changes; pure quality improvement.

```
!java-engineering-excellence

Task: test-generation
Repository: Cognition-Partner-Workshops/ts-java-spring-boot-realworld

Run the JaCoCo coverage report and identify classes below the 80%
line-coverage threshold. Generate unit tests for the lowest-coverage
classes following existing test patterns:
- Controller tests: @WebMvcTest + REST-Assured MockMvc
- Repository tests: extend DbTestBase
- Service tests: JUnit 5 + Mockito

Target: bring overall line coverage to ≥ 80% and pass
jacocoTestCoverageVerification. Do not modify production code.
```

**What to observe:**

1. Devin runs `./gradlew jacocoTestReport`, reads the HTML report, identifies
   gap classes.
2. Writes tests following existing patterns (reads neighboring test files for
   style).
3. Runs `./gradlew jacocoTestCoverageVerification` — passes the 80% gate.
4. PR shows coverage before → after.

**The value**: test writing is high-effort, low-glory work that teams defer.
Devin generates production-quality tests that follow existing conventions,
meeting the coverage bar without a human writing a single `@Test` method.

---

<a id="fan-out"></a>
## Parallel Fan-Out — Modernization at Scale

The Act 3 upgrade can be parallelized with child sessions — one agent per
architectural layer, all working simultaneously:

```
Act as the orchestrator for a Spring Boot upgrade across the
ts-java-spring-boot-realworld codebase, using child Devin sessions to
parallelize the work by layer.

Repository: Cognition-Partner-Workshops/ts-java-spring-boot-realworld

Spawn one child Devin session per layer below. Each child follows the
!java-engineering-excellence playbook (task: app-modernization) for
its assigned packages only, creates its own branch, runs the gate for
its layer, and reports green:

1. API + Security layer (io.spring.api.*)
   - WebSecurityConfigurerAdapter → SecurityFilterChain
   - javax.* → jakarta.* in controllers
   - antMatchers → requestMatchers
   Branch: devin/<ts>-upgrade-api

2. Infrastructure layer (io.spring.infrastructure.*)
   - MyBatis starter 2.x → 3.x
   - jjwt 0.11 → 0.12
   - sqlite-jdbc upgrade
   Branch: devin/<ts>-upgrade-infra

3. GraphQL layer (io.spring.graphql.*)
   - DGS 4.x → 8.x
   - Update codegen plugin
   Branch: devin/<ts>-upgrade-graphql

4. Core + Application layer (io.spring.core.*, io.spring.application.*)
   - joda-time → java.time
   - javax.validation → jakarta.validation in domain
   Branch: devin/<ts>-upgrade-core

After all children report green, merge the four branches into a single
upgrade branch, run the full gate (./gradlew clean test spotlessCheck),
and open the consolidated PR.
```

**What to observe:**

- Four child sessions running simultaneously, each on its own isolated VM.
- Each child opens its own PR with passing tests for its layer.
- The orchestrator merges, runs the final integrated gate, and produces the
  consolidated PR.
- Isolation is the feature: no merge conflicts during parallel work because
  each child touches a disjoint package set.

---

<a id="scheduled"></a>
## Scheduled Devins — Continuous Engineering Excellence

The same playbook runs unattended on a schedule for ongoing maintenance:

| Schedule | Task | Prompt |
|----------|------|--------|
| **Nightly** | Security scan | `!java-engineering-excellence` task: `issue-triage` — scan for new CVEs, remediate critical/high |
| **Weekly** | Dependency freshness | `!java-engineering-excellence` task: `issue-triage` — check for outdated dependencies, bump patch/minor versions |
| **On PR merge** | Coverage guard | `!java-engineering-excellence` task: `test-generation` — ensure coverage stays above 80% after new code lands |

Configure these in Settings → Schedules. Each scheduled run produces a PR if
action is needed, or a clean report if everything is current. The team reviews
PRs in the morning — maintenance work that previously required dedicated sprint
capacity now happens automatically.

---

<a id="confirm"></a>
## Confirming Completion

After each act, verify the result in GitHub:

**1. The PR exists and CI is green.** Open the PR — GitHub Actions shows the
`Java CI` workflow passing: checkout → JDK setup → `./gradlew clean test`.
All tests pass. Spotless reports no violations.

**2. The verification gate output is in the PR.** The PR description or a
comment includes the test results:

```
BUILD SUCCESSFUL in 42s
87 tests completed, 0 failed
Spotless: 120 files checked — no violations
```

**3. For Act 3 (modernization), the upgrade is visible in the diff.** The
`build.gradle` shows `sourceCompatibility = '21'`, Spring Boot `3.5.x`, and
the `WebSecurityConfig` uses the new lambda DSL. The test count is the same or
higher — no tests were deleted to make the build pass.

**4. For Act 4 (testing), the coverage report improves.** Compare the JaCoCo
HTML report before and after — line coverage moves from the previous level to
≥ 80%. New test files appear in `src/test/java/` following existing naming
conventions.

**5. Devin Review feedback loop.** Each PR gets automated review feedback.
Devin responds to review comments, iterates, and pushes fixes — the same
collaboration model as a human teammate. The PR history shows the
conversation, not a black-box result.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Engineering excellence is a compound effect.** Feature dev, security,
  modernization, and testing are not separate initiatives — they are the same
  team's work, and Devin handles all four with the same verification discipline.
- **Confidence comes from programmatic verification, not review.** The test suite
  caught a silent validation regression that a human reviewer — seeing a clean
  compile and passing smoke test — would have shipped. "Looks reasonable" is not
  a quality gate; `./gradlew clean test` is.
- **Velocity without quality trade-offs.** A major framework upgrade (Java 11 →
  21, Boot 2.6 → 3.5) done layer by layer with continuous verification — not in
  days of manual work, but in a single session with the test suite holding the
  bar.
- **Scope amplification.** One engineer managing feature requests, security
  posture, framework currency, and test coverage simultaneously — not by working
  harder, but by delegating each task type to Devin with the same playbook and
  the same verification bar.
- **Automation closes the loop.** Scheduled sessions handle maintenance
  (dependency freshness, coverage enforcement). Event-driven Automations handle
  incidents (CVE alert → verified fix PR before the team opens Slack). The
  engineer's role shifts from executing to reviewing.
- **Parallel fan-out scales the hardest tasks.** A monolithic upgrade that would
  block one engineer for a sprint becomes four parallel child sessions completing
  in a fraction of the time — each isolated, each verified, each producing its
  own reviewable PR.
