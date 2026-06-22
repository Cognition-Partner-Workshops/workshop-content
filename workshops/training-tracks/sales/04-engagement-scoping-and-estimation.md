# Engagement Scoping & Estimation

How to decompose a large client engagement into standard units of work with predictable costs.

---

## The Scoping Process

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

---

## Step 1: Identify the Campaign Type

Each campaign type has characteristic unit definitions and cost profiles:

| Campaign Type | Typical Unit | ACU Range/Unit | Review Hours/Unit | Exception Rate |
|---------------|-------------|---------------|-------------------|----------------|
| **Framework upgrade** | One service/module | 5-12 ACUs | 1-2 hrs | 10-15% |
| **Security remediation** | One finding | 2-5 ACUs | 0.3-1 hr | 10-20% |
| **Language migration** | One source file/copybook | 8-15 ACUs | 2-3 hrs | 15-25% |
| **Test generation** | One module under test | 3-8 ACUs | 0.5-1 hr | 5-10% |
| **Documentation** | One component/API surface | 2-4 ACUs | 0.5-1 hr | 5-10% |
| **Feature development** | One user story | 10-20 ACUs | 2-4 hrs | 20-30% |

---

## Step 2: Define the Unit of Work

The unit must be:
- **Independently executable** — an agent can complete it without depending on other units
- **Independently verifiable** — success criteria can be checked without reference to other units
- **Consistently sized** — units within the same campaign should have similar scope
- **Atomic** — produces one PR, one deliverable, one reviewable artifact

**Good units:** One microservice upgrade. One COBOL copybook translation. One SAST finding remediation. One API endpoint test suite.

**Bad units:** "Upgrade the platform" (too large). "Fix line 47" (too small, insufficient context). "Handle auth and payments" (two unrelated concerns).

---

## Step 3: Estimate ACU Cost Per Unit

ACU cost depends on:
- **Complexity** — How many files, how much logic, how many dependencies?
- **Novelty** — Has this pattern been done before (playbook exists)?
- **Verification depth** — How extensive is the test/validation suite?

| Complexity Tier | Characteristics | ACU Multiplier |
|----------------|----------------|----------------|
| **Simple** | Single file, mechanical transformation, existing tests | 1x base |
| **Standard** | Multiple files, some logic changes, test updates needed | 2x base |
| **Complex** | Many files, business logic, new test creation, edge cases | 3-4x base |

---

## Step 4: Estimate Review Hours Per Unit

Human review time depends on:
- **Output size** — Larger diffs take longer to review
- **Risk level** — High-risk changes (payments, auth, data) require deeper review
- **Reviewer expertise** — Domain experts review faster

| Review Level | When | Hours/Unit |
|-------------|------|------------|
| **Light** | Mechanical change, high test coverage, low risk | 0.3-0.5 hrs |
| **Standard** | Logic changes, moderate risk, good test coverage | 1-2 hrs |
| **Deep** | Business-critical, low test coverage, novel patterns | 2-4 hrs |

---

## Step 5: Calculate Total

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

---

## Case Study Exercise: Scope an Engagement

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

## Knowledge Checks

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

---

## Key Takeaways

- The scoping process is systematic: identify campaign type → define unit → estimate agent cost → estimate review cost → calculate total
- Good units are independently executable, independently verifiable, consistently sized, and atomic
- Complexity tiers (simple/standard/complex) drive both ACU cost and review time estimates
- Exception rates vary by campaign type and should be explicitly included in estimates — they represent the portion requiring human rework
