# Agent Orchestration

<a id="toc"></a>
## Table of Contents

- [Agents as Workforce](#agents-as-workforce)
- [How Work Gets Distributed](#how-work-gets-distributed)
- [The Intelligence Layer](#the-intelligence-layer)
- [Orchestration Patterns](#orchestration-patterns)
- [Team Topology with Agents](#team-topology-with-agents)
- [Exploration Activity](#exploration-activity)
- [Key Takeaways](#key-takeaways)

---

<a id="agents-as-workforce"></a>
## Agents as Workforce

The most productive framing for AI agents is not "tool" — it is **team member**. A tool waits to be picked up. A team member receives assignments, works independently, asks for help when blocked, and delivers results.

This distinction shapes how you think about work allocation:

| Mental Model | Implication |
|-------------|-------------|
| "AI is a tool" | I use it when I'm already working on something. It accelerates my hands. |
| "AI is a team member" | I assign it work from the backlog. It runs independently while I do something else. |

When you treat agents as workforce, new questions emerge:
- What work is sitting in the backlog because no one has capacity?
- What tasks are well-defined enough that an agent could execute them with a clear prompt?
- How much of our sprint is consumed by routine maintenance that an agent could handle on a schedule?
- If we could parallelize a campaign across 20 agents, what backlog would we clear?

<a id="how-work-gets-distributed"></a>
## How Work Gets Distributed

In a team that includes AI agents, work distributes across three tiers:

```
┌─────────────────────────────────────────────────────────┐
│              HUMAN ENGINEERS                             │
│                                                         │
│  • Architecture and system design                       │
│  • Novel problem-solving                                │
│  • Product decisions requiring judgment                  │
│  • Cross-team coordination                              │
│  • Code review and approval (final authority)           │
│  • Exception handling and escalations                   │
└─────────────────────────────────────────────────────────┘
                         ↕ PR review, feedback, approval
┌─────────────────────────────────────────────────────────┐
│              AI AGENTS (CLOUD)                           │
│                                                         │
│  • Implementation of well-defined tasks                 │
│  • Repetitive work at scale (migrations, upgrades)      │
│  • Event-driven responses (CI fixes, security triage)   │
│  • Scheduled maintenance (dep bumps, dead code, docs)   │
│  • Investigation and diagnosis                          │
│  • Test generation and coverage expansion               │
└─────────────────────────────────────────────────────────┘
                         ↕ delegation, context sharing
┌─────────────────────────────────────────────────────────┐
│              AI ASSISTANCE (LOCAL)                       │
│                                                         │
│  • Code completion and generation in the editor         │
│  • Real-time pair programming                           │
│  • Quick refactors within active focus                  │
│  • Inline explanations and documentation                │
└─────────────────────────────────────────────────────────┘
```

The tiers are not hierarchical — they represent different **modes of interaction**. An engineer might:
1. Use a local AI assistant to explore a design approach (Tier 3)
2. Delegate the implementation to a cloud agent (Tier 2)
3. Review the PR and make an architectural decision about the approach (Tier 1)

<a id="the-intelligence-layer"></a>
## The Intelligence Layer

What makes Devin different from a simple script or CI job that runs code? The intelligence layer:

**Context retrieval:** Before acting, Devin reads the codebase, consults DeepWiki documentation, queries connected integrations (Jira, Datadog, Confluence), and retrieves relevant Knowledge notes. It builds understanding of the system before making changes — much like a human engineer would read docs and code before starting work.

**Decision-making:** Devin chooses an implementation approach, decides which files to modify, selects appropriate testing strategies, and adapts when things don't work. If a test fails, it reads the error, diagnoses the issue, and tries a different approach.

**Verification:** Devin runs your build and test suite to confirm its work. It monitors CI after pushing. It iterates until checks pass or escalates when it cannot resolve an issue.

**Communication:** Devin opens PRs with context-rich descriptions, responds to review comments, and sends status updates. It participates in your team's existing communication channels (GitHub, Slack, Teams).

This is not "just automation." A cron job runs the same commands every time. An intelligent agent reads context, makes decisions, adapts to failures, and communicates results — the same workflow a human engineer follows, but at machine speed.

<a id="orchestration-patterns"></a>
## Orchestration Patterns

### Single Agent (Most Common)

```
Prompt → One Agent → One PR
```

One task, one agent, one output. This is the default for most engineering work: fix a bug, add a feature, upgrade a dependency, write tests.

### Parent-Child (Divide and Conquer)

```
Campaign Prompt → Parent Agent
                      │
                      ├── analyzes scope
                      ├── creates methodology (playbook)
                      │
                      ├── spawns Child 1 → Target A → PR
                      ├── spawns Child 2 → Target B → PR
                      ├── spawns Child 3 → Target C → PR
                      │         ...
                      └── spawns Child N → Target N → PR
                      │
                      └── monitors progress, handles failures
```

For large-scale campaigns where the same pattern applies to many targets. Each child agent works independently on its own VM, its own branch, its own PR. Failures in one child don't affect others.

### Event-Driven (Reactive)

```
Event → Trigger → Agent → Response
```

The event-driven pattern from the [previous section](02-event-driven-reactions.md). Agents respond to signals from your toolchain automatically.

### Scheduled (Proactive Maintenance)

```
Cron → Agent → Maintenance PR
```

Recurring sessions that perform routine hygiene — dependency updates, dead code cleanup, documentation refresh — without human initiation.

<a id="team-topology-with-agents"></a>
## Team Topology with Agents

Consider what changes when agents are part of your team's capacity:

**Before (humans only):**
- Sprint planning accounts for 100% human-hours
- Maintenance work competes with feature work for the same pool of capacity
- Large campaigns (migrations, upgrades) take months because engineers can't be fully dedicated
- Reactive work (incidents, CI failures) interrupts deep work

**After (humans + agents):**
- Sprint planning separates "human-judgment work" from "well-defined implementable work"
- Maintenance runs on schedule without consuming sprint capacity
- Campaigns parallelize across child agents — weeks instead of months
- Reactive work is handled by event-driven agents — humans review results asynchronously
- Engineers focus on architecture, design, and the problems only humans can solve

The key insight: **adding agent capacity does not replace engineers — it changes what engineers spend their time on.** The total output of the team increases because the work that was perpetually deferred (maintenance, coverage, upgrades, compliance) now actually gets done.

---

<a id="exploration-activity"></a>
## Exploration Activity

**Try this:** Look at your team's current sprint or backlog. Categorize the work items:

1. **Requires human judgment throughout** — architecture decisions, novel design, ambiguous requirements
2. **Well-defined but requires implementation capacity** — bug fixes with clear repro steps, version upgrades, test additions, documentation updates
3. **Repetitive across targets** — the same change applied to many services, modules, or repos
4. **Reactive** — responses to CI failures, security findings, incidents

Categories 2, 3, and 4 are where agent capacity compounds. What percentage of your backlog falls into those categories?

If you have access to Ask Devin, try asking: *"What are the common maintenance tasks in [your repo]?"* — this gives you a sense of how Devin builds understanding of a codebase's needs.

---

<a id="key-takeaways"></a>
## Key Takeaways

- Treat agents as workforce (team members receiving assignments), not as tools you pick up
- Work distributes across three tiers: human engineers (judgment), cloud agents (execution), local assistants (acceleration)
- The intelligence layer — context retrieval, decision-making, verification, communication — is what separates agents from scripts
- Orchestration patterns range from single-agent tasks to parallel campaigns with dozens of child agents
- Agent capacity does not replace engineers — it changes what they spend time on, elevating focus to architecture, design, and review
- The backlog items that never get prioritized (maintenance, coverage, compliance) are often the best candidates for agent delegation
