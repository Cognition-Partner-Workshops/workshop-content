# Cross-Service Bug Investigation

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Challenge](#challenge)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Going Further](#going-further)
- [Notes](#notes)
- [quickapp-microservices](#quickapp-microservices)

---

## Quick Start

Paste this prompt into Devin to try investigating a cross-service bug:

```
Order confirmation notification emails are showing wrong
amounts after the microservice decomposition. A $149.99
order shows as $1.50 in the email preview.

Investigate and fix this bug in quickapp-microservices.
Work on branch workshop-<attendee_id>.

To reproduce:
1. Run the notification-service locally
2. POST to http://localhost:5005/api/notification/
   events/order-placed with body:
   {"orderId": "11111111-1111-1111-1111-111111111111",
    "customerId": "22222222-2222-2222-2222-222222222222",
    "totalAmount": 149.99,
    "placedAt": "2026-03-17T12:00:00Z"}
3. Open the preview URL from the response — the total
   shows $1.50 instead of $149.99

Find the root cause, fix it, take before/after
screenshots, and open a PR with your fix and root cause
analysis.
```

---

## Repositories

- [quickapp-microservices](#quickapp-microservices) — .NET microservices with the bug

---

## Challenge

Investigate and fix a realistic cross-service bug in a microservices architecture. An order confirmation notification email displays wrong monetary amounts ($1.50 instead of $149.99). The bug requires tracing data flow across service boundaries — from the Order service's event to the Notification service's rendering logic — to find a unit mismatch (dollars vs. cents) introduced during the microservice decomposition.

This is a debugging exercise, not a building exercise. Devin needs to trace the data flow, identify the root cause, verify with reproduction steps, and produce a clean fix with regression tests.

## What Participants Will Learn

- How Devin investigates bugs across service boundaries by tracing event data flows
- How Devin reads shared contracts (DTOs, events) to identify semantic mismatches
- How unit mismatch bugs (dollars vs. cents, milliseconds vs. seconds) are a common class of cross-service errors
- How to write regression tests that prevent similar bugs from reappearing
- How PR descriptions should include root cause analysis, not just the fix

## Devin Features Exercised

- Cross-service code comprehension
- Bug investigation and root cause analysis
- Targeted code fixes with regression tests
- Screenshot capture (before/after)
- PR creation with root cause documentation
- AskDevin for investigation strategy

## Difficulty

Intermediate

## Estimated Time

45 minutes

## Going Further

- **Scheduled smoke tests**: Configure a scheduled session that runs the notification email rendering test periodically to catch similar regressions before they reach users.
- **Event-driven bug detection**: Connect a webhook so that when changes to shared contracts (DTOs, events) are pushed, Devin automatically checks all consumers for compatibility.
- **Playbook-driven bug investigation**: Encode the investigation methodology (reproduce → trace data flow → identify root cause → fix → add regression test → document) as a playbook for cross-service bug reports.
- **Knowledge notes**: Capture the "dollars vs. cents" pattern as a knowledge note so future sessions are primed to look for unit mismatch bugs when investigating monetary calculation errors.
- **Team-based debugging**: In a microservices architecture, different teams own different services. This module shows how a single Devin session can trace a bug across team boundaries.

## Notes

- The bug is in the Notification service's `FormatCurrency()` method in `NotificationRenderer.cs` — it divides by 100, assuming cents, but `OrderPlacedEvent.TotalAmount` is in dollars
- The shared contract `OrderPlacedEvent.cs` defines `TotalAmount` as `decimal` (dollars, not cents)
- Key files to investigate:
  - `src/Services/Notification/Notification.API/Services/NotificationRenderer.cs` — contains the buggy `FormatCurrency()` method
  - `src/Services/Notification/Notification.API/Controllers/NotificationController.cs` — endpoints for event ingestion and HTML preview
  - `src/Shared/Shared.Contracts/Events/OrderPlacedEvent.cs` — the shared contract showing `TotalAmount` is `decimal` (dollars, not cents)
- This pairs well with [.NET Monolith Decomposition](dotnet-monolith-decomposition.md) — the decomposition is what introduced this class of bug

---

## <a id="quickapp-microservices"></a>quickapp-microservices

**Repository:** [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices)

.NET microservices with 5 services (Identity, Customer, Order, Product, Notification) + YARP API Gateway + shared contracts. The Notification service has a unit mismatch bug in its currency formatting logic.

Each participant creates a `workshop-<attendee_id>` branch from `main` and pushes their work there.

### Step 1: Paste into Devin — Bug Investigation

```
Order confirmation notification emails are showing wrong
amounts after the microservice decomposition. A $149.99
order shows as $1.50 in the email preview.

Investigate and fix this bug in quickapp-microservices.
Work on branch workshop-<attendee_id>.

To reproduce:
1. Run the notification-service locally
2. POST to http://localhost:5005/api/notification/
   events/order-placed with body:
   {"orderId": "11111111-1111-1111-1111-111111111111",
    "customerId": "22222222-2222-2222-2222-222222222222",
    "totalAmount": 149.99,
    "placedAt": "2026-03-17T12:00:00Z"}
3. Open the preview URL from the response — the total
   shows $1.50 instead of $149.99

Find the root cause, fix it, take before/after
screenshots, and open a PR with your fix and root cause
analysis.
```

### Step 2: Research with Ask Devin

- *"Trace the data flow from when an OrderPlacedEvent is received to when the notification email is rendered. Where does the monetary amount get transformed?"*
- *"What does the OrderPlacedEvent.TotalAmount field represent — dollars or cents? Check the shared contract definition."*
- *"Are there any other places in the Notification service that make assumptions about the unit of TotalAmount?"*

### Step 3 (Optional): Read the DeepWiki

Open the microservices repo's DeepWiki to understand:
1. The event flow from Order service to Notification service
2. The `Shared.Contracts` library and what the `OrderPlacedEvent` record defines
3. The `NotificationRenderer` and how it formats monetary values

### Step 4 (Optional): Review & Give Feedback

- **Review the fix** — Did Devin remove the division by 100? Did it also fix the misleading comment?
- **Check for similar bugs** — Ask Devin: *"Are there any other places in the codebase that make the same cents-vs-dollars assumption?"*
- **Leave a comment** asking for a regression test: *"Add a unit test for FormatCurrency that verifies $149.99 renders as '$149.99' and not '$1.50'"*

### Key Takeaways

- **Cross-service tracing**: Devin traces data flow across service boundaries — from the Order service's event through shared contracts to the Notification service's rendering — to find the root cause
- **Shared contract as source of truth**: The `OrderPlacedEvent` contract defines the semantics (dollars, not cents). Consumers must respect the contract, not assume their own interpretation
- **Regression testing**: A simple unit test for `FormatCurrency` prevents this class of bug from recurring
- **Root cause documentation**: The PR description should explain *why* the bug happened (unit mismatch introduced during decomposition), not just *what* was changed
