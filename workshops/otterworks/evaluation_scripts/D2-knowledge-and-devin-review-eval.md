# Evaluation: D2 — Knowledge Layer & Devin Review

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check the participant's Devin session history, Knowledge notes configuration, and any PRs created. Evaluate whether the participant demonstrated the before/after value of Knowledge notes and configured Devin Review.

## Criteria (all must pass unless marked recommended)

1. **Baseline captured (Step 1):** Participant ran the health endpoint prompt against notification-service WITHOUT Knowledge notes and observed the output.

2. **Knowledge notes created:** At least 2 Knowledge notes were created with specific, actionable content about OtterWorks conventions (e.g., logging libraries per language, health endpoint patterns, error envelope format, architecture decisions).

3. **Notes are specific:** Knowledge notes reference concrete OtterWorks conventions (e.g., "Python services use structlog," "health endpoints at /health and /health/ready," "services communicate via SNS/SQS for async events") rather than generic advice like "follow best practices."

4. **Comparison completed (Step 3):** Participant re-ran the same health endpoint prompt WITH Knowledge notes and can describe the difference in output quality.

5. **Shared context layer understood:** Participant can name the components of the shared context layer: Knowledge notes, Playbooks, Blueprints, MCP servers, Secrets. *(recommended)*

6. **Devin Review configured:** Devin Review was enabled for the otterworks repo and participant observed review comments on at least one PR. *(recommended)*

7. **Compound effect articulated:** Participant can explain the ROI of Knowledge notes — one note improves every future session for every team member in the org. *(recommended)*

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. Baseline captured: PASS/FAIL — [details]
2. Knowledge notes created: PASS/FAIL — [details]
3. Notes are specific: PASS/FAIL — [details]
4. Comparison completed: PASS/FAIL — [details]
5. Shared context layer understood: PASS/FAIL — [details]
6. Devin Review configured: PASS/FAIL — [details]
7. Compound effect articulated: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS only if criteria 1-4 all pass (5-7 are recommended but not blocking).
