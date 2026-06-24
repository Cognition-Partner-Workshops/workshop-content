# Evaluation: A3 — Language Translation (Flask → FastAPI)

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check out the specified branch/PR in `{{git_repo}}`. Evaluate whether the search-service has been translated from Flask to FastAPI by checking each criterion below.

Compare the branch against `main` to identify what the participant changed in `services/search-service/`.

## Criteria (all must pass)

1. **Flask removed:** `services/search-service/requirements.txt` no longer lists `Flask`. FastAPI and Uvicorn (or equivalent ASGI server) are present.

2. **FastAPI app:** The main application entry point uses `FastAPI()` instead of `Flask(__name__)`. Check `services/search-service/app/` for `from fastapi import FastAPI` or equivalent.

3. **All endpoints preserved:** The following endpoints must still exist with the same HTTP methods and paths:
   - `GET /search`
   - `GET /suggest`
   - `POST /advanced-search`
   - `POST /index/document`
   - `POST /index/file`
   - `GET /health`
   - `GET /ready`
   Verify by reading the route definitions. Compare against the OpenAPI spec at `reference/openapi/search-service.yaml`.

4. **Pydantic models:** Response data uses Pydantic models (not raw `jsonify()` dicts). Check for `from pydantic import BaseModel` or `pydantic.BaseModel` subclasses.

5. **Async handlers:** At least the I/O-bound handlers (search, suggest, index) use `async def` instead of `def`.

6. **APIRouter usage:** Flask `Blueprint` replaced with FastAPI `APIRouter`. Check for `from fastapi import APIRouter` or `APIRouter()`.

7. **Prometheus metrics preserved:** The same Prometheus metric names that existed in the Flask version are present in the FastAPI version. Check for `prometheus_client` usage with matching metric names.

8. **Health/readiness endpoints functional:** The `/health` and `/ready` endpoints return appropriate JSON responses with status fields.

9. **Contract tests pass:** If `tests/contract/test_search_contract.py` exists, run it and verify it passes. If it requires a running service, note that and check the test code for correctness instead.

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. Flask removed: PASS/FAIL — [details]
2. FastAPI app: PASS/FAIL — [details]
3. All endpoints preserved: PASS/FAIL — [list any missing endpoints]
4. Pydantic models: PASS/FAIL — [details]
5. Async handlers: PASS/FAIL — [details]
6. APIRouter usage: PASS/FAIL — [details]
7. Prometheus metrics preserved: PASS/FAIL — [details]
8. Health/readiness endpoints: PASS/FAIL — [details]
9. Contract tests pass: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS only if criteria 1-6 and 8 all pass (7 and 9 are recommended but not blocking).
