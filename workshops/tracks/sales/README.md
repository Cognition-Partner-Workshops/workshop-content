# Workshop Track: Sales

## Overview

| | |
|---|---|
| **Focus** | How the hybrid operating model (human engineers + AI agents) changes staffing, bidding, cost structures, and resource allocation — enabling SIs to capture work and price engagements competitively |
| **Duration** | 4-5 hours (self-paced reading + exercises + case study) |
| **Audience** | Sales Engineers, Account Executives, Practice Leads, and P&L Owners at SI partner firms |
| **Format** | Guided reading with knowledge checks, worked examples, and a capstone case study exercise |
| **Prerequisite** | [Foundations Workshop](../../foundations/) (Level 1) |

## Workshop Narrative

The consulting and systems integration industry is entering a structural shift. Organizations that adopt a hybrid workforce model — combining human engineers for judgment work with AI agents for execution work — can bid more competitively, deliver faster, and produce higher-quality outcomes than headcount-only approaches.

This workshop equips sales-facing roles with the frameworks, economics, and narratives they need to:
- **Understand** how hybrid delivery changes the cost structure of engagements
- **Estimate** engagement costs using the unit-of-work model
- **Position** the hybrid model to different buyer personas
- **Scope** proposals that separate agent-executable work from human-delivered work
- **Compete** against headcount-only bids with confidence in cost, timeline, and quality advantages

The content builds progressively: first the operating model transformation, then the economics, then how to position and sell it.

---

<a id="toc"></a>
## Table of Contents

- [1. Operating Model Transformation](#operating-model-transformation)
- [2. Unit-of-Work Economics](#unit-of-work-economics)
- [3. Value Narrative Selection](#value-narrative-selection)
- [4. Engagement Scoping & Estimation](#engagement-scoping-and-estimation)
- [5. Competitive Positioning](#competitive-positioning)
- [6. The Decision Framework for Sales](#the-decision-framework-for-sales)
- [Case Study: Full Engagement Estimation](#case-study-full-engagement-estimation)
- [Gaps & Future Content](#gaps-and-future-content)

---

<a id="operating-model-transformation"></a>
## 1. Operating Model Transformation

### The Three-Tier Work Distribution Model

In a hybrid delivery organization, work distributes across three tiers — each optimized for what it does best:

```
┌─────────────────────────────────────────────────────────────┐
│              HUMAN ENGINEERS (Judgment Tier)                 │
│                                                             │
│  • Architecture and system design decisions                 │
│  • Novel problem-solving requiring creativity               │
│  • Stakeholder communication and political navigation       │
│  • Code review, approval, and quality gates                 │
│  • Exception handling when agents escalate                  │
│  • Engagement oversight and orchestration decisions         │
└─────────────────────────────────────────────────────────────┘
                      ↕ PR review, feedback, approval
┌─────────────────────────────────────────────────────────────┐
│              CLOUD AGENTS (Execution Tier)                   │
│                                                             │
│  • Implementation of well-defined tasks at scale            │
│  • Repetitive work parallelized across targets              │
│  • Event-driven responses (CI fixes, security triage)       │
│  • Scheduled maintenance (dependency bumps, docs refresh)   │
│  • Investigation, diagnosis, and remediation                │
│  • Test generation and coverage expansion                   │
└─────────────────────────────────────────────────────────────┘
                      ↕ Real-time assistance in the editor
┌─────────────────────────────────────────────────────────────┐
│              LOCAL AGENTS (Acceleration Tier)                │
│                                                             │
│  • Code completion and generation in the IDE                │
│  • Real-time pair programming during active development     │
│  • Quick refactors within active focus                      │
│  • Inline explanations and documentation                    │
└─────────────────────────────────────────────────────────────┘
```

The **Judgment Tier** is where human engineers are irreplaceable — architecture decisions, creative design, political navigation, and final approval authority. This is the work that justifies senior billing rates.

The **Execution Tier** is where cloud agents produce the most leverage — high-volume, well-defined tasks that can be parallelized and verified programmatically. This is the work that traditionally required large teams of mid-level engineers.

The **Acceleration Tier** is where local AI assistants speed up individual engineers during their active development — code completion, quick refactors, and inline help. This improves individual productivity but does not change staffing models.

### How Sprint Planning Changes

**Before (headcount-only model):**
- Sprint capacity = number of engineers x hours per sprint
- Maintenance work competes with feature work for the same pool of capacity
- Large campaigns (migrations, upgrades) take months because engineers cannot be fully dedicated
- Reactive work (incidents, CI failures) interrupts deep work
- Bench cost accumulates during ramp-up and between engagements

**After (hybrid model):**
- Sprint planning separates "human-judgment work" from "well-defined implementable work"
- Agent capacity handles the execution portion — no bench cost, no ramp-up delay
- Campaigns parallelize across child agents (weeks instead of months)
- Reactive work is handled by event-driven agents — humans review results asynchronously
- Engineers focus on architecture, design, and the problems that justify their billing rates

### Bench Economics and Attrition Risk

The hybrid model fundamentally changes the bench cost equation:

| Factor | Headcount-Only | Hybrid Model |
|--------|---------------|--------------|
| **Ramp-up cost** | Weeks to onboard new engineers to client systems | Agents onboard via blueprints and knowledge notes in minutes |
| **Bench cost during gaps** | Full salary continues between engagements | Agent capacity scales to zero when not in use — no idle cost |
| **Attrition risk** | Key engineers leave, taking institutional knowledge | Knowledge is encoded in playbooks, knowledge notes, and blueprints — survives staff turnover |
| **Surge capacity** | Requires hiring or subcontracting (weeks/months lead time) | Spin up additional agent sessions immediately (minutes) |
| **Specialization cost** | Each technology requires specialists on the bench | Agents typically handle a wide range of technologies with appropriate prompts and playbooks |

The margin improvement path: as playbooks mature and knowledge notes accumulate, agents' first-pass success rate improves, human review burden decreases, and margin expands over the life of an engagement.

### The Efficiency-Quality-Outcomes Cycle

The hybrid operating model creates a virtuous cycle:

1. **Efficiency first** — Agents handle high-volume, well-defined execution at consistent throughput. No idle time, no ramp cost, no context-switching loss.
2. **Quality rises** — Consistency and standardization (playbooks, automated review, automated testing) raise baseline quality without adding cost.
3. **Outcomes improve** — Fewer defects lead to less rework, faster delivery, shorter time-to-remediation, and more capacity for the next cycle.

This cycle compounds. Each engagement makes the next one cheaper and better because the playbooks, knowledge, and blueprints persist and improve.

---

### Knowledge Check 1

**Knowledge Check 1.1**
Q: In the three-tier work distribution model, which tier handles parallelized migration campaigns across 50 microservices?
A) Judgment Tier (Human Engineers)
B) Execution Tier (Cloud Agents)
C) Acceleration Tier (Local Agents)
D) All three tiers equally
*Answer: B — Cloud agents parallelize well-defined, repetitive work across many targets. Humans review the PRs; they don't implement each one.*

**Knowledge Check 1.2**
Q: What happens to bench cost in the hybrid model when there is no active engagement work?
A) It increases because you need both humans and agent licenses
B) It stays the same — you still pay for the engineering team
C) Agent capacity scales to zero with no idle cost; only human bench remains
D) Agents are reassigned to internal R&D
*Answer: C — Agent capacity is consumption-based. When no work is running, agent cost is zero. Human bench cost still exists but is smaller because fewer engineers are needed for execution work.*

