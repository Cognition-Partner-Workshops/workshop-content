# OtterWorks Lab Evaluation Scripts

Automated evaluation prompts for verifying lab completion. Each script is designed to be run through the Devin v3 API as a service-account session.

## Usage

Create a Devin session via the v3 API with the evaluation prompt as the task. Replace the template variables:

- `{{user_email}}` — email or user ID of the participant being evaluated
- `{{git_repo}}` — the participant's fork or the shared repo (e.g. `Cognition-Partner-Workshops/otterworks`)
- `{{branch_or_pr}}` — the branch name or PR number to evaluate (e.g. `workshop-attendee-42` or `PR #120`)

Each evaluation returns a structured `RESULT: PASS | FAIL` with per-criterion breakdown and a summary explanation for any failures.

## Evaluation Scripts

| Lab | File | What It Checks |
|-----|------|----------------|
| A1 — ETL Modernization | `A1-etl-modernization-eval.md` | Airflow DAGs exist, credentials removed, task separation, retry policies, business logic preserved |
| A2 — Framework Upgrade | `A2-framework-upgrade-eval.md` | Java 17, Spring Boot 3.2+, javax→jakarta, JUnit 5, springdoc, tests pass, build succeeds |
| A3 — Language Translation | `A3-language-translation-eval.md` | Flask removed, FastAPI app, all endpoints preserved, Pydantic models, async handlers, contract tests |
| B1 — Investigate Incident | `B1-investigate-incident-eval.md` | Root cause documented for at least one chaos scenario |
| B2 — Complete Runbooks | `B2-complete-runbooks-eval.md` | At least 2 runbooks filled in with actionable, codebase-grounded content |
| B3 — Add Observability | `B3-add-observability-eval.md` | Prometheus metrics and structured logging added to at least one service |
| C1 — Security Sprint | `C1-security-sprint-eval.md` | CVEs remediated, dependency files consistent, trivyignore cleaned, suppressions documented |
| C2 — Contract Audit | `C2-contract-audit-eval.md` | Notification and audit event schema drift identified and fixed |
| C3 — Test Coverage | `C3-test-coverage-eval.md` | At least 3 meaningful tests added to the weakest service |
| D1 — CI Pipeline Audit | `D1-ci-pipeline-audit-eval.md` | CI strategy drift documented, change-detection gaps identified |
| D2 — IaC Review | `D2-infrastructure-review-eval.md` | Terraform and Helm charts audited, at least 3 findings documented |
| D3 — Event-Driven SAST | `D3-event-driven-sast-eval.md` | SAST pipeline flow understood, bot-loop prevention explained, improvement proposed |
| E1 — Bug Hunt | `E1-cross-service-bug-hunt-eval.md` | At least 2 bugs from QA report fixed with cross-service tracing |
| E2 — Gateway Routes | `E2-api-gateway-routes-eval.md` | At least 2 missing route prefixes added to API gateway |
| E3 — Synthetic Test Data | `E3-synthetic-test-data-eval.md` | New namespace created, generation scripts functional, validation considered |
| F1 — ADRs | `F1-architecture-decision-records-eval.md` | At least 3 ADRs written, grounded in codebase evidence |
| F2 — API Flow Tests | `F2-api-flow-test-expansion-eval.md` | At least 3 new black-box API tests, following existing patterns |
| F3 — Technical Docs | `F3-technical-documentation-eval.md` | At least 2 service READMEs with comprehensive, codebase-grounded content |

## Pass Criteria

Each evaluation script defines which criteria are blocking vs. recommended. The overall PASS/FAIL logic is documented at the bottom of each script.
