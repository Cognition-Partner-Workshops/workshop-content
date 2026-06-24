# Lab: Full Automation Pipeline (Capstone)

## Objective

Wire up a complete Devin automation pipeline for OtterWorks that combines everything from the workshop: event-driven triggers, playbooks, child agents, scheduled sessions, and the shared context layer — demonstrating the full Ask → Session → Playbook → Automation lifecycle.

## Why This Matters

This is the capstone lab. The previous labs taught individual patterns:
- B1: event-driven triggers (alert → Devin)
- B2: ask → session → playbook progression
- B3: divide-and-conquer with child agents
- C3: scheduled sessions for recurring work
- D1: event-driven SAST pipeline
- D2: knowledge layer and Devin Review

This lab combines them into a complete automation strategy that a team could actually run in production.

## The Pipeline

You will design an automation strategy with three layers:

### Layer 1 — Event-Driven (Real-Time Response)

Devin responds automatically to signals:
- **Security findings on PRs** → SAST auto-remediate (already built in D1)
- **Production incidents** → Investigate and fix (already built in B1)
- **New GitHub Issues labeled `devin-auto`** → Devin picks up the issue and implements it

For the new piece (issue-driven automation), create the trigger:

```
Design a GitHub Actions workflow that triggers when an
issue is labeled devin-auto in the otterworks repo.
The workflow should:

1. Read the issue title and body
2. Call the Devin API to create a session with the
   issue context as the prompt
3. Include a link to the issue in the session prompt
4. Post a comment on the issue with the session link
5. Skip if the issue was created by devin-ai-integration
   bot (prevent loops)

Reference the existing sast-auto-remediate.yml for the
Devin API call pattern. Save the workflow as
.github/workflows/issue-to-devin.yml.
```

### Layer 2 — Scheduled (Recurring Maintenance)

Devin runs on a schedule for maintenance tasks:
- **Weekly:** Security scan + dependency audit (from C3)
- **Weekly:** Test coverage regression check (from C3)
- **Monthly:** Full dependency lifecycle campaign with child agents (from B3)

Design the monthly campaign schedule:

```
Create a Devin scheduled session that runs monthly and
executes a full dependency lifecycle audit:

1. List all services and their dependency files
2. Spawn child sessions to audit each service in
   parallel (use the dependency-audit-per-service
   playbook from B3)
3. Aggregate findings into a summary issue
4. Tag CRITICAL findings for immediate attention

This should use the divide-and-conquer pattern: parent
scopes the work, children execute independently, parent
aggregates results.
```

### Layer 3 — On-Demand (Human-Initiated)

Team members use Playbooks for common tasks:
- **Incident investigation** playbook (from B2)
- **Dependency audit** playbook (from B3)
- **Service scaffolding** playbook (new)

For the new service scaffolding playbook:

```
Create a Devin Playbook called otterworks-new-service
that scaffolds a new microservice following OtterWorks
conventions:

1. Create the service directory structure following the
   pattern of existing services
2. Include: health endpoints (/health, /health/ready,
   /metrics), structured logging, Dockerfile,
   docker-compose entry, Helm chart
3. Wire the service into the API gateway with a new
   route prefix
4. Add a CI pipeline entry in ci.yml with paths-filter
5. Add a basic test suite
6. Update ARCHITECTURE.md with the new service

The playbook should accept parameters: service name,
language/framework, and API prefix.
```

## What Done Looks Like

- [ ] **Layer 1:** Designed or implemented the issue-to-Devin workflow
- [ ] **Layer 2:** Designed the monthly campaign schedule using child agents
- [ ] **Layer 3:** Created at least one new Playbook for on-demand use
- [ ] Can draw the complete automation strategy showing all three layers
- [ ] Can explain which layer handles which type of work:
  - Event-driven = real-time response to signals (seconds)
  - Scheduled = recurring maintenance (weekly/monthly)
  - On-demand = human-initiated with Playbooks (ad-hoc)
- [ ] Can articulate how the shared context layer (Knowledge, Playbooks, Blueprints) ties all three layers together

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Start with the Diagram

Before writing any code, draw the three-layer automation strategy on a whiteboard:
- Top: Event sources (GitHub PRs, Grafana alerts, Issue labels)
- Middle: Devin triggers (webhooks, schedules, manual playbook invocation)
- Bottom: Outputs (PRs, reports, issue comments)

### Hint 2 — Focus on One Layer

If time is short, pick one layer to implement fully rather than all three partially. Layer 1 (issue-to-Devin workflow) is the most concrete and testable.

### Hint 3 — The Strategy Document

The real deliverable is not just the code — it is the automation strategy document. Write a one-page summary: what is automated, what triggers it, what Devin does, what humans still review. This is what you would present to your engineering leadership.

## Key Takeaways

- A complete Devin automation strategy has three layers: event-driven (real-time), scheduled (recurring), on-demand (human-initiated)
- Each layer handles different work with different urgency and patterns
- The shared context layer (Knowledge, Playbooks, Blueprints) is the foundation that makes all three layers effective
- The Ask → Session → Playbook → Automation progression is how teams mature their Devin adoption: start manual, codify what works, automate the proven patterns
- Devin is a team-based resource, not an individual tool — the automation strategy serves the whole team

## Time Budget

- ~60-90 minutes for the full capstone
- Advanced participants: implement all three layers and write the automation strategy document
- Minimum viable: implement Layer 1 (issue-to-Devin) and design (not implement) Layers 2-3
