# Evaluation: D1 — Event-Driven SAST Pipeline

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check the participant's Devin session history and any documentation or code produced. Evaluate whether the participant traced the event-driven SAST pipeline and proposed an improvement.

## Criteria (all must pass unless marked recommended)

1. **Trivy path traced:** Participant can explain the complete Trivy flow: PR opened → `sast-scan` job runs Trivy → findings detected → Devin webhook called with CVE context → Devin pushes fix → `synchronize` event re-triggers scan → verified or escalated after `MAX_FIX_ATTEMPTS`.

2. **SonarCloud path traced:** Participant can explain the SonarCloud flow: PR opened → `sonarcloud-gate` job runs analysis → quality gate checked → if failed, Devin webhook called → one-time remediation attempt.

3. **Bot-loop prevention explained:** Participant can explain the `github.actor != 'devin-ai-integration[bot]'` check and why it is critical — without it, Devin's fix push would trigger another scan which triggers another Devin session, creating an infinite loop.

4. **Escalation path understood:** Participant can explain the `MAX_FIX_ATTEMPTS` mechanism and the escalation path (create GitHub Issue when automated remediation is insufficient).

5. **Extension proposed:** Participant proposed or implemented at least one extension: adding a third scanner path (e.g., secret scanning with gitleaks), improving the Devin prompt with richer context, adding notification steps, or other improvements. *(recommended)*

6. **Toolchain-agnostic pattern articulated:** Participant can describe the pattern as `[Scanner] → webhook → [Trigger Layer] → Devin → PR` and explain how swapping scanners or CI systems doesn't change the topology. *(recommended)*

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. Trivy path traced: PASS/FAIL — [details]
2. SonarCloud path traced: PASS/FAIL — [details]
3. Bot-loop prevention explained: PASS/FAIL — [details]
4. Escalation path understood: PASS/FAIL — [details]
5. Extension proposed: PASS/FAIL — [details]
6. Toolchain-agnostic pattern articulated: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS only if criteria 1-4 all pass (5-6 are recommended but not blocking).
