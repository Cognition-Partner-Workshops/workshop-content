# Unit-of-Work Economics

The formula for predictable, defensible pricing in human + agent delivery engagements.

---

## The Core Formula

The organization that defines the low-level unit of work controls the cost model. Whether bidding on a migration or planning a quarter, the formula is:

```
Unit cost = (ACUs per unit × $/ACU) + (Review hours per unit × Blended rate)

Total bid = Σ(Unit cost × Quantity) + Orchestration overhead + Margin
```

Where:
- **ACUs per unit** = Agent Compute Units consumed to execute one standard unit of work (e.g., one microservice upgrade, one SAST finding remediation, one COBOL copybook translation, one test suite for a module)
- **$/ACU** = Cost per Agent Compute Unit (consumption-based pricing)
- **Review hours per unit** = Human time spent reviewing, approving, and handling exceptions
- **Blended rate** = Weighted average billing rate for the humans involved (architects, senior engineers, reviewers)
- **Quantity** = Number of units in the engagement
- **Orchestration overhead** = Planning, playbook development, environment setup, and coordination
- **Margin** = Target profit margin for the engagement

---

## What Makes This Different

Traditional estimation:
```
Total cost = Number of engineers × Hourly rate × Estimated hours
           + Uncertainty buffer (typically 30-50%)
```

The traditional model has high variance because human productivity varies by individual, day, and context. Estimation accuracy depends on the estimator's experience and optimism.

Human + agent estimation:
```
Total cost = (Known agent cost per unit × Unit count)
           + (Known review cost per unit × Unit count)
           + Orchestration overhead (fixed, amortized)
           + Margin
```

The human + agent model has lower variance because the agent-executed portion has predictable throughput. When every task follows the same playbook and produces the same artifact structure, variance in execution time and cost narrows dramatically.

---

## Worked Example 1: Spring Boot Upgrade Campaign

**Scenario:** Upgrade 50 microservices from Spring Boot 2.7 to 3.2 (Jakarta namespace migration, dependency bumps, test verification).

| Component | Calculation | Cost |
|-----------|-------------|------|
| **Agent execution** | 50 services × ~8 ACUs/service × $/ACU | Agent cost |
| **Human review** | 50 services × 1.5 hrs/service × blended rate | Review cost |
| **Orchestration** | Playbook creation (8 hrs) + environment setup (4 hrs) + coordination (8 hrs) = 20 hrs × architect rate | Orchestration cost |
| **Exception handling** | ~10% failure rate × 50 = 5 services × 4 hrs manual rework × senior rate | Exception cost |
| **Margin** | Target margin applied to total | Margin |

**Timeline comparison:**
- Headcount-only: 50 services × 4 engineer-hours each = 200 hours = ~5 weeks (1 engineer) or ~1 week (5 engineers)
- Human + agent: 50 parallel agent sessions (complete in days) + review queue (1 week) = ~1.5 weeks total with 1-2 reviewers

**Key insight:** The human + agent delivery model compresses the timeline by parallelizing execution while elevating the engineer's role — engineers work more like architects and product owners (reviewing, deciding, approving) rather than implementers.

---

## Worked Example 2: Security Remediation Campaign

**Scenario:** Remediate 500 SAST findings across 80 repositories (mix of HIGH and CRITICAL severity).

| Component | Calculation | Cost |
|-----------|-------------|------|
| **Agent execution** | 500 findings × ~3 ACUs/finding × $/ACU | Agent cost |
| **Human review** | 500 findings × 0.5 hrs/finding × blended rate | Review cost |
| **Orchestration** | Campaign planning (4 hrs) + playbook per finding type (3 types × 4 hrs) + coordination (8 hrs) = 24 hrs × architect rate | Orchestration cost |
| **Exception handling** | ~15% require manual intervention = 75 findings × 2 hrs × senior rate | Exception cost |
| **Margin** | Target margin applied to total | Margin |

**Why granularity matters:** A "security remediation project" is vague and hard to estimate. "500 individual findings, each with a known advisory, a known fix pattern, and a re-scan verification step" is granular and estimatable. The unit (one finding) has predictable agent cost.

---

