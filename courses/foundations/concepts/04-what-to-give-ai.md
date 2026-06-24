# What to Give AI vs. Not

<a id="toc"></a>
## Table of Contents

- [The Decision Framework](#the-decision-framework)
- [Tasks That Belong to AI](#tasks-that-belong-to-ai)
- [Tasks That Don't Belong to AI](#tasks-that-dont-belong-to-ai)
- [The Gray Zone](#the-gray-zone)
- [Prompt Quality and Task Boundaries](#prompt-quality-and-task-boundaries)
- [Exploration Activity](#exploration-activity)
- [Key Takeaways](#key-takeaways)

---

<a id="the-decision-framework"></a>
## The Decision Framework

Not every task belongs to an AI agent. The most effective teams develop intuition for the boundary — knowing which work to delegate and which to keep. This framework helps build that intuition.

The core question: **Can success be verified without human judgment?**

```
                     Can success be verified?
                          │
          ┌───────────────┼───────────────────┐
          │               │                   │
    Objectively      Human can judge      Requires deep
    (tests pass,     the output           human judgment
    scan clean,      (visual UAT,         throughout
    build succeeds,  prototype review,         │
    output matches   tolerable if          ❌ Keep
    spec)            imperfect)            for humans
          │               │
     ┌────┴────┐     ┌────┴────┐
     │         │     │         │
    YES        NO   Low-risk   High-risk
     │         │     │         │
✅ Give to   Refine  ✅ Give   Scope it
 the agent   reqs    to agent  carefully
             first
```

<a id="tasks-that-belong-to-ai"></a>
## Tasks That Belong to AI

These task categories share common traits: clear success criteria, verifiable outcomes, and repeatable patterns.

### Bug Fixes with Clear Reproduction Steps

**Why it works:** The failing test or error message defines success. The agent reproduces, fixes, and verifies — the test turning green is objective proof.

**Example:** "The `/api/users` endpoint returns 500 when the `email` field contains unicode characters. Fix the validation logic. Tests exist in `tests/api/test_users.py`."

### Security Vulnerability Remediation

**Why it works:** SAST/SCA tools produce specific findings with advisory IDs, affected lines, and recommended fixes. Success = the re-scan shows the finding resolved.

**Example:** "Remediate the HIGH severity findings from the latest SonarQube scan in the `auth-service` repo. Run the scan again to verify findings are resolved."

### Version Upgrades and Dependency Bumps

**Why it works:** "Update X from version A to version B. Tests pass." Clear target state, mechanically verifiable.

**Example:** "Upgrade Spring Boot from 2.7 to 3.2 in the `order-service` repo. Migrate Jakarta namespace changes. All existing tests must pass."

### Test Generation

**Why it works:** Coverage reports define what's missing. The agent writes tests; the coverage metric moves. The behavior being tested is already defined by the code.

**Example:** "Increase unit test coverage for `src/services/` from 45% to 80%. Follow the testing patterns established in `tests/services/test_auth.py`."

### Code Migrations and Translations

**Why it works:** Input language/framework → output language/framework is a well-defined transformation. Golden-file testing, output parity verification, or parallel testing (do the same inputs yield the same outputs across the original and migrated systems?) confirms correctness.

**Example:** "Translate the SAS ETL jobs in `etl/legacy/` to Python/Pandas equivalents. Validate that output DataFrames match the expected CSVs in `test_data/expected/`."

### Documentation Updates

**Why it works:** The code is the source of truth. The agent reads the code and generates docs that accurately reflect it. Accuracy is objectively verifiable.

**Example:** "Update the API documentation in `docs/api/` to reflect the new endpoints added in the last 3 PRs. Follow the existing OpenAPI spec format."

### Scheduled Maintenance

**Why it works:** Routine tasks with predictable scope: bump deps, detect dead code, audit licenses. Success is measurable (all deps current, no dead code, no license violations).

<a id="tasks-that-dont-belong-to-ai"></a>
## Tasks That Don't Belong to AI

These tasks require capabilities that are inherently human: creativity, judgment, unarticulated intent, or information the model doesn't have.

### Creative Work and Novel Architecture

**Why it doesn't work:** Architecture decisions require weighing trade-offs that aren't in the codebase — team preferences, future roadmap, organizational constraints, political considerations. There is no "test" for a good architecture.

**Anti-example:** "Design the data model for our new billing system." → The agent will produce *an* answer, but it won't reflect your team's constraints and preferences.

**Better:** Use AI for research ("What are common billing data model patterns?") but make the decision yourself.

### Problems Requiring Unarticulated Intent

**Why it doesn't work:** If you can't write down what "done" looks like, the agent can't verify its own work. Vague requirements produce vague outputs.

**Anti-example:** "Make the dashboard look better." → What does "better" mean? Faster? More readable? Different layout? Different color scheme?

**Better:** "Increase the font size of the dashboard title to 24px and add 16px padding between cards." — Now success is measurable.

### Tasks Requiring Information the Model Doesn't Have

**Why it doesn't work:** The agent can only work with information it can retrieve — codebases, documentation, configured integrations. If the answer lives in someone's head, a private conversation, or an undocumented business rule, the agent cannot access it.

**Anti-example:** "Implement the pricing logic that Sarah described in yesterday's meeting." → The agent wasn't in the meeting.

**Better:** Write down the pricing rules first. Then give the agent the specification: "Implement the pricing logic defined in `docs/pricing-rules.md`."

### Subjective Quality Judgments

**Why it doesn't work:** "Is this code clean?" is subjective. "Does this API feel intuitive?" requires human empathy. "Is this naming consistent with our style?" requires cultural knowledge that may not be fully encoded.

**Better:** Encode style preferences in linters, AGENTS.md, or Knowledge notes. Then the agent can follow codified standards — but the initial judgment of what "good" means must come from humans.

### Politically Sensitive Changes

**Why it doesn't work:** Some changes have organizational implications beyond the code — deprecating a service another team owns, changing a shared API contract, modifying compliance-critical logic. These require human communication and buy-in.

**Better:** Use the agent for the implementation *after* the decision is made and communicated. The PR is the proposal; the human socializes it.

<a id="the-gray-zone"></a>
## The Gray Zone

Many tasks fall between the extremes. The pattern for handling gray-zone work:

**Break it into stages:**
1. **Research stage** (agent): "Analyze the codebase and produce a report on all places where X happens"
2. **Decision stage** (human): Review the report, decide on approach
3. **Implementation stage** (agent): "Implement approach Y as discussed — here are the specific changes needed"
4. **Review stage** (human): Review the PR, provide feedback

This pattern — agent research → human decision → agent implementation → human review — works for most complex tasks. The human contributes judgment at the decision points; the agent contributes labor at the execution points.

### Example: Refactoring a Module

- ❌ "Refactor the auth module" — too vague, requires judgment
- ✅ Stage 1: "Analyze the auth module. List all public methods, their callers, and any circular dependencies. Output as a markdown report."
- ✅ Stage 2: (Human reviews report, decides which methods to extract into a new service)
- ✅ Stage 3: "Extract methods X, Y, Z from `auth.py` into a new `token_service.py`. Update all callers. All existing tests must pass."

<a id="prompt-quality-and-task-boundaries"></a>
## Prompt Quality and Task Boundaries

The quality of agent output correlates directly with the clarity of the input:

| Prompt Quality | Result |
|---------------|--------|
| "Fix the bug" | Agent guesses which bug. May fix the wrong thing. |
| "Fix the 500 error on `/api/users` when email contains unicode" | Agent targets the exact issue. Can verify the fix. |
| "Fix the 500 error on `/api/users` when email contains unicode. The validation logic is in `src/validators/user.py`. Tests are in `tests/test_user_validator.py`. The fix should handle all unicode categories, not just ASCII." | Agent has full context. Produces a targeted, verifiable fix. |

**Include in your prompts:**
- Repository name (if not obvious from context)
- Specific file paths when known
- Expected behavior vs. actual behavior
- Acceptance criteria (what "done" looks like)
- Test or verification mechanism (how to confirm success)
- Constraints (don't change X, must use library Y, follow pattern in Z)

---

<a id="exploration-activity"></a>
## Exploration Activity

**Try this:** Pick three recent tasks from your team's backlog or recently completed work. For each one, evaluate:

1. **Verifiability:** Could success be objectively confirmed? (tests, scans, metrics)
2. **Clarity:** Could you write a prompt that fully specifies the task?
3. **Judgment:** Does it require subjective human decisions at every step, or just at review time?

Score each task:
- **Strong candidate for AI:** High verifiability + high clarity + judgment only at review
- **Gray zone:** Needs to be broken into stages (research → decide → implement → review)
- **Keep for humans:** Low verifiability, requires unarticulated intent, politically sensitive

Over time, this evaluation becomes automatic — you'll develop intuition for what to delegate.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The core question: "Can success be verified without human judgment?" If yes, it's likely a strong candidate for an agent.
- Best for AI: bug fixes, security remediation, version upgrades, test generation, migrations, documentation, scheduled maintenance
- Not for AI: creative architecture, unarticulated intent, information the model can't access, subjective quality, politically sensitive changes
- Gray-zone tasks should be decomposed: agent research → human decision → agent implementation → human review
- Prompt quality directly determines output quality — include repo, paths, acceptance criteria, and constraints
- The goal is not "replace all human work" but "move humans to the decision and review points where their judgment matters"
