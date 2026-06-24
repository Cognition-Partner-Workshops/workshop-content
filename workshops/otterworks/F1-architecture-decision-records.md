# Lab: Architecture Decision Records

## Objective

Use Devin to generate Architecture Decision Records (ADRs) for key design choices in the OtterWorks codebase by reading the actual implementation and documenting the "why" behind the architecture.

## What's Wrong

OtterWorks has detailed architecture documentation (`ARCHITECTURE.md`, `docs/flows.md`) that describes **what** the system looks like and **how** requests flow. What is missing is **why** — the rationale behind key decisions. For example:

- Why polyglot? Why not standardize on one or two languages?
- Why DynamoDB for file metadata instead of PostgreSQL (which is already used for documents)?
- Why MeiliSearch instead of Elasticsearch or PostgreSQL full-text search?
- Why CRDT (Yjs) for collaboration instead of Operational Transform?
- Why a monorepo instead of separate repos per service?
- Why Flask for search-service when document-service already uses FastAPI?
- Why Rails for the admin-service instead of reusing an existing language stack?

These are the kinds of decisions that new team members ask about, that get lost in Slack threads and meeting notes, and that lead to inconsistent future decisions when the original rationale is forgotten.

## Where to Look

- `ARCHITECTURE.md` — **Read this first.** Comprehensive architecture overview with service descriptions, data stores, infrastructure, and observability.
- `docs/flows.md` — Request flow diagrams showing how services interact.
- `docs/CI_STRATEGY.md` — CI/CD architecture decisions.
- `docs/EVENT_DRIVEN_SECURITY.md` — Event-driven SAST pipeline architecture.
- Each service's source code — The implementation reveals technology choices (frameworks, libraries, patterns).
- `infrastructure/terraform/` — Infrastructure decisions (which AWS services, why).
- `docker-compose.yml` — Local development stack and service dependencies.
- `shared/proto/README.md` — Mentions gRPC as a future consideration (decision to use REST for now).
- `services/report-service/pom.xml` — Legacy Java 8 service (decision to keep it on old stack).

## What Done Looks Like

- [ ] Created `docs/adr/` directory with ADR template
- [ ] Wrote at least 3 ADRs following the standard format:
  - **Title:** Short descriptive name
  - **Status:** Accepted / Proposed / Deprecated / Superseded
  - **Context:** What is the issue or forces at play?
  - **Decision:** What was decided?
  - **Consequences:** What are the trade-offs? What becomes easier or harder?
- [ ] Each ADR is grounded in evidence from the codebase (not generic boilerplate)
- [ ] ADRs reference specific files, services, or configuration to support the rationale
- [ ] Optionally: numbered ADRs sequentially (e.g., `0001-polyglot-architecture.md`, `0002-dynamodb-for-file-metadata.md`)

## Suggested ADR Topics

| # | Decision | Key Evidence |
|---|----------|-------------|
| 1 | Polyglot microservices architecture | `ARCHITECTURE.md` — 10 languages, deliberate variety |
| 2 | DynamoDB for file metadata, PostgreSQL for documents | `services/file-service/` uses DynamoDB, `services/document-service/` uses PostgreSQL |
| 3 | MeiliSearch for full-text search | `services/search-service/` — chose MeiliSearch over Elasticsearch |
| 4 | Yjs CRDT for real-time collaboration | `services/collab-service/` — Yjs over Operational Transform |
| 5 | Monorepo structure | All services in one repo with `dorny/paths-filter` for CI |
| 6 | REST over gRPC for inter-service communication | `shared/proto/README.md` — gRPC deferred |
| 7 | Event-driven architecture via SNS/SQS | `shared/events/schemas/` — domain events for async processing |
| 8 | Separate admin dashboard (Angular) from user app (React/Next.js) | `frontend/` — two distinct frontends |

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Read `ARCHITECTURE.md` and pick the decision that interests you most. A good first ADR is "Why polyglot?" because the evidence is spread across the entire codebase and the trade-offs are clear (flexibility vs. operational complexity).

### Hint 2 — Specific Direction

For the DynamoDB vs. PostgreSQL ADR, compare `services/file-service/` (uses AWS SDK for DynamoDB) with `services/document-service/` (uses SQLAlchemy + PostgreSQL). What properties of each data model drove the choice? File metadata is key-value with high read throughput; documents have relational data with version history and complex queries.

### Hint 3 — Approach

Ask Devin: "Read `ARCHITECTURE.md` and the service implementations. Create `docs/adr/` and write an ADR for why OtterWorks uses DynamoDB for file metadata (`services/file-service/`) but PostgreSQL for document metadata (`services/document-service/`). Include specific evidence from the code — data access patterns, query types, consistency requirements — and document the trade-offs."

## Time Budget

- ~10 minutes: Read ARCHITECTURE.md and pick 3 ADR topics
- ~15-20 minutes per ADR
- ~45-60 minutes for 3 ADRs