**Knowledge Check 1.3**
Q: Why does the hybrid model reduce attrition risk for an SI?
A) Engineers are happier because they get paid more
B) Institutional knowledge is encoded in playbooks and knowledge notes rather than residing only in engineers' heads
C) Agents never leave the company
D) Engineers have less work to do
*Answer: B — When knowledge is codified in playbooks and knowledge notes, it survives staff turnover. A departing engineer's domain expertise persists in the system.*

**Knowledge Check 1.4**
Q: In the efficiency-quality-outcomes cycle, what drives quality improvement?
A) Hiring more QA engineers
B) Consistency and standardization from playbooks, automated review, and automated testing
C) Manual code review of every line
D) Reducing the number of deployments
*Answer: B — Automated and standardized processes (playbooks, Devin Review, automated testing) apply the same rigor to every change, raising baseline quality without adding headcount.*

### Key Takeaways

- Work distributes across three tiers: humans for judgment, cloud agents for execution, local agents for acceleration
- Sprint planning in the hybrid model separates judgment work (human-delivered) from execution work (agent-delivered)
- Bench cost, attrition risk, and ramp-up time are structurally reduced because agent capacity is elastic and knowledge is codified
- The efficiency-quality-outcomes cycle compounds over time as playbooks and knowledge mature

---

<a id="unit-of-work-economics"></a>
## 2. Unit-of-Work Economics

### The Core Formula

The organization that defines the low-level unit of work controls the cost model. Whether bidding on a migration or planning a quarter, the formula is:

```
Unit cost = (ACUs per unit × $/ACU) + (Review hours per unit × Blended rate)

Total bid = Σ(Unit cost × Quantity) + Orchestration overhead + Margin
```

Where:
- **ACUs per unit** = Agent Compute Units consumed to execute one standard unit of work
- **$/ACU** = Cost per Agent Compute Unit (consumption-based pricing)
- **Review hours per unit** = Human time spent reviewing, approving, and handling exceptions
- **Blended rate** = Weighted average billing rate for the humans involved (architects, senior engineers, reviewers)
- **Quantity** = Number of units in the engagement
- **Orchestration overhead** = Planning, playbook development, environment setup, and coordination
- **Margin** = Target profit margin for the engagement

### What Makes This Different

Traditional estimation:
```
Total cost = Number of engineers × Hourly rate × Estimated hours
           + Uncertainty buffer (typically 30-50%)
```

The traditional model has high variance because human productivity varies by individual, day, and context. Estimation accuracy depends on the estimator's experience and optimism.

Hybrid estimation:
```
Total cost = (Known agent cost per unit × Unit count)
           + (Known review cost per unit × Unit count)
           + Orchestration overhead (fixed, amortized)
           + Margin
```

The hybrid model has lower variance because the agent-executed portion has predictable throughput. When every task follows the same playbook and produces the same artifact structure, variance in execution time and cost narrows dramatically.

### Worked Example 1: Spring Boot Upgrade Campaign

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
- Hybrid: 50 parallel agent sessions (complete in days) + review queue (1 week) = ~1.5 weeks total with 1-2 reviewers

**Key insight:** The hybrid model compresses the timeline by parallelizing execution while requiring fewer senior engineers (reviewers only, not implementers).

### Worked Example 2: Security Remediation Campaign

**Scenario:** Remediate 500 SAST findings across 80 repositories (mix of HIGH and CRITICAL severity).

| Component | Calculation | Cost |
|-----------|-------------|------|
| **Agent execution** | 500 findings × ~3 ACUs/finding × $/ACU | Agent cost |
| **Human review** | 500 findings × 0.5 hrs/finding × blended rate | Review cost |
| **Orchestration** | Campaign planning (4 hrs) + playbook per finding type (3 types × 4 hrs) + coordination (8 hrs) = 24 hrs × architect rate | Orchestration cost |
| **Exception handling** | ~15% require manual intervention = 75 findings × 2 hrs × senior rate | Exception cost |
| **Margin** | Target margin applied to total | Margin |

**Why granularity matters:** A "security remediation project" is vague and hard to estimate. "500 individual findings, each with a known advisory, a known fix pattern, and a re-scan verification step" is granular and estimatable. The unit (one finding) has predictable agent cost.

### Worked Example 3: COBOL Migration Campaign

**Scenario:** Migrate 200 COBOL copybooks to Java equivalents with output parity validation.

| Component | Calculation | Cost |
|-----------|-------------|------|
| **Agent execution** | 200 copybooks × ~12 ACUs/copybook × $/ACU | Agent cost |
| **Human review** | 200 copybooks × 2 hrs/copybook × blended rate | Review cost |
| **Orchestration** | Reference architecture (16 hrs) + golden-file test harness (12 hrs) + playbook per copybook type (4 types × 6 hrs) + coordination (16 hrs) = 68 hrs × architect rate | Orchestration cost |
| **Exception handling** | ~20% require rework = 40 copybooks × 6 hrs × senior rate | Exception cost |
| **Margin** | Target margin applied to total | Margin |

