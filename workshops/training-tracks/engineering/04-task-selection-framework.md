# The Task Selection Framework

<a id="toc"></a>
## Table of Contents

- [The Three Tests](#the-three-tests)
- [Gray-Zone Decomposition](#gray-zone-decomposition)
- [Task Classification Practice](#task-classification-practice)
- [Knowledge Check](#knowledge-check)
- [Key Takeaways](#key-takeaways)

---

Knowing *what* to give Devin is as important as knowing *how* to prompt it. This section turns the [Foundations framework](../../foundations/concepts/04-what-to-give-ai.md) into a daily practice.

<a id="the-three-tests"></a>
## The Three Tests

Before creating a Devin session, run the task through three quick mental tests:

### Test 1: The Verifiability Test

> *"Can success be confirmed without human judgment?"*

| Verifiability Level | Example | Verdict |
|-------------------|---------|---------|
| **Objective** — tests pass, scan is clean, build succeeds | "Upgrade Spring Boot from 2.7 to 3.2. All tests must pass." | Give to Devin |
| **Human-judgable** — a person can quickly confirm the output | "Generate API documentation for the new endpoints" | Give to Devin (you review the PR) |
| **Subjective throughout** — requires taste, politics, or unarticulated intent | "Redesign the API to be more intuitive" | Keep for humans |

### Test 2: The Clarity Test

> *"Can I write a complete prompt for this task?"*

If you cannot articulate what "done" looks like in writing, the task is not ready for Devin. This is not a Devin limitation — it is an information problem. Write down the requirements first (or use Ask Devin to research), then delegate implementation.

| Clarity Level | Example | Action |
|--------------|---------|--------|
| **Fully specifiable** | "Add input validation: name max 100 chars, email must match RFC 5322" | Write the prompt, create the session |
| **Partially specifiable** | "Improve the error handling in the payment service" | Research first: ask Devin to analyze current error handling patterns, then you decide what to change |
| **Unspecifiable** | "Make it better" | Not ready for Devin. Define what "better" means first. |

### Test 3: The Judgment Test

> *"Does this task require my subjective input throughout, or just at review time?"*

| Judgment Pattern | Example | Approach |
|-----------------|---------|----------|
| **Judgment at review only** | Bug fix, version upgrade, test generation | Full delegation — Devin implements, you review |
| **Judgment at decision points** | Refactoring, new architecture | Gray-zone decomposition (see below) |
| **Judgment throughout** | Novel architecture design, UX decisions, politically sensitive changes | Keep for humans; use Devin for research |

<a id="gray-zone-decomposition"></a>
## Gray-Zone Decomposition

Most real-world tasks are not purely "give to Devin" or "keep for humans." They live in the gray zone. The pattern for handling them:

```
Stage 1: Research (agent)
    "Analyze X and produce a report"
         ↓
Stage 2: Decide (you)
    Review the report, choose an approach
         ↓
Stage 3: Implement (agent)
    "Implement approach Y with these specifics"
         ↓
Stage 4: Review (you)
    Review the PR, provide feedback
```

**Example: Extracting a microservice from a monolith**

- Stage 1 (agent): *"Analyze the order-service module in monolith-app. List all public methods, their callers, database tables accessed, and any circular dependencies. Output as a markdown report."*
- Stage 2 (you): Review the report. Decide which methods to extract, how to handle shared tables, which communication pattern to use.
- Stage 3 (agent): *"Extract methods X, Y, Z from order-service into a new order-service-v2 Spring Boot app. Use HTTP for inter-service communication. Shared tables should be accessed through the monolith's API until data migration is complete. All existing tests must pass."*
- Stage 4 (you): Review the PR. Leave comments on interface design, error handling, or data consistency concerns.

<a id="task-classification-practice"></a>
## Task Classification Practice

Classify each scenario. For gray-zone tasks, identify the decomposition stages.

| # | Scenario | Your Classification |
|---|----------|-------------------|
| 1 | Upgrade all npm dependencies to latest minor versions across 12 microservices | Strong candidate — objective verification (build passes), repetitive across targets, parallelize with child agents |
| 2 | Design the data model for a new billing feature | Not for Devin — requires business context, trade-off decisions, and unarticulated requirements. Use Devin for research ("What are common billing data model patterns?") but make the decision yourself |
| 3 | Fix a bug: the `/api/reports` endpoint returns 500 when date range exceeds 90 days | Strong candidate — clear reproduction steps, objective verification (the endpoint returns 200), specific scope |
| 4 | Remediate 200 SAST findings across 30 repos | Strong candidate — each finding is well-defined (advisory ID, affected file, recommended fix), verification via re-scan, massively parallelizable |
| 5 | Migrate the team from REST to GraphQL | Gray zone — decompose: Stage 1: agent analyzes current API surface. Stage 2: you decide schema design and migration strategy. Stage 3: agent implements the GraphQL layer. Stage 4: you review |
| 6 | Write a technical design document for the next quarter's architecture changes | Not for Devin — requires organizational context, cross-team alignment, and strategic judgment. Use Devin to generate a first draft skeleton, but the content requires human authorship |
| 7 | Add unit tests for all uncovered functions in the `auth` module | Strong candidate — coverage report defines scope, existing test patterns define conventions, coverage metric verifies success |
| 8 | Refactor the logging framework from log4j to SLF4J across the monolith | Strong candidate — well-defined transformation, verifiable (build passes, tests pass, no log4j imports remain) |
| 9 | Decide whether to use Kafka or RabbitMQ for the new event bus | Not for Devin — architectural decision requiring trade-off analysis against team context. Use Devin for research ("Compare Kafka vs. RabbitMQ for X use case"), then decide |
| 10 | Update API documentation to reflect the latest 3 PRs | Strong candidate — code is the source of truth, documentation accuracy is verifiable, existing format provides conventions |

For the complete framework, see [What to Give AI vs. Not](../../foundations/concepts/04-what-to-give-ai.md).

---

<a id="knowledge-check"></a>
## Knowledge Check

**Knowledge Check 4.1**
Q: You have a task: "Improve the performance of the dashboard page." Which test does this fail?
A) The Verifiability Test — there is no way to measure performance
B) The Clarity Test — "improve performance" is not specific enough to write a complete prompt
C) The Judgment Test — performance optimization requires human judgment throughout
D) None — this is a valid prompt for Devin

*Answer: B — "Improve performance" is too vague to write a complete prompt. What metric? What target? Which part of the page? A better version: "Reduce the p95 load time of GET /api/dashboard from 3s to under 500ms. Profile the endpoint to identify bottlenecks. Run load tests to verify."*

**Knowledge Check 4.2**
Q: A task requires you to decide between two database migration strategies, then implement the chosen one. What is the correct approach?
A) Write one prompt covering both the decision and the implementation
B) Let Devin choose the strategy and implement it
C) Decompose: have Devin research both options (Stage 1), you decide (Stage 2), then Devin implements (Stage 3)
D) Do the entire task yourself since it requires judgment

*Answer: C — This is a gray-zone task. The research and implementation are well-suited for Devin; the strategic decision requires your judgment. Decompose into stages so each party contributes where they are strongest.*

**Knowledge Check 4.3**
Q: You need to apply the same security patch to 40 microservices. What makes this a strong Devin candidate?
A) It is a security task
B) It is repetitive across many targets with objective verification (scan passes) and can be parallelized
C) Security patches are straightforward
D) It involves many files

*Answer: B — The task has clear success criteria (re-scan passes), is repetitive across targets (same patch, different repos), and can be parallelized with child agents — one per microservice.*

<a id="key-takeaways"></a>
## Key Takeaways

- Run every task through three tests before delegating: verifiability, clarity, judgment
- If you cannot write a complete prompt, the task is not ready — research first, then delegate
- Gray-zone tasks should be decomposed: agent research → your decision → agent implementation → your review
- The strongest Devin candidates are: objectively verifiable, clearly specifiable, repetitive across targets, and require judgment only at review time
