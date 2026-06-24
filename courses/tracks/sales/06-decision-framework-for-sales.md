# The Decision Framework for Sales

Applying "what to give AI vs. not" to proposal scoping — separating agent-executable work from human-delivered work.

---

## Applying "What to Give AI" to Proposal Scoping

When scoping a proposed engagement, Sales must determine which portions are agent-executable and which remain human-delivered. This directly affects the cost model, the staffing plan, and the timeline.

The core question from the [Foundations decision framework](../../foundations/concepts/04-what-to-give-ai.md) applies directly:

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

---

## The Proposal Decomposition Checklist

For each line item in a proposed engagement, ask:

| Question | If YES | If NO |
|----------|--------|-------|
| Can you write acceptance criteria that a machine can verify? | Agent-executable | Human-delivered |
| Is the pattern repeated across many targets? | Parallelizable (child agents) | Single-session agent or human |
| Does it require creative/architectural decisions? | Human-delivered (architect rate) | Agent-executable |
| Does it require information that isn't in the codebase or docs? | Human-delivered until info is provided | Agent-executable |
| Is it politically sensitive (affects other teams/orgs)? | Human-delivered (human communicates, agent implements after decision) | Agent-executable |

---

## Example: Decomposing a Modernization Engagement

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

---

## Handling Gray-Zone Deliverables

Some deliverables sit in the gray zone. The pattern for handling these in proposals:

**Break them into stages and price each stage appropriately:**

1. **Discovery/Research stage** (agent) — "Analyze the codebase, produce a report on current state, identify patterns and exceptions"
2. **Decision stage** (human) — Architect reviews the report, makes design decisions, documents the approach
3. **Implementation stage** (agent) — "Implement the decided approach across all targets following playbook X"
4. **Review/Approval stage** (human) — Senior engineer reviews all outputs, handles exceptions

**In the proposal:** Price stages 1 and 3 at agent rates. Price stages 2 and 4 at human rates. This makes the cost breakdown transparent and defensible.

---

## Red Flags in Proposal Scoping

Watch for these patterns that indicate a deliverable should NOT be scoped as agent-executable:

- **"It depends on what we find"** — Undefined scope means undefined success criteria
- **"The business rules aren't documented"** — Agent needs information that doesn't exist yet
- **"We'll need stakeholder sign-off at each step"** — Too many human decision points for autonomous execution
- **"The last team tried this and it was harder than expected"** — Hidden complexity suggests high exception rate
- **"This is highly regulated and needs compliance review"** — Human judgment required throughout

These don't mean "don't bid" — they mean "price the human-judgment portions at senior rates and set realistic exception rate estimates."

---

## Knowledge Checks

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

---

## Key Takeaways

- Apply the decision framework to every line item in a proposal: "Can success be verified without human judgment?"
- Agent-executable work must be well-defined, verifiable, and preferably repetitive — this drives parallelization and cost predictability
- Gray-zone deliverables should be decomposed into stages: agent-executed stages (research, implementation) and human-delivered stages (decisions, review)
- Red flags (undocumented business rules, undefined scope, heavy regulation) don't mean "don't bid" — they mean "price with appropriate exception rates and senior staffing"