**Key insight:** COBOL migrations have higher exception rates because legacy code often contains undocumented business logic. The orchestration overhead is also higher because you need a reference architecture and parity validation harness. But even at 20% exception rate, 80% of the volume is agent-executed — dramatically reducing the senior engineer hours required.

### How Granularity Affects Estimatability

| Granularity Level | Example Unit | Estimation Confidence | Agent Success Rate |
|-------------------|-------------|----------------------|-------------------|
| **Project level** | "Modernize the platform" | Very low (±50%) | N/A — too vague for agents |
| **Epic level** | "Upgrade all services to Spring Boot 3" | Low-medium (±30%) | Medium — needs decomposition |
| **Story level** | "Upgrade order-service to Spring Boot 3.2" | Medium-high (±15%) | High — well-defined scope |
| **Task level** | "Update Jakarta imports in OrderController.java" | Very high (±5%) | Very high — mechanical transformation |

The sweet spot for hybrid estimation is **story level** — each unit is a single service, module, or component. This gives high estimation confidence while keeping orchestration overhead manageable.

---

### Knowledge Check 2

**Knowledge Check 2.1**
Q: In the unit-of-work formula, what does "Orchestration overhead" cover?
A) The cost of running the AI agents
B) Planning, playbook development, environment setup, and coordination
C) Human code review time
D) The margin added to the engagement
*Answer: B — Orchestration overhead is the fixed cost of setting up the campaign: creating playbooks, configuring environments, planning the decomposition, and coordinating the work.*

**Knowledge Check 2.2**
Q: Why does the hybrid estimation model have lower variance than traditional estimation?
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

### Key Takeaways

- The unit-of-work formula gives predictable, defensible pricing: `(ACUs × rate) + (Review hours × rate) + Overhead + Margin`
- Hybrid estimation has lower variance than traditional headcount estimation because agent-executed work is standardized
- Granularity matters: story-level decomposition (one service, one module) is the sweet spot for balancing confidence and overhead
- Exception rates vary by campaign type (10% for upgrades, 15% for security, 20% for legacy migrations) — build them into the estimate

---

<a id="value-narrative-selection"></a>
## 3. Value Narrative Selection

### Mapping Buyer Personas to Narratives

Different stakeholders care about different outcomes. The same hybrid delivery capability should be positioned differently depending on who you're talking to:

| Buyer Persona | Primary Narrative | Secondary Narrative | What They Need to Hear |
|---------------|------------------|--------------------|-----------------------|
| **CTO / VP Engineering** | Capacity Unlocking | Engineering Focus Elevation | "Your engineers stop doing mechanical work and focus on architecture and design. The backlog of deferred work actually gets done." |
| **CISO / Security Leadership** | Risk Reduction | Quality Improvement | "Vulnerability exposure windows shrink from 'next sprint' to 'next CI run'. PRs receive automated security review." |
| **FinOps / CFO** | Cost Optimization | Operating Model Efficiency | "Consumption-based pricing with measurable ROI. Each session produces attributable output. Licensing cost avoidance accelerates migration payback." |
| **Delivery Director / Program Manager** | Velocity Multiplication | Operating Model Efficiency | "Migration timelines compress from months to weeks. Parallel execution means campaigns finish before the client loses patience." |
| **Practice Lead / P&L Owner** | Operating Model Efficiency | Competitive Positioning | "Your cost structure becomes the most competitive in RFP scenarios. Margin expands as playbooks mature." |

### The Narrative Details

#### For CTO / VP Engineering: Capacity Unlocking + Engineering Focus Elevation

**Lead with:** "Your engineers are spending 60%+ of their time on implementation work that could be delegated. The hybrid model shifts that distribution — engineers focus on architecture, design, and the decisions that require human judgment."

**Supporting points:**
- Show the backlog of deferred "should-do" work (dependency updates, test coverage, documentation debt)
- Quantify sprint points consumed by maintenance vs. feature development
- Position agents as unlocking capacity that already exists in the team's backlog
- Frame as team augmentation, not replacement — the engineering team becomes more effective, not smaller

**Evidence to provide:**
- Before/after sprint allocation (60% implementation → 30% implementation + 30% review)
- Time-to-complete for routine tasks (hours of implementation → minutes of review)
- Backlog velocity improvement when maintenance is automated

#### For CISO / Security Leadership: Risk Reduction + Quality Improvement

**Lead with:** "Your vulnerability exposure window goes from 'next sprint' to 'next CI run' because findings are remediated as soon as they are detected."

**Supporting points:**
- Mean Time to Remediate (MTTR) drops when agents respond automatically
- Consistency — agents do not skip the security scan because the sprint is tight
- Closed-loop verification — scan, remediate, re-scan, confirm
- Knowledge bus factor decreases — security policies encoded in playbooks survive staff changes

**Evidence to provide:**
- MTTR reduction for critical findings (sprint cycles → hours)
- Percentage of PRs receiving automated security review (high coverage with Devin Review)
- Compliance audit trail (SBOM generation, remediation documentation)

#### For FinOps / CFO: Cost Optimization + Operating Model Efficiency

**Lead with:** "Consumption-based pricing with directly attributable ROI. Sessions produce measurable output — PRs merged, findings remediated, tests added. No idle cost."

**Supporting points:**
- ACU budgets provide hard spend controls at org, team, or user level
- ROI is directly measurable (compare ACU cost to equivalent engineer-hours)
- Licensing cost avoidance — migration from expensive platforms (COBOL/mainframe, SAS, Oracle Forms) to open alternatives accelerates when agents execute the migration
- Predictable budgeting — agent costs are forecastable, unlike variable human productivity

**Evidence to provide:**
- Cost per unit of work (agent) vs. cost per unit of work (engineer)
- Licensing cost savings from accelerated platform migrations
- Budget predictability improvement (variance reduction in sprint-over-sprint costs)

#### For Delivery Director: Velocity Multiplication + Operating Model Efficiency

**Lead with:** "The timeline for this migration drops from months to weeks because agents parallelize the implementation and engineers review the PRs."

**Supporting points:**
- Parent-child orchestration processes N targets in parallel
- Timeline compression is dramatic for campaigns with many similar targets
- Engineers are not blocked on each other — agents work independently on isolated branches
- Risk is contained — each unit produces its own PR; failures in one do not affect others

