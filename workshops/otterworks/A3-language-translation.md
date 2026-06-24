# Lab: Language Translation (Flask to FastAPI)

## Objective

Translate the search-service from Flask (synchronous, WSGI) to FastAPI (async, ASGI) while preserving all API contracts verified by the OpenAPI spec.

## What's Wrong

The search-service (`services/search-service/`) is the only Python service still using Flask in a codebase where the document-service already uses FastAPI. This inconsistency means:
- The search-service cannot use `async`/`await` for I/O-bound MeiliSearch queries
- It uses a different request handling pattern from the document-service
- The two Python services cannot share middleware, dependency injection, or Pydantic models
- Flask's Blueprint pattern is less structured than FastAPI's router + dependency injection

The translation must preserve the exact API contract defined in the OpenAPI spec.

## Where to Look

- `services/search-service/app/api/search.py` — Main search endpoints (`/search`, `/suggest`, `/advanced-search`)
- `services/search-service/app/api/index.py` — Indexing endpoints (`/index/document`, `/index/file`)
- `services/search-service/app/api/health.py` — Health, readiness, and Prometheus metrics endpoints
- `services/search-service/app/services/meilisearch_client.py` — MeiliSearch client integration
- `services/search-service/requirements.txt` — Current Flask dependencies
- `reference/openapi/search-service.yaml` — **The contract.** Every endpoint, parameter, and response schema.
- `services/search-service/TRANSLATION_GUIDE.md` — **Read this first.** Lists every translation point with before/after code patterns.
- `tests/contract/test_search_contract.py` — Contract test that validates responses against the OpenAPI spec.
- `services/document-service/` — Reference implementation of a FastAPI service in this codebase (look at `app/api/documents.py` for patterns to follow)

## What Done Looks Like

- [ ] All Flask code replaced with FastAPI equivalents
- [ ] `requirements.txt` updated: Flask removed, FastAPI + Uvicorn added
- [ ] All endpoints preserved with identical paths, methods, parameters, and response shapes
- [ ] Pydantic models for request/response validation (replacing manual `jsonify` calls)
- [ ] `async def` handlers for I/O-bound operations
- [ ] FastAPI `APIRouter` replacing Flask `Blueprint`
- [ ] Dependency injection for MeiliSearch client (replacing `current_app.config`)
- [ ] Prometheus metrics preserved with same metric names
- [ ] Structured logging preserved (structlog)
- [ ] Health and readiness endpoints still functional
- [ ] Contract tests pass: `pytest tests/contract/test_search_contract.py`
- [ ] Existing search-service tests pass (or are updated for FastAPI test client)

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Read `services/search-service/TRANSLATION_GUIDE.md` for the complete translation mapping. Then look at `services/document-service/app/api/documents.py` for a working FastAPI example in this codebase.

### Hint 2 — Specific Direction

Start with the health endpoints (simplest) to establish the FastAPI app structure. Then translate the search endpoints. Save the chaos-related code in `suggest` for last — it has special error handling logic that needs careful translation.

### Hint 3 — Approach

Ask Devin to read `services/search-service/TRANSLATION_GUIDE.md`, the current Flask implementation in `services/search-service/app/api/search.py`, and the FastAPI reference at `services/document-service/app/api/documents.py`. Then ask it to translate the search-service to FastAPI while preserving the OpenAPI contract at `reference/openapi/search-service.yaml`.

## Time Budget

- ~60-90 minutes for the full translation
- The search and suggest endpoints are the most complex due to chaos flag handling
- Advanced participants can also translate the indexing endpoints and add async MeiliSearch calls
