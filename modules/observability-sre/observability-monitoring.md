# Observability & Monitoring

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Add observability instrumentation to an application — structured logging, metrics collection, distributed tracing, and health dashboards. This exercises Devin's ability to integrate observability libraries and configure monitoring infrastructure.

## Target Outcomes

- Structured JSON logging with correlation IDs
- Prometheus-compatible metrics endpoint
- Health check and readiness endpoints
- OpenTelemetry tracing (or equivalent) integrated
- PR with observability improvements

## What Participants Will Learn

- How Devin integrates observability libraries into existing applications
- Structured logging vs. unstructured logging
- Metrics, traces, and logs — the three pillars of observability
- How to configure monitoring endpoints for production readiness

## Devin Features Exercised

- Library integration (Winston, Micrometer, OpenTelemetry)
- Configuration management
- Multi-file changes across application layers
- PR creation

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express application — add Winston structured logging, Prometheus metrics, and OpenTelemetry tracing.

### Step 1: Paste into Devin

> Add observability to timesheet-app: (1) Replace console.log with Winston structured JSON logging with correlation IDs per request, (2) Add a /metrics endpoint using prom-client for Prometheus scraping (request count, latency histogram, error rate), (3) Add OpenTelemetry auto-instrumentation for HTTP and Express. Open a PR.

### Step 2: Research with Ask Devin

- *"What logging patterns does timesheet-app currently use? How inconsistent is the logging?"*
- *"What metrics are most valuable for a CRUD application — request rate, error rate, latency, or database query times?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the middleware chain and request lifecycle. Plan where to inject logging, metrics, and tracing middleware.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are correlation IDs propagated through the entire request lifecycle?
- **Leave a comment** asking Devin to add a Docker Compose file with Prometheus and Grafana for local observability

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot application — add Spring Boot Actuator, Micrometer metrics, and structured logging with Logback.

### Step 1: Paste into Devin

> Add observability to uc-spring-boot-upgrade-microservice-extraction: (1) Enable Spring Boot Actuator with health, info, metrics, and prometheus endpoints, (2) Add Micrometer Prometheus registry for metrics export, (3) Configure Logback for structured JSON logging with MDC correlation IDs, (4) Add custom metrics for article and user operations. Open a PR.

### Step 2: Research with Ask Devin

- *"Which Spring Boot Actuator endpoints should be exposed vs. secured in production?"*
- *"What custom business metrics would be valuable — article creation rate, user registration rate, API error rate?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the application layers and identify where to add custom metrics. Map out the request flow for MDC correlation ID propagation.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are Actuator endpoints properly secured? Are custom metrics named following Micrometer conventions?
- **Leave a comment** asking Devin to add a Grafana dashboard JSON that visualizes the key metrics