**Evidence to provide:**
- Timeline comparison (sequential human vs. parallel agent execution)
- Throughput metrics (units completed per week)
- Quality metrics (defect rate per unit, rework rate)

### The Decision Matrix

Use this matrix to select your primary and secondary narratives before a client conversation:

```
Step 1: Identify the buyer's role
Step 2: Identify their primary pain point
Step 3: Select the matching narrative from the table above
Step 4: Prepare evidence specific to their context
Step 5: Anticipate objections based on their persona
```

| Pain Point | Narrative to Lead With | Key Metric |
|-----------|----------------------|-----------|
| "We can't hire fast enough" | Capacity Unlocking | Story points reclaimed from maintenance |
| "Our security posture is reactive" | Risk Reduction | MTTR for critical findings |
| "Migrations take too long" | Velocity Multiplication | Timeline compression ratio |
| "We're losing RFPs on price" | Operating Model Efficiency | Cost per unit of work |
| "Our engineers are doing grunt work" | Engineering Focus Elevation | % time on design vs. implementation |
| "We can't predict project costs" | Unit-of-Work Economics | Estimation variance reduction |

---

### Knowledge Check 3

**Knowledge Check 3.1**
Q: A CISO is concerned about their team's slow response to critical vulnerabilities. Which narrative should you lead with?
A) Capacity Unlocking
B) Velocity Multiplication
C) Risk Reduction
D) Cost Optimization
*Answer: C — Risk Reduction directly addresses vulnerability exposure windows and MTTR. The CISO cares about reducing the time between detection and remediation.*

**Knowledge Check 3.2**
Q: A Practice Lead says "We keep losing competitive bids because our proposals are too expensive." Which narrative is most relevant?
A) Engineering Focus Elevation
B) Operating Model Efficiency
C) Quality Improvement
D) Capacity Unlocking
*Answer: B — Operating Model Efficiency addresses cost structure competitiveness directly. The hybrid model produces a lower cost-per-unit that translates to more competitive bids.*

**Knowledge Check 3.3**
Q: Why should you prepare different evidence for a CTO vs. a CFO, even though both benefit from the hybrid model?
A) They have different budget authority
B) They evaluate success using different metrics — the CTO cares about engineering effectiveness, the CFO cares about cost predictability and ROI
C) The CTO is more technical
D) The CFO won't understand the technology
*Answer: B — Each persona evaluates the same capability through different lenses. The CTO measures engineering output and focus; the CFO measures cost, predictability, and attributable ROI.*

**Knowledge Check 3.4**
Q: A Delivery Director managing a 50-service migration says "This project is going to take 6 months." Which evidence would be most compelling?
A) Cost per ACU savings
B) Timeline comparison showing parallel agent execution compresses months to weeks
C) Security compliance improvements
D) Engineer satisfaction scores
*Answer: B — Velocity Multiplication with a concrete timeline comparison (sequential vs. parallel) directly addresses the Delivery Director's concern about project duration.*

### Key Takeaways

- Match the narrative to the buyer persona — the same capability is positioned differently for a CTO, CISO, CFO, and Delivery Director
- Lead with the pain point, not the technology — find what keeps them up at night and map it to the relevant narrative
- Prepare persona-specific evidence (metrics and examples) before every client conversation
- Use the decision matrix to systematically select primary and secondary narratives based on the buyer's stated concerns

---

<a id="engagement-scoping-and-estimation"></a>
## 4. Engagement Scoping & Estimation

### The Scoping Process

Decomposing a large client engagement into standard units of work follows a systematic process:

```
┌──────────────────────────────────────────────────────────┐
│  Step 1: IDENTIFY THE CAMPAIGN TYPE                       │
│                                                          │
│  Migration | Remediation | Modernization | Greenfield    │
└──────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────┐
│  Step 2: DEFINE THE UNIT OF WORK                          │
│                                                          │
│  One service | One module | One finding | One component   │
└──────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────┐
│  Step 3: ESTIMATE ACU COST PER UNIT                       │
│                                                          │
│  Complexity tier × Base ACU cost × Risk multiplier        │
└──────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────┐
│  Step 4: ESTIMATE REVIEW HOURS PER UNIT                   │
│                                                          │
│  Base review time × Complexity factor × Exception rate    │
└──────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────┐
│  Step 5: CALCULATE TOTAL                                  │
│                                                          │
│  Σ(Unit costs) + Orchestration overhead + Margin          │
└──────────────────────────────────────────────────────────┘
```

### Step 1: Identify the Campaign Type

Each campaign type has characteristic unit definitions and cost profiles:

| Campaign Type | Typical Unit | ACU Range/Unit | Review Hours/Unit | Exception Rate |
|---------------|-------------|---------------|-------------------|----------------|
| **Framework upgrade** | One service/module | 5-12 ACUs | 1-2 hrs | 10-15% |
| **Security remediation** | One finding | 2-5 ACUs | 0.3-1 hr | 10-20% |
| **Language migration** | One source file/copybook | 8-15 ACUs | 2-3 hrs | 15-25% |
| **Test generation** | One module under test | 3-8 ACUs | 0.5-1 hr | 5-10% |
| **Documentation** | One component/API surface | 2-4 ACUs | 0.5-1 hr | 5-10% |
| **Feature development** | One user story | 10-20 ACUs | 2-4 hrs | 20-30% |

### Step 2: Define the Unit of Work

The unit must be:
- **Independently executable** — an agent can complete it without depending on other units
- **Independently verifiable** — success criteria can be checked without reference to other units
- **Consistently sized** — units within the same campaign should have similar scope
- **Atomic** — produces one PR, one deliverable, one reviewable artifact

**Good units:** One microservice upgrade. One COBOL copybook translation. One SAST finding remediation. One API endpoint test suite.

**Bad units:** "Upgrade the platform" (too large). "Fix line 47" (too small, insufficient context). "Handle auth and payments" (two unrelated concerns).

### Step 3: Estimate ACU Cost Per Unit

ACU cost depends on:
- **Complexity** — How many files, how much logic, how many dependencies?
- **Novelty** — Has this pattern been done before (playbook exists)?
- **Verification depth** — How extensive is the test/validation suite?

| Complexity Tier | Characteristics | ACU Multiplier |
|----------------|----------------|----------------|
| **Simple** | Single file, mechanical transformation, existing tests | 1x base |
| **Standard** | Multiple files, some logic changes, test updates needed | 2x base |
| **Complex** | Many files, business logic, new test creation, edge cases | 3-4x base |

