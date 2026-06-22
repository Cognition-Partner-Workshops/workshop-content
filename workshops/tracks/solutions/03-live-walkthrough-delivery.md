# 3. Live Walkthrough Delivery

*Reference: [`demos/security/general.md`](../../../demos/security/general.md), [`demos/data-engineering/sas-to-databricks-demo.md`](../../../demos/data-engineering/sas-to-databricks-demo.md), [`demos/migration/mulesoft-to-spring-boot-demo.md`](../../../demos/migration/mulesoft-to-spring-boot-demo.md)*

This section teaches you how to run each walkthrough type end-to-end in a simulated environment. For each: the setup steps, the prompts to run, what to explain while it runs, and how to handle if things go off-script.

## 3.1 Security Remediation — Scan → Fix → Rescan Closed Loop

**What you are showing:** Devin as a background security agent that responds to scan findings, remediates vulnerabilities, and re-scans to prove the fix — in a closed loop.

### Setup Steps

1. Ensure the `Cognition-Partner-Workshops/otterworks` repo is accessible in your Devin org
2. Verify a Trivy scan workflow exists (or use the prompt below to create one)
3. Pre-configure a Devin Automation to fire on security scan failures (optional — you can create it live)

### Prompts to Run

**Create the scanner workflow:**

```
Create a GitHub Actions workflow called security-scan.yml
on the Cognition-Partner-Workshops/otterworks repo that:

1. Triggers on pull_request events (opened, synchronize).
2. Runs a Trivy scan targeting HIGH and CRITICAL severity
   findings.
3. Reports results as a GitHub check run.
4. Posts a PR comment summarizing findings by service
   directory.
```

**Create the automation trigger:**

```
When a security scan check run fails on
Cognition-Partner-Workshops/otterworks, start a Devin
session that:

1. Reads the scan findings attached to the check run.
2. Triages HIGH and CRITICAL findings by service directory.
3. Applies fixes — dependency upgrades or code changes —
   in the correct manifest or source file.
4. Runs each affected service's tests.
5. Pushes the fix to the same branch.

Cap at 2 invocations per PR. Set an ACU limit of 50 per
session.
```

### What to Explain While It Runs

- **Before launching:** Explain the closed-loop pattern — scanner detects → automation triggers → Devin remediates → push triggers re-scan → converges to green
- **While Devin scans:** Point out that Devin is reading the scan output, identifying the correct manifest files per language/package manager, and prioritizing by severity
- **While Devin fixes:** Explain that Devin runs affected service tests before pushing — local verification before CI verification
- **When CI re-runs:** Highlight the converging loop — each iteration reduces findings. When findings reach zero, CI goes green automatically
- **Safeguards:** Mention invocation limits (prevents runaway loops), ACU caps (controls cost), and escalation policy (after N attempts, opens an issue for human review)

### If It Goes Off-Script

| Scenario | Recovery |
|----------|----------|
| Scanner finds no vulnerabilities | Explain this is the "green" steady state. Switch to the backlog burn-down prompt to show remediation on existing findings |
| Devin picks wrong manifest file | Leave a PR comment: "The vulnerable dependency is in `services/auth/package.json`, not the root" — show the PR feedback loop |
| Fix breaks tests | This is a feature, not a bug — explain that Devin will iterate (read failure logs, push fix, re-run). Monitor the session timeline |
| Automation does not fire | Check the trigger conditions in the Automations UI. Common issue: the check run name does not match the filter. Adjust and re-trigger |

---

## 3.2 Migration — Language/Framework Translation with Parity Verification

**What you are showing:** Devin reading an unfamiliar legacy estate, converting code to a modern target, and proving parity through programmatic verification — not just "it compiles."

### Setup Steps

1. Ensure both repos are accessible: `ts-java-mulesoft-employee-api` (source) and `uc-api-migration-mulesoft-to-spring-boot` (target)
2. Verify the target repo's contract test harness works: `make db-up && make build && make db-down`
3. Familiarize yourself with the "real bug" — the 404 empty body divergence (see walkthrough below)

### Prompts to Run

**Orient over the legacy estate:**

```
Using the ts-java-mulesoft-employee-api repo, give me a map
of the MuleSoft API estate: the Mule XML flows in
src/main/mule/employee-services-api.xml, what each flow does
(OAuth, employee goals, learning, pay date, PTO), the RAML
spec at src/main/resources/api/employee-services-api.raml,
the database tables (api_clients, employee_goals,
employee_learning, employee_pto), and how the authentication
flow works (client credentials → token validation →
protected endpoints).
```

**Convert one endpoint with playbook verification:**

```
!convert-mulesoft-to-spring-boot

Convert the employee goals endpoint from the MuleSoft estate
in ts-java-mulesoft-employee-api into the Spring Boot target
in uc-api-migration-mulesoft-to-spring-boot.

- MuleSoft source: src/main/mule/employee-services-api.xml
  (get:\employee\{employeeId}\goals flow + related subflows)
- RAML spec:
  src/main/resources/api/employee-services-api.raml
- Target: spring-boot-app/ in
  uc-api-migration-mulesoft-to-spring-boot
- Namespace: migration/emp-goals
```

