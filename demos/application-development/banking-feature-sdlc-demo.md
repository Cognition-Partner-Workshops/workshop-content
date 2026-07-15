# Brownfield Banking Feature, End-to-End — Full-SDLC Demo

A single linear demo that shows Devin delivering a **new feature into an existing
banking microservice** across the entire SDLC — requirements, technical design,
implementation, tests, and quality gates — landing as a review-ready PR. Devin
orients over an unfamiliar brownfield service, writes the spec and design, builds
the feature, catches a real boundary bug with a test, fixes it against the
acceptance criteria, then fans additional statement features out in parallel.
Each unit of work lands as a PR gated by a green test run.

The prompts below invoke the `!deliver-banking-feature-sdlc` Devin Playbook — the
reusable full-SDLC procedure — whose source lives in the code repo at
[`ts-java-spring-boot-internet-banking/.workshop/playbooks/deliver-banking-feature-sdlc.devin.md`](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking/blob/main/.workshop/playbooks/deliver-banking-feature-sdlc.devin.md).
The repo-specific `./gradlew test` mechanics and package conventions come from
that repo's Skill (`.agents/skills/banking-feature-sdlc/SKILL.md`).

## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Runs the SDLC](#part-1)
  - [Act 1 — Orient over the banking service](#act-1)
  - [Act 2 — Deliver one feature live, with verification](#act-2)
  - [Act 3 — Fan out statement features in parallel](#act-3)
  - [Act 4 — Confidence = programmatic verification](#act-4)
- [Part 2 — Run the Produced Artifact](#part-2)
- [Confirming Completion](#confirming-completion)
- [Where This Goes Next](#going-next)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Build and run the verification gate from the service module:

```bash
cd core-banking-service
./gradlew test            # the verification gate — must be green
./gradlew build           # compile + test + assemble
```

Prerequisites: Java 21 and the Gradle wrapper included in the repo. Tests run on
H2, so no external database is required for the verification loop.

---

<a id="repository"></a>
## Repository

- [ts-java-spring-boot-internet-banking](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking) —
  a Java 21 / Spring Boot 3.2.x internet-banking system: a service registry,
  config server, API gateway, and domain services (core banking, user, fund
  transfer, utility payment). The feature work lands in the **`core-banking-service`**
  module, which owns accounts and transactions (`TransactionController`,
  `TransactionService`, `TransactionRepository`, `TransactionEntity` mapped to the
  `banking_core_transaction` table) and carries the playbook source and Skill.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Data |
|---|---|---|
| **Before** | `main`: the banking services build and test green, with `POST` transaction endpoints (fund transfer, utility payment) but **no** account-statement / transaction-history read API. The playbook source and Skill live here. | Flyway-seeded `banking_core_account` and `banking_core_transaction` tables; H2 for tests |
| **After** | a PR branch with the new statement feature — migration, entity change, DTO, repository query, service method, endpoint, and tests — built live by Devin | Same schema plus one additive migration column; the before is untouched |

The **before** state is a real brownfield service: it compiles and runs, exposes
transaction *writes*, but has no way to read an account's transaction history.
What Devin delivers **live** is the full lifecycle for that feature — spec →
design → implementation → tests → green gate — not just a code snippet.

The verification loop sits at the center: the feature is trusted only when
`./gradlew test` is green with tests that assert the contract. The before state
is durable; the after lives on a feature branch, which is what makes this safe to
repeat and safe to run concurrently.

> **On "done":** a feature is done when the test suite is green with tests that
> assert the new behavior — ordering, filtering, pagination, and boundaries — not
> when the code merely compiles.

---

<a id="part-1"></a>
## Part 1 — Devin Runs the SDLC

<a id="act-1"></a>
### Act 1 — Orient over the banking service

Open the repo and ask Devin to map the transaction area. With DeepWiki over the
repo, Devin typically maps an unfamiliar service in minutes (coverage depends on
repo structure).

```
Using the ts-java-spring-boot-internet-banking repo, map the
transaction area of the core-banking-service module: the
TransactionController (/api/v1/transaction), TransactionService,
TransactionRepository, and the TransactionEntity mapped to the
banking_core_transaction table. Explain how a transaction links to
an account (BankAccountEntity.number), what the existing endpoints
do, and how the Flyway migrations under
src/main/resources/db/migration build the schema.
```

Expected: a tour of the write-side transaction flow — `fundTransfer` and
`utilPayment` on the service, the `@OneToOne` link from `TransactionEntity` to
`BankAccountEntity`, the `banking_core_transaction` schema, and the JUnit 5 +
Mockito test style in `TransactionServiceTest`. Devin confirms there is no
read/history endpoint today.

<a id="act-2"></a>
### Act 2 — Deliver one feature live, with verification

The core beat. Paste the playbook prompt. Devin writes the spec and design, then
implements the feature, adds tests, runs the gate, catches a divergence, fixes
it, and produces a PR that walks the lifecycle.

```
!deliver-banking-feature-sdlc

Deliver an "Account Statement & Transaction History" feature
end-to-end in the core-banking-service module of
ts-java-spring-boot-internet-banking. Work entirely inside
core-banking-service and follow existing patterns.

Requirements (SPEC.md at the module root): a read endpoint
GET /api/v1/account/{accountNumber}/transactions returning an
account's transactions most-recent-first, with inclusive date-range
filtering (from/to), optional transaction-type filtering, and
pagination. Include acceptance criteria and edge cases (empty
result, range boundaries).

Design (DESIGN.md): an additive Flyway migration adding a
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP column to
banking_core_transaction (new migration file; update
TransactionEntity); a TransactionHistoryDto (id, amount, type,
reference number, timestamp); a repository query returning an
account's transactions newest-first; a service method mapping
entities to the DTO with date-range and type filtering; and the
controller endpoint with @Operation and @Tag matching existing
style.

Tests: follow TransactionServiceTest — cover the happy path, an
empty result, most-recent-first ordering, and a date-range
boundary case. Run ./gradlew test for core-banking-service and get
it green.
```

**The verification beat (the real bug).** A plausible first cut filters the date
range with strictly exclusive bounds — `timestamp.isAfter(from) &&
timestamp.isBefore(to)`. It compiles and the happy path passes. The boundary test
fails:

```
TransactionServiceTest > getTransactionHistory_includesBoundaryTransactions FAILED
  org.opentest4j.AssertionFailedError:
  Expected size: 3 but was: 1
  A transaction whose timestamp equals the range end was dropped.
```

The acceptance criteria say the range is **inclusive** on both ends. The fix is
inclusive comparison (Spring Data `Between` is inclusive, or `!isAfter` /
`!isBefore`) — not relaxing the test. Re-run and the suite goes green:

```bash
./gradlew test
#   TransactionServiceTest — all tests PASSED
#   BUILD SUCCESSFUL
```

The point: a "compiles and looks reasonable" review would have shipped a
statement that silently omits the last transaction of a period — exactly the kind
of defect a banking reconciliation surfaces in production. The boundary test
caught it before the PR was opened. The full write-up is in the playbook at
`.workshop/playbooks/deliver-banking-feature-sdlc.devin.md` → *Worked example*.

<a id="act-3"></a>
### Act 3 — Fan out statement features in parallel

The remaining statement capabilities are independent, so launch a Devin session
per feature. Each follows the same playbook and produces its own PR gated by a
green test run — the same review bar applied many times in parallel instead of
once in series.

| Session | Feature | Primary symbols |
|---|---|---|
| 1 | Transaction history endpoint (the Act 2 worked example) | `TransactionController` + `TransactionService` + `TransactionRepository` |
| 2 | Monthly statement summary (totals by type for a period) | `TransactionService` aggregation + DTO |
| 3 | Statement export DTO (CSV/JSON projection) | `TransactionHistoryDto` + mapper |
| 4 | Balance-as-of-date query | `TransactionRepository` query + service method |

Each session uses its own feature branch (`feature/statement-history`,
`feature/statement-summary`, …) so the parallel builds never collide.

#### Parallelize from a single session (parent → child)

Instead of launching each by hand, run one **orchestrator** session that spawns a
child Devin session per feature and monitors them — one agent fanning itself out
across the wave. Paste:

```
Act as the orchestrator for an account-statement feature wave in
the core-banking-service module of
Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking,
using child Devin sessions to parallelize the work.

Spawn one child Devin session per feature below. Give each child
the repo, its own feature branch (feature/child1, child2, ...),
and tell it to follow the !deliver-banking-feature-sdlc playbook
(the repo's Skill supplies the ./gradlew test mechanics and
package conventions): write SPEC.md and DESIGN.md, implement
against existing patterns, add tests that assert the contract, and
build until ./gradlew test is green.

Features:
1. Transaction history endpoint
   GET /api/v1/account/{accountNumber}/transactions
2. Monthly statement summary (totals by transaction type for a
   period)
3. Statement export DTO (CSV/JSON projection of history)
4. Balance-as-of-date query

After launching, monitor the child sessions until each feature is
delivered with a green test run. Summarize the results and call
out any boundary or ordering divergences the children caught.
```

The children inherit the organization's scoped credentials, and each writes to
its own feature branch so the parallel builds never collide — the same verified
SDLC loop as a single session, run many times at once from one parent.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

The gates that make every PR trustworthy:

- **Build** (`./gradlew build` in `core-banking-service`): compiles the module and
  assembles the artifact.
- **Tests** (`./gradlew test`): JUnit 5 + Mockito service tests on H2 asserting the
  contract — most-recent-first ordering, inclusive date-range boundaries,
  transaction-type filtering, empty result, and pagination.
- **Devin Review**: an automated reviewer on every PR, which also helps humans
  digest the diff and flags issues proactively.

A feature is "done" when the test suite is green on the PR — not when the code
merely compiles.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifact

Show the feature working end to end against the running service.

```bash
cd core-banking-service
./gradlew bootRun         # start core-banking-service
```

In a separate terminal, exercise the new statement endpoint:

```bash
# Full history, most-recent-first
curl "http://localhost:8081/api/v1/account/0002200005500001/transactions"

# Inclusive date-range + type filter, paginated
curl "http://localhost:8081/api/v1/account/0002200005500001/transactions?\
from=2021-01-01T00:00:00&to=2021-12-31T23:59:59&type=FUND_TRANSFER&page=0&size=20"

# Empty result for an account with no transactions in range
curl "http://localhost:8081/api/v1/account/0002200005500001/transactions?\
from=1990-01-01T00:00:00&to=1990-01-02T00:00:00"
```

> Confirm the module's server port and a seeded account number from the repo's
> Flyway data before running live; the values above are illustrative.

Then re-run the full gate to show the contract holds:

```bash
./gradlew test
#   BUILD SUCCESSFUL — all tests PASSED
```

---

<a id="confirming-completion"></a>
## Confirming Completion

The milestone is complete when four things are true. Walk through in this order:

**1. The spec and design exist and match the code.** Open `SPEC.md` and
`DESIGN.md` at the module root — the endpoint contract, filters, ordering,
pagination, and the schema change described there are exactly what shipped. The
SDLC ran, not just codegen.

**2. The boundary beat — the inclusive range holds.** Show the
`getTransactionHistory_includesBoundaryTransactions` test passing, and hit the
endpoint with a `to` equal to a known transaction's timestamp to show that
transaction is present, not dropped. This is the "verification caught a wrong
implementation" proof, visible as data.

**3. The gate is green.** Show `./gradlew test` output: `BUILD SUCCESSFUL` with the
new tests (happy path, empty result, ordering, boundary) passing. A green suite
is the definition of done; compiling is not.

**4. The before is untouched.** Show that `main` still has only the write-side
transaction endpoints — no history API — while the feature lives on the branch,
ready for review and merge when the team is satisfied.

---

<a id="going-next"></a>
## Where This Goes Next

The same SDLC loop extends into real delivery workflows:

- **Devin Automations** — a ticket tagged for implementation, or a failed CI check
  on the branch, can start a session automatically that runs the playbook and
  pushes a fix. See [Automations](https://docs.devin.ai/product-guides/automations).
- **Scheduled sessions** — run recurring quality-of-life work on a cadence
  (nightly test runs, weekly coverage-gap sweeps over the transaction area).
- **Child-session fan-out** — the Act 3 orchestrator scales a feature backlog
  across many parallel sessions, each with its own branch and green gate.
- **Knowledge + playbooks** — the `!deliver-banking-feature-sdlc` playbook and repo
  Skill encode the methodology so every future session (and every child) inherits
  the same lifecycle and verification bar.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The value on display is **Devin orchestrating the entire SDLC** — reading an
  unfamiliar brownfield banking service, writing the spec and design, implementing
  the feature, and gating it with tests — not just producing code. Requirements
  and technical design are the real bottlenecks, and Devin runs them too.
- **Confidence comes from programmatic verification.** The test suite gates every
  build, and the demo shows a real divergence (an exclusive date-range boundary
  dropping the last transaction of a period) being caught and fixed against the
  acceptance criteria. "Compiles and looks reasonable" review would have missed it.
- **The acceptance criteria are the source of truth**: the fix corrects the code to
  match the inclusive-range contract, never the test.
- Features are **independent and parallelizable** — multiple Devin sessions deliver
  multiple statement capabilities at once, each on its own branch with its own
  green gate and PR. The playbook keeps every run consistent.
- Testing, compliance, and governance stay **first-class**: changes ship with a
  spec, a design, tests run on Devin's VM, and a PR that walks the lifecycle for
  review — higher throughput without giving up quality or auditability.
