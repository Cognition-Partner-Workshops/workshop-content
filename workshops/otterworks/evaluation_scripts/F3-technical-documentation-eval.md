# Evaluation: F3 — Technical Documentation Generation

## Task

Verify the participant generated at least 2 service-level READMEs grounded in the actual codebase.

## Evaluation Prompt

```
You are evaluating a workshop participant's work on the OtterWorks Technical
Documentation Generation lab. Check their branch ({{branch_or_pr}}) in
{{git_repo}}.

## Criteria

### Blocking (must pass for PASS)

1. **Service READMEs Created**: At least 2 new `README.md` files created
   in service directories (e.g., `services/notification-service/README.md`,
   `services/analytics-service/README.md`).

2. **Comprehensive Content**: Each README covers at minimum: service purpose,
   tech stack, local development setup, and how to run tests.

3. **Codebase Grounded**: Documentation references real files, real commands,
   and real configuration from the service — not generic boilerplate. For
   example, the local development section should reference actual Makefile
   targets or docker-compose entries.

### Recommended (bonus, not blocking)

4. **Project Structure Documented**: README includes a directory layout
   showing key source files and their purposes.

5. **Environment Variables Listed**: All environment variables the service
   reads are documented with descriptions and defaults.

6. **Onboarding Guide**: A `docs/onboarding.md` or similar guide was
   created for new developer orientation.

7. **OpenAPI Spec Generated**: An OpenAPI spec was generated for a service
   that previously lacked one in `shared/openapi/`.

## Output

Return `RESULT: PASS` if all blocking criteria are met.
Return `RESULT: FAIL` with per-criterion breakdown.
```
