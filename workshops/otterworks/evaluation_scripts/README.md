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
| B2 — From Ask to Playbook | `B2-from-ask-to-playbook-eval.md` | Playbook created with named methodology steps, before/after comparison completed |
| B3 — Dependency Lifecycle | `B3-dependency-lifecycle-eval.md` | Campaign scoped, playbook created, child sessions launched, findings categorized |
| C1 — Security Sprint | `C1-security-sprint-eval.md` | CVEs remediated, dependency files consistent, trivyignore cleaned, suppressions documented |
| C2 — Contract Audit | `C2-contract-audit-eval.md` | Notification and audit event schema drift identified and fixed |
| C3 — Scheduled Automation | `C3-scheduled-automation-eval.md` | Baseline established, scheduled session designed with specific prompt and triage criteria |
| D1 — Event-Driven SAST | `D1-event-driven-sast-eval.md` | SAST pipeline flow traced, bot-loop prevention explained, escalation path understood |
| D2 — Knowledge & Review | `D2-knowledge-and-devin-review-eval.md` | Knowledge notes created, before/after comparison completed, shared context layer understood |
| D3 — Full Automation (Capstone) | `D3-full-automation-pipeline-eval.md` | At least one automation layer designed, three-layer strategy understood, progression articulated |

## Pass Criteria

Each evaluation script defines which criteria are blocking vs. recommended. The overall PASS/FAIL logic is documented at the bottom of each script.
