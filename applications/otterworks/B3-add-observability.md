# Lab: Add Observability to Under-Instrumented Services

## Objective

Identify services with weak observability (missing metrics, no structured logging, no trace propagation) and add instrumentation.

## What's Wrong

Not all services have equal observability coverage. Some services have Prometheus metrics, structured logging, and OpenTelemetry trace propagation. Others are missing one or more of these. The observability gaps make it harder to investigate cross-service incidents.

## Where to Look

- Each service's source code — look for Prometheus metric definitions, logging configuration, and OpenTelemetry setup
- `observability/` — Prometheus, Grafana, and Jaeger configuration
- `docker-compose.yml` — Service definitions with environment variables for tracing
- Compare well-instrumented services (e.g., search-service with its `prometheus_client` metrics) against under-instrumented ones

## What Done Looks Like

- [ ] Identified at least one service with observability gaps
- [ ] Added missing Prometheus metrics (request count, duration histogram, error count)
- [ ] Added or improved structured logging (not `print()` or bare `puts`)
- [ ] Verified new metrics appear in the Prometheus targets
- [ ] Optionally: added a Grafana dashboard panel for the newly instrumented service

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started
Ask Devin to audit each service for Prometheus metrics, structured logging, and OpenTelemetry tracing. Which services are missing which?

### Hint 2 — Specific Direction
The analytics-service (Scala) and audit-service (C#) tend to have less observability coverage than the Python and Node.js services. Check those first.

### Hint 3 — Approach
Ask Devin: "Compare the observability instrumentation in `services/search-service/` (well-instrumented) with `services/analytics-service/` (potentially under-instrumented). Add equivalent Prometheus metrics and structured logging to the under-instrumented service."

## Time Budget
~45-60 minutes to instrument one service. Focus on metrics first (most impactful), then structured logging.
