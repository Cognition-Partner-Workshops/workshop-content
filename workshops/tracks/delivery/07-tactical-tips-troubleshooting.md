# Tactical Tips & Troubleshooting

<a id="toc"></a>
## Table of Contents

- [Common Failure Modes](#common-failure-modes)
- [Knowledge Notes That Improve Success Rate](#knowledge-notes-that-improve-success-rate)
- [Playbook Design Principles](#playbook-design-principles)
- [Knowledge Check](#knowledge-check)
- [Key Takeaways](#key-takeaways)

---

This section covers common failure modes and how to resolve them, plus the organizational practices (Knowledge notes, Playbooks) that improve Devin's success rate over time.

<a id="common-failure-modes"></a>
## Common Failure Modes

### Agent Loops on the Same Error

**Symptoms:** Devin pushes the same fix repeatedly, CI keeps failing with the same error, the session log shows repetitive attempts at the same approach.

**Root cause:** Devin is stuck in a local optimum — it has one hypothesis about the fix and keeps trying variations of it.

**What to do:**
1. **Intervene early.** If you see 3+ attempts at the same fix, do not wait for Devin to exhaust its retries.
2. **Leave a specific PR comment** with a different approach: "The issue is not in the validator — it is in the serializer. Check `src/serializers/order.py` line 87 where the amount is cast to int."
3. If Devin has been stuck for a while and the session has accumulated too much confusing context, **start a new session** with the diagnosis included in the prompt.

### Build/Test Environment Issues

**Symptoms:** Build fails with "command not found", missing dependencies, wrong runtime version.

**Root cause:** The blueprint is missing a dependency that the project requires, or the project needs a specific runtime version that is not installed.

**What to do:**
1. Check the blueprint — is the required tool/runtime installed in the `initialize` block?
2. Check if the dependency is a project-level one that should be in the repo's setup (e.g., `npm install`, `pip install -r requirements.txt`) vs. a system-level one that belongs in the blueprint (e.g., `apt-get install postgresql-client`).
3. Update the blueprint and rebuild the snapshot, or add the install command to the session prompt as a temporary workaround: "First run `apt-get install -y libpq-dev`, then proceed with the task."

### Agent Misunderstands the Task

**Symptoms:** Devin implements something different from what you intended. The PR is technically correct code but solves the wrong problem.

**Root cause:** The prompt was ambiguous or missing critical context.

**What to do:**
1. **Do not iterate on the wrong solution.** It is faster to start a new session than to redirect a misunderstood task through PR comments.
2. **Refine the prompt.** Add the missing context that caused the misunderstanding. Be specific about what "done" looks like.
3. **Include a negative constraint:** "Do NOT change the public API contract" or "Do NOT modify the database schema" to prevent Devin from going in the wrong direction.

### CI Failures the Agent Cannot Resolve

**Symptoms:** Devin pushes fixes for CI failures but they are infrastructure-related (flaky tests, network timeouts, Docker pull rate limits) rather than code-related.

**Root cause:** The CI failure is not caused by Devin's code. It is a pre-existing infrastructure issue.

**What to do:**
1. **Check the base branch.** If the same CI check fails on `main`, the issue is pre-existing — not caused by Devin.
2. **Leave a PR comment** explaining the infrastructure issue: "The Docker pull rate limit is an infrastructure issue. Re-run CI to get past it."
3. If the flaky test is a recurring problem, consider creating a **Knowledge note** documenting it: "The `test_payment_webhook` test is flaky due to external API timeout. Re-run CI if it fails in isolation."

<a id="knowledge-notes-that-improve-success-rate"></a>
## Knowledge Notes That Improve Success Rate

Knowledge notes are persistent context items that Devin retrieves automatically when relevant. Investing in the right Knowledge notes compounds over time.

**High-impact Knowledge notes to create:**

| Knowledge Note | What to Include | Impact |
|---------------|----------------|--------|
| **Coding standards** | Naming conventions, import ordering, error handling patterns, preferred libraries | Devin follows your team's style from the first commit |
| **Architecture decisions** | "We use repository pattern for data access", "All services communicate via gRPC", "State management uses Redux Toolkit" | Devin makes architectural choices consistent with your decisions |
| **Repo conventions** | "Tests go in `__tests__/` adjacent to source", "Migrations use sequential numbering", "All API endpoints require authentication middleware" | Devin produces code that fits the existing structure |
| **Known issues** | "The `payment-webhook` test is flaky — re-run if it fails in isolation", "Docker builds require `--platform linux/amd64` on M1 Macs" | Devin does not waste time debugging known issues |
| **Dependency preferences** | "Use `axios` for HTTP, not `fetch`", "Use `date-fns` not `moment`", "Prefer `pnpm` over `npm`" | Devin uses your preferred libraries without being told each time |

<a id="playbook-design-principles"></a>
## Playbook Design Principles

Playbooks encode a proven methodology into a repeatable procedure. When Devin follows a Playbook, every session applies the same process.

**When to create a Playbook:**
- You have run the same task pattern 3+ times and refined the approach
- Multiple team members need to execute the same methodology
- The task has a specific order of operations that matters (e.g., upgrade framework → migrate namespaces → fix deprecations → run tests)

**How to structure a Playbook:**

1. **Prerequisites** — What must be true before the Playbook starts (e.g., "Repo must have a `build.gradle` file", "CI pipeline must be configured")
2. **Steps** — Ordered, numbered steps with clear success criteria for each
3. **Verification** — How to confirm each step succeeded before moving to the next
4. **Error handling** — What to do if a step fails (retry, skip, escalate)
5. **Output expectations** — What the final PR should contain

**How to iterate on a Playbook:**
- Start with a manual prompt that works well
- Extract the common pattern into a Playbook
- Run it on 2-3 targets and review the results
- Refine the steps based on what worked and what needed manual intervention
- Share with the team once the Playbook consistently produces good results

---

<a id="knowledge-check"></a>
## Knowledge Check

**Knowledge Check 7.1**
Q: Devin has attempted the same CI fix 4 times with no success. The error is "Connection refused to localhost:5432." What should you do?
A) Wait for Devin to figure it out — it needs more attempts
B) Leave a PR comment: "The PostgreSQL service is not running. Add `docker-compose up -d postgres` before running tests"
C) Cancel the session and fix it yourself
D) Update the Playbook to skip the test

*Answer: B — The issue is a missing runtime dependency (PostgreSQL), not a code bug. Give Devin specific guidance on how to resolve the environment issue. Alternatively, update the blueprint to start PostgreSQL automatically.*

**Knowledge Check 7.2**
Q: You notice Devin consistently uses `moment.js` for date handling, but your team standardized on `date-fns`. What is the most efficient long-term fix?
A) Leave a PR comment every time asking Devin to switch
B) Create a Knowledge note: "Use date-fns for all date manipulation. Do not use moment.js."
C) Add `moment.js` to a blocklist in the linter
D) Both B and C

