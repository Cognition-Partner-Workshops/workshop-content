# Platform Fundamentals

<a id="toc"></a>
## Table of Contents

- [Session Lifecycle](#session-lifecycle)
- [Cloud vs. Local: The Decision Tree](#cloud-vs-local-the-decision-tree)
- [The PR Feedback Loop](#the-pr-feedback-loop)
- [The Verification Model](#the-verification-model)
- [Knowledge Check](#knowledge-check)
- [Key Takeaways](#key-takeaways)

---

This section establishes the operational model you will use every day. If you have completed the [Foundations workshop](../../foundations/), this is a condensed refresher focused on the practitioner's perspective.

<a id="session-lifecycle"></a>
## Session Lifecycle

Every Devin session moves through four states:

```
Active → Waiting for feedback → Hibernated → Resumed
```

| State | What's Happening | Cost Implication |
|-------|-----------------|------------------|
| **Active** | Devin is executing: writing code, running builds, pushing commits | ACUs consumed |
| **Waiting** | Devin has opened a PR or asked a question; monitoring for CI results and comments | Minimal — monitoring only |
| **Hibernated** | No activity for a period; VM state is snapshotted and compute is released | No cost — session is frozen |
| **Resumed** | New input arrives (PR comment, CI result, user message); VM restores from snapshot | ACUs resume on wake |

**What this means for you:**
- You do not need to respond immediately. Devin waits and resumes when you are ready.
- Long-running tasks (multi-day reviews, back-and-forth iterations) are natural. The session persists across the entire lifecycle with no context loss.
- Sessions do not waste compute while waiting for you. Cost is proportional to active work time.

<a id="cloud-vs-local-the-decision-tree"></a>
## Cloud vs. Local: The Decision Tree

Devin provides two execution tiers. Choosing the right one for each task is a daily decision.

```
Is the task quick and iterative (< 5 min)?
    │
    ├── YES → Use local agent (Devin CLI / Devin Desktop)
    │         Examples: quick fix, code exploration,
    │         prototype, explain a module
    │
    └── NO → Does it benefit from autonomy or parallelism?
              │
              ├── YES → Use Cloud Devin
              │         Examples: feature implementation,
              │         migration campaign, scheduled maintenance,
              │         event-driven response
              │
              └── MAYBE → Start locally, /handoff to cloud
                          Examples: triage a bug locally,
                          then hand off the fix for full
                          implementation + CI + PR
```

| Scenario | Start With | Then |
|----------|-----------|------|
| Bug report comes in | Devin CLI: reproduce locally, confirm the issue | `/handoff` to cloud: implement fix, run full test suite |
| Large migration (50 services) | Cloud Devin: parent agent plans campaign | Cloud child agents: execute in parallel |
| New feature design | Devin Local: explore codebase, prototype | Delegate to cloud: full implementation + CI |
| Quick typo fix | Devin CLI: fix directly, push | No handoff needed — stay local |
| Scheduled dependency bump | No local involvement | Cloud Devin on cron: weekly bump + PR |

For a deep dive, see [Cloud vs. Local Agents](../../../shared/general-themes/cloud-vs-local-agents.md).

<a id="the-pr-feedback-loop"></a>
## The PR Feedback Loop

The pull request is Devin's primary interface with human engineers. This is the workflow you will use hundreds of times:

```
You write a prompt
    ↓
Devin implements on its own VM
    ↓
Devin opens a PR
    ↓
CI checks run automatically
    ├── Pass → PR is ready for your review
    └── Fail → Devin reads logs, pushes fix, CI re-runs
    ↓
You inspect the diff
    ├── Approve → Merge (your decision)
    └── Request changes → Leave comments on specific lines
          ↓
Devin reads comments, pushes new commits
    ↓
CI re-runs → Review cycle repeats until approved
```

**Key properties:**
- Devin **never merges its own PRs**. The merge decision is yours.
- CI serves as an automated quality gate. Devin iterates until CI passes or escalates if it cannot resolve the failure.
- PR comments are the communication channel. Multiple reviewers can comment on the same PR — Devin reads and addresses feedback from everyone.
- The PR provides a complete audit trail of what Devin proposed and how it evolved through review.

<a id="the-verification-model"></a>
## The Verification Model

Devin verifies its own work through three mechanisms:

1. **Local build and test** — Devin runs your build and test suite on its VM. If tests fail, it reads the error, diagnoses, and fixes.
2. **CI pipeline** — After pushing, Devin monitors CI checks. If a check fails, it downloads logs, diagnoses, and pushes a fix commit.
3. **Escalation** — After multiple failed attempts at the same issue, Devin asks for help rather than looping indefinitely.

The tighter your verification loop (fast builds, comprehensive tests, clear CI output), the better Devin performs. This is why [Pattern 1: Locally Buildable and Testable Code](../../../shared/general-themes/design-patterns-for-devin.md) is the single most impactful design pattern.

For the full collaboration model, see [Collaboration Model](../../../shared/general-themes/collaboration-model.md).

---

<a id="knowledge-check"></a>
## Knowledge Check

**Knowledge Check 1.1**
Q: A Devin session has been waiting for your PR review for 3 days. What is the compute cost during this waiting period?
A) Full ACU cost — Devin's VM is still running
B) Reduced ACU cost — Devin is in a low-power state
C) No cost — the session hibernated and compute was released
D) It depends on the session configuration

*Answer: C — After inactivity, Devin hibernates its VM. The session state is preserved but compute resources are released. Cost is proportional to active work time only.*

**Knowledge Check 1.2**
Q: You are triaging a production bug. You reproduce it locally in 2 minutes and understand the fix. What is the most efficient execution path?
A) Open a Cloud Devin session with a detailed prompt
B) Fix it with Devin CLI locally, push directly
C) Fix it locally, then `/handoff` to cloud for tests and PR
D) Write a Playbook first, then trigger a Cloud session

*Answer: B — For a quick, well-understood fix, staying local avoids the overhead of cloud session boot. If the fix is small enough to push directly, do so. Use `/handoff` (option C) if the fix requires extensive testing or CI validation that would take time locally.*

**Knowledge Check 1.3**
Q: Devin opens a PR, CI fails on a linting check. What happens next?
A) Devin waits for you to fix the lint issue
B) Devin reads the CI logs, identifies the lint error, pushes a fix, and CI re-runs
C) The session ends with a failure
D) Devin asks you whether to fix it or ignore it

*Answer: B — Devin actively monitors CI results. For code-level failures like lint errors, Devin reads the logs, diagnoses the issue, pushes a fix commit, and waits for CI to re-run. It escalates to you only after multiple failed attempts.*

**Knowledge Check 1.4**
Q: Two reviewers leave different comments on the same Devin PR at the same time. What happens?
A) Devin only reads the first comment
B) Devin reads both comments, addresses both in subsequent commits
C) Devin creates a separate session for each comment
D) The second comment is ignored until the first is resolved

*Answer: B — Devin monitors its PRs for new comments from any reviewer. When comments arrive, Devin resumes from its hibernated state and addresses all pending feedback in subsequent commits.*

<a id="key-takeaways"></a>
## Key Takeaways

- Sessions cycle through Active → Waiting → Hibernated → Resumed; you only pay for active compute time
- Use local agents for quick, interactive work; use Cloud Devin for autonomous, long-running, or parallel tasks; use `/handoff` to bridge the two
- The PR feedback loop (Devin implements → CI runs → you review → Devin iterates) is the core daily workflow
- Devin verifies its own work through local tests, CI monitoring, and escalation — tighter verification loops produce better results
