# Oracle Forms → Spring Boot — Modernization Demo

A single linear demo that shows Devin modernizing a legacy **Oracle Forms**
module (the OtterWorks "Storage Billing" back-office) into a verified **Spring
Boot 3 (Java 21) REST service**: orient over the Forms estate, convert one block
live, prove behavioral parity through a contract harness against the OpenAPI
spec, catch a real divergence and fix it, then fan the remaining blocks out in
parallel. Each conversion lands as a PR with a green contract report.

The prompts below invoke the `!modernize-oracle-forms` Devin Playbook — the
reusable conversion procedure — whose source lives in the code repo at
[`otterworks/.workshop/playbooks/modernize-oracle-forms.devin.md`](https://github.com/Cognition-Partner-Workshops/otterworks/blob/main/.workshop/playbooks/modernize-oracle-forms.devin.md).
The repo-specific `make forms-build` / `make forms-verify` mechanics come from
that repo's Skill
(`.agents/skills/oracle-forms-modernization/SKILL.md`), auto-loaded when Devin
works in the repo.

## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Does the Modernization](#part-1)
  - [Act 1 — Orient over the Forms estate](#act-1)
  - [Act 2 — Convert one block live, with verification](#act-2)
  - [Act 3 — Fan out in parallel](#act-3)
  - [Act 4 — Confidence = programmatic verification](#act-4)
- [Part 2 — Run the Produced Artifact](#part-2)
- [Confirming Completion in Spring Boot](#confirm-spring-boot)
- [Concurrent Runs](#concurrent)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Build, run, and verify from the repo root — no Docker or external database
(the default `h2` profile is in-memory):

```bash
make forms-build              # ./gradlew bootJar in oracle-forms/spring-boot-app/
make forms-run                # start the service on http://localhost:8092
make forms-verify             # boot the service + run the contract harness + tear down
make forms-clean              # remove build artifacts
```

Prerequisites: Java 21 (the Gradle wrapper is included), Python 3 with
`oracle-forms/verify/requirements.txt` installed.

---

<a id="repository"></a>
## Repository

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) —
  everything for this demo lives under `oracle-forms/`:
  - `legacy/BILLING.fmb.xml` — the Oracle Forms module export (source of truth):
    blocks, items, LOVs, and `WHEN-VALIDATE-*` / `PRE-INSERT` triggers
  - `legacy/triggers/*.plsql` — the extracted PL/SQL trigger bodies, one per rule
  - `legacy/schema/legacy_schema.sql` — the legacy Oracle DDL
  - `contracts/openapi.yaml` — the REST contract distilled from the Forms definition
  - `spring-boot-app/` — the Spring Boot 3 / Java 21 target (`main` ships an
    unimplemented scaffold)
  - `verify/` — the Python contract-parity harness (pytest, black-box over HTTP)

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Data |
|---|---|---|
| **Before** | `main`: the Forms export + extracted triggers (source of truth), the OpenAPI contract, the Spring Boot scaffold whose `BillingService` methods all throw `NotImplementedException` (HTTP 501), and the contract harness. | Seed plans in `data.sql` (FREE, BASIC, PRO, ENTERPRISE) with their `maxSeats` / `maxDiscountPct` caps — the LOV source and the reference data the cross-field triggers validate against. |
| **After** | a namespace branch (`modernize/<block>`) with `BillingService` implemented — the customer and subscription rules reproduced from the Forms triggers — built live by Devin. | Same seeded plans; the service reads/writes customers and subscriptions in the same shape as the Forms blocks. |

The **before** state is deliberately an unimplemented scaffold: the Spring Boot
project compiles and starts, `/health` responds, but every data endpoint returns
501. What Devin converts **live** is the business logic — the `CUSTOMERS` and
`SUBSCRIPTIONS` blocks — by reading the Forms items and triggers, mapping each to
its REST equivalent, and proving parity with the contract harness.

The verification loop sits between them: every rule is tested against the
OpenAPI contract before it is trusted. The before state is durable on `main`;
the after state lives on a namespace branch — which is what makes this safe to
repeat and safe to run concurrently.

> **On "parity":** there is no running Oracle Forms runtime in this environment,
> so parity means contract verification against the Forms definition as the
> source of truth — block→endpoint existence, item→field validations, LOV/enum
> constraints, and each trigger's business rule (including cross-field ones),
> with the extracted `.plsql` trigger bodies as the behavioral reference.

---

<a id="part-1"></a>
## Part 1 — Devin Does the Modernization

<a id="act-1"></a>
### Act 1 — Orient over the Forms estate

Open the Forms module and ask Devin to explain it. With DeepWiki over the repo,
Devin typically maps an unfamiliar estate in minutes (coverage depends on repo
structure).

```
Using the otterworks repo, give me a map of the legacy Oracle Forms
"Storage Billing" estate under oracle-forms/. Cover the blocks and their
base tables in legacy/BILLING.fmb.xml (CUSTOMERS, SUBSCRIPTIONS), the
items and their properties (Required, Maximum_Length, LOV, ranges), the
LOVs / record groups, and — most importantly — every WHEN-VALIDATE-ITEM,
WHEN-VALIDATE-RECORD, and PRE-INSERT trigger in legacy/triggers/ and the
business rule each one encodes. Produce a rule inventory: one line per
trigger, mapping it to the REST behavior it implies against the contract
in contracts/openapi.yaml.
```

Expected: a tour of the two blocks and their items, the `PLAN_LOV` / `STATUS_LOV`
value sets, and a rule inventory of the triggers — email validation on
`CUSTOMERS.CONTACT_EMAIL`, the plan-code LOV check, the `SEATS` and
`DISCOUNT_PCT` cross-field caps, the `end_date > start_date` record rule, and the
"no subscription for a CLOSED customer" pre-insert rule.

<a id="act-2"></a>
### Act 2 — Convert one block live, with verification

The core beat. Paste the playbook prompt for the subscriptions block. Devin reads
the Forms triggers, implements the `BillingService` logic, builds, runs the
contract harness, catches a divergence, fixes it, and produces a PR with the
verification report.

```
!modernize-oracle-forms

Modernize the SUBSCRIPTIONS block of the legacy Oracle Forms module into
the Spring Boot service, all within the otterworks repo under
oracle-forms/.

- Forms source: oracle-forms/legacy/BILLING.fmb.xml (SUBSCRIPTIONS block)
  and the extracted triggers in oracle-forms/legacy/triggers/
  (SUBSCRIPTIONS-WVI-SEATS, SUBSCRIPTIONS-WVI-DISCOUNT_PCT,
  SUBSCRIPTIONS-WVR, SUBSCRIPTIONS-PRE-INSERT)
- Contract: oracle-forms/contracts/openapi.yaml
- Target: implement the subscription methods in
  oracle-forms/spring-boot-app/.../service/BillingService.java
- Verify with: make forms-verify
- Namespace: modernize/subscriptions-block
```

**The verification beat (the real bug).** `SUBSCRIPTIONS.DISCOUNT_PCT` carries a
`0..100` field range **and** a `WHEN-VALIDATE-ITEM` trigger capping the discount
at the plan's `MAX_DISCOUNT_PCT` — which is `0` for every plan except
`ENTERPRISE`. A plausible conversion maps the field range and stops there, so a
15% discount on a `PRO` plan is accepted. The harness catches the wrong `201`:

```
test_subscription_discount_over_plan_max_400  FAILED
  discount above the plan maximum must be rejected; a field-only 0..100
  range check would return 201. Got 201:
  {"planCode":"PRO","discountPct":15,"status":"ACTIVE",...}
```

Correct the implementation to look up the plan and enforce the plan-dependent
cap, matching the trigger:

```java
if (discount.compareTo(plan.getMaxDiscountPct()) > 0) {
  throw new ValidationException("discountPct", "Discount exceeds plan maximum");
}
```

Re-run, and the harness goes green:

```bash
make forms-verify
#   15 passed
```

The point: "compiles and the screen looks the same" review would have shipped a
rule the business depends on — enterprise-only discounting. The contract harness
caught it because the rule was encoded as an assertion tied to the trigger. The
full write-up is in the playbook at
`.workshop/playbooks/modernize-oracle-forms.devin.md` → *Worked example*.

<a id="act-3"></a>
### Act 3 — Fan out in parallel

The blocks and endpoint groups are independent, so launch a Devin session per
group. Each follows the same playbook and produces its own verified PR — the same
review bar applied many times in parallel instead of once in series.

| Session | Forms source | Spring Boot target |
|---|---|---|
| 1 | `CUSTOMERS` block + `CUSTOMERS-WVI-CONTACT_EMAIL` + `STATUS_LOV` | `createCustomer` / `getCustomer` / `listCustomers` in `BillingService` |
| 2 | `SUBSCRIPTIONS` block triggers (seats, discount, record, pre-insert) | `createSubscription` / `listSubscriptions` (the Act 2 worked example) |
| 3 | `PLAN_RG` record group / `PLAN_LOV` | `listPlans` / `getPlan` (the reference data) |

Each session uses its own namespace branch (`modernize/customers-block`,
`modernize/subscriptions-block`, …) so the parallel builds never collide.

#### Parallelize from a single session (parent → child)

Instead of launching each session by hand, run one **orchestrator** session that
spawns a child Devin session per block and monitors them — one agent fanning
itself out across the wave. Paste:

```
Act as the orchestrator for an Oracle Forms -> Spring Boot modernization
across the blocks of the BILLING module, using child Devin sessions to
parallelize the work.

Repo: Cognition-Partner-Workshops/otterworks, all under oracle-forms/.

Spawn one child Devin session per block group below. Give each child the
repo, its own namespace branch (modernize/customers-block,
modernize/subscriptions-block, modernize/plans), and tell it to follow
the !modernize-oracle-forms playbook (the repo's Skill supplies the
make forms-build / make forms-verify mechanics): treat the Forms module
and its triggers as the source of truth, reproduce each rule exactly,
and build until make forms-verify is fully green.

Block groups:
1. Plans (reference data): PLAN_RG / PLAN_LOV
   -> listPlans, getPlan
2. Customers: CUSTOMERS block + CONTACT_EMAIL trigger + STATUS_LOV
   -> createCustomer, getCustomer, listCustomers
3. Subscriptions: SUBSCRIPTIONS triggers (seats, discount, record,
   pre-insert) -> createSubscription, listSubscriptions

After launching, monitor the child sessions until each block is
converted with a green contract harness. Summarize the results and call
out any contract divergences the children caught (e.g., a plan-dependent
discount cap that a field-only range check would have missed).
```

The children each write to their own namespace branch so the parallel builds
never collide. This is the same verified conversion loop as a single session —
run many times at once, from one parent.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

The gates that make every PR trustworthy:

- **Build** (`make forms-build`): `./gradlew bootJar` compiles the service with
  Java 21 and Spotless / Google Java Format enforced.
- **Contract harness** (`make forms-verify`): boots the service on port 8092 and
  runs the pytest suite in `oracle-forms/verify/` against it — proving
  block→endpoint existence, field validations, LOV/enum constraints, each
  trigger-derived rule (including the cross-field discount and seats caps), and
  the persisted result — all gated by `contracts/openapi.yaml`.
- **Devin Review**: an automated reviewer on every PR.

A conversion is "done" when the contract harness is green on the PR — not when
the code merely compiles.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifact

Show the modernized service running end to end, with a repeatable before/after.

```bash
make forms-run                # start Spring Boot on http://localhost:8092 (H2, in-memory)
```

In a separate terminal, exercise the API and the trigger-derived rules:

```bash
# 1. Health check
curl http://localhost:8092/health
# {"status":"healthy"}

# 2. Plans (the LOV source, with the caps the cross-field triggers read)
curl http://localhost:8092/api/plans

# 3. Create a customer
CID=$(curl -s -X POST http://localhost:8092/api/customers \
  -H "Content-Type: application/json" \
  -d '{"companyName":"Acme Corp","contactEmail":"ops@acme.example"}' \
  | jq -r '.customerId')

# 4. A valid ENTERPRISE subscription with a 15% discount (allowed: max is 20)
curl -X POST http://localhost:8092/api/customers/$CID/subscriptions \
  -H "Content-Type: application/json" \
  -d '{"planCode":"ENTERPRISE","seats":10,"discountPct":15,"startDate":"2026-01-01"}'
# 201 Created

# 5. The cross-field beat: a 15% discount on PRO (max_discount_pct = 0)
curl -w "\n%{http_code}" -X POST http://localhost:8092/api/customers/$CID/subscriptions \
  -H "Content-Type: application/json" \
  -d '{"planCode":"PRO","seats":5,"discountPct":15,"startDate":"2026-01-01"}'
# {"field":"discountPct","message":"Discount exceeds plan maximum"}
# 400

# 6. Bad email is rejected (CONTACT_EMAIL WHEN-VALIDATE-ITEM)
curl -w "\n%{http_code}" -X POST http://localhost:8092/api/customers \
  -H "Content-Type: application/json" \
  -d '{"companyName":"X","contactEmail":"not-an-email"}'
# {"field":"contactEmail","message":"Contact email must be a valid email address"}
# 400
```

Then run the full contract-parity harness:

```bash
make forms-verify
#   15 passed
```

---

<a id="confirm-spring-boot"></a>
## Confirming Completion in Spring Boot

The modernization milestone is complete when four things are true: the Spring
Boot app **builds and starts**, the **contract harness is green** (parity proven
against the OpenAPI contract), the API **enforces the same rules** as the Forms
triggers, and the **before is untouched** next to the after. Walk through in this
order:

**1. The app builds and starts.** `make forms-build` exits green and the service
starts on port 8092 with the seed plans loaded and `/health` responding.

**2. The contract-parity beat — the discount cap matches the trigger.** POST a
15% discount on a `PRO` plan and show the response is
`{"field":"discountPct","message":"Discount exceeds plan maximum"}` with a `400`
— not a `201`. This is the "verification caught a wrong conversion" proof,
visible as data: the Forms `WHEN-VALIDATE-ITEM` trigger caps the discount at the
plan's maximum, and the Spring Boot service now does the same.

**3. The contract harness — every test PASS.** Show the output of
`make forms-verify`: all 15 tests report PASS, each tied to a Forms item, LOV, or
trigger. A green suite is the definition of "done"; the code merely compiling is
not.

**4. The before is untouched.** Show that `main` still has the unimplemented
scaffold — `BillingService` methods throwing `NotImplementedException`, so
`make forms-verify` there is red except `/health`. The converted code lives on
the namespace branch (`modernize/…`), ready for review and merge when the team is
satisfied. A reference implementation is kept on the unmerged
`oracle-forms/answer-key` branch for comparison.

---

<a id="concurrent"></a>
## Concurrent Runs

Each conversion targets its own namespace branch, so multiple runs — and the
parallel fan-out in Act 3 — coexist with no collisions:

```bash
# Run 1 — Alice's session
git checkout -b modernize/alice
# … implement, verify, PR

# Run 2 — Bob's session
git checkout -b modernize/bob
# … implement, verify, PR
```

Each run boots its own in-memory H2 database (seeded from `data.sql`), so
concurrent app instances on different ports never share state or collide.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The value on display is **Devin doing the modernization**: reading an unfamiliar Oracle Forms estate (blocks, items, LOVs, PL/SQL triggers), converting blocks off a reusable playbook, and proving each conversion against the OpenAPI contract — not just a finished artifact to run.
- **Confidence comes from programmatic verification.** The contract harness (endpoint existence, field validations, LOV/enum constraints, trigger-derived rules) gates every build, and the demo shows a real divergence — a plan-dependent discount cap that a field-only `0..100` range check would have missed — being caught and fixed. "Compiles and looks reasonable" review would have shipped it.
- The **Forms definition is the source of truth**: the interesting logic lives in triggers, not column types. Conversions reproduce the trigger behavior faithfully (quirks flagged, not silently "fixed"); redesign is a separate, deliberate decision.
- Conversions are **independent and parallelizable** — multiple Devin sessions convert multiple blocks at once, each producing its own verified PR, orchestrated by a single parent session. The playbook keeps every run consistent.
- Spring Boot replaces the Forms runtime with idiomatic Java: `@RestController` for the block screens, Spring Data JPA for the base-table queries, Java records for DTOs, and `@ControllerAdvice` for the `FND_MESSAGE` error handling. An unimplemented scaffold on `main` plus namespace branches and a one-command revert make the story safe to repeat.
