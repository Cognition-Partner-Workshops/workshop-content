# Design Patterns for Devin — Quick Reference

> Comprehensive training: [Solutions Track — SDLC Integration Design](../../workshops/training-tracks/solutions/02-sdlc-integration-design.md) | [Solutions Track — Automation Topology Design](../../workshops/training-tracks/solutions/04-automation-topology-design.md)

## Pattern Reference Card

| # | Pattern | Core Principle | When to Use |
|---|---------|---------------|-------------|
| 1 | **Locally Buildable Code** | If a dev can `make test` with no external deps, Devin can too | Containerize deps, use in-memory DBs for tests, document build command in README |
| <a id="pattern-2-event-driven-triggers"></a>2 | **Event-Driven Triggers** | Connect Devin to existing event sources — no human remembers to invoke it | CI failure → fix; SAST finding → remediate; Ticket tagged → implement; Alert → investigate |
| <a id="pattern-3-divide-and-conquer-with-child-agents"></a>3 | **Divide & Conquer** | Parent agent breaks work into N independent units, spawns one child each | Migrating N services; Remediating N findings; Generating tests for N files; Same refactor across repos |
| 4 | **Human-in-the-Loop (PR)** | Devin proposes via PR; humans review, comment, approve; Devin iterates | Every implementation — the PR is the familiar, auditable review gate |
| 5 | **Toolchain-Agnostic Stubs** | Design integration with replaceable tool slots — pattern stays the same | `[SAST Tool] → webhook → Trigger → Devin → PR` — swap SonarQube/Snyk/Trivy freely |
| 6 | **Context Layer Config** | Invest in shared config once to benefit every future session | Blueprints, Knowledge, Playbooks, MCP servers, Secrets, Git connections |

## Event-Driven Architecture

```
Event Source → webhook/API → Trigger Layer → Devin Session → PR/comment
```

**Safeguards:** Filter out Devin's own events (prevent loops), set max retry count, use idempotency keys.

## Divide & Conquer Architecture

```
Parent Agent
├── Analyzes scope (list of targets)
├── Creates playbook (reusable methodology)
├── Spawns Child 1 → target A → PR
├── Spawns Child 2 → target B → PR
├── Spawns Child N → target N → PR
└── Monitors progress, handles failures
```

**Best practices:** Clear unit of work per child, playbooks for consistency, max concurrency cap, parent monitors and escalates failures.

## Key Rules
- The tighter your build/test loop, the better Devin performs — Pattern 1 is the highest-impact investment
- Every automation topology needs loop prevention, concurrency limits, and escalation policies
- The merge decision is always human — Pattern 4 is non-negotiable
- Context layer configuration (Pattern 6) compounds across every session in the org
