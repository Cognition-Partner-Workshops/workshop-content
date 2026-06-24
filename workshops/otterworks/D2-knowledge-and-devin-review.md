# Lab: Knowledge Layer & Devin Review

## Objective

Configure the Devin shared context layer for OtterWorks — Knowledge notes, coding standards, and Devin Review rules — then observe how this one-time investment improves every subsequent Devin session and PR review.

## Why This Matters

Devin's shared context layer is the highest-leverage investment a team can make. Configure it once, and every session — from every team member — benefits automatically. Without it, every session starts from scratch. With it, Devin already knows your coding standards, architecture decisions, and review criteria before anyone types a prompt.

This lab demonstrates the "Context Layer Config" design pattern: invest in shared config once to benefit every future session.

## The Lab

### Step 1 — Observe the Baseline (10 min)

First, see what Devin produces without context layer configuration. Ask:

```
Add a new health check endpoint to the OtterWorks
notification-service at services/notification-service/.
The endpoint should be at /health/detailed and return
the status of all downstream dependencies (database,
Redis, message queue).
```

Review the output. Does Devin follow the existing patterns in the notification-service? Does it use the right logging format? The right error handling style? Probably not consistently — it has no project-specific guidance.

### Step 2 — Create Knowledge Notes (15 min)

Now configure Knowledge notes that encode OtterWorks conventions. Go to Devin settings → Knowledge and create:

**Note 1 — Coding Standards:**
```
Title: OtterWorks Coding Standards
Trigger: When working on any service in the otterworks repo

Content:
- All services must have structured logging (not print/puts).
  Python: use structlog. Node.js: use pino. Go: use zerolog.
  Java/Kotlin: use SLF4J with Logback.
- Health endpoints follow the pattern: /health (basic liveness),
  /health/ready (readiness with dependency checks),
  /metrics (Prometheus format).
- All API responses must include X-Request-ID header for
  distributed tracing.
- Error responses must use the standard error envelope:
  {"error": {"code": "...", "message": "...", "request_id": "..."}}
- Tests must be runnable in isolation (no external service
  dependencies). Use mocks/stubs for external calls.
```

**Note 2 — Architecture Decisions:**
```
Title: OtterWorks Architecture Decisions
Trigger: When making design decisions for otterworks

Content:
- Services communicate via SNS/SQS for async events and
  HTTP through the API gateway for sync requests. Never
  call services directly — always go through the gateway.
- PostgreSQL is the primary datastore for most services.
  DynamoDB is used only for file-service metadata.
  MeiliSearch is used only by search-service.
- All environment-specific config must be via environment
  variables, never hardcoded. See docker-compose.yml for
  the canonical list of env vars per service.
- The API gateway handles auth (JWT validation). Backend
  services trust the X-User-ID header set by the gateway.
```

### Step 3 — Retry with Context (10 min)

Run the same prompt from Step 1 again in a new session:

```
Add a new health check endpoint to the OtterWorks
notification-service at services/notification-service/.
The endpoint should be at /health/detailed and return
the status of all downstream dependencies (database,
Redis, message queue).
```

Compare the output to Step 1. Does it now follow the logging convention? Does it use the standard health endpoint pattern? Does it include the error envelope format?

### Step 4 — Configure Devin Review (15 min)

Now set up Devin Review rules so every PR gets automatic quality checks:

1. Go to Devin settings → Devin Review
2. Enable Devin Review for the otterworks repo
3. The review will automatically check PRs against your Knowledge notes and repo conventions

To see Devin Review in action, open a PR from any of the other labs (A1-C2). Look at the review comments — Devin Review catches issues like:
- Inconsistent logging (using print instead of structlog)
- Missing health endpoints
- Hardcoded configuration
- Missing test coverage for new code

## What Done Looks Like

- [ ] Completed Step 1: observed Devin's baseline output without context
- [ ] Created at least 2 Knowledge notes encoding OtterWorks conventions
- [ ] Completed Step 3: re-ran the same prompt and observed improved output
- [ ] Configured Devin Review for the otterworks repo
- [ ] Can articulate the value: Knowledge notes are a one-time investment that compounds across every session and team member
- [ ] Can explain the shared context layer: Knowledge, Playbooks, Blueprints, MCP servers, Secrets

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Start with Step 1 vs Step 3 — the before/after comparison is the most compelling demonstration of Knowledge notes. Use the exact same prompt both times to control the comparison.

### Hint 2 — Good Knowledge Notes

Good Knowledge notes are specific and actionable. "Follow best practices" is useless. "Use structlog for Python services, format logs as JSON with service_name, request_id, and timestamp fields" is useful. Think about what you tell a new team member on their first day.

### Hint 3 — The Compound Effect

Each Knowledge note helps every future session. If your team has 10 engineers each running 5 sessions/week, one well-written Knowledge note improves 50 sessions/week. The ROI of spending 15 minutes writing a good note is enormous.

## Key Takeaways

- The shared context layer (Knowledge, Playbooks, Blueprints) is the highest-leverage investment for Devin adoption
- Knowledge notes encode what you would tell a new team member — coding standards, architecture decisions, conventions
- Devin Review applies your standards automatically to every PR — consistent quality without manual review effort
- Configure once, benefit everywhere — every team member's sessions inherit the same context

## Time Budget

- ~45-60 minutes for all 4 steps
- The before/after comparison (Steps 1 vs 3) is the key insight — don't skip it
