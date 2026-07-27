# Audit Readiness for a Regulated Banking Platform — Compliance & Governance Demo

Devin operates as a standing compliance engineering agent: it assesses control
gaps, turns control expectations into policy-as-code, assembles scheduled
evidence packs, fans work across a portfolio, and preserves a review trail.
The agent produces evidence and remediation work for audit preparation; it does
not certify, attest, or make the compliance determination.

<a id="toc"></a>
## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [The Control Model](#the-control-model)
- [Part 1 — The Control Gap](#part-1)
- [Part 2 — Remediation PR with an Auditable Trail](#part-2)
- [Part 3 — Policy-as-Code in CI](#part-3)
- [Part 4 — Devin Review as the Control-Conformance Reviewer](#part-4)
- [Part 5 — The Scheduled Evidence Pack](#part-5)
- [Part 6 — Portfolio Fan-Out with Child Sessions](#part-6)
- [What Still Needs a Human](#what-still-needs-a-human)
- [Before and After](#before-and-after)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Start with the control-gap assessment. It creates the baseline artifact that
the remediation, CI, review, and evidence-pack steps will reference.

Paste this prompt into Devin:

```
Assess the six services in
Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking
against these controls:

1. Change tracking and auditing on regulated entities.
2. No hard-coded secrets in configuration.
3. Dependency currency for money-movement paths.

Use the repository's main branch. Inspect the service source trees,
configuration, build files, and existing tests. Map each control to
PCI DSS 4.0 Requirement 10.x-style language without claiming
certification or compliance. Record control ID, framework mapping,
severity, status, rationale, and exact evidence file paths.

Write CONTROL_GAP_ASSESSMENT.md at the repository root. Include a
short executive summary, a per-service control matrix, the expected
headline finding, evidence paths, and recommended remediation order.
Return a Markdown summary with the artifact path, findings by severity,
and a list of paths inspected.
```

Expected result: `CONTROL_GAP_ASSESSMENT.md` identifies the
`core-banking-service` change-tracking gap with sibling-service JPA evidence.
It is an audit-prep artifact, not an attestation.

---

<a id="repositories"></a>
## Repositories
- [ts-java-spring-boot-internet-banking](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking)
  — Spring Boot internet-banking services, including core accounts and
  money-movement transactions.
- [fineract](https://github.com/Cognition-Partner-Workshops/fineract) —
  the large Apache Fineract banking platform clone, with modular Gradle
  services such as `fineract-core` and `fineract-provider`.
- [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
  — Spring Boot application with OWASP Dependency-Check and SonarQube Gradle
  plugin configuration.
- [uc-document-review-automation](https://github.com/Cognition-Partner-Workshops/uc-document-review-automation)
  — Python loan-document review workflow with application-level decision and
  audit logging.

The banking repository uses `main` as its default branch. Its
`core-banking-service` module owns accounts and transaction writes through
`TransactionController`:

- `POST /api/v1/transaction/fund-transfer`
- `POST /api/v1/transaction/util-payment`

---

<a id="the-control-model"></a>
## The Control Model

| Control ID | Control expectation | Evidence style |
|---|---|---|
| GOV-10.1 | Regulated entities have attributable change tracking. | JPA auditing configuration, auditor provider, entity listener, and tests |
| GOV-10.2 | Configuration does not embed application secrets. | Configuration paths, secret-scanning output, and review findings |
| GOV-10.3 | Dependencies in money-movement paths have a currency review. | Build files, dependency scan output, and remediation history |

GOV-10.1 maps to PCI DSS 4.0 Requirement 10 wording style around logging and
monitoring access to system components; people decide framework scope.

The verified starting state has a useful contrast. The fund-transfer, user,
and utility-payment services contain these exact paths:
`internet-banking-fund-transfer-service/src/main/java/com/javatodev/finance/configuration/audit/AuditConfig.java`,
`internet-banking-fund-transfer-service/src/main/java/com/javatodev/finance/configuration/audit/AuditorAwareConfig.java`,
`internet-banking-fund-transfer-service/src/main/java/com/javatodev/finance/model/dto/AuditAware.java`,
`internet-banking-user-service/src/main/java/com/javatodev/finance/configuration/audit/AuditConfig.java`,
`internet-banking-user-service/src/main/java/com/javatodev/finance/configuration/audit/AuditorAwareConfig.java`,
`internet-banking-user-service/src/main/java/com/javatodev/finance/model/dto/AuditAware.java`,
`internet-banking-utility-payment-service/src/main/java/com/javatodev/finance/configuration/audit/AuditConfig.java`,
`internet-banking-utility-payment-service/src/main/java/com/javatodev/finance/configuration/audit/AuditorAwareConfig.java`,
and `internet-banking-utility-payment-service/src/main/java/com/javatodev/finance/model/dto/AuditAware.java`.
Their patterns include `@EnableJpaAuditing`, `@CreatedBy`, and
`@LastModifiedBy`. The `core-banking-service` module, which owns accounts and
money-movement transactions, does not contain the corresponding audit
configuration. That is a concrete, verifiable change-tracking control gap on
`main`, not a claim that the application fails a certification.

---

<a id="part-1"></a>
## Part 1 — The Control Gap
Run the assessment from Quick Start and compare the three sibling services with
`core-banking-service`.

The controller evidence is
`core-banking-service/src/main/java/com/javatodev/finance/controller/TransactionController.java`;
it declares `@RequestMapping(value = "/api/v1/transaction")` and implements
the two transaction POST endpoints. The module wrapper is
`core-banking-service/gradlew`.

**Expected:** the headline finding says that `core-banking-service` is missing
the audit configuration present in its fund-transfer, user, and utility-payment
sibling services. Evidence cites `AuditConfig.java`,
`AuditorAwareConfig.java`, and `AuditAware.java` using the exact repository
paths. The assessment prompt records control ID, mapping, severity, status,
rationale, and evidence paths.
The recommendation is to add the existing pattern to the owning module and
verify it with the module test gate.

The artifact can record GOV-10.2 and GOV-10.3 separately. Record “not
observed” or “needs human scope decision” when the evidence does not support a
finding.

---

<a id="part-2"></a>
## Part 2 — Remediation PR with an Auditable Trail

Paste this prompt into Devin:

```
In Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking,
remediate GOV-10.1 in the core-banking-service module.

Follow the existing sibling pattern from:

- internet-banking-fund-transfer-service/src/main/java/com/javatodev/finance/configuration/audit/AuditConfig.java
- internet-banking-fund-transfer-service/src/main/java/com/javatodev/finance/configuration/audit/AuditorAwareConfig.java
- internet-banking-fund-transfer-service/src/main/java/com/javatodev/finance/model/dto/AuditAware.java

Add the equivalent AuditConfig, AuditorAwareConfig, and AuditAware
implementation under the core-banking-service package layout. Apply the
auditing listener and fields to the regulated JPA entities that own
account and transaction state. Preserve existing conventions and avoid
unrelated refactoring.

Run ./gradlew test from core-banking-service. Write a PR body that
traces:

finding → control → evidence → fix → verification

The PR body must list exact changed paths, the test command and result,
the sibling paths used as precedent, and any scope limitations. Return
the changed-file list, test output summary, and the proposed PR body as
Markdown.
```

The PR’s diff, review thread, test check, and linked assessment preserve the
chain from finding to fix without replacing control-owner review.

```bash
cd core-banking-service
./gradlew test
```

The repository Skill describes this verification gate and limits unrelated
change by keeping work inside the module.

---

<a id="part-3"></a>
## Part 3 — Policy-as-Code in CI

The starting repository has `.github/` but no `.github/workflows/` directory.
This step creates the workflow and a machine-readable policy directory. The
workflow checks pull requests that touch regulated Java source paths:

```text
.github/workflows/compliance-policy-check.yml
policy/
```

Paste this prompt into Devin:

```
In Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking,
create a policy-as-code gate for regulated source changes.

Create .github/workflows/compliance-policy-check.yml and a policy/
directory. Use Rego/conftest or a small, testable script with
machine-readable rules. The rules must cover:

1. Entities under each service's model/entity path require an audit
   configuration in the owning service.
2. Run a Gitleaks secrets scan against the pull request.
3. Run a dependency review for changed build files and money-movement
   paths.

Trigger the workflow on pull_request when changed paths include
services' src/main/java trees, including:
core-banking-service/src/main/java/
internet-banking-fund-transfer-service/src/main/java/
internet-banking-user-service/src/main/java/
internet-banking-utility-payment-service/src/main/java/

On failure, post a concise PR comment with the control ID, failed rule,
changed paths, and remediation link. Call the Devin v3 API to open a
remediation session with the PR number, changed paths, failed policy
results, and repository name. Skip PRs authored by
devin-ai-integration[bot]. Track remediation attempts and escalate to
a GitHub Issue after 2 attempts to avoid a bot loop.

Add workflow tests or a local policy test fixture. Document the policy
inputs, outputs, secrets, and bot-loop guard in policy/README.md.
Return the workflow path, policy file list, test commands, and an
example failed-policy PR comment as Markdown.
```

The implementation mirrors the deeper scanner, comment, Devin v3 API, and
escalation pattern in [`event-driven-sast-remediation-demo.md`](../security/use-cases/event-driven-sast-remediation-demo.md).

The remediation-session trigger payload contains:

```text
repository: Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking
pull_request_number: <PR number>
changed_paths: <paths from the pull_request event>
failed_controls: <machine-readable policy results>
attempt_count: <current remediation count>
```

CI posts failure context, starts remediation, and rechecks after the fix; the
author guard and two-attempt limit prevent loops and escalate persistent
findings to a human-owned GitHub Issue.

---

<a id="part-4"></a>
## Part 4 — Devin Review as the Control-Conformance Reviewer

Now make the control visible during ordinary review. A human can create a
small change that adds a transaction field without audit columns:

```bash
git switch -c workshop-control-review
printf '\n    private String reviewMarker;\n' >> \
  core-banking-service/src/main/java/com/javatodev/finance/model/entity/TransactionEntity.java
git add core-banking-service/src/main/java/com/javatodev/finance/model/entity/TransactionEntity.java
git commit -m "Add transaction review marker"
```

Ask Devin Review to inspect the resulting PR for control conformance:

```
Review the pull request in
Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking.

Focus on GOV-10.1 and the existing evidence convention. Inspect
core-banking-service/src/main/java/com/javatodev/finance/model/entity/TransactionEntity.java,
core-banking-service/src/main/java/com/javatodev/finance/controller/TransactionController.java,
and the sibling audit pattern in
internet-banking-fund-transfer-service/src/main/java/com/javatodev/finance/model/dto/AuditAware.java.

Comment on control-conformance issues with:
- finding and severity
- exact changed path and line
- framework mapping in PCI DSS 4.0 Requirement 10.x-style language
- evidence path
- resolution needed before approval

Do not claim certification. Return a review summary in Markdown and
keep the findings in the PR review thread.
```

The same loop applies to Devin’s remediation PR from Part 2: Devin addresses
the review finding with a follow-up commit. The persistent thread records
reviewers, findings, resolutions, and timestamps as an audit-prep artifact,
rather than replacing sign-off.

Create a shared Knowledge note so the control set survives the session:

```
Create a Knowledge note titled "Banking control conformance and evidence"
for Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking.
Record GOV-10.1, GOV-10.2, GOV-10.3, the PCI DSS 4.0 Requirement
10.x-style mapping convention, exact path citations, the
finding → control → evidence → fix → verification format, and the rule
that Devin produces evidence and remediation but does not certify or attest.
Return the note title and scope as Markdown.
```

Knowledge, DeepWiki, and the banking playbook form a shared context layer for
reusing evidence conventions.

---

<a id="part-5"></a>
## Part 5 — The Scheduled Evidence Pack

A scheduled session runs at 07:00 UTC on the first day of each quarter and
assembles an evidence directory for review:

```text
evidence/<quarter>/
```

Paste this as the scheduled-session instruction text:

```
On the scheduled quarterly run, assemble an audit-prep evidence pack
for Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking.
Run at 07:00 UTC on the first day of the quarter.

Create evidence/<quarter>/ with:

1. A CycloneDX SBOM for each service, with service name, commit SHA,
   generation command, and artifact path.
2. A dependency scan summary covering the money-movement paths and
   build files.
3. The current CONTROL_GAP_ASSESSMENT.md delta versus the prior
   quarter's assessment, with added, resolved, and unchanged findings.
4. Links to merged remediation PRs and their review threads, including
   the finding, control ID, evidence paths, fix, and verification.
5. A manifest with timestamps, source commit SHAs, tool versions, and
   session links.

Use the banking repository's main branch as the source baseline. Cite
exact paths and preserve prior evidence rather than overwriting it.
Return the evidence directory tree and a Markdown index of the pack.
The pack is input for humans and auditors who make the compliance
determination; Devin does not certify or attest.
```

Humans review the pack; coverage depends on repository structure and available
tools.

---

<a id="part-6"></a>
## Part 6 — Portfolio Fan-Out with Child Sessions
A parent session applies the same control set across a portfolio with one child
session per repository:

| Repository | Child scope | Evidence anchor |
|---|---|---|
| `ts-java-spring-boot-internet-banking` | JPA auditing across banking services | `configuration/audit/AuditConfig.java`, `AuditorAwareConfig.java`, and `model/dto/AuditAware.java` |
| `fineract` | `fineract-core` and `fineract-provider` first, with scope expanded by structure | Full Apache Fineract clone on default branch `main` |
| `uc-cve-remediation-regulatory-compliance` | Spring Boot 2.6.3 dependency and static-analysis controls on default `main` | `build.gradle` with OWASP Dependency-Check and SonarQube plugins |
| `uc-document-review-automation` | Application-level review decision traceability | `src/agents/audit_agent.py` and `src/models/review_result.py` |

The Fineract child starts with `fineract-core` and `fineract-provider` because
the repository is large. Coverage depends on repo structure; it reports what
it inspected rather than implying coverage from a partial scan.

The document-review child cites `src/agents/audit_agent.py`, which writes JSON
Lines to `logs/audit.jsonl` by default, and
`src/models/review_result.py`, which defines `AuditEntry`, `ReviewResult`,
decisions, confidence, and mismatch information.

Paste this parent prompt:

```
Act as the portfolio control-assessment parent for these repositories:

1. Cognition-Partner-Workshops/ts-java-spring-boot-internet-banking
2. Cognition-Partner-Workshops/fineract
3. Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance
4. Cognition-Partner-Workshops/uc-document-review-automation

Use one child Devin session per repository. Apply GOV-10.1 change
tracking, GOV-10.2 secret handling, and GOV-10.3 dependency currency.
Each child must use the repository's default branch, inspect its
declared scope, cite exact evidence paths, and report coverage limits.

Start the child scopes as follows:
- ts-java-spring-boot-internet-banking: JPA auditing in the six
  services, with focus on core-banking-service.
- fineract: fineract-core and fineract-provider first.
- uc-cve-remediation-regulatory-compliance: build.gradle,
  dependency scanning, and SonarQube configuration.
- uc-document-review-automation: src/agents/audit_agent.py,
  src/models/review_result.py, and audit tests.

Aggregate the child outputs into PORTFOLIO_CONTROL_SCORECARD.md.
For each repository include control status, scope inspected, evidence
links, severity, remediation PR links, and unresolved human decisions.
Return the scorecard path, child session links, and a Markdown summary.
```

The parent aggregates independent child work; this cross-repository,
unattended, event- and schedule-driven flow fits a cloud agent and child-session
team better than an IDE assistant in one checkout.
People retain ownership of interpretation, approvals, and production decisions.

---

<a id="what-still-needs-a-human"></a>
## What Still Needs a Human

The agent can gather evidence and prepare remediation, but people own:

- Control interpretation and framework scoping.
- Accepting, rejecting, or waiving a finding.
- Sign-off and attestation to auditors.
- Production access and data-handling decisions.
- Judgment about compensating controls.
- Approval of remediation risk and rollout timing.
- The final determination of whether evidence is sufficient for the intended
  audit or governance process.

Keep these decisions visible in the review trail and evidence-pack index.

---

<a id="before-and-after"></a>
## Before and After

| Outcome | Before | After |
|---|---|---|
| Audit preparation | Weeks of manual evidence gathering at quarter end. | A standing evidence pipeline is reviewed, rather than assembled, by humans. |
| Time to remediate a finding | A finding sits in a spreadsheet while ownership and evidence are assembled. | A remediation PR is typically open the same day the gap is detected. |
| Control review | Sibling-service patterns are known informally and gaps are easy to miss. | A control matrix cites paths, policy checks, review comments, and verification output. |

---

<a id="key-takeaways"></a>
## Key Takeaways

- Devin can operate as a background compliance engineering agent rather than
  only as an IDE assistant working on one immediate change.
- Event-driven policy checks and scheduled evidence sessions turn governance
  work into a standing workflow instead of a quarter-end scramble.
- Child sessions let a team fan one control set across repositories while a
  parent session aggregates bounded findings and coverage limits.
- Devin Review comments, follow-up commits, checks, and timestamps create a
  persistent review trail that can support audit preparation.
- Knowledge, DeepWiki, and playbooks provide a shared context layer for
  organization-specific control and evidence conventions.
- Devin is a team resource: parent coordination, child assessment, policy
  automation, and human review can proceed as a collaborative flow.
- The human remains in the loop for scope, interpretation, waivers, production
  access, compensating controls, sign-off, and the compliance determination.
