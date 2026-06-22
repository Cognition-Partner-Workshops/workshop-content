# Prompt Engineering for Agents

<a id="toc"></a>
## Table of Contents

- [Components of a Good Prompt](#components-of-a-good-prompt)
- [Progressive Examples: Bad to Great](#progressive-examples-bad-to-great)
- [Common Anti-Patterns](#common-anti-patterns)
- [How Prompt Quality Correlates with Output Quality](#how-prompt-quality-correlates-with-output-quality)
- [Knowledge Check](#knowledge-check)
- [Key Takeaways](#key-takeaways)

---

The quality of Devin's output correlates directly with the quality of your prompt. This section teaches you to write prompts that produce high-quality results on the first attempt.

<a id="components-of-a-good-prompt"></a>
## Components of a Good Prompt

Every effective prompt contains these elements:

| Component | What It Provides | Example |
|-----------|-----------------|---------|
| **Repository name** | Tells Devin which codebase to work in | `uc-cve-remediation-regulatory-compliance` |
| **File paths** | Narrows scope and prevents Devin from modifying the wrong files | `src/validators/user.py`, `tests/test_user_validator.py` |
| **Expected behavior** | Defines what "working" looks like | "The endpoint should return 200 with a JSON array of articles" |
| **Acceptance criteria** | Defines what "done" looks like | "All existing tests pass, coverage above 80%, no lint errors" |
| **Verification mechanism** | Tells Devin how to confirm success | "Run `./gradlew test` and `./gradlew dependencyCheckAnalyze`" |
| **Constraints** | Prevents unwanted changes | "Do not change the public API contract", "Use the existing ORM, not raw SQL" |

Not every prompt needs all six — a simple bug fix might only need the repo, the bug description, and the test file. But for complex tasks, including all six dramatically improves first-attempt success rate.

<a id="progressive-examples-bad-to-great"></a>
## Progressive Examples: Bad to Great

Here is the same task prompted at four quality levels. Notice how each version adds specificity that reduces ambiguity.

**The task:** Fix a Unicode handling bug in a user validation endpoint.

---

**Level 1 — Vague (likely to produce wrong output):**

```
Fix the bug in the user API.
```

*Problems:* Which bug? Which API? Which repo? Devin has to guess at every dimension. It may fix a different bug, modify the wrong file, or not be able to reproduce the issue at all.

---

**Level 2 — Better (correct direction, missing verification):**

```
Fix the 500 error on the /api/users endpoint in
my-service when the email field contains unicode
characters.
```

*Improvements:* Devin knows the symptom (500 error), the endpoint, and the trigger condition (unicode in email). *Still missing:* Which repo? Which file contains the validation logic? How should Devin verify the fix?

---

**Level 3 — Good (actionable and verifiable):**

```
Fix the 500 error on /api/users in my-service when
the email field contains unicode characters. The
validation logic is in src/validators/user.py. Tests
are in tests/test_user_validator.py. The fix should
handle all unicode categories, not just ASCII. Run
the existing tests to verify.
```

*Improvements:* Devin knows the exact files, has a verification mechanism (existing tests), and has a constraint (handle all unicode categories). This prompt will typically succeed.

---

**Level 4 — Great (complete context, constraints, and verification):**

```
Fix the 500 error on /api/users in my-service when
the email field contains unicode characters like
"user@examplé.com".

Context:
- Validation logic: src/validators/user.py
- Tests: tests/test_user_validator.py
- The validator currently uses an ASCII-only regex
  for email validation

Requirements:
- Use the email-validator library (already in
  requirements.txt) instead of the custom regex
- Handle internationalized domain names (IDN)
- Add test cases for: standard ASCII emails,
  accented characters in local part, IDN domains,
  and malformed inputs
- All existing tests must continue to pass

Verify by running: pytest tests/test_user_validator.py -v
```

*Improvements:* Reproduction steps (example input), root cause hint (ASCII-only regex), specific library to use, edge cases to cover, and an explicit verification command. This prompt typically produces a merge-ready PR on the first attempt.

---

<a id="common-anti-patterns"></a>
## Common Anti-Patterns

| Anti-Pattern | Why It Fails | Fix |
|-------------|-------------|-----|
| **Too vague** — "Make the app faster" | No measurable success criteria. Devin cannot verify its own work. | Specify the metric: "Reduce p95 latency of GET /api/articles from 800ms to under 200ms" |
| **Too prescriptive about implementation** — "On line 42 of app.py, change the for loop to a list comprehension, then on line 55..." | You are writing the code yourself via Devin. Let the agent choose the implementation. | Describe the *outcome*: "Optimize the article query to reduce response time. The current N+1 query pattern is the bottleneck." |
| **Missing verification** — "Add a caching layer to the article service" | No way for Devin to confirm success. It may add caching that is never used, or that breaks existing behavior. | Add: "Verify by running the existing test suite and confirming GET /api/articles response time drops below 200ms on repeated calls" |
| **Scope too large** — "Rewrite the entire authentication system to use OAuth2 instead of session cookies, migrate all existing users, update all API clients, and add MFA support" | Multiple independent workstreams in one session. If any part fails, the entire session stalls. | Decompose: Session 1: "Add OAuth2 provider integration." Session 2: "Migrate session-based auth to OAuth2 tokens." Session 3: "Add MFA support." |
| **No repo specified** — "Fix the login bug" | Devin needs to know which codebase to clone and work in. | Always include the repository name: "Fix the login bug in timesheet-app" |
| **Including 'Open a PR'** — "Fix the bug and open a PR" | Devin opens PRs by default. Including this wastes prompt space and adds no value. | Remove it. Devin will open a PR automatically. |

<a id="how-prompt-quality-correlates-with-output-quality"></a>
## How Prompt Quality Correlates with Output Quality

```
Prompt Quality          Output Quality
─────────────          ──────────────
Vague, no repo         → Guesses, wrong files, may fail entirely
Correct direction      → Right area, but misses edge cases
Actionable + verified  → Solid first attempt, minor review feedback
Complete context       → Merge-ready PR, minimal iteration needed
```

The investment is asymmetric: spending 5 extra minutes writing a specific prompt saves 30+ minutes of review and iteration. For recurring tasks, this investment compounds — a well-written prompt becomes a Playbook that any team member can trigger.

For the foundational framework on what makes a good agent task, see the [Prompt Quality section in What to Give AI](../../foundations/04-what-to-give-ai.md#prompt-quality-and-task-boundaries).

---

<a id="knowledge-check"></a>
## Knowledge Check

**Knowledge Check 2.1**
Q: Which of these prompt components has the highest impact on first-attempt success rate?
A) Repository name
B) Acceptance criteria and verification mechanism
C) Specific file paths
D) Constraints

*Answer: B — Acceptance criteria define "done" and the verification mechanism lets Devin confirm its own work. Without these, Devin cannot close the verify-and-iterate loop. Repository name is necessary but insufficient on its own.*

**Knowledge Check 2.2**
Q: A colleague writes this prompt: "Refactor the order service to be more maintainable." What is the primary problem?
A) It is missing the repo name
B) "More maintainable" is subjective and unverifiable
C) It should specify which files to refactor
D) It needs a constraint section

