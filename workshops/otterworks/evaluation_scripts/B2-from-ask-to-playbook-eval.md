# Evaluation: B2 — From Ask to Playbook

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check the participant's Devin session history and any PRs created. Evaluate whether the Ask → Session → Playbook progression was completed by checking each criterion below.

## Criteria (all must pass unless marked recommended)

1. **Step 1 completed:** Participant ran the ad-hoc ask prompt against the search-service suggest endpoint and got a fix (likely the `s['_rankingScore']` KeyError in `services/search-service/app/api/search.py`).

2. **Step 2 completed:** Participant ran a refined prompt with explicit investigation methodology (runbook reference, chaos flag check, request tracing, test verification).

3. **Playbook created:** A Devin Playbook named `otterworks-incident-investigation` (or similar) was created with at least 5 named steps encoding investigation methodology.

4. **Playbook steps are specific:** Steps reference concrete OtterWorks artifacts (runbook paths, chaos flags, test commands) rather than generic instructions like "investigate the issue."

5. **Progression articulated:** Participant can explain the difference between ad-hoc ask vs. Playbook — that Playbooks encode institutional methodology for consistent results across team members. *(recommended)*

6. **Step 4 completed:** Playbook was invoked on a different incident scenario (e.g., notification-service 400 errors) and produced consistent results. *(recommended)*

7. **Automation discussed:** Participant discussed connecting the Playbook to event-driven triggers (e.g., Grafana alert webhook from B1). *(recommended)*

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. Step 1 completed: PASS/FAIL — [details]
2. Step 2 completed: PASS/FAIL — [details]
3. Playbook created: PASS/FAIL — [details]
4. Playbook steps are specific: PASS/FAIL — [details]
5. Progression articulated: PASS/FAIL — [details]
6. Step 4 completed: PASS/FAIL — [details]
7. Automation discussed: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS only if criteria 1-4 all pass (5-7 are recommended but not blocking).
