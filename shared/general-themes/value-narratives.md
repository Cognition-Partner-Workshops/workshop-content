# Value Narratives

Frameworks for articulating Devin's impact across different audiences and decision-making contexts.

## Capacity Unlocking

**For: Engineering Management, Delivery Leadership**

Every engineering team has a backlog of "should-do" work that gets deferred indefinitely: dependency updates, test coverage gaps, documentation debt, accessibility fixes, security finding remediation. This work is valuable but never urgent enough to displace feature development.

Devin unlocks this capacity by executing well-defined tasks autonomously:
- Engineers review PRs (minutes of effort) instead of doing the implementation (hours of effort)
- Routine maintenance runs on schedule without consuming sprint capacity
- Surge capacity is available on demand — spin up child agents for a migration campaign without pulling engineers off their current work

**Metric framing:** "How many story points per sprint are consumed by maintenance tasks? Devin can reclaim those points for product development."

## Engineering Focus Elevation

**For: Engineering Leadership, Product Management**

Engineers are most valuable when they are designing systems, solving novel problems, and making architectural decisions. They are least valuable — and least engaged — when they are doing repetitive, well-defined tasks at scale.

Devin shifts the distribution of engineering effort:
- **Before:** 60% implementation, 20% design, 20% review → engineers spend most of their time on tasks Devin could do
- **After:** 30% implementation (complex/novel), 30% design, 30% review, 10% Devin-output review → engineers focus on what only humans can do

**Narrative:** "Devin does the implementation work so your engineers can focus on architecture, design, and the decisions that require human judgment."

## Velocity Multiplication

**For: Delivery, Program Management**

Devin multiplies throughput without multiplying headcount. For large-scale campaigns (migrations, upgrades, compliance remediation), Devin's child agent model processes N targets in parallel:

- **Sequential (human only):** 50 microservices × 4 hours each = 200 engineer-hours (5 weeks at 40 hrs/week)
- **Parallel (Devin + human review):** 50 child agents × 1 hour each + human review = 50 ACU-hours + 25 review-hours (completed in days, not weeks)

**Narrative:** "The timeline for this migration drops from 5 weeks to 5 days because Devin parallelizes the implementation and engineers review the PRs."

## Quality Improvement

**For: QA Leadership, Security, Compliance**

Devin applies checks consistently and tirelessly:
- **Devin Review** catches bugs in every PR — not just the ones a reviewer happens to notice
- **Scheduled SAST** finds and remediates vulnerabilities before they reach production
- **Test generation** fills coverage gaps systematically, not opportunistically
- **Compliance audits** run on every dependency change, not just before release

**Narrative:** "Devin does not forget to run the security scan. It does not skip the accessibility check because the sprint is tight. It applies the same rigor to every change, every time."

## Risk Reduction

**For: CISO, CTO, Risk Management**

Devin reduces operational risk through consistency and speed:
- **Mean Time to Remediate (MTTR)** drops when Devin responds to incidents automatically — no waiting for an engineer to be available
- **Vulnerability exposure window** shrinks when SAST findings are remediated in hours instead of sprint cycles
- **Change risk** decreases when every PR gets automated review for logic errors, security issues, and regressions
- **Knowledge bus factor** decreases when institutional knowledge is encoded in playbooks and knowledge notes rather than residing in individual engineers' heads

**Narrative:** "Your vulnerability exposure window goes from 'next sprint' to 'next CI run' because Devin remediates findings as soon as they are detected."

## Cost Optimization

**For: FinOps, Finance, Executive Leadership**

Devin's cost model is consumption-based (ACU — Agent Compute Units):
- **Pay for active work** — Devin hibernates during idle time. You pay for compute only when Devin is executing
- **Predictable budgeting** — ACU budgets can be set at the organization, team, or user level with hard enforcement
- **ROI measurement** — Each session produces measurable output (PRs merged, findings remediated, tests added). ROI is directly attributable
- **Licensing cost avoidance** — Migration from expensive proprietary platforms (COBOL/mainframe, SAS, Oracle Forms, Informatica) to open-source or cloud-native alternatives eliminates ongoing per-seat, per-CPU, or per-transaction licensing. The business case for migration is often already approved — the blocker is engineering capacity to execute. Devin removes that blocker

**Narrative:** "The ACU cost for this migration campaign is a fraction of the engineering salary cost, and it delivers the licensing savings months earlier than a manual approach."

## Operating Model Efficiency

**For: SI Practice Leads, Delivery Directors, P&L Owners, Engagement Controllers**

The near-term opportunity is to build the most efficient operating model atop digital and human workers. Efficiency is the primary value driver; quality rises as a consequence of embedding AI intelligence in every task.

### The Efficiency → Quality → Outcomes Cycle

The hybrid operating model creates a virtuous cycle:

