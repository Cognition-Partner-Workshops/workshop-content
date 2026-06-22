# Value Narrative Selection

Mapping buyer personas to the right value narratives for effective positioning.

---

## Mapping Buyer Personas to Narratives

Different stakeholders care about different outcomes. The same human + agent delivery capability should be positioned differently depending on who you're talking to:

| Buyer Persona | Primary Narrative | Secondary Narrative | What They Need to Hear |
|---------------|------------------|--------------------|-----------------------|
| **CTO / VP Engineering** | Capacity Unlocking | Engineering Focus Elevation | "Your engineers stop doing mechanical work and focus on architecture and design. The backlog of deferred work actually gets done." |
| **CISO / Security Leadership** | Risk Reduction | Quality Improvement | "Vulnerability exposure windows shrink from 'next sprint' to 'next CI run'. PRs receive automated security review." |
| **FinOps / CFO** | Cost Optimization | Operating Model Efficiency | "Consumption-based pricing with measurable ROI. Each session produces attributable output. Licensing cost avoidance accelerates migration payback." |
| **Delivery Director / Program Manager** | Velocity Multiplication | Operating Model Efficiency | "Migration timelines compress from months to weeks. Parallel execution means campaigns finish before the client loses patience." |
| **Practice Lead / P&L Owner** | Operating Model Efficiency | Competitive Positioning | "Your cost structure becomes the most competitive in RFP scenarios. Margin expands as playbooks mature." |

---

## The Narrative Details

### For CTO / VP Engineering: Capacity Unlocking + Engineering Focus Elevation

**Lead with:** "Your engineers are spending 60%+ of their time on implementation work that could be delegated. The human + agent model shifts that distribution — engineers focus on architecture, design, and the decisions that require human judgment."

**Supporting points:**
- Show the backlog of deferred "should-do" work (dependency updates, test coverage, documentation debt)
- Quantify sprint points consumed by maintenance vs. feature development
- Position agents as unlocking capacity that already exists in the team's backlog
- Frame as team augmentation, not replacement — the engineering team becomes more effective, not smaller

**Evidence to provide:**
- Before/after sprint allocation (60% implementation → 30% implementation + 30% review)
- Time-to-complete for routine tasks (hours of implementation → minutes of review)
- Backlog velocity improvement when maintenance is automated

### For CISO / Security Leadership: Risk Reduction + Quality Improvement

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

### For FinOps / CFO: Cost Optimization + Operating Model Efficiency

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

### For Delivery Director: Velocity Multiplication + Operating Model Efficiency

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

---

## The Decision Matrix

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

## Knowledge Checks

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
*Answer: B — Operating Model Efficiency addresses cost structure competitiveness directly. The human + agent model produces a lower cost-per-unit that translates to more competitive bids.*

**Knowledge Check 3.3**
Q: Why should you prepare different evidence for a CTO vs. a CFO, even though both benefit from the human + agent model?
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

---

## Key Takeaways

- Match the narrative to the buyer persona — the same capability is positioned differently for a CTO, CISO, CFO, and Delivery Director
- Lead with the pain point, not the technology — find what keeps them up at night and map it to the relevant narrative
- Prepare persona-specific evidence (metrics and examples) before every client conversation
- Use the decision matrix to systematically select primary and secondary narratives based on the buyer's stated concerns
