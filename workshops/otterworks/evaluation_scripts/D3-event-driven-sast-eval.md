# Evaluation: D3 — Event-Driven SAST Pipeline

## Task

Verify the participant understands the event-driven SAST remediation pipeline and proposed an improvement.

## Evaluation Prompt

```
You are evaluating a workshop participant's work on the OtterWorks Event-Driven
SAST Pipeline lab. Check their branch ({{branch_or_pr}}) in {{git_repo}}.

## Criteria

### Blocking (must pass for PASS)

1. **Flow Understanding**: The participant can explain (via PR description,
   commit messages, or documentation) the end-to-end flow: PR opened →
   scanner → findings → Devin webhook → fix → re-scan → resolved or escalated.

2. **Bot-Loop Prevention**: The participant documented or explained why PRs
   from `devin-ai-integration[bot]` are skipped and how re-scans still work
   on human PRs with Devin commits.

3. **Improvement Proposed**: At least one concrete improvement to the pipeline
   was proposed or implemented (new scanner path, improved Devin prompt,
   notifications, SBOM generation, etc.).

### Recommended (bonus, not blocking)

4. **Improvement Implemented**: The proposed improvement is committed as a
   code change (workflow modification, new configuration, etc.).

5. **Escalation Path Documented**: The participant explained what happens
   after MAX_FIX_ATTEMPTS and why SonarCloud is one-time only.

## Output

Return `RESULT: PASS` if all blocking criteria are met.
Return `RESULT: FAIL` with per-criterion breakdown.
```
