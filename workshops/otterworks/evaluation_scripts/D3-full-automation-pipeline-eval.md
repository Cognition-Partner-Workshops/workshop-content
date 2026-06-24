# Evaluation: D3 — Full Automation Pipeline (Capstone)

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check the participant's Devin session history, any workflows or configurations created, and documentation produced. Evaluate whether the participant designed a multi-layer automation strategy.

## Criteria (all must pass unless marked recommended)

1. **At least one layer implemented:** Participant designed or implemented at least one of the three automation layers: event-driven (issue-to-Devin workflow), scheduled (monthly campaign), or on-demand (new Playbook).

2. **Three-layer strategy understood:** Participant can explain the three automation layers and which type of work each handles: event-driven (real-time response to signals), scheduled (recurring maintenance), on-demand (human-initiated with Playbooks).

3. **Progression articulated:** Participant can explain the Ask → Session → Playbook → Automation progression and how teams mature their Devin adoption through these stages.

4. **Bot-loop prevention included:** Any event-driven automation designed includes a mechanism to prevent infinite loops (e.g., skip bot-authored issues/PRs).

5. **Issue-to-Devin workflow implemented:** The `.github/workflows/issue-to-devin.yml` workflow was written with: label trigger, issue context extraction, Devin API call, issue comment with session link, and bot-loop prevention. *(recommended)*

6. **Playbook created:** A new Playbook was created for on-demand use (e.g., service scaffolding, incident investigation, or another reusable pattern). *(recommended)*

7. **Strategy document produced:** Participant created a summary document describing the complete automation strategy — what is automated, what triggers it, what Devin does, what humans review. *(recommended)*

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. At least one layer implemented: PASS/FAIL — [details]
2. Three-layer strategy understood: PASS/FAIL — [details]
3. Progression articulated: PASS/FAIL — [details]
4. Bot-loop prevention included: PASS/FAIL — [details]
5. Issue-to-Devin workflow implemented: PASS/FAIL — [details]
6. Playbook created: PASS/FAIL — [details]
7. Strategy document produced: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS only if criteria 1-4 all pass (5-7 are recommended but not blocking).
