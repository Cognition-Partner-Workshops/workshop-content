# Contract Testing

<a id="table-of-contents"></a>
## Table of Contents
- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [petclinic-microservices](#petclinic-microservices)
- [quickapp-microservices](#quickapp-microservices)
- [Going Further](#going-further)

## Repositories

- [petclinic-microservices](#petclinic-microservices)
- [quickapp-microservices](#quickapp-microservices)

---

<a id="challenge"></a>
## Challenge

Generate consumer-driven contract tests (Pact or Spring Cloud Contract) for service boundaries in microservice architectures. Tests verify that services can communicate correctly across API boundaries. Contract testing is high-value but often skipped because it requires understanding both sides of an API — Devin analyzes both consumer and provider codebases to generate accurate contracts.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Analyze the service boundaries in
petclinic-microservices. The API Gateway calls Customers
Service and Visits Service via WebClient. Generate Spring
Cloud Contract tests for at least 2 of these service
boundaries — create both the consumer (gateway) and
provider (service) contract tests. Ensure the contract
verification passes.
```

<a id="target-outcomes"></a>
## Target Outcomes

- Contract test suite covering at least 2 service boundaries
- Consumer and provider test stubs generated
- Contract verification passing in CI
- PR with contract tests and documentation

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin analyzes microservice communication patterns to identify contract boundaries
- How Devin selects and configures the appropriate contract testing framework for the tech stack
- Devin's ability to generate both consumer and provider sides of a contract
- How to evaluate whether generated contracts cover real integration risks
- How Devin Review can catch API contract-breaking changes in PRs before they reach production

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Microservice architecture understanding
- Multi-repo code analysis (consumer and provider)
- Test framework selection and configuration
- PR creation with contract documentation

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="petclinic-microservices"></a>petclinic-microservices

**Repository:** [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices)

Spring Cloud microservices with multiple service boundaries (API Gateway, Customers, Visits, Vets) communicating via REST.

### Step 1: Paste into Devin

```
Analyze the service boundaries in
petclinic-microservices. The API Gateway calls Customers
Service and Visits Service via WebClient. Generate Spring
Cloud Contract tests for at least 2 of these service
boundaries — create both the consumer (gateway) and
provider (service) contract tests. Ensure the contract
verification passes.
```

### Step 2: Research with Ask Devin

- *"What are the main service-to-service communication patterns in petclinic-microservices? Which boundaries carry the most data?"*
- *"Should we use Spring Cloud Contract or Pact for this Spring Boot microservices project? What are the tradeoffs?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the API Gateway's aggregation pattern — it calls both CustomersServiceClient and VisitsServiceClient and merges results. These are the primary contract boundaries to test.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the contracts accurately reflect the real API payloads between services? Check that field names and types match the actual DTOs
- **Leave a comment** asking Devin to add a contract test for the Vets Service boundary
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- Contract tests catch integration issues at build time — before services are deployed together
- Devin generates both sides (consumer and provider) from a single analysis of the codebase
- Spring Cloud Contract's stub generation enables independent service testing

---

## <a id="quickapp-microservices"></a>quickapp-microservices

**Repository:** [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices)

.NET microservices with Angular frontend — a containerized decomposition architecture with multiple service boundaries.

### Step 1: Paste into Devin

```
Analyze the service boundaries in
quickapp-microservices. Identify the inter-service API
contracts and generate Pact consumer-driven contract tests
for at least 2 service boundaries. Create both consumer
and provider test implementations.
```

### Step 2: Research with Ask Devin

- *"What inter-service communication patterns exist in this .NET microservices project? Are services using REST, gRPC, or message queues?"*
- *"What is the best contract testing approach for .NET microservices — Pact.NET or a custom solution?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the decomposition boundaries and identify which services depend on each other's APIs.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the Pact interactions realistic? Do they cover error scenarios as well as happy paths?
- **Leave a comment** asking Devin to add contract tests for an additional service boundary or an error response scenario
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- Consumer-driven contracts put the consumer's expectations first — the provider must satisfy them
- Contract testing across technology stacks (.NET services) validates that serialization/deserialization works correctly
- Pact broker integration enables contract verification across independent CI pipelines

---

<a id="going-further"></a>
## Going Further

### Event-Driven Contract Verification

When a provider service's API changes (detected in CI), automatically trigger Devin to verify and update consumer contracts:

```
PR changes a controller or DTO in the provider service
    → CI detects API surface change
    → Devin session starts: "Verify all consumer contracts
       still pass. Update any broken contracts and the
       corresponding consumer code."
    → Devin opens PRs on affected consumer repos
```

### Divide and Conquer Across Services

For organizations with many microservices, a parent Devin session can fan out contract generation across service boundaries:

1. Parent analyzes the service dependency graph
2. Spawns a child session per service boundary
3. Each child generates consumer and provider contracts
4. Parent verifies cross-service contract compatibility

### Scheduled Contract Drift Detection

Configure a weekly Devin session to compare live API behavior against defined contracts. This catches drift where implementations have changed but contracts haven't been updated.