## Worked Example 3: COBOL Migration Campaign

**Scenario:** Migrate 200 COBOL copybooks to Java equivalents with output parity validation.

| Component | Calculation | Cost |
|-----------|-------------|------|
| **Agent execution** | 200 copybooks × ~12 ACUs/copybook × $/ACU | Agent cost |
| **Human review** | 200 copybooks × 2 hrs/copybook × blended rate | Review cost |
| **Orchestration** | Reference architecture (16 hrs) + golden-file test harness (12 hrs) + playbook per copybook type (4 types × 6 hrs) + coordination (16 hrs) = 68 hrs × architect rate | Orchestration cost |
| **Exception handling** | ~20% require rework = 40 copybooks × 6 hrs × senior rate | Exception cost |
| **Margin** | Target margin applied to total | Margin |

**Key insight:** COBOL migrations have higher exception rates because legacy code often contains undocumented business logic. The orchestration overhead is also higher because you need a reference architecture and parity validation harness. But even at 20% exception rate, 80% of the volume is agent-executed — dramatically reducing the senior engineer hours required.

---

## How Granularity Affects Estimatability

| Granularity Level | Example Unit | Estimation Confidence | Agent Success Rate |
|-------------------|-------------|----------------------|-------------------|
| **Project level** | "Modernize the platform" | Very low (±50%) | N/A — too vague for agents |
| **Epic level** | "Upgrade all services to Spring Boot 3" | Low-medium (±30%) | Medium — needs decomposition |
| **Story level** | "Upgrade order-service to Spring Boot 3.2" | Medium-high (±15%) | High — well-defined scope |
| **Task level** | "Update Jakarta imports in OrderController.java" | Very high (±5%) | Very high — mechanical transformation |

The sweet spot for human + agent estimation is **story level** — each unit is a single service, module, or component. This gives high estimation confidence while keeping orchestration overhead manageable.

---

## Knowledge Checks

**Knowledge Check 2.1**
Q: In the unit-of-work formula, what does "Orchestration overhead" cover?
A) The cost of running the AI agents
B) Planning, playbook development, environment setup, and coordination
C) Human code review time
D) The margin added to the engagement
*Answer: B — Orchestration overhead is the fixed cost of setting up the campaign: creating playbooks, configuring environments, planning the decomposition, and coordinating the work.*

**Knowledge Check 2.2**
Q: Why does the human + agent estimation model have lower variance than traditional estimation?
A) AI agents are cheaper than human engineers
B) The agent-executed portion has predictable throughput because every task follows the same playbook
C) There are fewer total tasks to estimate
D) The margin buffer absorbs all uncertainty
*Answer: B — When work follows a standardized playbook, execution time and cost become predictable. Variance concentrates in the human-judgment portions, which are a smaller fraction of total effort.*

**Knowledge Check 2.3**
Q: In the COBOL migration example, why is the orchestration overhead higher than the Spring Boot upgrade?
A) COBOL agents cost more per ACU
B) There are more copybooks than microservices
C) COBOL migrations require a reference architecture and parity validation harness that must be designed upfront
D) The human review rate is lower for COBOL
*Answer: C — Legacy migrations have more upfront design work: reference architecture decisions, golden-file test harness creation, and multiple playbook variants for different copybook types.*

**Knowledge Check 2.4**
Q: What granularity level offers the best balance of estimation confidence and manageable orchestration overhead?
A) Project level — keeps things simple
B) Epic level — good enough for most bids
C) Story level — each unit is a single service/module/component
D) Task level — maximum precision
*Answer: C — Story-level units (one service, one module, one copybook) give high estimation confidence (±15%) while keeping orchestration overhead practical. Task-level is too granular to manage at scale.*

---

## Key Takeaways

- The unit-of-work formula gives predictable, defensible pricing: `(ACUs × rate) + (Review hours × rate) + Overhead + Margin`
- Human + agent estimation has lower variance than traditional headcount estimation because agent-executed work is standardized
- Granularity matters: story-level decomposition (one service, one module) is the sweet spot for balancing confidence and overhead
- Exception rates vary by campaign type (10% for upgrades, 15% for security, 20% for legacy migrations) — build them into the estimate