### Step 4: Estimate Review Hours Per Unit

Human review time depends on:
- **Output size** — Larger diffs take longer to review
- **Risk level** — High-risk changes (payments, auth, data) require deeper review
- **Reviewer expertise** — Domain experts review faster

| Review Level | When | Hours/Unit |
|-------------|------|------------|
| **Light** | Mechanical change, high test coverage, low risk | 0.3-0.5 hrs |
| **Standard** | Logic changes, moderate risk, good test coverage | 1-2 hrs |
| **Deep** | Business-critical, low test coverage, novel patterns | 2-4 hrs |

### Step 5: Calculate Total

Apply the formula and add the structural costs:

```
Agent cost      = Σ (ACUs per unit × $/ACU) across all units
Review cost     = Σ (Review hours per unit × Blended rate) across all units
Exception cost  = Exception rate × Unit count × Avg rework hours × Senior rate
Orchestration   = Playbook dev + Environment setup + Campaign planning + Coord
─────────────────────────────────────────────────────────────────────────────────
Subtotal        = Agent cost + Review cost + Exception cost + Orchestration
Total bid       = Subtotal + Margin
```

### Case Study Exercise: Scope an Engagement

**Scenario:** A client has 30 Spring Boot 2.5 microservices that need to be upgraded to Spring Boot 3.2. They also want the Jakarta namespace migration completed and all existing tests passing on the new version. The services range from simple CRUD APIs (10 services) to complex domain services with business logic (15 services) to critical payment/auth services (5 services).

**Your task:** Using the framework above, produce:
1. The unit definition
2. ACU estimate per complexity tier
3. Review hours per complexity tier
4. Exception rate estimate with justification
5. Total cost calculation (use placeholder rates)

**Reference answer:**

| Tier | Count | ACUs/Unit | Review Hrs/Unit | Exception Rate |
|------|-------|-----------|-----------------|----------------|
| Simple (CRUD) | 10 | ~6 ACUs | 1 hr | 5% |
| Standard (domain logic) | 15 | ~10 ACUs | 1.5 hrs | 15% |
| Complex (payment/auth) | 5 | ~14 ACUs | 3 hrs | 25% |

```
Agent cost:     (10×6 + 15×10 + 5×14) × $/ACU = 280 ACUs × $/ACU
Review cost:    (10×1 + 15×1.5 + 5×3) × blended rate = 47.5 hrs × blended rate
Exception cost: (10×0.05×3 + 15×0.15×4 + 5×0.25×6) × senior rate
                = (1.5 + 9 + 7.5) = 18 hrs × senior rate
Orchestration:  Playbook (12 hrs) + Env setup (6 hrs) + Planning (8 hrs)
                + Coordination (12 hrs) = 38 hrs × architect rate
─────────────────────────────────────────────────────────────────────
Total = Agent + Review + Exception + Orchestration + Margin
```

---

### Knowledge Check 4

**Knowledge Check 4.1**
Q: Which of the following is a good unit of work for a security remediation campaign?
A) "Fix all security issues in the codebase"
B) "One SAST finding with a known advisory and fix pattern"
C) "Handle the OWASP Top 10"
D) "Fix line 47 in AuthController.java"
*Answer: B — A single finding is independently executable, independently verifiable, consistently sized, and atomic (one PR).*

**Knowledge Check 4.2**
Q: Why do COBOL migration campaigns have higher exception rates than framework upgrade campaigns?
A) COBOL is harder for agents to read
B) Legacy code frequently contains undocumented business logic and edge cases that require human judgment to resolve
C) There are more copybooks than microservices
D) The review process is slower for COBOL
*Answer: B — Legacy systems often embed business rules that are not documented anywhere except in the code itself. When an agent encounters these, it may need human guidance to determine the correct modern equivalent.*

**Knowledge Check 4.3**
Q: In the scoping process, what comes immediately after "Define the unit of work"?
A) Calculate the total bid
B) Identify the campaign type
C) Estimate ACU cost per unit
D) Estimate review hours per unit
*Answer: C — The process flows: Identify campaign type → Define unit → Estimate ACU cost → Estimate review hours → Calculate total.*

### Key Takeaways

- The scoping process is systematic: identify campaign type → define unit → estimate agent cost → estimate review cost → calculate total
- Good units are independently executable, independently verifiable, consistently sized, and atomic
- Complexity tiers (simple/standard/complex) drive both ACU cost and review time estimates
- Exception rates vary by campaign type and should be explicitly included in estimates — they represent the portion requiring human rework

---

<a id="competitive-positioning"></a>
## 5. Competitive Positioning

### The Displacement Risk

In an RFP scenario, the SI with the most efficient operating model wins. If a competitor bids with a hybrid workforce and you bid headcount-only, you lose on three dimensions simultaneously:

| Dimension | Headcount-Only Bid | Hybrid Model Bid |
|-----------|-------------------|-----------------|
| **Cost** | Higher — paying senior rates for mechanical work | Lower — agents handle execution at a fraction of human cost |
| **Timeline** | Longer — sequential execution limited by team size | Shorter — parallel agent execution compresses timelines dramatically |
| **Quality** | Variable — depends on individual engineer consistency | Consistent — standardized playbooks and automated review on every unit |

This is not a marginal advantage. It is a structural one. The hybrid model produces fundamentally different economics that headcount-only bids cannot match without sacrificing margin.

### How to Frame This Without Disparaging Competitors

The goal is not to attack other SIs. The goal is to demonstrate that your operating model produces better outcomes for the client:

**Do say:**
- "Our delivery model combines senior human judgment with AI-powered execution to deliver faster at lower cost"
- "We decompose work into standard units with predictable costs, giving you tighter confidence intervals on timeline and budget"
- "Our hybrid workforce model means we can scale capacity to your timeline without the lead time of hiring"

**Do not say:**
- "Other SIs are outdated / behind the times"
- "Traditional delivery is dead"
- "Anyone not using AI will go out of business"

**Frame it as client benefit, not competitor weakness:**
- "You get the timeline you need without paying for overnight shifts or additional headcount"
- "Every unit of work goes through automated quality checks plus human review — this is not a tradeoff between speed and quality"
- "We can demonstrate the unit cost model on a pilot scope before you commit to the full engagement"