### What to Explain While It Runs

- **Before launching:** Explain the "before" state — an empty Spring Boot scaffold with a contract test harness and OpenAPI spec, ready for Devin to fill in
- **During orientation (Act 1):** Point out that Devin is reading XML flows, RAML specs, and DataWeave transforms it has never seen before — mapping the estate using DeepWiki context and direct file retrieval
- **During conversion (Act 2):** Explain the pattern mapping: Mule HTTP Listeners → `@RestController`, `<db:select>` → Spring Data JPA, DataWeave → Jackson DTOs, `<choice>` error handlers → `@ControllerAdvice`
- **When verification catches the bug:** This is the key beat. The contract test fails because the MuleSoft `<choice>` handler returns a JSON error body on 404, but a plausible-looking conversion uses `ResponseEntity.notFound().build()` (empty body). Explain: "Compiles and looks reasonable" review would have shipped this bug. The contract test against the spec caught it
- **After the fix:** Show the green test suite. Emphasize: a conversion is "done" when contract tests are green, not when the code compiles

### If It Goes Off-Script

| Scenario | Recovery |
|----------|----------|
| Devin does not hit the 404 bug | This can happen if Devin reads the contract test expectations first. Explain the verification model is still working — the tests passed because Devin got it right. Reference the worked example from the playbook documentation |
| Database connection fails | Leave a PR comment: "Run `make db-up` first — the PostgreSQL container needs to be running for contract tests." This shows real-world iteration |
| Devin converts multiple endpoints at once | Good — show the parallel conversion value. Point out that each endpoint's tests pass independently |
| Compilation errors in generated code | Explain that Devin will iterate — this is local testing catching issues before CI. Watch the session timeline for the fix cycle |

---

## 3.3 Data Engineering — ETL Translation with Reconciliation

**What you are showing:** Devin migrating a SAS analytics estate to dbt/Databricks with programmatic reconciliation — proving source-target parity through control totals, not just "it runs."

### Setup Steps

1. Ensure both repos are accessible: `ts-sas-legacy-analytics` (source) and `uc-data-migration-sas-to-databricks` (target)
2. Verify Databricks workspace credentials are configured as org secrets (`DATABRICKS_HOST`, `DATABRICKS_TOKEN`, `DATABRICKS_HTTP_PATH`)
3. Confirm the reconciliation harness works: `make seed && make demo-up NS=dev && make reconcile NS=dev && make demo-down NS=dev`
4. Familiarize yourself with the "real bug" — the LOC risk-weight divergence (0.75 vs. source's 1.00)

### Prompts to Run

**Orient over the SAS estate:**

```
Using the ts-sas-legacy-analytics repo, give me a map of
the SAS estate: the banking and insurance programs, what
each one reads and writes, the LIBNAMEs, the macros and
PROC FORMATs they depend on, and which programs are
set-based (good for dbt) vs procedural/multi-output
(better as PySpark).
```

**Convert one program with reconciliation verification:**

```
!convert-sas-to-databricks

Convert the SAS program
Programs/Banking/monthly_regulatory_reporting.sas in the
ts-sas-legacy-analytics estate into dbt models on
Databricks, writing to
uc-data-migration-sas-to-databricks.

- SAS program:
  Programs/Banking/monthly_regulatory_reporting.sas
- Target model(s): mart_regulatory_rwa +
  mart_delinquency_aging
- Namespace: dev
```

### What to Explain While It Runs

- **Before launching:** Explain the "before" state — raw source data seeded into `banking_analytics.raw.*`, the banking domain already migrated (Phase 1), and the reconciliation harness in place. What Devin converts live is the next wave
- **During orientation (Act 1):** Point out that Devin is mapping SAS programs, macros, formats, and batch orchestration — distinguishing set-based logic (→ dbt) from procedural multi-output logic (→ PySpark)
- **During conversion (Act 2):** Explain that each converted model gets reconciliation controls: completeness checks, control totals, and source-parity mappings
- **When reconciliation catches the divergence:** This is the key beat. The SAS CASE maps `LOC` (line of credit) → risk weight 1.00. A plausible conversion maps it to 0.75 (matching other revolving-credit products). The parity control catches: `rwa_risk_weight_parity | FAIL | LOC=0.75 (source expects 1.00)`. Explain: "Looks reasonable" review would have shipped silent capital miscalculation. The reconciliation against the source caught it
- **After the fix:** Show the green reconciliation report. Emphasize: a conversion is "done" when source-parity controls are green, not when the code runs

### If It Goes Off-Script

| Scenario | Recovery |
|----------|----------|
| Databricks connection fails | Check secrets are configured. Explain this is a common setup issue — the shared context layer (Secrets) must be configured once for the org |
| Devin skips the reconciliation step | Leave a PR comment: "Please run `make reconcile NS=dev` and include the report." Show the PR feedback loop driving quality |
| Reconciliation fails on a different control | This is fine — explain that multiple controls may catch different issues. The point is programmatic verification, not a specific bug |
| Namespace collision with another session | Use a different namespace: `NS=walkthrough1`. Explain namespaced outputs enable concurrent execution |

---

## 3.4 Feature Development — Implement → Test → PR → Iterate

**What you are showing:** Devin as a day-to-day development partner — taking a feature requirement, implementing it, writing tests, opening a PR, and iterating based on review feedback.

### Setup Steps

1. Ensure the target repository is accessible (choose based on audience — `timesheet-app` for full-stack, `otterworks` for microservices)
2. Prepare a realistic feature request (or use the prompt below)
3. Familiarize yourself with the PR feedback loop — you will leave review comments live

### Prompts to Run

**Feature implementation:**

```
In the Cognition-Partner-Workshops/timesheet-app repo,
add a "Bulk Import" feature to the timesheet entries.
Requirements:

1. Add a CSV upload endpoint (POST /api/entries/import)
   that accepts a CSV file with columns: date, project,
   hours, description.
2. Validate each row (date format, hours > 0, project
   exists).
3. Return a summary: imported count, skipped count,
   and error details for skipped rows.
4. Add unit tests for the CSV parsing and validation
   logic.
5. Add an integration test that uploads a sample CSV
   and verifies the response.
```

### What to Explain While It Runs

- **Before launching:** Explain the prompt structure — it includes the repo, the endpoint path, specific requirements, validation rules, and expected output format. This is what makes prompts effective: concrete, verifiable acceptance criteria
- **During implementation:** Point out that Devin is reading existing code patterns (how other endpoints are structured, what test framework is used) and following conventions automatically via Knowledge notes
- **When the PR opens:** Show the PR — Devin has written code, tests, and a description. This is the handoff point
- **During review iteration:** Leave a review comment live ("Add input validation for the hours field — reject values over 24"). Watch Devin respond and push a new commit. Explain: this is the core workflow — PR comments are the communication channel

### If It Goes Off-Script

| Scenario | Recovery |
|----------|----------|
| Devin uses a different framework/library than expected | Leave a PR comment asking for the preferred approach. This shows how Knowledge notes prevent this in production |
| Tests fail on CI | Explain that Devin monitors CI and will iterate. Watch the session timeline for the fix cycle |
| Feature scope creeps beyond the prompt | Explain that well-scoped prompts with explicit boundaries prevent this. Reference the planning phase (Ask Devin) for requirement refinement |

---

## Knowledge Checks

**Knowledge Check 3.1**
Q: During a security remediation walkthrough, the scanner finds no vulnerabilities. What do you do?
A) "Apologize and end the walkthrough"
B) "Explain this is the green steady state, then switch to the backlog burn-down prompt to show remediation on existing findings"
C) "Run the scanner again with lower thresholds"
D) "Manually introduce a vulnerability to trigger the automation"
*Answer: B — A green scan is a valid state. Pivot to showing the backlog burn-down pattern, which shows the same remediation loop against existing (pre-committed) findings.*

