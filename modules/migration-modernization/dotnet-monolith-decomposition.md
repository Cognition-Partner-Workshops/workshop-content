# .NET Monolith Decomposition

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Challenge](#challenge)
- [Prerequisites](#prerequisites)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Going Further](#going-further)
- [Notes](#notes)
- [quickapp-monolith](#quickapp-monolith)
- [quickapp-microservices](#quickapp-microservices)

---

## Quick Start

Paste this prompt into Devin to try extracting a domain from the QuickApp monolith:

```
Analyze the QuickApp monolith (quickapp-monolith) and
extract the Order Management domain into a standalone
.NET Web API microservice. Work on branch
workshop-<attendee_id>.

Deliverables:
1. New .NET Web API for the order-service with its own
   models, controllers, services, and EF Core DbContext
2. Shared contracts — DTOs and events in a shared library
   for inter-service communication
3. Dockerfile — multi-stage build for the order-service
4. Docker Compose — local development setup running the
   monolith and order-service together (SQLite per service)
5. Monolith refactoring — replace in-process Order calls
   with an HTTP client that calls the new service
6. Integration smoke test — verify order creation flows
   through both services

Push the new order-service to quickapp-microservices on
branch workshop-<attendee_id>. Create a PR.
Push the monolith refactoring to quickapp-monolith on
branch workshop-<attendee_id>. Create a PR.
```

---

## Repositories

- [quickapp-monolith](#quickapp-monolith) — .NET monolith (source for extraction)
- [quickapp-microservices](#quickapp-microservices) — target scaffold with 5 microservices

---

## Challenge

Decompose a .NET Angular modular monolith (QuickApp) into microservices by extracting one domain at a time. Start with the least-coupled domain, create a standalone .NET Web API with its own database and Dockerfile, refactor the monolith to call the new service via HTTP, and verify everything works with Docker Compose.

This exercise uses two repos: the **monolith** (source) and the **microservices scaffold** (target). Participants extract a domain from the monolith and push the new service to the microservices repo while pushing the monolith refactoring back to the monolith repo.

## Prerequisites

- Each participant creates a `workshop-<attendee_id>` branch from `main` in both repos before starting
- The monolith uses EF Core with SQLite — each extracted service should get its own `.db` file
- The Angular frontend hits `/api/*` routes — preserve these exact paths during extraction so the frontend continues working

## What Participants Will Learn

- How Devin identifies bounded contexts in a .NET modular monolith
- How to extract a domain into a standalone .NET Web API with its own DbContext
- How to replace in-process calls with HTTP client calls (IHttpClientFactory)
- How Docker Compose orchestrates multi-service local development
- How shared contracts (DTOs, events) enable clean service boundaries

## Devin Features Exercised

- Architecture analysis and domain decomposition
- Multi-repo code generation (monolith changes + new service)
- Dockerfile and Docker Compose creation
- Cross-project refactoring (removing direct references, adding HTTP clients)
- PR creation across multiple repos
- AskDevin for architecture decisions
- DeepWiki for understanding monolith structure
- Child sessions for parallel domain extraction

## Difficulty

Intermediate to Advanced

## Estimated Time

75 minutes

## Going Further

- **Divide-and-conquer with child sessions**: The QuickApp monolith has four domains (Products, Customers, Inventory, Orders). Extract them in the recommended order (least coupled → most coupled: Products → Customers → Inventory → Orders) using a child session per domain. Each child follows the same extraction playbook.
- **Evals-first extraction**: Write contract tests first (they fail because the service does not exist yet), then extract the domain and make the tests pass. Every PR must pass: `dotnet build`, `dotnet test`, `dotnet format --verify-no-changes`.
- **Playbook-driven decomposition**: Encode the extraction process (identify domain, split DbContext, create service, add HTTP client, write contract tests, create Dockerfile, update Compose) as a playbook. Apply it to each domain extraction.
- **Scheduled regression testing**: After extraction, configure a scheduled session that runs the full integration test suite to verify both monolith and microservice still work together.
- **Event-driven integration testing**: Connect a webhook so that when either repo receives a push, Devin automatically runs the integration smoke tests.
- **Team-based review**: Each extraction PR touches both repos. Multiple reviewers can verify the monolith refactoring and the new service independently.

## Notes

- Extraction order matters: Products (least coupled) → Customers → Inventory → Orders (most coupled, depends on all others)
- When splitting `AppDbContext`, remove navigation properties that cross module boundaries and replace them with foreign key IDs
- Copy relevant seed data from `SeedData.cs` to each extracted service
- Each new `DbContext` should configure its own `OnModelCreating` with only its entity mappings
- Do NOT modify the Angular frontend — it hits `/api/*` routes regardless of which backend serves them
- Do NOT change the SQLite database file pattern — each service gets its own `.db` file
- Preserve exact API route structure so the frontend continues working

---

## <a id="quickapp-monolith"></a>quickapp-monolith

**Repository:** [quickapp-monolith](https://github.com/Cognition-Partner-Workshops/quickapp-monolith)

.NET Angular modular monolith (QuickApp). Contains Identity, Customer, Order, Product, and Inventory modules sharing a single `AppDbContext`. The Angular SPA frontend communicates via `/api/*` REST endpoints. Uses EF Core with SQLite and ASP.NET Core Identity.

Each participant creates a `workshop-<attendee_id>` branch from `main` and pushes their monolith refactoring there.

### Step 1: Paste into Devin — Domain Extraction

```
Analyze the QuickApp monolith (quickapp-monolith) and
extract the Order Management domain into a standalone
.NET Web API microservice. Work on branch
workshop-<attendee_id>.

Deliverables:
1. New .NET Web API for the order-service with its own
   models, controllers, services, and EF Core DbContext
2. Shared contracts — DTOs and events in a shared library
   for inter-service communication
3. Dockerfile — multi-stage build for the order-service
4. Docker Compose — local development setup running the
   monolith and order-service together (SQLite per service)
5. Monolith refactoring — replace in-process Order calls
   with an HTTP client that calls the new service
6. Integration smoke test — verify order creation flows
   through both services

Push the new order-service to quickapp-microservices on
branch workshop-<attendee_id>. Create a PR.
Push the monolith refactoring to quickapp-monolith on
branch workshop-<attendee_id>. Create a PR.
```

### Step 2: Research with Ask Devin

- *"Analyze the domain boundaries in the QuickApp monolith. Which bounded contexts have the tightest coupling? What shared EF Core entities exist between Orders and other modules?"*
- *"What's the best HTTP client pattern in .NET for service-to-service communication? Should we use IHttpClientFactory with typed clients or Refit?"*
- *"How should we handle the database split? The monolith uses a single shared DbContext — what's the migration strategy for giving the Order service its own database?"*

### Step 3 (Optional): Read the DeepWiki

Open each repo's DeepWiki page to understand the architecture:

1. **quickapp-monolith** — Understand the QuickApp module structure, shared models, DbContext, and dependency graph between bounded contexts
2. **quickapp-microservices** — Study the target scaffold to understand the expected service structure, YARP gateway config, and shared contracts
3. **quickapp-iac** — Understand Helm chart structure per service for future K8s deployment

### Step 4 (Optional): Review & Give Feedback

- **Review the monolith PR** — Does the HTTP client correctly replace all in-process Order calls? Are there leftover direct references to Order entities in the monolith's DbContext?
- **Review the microservice PR** — Does the Dockerfile use multi-stage build? Does docker-compose wire services correctly?
- **Leave a comment** asking Devin to improve something specific:
  - *"Add retry and circuit breaker logic to the OrderServiceClient using Polly"*
  - *"Add health check endpoints for readiness and liveness probes"*
  - *"The docker-compose is missing a volume for PostgreSQL data persistence — add one"*

### Key Takeaways

- **Bounded context extraction**: Devin identifies domain boundaries, splits the shared DbContext, creates a standalone service, and refactors the monolith to use HTTP — all in a single session
- **Multi-repo coordination**: The extraction touches two repos (monolith + microservices). Devin creates PRs in both, keeping the changes consistent
- **Evals-first approach**: Write contract tests before extracting. The tests define the expected API surface; the extraction makes them pass

---

## <a id="quickapp-microservices"></a>quickapp-microservices

**Repository:** [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices)

Target-state scaffold with 5 microservices (Identity, Customer, Order, Product, Notification) + YARP API Gateway. Each service follows Clean Architecture (API/Domain/Infrastructure layers). Includes docker-compose for local development with PostgreSQL and RabbitMQ.

Each participant creates a `workshop-<attendee_id>` branch from `main` and pushes their work there.
