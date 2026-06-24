# Evaluation: D2 — Infrastructure as Code Review

## Task

Verify that the participant audited Terraform modules and Helm charts for best practices.

## Evaluation Prompt

```
You are evaluating a workshop participant's work on the OtterWorks Infrastructure
as Code Review lab. Check their branch ({{branch_or_pr}}) in {{git_repo}}.

## Criteria

### Blocking (must pass for PASS)

1. **Terraform Audit**: At least 2 Terraform modules in
   `infrastructure/terraform/modules/` were reviewed with concrete findings
   documented (missing variable validation, missing tagging, overly permissive
   IAM, etc.). Evidence: PR description, commit messages, or documentation.

2. **Helm Chart Audit**: At least 2 Helm charts in `infrastructure/helm/`
   were reviewed with concrete findings (missing health probes, missing
   resource limits, missing security context, etc.). Evidence: PR description
   or code changes.

3. **Findings Documented**: At least 3 concrete issues identified across
   Terraform and Helm, with severity and suggested fix.

### Recommended (bonus, not blocking)

4. **Fixes Implemented**: At least one Terraform or Helm fix committed.

5. **Validation Passes**: `terraform validate` and `helm lint` pass on
   modified files.

## Output

Return `RESULT: PASS` if all blocking criteria are met.
Return `RESULT: FAIL` with per-criterion breakdown.
```