**Knowledge Check 3.2**
Q: What is the key beat in the migration walkthrough that distinguishes Devin's approach from "it compiles and looks reasonable" review?
A) "The speed of conversion"
B) "The contract test / reconciliation catching a real parity divergence that human review would likely miss"
C) "The number of lines of code generated"
D) "The parallel fan-out across multiple endpoints"
*Answer: B — The verification beat is the core value message. Programmatic verification against the source of truth (contract tests, reconciliation controls) catches bugs that "looks reasonable" review would miss.*

**Knowledge Check 3.3**
Q: During a live walkthrough, Devin's PR has a compilation error. How do you handle this?
A) "Stop the walkthrough and start over"
B) "Explain that Devin will iterate — this is local testing catching issues before CI. Show the session timeline as Devin reads the error, pushes a fix, and re-runs"
C) "Manually fix the code in the PR"
D) "Switch to a different walkthrough type"
*Answer: B — Compilation errors during a walkthrough are a feature, not a failure. They show the verification model in action — Devin iterates toward green, just as a developer would.*

**Knowledge Check 3.4**
Q: What should you explain BEFORE launching a Devin session in a walkthrough?
A) "Nothing — just launch it and explain as it goes"
B) "The pattern Devin will follow, the verification mechanism, and what a successful outcome looks like — so the audience knows what to watch for"
C) "A full technical deep-dive on the architecture"
D) "The pricing model"
*Answer: B — Always set context before launching. The audience needs to know the pattern (what Devin will do), the verification (how you know it worked), and the success criteria (what done looks like). Without this framing, they are watching a terminal scroll.*

## Key Takeaways

- **Always explain the pattern before running** — set context so the audience knows what to watch for and what success looks like
- **The verification beat is the core value message** — the moment programmatic verification catches a bug that human review would miss is the highest-impact moment in any walkthrough
- **Off-script scenarios are recoverable** — every walkthrough type has predictable failure modes with known recovery patterns. Practice these
- **The PR feedback loop is the universal recovery tool** — when in doubt, leave a PR comment and show Devin iterating. This demonstrates the collaboration model regardless of what went off-script
