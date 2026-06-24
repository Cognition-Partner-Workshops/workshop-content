# Lab: API Contract Audit

## Objective

Find and fix mismatches between OpenAPI specs, event schemas, and actual service implementations.

## What's Wrong

The OtterWorks codebase has OpenAPI specifications in `reference/openapi/` and event schemas in `reference/events/schemas/`. These specs are supposed to be the source of truth for how services communicate. But some specs have drifted from the actual implementations:

1. **Notification events schema drift:** The schema at `reference/events/schemas/notification-events.json` uses `notificationId` (camelCase) for event fields, but the notification-service actually publishes events with `notification_id` (snake_case). This mismatch means any consumer validating against the schema will reject real events.

2. **Audit events schema drift:** The schema at `reference/events/schemas/audit-events.json` marks `timestamp` as a required field, but the audit-service sometimes omits it for batch events. This means batch events fail schema validation even though they are intentionally structured differently.

3. **Potential OpenAPI drift:** The OpenAPI specs in `reference/openapi/` may not perfectly match the actual service endpoint behaviors. Validating this requires running the services and comparing actual responses to the spec.

## Where to Look

- `reference/openapi/search-service.yaml` — OpenAPI spec for search-service
- `reference/openapi/document-service.yaml` — OpenAPI spec for document-service
- `reference/openapi/notification-service.yaml` — OpenAPI spec for notification-service
- `reference/events/schemas/notification-events.json` — Event schema with camelCase/snake_case drift
- `reference/events/schemas/audit-events.json` — Event schema with required field drift
- `reference/events/schemas/collaboration-events.json` — Collaboration event schema (should be correct)
- `reference/events/schemas/document-events.json` — Document event schema (reference, should be correct)
- `reference/events/schemas/file-events.json` — File event schema (reference, should be correct)
- `services/notification-service/src/main/kotlin/com/otterworks/notification/` — Actual notification event publishing code
- `services/audit-service/` — Actual audit event publishing code
- `docs/labs/contract-audit-guide.md` — **Read this first.** Documents the known drift areas.
- `tests/contract/test_search_contract.py` — Example contract test (validates search-service against its OpenAPI spec)

## What Done Looks Like

- [ ] Identified the camelCase/snake_case mismatch in notification event schemas
- [ ] Fixed either the schema or the service code to match (and documented which direction was chosen and why)
- [ ] Identified the missing `timestamp` in audit batch events
- [ ] Fixed either the schema (make timestamp optional for batch events) or the service (always include timestamp)
- [ ] Verified at least one OpenAPI spec against the actual service implementation
- [ ] Optionally: wrote or ran contract tests to automate the verification

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started
Read `docs/labs/contract-audit-guide.md` for the full list of known drift areas. Start with the notification event schema — the camelCase vs. snake_case mismatch is the most straightforward to find.

### Hint 2 — Specific Direction
Compare `reference/events/schemas/notification-events.json` field names against the actual event publishing code in `services/notification-service/src/main/kotlin/com/otterworks/notification/`. Look at the data class or model that defines the event structure.

### Hint 3 — Approach
Ask Devin: "Compare the event schema at `reference/events/schemas/notification-events.json` with the actual event publishing code in the notification-service. Find any field naming mismatches and fix the schema to match the implementation (or vice versa). Then do the same for `reference/events/schemas/audit-events.json` and the audit-service."

## Time Budget
~45-60 minutes to find and fix both drift issues plus verify one OpenAPI spec.
