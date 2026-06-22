# Debug Exercises

For each scenario, identify: (1) what happened, (2) what went wrong, and (3) what the practitioner should have done differently.

## Debug Exercise 1: The Infinite Loop

**What happened:** A team configured an event-driven automation that triggers a Devin session whenever CI fails on `main`. On Monday morning, a failing test was merged. Devin created a session, pushed a fix, which triggered CI, which failed on a *different* test, which triggered another Devin session, which pushed another fix, which triggered CI again. By the time the team noticed, 15 sessions had been created in 2 hours.

<details>
<summary><strong>Diagnosis</strong> (expand after thinking through it)</summary>

**What went wrong:** The automation did not filter out CI failures caused by Devin's own commits. Each Devin fix triggered a new CI run, and if that run failed (even on a different issue), it triggered a new Devin session.

**What the practitioner should have done:**
1. **Filter the trigger:** Exclude CI events where the committer is `devin-ai-integration[bot]`
2. **Set a retry limit:** Maximum 2-3 sessions per original failure (using the original commit SHA as the idempotency key)
3. **Scope the automation:** Only trigger on specific test suites or failure patterns, not all CI failures

</details>

---

## Debug Exercise 2: The Wrong Fix

**What happened:** A developer created a session with the prompt: "The dashboard is slow. Fix it." Devin analyzed the codebase, identified that the dashboard component was re-rendering on every state change, and added `React.memo()` wrappers around 12 components. The PR passed CI. However, the actual performance issue was a slow SQL query in the backend API that took 4 seconds to return — the frontend rendering was never the problem. The team spent 30 minutes reviewing the wrong fix before realizing it did not address the real issue.

<details>
<summary><strong>Diagnosis</strong> (expand after thinking through it)</summary>

**What went wrong:** The prompt was too vague. "The dashboard is slow" does not specify where the slowness is (frontend rendering vs. backend API vs. network), what the target metric is, or how to measure success. Devin made a reasonable assumption (frontend rendering) but it was wrong.

**What the practitioner should have done:**
1. **Diagnose first, then delegate.** Profile the dashboard to identify whether the bottleneck is frontend, backend, or network. Devin can help with this: "Profile the /api/dashboard endpoint and the Dashboard React component. Report where time is spent."
2. **Write a specific prompt:** "The GET /api/dashboard endpoint takes 4s to respond. The bottleneck is the article aggregation query that performs N+1 selects. Refactor to use a single JOIN query. Verify by running: `curl -w '%{time_total}' /api/dashboard` — target response time under 500ms."
3. **Include a verification mechanism with a target metric** so Devin can confirm whether its fix actually solved the problem.

</details>

---

## Debug Exercise 3: The Missing Environment

**What happened:** A developer created a session to fix a bug in a .NET Framework 4.8 application. Devin's session started on the default Ubuntu VM. The build immediately failed with "MSBuild not found." Devin attempted to install MSBuild on Linux, which failed. It then tried using Mono, which partially worked but produced runtime errors due to missing Windows-specific APIs. The session spent 45 minutes trying workarounds before the developer noticed and intervened.

<details>
<summary><strong>Diagnosis</strong> (expand after thinking through it)</summary>

**What went wrong:** The session ran on Ubuntu, but .NET Framework 4.8 requires Windows. The developer did not configure the session to use a Windows host.

**What the practitioner should have done:**
1. **Check the host OS requirement** before creating the session. .NET Framework (not .NET Core/.NET 5+) is Windows-only — this is a hard requirement, not a preference.
2. **Configure a Windows blueprint** for this repository with MSBuild, .NET Framework 4.8 SDK, and NuGet pre-installed.
3. **Include the host OS in the prompt or repo configuration** so the correct VM type is used automatically.
4. As a general rule: when starting with a new repo, verify that Devin's default environment can build it (run the build command) before assigning complex tasks.

</details>
