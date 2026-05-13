# Lab: Test Coverage and API Contract Audit

This lab covers two activities that can be done independently.

---

## Activity C2: API Contract Audit

### Objective

Find and fix mismatches between OpenAPI specs, event schemas, and actual service implementations.

### What's Wrong

The OtterWorks codebase has OpenAPI specifications in `shared/openapi/` and event schemas in `shared/events/schemas/`. These specs are supposed to be the source of truth for how services communicate. But some specs have drifted from the actual implementations:

1. **Notification events schema drift:** The schema at `shared/events/schemas/notification-events.json` uses `notificationId` (camelCase) for event fields, but the notification-service actually publishes events with `notification_id` (snake_case). This mismatch means any consumer validating against the schema will reject real events.

2. **Audit events schema drift:** The schema at `shared/events/schemas/audit-events.json` marks `timestamp` as a required field, but the audit-service sometimes omits it for batch events. This means batch events fail schema validation even though they are intentionally structured differently.

3. **Potential OpenAPI drift:** The OpenAPI specs in `shared/openapi/` may not perfectly match the actual service endpoint behaviors. Validating this requires running the services and comparing actual responses to the spec.

### Where to Look

- `shared/openapi/search-service.yaml` — OpenAPI spec for search-service
- `shared/openapi/document-service.yaml` — OpenAPI spec for document-service
- `shared/openapi/notification-service.yaml` — OpenAPI spec for notification-service
- `shared/events/schemas/notification-events.json` — Event schema with camelCase/snake_case drift
- `shared/events/schemas/audit-events.json` — Event schema with required field drift
- `shared/events/schemas/collaboration-events.json` — Collaboration event schema (should be correct)
- `shared/events/schemas/document-events.json` — Document event schema (reference, should be correct)
- `shared/events/schemas/file-events.json` — File event schema (reference, should be correct)
- `services/notification-service/src/main/kotlin/com/otterworks/notification/` — Actual notification event publishing code
- `services/audit-service/` — Actual audit event publishing code
- `docs/labs/contract-audit-guide.md` — **Read this first.** Documents the known drift areas.
- `tests/contract/test_search_contract.py` — Example contract test (validates search-service against its OpenAPI spec)

### What Done Looks Like

- [ ] Identified the camelCase/snake_case mismatch in notification event schemas
- [ ] Fixed either the schema or the service code to match (and documented which direction was chosen and why)
- [ ] Identified the missing `timestamp` in audit batch events
- [ ] Fixed either the schema (make timestamp optional for batch events) or the service (always include timestamp)
- [ ] Verified at least one OpenAPI spec against the actual service implementation
- [ ] Optionally: wrote or ran contract tests to automate the verification

### Hints

#### Hint 1 — Getting Started
Read `docs/labs/contract-audit-guide.md` for the full list of known drift areas. Start with the notification event schema — the camelCase vs. snake_case mismatch is the most straightforward to find.

#### Hint 2 — Specific Direction
Compare `shared/events/schemas/notification-events.json` field names against the actual event publishing code in `services/notification-service/src/main/kotlin/com/otterworks/notification/`. Look at the data class or model that defines the event structure.

#### Hint 3 — Approach
Ask Devin: "Compare the event schema at `shared/events/schemas/notification-events.json` with the actual event publishing code in the notification-service. Find any field naming mismatches and fix the schema to match the implementation (or vice versa). Then do the same for `shared/events/schemas/audit-events.json` and the audit-service."

### Time Budget
~45-60 minutes to find and fix both drift issues plus verify one OpenAPI spec.

---

## Activity C3: Test Coverage Blitz

### Objective

Identify the service with weakest test coverage, then use Devin to add meaningful tests.

### What's Wrong

Test coverage varies wildly across the OtterWorks services. Some services have good coverage with unit and integration tests. Others have minimal or no tests. Coverage configuration has been added across services (`.coveragerc`, Jest config, Jacoco, SimpleCov, etc.) but many services still have gaps.

The goal is not to achieve 100% coverage everywhere — it is to identify the weakest service and meaningfully improve its test suite.

### Where to Look

- Each service's test directory (patterns vary by language):
  - Python: `pytest --cov=app --cov-report=term-missing`
  - Node.js: `npm test -- --coverage`
  - Go: `go test -cover ./...`
  - Java: `./gradlew test jacocoTestReport` or `mvn test`
  - Rust: `cargo test`
  - Ruby: `bundle exec rspec` (SimpleCov report)
  - C#: `dotnet test`
- `Makefile` — Run `make test-coverage` to get coverage reports for all services
- Coverage config files:
  - `services/document-service/.coveragerc`
  - `services/search-service/.coveragerc`
  - `services/collab-service/package.json` (jest coverage config)
  - `services/auth-service/build.gradle` (Jacoco config)

### What Done Looks Like

- [ ] Ran `make test-coverage` (or individual service test commands) and reviewed coverage reports
- [ ] Identified the service with the weakest coverage (fewest tests, lowest line coverage, or most untested critical paths)
- [ ] Added at least 3-5 meaningful tests to the weakest service:
  - Tests cover actual business logic (not just "function exists" assertions)
  - Tests follow the existing test patterns and framework in the service
  - Tests pass when run with the standard test command
- [ ] Coverage improved measurably for the target service

### Hints

#### Hint 1 — Getting Started
Run `make test-coverage` to get a baseline for all services. Look for services with 0% coverage, very few test files, or large untested modules.

#### Hint 2 — Specific Direction
The document-service (Python/FastAPI) and notification-service (Kotlin/Ktor) tend to have room for improvement. Look at what endpoints and service methods exist vs. which ones have test coverage.

#### Hint 3 — Approach
Ask Devin: "Run `make test-coverage` and identify the service with the lowest test coverage. Then read the service's source code and existing tests (if any), and add 5 meaningful unit or integration tests that cover the most critical untested code paths. Follow the existing test framework and patterns."

### Time Budget
- ~15 minutes: Run coverage reports, identify the weakest service
- ~30-45 minutes: Write and verify 3-5 meaningful tests
- Advanced participants: tackle multiple services or add integration tests