*Answer: B — "More maintainable" has no objective definition. Devin cannot verify when it has achieved "maintainable." A better version would specify concrete changes: "Extract the order validation logic from OrderService into a separate OrderValidator class. All existing tests must pass."*

**Knowledge Check 2.3**
Q: You have a complex task: redesign the authentication system from session-based to token-based. What is the best prompting strategy?
A) Write one detailed prompt covering the entire migration
B) Break it into stages: research (agent) → decision (you) → implementation (agent) → review (you)
C) Let Devin figure out the decomposition on its own
D) Write a vague prompt and iterate through PR comments

*Answer: B — Complex tasks with multiple independent workstreams should be decomposed into stages. The agent handles research and implementation; you make architectural decisions and review. This prevents a single session from stalling on the entire migration.*

**Knowledge Check 2.4**
Q: Your prompt includes "On line 147, change `response.status(500)` to `response.status(400)`." What is the anti-pattern?
A) Too vague
B) Missing verification
C) Too prescriptive about implementation — you are writing the code
D) Scope too large

*Answer: C — If you already know the exact line change, you might as well make it yourself. Instead, describe the outcome: "The /api/users endpoint returns 500 for validation errors; it should return 400 with a descriptive error message." Let Devin find the right approach.*

<a id="key-takeaways"></a>
## Key Takeaways

- Good prompts include: repo name, file paths, expected behavior, acceptance criteria, verification mechanism, and constraints
- Move from vague ("fix the bug") to complete ("fix X in Y repo, verify with Z command, constrained by W")
- Avoid the extremes: too vague means Devin guesses; too prescriptive means you are writing the code through Devin
- Spending 5 extra minutes on a prompt saves 30+ minutes of iteration — the investment is asymmetric
- Recurring well-written prompts become Playbooks that scale across the team
