# Evaluation: B3 — Dependency Lifecycle Campaign

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check the participant's Devin session history and any PRs created. Evaluate whether the dependency lifecycle campaign was executed using the divide-and-conquer pattern.

## Criteria (all must pass unless marked recommended)

1. **Campaign scoped:** Participant listed all services with their language, runtime version, and dependency file. The inventory should cover at least 10 services.

2. **Playbook created:** A Devin Playbook named `dependency-audit-per-service` (or similar) was created with steps for: reading dependency files, checking for CVEs, categorizing findings, and applying fixes.

3. **Child sessions launched:** At least 2 child sessions were launched (or simulated with sequential sessions), each targeting a different service.

4. **Findings produced:** Child sessions produced PRs or findings reports with categorized findings (CRITICAL/HIGH/LOW).

5. **Known vulnerabilities found:** At least one of these known issues was identified: report-service Guava 28.0-jre CVEs, collab-service lodash 4.17.20, or search-service urllib3 1.26.5.

6. **At least 3 services audited:** Child sessions covered services across different language ecosystems (e.g., Java + Node.js + Python). *(recommended)*

7. **Scheduling discussed:** Participant discussed running this campaign on a recurring schedule (e.g., weekly or monthly) for continuous dependency lifecycle management. *(recommended)*

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. Campaign scoped: PASS/FAIL — [details]
2. Playbook created: PASS/FAIL — [details]
3. Child sessions launched: PASS/FAIL — [details]
4. Findings produced: PASS/FAIL — [details]
5. Known vulnerabilities found: PASS/FAIL — [details]
6. At least 3 services audited: PASS/FAIL — [details]
7. Scheduling discussed: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS only if criteria 1-5 all pass (6-7 are recommended but not blocking).
