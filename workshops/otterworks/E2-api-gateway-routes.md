# Lab: API Gateway Route Completion

## Objective

Wire missing API routes through the Go API gateway so that all service endpoints are reachable from the frontend applications, and verify the new routes with black-box API flow tests.

## What's Wrong

The OtterWorks API gateway (`services/api-gateway/`) routes requests from the frontends to backend services based on URL prefix. However, several backend services expose endpoints that are not yet routed through the gateway. The `docs/api-route-matrix.md` documents these gaps:

1. **Templates** — The document-service exposes `GET /api/v1/templates` and `POST /api/v1/templates`, but the gateway only routes `/api/v1/documents` to the document-service. Template endpoints are unreachable from the frontend.

2. **Folders** — The file-service exposes `GET /api/v1/folders/{id}`, `POST /api/v1/folders`, etc., but the gateway only routes `/api/v1/files`. Folder CRUD operations go through direct service calls only.

3. **Reports** — The report-service is configured in `docker-compose.yml` (with `REPORT_SERVICE_URL`), but the gateway does not route `/api/v1/reports` to it.

4. **Preferences** — The notification-service exposes `/api/v1/preferences` for notification settings, but the gateway does not route this prefix.

These gaps mean the frontend must work around the gateway or the features are simply broken for end users.

## Where to Look

- `docs/api-route-matrix.md` — **Read this first.** Complete route inventory with gap annotations.
- `services/api-gateway/internal/proxy/router.go` — Route registration and reverse proxy logic. This is where new routes are added.
- `services/api-gateway/internal/config/config.go` — Service URL configuration (environment variables for each backend service).
- `services/api-gateway/cmd/server/main.go` — Server startup and middleware chain.
- `services/document-service/app/api/` — Document-service endpoints including templates.
- `services/file-service/src/handlers.rs` — File-service handlers including folder operations.
- `services/report-service/` — Report-service API endpoints.
- `services/notification-service/src/main/kotlin/com/otterworks/notification/` — Preferences endpoints.
- `docker-compose.yml` — Service URL environment variables passed to the gateway.
- `tests/api/` — Existing black-box API flow tests (reference for writing new tests).

## What Done Looks Like

- [ ] Added at least 2 new route prefixes to the API gateway (e.g., `/api/v1/templates`, `/api/v1/folders`, `/api/v1/reports`, `/api/v1/preferences`)
- [ ] Each new route correctly proxies to the backend service
- [ ] The gateway's configuration accepts the backend service URL via environment variable (consistent with existing patterns)
- [ ] `docker-compose.yml` is updated with any new environment variables
- [ ] The new routes pass basic smoke testing (e.g., `curl http://localhost:8080/api/v1/templates` returns a response from the document-service, not a 404)
- [ ] Gateway tests pass: `cd services/api-gateway && go test ./...`
- [ ] Optionally: wrote a new API flow test in `tests/api/` to exercise the new routes end-to-end

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Read `docs/api-route-matrix.md` to see the full gap list. Then read `services/api-gateway/internal/proxy/router.go` to understand the pattern for adding routes. The existing routes (e.g., `/api/v1/documents` → document-service) show the exact pattern to follow.

### Hint 2 — Specific Direction

Start with the templates route — it is the simplest because the document-service already handles both `/api/v1/documents` and `/api/v1/templates`, so you just need to add another prefix that routes to the same backend. The folders route is similar (same file-service backend, different prefix).

### Hint 3 — Approach

Ask Devin: "Read `docs/api-route-matrix.md` and `services/api-gateway/internal/proxy/router.go`. Add route prefixes for `/api/v1/templates` (→ document-service), `/api/v1/folders` (→ file-service), and `/api/v1/reports` (→ report-service) following the existing routing pattern. Update `docker-compose.yml` if any new environment variables are needed. Run `cd services/api-gateway && go test ./...` to verify."

## Time Budget

- ~10 minutes: Read the route matrix and understand the gateway routing pattern
- ~15-20 minutes per route: Add prefix, update config, verify
- ~45-60 minutes for 2-3 new routes plus smoke testing
