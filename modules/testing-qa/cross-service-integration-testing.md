# Cross-Service Integration Testing

<a id="table-of-contents"></a>
## Table of Contents
- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Prerequisites](#prerequisites)
- [Notes](#notes)
- [quickapp-monolith](#quickapp-monolith)
- [quickapp-microservices](#quickapp-microservices)
- [Going Further](#going-further)

## Repositories

- [quickapp-monolith](#quickapp-monolith) — refactored monolith (from MM15)
- [quickapp-microservices](#quickapp-microservices) — extracted microservice (from MM15)

---

<a id="challenge"></a>
## Challenge

Write integration tests that validate the HTTP contract between the refactored monolith and the newly extracted Order microservice. This is the second phase of the cloud-native modernization narrative: after extracting a service (MM15), participants must prove the two systems work together correctly before deploying to Kubernetes. Cross-service integration testing is often the gap where production issues hide — Devin understands both sides of the contract.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Write integration tests for the HTTP contract between the
QuickApp monolith and the extracted Order microservice.

Context:
- The monolith in quickapp-monolith now calls the Order
  service via an HTTP client
- The Order service in quickapp-microservices exposes REST
  endpoints for order management
- Both services share contract DTOs from Shared.Contracts

Deliverables:
1. Integration test project (tests/Integration/) targeting
   both services
2. Docker Compose test configuration
   (docker-compose.test.yml) that spins up the monolith,
   order-service, and PostgreSQL with test seed data
3. Contract tests covering: create order (POST), get order
   by ID (GET), list orders (GET), update order status
   (PUT), and error responses (404, 400)
```

<a id="target-outcomes"></a>
## Target Outcomes

- Integration test project that validates the monolith-to-microservice HTTP contract
- Docker Compose test environment that spins up both services with test databases
- Contract tests verifying: order creation, order retrieval, order status updates, error handling
- End-to-end flow test: monolith creates order via HTTP client → Order service processes and persists → monolith receives confirmation
- Shared contract validation: DTOs serialized by the monolith deserialize correctly in the microservice (and vice versa)
- PR with integration test suite and test infrastructure

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin generates contract tests for inter-service HTTP communication
- Integration testing patterns for distributed .NET systems (WebApplicationFactory, TestContainers)
- Docker Compose as a test orchestration tool
- The importance of testing service boundaries before production deployment
- How to validate shared contract compatibility between services

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Multi-repo code analysis (understanding both sides of the HTTP contract)
- Test generation (.NET xUnit/NUnit integration tests)
- Docker Compose orchestration for test environments
- HTTP client testing patterns
- PR creation with test evidence

## Difficulty

Intermediate

## Estimated Time

45 minutes

## Prerequisites

- Completion of MM15 (or a pre-decomposed branch with the Order service already extracted)
- Docker available for running the test environment
- Both repos cloned and accessible to Devin

## Notes

- If participants haven't completed MM15, they can use the `main` branch of the microservices repo which has the service scaffold — but the tests will need the services to be implemented
- The integration tests should run locally via Docker Compose with no external dependencies
- This lab feeds into MM17 (cross-service bug hunt) where participants use similar debugging skills

---

## <a id="quickapp-monolith"></a>quickapp-monolith

**Repository:** [quickapp-monolith](https://github.com/Cognition-Partner-Workshops/quickapp-monolith)

The monolith should have been refactored in MM15 to call the Order service via HTTP. The integration tests validate this HTTP boundary.

### Step 1: Paste into Devin

```
Write integration tests for the HTTP contract between the
QuickApp monolith and the extracted Order microservice.

Work on branch workshop-<attendee_id>.

Context:
- The monolith in quickapp-monolith now calls the Order
  service via an HTTP client (refactored in the previous
  lab)
- The Order service in quickapp-microservices exposes REST
  endpoints for order management
- Both services share contract DTOs from Shared.Contracts

Deliverables:
1. Integration test project (tests/Integration/) targeting
   both services
2. Docker Compose test configuration
   (docker-compose.test.yml) that spins up the monolith,
   order-service, and PostgreSQL with test seed data
3. Contract tests covering: create order (POST), get order
   by ID (GET), list orders (GET), update order status
   (PUT), and error responses (404, 400)
4. End-to-end flow test: create a customer in the monolith
   → place an order through the monolith → verify the
   order exists in the Order service database → verify the
   monolith's HTTP client received the correct response
5. Shared contract serialization test: verify that DTOs
   roundtrip correctly between the two services
```

### Step 2: Research with Ask Devin

- *"What are the critical failure modes when the monolith calls the Order service? How should we test timeouts, network errors, and malformed responses?"*
- *"Should we use WebApplicationFactory for in-process testing or TestContainers for full Docker-based integration testing? What are the trade-offs?"*
- *"What test data strategy should we use? Seed data per test, shared fixtures, or database-per-test?"*

### Step 3 (Optional): Read the DeepWiki

Review both repos to understand the HTTP contract:
1. **Monolith** — Find the `OrderServiceClient` (or equivalent HTTP client) and understand what endpoints it calls and what DTOs it expects
2. **Order service** — Find the controller endpoints and understand what request/response shapes they use
3. **Shared contracts** — Verify which DTOs are shared between the services

### Step 4 (Optional): Review & Give Feedback

- **Review test coverage** — Are all CRUD operations tested? Are error scenarios covered?
- **Leave a comment** asking Devin to add specific tests:
  - *"Add a test for concurrent order creation — what happens when two orders are placed simultaneously?"*
  - *"Add a contract backwards-compatibility test — if we add a new field to OrderDto, does the old client still work?"*
  - *"Add a performance test that creates 100 orders and verifies throughput"*

### Key Takeaways

- Cross-service integration tests are the safety net between extraction and deployment
- Docker Compose test environments ensure tests run identically in CI and locally
- Shared contract serialization tests catch the most subtle inter-service bugs

---

## <a id="quickapp-microservices"></a>quickapp-microservices

**Repository:** [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices)

The extracted Order service with REST endpoints. Integration tests validate the contract from the monolith's perspective.

### Key Takeaways

- The provider side of integration tests verifies that the service API matches what consumers expect
- Docker Compose orchestration makes the multi-service test environment reproducible

---

<a id="going-further"></a>
## Going Further

### Event-Driven Contract Verification

When either service's API changes, automatically trigger integration tests:

```
PR modifies OrderController or OrderServiceClient
    → CI triggers cross-service integration test suite
    → If tests fail, Devin session starts:
       "Integration tests failed after API changes in
        the Order service. Diagnose and fix the contract
        mismatch."
    → Devin opens a PR on the affected repo
```

### Scheduled Integration Health Checks

Configure a daily Devin session to run the cross-service integration tests against the latest builds of both services. This catches drift between services that deploy on different schedules.

### Divide and Conquer for Multi-Service Testing

For architectures with many services, spawn child Devin sessions to generate integration tests for each service boundary in parallel. Each child focuses on one consumer-provider pair.
