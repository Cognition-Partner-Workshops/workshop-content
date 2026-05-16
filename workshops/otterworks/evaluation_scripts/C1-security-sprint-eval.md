# Evaluation: C1 — Monorepo Security Sprint

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check out the specified branch/PR in `{{git_repo}}`. Evaluate whether the security sprint lab has been completed by checking each criterion below.

Compare the branch against `main` to identify what the participant changed.

## Criteria (all must pass for overall PASS)

1. **CVEs remediated:** At least 2 CRITICAL or HIGH CVEs have been remediated by updating dependency versions. Check these files for version changes:
   - `services/collab-service/package.json` — look for `lodash` or `ws` version bumps
   - `services/search-service/requirements.txt` — look for `urllib3` or `requests` version bumps
   - `services/admin-service/Gemfile` — look for `activestorage` or other gem version bumps
   - Any other dependency file that was modified

2. **Dependency files consistent:** If `package.json` was updated, `package-lock.json` must also be updated (run `npm install --package-lock-only` or equivalent). If `Gemfile` was updated, `Gemfile.lock` must also be updated. If `requirements.txt` was updated, verify versions are pinned.

3. **No breaking changes:** The updated dependencies should not introduce breaking changes. If tests exist for the affected service, verify they still pass. At minimum, check that the version bump is within a compatible range (e.g., not jumping major versions without code changes).

4. **Trivyignore cleaned up:** `.trivyignore` has been modified. At least one overly broad suppression has been removed or narrowed. Check for entries that were commented as "Bulk ignore" or "revisit in Q4" — these should be addressed.

5. **Justified suppressions documented:** Any remaining `.trivyignore` entries have comments explaining why they are suppressed (not just `# Bulk ignore`).

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. CVEs remediated: PASS/FAIL — [list specific CVEs and version changes]
2. Dependency files consistent: PASS/FAIL — [details]
3. No breaking changes: PASS/FAIL — [details]
4. Trivyignore cleaned up: PASS/FAIL — [what was removed/narrowed]
5. Justified suppressions documented: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS if criteria 1, 3, and 4 all pass (2 and 5 are recommended but not blocking).