### The "Cost of Not Adopting" Argument

When a prospect is hesitant about the hybrid model, the framing shifts from "why adopt" to "what happens if you don't":

1. **Competitive pressure is real** — If your competitors are already bidding with hybrid models, your headcount-only bids will be structurally more expensive. You're not competing against their technology; you're competing against their cost structure.

2. **Talent market pressure** — Senior engineers increasingly expect to work on interesting problems, not mechanical migration tasks. Organizations that can't offer focus-elevated work will lose talent to those that can.

3. **Client expectations are shifting** — Clients who have seen hybrid delivery results will expect it from future engagements. The timeline compression and cost predictability become the new baseline expectation.

4. **Compounding disadvantage** — Early adopters build playbooks, knowledge bases, and operational muscle that make each subsequent engagement cheaper and faster. Late adopters start from zero while competitors are on their third or fourth iteration.

### Scenario Exercise: The RFP Response

**Scenario:** A client has issued an RFP for migrating 200 legacy COBOL programs to Java. Two SIs are bidding:

- **SI-A (headcount-only):** 15 engineers for 9 months. Linear execution. Quality depends on individual engineer consistency.
- **SI-B (hybrid model):** 4 senior engineers + AI agent fleet. Parallel execution. Standardized playbooks with automated verification.

**Questions to consider:**
1. How would SI-B's timeline compare to SI-A's? (Hint: parallel execution of 200 units vs. sequential)
2. What quality arguments can SI-B make? (Hint: standardized playbooks, automated parity testing, consistent review)
3. How does SI-B handle the client's concern about "will AI produce good code"? (Hint: human review on every PR, parity validation, pilot phase)
4. What is SI-A's likely counter-argument, and how would you respond? (Hint: "experienced engineers understand the business logic" → "our architects design the approach; agents execute the mechanical translation under human review")

**Reference positioning:**

SI-B's pitch: "We deliver in 3 months, not 9. Our senior architects design the migration approach and review every output. Our AI agents execute the mechanical translation — 200 copybooks in parallel — while humans handle the business logic decisions. Every translated program is validated against the original using automated parity testing. You get faster delivery, lower cost, and higher consistency."

---

### Knowledge Check 5

**Knowledge Check 5.1**
Q: In the displacement risk framework, on which three dimensions does the hybrid model simultaneously outperform headcount-only bids?
A) Speed, margin, and flexibility
B) Cost, timeline, and quality
C) Scale, reliability, and innovation
D) Pricing, staffing, and reputation
*Answer: B — The hybrid model produces lower cost (agents cheaper than engineers for execution work), shorter timeline (parallel execution), and more consistent quality (standardized playbooks + automated review).*

**Knowledge Check 5.2**
Q: How should you position the hybrid model's advantage without disparaging competitors?
A) Point out that competitors are outdated
B) Frame it as client benefit — faster delivery, lower cost, tighter confidence intervals
C) Show a cost comparison naming specific competitor pricing
D) Emphasize that traditional delivery is obsolete
*Answer: B — Position the advantage as client outcome, not competitor weakness. "You get X" is more effective than "they can't do X."*

**Knowledge Check 5.3**
Q: In the "cost of not adopting" argument, what is the compounding disadvantage for late adopters?
A) They have to pay more for AI licenses
B) Early adopters build playbooks, knowledge, and operational muscle that make each engagement cheaper — late adopters start from zero
C) Clients will refuse to work with non-AI firms
D) Engineers will only work at AI-enabled companies
*Answer: B — Playbooks and knowledge compound. Early adopters' operational efficiency improves with each engagement, creating a widening gap that late adopters must close from scratch.*

**Knowledge Check 5.4**
Q: A client asks "How do I know the AI won't produce bad code?" What is the strongest response?
A) "AI produces better code than humans"
B) "Every PR is reviewed by a senior engineer and validated by automated tests — humans maintain approval authority over every change"
C) "Trust us, it works"
D) "We'll fix anything that goes wrong for free"
*Answer: B — The strongest response emphasizes the human review gate and automated verification. The hybrid model does not eliminate human judgment — it concentrates it at the review/approval points.*

### Key Takeaways

- The hybrid model creates structural advantages on cost, timeline, and quality simultaneously — headcount-only bids cannot match this without sacrificing margin
- Frame competitive advantages as client benefits, not competitor attacks
- The "cost of not adopting" argument works for hesitant prospects: competitive pressure, talent market pressure, shifting client expectations, and compounding disadvantage
- Handle "will AI produce good code?" with the human review gate — every change is reviewed by senior engineers and validated by automated tests

---

<a id="the-decision-framework-for-sales"></a>
## 6. The Decision Framework for Sales

### Applying "What to Give AI" to Proposal Scoping

When scoping a proposed engagement, Sales must determine which portions are agent-executable and which remain human-delivered. This directly affects the cost model, the staffing plan, and the timeline.

The core question from the [Foundations decision framework](../../foundations/04-what-to-give-ai.md) applies directly:

**Can success be verified without human judgment?**

```
For each proposed deliverable, evaluate:

┌─────────────────────────────────────────────────────┐
│  Is the task well-defined?                           │
│  (Clear inputs, clear outputs, clear success criteria)│
├─────────────────────────────────────────────────────┤
│         YES                        NO               │
│          │                          │               │
│  Is it verifiable?            Requires human         │
│  (Tests, scans, output parity)  judgment throughout  │
│          │                          │               │
│     YES       NO              HUMAN-DELIVERED        │
│      │         │              (senior rate)          │
│  Is it repetitive?    Refine requirements            │
│  (Same pattern, many targets) first                  │
│      │                                              │
│  YES       NO                                        │
│   │         │                                        │
│ AGENT     AGENT                                      │
│ (parallel) (single session)                          │
└─────────────────────────────────────────────────────┘
```

### The Proposal Decomposition Checklist

For each line item in a proposed engagement, ask:

| Question | If YES | If NO |
|----------|--------|-------|
| Can you write acceptance criteria that a machine can verify? | Agent-executable | Human-delivered |
| Is the pattern repeated across many targets? | Parallelizable (child agents) | Single-session agent or human |
| Does it require creative/architectural decisions? | Human-delivered (architect rate) | Agent-executable |
| Does it require information that isn't in the codebase or docs? | Human-delivered until info is provided | Agent-executable |
| Is it politically sensitive (affects other teams/orgs)? | Human-delivered (human communicates, agent implements after decision) | Agent-executable |

