# Workshop: .NET Cloud-Native Modernization

## Overview

| | |
|---|---|
| **Focus** | Progressive modernization of a monolithic .NET system to Kubernetes-hosted cloud-native APIs on EKS |
| **Duration** | 2.5-3 hours (configurable — see Duration Variants below) |
| **Audience** | .NET developers, solution architects, platform engineers, modernization teams |
| **Key Modules** | [.NET Monolith Decomposition](../../modules/migration-modernization/dotnet-monolith-decomposition.md), [Cross-Service Integration Testing](../../modules/testing-qa/cross-service-integration-testing.md), [Cross-Service Bug Investigation](../../modules/migration-modernization/cross-service-bug-investigation.md) |

## Workshop Narrative

This workshop follows a progressive modernization sequence that mirrors how real enterprise teams decompose monoliths:

1. **Extract** — Decompose a bounded context from a .NET monolith into a containerized microservice with local hosting via Docker Compose
2. **Validate** — Write integration tests between the existing monolith and the newly extracted microservice to prove they work together
3. **Debug** — Investigate a cross-service bug in a service outside the scope of the translated code, demonstrating how Devin traces root causes across distributed systems

Each phase builds on the previous one, and all development uses local hosting alternatives (Docker Compose) so participants can test without cloud accounts. The target deployment platform is EKS, with Helm charts and platform conformance patterns available as context.

## Getting the Most from This Workshop

> **Devin works asynchronously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin keeps working in the background.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem before Devin executes.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments and Devin will wake up and address them — this is the core feedback loop.
- **Try parallel sessions.** Running multiple sessions simultaneously mirrors real enterprise usage and demonstrates team-based operation at scale.

## Table of Contents

