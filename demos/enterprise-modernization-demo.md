# Enterprise Modernization — End-to-End Demo

A single threaded demo that walks through an enterprise's full modernization
journey: legacy data pipelines, database stored procedures, integration APIs,
and security posture — all migrated, verified, and hardened by Devin. Each
phase follows the same pattern — orient over the legacy estate, convert one
artifact live, catch a real divergence through programmatic verification, fix
it, and fan the work out in parallel — building confidence incrementally until
the entire estate is modern, tested, and secured in CI.

<a id="toc"></a>
## Table of Contents

- [The Story](#the-story)
- [Phase 1 — Data Pipelines: SAS → dbt/Databricks](#phase-1)
  - [Orient over the SAS estate](#phase-1-orient)
  - [Convert one program live](#phase-1-convert)
  - [The verification beat](#phase-1-verify)
  - [Fan out in parallel](#phase-1-fanout)
  - [Run the produced artifact](#phase-1-run)
- [Phase 2 — Database: Sybase ASE → SQL Server](#phase-2)
  - [Orient over the Sybase estate](#phase-2-orient)
  - [Convert one procedure live](#phase-2-convert)
  - [The verification beat](#phase-2-verify)
  - [Fan out in parallel](#phase-2-fanout)
  - [Run the produced artifact](#phase-2-run)
- [Phase 3 — APIs: MuleSoft → Spring Boot](#phase-3)
  - [Orient over the MuleSoft estate](#phase-3-orient)
  - [Convert one endpoint live](#phase-3-convert)
  - [The verification beat](#phase-3-verify)
  - [Fan out in parallel](#phase-3-fanout)
  - [Run the produced artifact](#phase-3-run)
- [Phase 4 — Security: Event-Driven SAST Remediation](#phase-4)
  - [The vulnerability backlog](#phase-4-backlog)
  - [Devin burns it down](#phase-4-burndown)
  - [The CI gate: live trigger](#phase-4-ci-gate)
  - [Closed-loop verification and escalation](#phase-4-closed-loop)
  - [Scale with child sessions](#phase-4-scale)
- [The Pattern](#the-pattern)
- [Key Takeaways](#key-takeaways)

---

<a id="the-story"></a>
## The Story

An enterprise runs on four legacy layers stacked together:

| Layer | Legacy | Modern Target |
|-------|--------|---------------|
| **Data pipelines** | SAS programs (banking, insurance, regulatory reporting) | dbt models on Databricks with PySpark for procedural logic |
| **Database** | Sybase ASE stored procedures (loan servicing) | SQL Server T-SQL with namespace isolation |
| **APIs** | MuleSoft Mule 4 XML flows (employee services) | Spring Boot 3.x REST controllers with OpenAPI contracts |
| **Security posture** | Manual triage across a polyglot monorepo | Event-driven SAST with dual scanners and autonomous remediation |

Each layer has its own legacy artifacts, its own conversion challenges, and its
own definition of "done." But the approach is identical every time:

1. **Orient** — Devin maps the unfamiliar estate
2. **Convert one** — live, on camera, against the real target
3. **Verify** — programmatic controls catch a real divergence
4. **Fix** — the source of truth wins; silent "fixes" are not allowed
5. **Fan out** — child sessions convert the rest in parallel
6. **Run** — the produced artifact works end to end

The demo threads through all four layers in order, building a cumulative case
that the same pattern — playbook-driven conversion with programmatic
verification — works regardless of the technology being migrated.

---

<a id="phase-1"></a>
## Phase 1 — Data Pipelines: SAS → dbt/Databricks

**Repositories:**
- [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics) — the SAS estate (banking/insurance programs, macros, formats, batch orchestration). Read-only reference.
- [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks) — the dbt + Databricks target: models, reconciliation harness, seeder, PySpark job, Asset Bundle (IaC), CI/CD, and the conversion playbook.

The starting state is deliberately a *partial* migration: the banking programs
are already on `main` with a working reconciliation harness. What Devin
converts live is the next wave — regulatory and insurance programs not yet
migrated.

<a id="phase-1-orient"></a>
### Orient over the SAS estate

```
Using the ts-sas-legacy-analytics repo, give me a map of the SAS estate:
the banking and insurance programs, what each one reads and writes, the
LIBNAMEs, the macros and PROC FORMATs they depend on, and which programs are
set-based (good for dbt) vs procedural/multi-output (better as PySpark).
```

Expected: a tour of `Programs/Banking/*`, `Programs/Insurance/*`, the `Macro/`
and `Formats/` dependencies, and the Control-M-style `BatchJobs/` wrappers —
with a dbt-vs-PySpark recommendation per program.

<a id="phase-1-convert"></a>
### Convert one program live

The conversion uses the `!convert-sas-to-databricks` playbook. Devin reads the
SAS, writes the dbt model plus reconciliation controls, builds against the live
workspace, runs the controls, and produces a PR.

```
!convert-sas-to-databricks

Convert the SAS program Programs/Banking/monthly_regulatory_reporting.sas in
the ts-sas-legacy-analytics estate into dbt models on Databricks, writing to
uc-data-migration-sas-to-databricks.

- SAS program: Programs/Banking/monthly_regulatory_reporting.sas
- Target model(s): mart_regulatory_rwa + mart_delinquency_aging
- Namespace: dev
```

<a id="phase-1-verify"></a>
### The verification beat

The SAS CASE maps `LOC` (line of credit) → risk weight **1.00**. A
plausible-looking conversion maps `LOC` → 0.75 to match the other
revolving-credit products — silently overstating capital relief. The parity
control catches it:

```bash
make reconcile NS=dev
#   rwa_risk_weight_parity | FAIL | LOC=0.75 (source expects 1.00)
```

Restore `LOC` → 1.00 (source-faithful), re-run, and the report goes green:

```bash
make reconcile NS=dev
#   rwa_risk_weight_parity | PASS | all account_types match the SAS mapping
```

"Looks reasonable" review would have shipped 0.75. The parity check against the
source did not.

<a id="phase-1-fanout"></a>
### Fan out in parallel

Conversions are independent. Launch a Devin session per program using the same
playbook — or run one orchestrator session that spawns child sessions:

```
Act as the orchestrator for a SAS->Databricks migration across multiple
programs, using child Devin sessions to parallelize the work.

Repos: read Cognition-Partner-Workshops/ts-sas-legacy-analytics (the SAS
source), write Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks.

Spawn one child Devin session per program below. Give each child both repos, its
own namespace (NS=child1, child2, ...), and tell it to follow the
!convert-sas-to-databricks playbook (the repo's Skill supplies the
`make demo-up NS=...` / `make reconcile NS=...` mechanics): treat the SAS source
as the source of truth and reproduce its logic exactly; flag (do not silently
fix) anything that looks wrong; add reconciliation controls (completeness, a
control total, and a parity check for every CASE/mapping); and build until
everything is green, with the reconciliation report included.

Programs:
1. Programs/Banking/monthly_regulatory_reporting.sas
   -> mart_regulatory_rwa + mart_delinquency_aging
2. Programs/Insurance/claims_processing.sas
   -> stg_claims + int_claims_adjudication (dbt) AND a PySpark job
      src/pyspark/claims_processing.py
3. Programs/Insurance/policy_valuation.sas
   -> int_policy_valuation + mart_loss_ratios
4. Programs/Reports/customer_profitability.sas -> mart_customer_pnl

After launching, monitor the child sessions until each program is converted with
a green reconciliation report. Then summarize the results and call out any
source-parity divergences the children caught (e.g. a risk-weight mapping that
did not match the SAS source).
```

Each child writes to its own namespace — no collisions.

<a id="phase-1-run"></a>
### Run the produced artifact

```bash
make demo-up   NS=dev     # seed + dbt build into dev_* schemas
make reconcile NS=dev     # all controls PASS
```

Query before and after side by side:

```sql
SELECT count(*) FROM banking_analytics.raw.cust_accounts;            -- before
SELECT count(*) FROM banking_analytics.dev_marts.mart_risk_scores;  -- after

-- LOC sits at risk_weight = 1.00, matching the SAS source
SELECT account_type, risk_weight, n_accounts, total_exposure, rwa
FROM banking_analytics.dev_marts.mart_regulatory_rwa ORDER BY account_type;
```

Deploy as IaC, run the Workflow, then revert — `main` and the raw data are
never touched:

```bash
make deploy               # databricks bundle deploy
make run-job   TARGET=dev # DAG: dbt + pyspark in parallel
make destroy   TARGET=dev # remove deployed job
make demo-down NS=dev     # drop dev_* schemas
```

**Transition:** The data layer is migrated and verified. The same pattern —
orient, convert, verify, fan out — now applies to the database layer.

---

<a id="phase-2"></a>
## Phase 2 — Database: Sybase ASE → SQL Server

**Repositories:**
- [ts-tsql-sybase-legacy-db](https://github.com/Cognition-Partner-Workshops/ts-tsql-sybase-legacy-db) — the Sybase ASE estate (loan servicing: stored procedures, views, triggers, functions, batch scripts). Read-only reference.
- [uc-db-migration-sybase-to-sqlserver](https://github.com/Cognition-Partner-Workshops/uc-db-migration-sybase-to-sqlserver) — the SQL Server target: converted T-SQL, reconciliation harness, synthetic data seeder, conversion playbook, and CI/CD.

Same pattern: Phase 1 objects (tables, scalar functions, simple CRUD) are
already on `main`. Devin converts the complex procedures, views, and triggers
live.

<a id="phase-2-orient"></a>
### Orient over the Sybase estate

```
Using the ts-tsql-sybase-legacy-db repo, give me a map of the Sybase estate:
the tables, stored procedures (Servicing, Reporting, Batch, CRUD), views,
triggers, and functions. For each object, identify every Sybase-specific
construct that needs conversion to SQL Server. Reference the migration map at
uc-db-migration-sybase-to-sqlserver/docs/SYBASE_TO_SQLSERVER_MIGRATION_MAP.md
and rank each object by conversion complexity (simple, medium, complex).
```

Expected: 7 tables, 12 stored procedures, 3 views, 2 triggers, 3 functions.
Complex objects flagged with `*=` outer joins, `@@sqlstatus`,
`DEALLOCATE CURSOR`, `RAISERROR` without parentheses, `SET ROWCOUNT`,
`COMPUTE BY`, and `@@error + GOTO` error handling.

<a id="phase-2-convert"></a>
### Convert one procedure live

```
Convert the Sybase stored procedure
ts-tsql-sybase-legacy-db/stored_procs/Reporting/sp_delinquency_aging_report.sql
to SQL Server, following the conversion playbook at
uc-db-migration-sybase-to-sqlserver/docs/CONVERSION_PLAYBOOK.md.

Place the converted file at:
  migrations/stored_procs/Reporting/111_sp_delinquency_aging_report.sql

Use [$(NS)] schema prefix for namespace isolation. After conversion, deploy
and run reconciliation:
  make demo-down NS=dev && make demo-up NS=dev

If any reconciliation control fails, identify the root cause and fix it.
Include the reconciliation report in the PR.
```

<a id="phase-2-verify"></a>
### The verification beat

The Sybase source uses `*=` (outer join) to join `dbo.loans` to
`dbo.loan_modifications`. A plausible conversion removes the `*=` and writes
`INNER JOIN` — silently dropping every loan with no modification history. The
completeness control catches it:

```bash
make reconcile NS=dev
#   loan_portfolio_completeness | FAIL | expected=704, actual=440
#   Missing 264 loans in target.
#   Likely cause: *= outer join converted to INNER JOIN
```

Fix the join to `LEFT JOIN`, redeploy, and the report goes green:

```bash
make reconcile NS=dev
#   loan_portfolio_completeness | PASS | expected=704, actual=704
#   delinquency_bucket_parity   | PASS
#   payment_waterfall_totals    | PASS
#   va_late_fee_exclusion       | PASS
```

"Looks reasonable" review — or SSMA's bulk conversion — would have shipped the
INNER JOIN. The completeness control did not.

<a id="phase-2-fanout"></a>
### Fan out in parallel

| Session | Sybase Procedure | Key Constructs |
|---|---|---|
| 1 | `sp_delinquency_aging_report` | `*=` outer join (the worked example) |
| 2 | `sp_process_monthly_payments` | `@@sqlstatus`, `DEALLOCATE CURSOR`, `@@error + GOTO` |
| 3 | `sp_apply_late_fees` | `SET ROWCOUNT`, VA late-fee business rule |
| 4 | `sp_nightly_accrual` | `HOLDLOCK`, cursor pattern, `RAISERROR` |
| 5 | `vw_active_loan_portfolio` | `*=` outer join (view), `COMPUTE BY` removal |

Each session uses its own namespace (`NS=session1`, …) — no collisions.

<a id="phase-2-run"></a>
### Run the produced artifact

```bash
make seed                 # synthetic loan data into [raw]
make demo-up   NS=dev     # deploy converted objects into [dev]
make reconcile NS=dev     # all controls PASS
```

```sql
-- Before: source data
SELECT COUNT(*) FROM [raw].loans WHERE loan_status IN ('AC', 'DL');
-- After: converted view includes loans WITHOUT modifications
SELECT COUNT(*) FROM [dev].vw_active_loan_portfolio;
-- VA loans: zero late fees (business rule preserved)
SELECT loan_type, SUM(late_fee_balance) AS total_fees
FROM [dev].loans WHERE loan_type = 'VA' GROUP BY loan_type;
```

```bash
make demo-down NS=dev     # drop [dev] namespace
```

**Transition:** Two legacy layers down — data pipelines and database — both
migrated with the same orient-convert-verify pattern. The API layer is next.

---

<a id="phase-3"></a>
## Phase 3 — APIs: MuleSoft → Spring Boot

**Repositories:**
- [ts-java-mulesoft-employee-api](https://github.com/Cognition-Partner-Workshops/ts-java-mulesoft-employee-api) — the MuleSoft Mule 4 estate (Mule XML flows for OAuth2, employee endpoints, RAML spec, PostgreSQL integration). Read-only reference.
- [uc-api-migration-mulesoft-to-spring-boot](https://github.com/Cognition-Partner-Workshops/uc-api-migration-mulesoft-to-spring-boot) — the Spring Boot 3.5 target: project scaffold (empty controllers), OpenAPI contract, REST Assured verification harness, Docker Compose, and the conversion playbook.

The starting state is an empty scaffold: Spring Boot compiles and starts, but
no endpoints are implemented.

<a id="phase-3-orient"></a>
### Orient over the MuleSoft estate

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
contract, the `<db:select>` queries, DataWeave transforms, and `<choice>`
error handlers.

<a id="phase-3-convert"></a>
### Convert one endpoint live

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

<a id="phase-3-verify"></a>
### The verification beat

The MuleSoft `<choice>` error handler returns
`{"message": "No goals found for employee 74"}` on 404. A plausible conversion
uses `ResponseEntity.notFound().build()` — returning an *empty* 404 body. The
contract test catches it:

```
ContractVerificationIT > GoalsEndpoint > shouldReturn404WithMessageForUnknownEmployee
  FAILED
  Expected: body containing "message"
  Actual:   empty response body
```

Correct the implementation to return the `ErrorResponse` DTO with the message
field populated. Re-run, and the tests go green:

```bash
make verify
#   ContractVerificationIT — 8 tests PASSED
```

"Compiles and looks reasonable" review would have shipped an empty 404 body.
The contract test caught it.

<a id="phase-3-fanout"></a>
### Fan out in parallel

| Session | MuleSoft flow | Spring Boot target |
|---|---|---|
| 1 | `oauth-token-flow` + `validate-token-subflow` | `OAuthController` + `TokenService` |
| 2 | `get:\employee\{employeeId}\goals` | `GoalsController` + `GoalService` (the worked example) |
| 3 | `get:\employee\{employeeId}\learning-status` | `LearningController` + `LearningService` |
| 4 | `get:\employee\{employeeId}\next-pay-date` | `PayDateController` + `PayDateService` |
| 5 | PTO balance GET + schedule POST | `PtoController` + `PtoService` |

Each session targets its own namespace branch — no collisions.

<a id="phase-3-run"></a>
### Run the produced artifact

```bash
make db-up                    # PostgreSQL via Docker Compose
make run                      # Spring Boot on port 8080
```

Exercise the full API:

```bash
# Health check
curl http://localhost:8080/health

# Obtain OAuth token
TOKEN=$(curl -s -X POST http://localhost:8080/oauth/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials&client_id=demo-client&client_secret=demo-secret" \
  | jq -r '.access_token')

# Employee goals
curl -H "Authorization: Bearer $TOKEN" \
  http://localhost:8080/api/employee/EMP001/goals

# Verify 404 with message body (the verification beat, as data)
curl -H "Authorization: Bearer $TOKEN" \
  http://localhost:8080/api/employee/UNKNOWN/goals
# {"message": "No goals found for employee UNKNOWN"}

# Full contract suite
make verify
#   ContractVerificationIT — 8 nested test classes, all PASS
```

```bash
make db-down
```

**Transition:** Three legacy layers migrated — data pipelines, database, and
APIs — each with the same playbook-driven, verification-gated pattern. The
estate is modern. Now secure it.

---

<a id="phase-4"></a>
## Phase 4 — Security: Event-Driven SAST Remediation

**Repository:**
- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — polyglot monorepo with 11 backend services (Go, Rust, Python, Java, Kotlin, Scala, Ruby, C#, Node.js), 2 TypeScript frontends, and pre-existing Trivy/Semgrep/Gitleaks CI. Has planted CVEs and is onboarded to SonarCloud.

The previous three phases produced new modern code. This phase ensures it — and
everything else — stays secure, autonomously.

<a id="phase-4-backlog"></a>
### The vulnerability backlog

Open the SonarCloud dashboard:

```
https://sonarcloud.io/project/overview?id=Cognition-Partner-Workshops_otterworks
```

| Metric | Count |
|--------|-------|
| Vulnerabilities | 29 |
| Security Hotspots | 51 |
| Bugs | 24 |
| Code Smells | 434 |
| Lines of Code | 33,857 |

29 vulnerabilities across 9 languages. Hard-coded secrets, weak crypto, missing
ownership checks, CSRF disabled, ReDoS-prone regex, recursive COPY in
Dockerfiles. Manually triaging and fixing this is a multi-day effort.

<a id="phase-4-burndown"></a>
### Devin burns it down

```
Review the SonarCloud dashboard for otterworks at
https://sonarcloud.io/project/issues?id=Cognition-Partner-Workshops_otterworks&types=VULNERABILITY&resolved=false

There are 29 open vulnerabilities across the polyglot
monorepo. Triage them by severity (BLOCKER first, then
CRITICAL, then MAJOR). For each one:

1. Read the SonarCloud issue detail to understand the fix.
2. Check out a branch from main.
3. Fix the vulnerability in the correct file.
4. Run the affected service's tests.
5. Push the fix.

Group related fixes per service into single PRs when
possible (e.g., all docker-compose.yml password issues
in one PR, all Dockerfile COPY issues in another).

Skip findings in test files — those are acceptable.
```

One agent, 9 languages — no human triaging which file goes to which team. Devin
reads the right manifest (`package.json`, `Gemfile`, `go.mod`, `Cargo.toml`,
`pyproject.toml`) and runs the right test command (`npm test`, `pytest`,
`bundle exec rspec`) for each service.

<a id="phase-4-ci-gate"></a>
### The CI gate: live trigger

The backlog burn-down remediates existing debt. The CI gate prevents *new* debt
from shipping. The architecture is a dual-scanner pipeline in a single GitHub
Actions workflow:

```
Developer opens PR
        ↓
GitHub Actions: sast-auto-remediate.yml
        ↓
┌──────────────────────┬───────────────────────────────┐
│ PATH 1: Trivy        │ PATH 2: SonarCloud            │
│ (pull_request)       │ (check_run)                   │
│                      │                               │
│ Trivy fs scan        │ SonarCloud GitHub App         │
│ HIGH + CRITICAL      │ analyzes PR automatically     │
│     ↓                │     ↓                         │
│ Findings?            │ Quality gate failed?          │
│   No → pass          │   No → pass                  │
│   Yes ↓              │   Yes ↓                       │
│                      │                               │
│ attempts < 2?        │ Already attempted fix?        │
│   No → escalate      │   Yes → skip (one-time)      │
│   Yes ↓              │   No ↓                        │
│                      │                               │
│ Post PR comment      │ Validate PR state             │
│     ↓                │     ↓                         │
├──────────────────────┴───────────────────────────────┤
│           Devin v3 API → remediation session         │
│                                                      │
│  Devin checks out branch, fixes vulnerabilities,     │
│  runs service tests, pushes fix commit               │
│           ↓                                          │
│  Push triggers re-scan (closed loop):                │
│  • Trivy: synchronize event                          │
│  • SonarCloud: new check_run                         │
└──────────────────────────────────────────────────────┘
```

Trigger it live by opening a PR:

```bash
cd otterworks
git checkout -b workshop-test-sast main
echo "# Security pipeline test" >> README.md
git add README.md
git commit -m "docs: trigger SAST pipeline"
git push origin workshop-test-sast
gh pr create --title "docs: trigger SAST pipeline" \
  --body "Testing event-driven security remediation" \
  --base main
```

The PR timeline shows Trivy findings posted as a comment, followed by a Devin
session link. Nobody asked Devin to do this — the pipeline called the API.

<a id="phase-4-closed-loop"></a>
### Closed-loop verification and escalation

When Devin pushes a fix:

1. The `synchronize` event re-triggers Trivy. If findings are gone, CI goes
   green.
2. SonarCloud re-analyzes the PR. If the quality gate passes, the one-time
   guard prevents re-triggering.
3. If Devin's fix doesn't resolve everything, the pipeline runs again. After
   two failed attempts, it opens a GitHub Issue labeled `security` and
   `needs-human-review`. The system knows its limits.

Automated remediation handles the 80% case. The 20% requiring architectural
decisions gets escalated to humans.

<a id="phase-4-scale"></a>
### Scale with child sessions

For enterprise-scale remediation, use parent-child orchestration:

```
You are coordinating a polyglot security remediation
across the otterworks monorepo.

Run make security-scan and capture the output. Create a
SECURITY_BACKLOG.md that lists all CRITICAL and HIGH
findings organized by service.

Then launch parallel child sessions — one per affected
service — with scoped instructions:
- Each child works only on its assigned service directory
- Each child upgrades the vulnerable dependency, runs
  the service tests, and pushes to the same branch
- After all children complete, summarize results in
  REMEDIATION_SUMMARY.md
```

One scan produces a backlog; multiple agents remediate in parallel.

---

<a id="the-pattern"></a>
## The Pattern

Every phase follows the same structure. This is not a coincidence — it is the
design pattern that makes Devin effective at any migration or remediation
campaign:

| Step | Phase 1 (SAS) | Phase 2 (Sybase) | Phase 3 (MuleSoft) | Phase 4 (Security) |
|------|---------------|------------------|--------------------|--------------------|
| **Orient** | Map SAS programs, macros, formats | Map procedures, views, triggers, Sybase constructs | Map Mule XML flows, RAML spec, DataWeave | Scan SonarCloud: 29 vulns, 51 hotspots, 9 languages |
| **Convert one** | `monthly_regulatory_reporting.sas` → dbt models | `sp_delinquency_aging_report` → T-SQL | Employee goals → Spring Boot controller | BLOCKER-first triage → per-service fixes |
| **Verify** | Reconciliation controls (completeness, parity) | Reconciliation controls (loan count, bucket parity) | Contract tests (OpenAPI schema, status codes) | Trivy re-scan + SonarCloud re-analysis |
| **The real bug** | LOC risk weight 0.75 vs source 1.00 | `*=` outer join → INNER JOIN drops 264 loans | Empty 404 body vs source JSON error message | Closed-loop: fix, re-scan, escalate if stuck |
| **Fan out** | Child sessions per SAS program | Session per Sybase procedure | Session per MuleSoft endpoint | Child sessions per service directory |
| **Run end to end** | dbt build + IaC deploy + Workflow | seed + deploy + reconcile | Spring Boot + contract suite | CI gate prevents new debt from shipping |

The **source of truth** is always the legacy system. Conversions reproduce
legacy logic faithfully; quirks are flagged, not silently "fixed." Remediation
is a separate, deliberate decision.

The **playbook** makes every run consistent. Whether one human launches a
session or an orchestrator spawns 10 child sessions, the same procedure and the
same verification bar apply.

**CI gates every conversion.** No migration merges without passing verification
— reconciliation controls, contract tests, or re-scans. "Looks reasonable" is
not the bar; "programmatically verified against the source" is.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **One pattern, four technologies.** The orient-convert-verify-fanout loop
   works across data pipelines (SAS → dbt), databases (Sybase → SQL Server),
   APIs (MuleSoft → Spring Boot), and security posture (polyglot SAST). The
   technology changes; the approach does not.

2. **Confidence comes from programmatic verification, not code review.** Every
   phase shows a real divergence — a wrong risk weight, a dropped outer join,
   an empty error body — caught by automated controls, not human inspection.
   "Looks reasonable" review would have shipped each one.

3. **The source of truth is always the legacy system.** Conversions reproduce
   legacy behavior faithfully. Silent "improvements" are flagged; the source
   wins until a human decides otherwise.

4. **Playbooks make it repeatable; child sessions make it parallel.** The same
   conversion procedure applies whether you run one session or fifty. The
   orchestrator pattern (parent spawns children, each with its own namespace)
   means a 50-program estate is converted in parallel, not in series.

5. **From backlog to CI gate.** Phase 4 shows both sides of security: burning
   down existing tech debt *and* preventing new debt from shipping. The same
   pattern — scan, fix, re-scan — runs as a background service, not a manual
   process.

6. **The migration is the demo.** The value on display is Devin doing the work
   — reading unfamiliar estates, converting artifacts off reusable playbooks,
   catching real bugs through verification, and scaling via parallelism. The
   finished artifact running end to end is the proof, not the point.
