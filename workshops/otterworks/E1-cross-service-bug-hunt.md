# Lab: Cross-Service Bug Hunt

## Objective

Use the OtterWorks exploratory QA report as a bug backlog and fix real cross-service bugs that span the API gateway, backend services, and frontend applications.

## What's Wrong

An exploratory QA pass (`docs/exploratory-qa-report.md`) documented several confirmed bugs and UX issues across the OtterWorks platform. These are not planted chaos-engineering failures — they are real integration issues that exist in the codebase. Fixing them requires tracing request flows across multiple services and understanding how the API gateway, backend services, and frontends interact.

The highest-priority bugs documented are:

1. **Notifications API returns 400 everywhere** — Every authenticated page calls `GET /api/v1/notifications/unread-count` and `GET /api/v1/notifications?page=1&pageSize=20`, both returning `400 Bad Request`. The notification-service health endpoint is healthy, but the functional API rejects requests. This could be a missing header, a request validation issue, or an API gateway routing problem.

2. **Settings endpoint is missing** — The settings page calls `GET /api/v1/settings` which returns `404 Not Found`. Either the backend endpoint does not exist, the route is not wired in the API gateway, or both.

3. **Observability sidecars are unhealthy** — The `fluent-bit` and `otel-collector` containers run but report unhealthy status. The core app works, but log shipping and trace collection are degraded.

4. **Text file preview does not show content** — File detail page loads for `.txt` files but does not render the file content inline. Users must download to view.

## Where to Look

- `docs/exploratory-qa-report.md` — **Read this first.** The complete QA report with all confirmed bugs, UI issues, and product gaps.
- `docs/api-route-matrix.md` — API route inventory showing which routes exist vs. which are wired through the gateway.
- `services/api-gateway/internal/proxy/router.go` — Gateway route configuration. Check which prefixes are routed to which services.
- `services/notification-service/src/main/kotlin/com/otterworks/notification/` — Notification service implementation. Check request validation on the notification list and unread-count endpoints.
- `frontend/web-app/src/app/notifications/` — Frontend notification page. Check what headers and parameters it sends.
- `frontend/web-app/src/app/settings/` — Settings page. What endpoint does it call?
- `observability/otel/otel-collector-config.yml` — OpenTelemetry collector configuration.
- `observability/logging/fluentbit-config.yml` — Fluent Bit log shipper configuration.
- `docker-compose.yml` — Service environment variables and health check definitions.

## What Done Looks Like

- [ ] Read the QA report and triaged the bugs by root cause
- [ ] Fixed at least 2 of the documented bugs:
  - Notifications API returns 200 with correct data (or a clear, documented reason why 400 is expected)
  - Settings endpoint is routed and functional (or the frontend gracefully handles its absence)
- [ ] Each fix was verified by tracing the request through the gateway to the backend service
- [ ] Tests pass for any modified services
- [ ] Optionally: fixed the observability sidecar health issues or the file preview bug

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Read `docs/exploratory-qa-report.md` for the full bug list. Start with the notifications bug — it is the most impactful because it affects every authenticated page. Check `docs/api-route-matrix.md` to see if the notification routes are listed and whether there are known gateway routing gaps.

### Hint 2 — Specific Direction

For the notifications bug, trace the request: frontend sends `GET /api/v1/notifications/unread-count` → API gateway routes to notification-service → notification-service validates the request. Check what headers the frontend sends (especially `X-User-ID` or `Authorization`) and what the notification-service expects. The 400 may be a missing or malformed header.

### Hint 3 — Approach

Ask Devin: "Read `docs/exploratory-qa-report.md` and `docs/api-route-matrix.md`. The notifications API returns 400 on every authenticated page. Trace the request flow from the frontend (`frontend/web-app/src/app/notifications/`) through the API gateway (`services/api-gateway/internal/proxy/router.go`) to the notification-service. Identify why the request is rejected and fix it."

## Time Budget

- ~10 minutes: Read the QA report and triage
- ~25-30 minutes per bug fix (trace request flow, identify root cause, implement fix)
- ~45-60 minutes total for 2 bug fixes