- [Duration Variants](#duration-variants)
- [Labs](#labs)
  - [Lab 1 — Monolith Decomposition & Containerization](#lab-1--monolith-decomposition--containerization-75-min)
  - [Lab 2 — Integration Testing Between Monolith & Microservice](#lab-2--integration-testing-between-monolith--microservice-45-min)
  - [Lab 3 — Cross-Service Bug Hunt](#lab-3--cross-service-bug-hunt-45-min)
- [Repos Required](#repos-required)

---

## Duration Variants

| Variant | Labs | Duration | Best For |
|---------|------|----------|----------|
| **Full workshop** | Labs 1 + 2 + 3 | 2.5-3 hours | Half-day workshops, modernization-focused audiences |
| **Hands-on extract + debug** | Labs 1 + 3 | 2 hours | Time-constrained workshops, focus on Devin's extraction + debugging intelligence |
| **Bug hunt standalone** | Lab 3 only | 45 min | Quick hands-on session, adding to a broader workshop agenda |
| **Extract + validate** | Labs 1 + 2 | 2 hours | QE-focused audiences, emphasis on testing practices |

## Getting Started

Before beginning any lab, each participant creates a personal working branch from `main`:

```
git checkout main && git pull
git checkout -b workshop-<attendee_id>
```

Replace `<attendee_id>` with a unique identifier (e.g., name, employee ID). All work happens on this branch. This prevents conflicts when multiple participants run labs simultaneously against the same repos.

---

## Labs

### Lab 1 — Monolith Decomposition & Containerization (75 min)

- **Module:** [.NET Monolith Decomposition with Local Hosting](../../modules/migration-modernization/dotnet-monolith-decomposition.md)
- **Repositories:**
  - [quickapp-monolith](https://github.com/Cognition-Partner-Workshops/quickapp-monolith) — .NET + Angular monolith (source)
  - [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices) — target scaffold (reference)
  - [quickapp-iac](https://github.com/Cognition-Partner-Workshops/quickapp-iac) — Helm charts (context)
  - [platform-engineering-shared-services](https://github.com/Cognition-Partner-Workshops/platform-engineering-shared-services) — EKS platform standard (context)
- **Objective:** Extract the Order bounded context into a standalone .NET microservice with Docker Compose for local testing
- **Duration:** 75 min

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Extract the Order bounded context from quickapp-monolith into a standalone .NET microservice. Work on branch `workshop-<attendee_id>` in both repos. Use quickapp-microservices as reference for the target architecture, and quickapp-iac for Helm chart patterns. Deliverables: (1) New .NET Web API for order-service, (2) Shared contracts for inter-service communication, (3) Dockerfile with multi-stage build, (4) Docker Compose for local dev (monolith + order-service + PostgreSQL), (5) Monolith refactored to use HTTP client, (6) Integration smoke test. Push to both repos and create PRs.
```

#### Step 2: Research with Ask Devin

- *"Analyze the domain boundaries in the QuickApp monolith. Which bounded contexts have clean boundaries vs. tight coupling?"*
- *"What's the best strategy for splitting the shared EF Core DbContext into per-service databases?"*

#### Step 3 (Optional): Read the DeepWiki

Open each repo's DeepWiki page to understand the monolith's module structure, the target microservices scaffold, and the Helm chart patterns.

#### Step 4 (Optional): Review & Give Feedback

Review both PRs. Ask Devin to add circuit breaker logic, health checks, or improve the Docker Compose configuration.

**Key Takeaways:**
- Devin analyzes domain boundaries and coupling before extracting code
- Devin generates both application code and containerization artifacts in one session
- Local Docker Compose hosting means no cloud account needed for development and testing

---

### Lab 2 — Integration Testing Between Monolith & Microservice (45 min)

- **Module:** [Cross-Service Integration Testing](../../modules/testing-qa/cross-service-integration-testing.md)
- **Repositories:** Same as Lab 1
- **Objective:** Write integration tests that validate the HTTP contract between the monolith and extracted Order service
- **Duration:** 45 min

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Write integration tests for the HTTP contract between the QuickApp monolith and the extracted Order microservice in quickapp-microservices. Create: (1) Integration test project, (2) Docker Compose test configuration, (3) Contract tests for all Order CRUD operations, (4) End-to-end flow test (create customer → place order → verify in Order service), (5) Shared DTO serialization roundtrip tests. Work on branch `workshop-<attendee_id>`.
```

#### Step 2: Research with Ask Devin

- *"What are the critical failure modes when the monolith calls the Order service?"*
- *"Should we use WebApplicationFactory or TestContainers for the integration tests?"*

#### Step 3 (Optional): Read the DeepWiki

Review both repos to understand the HTTP contract — find the HTTP client in the monolith and the controller endpoints in the Order service.

#### Step 4 (Optional): Review & Give Feedback

Ask Devin to add concurrent order creation tests, contract backwards-compatibility tests, or performance benchmarks.

**Key Takeaways:**
- Devin generates contract tests by analyzing both sides of the service boundary
- Docker Compose orchestrates test environments with no external dependencies
- Integration tests catch contract mismatches before they reach production

---

### Lab 3 — Cross-Service Bug Hunt (45 min)

- **Module:** [Cross-Service Bug Investigation](../../modules/migration-modernization/cross-service-bug-investigation.md)
- **Repository:**
  - [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices)
- **Objective:** Find and fix a visual bug in the Notification service where order confirmation emails show amounts 100x smaller than the actual order total
- **Duration:** 45 min

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Order confirmation notification emails are showing wrong amounts after the microservice decomposition. A $149.99 order shows as $1.50 in the email preview. Investigate and fix this bug in quickapp-microservices. Work on branch `workshop-<attendee_id>`. Reproduce by running the notification-service and POSTing to /api/notification/events/order-placed with `{"orderId":"11111111-1111-1111-1111-111111111111","customerId":"22222222-2222-2222-2222-222222222222","totalAmount":149.99,"placedAt":"2026-03-17T12:00:00Z"}`. Open the preview URL — the total shows $1.50 instead of $149.99. Find the root cause, fix it, take before/after screenshots, and open a PR.
```

#### Step 2: Research with Ask Devin

- *"Trace the data flow from OrderPlacedEvent to the rendered notification email. Where does the monetary amount get transformed?"*
- *"What does OrderPlacedEvent.TotalAmount represent — dollars or cents? Check the shared contract."*

#### Step 3 (Optional): Read the DeepWiki

Open the microservices repo's DeepWiki to understand the event flow from Order to Notification, the shared contracts library, and the rendering pipeline.

#### Step 4 (Optional): Review & Give Feedback

Check that Devin removed the erroneous division and fixed the misleading comment. Ask for a regression test.

**Key Takeaways:**
- Devin traces bugs across microservice boundaries by following event/data flows
- Devin doesn't trust misleading code comments — it verifies against the actual data contract
- Visual bugs in HTML email previews are immediately verifiable via Devin's browser
- The bug is outside the scope of the code that was "translated" — Devin's intelligence finds root causes in unfamiliar services

---

## Repos Required

- [ ] quickapp-monolith
- [ ] quickapp-microservices
- [ ] quickapp-microfrontends (optional — for exploring the full target state)
- [ ] quickapp-iac
- [ ] platform-engineering-shared-services

## Context

This workshop uses the C12 (.NET/Angular Containerized Decomposition) repo cluster. The monolith is imported from QuickApp, a real open-source .NET + Angular application with 5 bounded contexts. The microservices scaffold and IaC were generated to represent the target cloud-native architecture on EKS.

The workshop also references the existing [Platform-Conformant Microservice Decomposition](../platform-microservice-decomposition/) workshop (DA8), which uses a different .NET monolith family (OrderManager / C11 cluster). That workshop focuses on IaC conformance and multi-repo coordination; this workshop focuses on the progressive modernization journey with local hosting, integration testing, and cross-service debugging.

## Devin Features Checklist

- [ ] Multi-repo context awareness
- [ ] Architectural analysis (domain boundaries, coupling)
- [ ] Code extraction and refactoring (.NET, C#)
- [ ] Docker/docker-compose authoring
- [ ] Test generation (integration, contract)
- [ ] Cross-service debugging and root cause analysis
- [ ] Browser interaction (viewing HTML email previews)
- [ ] Screen recording (before/after bug evidence)
- [ ] PR creation and PR comment feedback loop
- [ ] DeepWiki for codebase exploration
- [ ] AskDevin for pre-session planning