### Example: Decomposing a Modernization Engagement

**Client request:** "Modernize our 30-service Java platform: upgrade Spring Boot, containerize, add CI/CD, improve test coverage, and redesign the auth service."

| Deliverable | Agent-Executable? | Reasoning | Staffing |
|-------------|-------------------|-----------|----------|
| Spring Boot 2.x → 3.x upgrade (30 services) | Yes — parallel | Mechanical, verifiable (tests pass), repetitive | Agents + reviewer |
| Containerization (Dockerfiles, k8s manifests) | Yes — parallel | Template-based, verifiable (builds run), repetitive | Agents + reviewer |
| CI/CD pipeline creation | Yes — single | Well-defined output (workflows exist), verifiable (pipeline runs) | Agent + reviewer |
| Test coverage expansion | Yes — parallel | Measurable (coverage %), repeatable pattern | Agents + reviewer |
| Auth service redesign | No | Requires architectural decisions, stakeholder input, creative design | Architect + senior engineers |
| Auth service implementation (post-design) | Yes — single | Once architecture is decided, implementation is well-defined | Agent + reviewer |

**Result:** 5 of 6 deliverables are agent-executable. The auth redesign is human-delivered, but only the design phase — implementation after the architecture decision can still be delegated.

**Staffing implication:** Instead of 8-10 engineers, the engagement requires 2-3 senior engineers (architecture + review) plus agent capacity. Cost structure is fundamentally different.

### Handling Gray-Zone Deliverables

Some deliverables sit in the gray zone. The pattern for handling these in proposals:

**Break them into stages and price each stage appropriately:**

1. **Discovery/Research stage** (agent) — "Analyze the codebase, produce a report on current state, identify patterns and exceptions"
2. **Decision stage** (human) — Architect reviews the report, makes design decisions, documents the approach
3. **Implementation stage** (agent) — "Implement the decided approach across all targets following playbook X"
4. **Review/Approval stage** (human) — Senior engineer reviews all outputs, handles exceptions

**In the proposal:** Price stages 1 and 3 at agent rates. Price stages 2 and 4 at human rates. This makes the cost breakdown transparent and defensible.

### Red Flags in Proposal Scoping

Watch for these patterns that indicate a deliverable should NOT be scoped as agent-executable:

- **"It depends on what we find"** — Undefined scope means undefined success criteria
- **"The business rules aren't documented"** — Agent needs information that doesn't exist yet
- **"We'll need stakeholder sign-off at each step"** — Too many human decision points for autonomous execution
- **"The last team tried this and it was harder than expected"** — Hidden complexity suggests high exception rate
- **"This is highly regulated and needs compliance review"** — Human judgment required throughout

These don't mean "don't bid" — they mean "price the human-judgment portions at senior rates and set realistic exception rate estimates."

---

### Knowledge Check 6

**Knowledge Check 6.1**
Q: A proposed deliverable is "redesign the payment processing architecture." Is this agent-executable?
A) Yes — agents can design architectures
B) No — it requires creative decisions, stakeholder input, and judgment that cannot be mechanically verified
C) Partially — the implementation is agent-executable but not the design
D) Both B and C are correct
*Answer: D — The design phase requires human creativity and judgment (not agent-executable). Once the architecture is decided, the implementation of that design can be delegated to agents.*

**Knowledge Check 6.2**
Q: A client wants test coverage expanded from 45% to 80% across 40 modules. How should this be scoped?
A) One large agent session for the entire codebase
B) 40 parallel agent sessions, one per module, with automated coverage verification
C) 40 senior engineers, one per module
D) A single QA lead manually writing tests
*Answer: B — Test generation is well-defined (measurable coverage target), repetitive (same pattern per module), and verifiable (coverage report confirms success). This is a textbook parallel agent campaign.*

**Knowledge Check 6.3**
Q: What is the correct approach for a gray-zone deliverable in a proposal?
A) Classify it entirely as human-delivered to be safe
B) Classify it entirely as agent-executed to be competitive
C) Break it into stages — price research/implementation at agent rates and decision/review at human rates
D) Exclude it from the proposal
*Answer: C — Gray-zone deliverables are decomposed into stages. Agent-executable stages (research, implementation) are priced at agent rates; human-judgment stages (decisions, review) are priced at human rates. This is both accurate and transparent.*

**Knowledge Check 6.4**
Q: Which red flag most strongly suggests a deliverable should NOT be scoped as agent-executable?
A) "The codebase is large"
B) "The business rules aren't documented anywhere"
C) "There are many services to update"
D) "The existing tests need to be updated"
*Answer: B — If the business rules aren't documented, the agent has no source of truth to work from. It cannot access information that only exists in people's heads. This is a fundamental blocker for agent execution, not just a complexity factor.*

### Key Takeaways

- Apply the decision framework to every line item in a proposal: "Can success be verified without human judgment?"
- Agent-executable work must be well-defined, verifiable, and preferably repetitive — this drives parallelization and cost predictability
- Gray-zone deliverables should be decomposed into stages: agent-executed stages (research, implementation) and human-delivered stages (decisions, review)
- Red flags (undocumented business rules, undefined scope, heavy regulation) don't mean "don't bid" — they mean "price with appropriate exception rates and senior staffing"

---

<a id="case-study-full-engagement-estimation"></a>
## Case Study: Full Engagement Estimation

### Scenario

A large financial services firm wants to modernize 30 legacy Java services currently running on Spring Boot 2.3 with a monolithic deployment model. The desired end state:

- All services upgraded to Spring Boot 3.2
- Each service containerized with Docker and deployable to Kubernetes
- CI/CD pipelines created for each service (build, test, scan, deploy)
- Test coverage increased from current ~35% average to 70% minimum
- Three core services (payments, auth, risk-scoring) need architectural redesign to support the new microservice topology
- API documentation generated for all public endpoints

The client wants this completed in 4 months with a clear cost breakdown.

### What You Should Produce

Using the frameworks from this workshop, create:

