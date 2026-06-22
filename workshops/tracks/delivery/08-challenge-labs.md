# Challenge-Mode Labs

<a id="toc"></a>
## Table of Contents

- [Lab 1: Extract a Microservice from a Monolith](#lab-1-extract-a-microservice-from-a-monolith)
- [Lab 2: Remediate SAST Findings Across a Repository](#lab-2-remediate-sast-findings-across-a-repository)
- [Lab 3: Set Up a Scheduled Dependency Upgrade](#lab-3-set-up-a-scheduled-dependency-upgrade)
- [Lab 4: Translate a Service from Java to Python](#lab-4-translate-a-service-from-java-to-python)
- [Lab 5: Design an Event-Driven SAST Pipeline](#lab-5-design-an-event-driven-sast-pipeline)

---

<a id="lab-1-extract-a-microservice-from-a-monolith"></a>

These labs require you to write your own prompts from scratch. No copy-paste is provided. For each scenario, read the description, apply the prompt engineering principles from [Section 2](02-prompt-engineering.md), and write a complete prompt.

## Lab 1: Extract a Microservice from a Monolith

**Scenario:** Your team has a Java Spring Boot monolith with three bounded contexts tightly coupled in a single application: Users, Products, and Orders. The Orders module has been identified for extraction into a standalone microservice. The Orders module accesses the `orders`, `order_items`, and `order_status_history` tables. It currently calls User and Product methods directly via in-process method calls.

**Success criteria:**
- A standalone Spring Boot service for Orders with its own `Dockerfile` and database configuration
- HTTP-based communication replacing the direct method calls to User and Product services
- Docker Compose file that runs the monolith and the extracted service together
- Integration tests that verify inter-service communication
- All existing tests in the monolith still pass

**Grading rubric — a good prompt includes:**
- [ ] Repository name
- [ ] Specific bounded context to extract (Orders) and the tables it owns
- [ ] Communication pattern to use (HTTP) and how shared data should be handled
- [ ] Explicit requirement for Dockerfile and Docker Compose
- [ ] Testing requirements (integration tests + existing tests pass)
- [ ] Constraints (what NOT to change — e.g., the User and Product modules)

<details>
<summary><strong>Reference answer</strong> (expand after writing your own)</summary>

```
Extract the Orders bounded context from
my-monolith-app into a standalone Spring Boot
microservice.

The Orders module owns these tables: orders,
order_items, order_status_history. It currently calls
UserService and ProductService via direct method
invocation.

Requirements:
- Create a new Spring Boot application for the Orders
  service with its own Dockerfile (multi-stage build)
- Replace direct method calls to UserService and
  ProductService with HTTP REST calls
- Create a docker-compose.yml that runs both the
  monolith and the Orders service with separate
  PostgreSQL databases
- Add integration tests that verify:
  - Creating an order calls the User service to
    validate the customer
  - Order status changes are persisted correctly
  - The monolith's remaining functionality is
    unaffected

Constraints:
- Do NOT modify the User or Product modules in the
  monolith
- The Orders service must be independently deployable
- Use the existing Spring Boot version (do not
  upgrade)

Verify by running: docker-compose up and executing
the integration test suite
```

</details>

---

<a id="lab-2-remediate-sast-findings-across-a-repository"></a>
## Lab 2: Remediate SAST Findings Across a Repository

**Scenario:** Your security team has run a SonarQube scan against your organization's main API gateway repository. The scan identified 47 findings: 3 CRITICAL (SQL injection), 12 HIGH (XSS, path traversal), and 32 MEDIUM (hardcoded secrets, weak crypto). You need to remediate the CRITICAL and HIGH findings and re-scan to verify.

**Success criteria:**
- All 3 CRITICAL findings remediated
- All 12 HIGH findings remediated
- Re-scan shows 0 CRITICAL and 0 HIGH findings remaining
- All existing tests pass
- A `SECURITY_REMEDIATION.md` documenting each finding and its fix

**Grading rubric — a good prompt includes:**
- [ ] Repository name
- [ ] Specific severity threshold to remediate (CRITICAL + HIGH)
- [ ] The scan tool and how to run it
- [ ] Verification mechanism (re-scan)
- [ ] Documentation requirement
- [ ] Constraint: do not change MEDIUM findings (to scope the work)

<details>
<summary><strong>Reference answer</strong> (expand after writing your own)</summary>

```
Remediate all CRITICAL and HIGH severity findings
from the SonarQube scan in api-gateway-service.

To get the current findings, run:
./gradlew sonarqube -Dsonar.host.url=${SONAR_URL}

Focus on:
- CRITICAL (3 findings): SQL injection vulnerabilities
  — use parameterized queries, not string concatenation
- HIGH (12 findings): XSS and path traversal — apply
  input sanitization and validate file paths against
  an allowlist

Do NOT remediate MEDIUM findings in this session.

After remediating:
1. Run ./gradlew test to verify no regressions
2. Re-run the SonarQube scan to confirm 0 CRITICAL
   and 0 HIGH findings remain
3. Create SECURITY_REMEDIATION.md documenting:
   - Each finding (ID, severity, description)
   - The fix applied
   - Before/after scan results
```

</details>

---

<a id="lab-3-set-up-a-scheduled-dependency-upgrade"></a>
## Lab 3: Set Up a Scheduled Dependency Upgrade

**Scenario:** Your team manages 8 Node.js microservices. Dependencies are currently 6-18 months behind because no one has time for manual bumps. You want to set up a recurring Devin session that automatically bumps dependencies weekly, verifies the build, and opens PRs for human review.

**Success criteria:**
- A working prompt that can be used as a recurring scheduled session
- The prompt handles: checking for updates, applying non-breaking upgrades, running tests, reverting breaking upgrades, documenting changes
- The prompt can be reused across different Node.js repos without modification (or with minimal variable substitution)

**Grading rubric — a good prompt includes:**
- [ ] Target repository (or parameterized for reuse)
- [ ] Scope of upgrades (minor/patch only — no major version jumps)
- [ ] Verification mechanism (run tests, run build)
- [ ] Rollback strategy (revert individual breaking upgrades)
- [ ] Documentation requirement (what was updated, what was skipped)
- [ ] PR title convention for consistency

<details>
<summary><strong>Reference answer</strong> (expand after writing your own)</summary>

```
Check all npm dependencies in [REPO_NAME] for
available minor and patch version updates.

Steps:
1. Run npm outdated to identify available updates
2. For each outdated dependency, update to the latest
   minor version (do NOT jump major versions)
3. After each batch of updates, run npm test and
   npm run build
4. If any upgrade breaks the build or tests, revert
   that specific upgrade and note it in the report

Create a DEPENDENCY_UPDATES.md documenting:
- Each dependency upgraded (from version → to version)
- Any upgrades skipped (with the reason: test failure,
  build failure, etc.)

Title the PR: "chore: weekly dependency bump — [date]"
```

</details>

---

<a id="lab-4-translate-a-service-from-java-to-python"></a>
## Lab 4: Translate a Service from Java to Python

**Scenario:** Your organization is migrating away from a legacy Java codebase to Python. The first service to migrate is the Notification service, which has 4 REST endpoints, uses JPA for database access, and sends emails via SMTP. You need the Python version to be a drop-in replacement with identical API contracts.

**Success criteria:**
- Python (FastAPI) application with the same 4 REST endpoints
- Same JSON request/response shapes as the Java version
- Parity tests proving identical behavior for the same inputs
- `MIGRATION_NOTES.md` documenting translation decisions
- Both versions build and run

**Grading rubric — a good prompt includes:**
- [ ] Source repository and target framework
- [ ] Specific endpoints to translate
- [ ] Requirement to preserve API contract (same JSON shapes)
- [ ] Parity/equivalence testing approach
- [ ] Documentation of translation decisions
- [ ] Technology choices for the Python version (ORM, validation library)

<details>
<summary><strong>Reference answer</strong> (expand after writing your own)</summary>

```
Translate the Notification service from
notification-service-java (Java/Spring Boot) to
Python/FastAPI.

Endpoints to translate:
- POST /api/notifications/send
- GET /api/notifications/{id}
- GET /api/notifications?userId={userId}
- DELETE /api/notifications/{id}

Requirements:
- Use FastAPI for the web framework
- Use SQLAlchemy for database access (replacing JPA)
- Use Pydantic for request/response models
- Preserve the exact JSON request/response shapes so
  the Python version is a drop-in replacement
- Use smtplib for email sending (replacing
  JavaMailSender)

Testing:
- Write pytest tests that verify each endpoint returns
  identical responses to the Java version for the same
  inputs
- Include edge cases: empty results, invalid IDs,
  malformed requests

Create MIGRATION_NOTES.md documenting:
- Java pattern → Python equivalent mapping
- Any behavioral differences and why
- Dependencies used
```

</details>

---

<a id="lab-5-design-an-event-driven-sast-pipeline"></a>
## Lab 5: Design an Event-Driven SAST Pipeline

**Scenario:** Your security team wants Devin to automatically remediate security findings whenever a SAST scan completes. The scan runs in CI after every merge to `main`. When findings above a threshold are detected, Devin should automatically create a session to remediate them.

**Success criteria:**
- A working description of the automation configuration (trigger, filter, prompt template, safeguards)
- The prompt template uses variables for the scan results
- Safeguards prevent infinite loops and duplicate sessions

**Grading rubric — a good prompt includes:**
- [ ] Trigger definition (CI completion event)
- [ ] Filter criteria (only when findings above threshold exist)
- [ ] Prompt template with variables for scan results
- [ ] Explicit safeguards (loop prevention, retry limits, idempotency)
- [ ] Verification mechanism (re-scan after remediation)

<details>
<summary><strong>Reference answer</strong> (expand after writing your own)</summary>

This is an automation configuration, not a single prompt. A good answer describes:

**Trigger:** GitHub Actions workflow completion webhook for the `sast-scan` workflow on the `main` branch.

**Filter:** Only trigger when the scan reports CRITICAL or HIGH findings (check the scan artifact for finding count > 0). Do NOT trigger on PRs authored by `devin-ai-integration[bot]`.

**Prompt template:**

```
The latest SAST scan on {{repo_name}} (branch: main,
commit: {{commit_sha}}) found {{finding_count}}
CRITICAL/HIGH findings.

Scan results are at: {{scan_artifact_url}}

Remediate all CRITICAL and HIGH findings. For each:
1. Read the finding details (CWE, affected file, line)
2. Apply the recommended fix
3. Run the test suite to verify no regressions

After all remediations:
- Re-run the SAST scan to verify 0 CRITICAL/HIGH
  remain
- Document fixes in SECURITY_REMEDIATION.md
```

**Safeguards:**
- Filter: exclude events from `devin-ai-integration[bot]`
- Max 3 sessions per commit SHA (idempotency)
- Max 1 concurrent remediation session per repo

</details>
