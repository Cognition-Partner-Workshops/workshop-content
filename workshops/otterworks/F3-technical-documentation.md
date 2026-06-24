# Lab: Technical Documentation Generation

## Objective

Use Devin to generate or improve technical documentation for OtterWorks: onboarding guides, service READMEs, and operational documentation grounded in the actual codebase rather than assumptions.

## What's Wrong

OtterWorks has solid top-level documentation (`README.md`, `ARCHITECTURE.md`, `docs/flows.md`) but uneven documentation at the service level. Most services lack individual READMEs — a new developer assigned to work on the notification-service has no service-level guide explaining the local development setup, key design patterns, or how to run and test just that service.

Documentation gaps include:

1. **Service READMEs** — Most of the 11 services do not have their own `README.md`. The collab-service has `DEPENDENCY_NOTES.md` and the report-service has `UPGRADE_GUIDE.md`, but there is no standardized per-service developer guide.

2. **Onboarding documentation** — No "new developer onboarding" guide exists. A new team member must piece together information from `README.md`, `ARCHITECTURE.md`, `docs/flows.md`, and `Makefile` to understand how to get productive.

3. **Observability documentation** — The log format spec (`observability/logging/log-format-spec.md`) defines a standard, but there is no guide explaining how to use the Grafana dashboards, read Jaeger traces, or interpret Prometheus alerts for incident response.

4. **OpenAPI spec completeness** — `shared/openapi/` has specs for 3 services (document-service, notification-service, search-service) but not all 10 backend services.

## Where to Look

- `README.md` — Top-level project overview and quick start.
- `ARCHITECTURE.md` — System architecture (services, data stores, infrastructure, observability).
- `docs/flows.md` — Request flow diagrams.
- `docs/api-route-matrix.md` — API endpoint inventory.
- `docs/exploratory-qa-report.md` — QA findings (useful for understanding current system behavior).
- `docs/CI_STRATEGY.md` — CI/CD architecture.
- `docs/runbooks/` — SRE runbooks (7 runbooks, some incomplete).
- `observability/logging/log-format-spec.md` — Logging standard.
- `observability/grafana/dashboards/` — 6 Grafana dashboard definitions.
- `observability/prometheus/alerts.yml` — Alert rules.
- `shared/openapi/` — Existing OpenAPI specs (3 services).
- Each service's source code — For extracting development setup, dependencies, and patterns.
- `Makefile` — Build, test, lint targets that should be referenced in documentation.

## What Done Looks Like

- [ ] Generated at least 2 service-level READMEs that include:
  - Service purpose and tech stack
  - Local development setup (how to run just this service)
  - Key code structure and design patterns
  - How to run tests
  - How to build and deploy
  - Key environment variables
- [ ] Documentation is grounded in the actual codebase (references real files, real commands, real configuration)
- [ ] Optionally: created an onboarding guide (`docs/onboarding.md`) that walks through first-day setup, codebase orientation, and first task
- [ ] Optionally: generated an OpenAPI spec for a service that lacks one (by reading the service's endpoint definitions)

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Pick a service that lacks documentation. The notification-service (Kotlin/Ktor) and analytics-service (Scala/Akka) are good candidates — they are complex enough to benefit from documentation but have no service-level README.

### Hint 2 — Specific Direction

Ask Devin to read a service's source code, `Makefile`, `docker-compose.yml` entries, and `Dockerfile`, then generate a comprehensive README. A good README template: Purpose, Tech Stack, Local Development, Project Structure, Key Design Patterns, Testing, Building, Environment Variables, Troubleshooting.

### Hint 3 — Approach

Ask Devin: "Read the notification-service source code at `services/notification-service/`, its `Dockerfile`, its entry in `docker-compose.yml`, and the relevant Helm chart at `infrastructure/helm/notification-service/`. Generate a comprehensive `services/notification-service/README.md` that covers: service purpose, tech stack, local development setup, project structure, key design patterns (Kotlin coroutines, SQS consumption, SNS publishing), testing, building, and environment variables. Ground everything in the actual code — do not guess."

## Time Budget

- ~10 minutes: Survey which services lack documentation
- ~20-30 minutes per service README
- ~45-60 minutes for 2 service READMEs or 1 README + 1 onboarding guide