1. **Campaign decomposition** — Break the engagement into campaigns, then into units of work
2. **Staffing model** — Which roles are needed and at what allocation
3. **Cost estimate** — Using the unit-of-work formula with complexity tiers
4. **Timeline** — Parallel vs. sequential execution plan
5. **Risk register** — Exception rates by campaign, mitigation strategies

### Reference Answer

#### 1. Campaign Decomposition

| Campaign | Unit of Work | Count | Agent-Executable? |
|----------|-------------|-------|-------------------|
| Spring Boot Upgrade | One service upgrade | 30 | Yes — parallel |
| Containerization | One service Dockerfile + k8s manifests | 30 | Yes — parallel |
| CI/CD Pipeline | One service pipeline | 30 | Yes — parallel |
| Test Coverage | One service test expansion | 30 | Yes — parallel |
| Architecture Redesign | One service architecture (3 core services) | 3 | No — human judgment |
| Architecture Implementation | One redesigned service implementation | 3 | Yes — after design |
| API Documentation | One service API docs | 30 | Yes — parallel |

#### 2. Staffing Model

| Role | Allocation | Responsibility |
|------|-----------|----------------|
| Lead Architect | Full-time | Architecture redesign, orchestration decisions, exception escalations |
| Senior Engineer (2) | Full-time | PR review, exception handling, quality gate |
| DevOps Engineer | Half-time | Environment setup, CI/CD template validation, infrastructure review |
| AI Agent Fleet | As needed | All parallel execution work |

**Total human headcount:** 3.5 FTE (vs. estimated 10-12 FTE for headcount-only approach)

#### 3. Cost Estimate

**Spring Boot Upgrade (30 services):**

| Tier | Count | ACUs/Unit | Review Hrs | Exception Rate |
|------|-------|-----------|------------|----------------|
| Simple | 12 | 6 | 1.0 hr | 5% |
| Standard | 13 | 10 | 1.5 hrs | 15% |
| Complex | 5 | 14 | 3.0 hrs | 20% |

**Containerization (30 services):**

| Tier | Count | ACUs/Unit | Review Hrs | Exception Rate |
|------|-------|-----------|------------|----------------|
| Simple | 15 | 4 | 0.5 hr | 5% |
| Standard | 12 | 7 | 1.0 hr | 10% |
| Complex | 3 | 10 | 2.0 hrs | 15% |

**CI/CD Pipeline (30 services):**

| Tier | Count | ACUs/Unit | Review Hrs | Exception Rate |
|------|-------|-----------|------------|----------------|
| Template (most) | 25 | 3 | 0.5 hr | 5% |
| Custom | 5 | 8 | 1.5 hrs | 15% |

**Test Coverage (30 services):**

| Tier | Count | ACUs/Unit | Review Hrs | Exception Rate |
|------|-------|-----------|------------|----------------|
| Simple | 12 | 5 | 0.5 hr | 5% |
| Standard | 13 | 10 | 1.0 hr | 10% |
| Complex | 5 | 15 | 2.0 hrs | 15% |

**Architecture Redesign (3 services):** Human-delivered at architect rate — estimated 80 hrs total (discovery + design + documentation)

**Architecture Implementation (3 services):** 20 ACUs/service + 4 hrs review/service + 30% exception rate

**API Documentation (30 services):** 3 ACUs/service + 0.5 hrs review/service + 5% exception rate

**Orchestration overhead:** Playbook development (40 hrs) + Environment setup (20 hrs) + Campaign coordination (60 hrs) + Exception triage (40 hrs) = 160 hrs at blended rate

#### 4. Timeline

| Month | Activities |
|-------|-----------|
| **Month 1** | Architecture redesign (3 services), playbook development, environment setup. Start Spring Boot upgrade campaign (all 30 services in parallel). |
| **Month 2** | Complete Spring Boot upgrades. Start containerization campaign (30 parallel). Start CI/CD pipeline campaign (30 parallel). Architecture implementation begins for first redesigned service. |
| **Month 3** | Complete containerization + CI/CD. Start test coverage campaign (30 parallel). API documentation campaign (30 parallel). Complete architecture implementations. |
| **Month 4** | Complete test coverage expansion. Integration testing. Final review. Buffer for exceptions and rework. |

**Key insight:** Campaigns that would be sequential in a headcount model run in parallel with agents. The architecture redesign (human-judgment work) is the critical path, not the volume work.

#### 5. Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|-----------|
| Undocumented business logic in complex services | High | Medium | Higher exception rate (20%) for complex tier; architect review of all complex-tier PRs |
| Integration issues between upgraded services | Medium | High | Integration testing phase in Month 4; staged rollout (upgrade in dependency order) |
| Test coverage target unreachable for legacy code | Medium | Low | Accept 60% minimum for legacy modules; document gaps |
| Architecture redesign takes longer than estimated | Medium | High | Month 4 buffer; redesign is on critical path, so start early |
| CI/CD template doesn't fit all services | Low | Low | Custom tier (5 services) priced at higher ACU and review rate |

---

<a id="gaps-and-future-content"></a>
## Gaps & Future Content

The following materials would make this track more comprehensive. These represent future development priorities:

- [ ] **Video: Recorded client pitch walkthrough** — A walkthrough of a client conversation using the value narratives and unit-of-work economics
- [ ] **ROI Calculator Tool** — Interactive spreadsheet or web tool where Sales can input engagement parameters and get a cost estimate with hybrid vs. headcount comparison
- [ ] **Battle Cards** — One-page competitive positioning cards for common RFP scenarios (migration, remediation, modernization, greenfield)
- [ ] **Sample Proposal Templates** — Redacted engagement proposals showing hybrid staffing models, unit-of-work cost breakdowns, and timeline comparisons
- [ ] **Objection Handling Guide** — Common buyer objections and scripted responses with supporting evidence
- [ ] **Pilot Scoping Template** — How to propose a small-scale pilot (10-20 units) that demonstrates the model before a full commitment
- [ ] **Margin Improvement Tracking** — Framework for measuring how margin improves as playbooks mature over the life of an engagement
- [ ] **Industry-Specific Scenarios** — Worked examples for financial services, healthcare, retail, and government verticals
- [ ] **Executive Summary Template** — One-page leave-behind for buyer personas summarizing the hybrid model value proposition
- [ ] **Workshop Walkthrough Recording** — Video walkthrough of each section with commentary on key concepts and emphasis
