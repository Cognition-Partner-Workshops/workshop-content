# Devin Local + Devin Cloud: Plan Locally, Execute at Scale

A linear demo showing how **Devin Local** (the Devin agent running in
Windsurf) and **Devin Cloud** (remote autonomous sessions) work together under
one pane of glass. Use Devin Local to explore and plan work across a polyglot
microservices system, then fan out parallel Devin Cloud sessions to execute —
each producing its own PR independently.

**Application:** [OtterWorks](https://github.com/Cognition-Partner-Workshops/otterworks)
— 11 microservices in 9 languages (Go, Java, Rust, Python, Node.js, Kotlin,
Scala, Ruby, C#) with React and Angular frontends.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Part 1 — Local Scoping with Devin Local](#part-1)
- [Part 2 — Create the Execution Plan](#part-2)
- [Part 3 — Fan Out to Devin Cloud](#part-3)
- [Part 4 — Watch PRs Arrive in Parallel](#part-4)
- [Alternative Cross-Cutting Tasks](#alternatives)
- [Key Takeaways](#key-takeaways)

---

<a id="prerequisites"></a>
## Prerequisites

- [Windsurf](https://windsurf.com) installed with the OtterWorks repo open
- Devin Cloud access with the
  [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) repo
  connected
- Ability to create Devin sessions (via UI or API)

---

<a id="part-1"></a>
## Part 1 — Local Scoping with Devin Local

Open the `otterworks` repo in Windsurf. Use Devin Local to explore the
codebase and understand the current state of error handling across all services.

Paste into the Devin Local chat in Windsurf:

```
Look at the otterworks microservices under services/. There are 11
services in different languages. I want to understand how each one
currently handles API errors — what format do they return, is there a
shared schema, and what middleware/framework features do they use?

Give me a summary table: service name, language, current error format,
and what framework mechanism they use for error responses.
```

Devin Local explores the codebase locally (fast — no network round-trips) and
returns a summary like:

| Service | Language | Current Error Format | Mechanism |
|---------|----------|---------------------|-----------|
| api-gateway | Go | `{"error": "message"}` | Custom `writeJSONError` helper |
| auth-service | Java | Spring default JSON | `@ExceptionHandler` + ResponseEntity |
| file-service | Rust | `{"error": "message"}` | Custom `ErrorResponse` struct |
| document-service | Python | `{"detail": "message"}` | FastAPI `HTTPException` |
| collab-service | Node.js | `{"error": "message"}` | Express error middleware |
| notification-service | Kotlin | Ktor default | StatusPages plugin |
| search-service | Python | `{"error": "message"}` | Flask `@app.errorhandler` |
| analytics-service | Scala | `{"error": "message"}` | Akka rejection handler |
| admin-service | Ruby | Rails default JSON | `rescue_from` in ApplicationController |
| audit-service | C# | ASP.NET ProblemDetails | `UseExceptionHandler` middleware |
| report-service | Java | Spring default JSON | `@ControllerAdvice` |

The key insight: every service returns errors differently. Only the audit-service
uses the RFC 7807 standard. The rest use ad-hoc formats.

---

<a id="part-2"></a>
## Part 2 — Create the Execution Plan

Still in Windsurf, ask Devin Local to generate the implementation plan and
per-service prompts:

```
Based on that analysis, I want to standardize all 11 services to use
RFC 7807 Problem Details for error responses. The schema should be:

{
  "type": "https://otterworks.dev/errors/<error-type>",
  "title": "Human-readable summary",
  "status": 404,
  "detail": "Specific instance details",
  "instance": "/files/abc-123"
}

For each service, generate a self-contained Devin prompt that:
1. Adds an error response middleware/handler using the service's native
   framework patterns
2. Updates existing error returns to use the new format
3. Adds a test verifying the error schema
4. Does NOT touch other services — scoped to that one service only

Output the prompts as a numbered list I can paste into Devin sessions.
```

Devin Local produces 11 targeted prompts, each tailored to the service's
language and framework. This is the planning step — Devin Local has full
codebase context and generates precise, service-specific instructions.

Example output for the Go API Gateway:

```
In the otterworks repo, update services/api-gateway to use RFC 7807
Problem Details for all error responses:

1. Create internal/errors/problem.go with a ProblemDetail struct:
   Type (string), Title (string), Status (int), Detail (string),
   Instance (string)
2. Add a WriteProblem(w, status, title, detail, instance) helper
3. Replace all writeJSONError calls in internal/middleware/ with
   WriteProblem — preserving the same HTTP status codes
4. Add Content-Type: application/problem+json header
5. Add a test in internal/errors/problem_test.go verifying the
   JSON structure matches RFC 7807
```

---

<a id="part-3"></a>
## Part 3 — Fan Out to Devin Cloud

Now take the 11 prompts Devin Local generated and launch parallel Devin Cloud
sessions. Still in Windsurf, paste this into the Devin Local chat:

```
Using the Devin API, launch 11 parallel Devin Cloud sessions — one
for each service prompt you generated above. Use the otterworks repo
for each session. Each session should work independently on its
assigned service only.
```

Devin Local launches the cloud sessions directly from the IDE. Each session
runs on its own machine with full repo context, implementing RFC 7807 for its
assigned service.

Alternatively, launch sessions manually via the Devin API:

```bash
export DEVIN_ORG_ID="<your-org-id>"
export DEVIN_API_KEY="<your-api-key>"

# Example: launch the Go API Gateway session
curl -X POST "https://api.devin.ai/v3/organizations/$DEVIN_ORG_ID/sessions" \
  -H "Authorization: Bearer $DEVIN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "In the otterworks repo, update services/api-gateway to use RFC 7807 Problem Details for all error responses:\n\n1. Create internal/errors/problem.go with a ProblemDetail struct\n2. Add a WriteProblem helper\n3. Replace all writeJSONError calls with WriteProblem\n4. Add Content-Type: application/problem+json header\n5. Add a test verifying RFC 7807 schema"
  }'
```

Repeat for each service — or script it to launch all 11 in a loop.

---

<a id="part-4"></a>
## Part 4 — Watch PRs Arrive in Parallel

Within 5–15 minutes, PRs start arriving on the `otterworks` repo — one per
service, each implementing RFC 7807 in its native language:

| PR | Service | Language | What Changed |
|----|---------|----------|--------------|
| #N | api-gateway | Go | `internal/errors/problem.go` + middleware updates |
| #N+1 | auth-service | Java | `ProblemDetailException` + `@ControllerAdvice` |
| #N+2 | file-service | Rust | `ProblemDetail` struct + Actix error impl |
| #N+3 | document-service | Python | FastAPI exception handler + Pydantic model |
| #N+4 | collab-service | Node.js | Express error middleware + TypeScript types |
| #N+5 | notification-service | Kotlin | Ktor StatusPages + data class |
| #N+6 | search-service | Python | Flask errorhandler + dataclass |
| #N+7 | analytics-service | Scala | Akka RejectionHandler + case class |
| #N+8 | admin-service | Ruby | `rescue_from` + ProblemDetail concern |
| #N+9 | audit-service | C# | Already compliant — adds tests only |
| #N+10 | report-service | Java | `@ControllerAdvice` + ProblemDetail DTO |

Each PR has:
- The error middleware implementation in the service's idiomatic style
- Updated existing error returns
- A test proving the RFC 7807 schema
- A clean diff scoped to that one service

The total wall-clock time for 11 services: ~10–15 minutes (parallel). Doing
this sequentially would take 11× longer.

---

<a id="alternatives"></a>
## Alternative Cross-Cutting Tasks

Other cross-cutting tasks that follow the same plan-locally, execute-in-parallel pattern:

### Add Request Correlation IDs

```
Explore otterworks services/. I want to add X-Request-ID header
propagation to all services:
- Generate a UUID if not present in the incoming request
- Pass it through to all downstream calls
- Include it in all log entries and error responses
- Add a test verifying the header is returned

Generate per-service Devin prompts for parallel execution.
```

### Add `/info` Metadata Endpoint

```
Explore otterworks services/. I want every service to expose a
GET /info endpoint returning:
{"service": "name", "version": "0.1.0", "language": "Go 1.22",
 "framework": "Chi", "uptime_seconds": 3600, "git_sha": "abc123"}

Generate per-service Devin prompts. Each should read the version
from a config/env var and use the service's native uptime tracking.
```

### Standardize Structured Logging

```
Explore otterworks services/. I want all services to emit structured
JSON logs with consistent fields: timestamp, level, service, request_id,
message, error (optional). Identify which services already do this and
which need migration. Generate per-service Devin prompts.
```

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **Local scoping is fast and interactive** — Devin Local explores the full
   codebase in seconds, no waiting for remote sessions to boot. Use it for
   discovery, architecture understanding, and plan generation.

2. **Cloud execution scales horizontally** — Devin Cloud sessions run on
   independent machines. 11 services × 1 session each = 11× throughput vs.
   sequential work.

3. **Planning quality drives execution quality** — the prompts Devin Local
   generates are specific because it has full codebase context. Each Devin
   Cloud session receives a precise, self-contained task — no ambiguity, no
   cross-service coupling.

4. **One pane of glass** — the developer stays in Windsurf for planning with
   Devin Local, kicks off cloud sessions, and reviews the resulting PRs. No
   context-switching between tools.

5. **Polyglot is the point** — OtterWorks uses 9 languages deliberately. A
   human would context-switch between Go, Rust, Python, Java, Node.js, Kotlin,
   Scala, Ruby, and C#. Devin handles each natively in parallel.