1. **Efficiency first**: Devin handles high-volume, well-defined execution at consistent throughput — no idle time, no ramp cost, no context-switching loss
2. **Quality rises**: Consistency and standardization (playbooks, Devin Review, automated testing) raise baseline quality without adding cost
3. **Outcomes improve**: Fewer defects → less rework → faster delivery → shorter time-to-remediation → more capacity for the next cycle

This cycle compounds. As playbooks mature and knowledge notes accumulate, Devin's first-pass success rate improves, human review burden decreases, and margin expands.

### Task Decomposition and Estimatability

The key cost lever is **how well you decompose a large effort into smaller units of work**. A "Spring Boot upgrade" is a campaign with dozens of units. The cost depends on how granularly you carve it, the outcome distribution at each unit (complete on first pass, rework needed, or scrapped for human takeover), and how well the surrounding system supports the agent (environment blueprints, playbooks, CI/CD, test coverage).

Devin's consistency makes each unit more estimatable than traditional human delivery. When every task follows the same playbook and produces the same artifact structure, variance in execution time and cost narrows dramatically. This matters for:

- **SIs bidding on contracts**: Bottom-up cost estimates built from standard units of work (bug fix, migration unit, test module) have lower estimation risk
- **Internal delivery teams**: Sprint planning becomes more reliable when the Devin-executed portion has predictable throughput
- **Managed service providers**: Monthly operational costs are consistent and forecastable

The right granularity is **per-resource or per-module**, not per-line-of-code. Cost can be anchored on whichever proxy the audience uses: per human labor hour equivalent, per people-day, per story point, per ticket, or per module.

**Metric framing:** "Our estimation accuracy improves because the AI-executed portion of work has low variance. The risk concentrates in the human judgment work — which is a smaller portion of total effort."

### Unit-of-Work Economics

The organization that defines the low-level unit of work controls the cost model. Whether an SI bidding on a migration or an internal team planning a quarter, the formula is:

```
Unit cost = (ACUs per unit × $/ACU) + (Review hours per unit × Blended rate)
Total bid = Σ(Unit cost × Quantity) + Orchestration overhead + Margin
```

**Narrative:** "We decompose every engagement into standard units of work. Each unit has a known Devin cost and a known human review cost. This gives us — and you — predictable pricing with tighter confidence intervals than traditional estimation."

See [Billing Model Transformation](https://github.com/Devin-Samples/field-kit/blob/main/docs/sales-guides/billing-model-transformation.md) for worked examples, staffing model scenarios, and engagement billing models.

## Audience-Specific Talking Points

### For Sales Engineering
- Lead with **capacity unlocking** and **velocity multiplication** — these resonate with engineering leaders who feel understaffed
- Show **event-driven patterns** (incident response, security remediation) as "set it and forget it" value
- Use the **divide-and-conquer** pattern to demonstrate scale that is impossible with manual approaches

### For Solution Delivery
- Lead with **playbooks** and **design patterns** — these map directly to engagement deliverables
- Emphasize **toolchain-agnostic stubs** — customers do not need to change their tool stack
- Show **scheduled sessions** as ongoing value that extends beyond the initial engagement

### For Systems Integrators and Managed Service Delivery
- Lead with **operating model efficiency** — the hybrid model (human + digital workers) produces the most competitive cost structure for bidding
- Show the **unit-of-work estimation** model — standardized Devin tasks have low variance, making bids more predictable and margins more defensible
- Emphasize **quality as a built-in outcome** — automated review, testing, and security remediation are not separate line items; they are inherent in the operating model
- Frame **scalability without staffing risk** — Devin capacity scales immediately without bench cost, recruitment lag, or attrition risk
- Show the **margin improvement** path — as playbooks mature, Devin's first-pass success rate improves, human review burden decreases, and margin expands over the life of the engagement
- **Name the competitive displacement risk** — in RFP scenarios, the SI with the most efficient operating model wins. If a competitor bids with a hybrid workforce and you bid headcount-only, you lose on cost, timeline, and quality simultaneously. The cost of not adopting is losing work to organizations that operate more efficiently

### For Training & Enablement
- Lead with the **collaboration model** — Devin works the way engineers already work (PRs, CI, code review)
- Use **hands-on labs** from the partner workshops to demonstrate capabilities in a safe environment
- Emphasize the **continuous improvement cycle** — Devin gets more effective as the team invests in configuration

### For Leadership
- Lead with **risk reduction** and **cost optimization** — these are the decision-making factors
- Frame Devin as **team augmentation**, not replacement — the engineering team becomes more effective, not smaller
- Show the **compounding value** model — initial setup investment yields growing returns over time
- For P&L owners: show the **efficiency → quality → outcomes cycle** — efficiency gains are the entry point, quality improvements compound the value, and the financial impact grows over time
