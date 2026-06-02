# MuleSoft → Spring Boot — API Migration Demo

A single linear demo that shows Devin migrating a MuleSoft Mule 4 Employee
Service API to a verified Spring Boot 3.x implementation: orient over the
MuleSoft estate, convert one endpoint live, prove API parity through contract
tests against the OpenAPI spec, catch a real divergence and fix it, then fan the
remaining endpoints out in parallel. Each conversion lands as a PR with green
contract verification.

The prompts below invoke the `!convert-mulesoft-to-spring-boot` Devin Playbook —
the reusable conversion procedure — whose source lives in the code repo at
[`uc-api-migration-mulesoft-to-spring-boot/.workshop/playbooks/convert-mulesoft-to-spring-boot.devin.md`](https://github.com/Cognition-Partner-Workshops/uc-api-migration-mulesoft-to-spring-boot/blob/main/.workshop/playbooks/convert-mulesoft-to-spring-boot.devin.md).
The repo-specific `make build` / `make verify` mechanics come from that repo's
Skill (`.agents/skills/mulesoft-to-spring-boot-migration/SKILL.md`).

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Does the Migration](#part-1)
  - [Act 1 — Orient over the MuleSoft estate](#act-1)
  - [Act 2 — Convert one endpoint live, with verification](#act-2)
  - [Act 3 — Fan out in parallel](#act-3)
  - [Act 4 — Confidence = programmatic verification](#act-4)
- [Part 2 — Run the Produced Artifact](#part-2)
- [Confirming Completion in Spring Boot](#confirm-spring-boot)
- [Concurrent Runs](#concurrent)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Start the database, then build and run contract tests from the repo root:

```bash
make db-up                    # PostgreSQL via Docker Compose
make build                    # ./mvnw verify in spring-boot-app/
make verify                   # contract tests against running app
make db-down                  # tear down
```

Prerequisites: Java 21, Maven (wrapper included), Docker, and a local
PostgreSQL (provided by `docker/docker-compose.yml`).

---

<a id="repositories"></a>
## Repositories

- [ts-java-mulesoft-employee-api](https://github.com/Cognition-Partner-Workshops/ts-java-mulesoft-employee-api) — the MuleSoft Mule 4 source estate: `employee-services-api.xml` (Mule XML flows for OAuth2, employee goals, learning, pay date, PTO), the RAML spec (`employee-services-api.raml`), PostgreSQL database integration, and CloudHub 2.0 deployment config. Read-only reference for the "before".
- [uc-api-migration-mulesoft-to-spring-boot](https://github.com/Cognition-Partner-Workshops/uc-api-migration-mulesoft-to-spring-boot) — the Spring Boot 3.5 target: project scaffold (`spring-boot-app/`), OpenAPI contract (`contracts/openapi.yaml`), REST Assured contract verification harness (`verify/`), Docker Compose for PostgreSQL (`docker/`), the conversion playbook source (`.workshop/playbooks/`), and the repo Skill (`.agents/skills/`).

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Data |
|---|---|---|
| **Before** | `main`: the Spring Boot scaffold (empty controllers), the OpenAPI contract (source of truth, extracted from RAML), the contract test harness, Docker Compose, the playbook source, and the Skill. The MuleSoft estate lives in `ts-java-mulesoft-employee-api`. | `employee_db`: seeded tables (`api_clients`, `employee_goals`, `employee_learning`, `employee_pto`) via Flyway migration |
| **After** | a PR branch with the Spring Boot controllers, services, repositories, and DTOs implementing the full API surface — built live by Devin | Same database; the app reads and writes using the same schema as the MuleSoft original |

The **before** state is deliberately an empty scaffold: the Spring Boot project
compiles and starts, but no endpoints are implemented. What Devin converts
**live** is the full API — the OAuth2 flow and all five employee endpoints — by
reading the MuleSoft XML flows, mapping each construct to its Spring Boot
equivalent, and proving parity through contract tests.

The verification loop sits between them: every converted endpoint is tested
against the OpenAPI spec before it is trusted. The before state is durable; the
after state lives on a namespace branch (`migration/<name>`) — which is what
makes this safe to repeat and safe to run concurrently.

> **On "parity":** there is no live MuleSoft runtime in this environment, so
> parity means contract verification against the RAML/OpenAPI spec as the source
> of truth — endpoint existence, request/response schemas, HTTP status codes,
> authentication flow — plus the MuleSoft XML flows as behavioral reference for
> edge cases (error messages, query logic).

---

<a id="part-1"></a>
## Part 1 — Devin Does the Migration

<a id="act-1"></a>
### Act 1 — Orient over the MuleSoft estate

Open the MuleSoft estate and ask Devin to explain it. With DeepWiki over the
repo, Devin typically maps an unfamiliar estate in minutes (coverage depends on
repo structure).

```
Using the ts-java-mulesoft-employee-api repo, give me a map of the
MuleSoft API estate: the Mule XML flows in
src/main/mule/employee-services-api.xml, what each flow does (OAuth,
employee goals, learning, pay date, PTO), the RAML spec at
src/main/resources/api/employee-services-api.raml, the database tables
(api_clients, employee_goals, employee_learning, employee_pto), and how
the authentication flow works (client credentials → token validation →
protected endpoints).
```

Expected: a tour of the Mule XML flows — `oauth-token-flow`,
`validate-token-subflow`, the APIKit-routed employee endpoints — the RAML
contract, the `<db:select>` queries, the DataWeave transforms, and the
`<choice>` error handlers. Devin identifies the six API operations and their
database dependencies.

<a id="act-2"></a>
### Act 2 — Convert one endpoint live, with verification

The core beat. Paste the playbook prompt for the goals endpoint. Devin reads the
MuleSoft XML, writes the Spring Boot controller + service + repository + DTO,
builds, runs the contract tests, catches a divergence, fixes it, and produces a
PR with the verification report.

```
!convert-mulesoft-to-spring-boot

Convert the employee goals endpoint from the MuleSoft estate in
ts-java-mulesoft-employee-api into the Spring Boot target in
uc-api-migration-mulesoft-to-spring-boot.

- MuleSoft source: src/main/mule/employee-services-api.xml
  (get:\employee\{employeeId}\goals flow + related subflows)
- RAML spec: src/main/resources/api/employee-services-api.raml
- Target: spring-boot-app/ in uc-api-migration-mulesoft-to-spring-boot
- Namespace: migration/emp-goals
```

**The verification beat (the real bug).** The MuleSoft `<choice>` error handler
returns a JSON body `{"message": "No goals found for employee 74"}` on 404. A
plausible-looking conversion uses Spring's `ResponseEntity.notFound().build()` —
which returns an *empty* 404 body. The contract test catches the mismatch:

```
ContractVerificationIT > GoalsEndpoint > shouldReturn404WithMessageForUnknownEmployee
  FAILED
  Expected: body containing "message"
  Actual:   empty response body
```

Correct the implementation to return the `ErrorResponse` DTO with the message
field populated, matching the contract. Re-run, and the tests go green:

```bash
make verify
#   ContractVerificationIT — 8 tests PASSED
```

The point: "compiles and looks reasonable" review would have shipped an empty 404
body; the contract test against the OpenAPI spec caught it. The full write-up is
in the playbook at `.workshop/playbooks/convert-mulesoft-to-spring-boot.devin.md`
→ *Worked example*.

<a id="act-3"></a>
### Act 3 — Fan out in parallel

The remaining endpoints are independent, so launch a Devin session per endpoint.
Each follows the same playbook and produces its own verified PR — the same review
bar applied many times in parallel instead of once in series.

| Session | MuleSoft flow | Spring Boot target |
|---|---|---|
| 1 | `oauth-token-flow` + `validate-token-subflow` | `OAuthController` + `TokenService` (the Act 2 prerequisite) |
| 2 | `get:\employee\{employeeId}\goals` | `GoalsController` + `GoalService` (the Act 2 worked example) |
| 3 | `get:\employee\{employeeId}\learning-status` | `LearningController` + `LearningService` |
| 4 | `get:\employee\{employeeId}\next-pay-date` | `PayDateController` + `PayDateService` |
| 5 | `get:\employee\{employeeId}\pto\balance` + `post:\employee\{employeeId}\pto\schedule` | `PtoController` + `PtoService` |

Each session uses its own namespace branch (`migration/oauth`, `migration/goals`,
…) so the parallel builds never collide.

#### Parallelize from a single session (parent → child)

Instead of launching each session by hand, run one **orchestrator** session that
spawns a child Devin session per endpoint group and monitors them — one agent
fanning itself out across the wave. Paste:

```
Act as the orchestrator for a MuleSoft-to-Spring-Boot migration across
multiple endpoints, using child Devin sessions to parallelize the work.

Repos: read Cognition-Partner-Workshops/ts-java-mulesoft-employee-api
(the MuleSoft source), write
Cognition-Partner-Workshops/uc-api-migration-mulesoft-to-spring-boot.

Spawn one child Devin session per endpoint group below. Give each child
both repos, its own namespace branch (migration/child1, child2, ...),
and tell it to follow the !convert-mulesoft-to-spring-boot playbook
(the repo's Skill supplies the make build / make verify mechanics):
treat the MuleSoft XML and RAML as the source of truth, reproduce the
API behavior exactly, add contract tests proving parity, and build
until everything is green.

Endpoint groups:
1. OAuth2 flow: oauth-token-flow + validate-token-subflow
   -> OAuthController + TokenService
2. Employee goals: get:\employee\{employeeId}\goals
   -> GoalsController + GoalService
3. Learning status: get:\employee\{employeeId}\learning-status
   -> LearningController + LearningService
4. Pay date: get:\employee\{employeeId}\next-pay-date
   -> PayDateController + PayDateService
5. PTO: balance GET + schedule POST
   -> PtoController + PtoService

After launching, monitor the child sessions until each endpoint group
is converted with green contract tests. Summarize the results and call
out any contract divergences the children caught (e.g., a 404 response
format that did not match the MuleSoft source).
```

The children inherit the organization's database credentials, and each writes to
its own namespace branch so the parallel builds never collide. This is the same
verified conversion loop as a single session — run many times at once, from one
parent.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

The gates that make every PR trustworthy:

- **Build** (`./mvnw verify` in `spring-boot-app/`): compiles, runs unit tests
  against H2, validates the Spring Boot app starts cleanly.
- **Contract tests** (`./mvnw verify` in `verify/`): REST Assured integration
  tests against the running app + PostgreSQL, proving endpoint existence,
  request/response schema parity, correct HTTP status codes, and OAuth2 flow
  correctness — all gated by `contracts/openapi.yaml`.
- **Devin Review**: an automated reviewer on every PR.

A conversion is "done" when the contract tests are green on the PR — not when
the code merely compiles.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifact

Show the converted API running end to end, with a repeatable before/after.

```bash
make db-up                    # start PostgreSQL (seeds tables via Flyway)
make run                      # start Spring Boot app on port 8080
```

In a separate terminal, exercise the full API lifecycle:

```bash
# 1. Health check
curl http://localhost:8080/health
# {"status":"healthy","database":"connected"}

# 2. Obtain OAuth token
TOKEN=$(curl -s -X POST http://localhost:8080/oauth/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials&client_id=demo-client&client_secret=demo-secret" \
  | jq -r '.access_token')

# 3. Employee goals
curl -H "Authorization: Bearer $TOKEN" http://localhost:8080/api/employee/EMP001/goals

# 4. Learning status
curl -H "Authorization: Bearer $TOKEN" http://localhost:8080/api/employee/EMP001/learning-status

# 5. Next pay date
curl -H "Authorization: Bearer $TOKEN" http://localhost:8080/api/employee/EMP001/next-pay-date

# 6. PTO balance
curl -H "Authorization: Bearer $TOKEN" http://localhost:8080/api/employee/EMP001/pto/balance

# 7. Schedule PTO
curl -X POST -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"startDate":"2025-10-15","endDate":"2025-10-19","hours":40}' \
  http://localhost:8080/api/employee/EMP001/pto/schedule

# 8. Verify 401 without token
curl -w "\n%{http_code}" http://localhost:8080/api/employee/EMP001/goals
# 401

# 9. Verify 404 with message body
curl -H "Authorization: Bearer $TOKEN" http://localhost:8080/api/employee/UNKNOWN/goals
# {"message": "No goals found for employee UNKNOWN"}
```

Then run the full contract verification suite:

```bash
make verify
#   ContractVerificationIT — 8 nested test classes, all PASS
```

Tear down:

```bash
make db-down
```

---

<a id="confirm-spring-boot"></a>
## Confirming Completion in Spring Boot

The migration milestone is complete when four things are true: the Spring Boot
app **builds and starts**, the **contract tests are green** (parity proven
against the OpenAPI spec), the API **responds identically** to the MuleSoft
original, and the **before is untouched** next to the after. Walk through in
this order:

**1. The app builds and starts.** `./mvnw verify` in `spring-boot-app/` exits
green. The app starts on port 8080 with Flyway migrations applied and the
Actuator health endpoint responding.

**2. The contract-parity beat — the 404 body matches MuleSoft.** Hit
`/api/employee/UNKNOWN/goals` with a valid token and show the response is
`{"message": "No goals found for employee UNKNOWN"}` — not an empty body. This
is the "verification caught a wrong conversion" proof, visible as data: the
MuleSoft `<choice>` handler returns a JSON error body, and the Spring Boot
`@ControllerAdvice` now does the same.

**3. The contract test suite — every test PASS.** Show the output of
`make verify`: all 8 nested test classes (HealthEndpoint, OAuthEndpoint,
GoalsEndpoint, LearningEndpoint, PayDateEndpoint, PtoBalanceEndpoint,
PtoScheduleEndpoint, SpecCompleteness) report PASS. A green suite is the
definition of "done"; the code merely compiling is not.

**4. The before is untouched.** Show that `main` still has the empty scaffold —
no controllers implemented. The converted code lives on the namespace branch
(`migration/…`), ready for review and merge when the team is satisfied. The
MuleSoft source repo is completely unmodified.

---

<a id="concurrent"></a>
## Concurrent Runs

Each conversion targets its own namespace branch, so multiple runs — and the
parallel fan-out in Act 3 — coexist with no collisions:

```bash
# Run 1 — Alice's session
git checkout -b migration/alice
# … convert, test, PR

# Run 2 — Bob's session
git checkout -b migration/bob
# … convert, test, PR
```

The database schema is shared (seeded by Flyway), and reads are idempotent, so
concurrent app instances on different ports do not conflict.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The value on display is **Devin doing the migration**: reading an unfamiliar MuleSoft estate (XML flows, RAML spec, DataWeave transforms), converting endpoints off a reusable playbook, and proving each conversion against the OpenAPI contract — not just a finished artifact to run.
- **Confidence comes from programmatic verification.** Contract tests (endpoint existence, schema parity, status codes, auth flow) gate every build, and the demo shows a real divergence (empty 404 body vs. the source's JSON error message) being caught and fixed. "Compiles and looks reasonable" review would have missed it.
- The **RAML/OpenAPI contract is the source of truth**: conversions reproduce MuleSoft behavior faithfully (quirks flagged, not silently "fixed"); API redesign is a separate, deliberate decision.
- Conversions are **independent and parallelizable** — multiple Devin sessions convert multiple endpoint groups at once, each producing its own verified PR. The playbook keeps every run consistent.
- Spring Boot replaces MuleSoft's Mule XML flows with idiomatic Java: `@RestController` for HTTP Listeners, Spring Data JPA for `<db:select>`, Jackson DTOs for DataWeave transforms, `@ControllerAdvice` for error handlers. **Docker Compose + Flyway** replace CloudHub deployment, and namespace branches + one-command revert make the story safe to repeat.