*Answer: D — The Knowledge note provides immediate guidance to Devin; the linter rule provides automated enforcement. Both together give defense in depth. Option A works but does not compound — you would need to comment on every future PR.*

**Knowledge Check 7.3**
Q: When should you create a Playbook for a task?
A) Before the first time you run the task
B) After you have run the same pattern 3+ times and refined the approach
C) Only when multiple teams need the same process
D) When the task is too complex for a single prompt

*Answer: B — Playbooks encode a proven methodology. Creating one before you have refined the approach risks codifying a suboptimal process. Run it manually 3+ times, observe what works, then extract the pattern into a Playbook.*

**Knowledge Check 7.4**
Q: Devin implements something completely different from what you intended. The code is technically correct but solves the wrong problem. What went wrong and what should you do?
A) The agent is buggy — report a bug
B) The prompt was ambiguous — start a new session with a refined, more specific prompt
C) Leave PR comments redirecting Devin to the right solution
D) Approve the PR and open a follow-up session for the correct implementation

*Answer: B — When the fundamental task is misunderstood, iterating through PR comments is inefficient. Start a new session with the missing context that caused the misunderstanding. Include negative constraints if needed: "Do NOT change the API contract."*

<a id="key-takeaways"></a>
## Key Takeaways

- Intervene early when Devin loops — 3+ attempts at the same fix means a different approach is needed
- Distinguish between code issues (Devin can fix) and environment/infrastructure issues (fix the blueprint or guide Devin past it)
- When Devin misunderstands the task, start a new session with a refined prompt rather than redirecting through comments
- Knowledge notes compound over time: coding standards, architecture decisions, known issues, and dependency preferences
- Create Playbooks after refining a task pattern 3+ times; structure them with prerequisites, ordered steps, verification, and error handling
