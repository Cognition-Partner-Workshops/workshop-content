# Multi-Agent Workers

<a id="toc"></a>
## Table of Contents

- [Why Multiple Agents](#why-multiple-agents)
- [The Parent-Child Model](#the-parent-child-model)
- [How It Works in Practice](#how-it-works-in-practice)
- [When to Use Multi-Agent](#when-to-use-multi-agent)
- [The Role of Playbooks](#the-role-of-playbooks)
- [Scaling Considerations](#scaling-considerations)
- [Exploration Activity](#exploration-activity)
- [Key Takeaways](#key-takeaways)

---

<a id="why-multiple-agents"></a>
## Why Multiple Agents

Some tasks are too large for a single agent session:

- Upgrading a framework across 50 microservices
- Remediating 200 security findings across an organization
- Migrating 300 ETL jobs from a legacy platform to a modern one
- Adding test coverage to 100 uncovered modules

A single agent working sequentially would take too long. And the tasks are naturally independent — upgrading Service A doesn't depend on upgrading Service B.

Multi-agent orchestration solves this by decomposing the campaign into independent units and executing them in parallel. The total elapsed time drops from weeks to days because N agents work simultaneously, each handling one unit.

```
Sequential (one agent):
  ┌─────┬─────┬─────┬─────┬─────┬─────┬─────┐
  │ S1  │ S2  │ S3  │ S4  │ S5  │ ... │ S50 │
  └─────┴─────┴─────┴─────┴─────┴─────┴─────┘
  ←──────────── weeks ────────────────────────→

Parallel (many agents):
  ┌─────┐
  │ S1  │
  ├─────┤
  │ S2  │
  ├─────┤
  │ S3  │
  ├─────┤
  │ ... │  (all running simultaneously)
  ├─────┤
  │ S50 │
  └─────┘
  ←days→
```

<a id="the-parent-child-model"></a>
## The Parent-Child Model

Devin implements multi-agent orchestration through a parent-child model:

```
┌──────────────────────────────────────────────────┐
│                 PARENT AGENT                       │
│                                                    │
│  1. Receives campaign-level prompt                 │
│  2. Analyzes scope (discovers all targets)         │
│  3. Creates methodology (playbook)                 │
│  4. Spawns child agents — one per target           │
│  5. Monitors progress                              │
│  6. Handles failures and escalations               │
│  7. Reports aggregate results                      │
└──────────────────┬───────────────────────────────┘
                   │
     ┌─────────────┼─────────────┐
     │             │             │
     ▼             ▼             ▼
┌─────────┐  ┌─────────┐  ┌─────────┐
│ CHILD 1  │  │ CHILD 2  │  │ CHILD N  │
│          │  │          │  │          │
│ Own VM   │  │ Own VM   │  │ Own VM   │
│ Own branch│  │ Own branch│  │ Own branch│
│ Own PR   │  │ Own PR   │  │ Own PR   │
│          │  │          │  │          │
│ Target A │  │ Target B │  │ Target N │
└─────────┘  └─────────┘  └─────────┘
```

**Parent agent responsibilities:**
- Understands the full scope of the campaign
- Determines the list of targets (repos, modules, findings)
- Defines the methodology (or references an existing playbook)
- Spawns children with specific, scoped instructions
- Tracks which children succeeded, failed, or need help
- Reports aggregate status to the human

**Child agent responsibilities:**
- Receives a narrowly scoped task (one target)
- Follows the playbook or instructions from the parent
- Works independently on its own VM
- Opens a PR for its specific target
- Succeeds or fails without affecting other children

<a id="how-it-works-in-practice"></a>
## How It Works in Practice

### Example: Framework Upgrade Across 50 Services

**Prompt to parent:**
```
Upgrade all Spring Boot 2.x services in our org to Spring Boot 3.2.
Each service should get its own PR. Migrate the Jakarta namespace,
update deprecated APIs, and ensure all tests pass.
```

**Parent agent executes:**
1. Scans connected repos → finds 50 services on Spring Boot 2.x
2. Identifies the upgrade pattern: `spring-boot-starter` version bump + Jakarta namespace + deprecated API replacements
3. Creates a playbook encoding the upgrade procedure
4. Spawns 50 child agents, one per service

**Each child agent:**
1. Clones its assigned service
2. Follows the upgrade playbook step by step
3. Bumps the Spring Boot version
4. Migrates `javax.*` → `jakarta.*`
5. Replaces deprecated API calls
6. Runs the test suite
7. Iterates until tests pass
8. Opens a PR

**Result:** 50 PRs for human review. Each PR is independent — merge the ones that look good, provide feedback on the rest.

### Example: Security Remediation Campaign

**Prompt to parent:**
```
Remediate all HIGH and CRITICAL findings from our latest SonarQube
org-wide scan. Each finding should get its own session and PR.
```

**Parent agent executes:**
1. Queries the scan results (via MCP or API) → finds 120 HIGH/CRITICAL findings across 30 repos
2. Groups findings by repo and type
3. Spawns child agents — one per finding (or one per repo, depending on density)
4. Each child remediates its assigned finding(s) and verifies with a re-scan

**Result:** 120 findings addressed. Engineers review the PRs. Findings that the agent couldn't resolve are escalated back with an explanation.

<a id="when-to-use-multi-agent"></a>
## When to Use Multi-Agent

Multi-agent orchestration is appropriate when:

| Condition | Why It Matters |
|-----------|---------------|
| **Many independent targets** | Each target can be handled without coordination between children |
| **Same pattern applied repeatedly** | The methodology is consistent — a playbook works for every target |
| **Time is a constraint** | Parallel execution delivers results in days instead of weeks or months |
| **Targets are in separate repos or modules** | No merge conflicts between children working on different targets |
| **Human review scales linearly** | Each PR is reviewable independently |

Multi-agent is **not** appropriate when:
- Tasks are interdependent (one must complete before the next can start)
- Changes affect the same files (merge conflicts between children)
- The methodology is unique per target (no repeatable pattern)
- The campaign is small enough for a single session (< 5 targets)

<a id="the-role-of-playbooks"></a>
## The Role of Playbooks

Playbooks are what make multi-agent orchestration consistent. Without a playbook, each child agent might take a different approach — leading to inconsistent output quality.

A playbook encodes:
- **The procedure** — specific steps to follow in order
- **Quality gates** — verification checks between steps (tests pass, lint clean, scan clear)
- **Conventions** — naming patterns, file organization, commit message format
- **Escalation rules** — when to stop trying and report back to the parent

```
Playbook: "Spring Boot 3 Upgrade"

Step 1: Update pom.xml — bump spring-boot-starter-parent to 3.2.x
Step 2: Run compile — fix any immediate compilation errors
Step 3: Migrate javax → jakarta namespace in all Java files
Step 4: Replace deprecated APIs (list of known replacements)
Step 5: Run full test suite
Step 6: If tests fail, read errors and fix
Step 7: Run again — iterate up to 3 times
Step 8: If still failing, escalate to parent with error details
Step 9: If all checks pass, mark task as complete
```

Each child receives the same procedure. The output is typically predictable, reviewable, and consistent — regardless of which specific service the child is working on.

### Playbook Creation

Playbooks can be:
- **Written by humans** — encode institutional knowledge and proven procedures
- **Created by the parent agent** — analyzes the first few targets and derives a pattern
- **Iterated over time** — refine based on results from previous campaigns

The best playbooks are living documents — they improve each time the team runs a campaign.

<a id="scaling-considerations"></a>
## Scaling Considerations

> **Start small:** Get a handle on a low volume of concurrent children before scaling up. Running 5-10 children first helps you calibrate prompt quality, review load, and downstream system tolerance (CI, API rate limits) before expanding to larger campaigns.

### ACU Budget

Multi-agent campaigns consume ACUs proportionally. For example, if a typical Devin session costs ~5 ACUs and you have 50 targets, expect ~250 ACUs for the full campaign. Factor this into planning before kicking off.

### Failure Handling

Not every child will succeed. The parent agent's monitoring role includes:
- **Tracking completion** — which children finished, which failed
- **Categorizing failures** — transient (retry) vs. structural (escalate)
- **Reporting** — aggregate status to the human (45/50 succeeded, 5 need review)
- **Pausing** — if failure rate exceeds a threshold, stop spawning new children and alert the human

### Review Strategy

50 PRs reviewed serially is still a lot of human time. Strategies to manage review load:

- **Devin Review with AutoFix** — enable Devin Review on each child's PR branch. It catches bugs, security issues, and style violations automatically, and can push fixes before a human ever looks at the PR. This is your first line of defense at scale.
- **Spot-check** — review a sample (first 5, last 5, random 5) in detail, merge the rest if patterns match
- **Automated gates** — rely on CI, Devin Review, and linting to validate. If checks pass, the PR is likely sound
- **Batch merge** — merge green PRs at once after spot-checking a sample
- **Staggered delivery** — have the parent release PRs in batches of 10 so review doesn't pile up

---

<a id="exploration-activity"></a>
## Exploration Activity

**Try this:** Think about a large-scale campaign that your team has done (or deferred) in the past:

1. What was the scope? (How many targets — repos, modules, findings?)
2. Was the pattern repeatable? (Same change applied to each target?)
3. How long did it take (or would it take) with manual effort?
4. How would it decompose into independent units for child agents?

If you have access to Ask Devin, try: *"What would a playbook look like for upgrading [framework] across multiple services?"* — this helps you see how Devin thinks about methodology creation.

Consider: If each target costs ~5 ACUs and you have 50 targets, that's ~250 ACUs with parallel execution completing in ~1-2 days — vs. 50 engineer-hours spread across weeks of competing priorities.

---

<a id="key-takeaways"></a>
## Key Takeaways

- Multi-agent orchestration decomposes large campaigns into independent units executed in parallel
- The parent-child model: parent plans and coordinates, children execute independently
- Each child has its own VM, branch, and PR — failures are isolated and don't cascade
- Playbooks ensure consistency: each child receives the same validated procedure
- Multi-agent is for campaigns with many independent targets and a repeatable pattern — not for interdependent work
- Start small with concurrency, plan ACU budgets, and set failure thresholds before scaling up
- Review strategies (spot-check, automated gates, batch merge) manage the human review load
- Elapsed time drops from weeks/months to days because N agents work simultaneously
