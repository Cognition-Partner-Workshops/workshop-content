# 6. Handling Unexpected Outcomes

<a id="toc"></a>

- [6.1 Agent Gets Stuck](#61-agent-gets-stuck)
- [6.2 CI Fails Unexpectedly](#62-ci-fails-unexpectedly)
- [6.3 Walkthrough Goes Off-Script](#63-walkthrough-goes-off-script)
- [6.4 Client Asks a Question You Cannot Answer](#64-client-asks-a-question-you-cannot-answer)
- [6.5 Scenario Exercises](#65-scenario-exercises)
- [Knowledge Checks](#knowledge-checks)
- [Key Takeaways](#key-takeaways)

---

Recovery patterns for when things go wrong during a client walkthrough or engagement. This section covers the most common failure modes and how to respond.

## 6.1 Agent Gets Stuck

**Symptoms:** Session shows no progress for several minutes. Devin is in a loop (retrying the same action), or waiting for something that will not arrive.

**Recovery patterns:**

| Approach | When to Use | How |
|----------|------------|-----|
| **PR comment** | Devin has opened a PR but is stuck on a specific issue | Leave a comment on the PR with specific guidance: "The test failure is because `DB_URL` is not configured. Add `export DB_URL=postgresql://...` to your setup" |
| **Session message** | Devin has not opened a PR yet; still in the initial phase | Send a message directly in the session UI: "Skip the integration tests for now — focus on getting the unit tests passing first" |
| **Refine the prompt** | Devin is confused about the task scope | Send a follow-up message narrowing the scope: "Focus only on the `/api/users` endpoint. Ignore the other endpoints for now" |
| **Provide missing context** | Devin is searching for something it cannot find | Provide the answer: "The config file is at `config/production.yml`, not `config/prod.yml`" |

**During a client walkthrough:** If Devin gets stuck, use this as a teaching moment — explain: "This is the collaboration model in action. When an agent needs help, it signals via a question or by showing no progress. The human provides context or narrows the scope, and the agent continues." Then demonstrate the recovery live.

## 6.2 CI Fails Unexpectedly

**Symptoms:** Devin's PR has CI failures that are not immediately obvious to resolve.

**Decision tree:**

```
CI fails on Devin's PR
    ↓
Is the failure in Devin's code?
├── Yes → Devin should iterate (it monitors CI and reads logs)
│         If Devin does not iterate: leave a PR comment pointing
│         to the failure: "CI failed on test X — please investigate"
│
└── No (infrastructure / flaky test / pre-existing)
    → Check: does this same failure occur on the base branch?
    ├── Yes → Explain to the client: "This is a pre-existing issue
    │         unrelated to Devin's changes. The CI environment has
    │         [describe issue]."
    └── No → Something about Devin's changes triggers it indirectly.
             Leave a PR comment with context about the failure.
```

**During a client walkthrough:** CI failures are common and expected. Frame them positively: "Devin monitors CI and iterates — this is the same workflow a human developer follows. Watch the session timeline as Devin reads the logs and pushes a fix." If the failure is environmental, explain the difference between code issues (Devin iterates) and infrastructure issues (team resolves).

## 6.3 Walkthrough Goes Off-Script

**Common off-script scenarios and recovery:**

| Scenario | Recovery Pattern |
|----------|-----------------|
| Devin produces a different (but valid) approach | Accept it. Explain: "Devin chose a different implementation pattern. The verification model (tests/CI) confirms it works correctly. This is expected — we specify *what*, not *how*" |
| Devin finishes too quickly (before you finish explaining) | Review the PR together. Use the completed work to walk through what Devin did and why. The artifact is the same; the timing just shifted |
| Devin finishes too slowly (audience gets restless) | Switch to reviewing a pre-completed example. Explain: "Complex tasks take time — the same is true for human engineers. Let me show you the completed result from a previous run" |
| Devin asks a question instead of proceeding | Answer the question live. Explain: "Devin asks for clarification when requirements are ambiguous. This is preferable to guessing — it is the same behavior you want from a human team member" |
| Devin produces output in the wrong repo / branch | Redirect via session message: "Please work in `<correct-repo>` on branch `<correct-branch>`." Explain: prompt specificity prevents this in production |

## 6.4 Client Asks a Question You Cannot Answer

**Defer patterns for questions you cannot answer immediately:**

| Question Type | Defer Pattern |
|--------------|---------------|
| **Detailed technical architecture** (e.g., "What encryption is used for secrets at rest?") | "That is a platform-level security detail I want to answer precisely. Let me get you the exact specification from our engineering team and follow up within 24 hours." |
| **Capability you are unsure about** | "I want to verify that before committing to an answer. Let me confirm with the platform team and get back to you with a definitive response." |
| **Scope or limitations** | "I want to give you an accurate answer on what the platform handles today. Let me verify the specifics and follow up within 24 hours." |
| **Internal implementation details** | "That touches internal platform details I do not have visibility into. Let me connect you with the engineering team who can answer precisely." |

**General principles:**
- Never guess or speculate on technical details. Wrong answers erode trust faster than "I do not know, but I will find out"
- Always pair the defer with a follow-up commitment and timeline
- Redirect to what you CAN show: "I am not sure about that specific detail, but let me show you something directly relevant to your question"

## 6.5 Scenario Exercises

**Exercise 6A: Stuck Agent Recovery**

*Scenario:* You are running a security remediation walkthrough for a client. Devin has been working for 5 minutes but has not opened a PR. The session timeline shows Devin repeatedly trying to run `npm install` but failing with a "network timeout" error.

*What do you do?*

*Reference answer:*
1. Explain to the client: "Devin is hitting a network connectivity issue — this is an infrastructure problem, not a logic problem"
2. Send a session message: "Skip `npm install` — the dependencies are already installed in the environment. Run `npm audit` directly"
3. If that does not resolve it, explain the environment blueprint: "In a configured organization, this would be pre-installed in the VM snapshot. Let me show you what the resolution looks like" — and switch to a pre-completed example
4. Use this as a teaching moment about environment blueprints: "This is why we configure the environment layer — so sessions start ready to build"

**Exercise 6B: Off-Script Migration**

*Scenario:* You are running a MuleSoft → Spring Boot migration walkthrough. Devin converts the endpoint correctly on the first try — the contract test passes without hitting the expected "empty 404 body" bug. The client asks: "So where is the verification value if it just works?"

*What do you do?*

*Reference answer:*
1. Explain: "The verification value is that we *know* it works — not because it looks right, but because the contract tests passed against the OpenAPI spec. Without those tests, we would be relying on 'it compiles and looks reasonable' review"
2. Show the test output and explain what was verified: endpoint existence, schema parity, HTTP status codes, error response format
3. Reference the worked example from the playbook documentation: "In many cases, the contract tests DO catch a divergence — here is a documented example where the 404 response format was wrong"
4. Pivot to the broader point: "The value is not that bugs appear in every walkthrough — it is that you have programmatic proof of parity. When this runs across 50 endpoints in parallel, that proof scales without human review of each one"

**Exercise 6C: Client Questions During Walkthrough**

*Scenario:* During a feature development walkthrough, the client asks: "What happens if Devin writes code that introduces a security vulnerability? How do we catch that?"

*What do you do?*

*Reference answer:*
1. Explain the layered verification model: "Devin's PRs go through the same CI pipeline as human-authored code. If you have SAST scanning in CI, it catches vulnerabilities in Devin's code the same way it catches them in human code"
2. Add Devin Review: "Devin Review is an automated code reviewer that analyzes new PRs for bugs, security issues, and quality problems. It runs automatically on Devin's PRs"
3. Connect to the automation topology: "You can configure an event-driven automation where a SAST finding on Devin's PR triggers a remediation session — the same closed loop we showed in the security walkthrough"
4. Emphasize the PR gate: "Devin never merges its own work. The PR is the review gate. Human reviewers and automated tools verify the code before it reaches production"

---

## Knowledge Checks

**Knowledge Check 6.1**
Q: During a walkthrough, Devin's session shows no progress for 3 minutes. What is your first action?
A) "Restart the session from scratch"
B) "Check the session timeline to understand what Devin is doing — is it looping on an error, waiting for something, or making slow progress on a complex task?"
C) "Tell the client Devin is broken"
D) "Switch to a different walkthrough"
*Answer: B — Always diagnose before acting. Check the session timeline to understand the state. Many cases of "no progress" are actually Devin working on a complex step (installing dependencies, running a long build). If it is genuinely stuck, then apply recovery patterns.*

**Knowledge Check 6.2**
Q: CI fails on Devin's PR during a walkthrough. The failure is a flaky test that also fails intermittently on the base branch. How do you explain this to the client?
A) "Devin broke the tests"
B) "Verify the failure exists on the base branch, then explain: 'This is a pre-existing flaky test unrelated to Devin's changes. The same failure occurs on the base branch without Devin's code'"
C) "Ignore it and move on"
D) "Ask Devin to fix the flaky test"
*Answer: B — Always verify before claiming a failure is pre-existing. Check the base branch CI status. If confirmed pre-existing, explain clearly to the client that this is unrelated to Devin's changes.*

**Knowledge Check 6.3**
Q: A client asks "What encryption standard does Devin use for secrets at rest?" and you do not know the answer. What do you do?
A) "Guess AES-256"
B) "Say 'I do not know' and change the subject"
C) "Acknowledge you want to answer precisely, commit to following up within 24 hours with the exact specification from the engineering team"
D) "Say it is proprietary and you cannot share"
*Answer: C — Never guess on technical security details. Acknowledge the question, commit to a specific follow-up timeline, and deliver. Wrong answers erode trust faster than "I will confirm and follow up."*

## Key Takeaways

- **Diagnose before acting** — check the session timeline to understand state before applying recovery patterns
- **Failures during walkthroughs are recoverable** — every failure mode has a known recovery pattern and can be framed as a teaching moment about the platform
- **Never guess on questions you cannot answer** — commit to a specific follow-up timeline instead. Wrong answers damage credibility more than "I will confirm"
- **The PR feedback loop is the universal recovery tool** — when Devin is stuck, a PR comment or session message with specific guidance is almost always sufficient to unblock
