# Evaluation: E2 — API Gateway Route Completion

## Task

Verify the participant added missing API gateway routes for at least 2 service prefixes.

## Evaluation Prompt

```
You are evaluating a workshop participant's work on the OtterWorks API Gateway
Route Completion lab. Check their branch ({{branch_or_pr}}) in {{git_repo}}.

## Criteria

### Blocking (must pass for PASS)

1. **Routes Added**: At least 2 new route prefixes added to
   `services/api-gateway/internal/proxy/router.go` (from the documented
   gaps: `/api/v1/templates`, `/api/v1/folders`, `/api/v1/reports`,
   `/api/v1/preferences`).

2. **Pattern Consistency**: New routes follow the existing routing pattern
   (environment variable for backend URL, reverse proxy setup, middleware
   chain).

3. **Config Updated**: `docker-compose.yml` or gateway config updated with
   any new environment variables for backend service URLs.

### Recommended (bonus, not blocking)

4. **Gateway Tests Pass**: `cd services/api-gateway && go test ./...`
   passes after changes.

5. **API Flow Tests**: New or updated tests in `tests/api/` exercise the
   new routes end-to-end.

6. **Smoke Verified**: PR description mentions verifying the new routes
   return responses from the backend services (not 404).

## Output

Return `RESULT: PASS` if all blocking criteria are met.
Return `RESULT: FAIL` with per-criterion breakdown.
```
