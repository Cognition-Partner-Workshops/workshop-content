# Working with Agent Output

<a id="toc"></a>
## Table of Contents

- [What to Look For](#what-to-look-for)
- [How to Write Effective Feedback Comments](#how-to-write-effective-feedback-comments)
- [Iteration Patterns](#iteration-patterns)
- [The Review Feedback Loop in Practice](#the-review-feedback-loop-in-practice)
- [Knowledge Check](#knowledge-check)
- [Key Takeaways](#key-takeaways)

---

Reviewing AI-generated code is a skill that differs from reviewing human-written code. This section covers what to look for, how to write effective feedback, and when to iterate vs. start over.

<a id="what-to-look-for"></a>
## What to Look For

When reviewing a Devin PR, evaluate across four dimensions:

| Dimension | What to Check | Red Flags |
|-----------|--------------|-----------|
| **Correctness** | Does the code do what was asked? Does it handle edge cases? Do tests pass? | Missing null checks, untested error paths, logic that works for the happy path but fails on edge cases |
| **Security** | Are secrets handled properly? Is user input validated? Are there injection risks? | Hardcoded credentials, missing input sanitization, SQL built with string concatenation, overly permissive CORS |
| **Completeness** | Are all acceptance criteria met? Are there missing files, migrations, or documentation updates? | Prompt asked for tests but none were written, migration script missing, API docs not updated |
| **Style** | Does the code follow existing conventions? Are imports organized? Is naming consistent? | Different naming convention than the rest of the codebase, unnecessary new dependencies, patterns that contradict existing architecture |

<a id="how-to-write-effective-feedback-comments"></a>
## How to Write Effective Feedback Comments

Devin reads PR comments like a human engineer would. The better your feedback, the better the response.

**Effective comment pattern:**

```
[What's wrong] + [What to do] + [How to verify]
```

**Examples:**

| Weak Comment | Strong Comment |
|-------------|---------------|
| "This is wrong" | "The `formatCurrency` function divides by 100 but the input is already in dollars, not cents. Remove the division. Verify with: `formatCurrency(149.99)` should return `'$149.99'`" |
| "Add tests" | "Add a test case for `createOrder` when the inventory count is 0. It should return a 409 Conflict with message 'Out of stock'. Add this to `tests/test_orders.py`" |
| "Fix the style" | "This file uses camelCase for function names but the rest of the codebase uses snake_case. Rename `getUserProfile` to `get_user_profile` and update all callers" |
| "Looks good but needs work" | "The implementation is correct but missing error handling for the database connection timeout case. Add a try/except around the query in `get_articles()` that returns a 503 with a retry-after header" |

**Why specificity matters:** Devin responds to what you write. A vague comment like "fix the error handling" forces Devin to guess what you mean. A specific comment with the exact function, expected behavior, and verification steps lets Devin act precisely.

<a id="iteration-patterns"></a>
## Iteration Patterns

| Situation | Action |
|-----------|--------|
| Minor issues (style, naming, missing test case) | Leave PR comments — Devin wakes up and addresses them |
| Moderate issues (missing error handling, incomplete feature) | Leave detailed PR comments with specific instructions for each issue |
| Major issues (wrong approach, fundamental misunderstanding) | Consider starting a new session with a refined prompt rather than iterating through comments |
| The approach is correct but implementation is rough | Iterate through 2-3 rounds of PR comments — this is the normal workflow |
| After 3+ rounds of iteration with no convergence | Escalate — start a new session with lessons learned, or handle the remaining work yourself |

**When to comment vs. start a new session:**

```
Is the approach fundamentally correct?
    │
    ├── YES → Leave PR comments. Devin iterates.
    │         (This is the normal workflow — 1-3 rounds)
    │
    └── NO → Start a new session with a refined prompt.
             Include what you learned from the first attempt:
             "Previous attempt used X approach which failed
             because of Y. Instead, use Z approach."
```

<a id="the-review-feedback-loop-in-practice"></a>
## The Review Feedback Loop in Practice

In production, the typical flow is:

1. Devin opens a PR
2. **Devin Review** (the automated PR review agent) runs and flags potential issues
3. You review the diff alongside Devin Review's comments
4. You leave your own feedback comments on specific lines
5. Devin wakes up, reads all comments (yours + Devin Review's), pushes fixes
6. CI re-runs on the new commits
7. You review the updated diff
8. Repeat until satisfied, then merge

Devin Review catches many issues automatically (missing validation, null pointer risks, style violations), reducing the number of issues you need to find yourself. Think of it as a first-pass reviewer that handles the mechanical checks.

For the full collaboration model, see [Foundations — Cloud Agent vs. Local Agent](../../foundations/01-cloud-agent-vs-local-agent.md) and [Foundations — Sessions](../../foundations/06-devin-sessions.md). Quick reference: [Collaboration Model](../../../shared/general-themes/collaboration-model.md).

---

<a id="knowledge-check"></a>
## Knowledge Check

**Knowledge Check 6.1**
Q: Devin opens a PR and you notice the approach is fundamentally wrong — it used inheritance where composition was needed. What should you do?
A) Leave a PR comment explaining the issue
B) Start a new session with a refined prompt that specifies the composition approach
C) Fix the code yourself and push to Devin's branch
D) Leave a comment asking Devin to start over

*Answer: B — When the fundamental approach is wrong, iterating through PR comments is inefficient. Start a new session with a prompt that includes what you learned: "Use composition, not inheritance, for the notification handlers. See the existing CompositeValidator pattern in src/validators/ for the convention."*

**Knowledge Check 6.2**
Q: Which PR comment will produce a better response from Devin?
A) "Add error handling"
B) "Add a try/except around the database query in `get_articles()` (line 42) that catches `ConnectionError` and returns a 503 with a Retry-After header set to 30 seconds"

*Answer: B — The specific comment tells Devin exactly what to do, where to do it, and what the output should look like. The vague comment forces Devin to guess what kind of error handling you want and where.*

**Knowledge Check 6.3**
Q: You are on the third round of PR review comments and Devin keeps not converging on what you want. What should you do?
A) Keep leaving more detailed comments
B) Start a new session with lessons learned from the iterations, or handle the remaining work yourself
C) Approve the PR as-is
D) Ask Devin to explain its reasoning

*Answer: B — After 3+ rounds without convergence, the prompt or approach may need fundamental adjustment. Starting fresh with accumulated context is typically more efficient than continuing to iterate.*

<a id="key-takeaways"></a>
## Key Takeaways

- Review AI-generated PRs across four dimensions: correctness, security, completeness, style
- Write feedback as: [What's wrong] + [What to do] + [How to verify]
- Leave PR comments for iterative improvements; start a new session when the fundamental approach is wrong
- Devin Review provides automated first-pass review — use it to catch mechanical issues so you can focus on design and logic
- Typically 1-3 rounds of PR comments reach convergence; beyond that, consider a fresh session
