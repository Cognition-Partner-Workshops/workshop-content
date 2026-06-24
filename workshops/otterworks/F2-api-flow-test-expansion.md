# Lab: API Flow Test Expansion

## Objective

Expand the OtterWorks black-box API flow test suite by writing new end-to-end tests for user flows that are documented in the API route matrix but do not yet have test coverage.

## What's Wrong

OtterWorks has a black-box API test suite in `tests/api/` that exercises service flows through the API gateway — the same path real users take. The suite currently covers:

| Test File | Flow | Status |
|-----------|------|--------|
| `test_auth_flow.py` | Registration, login, profile, refresh, logout | Covered |
| `test_document_flow.py` | Document CRUD, versions, export, comments | Covered |
| `test_file_flow.py` | File upload, metadata, download, versions, trash, share | Covered |
| `test_search_flow.py` | Search, suggest, advanced search, indexing | Covered |
| `test_collaboration_flow.py` | Collaboration presence APIs | Covered |
| `test_audit_analytics_report_flow.py` | Audit events, analytics, reports | Covered |
| `test_notification_admin_gateway_flow.py` | Notifications, admin, gateway health | Covered |
| `test_side_effect_flow.py` | Cross-service side effects (events, indexing) | Covered |
| `test_degradation_flow.py` | Graceful degradation under failure | Covered |
| `test_websocket_collaboration_flow.py` | WebSocket real-time collaboration | Covered |

However, `docs/api-route-matrix.md` identifies several flows that are documented but lack test coverage. The route matrix also identifies gateway routing gaps — some of these may now be fixed (check first) or are still present. Additionally, existing tests may not cover edge cases like cross-user access control, concurrent operations, or error paths.

## Where to Look

- `docs/api-route-matrix.md` — **Read this first.** Complete route inventory with coverage annotations. Look for rows marked "Planned" or "Gap."
- `tests/api/` — Existing test suite. Read `conftest.py` for fixtures, helpers, and patterns.
- `tests/api/requirements.txt` — Test dependencies (requests, pytest, etc.).
- `tests/api/pytest.ini` — Test configuration.
- `tests/contract/test_search_contract.py` — Contract test example (validates responses against OpenAPI spec).
- `shared/openapi/` — OpenAPI specs for reference.
- `Makefile` — `test-api-flows` target runs the test suite against the local gateway, `test-api-flows-collect` collects tests without running.

## What Done Looks Like

- [ ] Read the existing test suite and understood the test patterns (auth helper, fixture setup, assertion style)
- [ ] Identified at least 2 untested or under-tested flows from the API route matrix
- [ ] Wrote at least 3 new test functions that exercise flows not covered by existing tests
- [ ] New tests follow the existing patterns: use `conftest.py` fixtures, go through the API gateway, assert specific status codes and response shapes
- [ ] Tests pass when run with `make test-api-flows` (with the local stack running)
- [ ] Optionally: added a new test file for an entirely new flow (e.g., `test_template_flow.py`, `test_folder_flow.py`)

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Read `tests/api/conftest.py` to understand the test infrastructure: how auth tokens are obtained, how the gateway URL is configured, and what helper fixtures are available. Then read `docs/api-route-matrix.md` to find flows marked as "Planned" or "Gap."

### Hint 2 — Specific Direction

Good candidates for new tests:
- **Template flow:** `GET /api/v1/templates`, `POST /api/v1/templates`, `POST /api/v1/documents/from-template/{id}` (if the gateway route exists)
- **Cross-user access control:** Register two users, create a document as User A, verify User B cannot access it
- **Folder operations:** Create folder, move file into folder, list folder contents, delete folder
- **Audit trail verification:** Perform an action (file upload), then verify an audit event was created via the audit API

### Hint 3 — Approach

Ask Devin: "Read `tests/api/conftest.py` and `tests/api/test_document_flow.py` to understand the test patterns. Then read `docs/api-route-matrix.md` and write 3 new test functions: (1) a cross-user document access control test, (2) a side-effect verification test that confirms file upload creates an audit event, and (3) a template workflow test. Follow the existing patterns and add them to the appropriate test file."

## Time Budget

- ~10 minutes: Read existing tests and the API route matrix
- ~15-20 minutes per new test function
- ~45-60 minutes for 3 new tests plus verification
