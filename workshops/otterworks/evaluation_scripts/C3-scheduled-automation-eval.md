# Evaluation: C3 — Scheduled Security & Coverage Automation

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check the participant's Devin session history and scheduled session configuration. Evaluate whether the participant established a baseline, designed a scheduled session, and verified the first run.

## Criteria (all must pass unless marked recommended)

1. **Baseline established:** Participant ran security scans (`make security-scan` or equivalent) and coverage checks (`make test-coverage` or equivalent) and recorded current findings.

2. **Scan tooling understood:** Participant identified the scanning tools in the repo: Trivy for dependency CVEs, SonarCloud for code quality, and per-language audit tools (npm audit, pip-audit, bundle-audit).

3. **Scheduled session designed:** Participant wrote a scheduled session prompt that includes: specific Makefile targets to run, how to detect new findings vs. known ones, decision criteria for auto-fix vs. report-only, and expected output format.

4. **Session created:** A Devin scheduled session was created (or the configuration was documented with name, schedule, repo, and prompt).

5. **First run verified:** The scheduled session was triggered manually and produced output — either a findings summary, a PR with fixes, or both. *(recommended)*

6. **Event-driven complement discussed:** Participant can explain the difference between scheduled automation (C3) and event-driven automation (D1) — scheduled catches ecosystem drift, event-driven catches PR-level issues. *(recommended)*

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. Baseline established: PASS/FAIL — [details]
2. Scan tooling understood: PASS/FAIL — [details]
3. Scheduled session designed: PASS/FAIL — [details]
4. Session created: PASS/FAIL — [details]
5. First run verified: PASS/FAIL — [details]
6. Event-driven complement discussed: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS only if criteria 1-4 all pass (5-6 are recommended but not blocking).
