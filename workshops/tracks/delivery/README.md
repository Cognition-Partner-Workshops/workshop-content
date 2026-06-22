# Workshop: Delivery Track

## Overview

| | |
|---|---|
| **Focus** | Tactical platform usage, prompt engineering, orchestration patterns, and troubleshooting — everything a delivery engineer needs to be an effective Devin operator |
| **Duration** | 6-8 hours (self-paced; sections are independent after Platform Fundamentals) |
| **Audience** | Engineers and developers at SI partner firms who use Devin to execute their already-defined jobs |
| **Prerequisite** | [Foundations workshop](../../foundations/) (Level 1) |
| **Format** | Reading + knowledge checks + challenge-mode labs (write your own prompts — no copy-paste) + debug exercises |

## Workshop Narrative

You have completed the Foundations workshop and understand the theory: cloud vs. local agents, event-driven patterns, orchestration models, and the framework for deciding what to give AI. This workshop makes it operational.

The Delivery track is built for the practitioner — the person who opens Devin sessions every day, writes prompts, configures environments, reviews PRs, and troubleshoots when things go sideways. By the end, you will know how to:

- Write prompts that produce high-quality output on the first attempt
- Choose the right host OS, blueprint configuration, and network connectivity pattern for any project
- Select the correct orchestration pattern (single agent, parent-child, event-driven, scheduled) for any task
- Review AI-generated code effectively and give feedback that Devin can act on
- Diagnose and resolve common failure modes without starting over

## Getting the Most from This Workshop

> **This is a practice-oriented workshop.** Each section includes knowledge checks to test your understanding and ends with guidance you can apply immediately. The Challenge-Mode Labs at the end require you to write your own prompts from scratch — no copy-paste provided.

Tips:
- **Read Platform Fundamentals first.** It establishes the session lifecycle and feedback loop that every other section builds on.
- **After that, jump to what matters most for your current project.** If you are configuring a new environment, start with Section 4. If you are writing prompts daily, start with Section 3.
- **Try the knowledge checks honestly.** They are designed to surface gaps in understanding before you hit them in production.
- **Use the Challenge-Mode Labs as a self-assessment.** If you can write a complete, well-structured prompt for each scenario, you are ready to operate Devin effectively on real engagements.

---

<a id="toc"></a>
## Table of Contents

| # | Section | What You'll Learn |
|---|---------|-------------------|
| 1 | [Overview](#overview) | Who this is for, format, prerequisites |
| 2 | [Platform Fundamentals](#platform-fundamentals) | Session lifecycle, cloud vs. local decision tree, PR feedback loop, verification model |
| 3 | [Prompt Engineering for Agents](#prompt-engineering-for-agents) | Components of a good prompt, progressive examples, common anti-patterns |
| 4 | [Environment & Architecture Decisions](#environment--architecture-decisions) | Host OS selection, blueprints, network connectivity, secrets, MCP servers |
| 5 | [The Task Selection Framework](#the-task-selection-framework) | Verifiability test, clarity test, judgment test, gray-zone decomposition |
| 6 | [Orchestration Patterns in Practice](#orchestration-patterns-in-practice) | Single agent, parent-child, event-driven, scheduled — when and how |
| 7 | [Working with Agent Output](#working-with-agent-output) | PR review techniques, effective feedback, iteration patterns |
| 8 | [Tactical Tips & Troubleshooting](#tactical-tips--troubleshooting) | Common failure modes, Knowledge notes, Playbook design |
| — | [Challenge-Mode Labs](#challenge-mode-labs) | Write your own prompts for real-world scenarios |
| — | [Debug Exercises](#debug-exercises) | Diagnose what went wrong in failing sessions |
| — | [Gaps & Future Content](#gaps--future-content) | What additional materials are needed |

---

<a id="platform-fundamentals"></a>
## 2. Platform Fundamentals

This section establishes the operational model you will use every day. If you have completed the [Foundations workshop](../../foundations/), this is a condensed refresher focused on the practitioner's perspective.

### Session Lifecycle

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

### Cloud vs. Local: The Decision Tree

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

### The PR Feedback Loop

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

### The Verification Model

Devin verifies its own work through three mechanisms:

1. **Local build and test** — Devin runs your build and test suite on its VM. If tests fail, it reads the error, diagnoses, and fixes.
2. **CI pipeline** — After pushing, Devin monitors CI checks. If a check fails, it downloads logs, diagnoses, and pushes a fix commit.
3. **Escalation** — After multiple failed attempts at the same issue, Devin asks for help rather than looping indefinitely.

The tighter your verification loop (fast builds, comprehensive tests, clear CI output), the better Devin performs. This is why [Pattern 1: Locally Buildable and Testable Code](../../../shared/general-themes/design-patterns-for-devin.md) is the single most impactful design pattern.

For the full collaboration model, see [Collaboration Model](../../../shared/general-themes/collaboration-model.md).

---

### Knowledge Check 2

**Knowledge Check 2.1**
Q: A Devin session has been waiting for your PR review for 3 days. What is the compute cost during this waiting period?
A) Full ACU cost — Devin's VM is still running
B) Reduced ACU cost — Devin is in a low-power state
C) No cost — the session hibernated and compute was released
D) It depends on the session configuration

*Answer: C — After inactivity, Devin hibernates its VM. The session state is preserved but compute resources are released. Cost is proportional to active work time only.*

**Knowledge Check 2.2**
Q: You are triaging a production bug. You reproduce it locally in 2 minutes and understand the fix. What is the most efficient execution path?
A) Open a Cloud Devin session with a detailed prompt
B) Fix it with Devin CLI locally, push directly
C) Fix it locally, then `/handoff` to cloud for tests and PR
D) Write a Playbook first, then trigger a Cloud session

*Answer: B — For a quick, well-understood fix, staying local avoids the overhead of cloud session boot. If the fix is small enough to push directly, do so. Use `/handoff` (option C) if the fix requires extensive testing or CI validation that would take time locally.*

**Knowledge Check 2.3**
Q: Devin opens a PR, CI fails on a linting check. What happens next?
A) Devin waits for you to fix the lint issue
B) Devin reads the CI logs, identifies the lint error, pushes a fix, and CI re-runs
C) The session ends with a failure
D) Devin asks you whether to fix it or ignore it

*Answer: B — Devin actively monitors CI results. For code-level failures like lint errors, Devin reads the logs, diagnoses the issue, pushes a fix commit, and waits for CI to re-run. It escalates to you only after multiple failed attempts.*

**Knowledge Check 2.4**
Q: Two reviewers leave different comments on the same Devin PR at the same time. What happens?
A) Devin only reads the first comment
B) Devin reads both comments, addresses both in subsequent commits
C) Devin creates a separate session for each comment
D) The second comment is ignored until the first is resolved

*Answer: B — Devin monitors its PRs for new comments from any reviewer. When comments arrive, Devin resumes from its hibernated state and addresses all pending feedback in subsequent commits.*

### Key Takeaways

- Sessions cycle through Active → Waiting → Hibernated → Resumed; you only pay for active compute time
- Use local agents for quick, interactive work; use Cloud Devin for autonomous, long-running, or parallel tasks; use `/handoff` to bridge the two
- The PR feedback loop (Devin implements → CI runs → you review → Devin iterates) is the core daily workflow
- Devin verifies its own work through local tests, CI monitoring, and escalation — tighter verification loops produce better results

---

<a id="prompt-engineering-for-agents"></a>
## 3. Prompt Engineering for Agents

The quality of Devin's output correlates directly with the quality of your prompt. This section teaches you to write prompts that produce high-quality results on the first attempt.

### Components of a Good Prompt

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

### Progressive Examples: Bad to Great

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

### Common Anti-Patterns

| Anti-Pattern | Why It Fails | Fix |
|-------------|-------------|-----|
| **Too vague** — "Make the app faster" | No measurable success criteria. Devin cannot verify its own work. | Specify the metric: "Reduce p95 latency of GET /api/articles from 800ms to under 200ms" |
| **Too prescriptive about implementation** — "On line 42 of app.py, change the for loop to a list comprehension, then on line 55..." | You are writing the code yourself via Devin. Let the agent choose the implementation. | Describe the *outcome*: "Optimize the article query to reduce response time. The current N+1 query pattern is the bottleneck." |
| **Missing verification** — "Add a caching layer to the article service" | No way for Devin to confirm success. It may add caching that is never used, or that breaks existing behavior. | Add: "Verify by running the existing test suite and confirming GET /api/articles response time drops below 200ms on repeated calls" |
| **Scope too large** — "Rewrite the entire authentication system to use OAuth2 instead of session cookies, migrate all existing users, update all API clients, and add MFA support" | Multiple independent workstreams in one session. If any part fails, the entire session stalls. | Decompose: Session 1: "Add OAuth2 provider integration." Session 2: "Migrate session-based auth to OAuth2 tokens." Session 3: "Add MFA support." |
| **No repo specified** — "Fix the login bug" | Devin needs to know which codebase to clone and work in. | Always include the repository name: "Fix the login bug in timesheet-app" |
| **Including 'Open a PR'** — "Fix the bug and open a PR" | Devin opens PRs by default. Including this wastes prompt space and adds no value. | Remove it. Devin will open a PR automatically. |

### How Prompt Quality Correlates with Output Quality

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

### Knowledge Check 3

**Knowledge Check 3.1**
Q: Which of these prompt components has the highest impact on first-attempt success rate?
A) Repository name
B) Acceptance criteria and verification mechanism
C) Specific file paths
D) Constraints

*Answer: B — Acceptance criteria define "done" and the verification mechanism lets Devin confirm its own work. Without these, Devin cannot close the verify-and-iterate loop. Repository name is necessary but insufficient on its own.*

**Knowledge Check 3.2**
Q: A colleague writes this prompt: "Refactor the order service to be more maintainable." What is the primary problem?
A) It is missing the repo name
B) "More maintainable" is subjective and unverifiable
C) It should specify which files to refactor
D) It needs a constraint section

*Answer: B — "More maintainable" has no objective definition. Devin cannot verify when it has achieved "maintainable." A better version would specify concrete changes: "Extract the order validation logic from OrderService into a separate OrderValidator class. All existing tests must pass."*

**Knowledge Check 3.3**
Q: You have a complex task: redesign the authentication system from session-based to token-based. What is the best prompting strategy?
A) Write one detailed prompt covering the entire migration
B) Break it into stages: research (agent) → decision (you) → implementation (agent) → review (you)
C) Let Devin figure out the decomposition on its own
D) Write a vague prompt and iterate through PR comments

*Answer: B — Complex tasks with multiple independent workstreams should be decomposed into stages. The agent handles research and implementation; you make architectural decisions and review. This prevents a single session from stalling on the entire migration.*

**Knowledge Check 3.4**
Q: Your prompt includes "On line 147, change `response.status(500)` to `response.status(400)`." What is the anti-pattern?
A) Too vague
B) Missing verification
C) Too prescriptive about implementation — you are writing the code
D) Scope too large

*Answer: C — If you already know the exact line change, you might as well make it yourself. Instead, describe the outcome: "The /api/users endpoint returns 500 for validation errors; it should return 400 with a descriptive error message." Let Devin find the right approach.*

### Key Takeaways

- Good prompts include: repo name, file paths, expected behavior, acceptance criteria, verification mechanism, and constraints
- Move from vague ("fix the bug") to complete ("fix X in Y repo, verify with Z command, constrained by W")
- Avoid the extremes: too vague means Devin guesses; too prescriptive means you are writing the code through Devin
- Spending 5 extra minutes on a prompt saves 30+ minutes of iteration — the investment is asymmetric
- Recurring well-written prompts become Playbooks that scale across the team

---

<a id="environment--architecture-decisions"></a>
## 4. Environment & Architecture Decisions

This section covers the tactical platform operations that determine whether Devin can build, test, and deliver in your project's environment.

### Ubuntu vs. Windows Host

Every Devin session runs on a virtual machine. The host OS determines what runtimes, build tools, and frameworks are available natively.

| Factor | Ubuntu (Linux) | Windows |
|--------|---------------|---------|
| **Default** | Yes — most sessions run on Ubuntu | Must be explicitly configured |
| **Language support** | Java, Python, Node.js, Go, Rust, Ruby, C/C++ | .NET Framework, PowerShell-dependent tooling, Windows-only SDKs |
| **.NET** | .NET Core / .NET 5+ (cross-platform) | .NET Framework 4.x (Windows-only) |
| **Build tools** | make, cmake, gradle, maven, npm, pip, cargo | MSBuild, NuGet, Visual Studio Build Tools |
| **Shell** | Bash | PowerShell, cmd |
| **Container support** | Docker native | Docker Desktop (WSL2 backend) |
| **Use when** | Most projects — it is the default for a reason | Legacy .NET Framework, PowerShell-heavy automation, Windows-specific tooling (e.g., certain IIS configurations, COM interop) |

**Decision matrix:**

```
Is the project .NET Framework (not .NET Core/5+)?
    ├── YES → Windows host
    └── NO
        │
Does the build require PowerShell or Windows-only tools?
    ├── YES → Windows host
    └── NO
        │
Does the project use COM interop or Windows-specific APIs?
    ├── YES → Windows host
    └── NO → Ubuntu host (default)
```

When in doubt, use Ubuntu. It covers the vast majority of modern development stacks and is the default for Devin sessions.

### Blueprint Configuration

Blueprints are YAML configurations that define what is pre-installed on Devin's VM. They have two sections with distinct purposes:

| Section | When It Runs | What It's For | Persisted? |
|---------|-------------|---------------|------------|
| **`initialize`** | Once, when the snapshot is built | Installing software, compiling tools, downloading large artifacts | Yes — baked into the VM snapshot |
| **`maintenance`** | Every session start (after snapshot restore) | Refreshing ephemeral state: rotating tokens, updating caches, re-authenticating | No — runs fresh each time |

**What goes where:**

```yaml
# initialize — runs once, persists in snapshot
initialize:
  - apt-get update && apt-get install -y postgresql-client redis-tools
  - curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
  - apt-get install -y nodejs
  - npm install -g pnpm@9
  - pip install pre-commit

# maintenance — runs every session start
maintenance:
  - aws sts get-caller-identity  # verify AWS creds are valid
  - gcloud auth print-access-token > /dev/null  # verify GCP auth
  - npm config set registry https://your-private-registry.com
```

**Org-level vs. repo-level blueprints:**

| Scope | When to Use | Example |
|-------|------------|---------|
| **Org-level** | Tools and runtimes shared across many repos | Node.js 20, Java 17, Docker, `pre-commit` |
| **Repo-level** | Project-specific dependencies and configurations | Gradle wrapper, specific Python packages, custom build scripts |

Org-level blueprints run *before* repo-level blueprints. A common pattern: org-level installs the language runtime; repo-level installs project dependencies.

**Testing blueprint changes:** Blueprint changes create a new snapshot build. You can verify by checking the build log URL provided when the snapshot is built. If a command fails during snapshot build, the snapshot will not update — the previous working snapshot remains active.

### Network Connectivity

Devin's VM needs network access to reach your private resources (databases, APIs, artifact registries). The three common patterns:

| Pattern | How It Works | When to Use |
|---------|-------------|------------|
| **SSM Port Forwarding** (AWS) | Creates an encrypted tunnel from Devin's VM to a private VPC resource via AWS Systems Manager | RDS databases, internal APIs behind private subnets. No public IP required on the target. |
| **Cloud SQL Auth Proxy** (GCP) | Google's binary creates an mTLS tunnel to Cloud SQL instances | Any GCP Cloud SQL database. Handles certificate rotation automatically. |
| **VPN / Static Egress IPs** | Route Devin's traffic through a VPN connection or allowlist Devin's static egress IPs on your firewall | On-premise resources, third-party APIs with IP allowlisting, corporate network resources behind a VPN gateway |

**The three-layer connectivity model:**

1. **Network Path** — How does traffic get from Devin's VM to the target? (tunnel, VPN, direct internet)
2. **Transport Security** — Is the connection encrypted? (TLS, mTLS, SSH tunnel)
3. **Identity & Authentication** — How does Devin prove who it is? (IAM database auth, service account tokens, username/password from secrets)

For detailed configuration guides, see the [network connectivity patterns in automations-and-integrations](https://github.com/Devin-Samples/automations-and-integrations/tree/main/network-connectivity).

### Secret Management

Secrets are credentials (API keys, database passwords, service account tokens) injected into Devin's session environment. They are never stored in prompts or code.

| Scope | Visibility | When to Use |
|-------|-----------|------------|
| **Org-scoped** | Available to all sessions in the org | Shared infrastructure credentials: artifact registry tokens, CI/CD service accounts, monitoring API keys |
| **User-scoped** | Available only to sessions created by one user | Personal access tokens, individual developer credentials |
| **Repo-scoped** | Available only to sessions working on a specific repo | Repo-specific API keys, database credentials for a specific service |

**How to reference secrets in prompts and blueprints:**

Use the `${SECRET_NAME}` syntax. The platform substitutes the actual value at runtime — you never see or type the real credential.

```yaml
# In a blueprint maintenance block:
maintenance:
  - echo "machine your-registry.com login _token password ${ARTIFACT_REGISTRY_TOKEN}" > ~/.netrc
```

**TOTP/2FA secrets:** For services requiring two-factor authentication, store TOTP secrets with a `_2FA_` prefix (e.g., `_2FA_JIRA_TOTP`). Devin generates time-based codes automatically at login.

**Best practice:** Use the narrowest scope possible. Org-scoped secrets are convenient but increase blast radius if compromised. Default to repo-scoped unless multiple repos need the same credential.

### MCP Server Configuration

MCP (Model Context Protocol) servers extend Devin's capabilities by connecting it to external tools — issue trackers, observability platforms, documentation systems, and databases.

| MCP Server | What It Enables | Example Use |
|------------|----------------|-------------|
| **Jira** | Read tickets, update status, link PRs | Devin reads acceptance criteria from a Jira ticket, implements, and links the PR back |
| **Datadog** | Query logs, traces, and metrics | Devin investigates an incident by querying Datadog for error patterns |
| **Confluence** | Read and update documentation | Devin reads architecture docs before implementing, updates docs after |
| **Azure DevOps** | Read work items, manage pipelines | Devin reads ADO work items and updates them with PR links |
| **Databases** (PostgreSQL, MySQL, etc.) | Query schemas, run migrations, validate data | Devin checks schema before writing migration scripts |

**Configuration:** MCP servers are configured once at the organization level in Devin's settings. Once configured, they are available to every session automatically — no per-session setup required.

**How they help:** Without MCP servers, Devin can only work with what is in the Git repository. With MCP servers, Devin can:
- Read the Jira ticket that defines the task (instead of you copy-pasting requirements into the prompt)
- Query production logs to understand an incident (instead of you pasting log snippets)
- Check database schemas before writing migrations (instead of guessing or relying on outdated ERD diagrams)
- Update documentation in Confluence after implementing a change (closing the docs-drift gap)

---

### Knowledge Check 4

**Knowledge Check 4.1**
Q: Your project uses .NET Framework 4.8 with MSBuild. Which Devin host OS should you configure?
A) Ubuntu — it is the default and supports .NET
B) Windows — .NET Framework 4.x requires Windows
C) Either — .NET Framework runs on both
D) Ubuntu with Wine for .NET compatibility

*Answer: B — .NET Framework (as opposed to .NET Core/.NET 5+) is Windows-only. You must configure a Windows host for projects that depend on it.*

**Knowledge Check 4.2**
Q: You need to install Node.js 20 and pnpm for every session across 15 repos. Where should this go in the blueprint?
A) Repo-level `maintenance` block for each repo
B) Org-level `initialize` block
C) Repo-level `initialize` block for each repo
D) Org-level `maintenance` block

*Answer: B — Tools shared across many repos belong in the org-level `initialize` block. They are installed once when the snapshot is built and persist across sessions. Putting them in `maintenance` would reinstall them every session start, wasting time.*

**Knowledge Check 4.3**
Q: You have a database password that only the `order-service` repo needs. What secret scope should you use?
A) Org-scoped — for convenience
B) User-scoped — it is your credential
C) Repo-scoped — narrowest scope that covers the need
D) Store it in the repository's `.env` file

*Answer: C — Use the narrowest scope possible. Repo-scoped secrets are only available to sessions working on that specific repo, minimizing blast radius. Never store secrets in files committed to the repo.*

**Knowledge Check 4.4**
Q: You configure a Jira MCP server at the org level. How do individual sessions access it?
A) Each session must be configured to use the MCP server
B) Users include a flag in their prompt to enable MCP
C) It is automatically available to every session — no per-session setup
D) Only sessions created by the user who configured it can use it

*Answer: C — MCP servers configured at the org level are part of Devin's shared context layer. They are automatically available to every session without additional configuration.*

### Key Takeaways

- Use Ubuntu by default; use Windows only for .NET Framework, PowerShell-dependent tooling, or Windows-only APIs
- Blueprint `initialize` runs once (persists in snapshot); `maintenance` runs every session start (for ephemeral state)
- Use org-level blueprints for shared tools; repo-level for project-specific dependencies
- Reference secrets with `${SECRET_NAME}` — never put credentials in prompts or code
- MCP servers extend Devin beyond Git: issue trackers, observability, documentation, databases — configured once, available everywhere

---

<a id="the-task-selection-framework"></a>
## 5. The Task Selection Framework

Knowing *what* to give Devin is as important as knowing *how* to prompt it. This section turns the [Foundations framework](../../foundations/04-what-to-give-ai.md) into a daily practice.

### The Three Tests

Before creating a Devin session, run the task through three quick mental tests:

#### Test 1: The Verifiability Test

> *"Can success be confirmed without human judgment?"*

| Verifiability Level | Example | Verdict |
|-------------------|---------|---------|
| **Objective** — tests pass, scan is clean, build succeeds | "Upgrade Spring Boot from 2.7 to 3.2. All tests must pass." | Give to Devin |
| **Human-judgable** — a person can quickly confirm the output | "Generate API documentation for the new endpoints" | Give to Devin (you review the PR) |
| **Subjective throughout** — requires taste, politics, or unarticulated intent | "Redesign the API to be more intuitive" | Keep for humans |

#### Test 2: The Clarity Test

> *"Can I write a complete prompt for this task?"*

If you cannot articulate what "done" looks like in writing, the task is not ready for Devin. This is not a Devin limitation — it is an information problem. Write down the requirements first (or use Ask Devin to research), then delegate implementation.

| Clarity Level | Example | Action |
|--------------|---------|--------|
| **Fully specifiable** | "Add input validation: name max 100 chars, email must match RFC 5322" | Write the prompt, create the session |
| **Partially specifiable** | "Improve the error handling in the payment service" | Research first: ask Devin to analyze current error handling patterns, then you decide what to change |
| **Unspecifiable** | "Make it better" | Not ready for Devin. Define what "better" means first. |

#### Test 3: The Judgment Test

> *"Does this task require my subjective input throughout, or just at review time?"*

| Judgment Pattern | Example | Approach |
|-----------------|---------|----------|
| **Judgment at review only** | Bug fix, version upgrade, test generation | Full delegation — Devin implements, you review |
| **Judgment at decision points** | Refactoring, new architecture | Gray-zone decomposition (see below) |
| **Judgment throughout** | Novel architecture design, UX decisions, politically sensitive changes | Keep for humans; use Devin for research |

### Gray-Zone Decomposition

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

### Task Classification Practice

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

For the complete framework, see [What to Give AI vs. Not](../../foundations/04-what-to-give-ai.md).

---

### Knowledge Check 5

**Knowledge Check 5.1**
Q: You have a task: "Improve the performance of the dashboard page." Which test does this fail?
A) The Verifiability Test — there is no way to measure performance
B) The Clarity Test — "improve performance" is not specific enough to write a complete prompt
C) The Judgment Test — performance optimization requires human judgment throughout
D) None — this is a valid prompt for Devin

*Answer: B — "Improve performance" is too vague to write a complete prompt. What metric? What target? Which part of the page? A better version: "Reduce the p95 load time of GET /api/dashboard from 3s to under 500ms. Profile the endpoint to identify bottlenecks. Run load tests to verify."*

**Knowledge Check 5.2**
Q: A task requires you to decide between two database migration strategies, then implement the chosen one. What is the correct approach?
A) Write one prompt covering both the decision and the implementation
B) Let Devin choose the strategy and implement it
C) Decompose: have Devin research both options (Stage 1), you decide (Stage 2), then Devin implements (Stage 3)
D) Do the entire task yourself since it requires judgment

*Answer: C — This is a gray-zone task. The research and implementation are well-suited for Devin; the strategic decision requires your judgment. Decompose into stages so each party contributes where they are strongest.*

**Knowledge Check 5.3**
Q: You need to apply the same security patch to 40 microservices. What makes this a strong Devin candidate?
A) It is a security task
B) It is repetitive across many targets with objective verification (scan passes) and can be parallelized
C) Security patches are always straightforward
D) It involves many files

*Answer: B — The task has clear success criteria (re-scan passes), is repetitive across targets (same patch, different repos), and can be parallelized with child agents — one per microservice.*

### Key Takeaways

- Run every task through three tests before delegating: verifiability, clarity, judgment
- If you cannot write a complete prompt, the task is not ready — research first, then delegate
- Gray-zone tasks should be decomposed: agent research → your decision → agent implementation → your review
- The strongest Devin candidates are: objectively verifiable, clearly specifiable, repetitive across targets, and require judgment only at review time

---

<a id="orchestration-patterns-in-practice"></a>
## 6. Orchestration Patterns in Practice

This section covers when and how to use each orchestration pattern. For the theoretical foundations, see [Agent Orchestration](../../foundations/03-agent-orchestration.md) and [Platform Capabilities](../../../shared/general-themes/platform-capabilities.md).

### Pattern 1: Single Agent

**When to use:** The default for most tasks. One prompt, one agent, one PR.

**Best for:**
- Bug fixes
- Feature implementation
- Version upgrades (single repo)
- Test generation
- Documentation updates
- Any self-contained task that does not need to be parallelized

**Example prompt:**

```
Fix the N+1 query in the articles feed endpoint in
my-api-service. The endpoint GET /api/articles/feed
currently executes one query per article to fetch the
author profile. Refactor to use a JOIN or batch fetch.

Verify by running: ./gradlew test
Confirm that the feed endpoint test response time
drops below 200ms for 100 articles (currently ~2s).
```

**When NOT to use:** When the same task needs to be applied to many targets (use parent-child) or when the task should run automatically in response to events (use event-driven) or on a schedule (use scheduled).

### Pattern 2: Parent-Child (Divide and Conquer)

**When to use:** For campaigns across N independent targets where each target follows the same methodology.

**Best for:**
- Framework upgrades across many microservices
- Security remediation across many repos
- Test generation across many modules
- Code style migration across many packages
- Any "do X to each of N things" pattern

**How to set it up:**

1. **Plan the decomposition** — Identify the list of targets (repos, modules, services) and verify each can be handled independently.
2. **Write a Playbook** — Encode the methodology so every child follows the same process. A Playbook ensures consistency even when targets have slight differences.
3. **Set concurrency limits** — Avoid overwhelming CI systems or API rate limits. Start with 5-10 concurrent children and adjust based on results.
4. **Write the parent prompt** — The parent analyzes scope, creates children, and monitors progress.

**Example parent prompt:**

```
Upgrade Spring Boot from 2.7 to 3.2 across the
following microservices: order-service, user-service,
notification-service, payment-service, inventory-service.

For each service:
1. Update Spring Boot version in build.gradle
2. Migrate javax.* imports to jakarta.*
3. Update deprecated API usage
4. Run ./gradlew test to verify
5. Document breaking changes in the PR description

Use the spring-boot-3-upgrade Playbook. Run up to
5 services in parallel.
```

**Monitoring children:** The parent agent tracks child progress. If a child fails, the parent can retry, adjust the approach, or escalate. You will see individual PRs from each child — review them like any other PR.

### Pattern 3: Event-Driven

**When to use:** When work should happen automatically in response to external signals — no human needs to create a session.

**Common triggers:**

| Trigger Source | Event | Devin's Response |
|---------------|-------|-----------------|
| CI pipeline | Build fails on a branch | Read failure logs, diagnose, push fix |
| SAST tool | New HIGH/CRITICAL finding | Read advisory, remediate, re-scan to verify |
| Issue tracker | Ticket tagged for implementation | Read acceptance criteria, implement, link PR |
| Alerting system | Production alert fires | Query logs via MCP, investigate, open fix PR |

**Configuration walkthrough:**

Event-driven automations are configured in Devin's Automations settings. The basic flow:

1. **Define the trigger** — Which event source (GitHub webhook, Jira webhook, PagerDuty webhook)?
2. **Define the filter** — Which events should trigger a session (e.g., only CI failures on `main`, only Jira tickets tagged `Devin:Implementation`)?
3. **Define the prompt template** — What should Devin do when the event fires? Use template variables to inject event data (e.g., `{{branch_name}}`, `{{failure_log_url}}`).
4. **Set safeguards** — Filter out Devin's own events (prevent infinite loops), set maximum retry counts, use idempotency keys.

**Safeguard checklist:**
- [ ] Filter out events from `devin-ai-integration[bot]` to prevent infinite loops
- [ ] Set maximum sessions per trigger (e.g., 3 retries per CI failure)
- [ ] Use idempotency keys to prevent duplicate sessions from the same event
- [ ] Test with a single event before enabling for all events

### Pattern 4: Scheduled

**When to use:** For recurring maintenance that should happen on a cadence without human initiation.

**Common schedules:**

| Task | Cadence | Why Scheduled? |
|------|---------|---------------|
| Dependency version bumps | Weekly (Monday morning) | Catches outdated packages before they accumulate |
| Dead code detection and cleanup | Monthly | Keeps codebase lean without consuming sprint capacity |
| Documentation freshness check | After major releases | Ensures docs stay in sync with code |
| License compliance audit | Quarterly | Meets compliance requirements without manual effort |
| Security scanning + remediation | Weekly | Reduces vulnerability backlog continuously |

**Configuration walkthrough:**

Scheduled sessions are configured in Devin's settings or via the API:

1. **Define the schedule** — Cron expression or human-readable recurrence (weekly, daily, etc.)
2. **Define the prompt** — What Devin should do on each run. Include verification steps.
3. **Define the target repo** — Which repository to work against.
4. **Set expectations for output** — Should Devin always open a PR (even if no changes needed)? Should it update a tracking document?

**Example scheduled session prompt:**

```
Check all npm dependencies in my-frontend-app for
available minor and patch updates. Run npm update to
apply non-breaking upgrades. Run npm test and
npm run build to verify nothing is broken. If any
upgrade breaks the build, revert that specific
upgrade and document why. Create a
DEPENDENCY_UPDATES.md summarizing what was updated.
Title the PR "chore: weekly dependency bump".
```

---

### Knowledge Check 6

**Knowledge Check 6.1**
Q: You need to apply a security patch to 30 microservices. Which orchestration pattern should you use?
A) Single agent — run one session per repo manually
B) Parent-child — one parent plans the campaign, spawns 30 children
C) Event-driven — wait for security alerts on each repo
D) Scheduled — run the patch on a weekly cadence

*Answer: B — This is a classic divide-and-conquer scenario. The parent agent identifies the targets and methodology, then spawns children that execute in parallel — each with its own VM, branch, and PR.*

**Knowledge Check 6.2**
Q: You want Devin to automatically fix CI failures on your `main` branch. Which pattern is this?
A) Single agent
B) Parent-child
C) Event-driven
D) Scheduled

*Answer: C — CI failures are external events that trigger Devin automatically. The event-driven pattern responds to webhooks from your CI system without human initiation.*

**Knowledge Check 6.3**
Q: When setting up event-driven automations, what is the most critical safeguard?
A) Setting a high concurrency limit for fast response
B) Filtering out Devin's own events to prevent infinite loops
C) Using detailed prompt templates
D) Connecting to multiple event sources simultaneously

*Answer: B — If Devin's own commits trigger CI, and CI failures trigger Devin, you get an infinite loop. Filtering out events from `devin-ai-integration[bot]` is the single most critical safeguard.*

**Knowledge Check 6.4**
Q: Your team wants dependency bumps done weekly without anyone remembering to trigger them. Which pattern fits?
A) Single agent — someone creates a session each Monday
B) Parent-child — a parent agent manages the schedule
C) Event-driven — triggered by new package releases
D) Scheduled — cron-based recurring session

*Answer: D — Scheduled sessions run on a cron expression without human initiation. Set it once and Devin opens a dependency bump PR every Monday morning automatically.*

### Key Takeaways

- Single agent is the default — use it for most self-contained tasks
- Parent-child divides large campaigns across many targets; each child is independent with its own VM, branch, and PR
- Event-driven responds to external signals (CI failures, security findings, ticket assignments) automatically — no human creates the session
- Scheduled runs maintenance on a cadence (weekly dependency bumps, monthly dead code cleanup) without human initiation
- For event-driven patterns, always filter out Devin's own events to prevent infinite loops

---

<a id="working-with-agent-output"></a>
## 7. Working with Agent Output

Reviewing AI-generated code is a skill that differs from reviewing human-written code. This section covers what to look for, how to write effective feedback, and when to iterate vs. start over.

### What to Look For

When reviewing a Devin PR, evaluate across four dimensions:

| Dimension | What to Check | Red Flags |
|-----------|--------------|-----------|
| **Correctness** | Does the code do what was asked? Does it handle edge cases? Do tests pass? | Missing null checks, untested error paths, logic that works for the happy path but fails on edge cases |
| **Security** | Are secrets handled properly? Is user input validated? Are there injection risks? | Hardcoded credentials, missing input sanitization, SQL built with string concatenation, overly permissive CORS |
| **Completeness** | Are all acceptance criteria met? Are there missing files, migrations, or documentation updates? | Prompt asked for tests but none were written, migration script missing, API docs not updated |
| **Style** | Does the code follow existing conventions? Are imports organized? Is naming consistent? | Different naming convention than the rest of the codebase, unnecessary new dependencies, patterns that contradict existing architecture |

### How to Write Effective Feedback Comments

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

### Iteration Patterns

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

### The Review Feedback Loop in Practice

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

For the full collaboration model, see [Collaboration Model](../../../shared/general-themes/collaboration-model.md).

---

### Knowledge Check 7

**Knowledge Check 7.1**
Q: Devin opens a PR and you notice the approach is fundamentally wrong — it used inheritance where composition was needed. What should you do?
A) Leave a PR comment explaining the issue
B) Start a new session with a refined prompt that specifies the composition approach
C) Fix the code yourself and push to Devin's branch
D) Leave a comment asking Devin to start over

*Answer: B — When the fundamental approach is wrong, iterating through PR comments is inefficient. Start a new session with a prompt that includes what you learned: "Use composition, not inheritance, for the notification handlers. See the existing CompositeValidator pattern in src/validators/ for the convention."*

**Knowledge Check 7.2**
Q: Which PR comment will produce a better response from Devin?
A) "Add error handling"
B) "Add a try/except around the database query in `get_articles()` (line 42) that catches `ConnectionError` and returns a 503 with a Retry-After header set to 30 seconds"

*Answer: B — The specific comment tells Devin exactly what to do, where to do it, and what the output should look like. The vague comment forces Devin to guess what kind of error handling you want and where.*

**Knowledge Check 7.3**
Q: You are on the third round of PR review comments and Devin keeps not converging on what you want. What should you do?
A) Keep leaving more detailed comments
B) Start a new session with lessons learned from the iterations, or handle the remaining work yourself
C) Approve the PR as-is
D) Ask Devin to explain its reasoning

*Answer: B — After 3+ rounds without convergence, the prompt or approach may need fundamental adjustment. Starting fresh with accumulated context is typically more efficient than continuing to iterate.*

### Key Takeaways

- Review AI-generated PRs across four dimensions: correctness, security, completeness, style
- Write feedback as: [What's wrong] + [What to do] + [How to verify]
- Leave PR comments for iterative improvements; start a new session when the fundamental approach is wrong
- Devin Review provides automated first-pass review — use it to catch mechanical issues so you can focus on design and logic
- Typically 1-3 rounds of PR comments reach convergence; beyond that, consider a fresh session

---

<a id="tactical-tips--troubleshooting"></a>
## 8. Tactical Tips & Troubleshooting

This section covers common failure modes and how to resolve them, plus the organizational practices (Knowledge notes, Playbooks) that improve Devin's success rate over time.

### Common Failure Modes

#### Agent Loops on the Same Error

**Symptoms:** Devin pushes the same fix repeatedly, CI keeps failing with the same error, the session log shows repetitive attempts at the same approach.

**Root cause:** Devin is stuck in a local optimum — it has one hypothesis about the fix and keeps trying variations of it.

**What to do:**
1. **Intervene early.** If you see 3+ attempts at the same fix, do not wait for Devin to exhaust its retries.
2. **Leave a specific PR comment** with a different approach: "The issue is not in the validator — it is in the serializer. Check `src/serializers/order.py` line 87 where the amount is cast to int."
3. If Devin has been stuck for a while and the session has accumulated too much confusing context, **start a new session** with the diagnosis included in the prompt.

#### Build/Test Environment Issues

**Symptoms:** Build fails with "command not found", missing dependencies, wrong runtime version.

**Root cause:** The blueprint is missing a dependency that the project requires, or the project needs a specific runtime version that is not installed.

**What to do:**
1. Check the blueprint — is the required tool/runtime installed in the `initialize` block?
2. Check if the dependency is a project-level one that should be in the repo's setup (e.g., `npm install`, `pip install -r requirements.txt`) vs. a system-level one that belongs in the blueprint (e.g., `apt-get install postgresql-client`).
3. Update the blueprint and rebuild the snapshot, or add the install command to the session prompt as a temporary workaround: "First run `apt-get install -y libpq-dev`, then proceed with the task."

#### Agent Misunderstands the Task

**Symptoms:** Devin implements something different from what you intended. The PR is technically correct code but solves the wrong problem.

**Root cause:** The prompt was ambiguous or missing critical context.

**What to do:**
1. **Do not iterate on the wrong solution.** It is faster to start a new session than to redirect a misunderstood task through PR comments.
2. **Refine the prompt.** Add the missing context that caused the misunderstanding. Be specific about what "done" looks like.
3. **Include a negative constraint:** "Do NOT change the public API contract" or "Do NOT modify the database schema" to prevent Devin from going in the wrong direction.

#### CI Failures the Agent Cannot Resolve

**Symptoms:** Devin pushes fixes for CI failures but they are infrastructure-related (flaky tests, network timeouts, Docker pull rate limits) rather than code-related.

**Root cause:** The CI failure is not caused by Devin's code. It is a pre-existing infrastructure issue.

**What to do:**
1. **Check the base branch.** If the same CI check fails on `main`, the issue is pre-existing — not caused by Devin.
2. **Leave a PR comment** explaining the infrastructure issue: "The Docker pull rate limit is an infrastructure issue. Re-run CI to get past it."
3. If the flaky test is a recurring problem, consider creating a **Knowledge note** documenting it: "The `test_payment_webhook` test is flaky due to external API timeout. Re-run CI if it fails in isolation."

### Knowledge Notes That Improve Success Rate

Knowledge notes are persistent context items that Devin retrieves automatically when relevant. Investing in the right Knowledge notes compounds over time.

**High-impact Knowledge notes to create:**

| Knowledge Note | What to Include | Impact |
|---------------|----------------|--------|
| **Coding standards** | Naming conventions, import ordering, error handling patterns, preferred libraries | Devin follows your team's style from the first commit |
| **Architecture decisions** | "We use repository pattern for data access", "All services communicate via gRPC", "State management uses Redux Toolkit" | Devin makes architectural choices consistent with your decisions |
| **Repo conventions** | "Tests go in `__tests__/` adjacent to source", "Migrations use sequential numbering", "All API endpoints require authentication middleware" | Devin produces code that fits the existing structure |
| **Known issues** | "The `payment-webhook` test is flaky — re-run if it fails in isolation", "Docker builds require `--platform linux/amd64` on M1 Macs" | Devin does not waste time debugging known issues |
| **Dependency preferences** | "Use `axios` for HTTP, not `fetch`", "Use `date-fns` not `moment`", "Prefer `pnpm` over `npm`" | Devin uses your preferred libraries without being told each time |

### Playbook Design Principles

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

### Knowledge Check 8

**Knowledge Check 8.1**
Q: Devin has attempted the same CI fix 4 times with no success. The error is "Connection refused to localhost:5432." What should you do?
A) Wait for Devin to figure it out — it needs more attempts
B) Leave a PR comment: "The PostgreSQL service is not running. Add `docker-compose up -d postgres` before running tests"
C) Cancel the session and fix it yourself
D) Update the Playbook to skip the test

*Answer: B — The issue is a missing runtime dependency (PostgreSQL), not a code bug. Give Devin specific guidance on how to resolve the environment issue. Alternatively, update the blueprint to start PostgreSQL automatically.*

**Knowledge Check 8.2**
Q: You notice Devin consistently uses `moment.js` for date handling, but your team standardized on `date-fns`. What is the most efficient long-term fix?
A) Leave a PR comment every time asking Devin to switch
B) Create a Knowledge note: "Use date-fns for all date manipulation. Do not use moment.js."
C) Add `moment.js` to a blocklist in the linter
D) Both B and C

*Answer: D — The Knowledge note provides immediate guidance to Devin; the linter rule provides automated enforcement. Both together give defense in depth. Option A works but does not compound — you would need to comment on every future PR.*

**Knowledge Check 8.3**
Q: When should you create a Playbook for a task?
A) Before the first time you run the task
B) After you have run the same pattern 3+ times and refined the approach
C) Only when multiple teams need the same process
D) When the task is too complex for a single prompt

*Answer: B — Playbooks encode a proven methodology. Creating one before you have refined the approach risks codifying a suboptimal process. Run it manually 3+ times, observe what works, then extract the pattern into a Playbook.*

**Knowledge Check 8.4**
Q: Devin implements something completely different from what you intended. The code is technically correct but solves the wrong problem. What went wrong and what should you do?
A) The agent is buggy — report a bug
B) The prompt was ambiguous — start a new session with a refined, more specific prompt
C) Leave PR comments redirecting Devin to the right solution
D) Approve the PR and open a follow-up session for the correct implementation

*Answer: B — When the fundamental task is misunderstood, iterating through PR comments is inefficient. Start a new session with the missing context that caused the misunderstanding. Include negative constraints if needed: "Do NOT change the API contract."*

### Key Takeaways

- Intervene early when Devin loops — 3+ attempts at the same fix means a different approach is needed
- Distinguish between code issues (Devin can fix) and environment/infrastructure issues (fix the blueprint or guide Devin past it)
- When Devin misunderstands the task, start a new session with a refined prompt rather than redirecting through comments
- Knowledge notes compound over time: coding standards, architecture decisions, known issues, and dependency preferences
- Create Playbooks after refining a task pattern 3+ times; structure them with prerequisites, ordered steps, verification, and error handling

---

<a id="challenge-mode-labs"></a>
## Challenge-Mode Labs

These labs require you to write your own prompts from scratch. No copy-paste is provided. For each scenario, read the description, apply the prompt engineering principles from Section 3, and write a complete prompt.

### Lab 1: Extract a Microservice from a Monolith

**Scenario:** Your team has a Java Spring Boot monolith with three bounded contexts tightly coupled in a single application: Users, Products, and Orders. The Orders module has been identified for extraction into a standalone microservice. The Orders module accesses the `orders`, `order_items`, and `order_status_history` tables. It currently calls User and Product methods directly via in-process method calls.

**Success criteria:**
- A standalone Spring Boot service for Orders with its own `Dockerfile` and database configuration
- HTTP-based communication replacing the direct method calls to User and Product services
- Docker Compose file that runs the monolith and the extracted service together
- Integration tests that verify inter-service communication
- All existing tests in the monolith still pass

**Grading rubric — a good prompt includes:**
- [ ] Repository name
- [ ] Specific bounded context to extract (Orders) and the tables it owns
- [ ] Communication pattern to use (HTTP) and how shared data should be handled
- [ ] Explicit requirement for Dockerfile and Docker Compose
- [ ] Testing requirements (integration tests + existing tests pass)
- [ ] Constraints (what NOT to change — e.g., the User and Product modules)

<details>
<summary><strong>Reference answer</strong> (expand after writing your own)</summary>

```
Extract the Orders bounded context from
my-monolith-app into a standalone Spring Boot
microservice.

The Orders module owns these tables: orders,
order_items, order_status_history. It currently calls
UserService and ProductService via direct method
invocation.

Requirements:
- Create a new Spring Boot application for the Orders
  service with its own Dockerfile (multi-stage build)
- Replace direct method calls to UserService and
  ProductService with HTTP REST calls
- Create a docker-compose.yml that runs both the
  monolith and the Orders service with separate
  PostgreSQL databases
- Add integration tests that verify:
  - Creating an order calls the User service to
    validate the customer
  - Order status changes are persisted correctly
  - The monolith's remaining functionality is
    unaffected

Constraints:
- Do NOT modify the User or Product modules in the
  monolith
- The Orders service must be independently deployable
- Use the existing Spring Boot version (do not
  upgrade)

Verify by running: docker-compose up and executing
the integration test suite
```

</details>

---

### Lab 2: Remediate SAST Findings Across a Repository

**Scenario:** Your security team has run a SonarQube scan against your organization's main API gateway repository. The scan identified 47 findings: 3 CRITICAL (SQL injection), 12 HIGH (XSS, path traversal), and 32 MEDIUM (hardcoded secrets, weak crypto). You need to remediate the CRITICAL and HIGH findings and re-scan to verify.

**Success criteria:**
- All 3 CRITICAL findings remediated
- All 12 HIGH findings remediated
- Re-scan shows 0 CRITICAL and 0 HIGH findings remaining
- All existing tests pass
- A `SECURITY_REMEDIATION.md` documenting each finding and its fix

**Grading rubric — a good prompt includes:**
- [ ] Repository name
- [ ] Specific severity threshold to remediate (CRITICAL + HIGH)
- [ ] The scan tool and how to run it
- [ ] Verification mechanism (re-scan)
- [ ] Documentation requirement
- [ ] Constraint: do not change MEDIUM findings (to scope the work)

<details>
<summary><strong>Reference answer</strong> (expand after writing your own)</summary>

```
Remediate all CRITICAL and HIGH severity findings
from the SonarQube scan in api-gateway-service.

To get the current findings, run:
./gradlew sonarqube -Dsonar.host.url=${SONAR_URL}

Focus on:
- CRITICAL (3 findings): SQL injection vulnerabilities
  — use parameterized queries, not string concatenation
- HIGH (12 findings): XSS and path traversal — apply
  input sanitization and validate file paths against
  an allowlist

Do NOT remediate MEDIUM findings in this session.

After remediating:
1. Run ./gradlew test to verify no regressions
2. Re-run the SonarQube scan to confirm 0 CRITICAL
   and 0 HIGH findings remain
3. Create SECURITY_REMEDIATION.md documenting:
   - Each finding (ID, severity, description)
   - The fix applied
   - Before/after scan results
```

</details>

---

### Lab 3: Set Up a Scheduled Dependency Upgrade

**Scenario:** Your team manages 8 Node.js microservices. Dependencies are currently 6-18 months behind because no one has time for manual bumps. You want to set up a recurring Devin session that automatically bumps dependencies weekly, verifies the build, and opens PRs for human review.

**Success criteria:**
- A working prompt that can be used as a recurring scheduled session
- The prompt handles: checking for updates, applying non-breaking upgrades, running tests, reverting breaking upgrades, documenting changes
- The prompt can be reused across different Node.js repos without modification (or with minimal variable substitution)

**Grading rubric — a good prompt includes:**
- [ ] Target repository (or parameterized for reuse)
- [ ] Scope of upgrades (minor/patch only — no major version jumps)
- [ ] Verification mechanism (run tests, run build)
- [ ] Rollback strategy (revert individual breaking upgrades)
- [ ] Documentation requirement (what was updated, what was skipped)
- [ ] PR title convention for consistency

<details>
<summary><strong>Reference answer</strong> (expand after writing your own)</summary>

```
Check all npm dependencies in [REPO_NAME] for
available minor and patch version updates.

Steps:
1. Run npm outdated to identify available updates
2. For each outdated dependency, update to the latest
   minor version (do NOT jump major versions)
3. After each batch of updates, run npm test and
   npm run build
4. If any upgrade breaks the build or tests, revert
   that specific upgrade and note it in the report

Create a DEPENDENCY_UPDATES.md documenting:
- Each dependency upgraded (from version → to version)
- Any upgrades skipped (with the reason: test failure,
  build failure, etc.)

Title the PR: "chore: weekly dependency bump — [date]"
```

</details>

---

### Lab 4: Translate a Service from Java to Python

**Scenario:** Your organization is migrating away from a legacy Java codebase to Python. The first service to migrate is the Notification service, which has 4 REST endpoints, uses JPA for database access, and sends emails via SMTP. You need the Python version to be a drop-in replacement with identical API contracts.

**Success criteria:**
- Python (FastAPI) application with the same 4 REST endpoints
- Same JSON request/response shapes as the Java version
- Parity tests proving identical behavior for the same inputs
- `MIGRATION_NOTES.md` documenting translation decisions
- Both versions build and run

**Grading rubric — a good prompt includes:**
- [ ] Source repository and target framework
- [ ] Specific endpoints to translate
- [ ] Requirement to preserve API contract (same JSON shapes)
- [ ] Parity/equivalence testing approach
- [ ] Documentation of translation decisions
- [ ] Technology choices for the Python version (ORM, validation library)

<details>
<summary><strong>Reference answer</strong> (expand after writing your own)</summary>

```
Translate the Notification service from
notification-service-java (Java/Spring Boot) to
Python/FastAPI.

Endpoints to translate:
- POST /api/notifications/send
- GET /api/notifications/{id}
- GET /api/notifications?userId={userId}
- DELETE /api/notifications/{id}

Requirements:
- Use FastAPI for the web framework
- Use SQLAlchemy for database access (replacing JPA)
- Use Pydantic for request/response models
- Preserve the exact JSON request/response shapes so
  the Python version is a drop-in replacement
- Use smtplib for email sending (replacing
  JavaMailSender)

Testing:
- Write pytest tests that verify each endpoint returns
  identical responses to the Java version for the same
  inputs
- Include edge cases: empty results, invalid IDs,
  malformed requests

Create MIGRATION_NOTES.md documenting:
- Java pattern → Python equivalent mapping
- Any behavioral differences and why
- Dependencies used
```

</details>

---

### Lab 5: Design an Event-Driven SAST Pipeline

**Scenario:** Your security team wants Devin to automatically remediate security findings whenever a SAST scan completes. The scan runs in CI after every merge to `main`. When findings above a threshold are detected, Devin should automatically create a session to remediate them.

**Success criteria:**
- A working description of the automation configuration (trigger, filter, prompt template, safeguards)
- The prompt template uses variables for the scan results
- Safeguards prevent infinite loops and duplicate sessions

**Grading rubric — a good prompt includes:**
- [ ] Trigger definition (CI completion event)
- [ ] Filter criteria (only when findings above threshold exist)
- [ ] Prompt template with variables for scan results
- [ ] Explicit safeguards (loop prevention, retry limits, idempotency)
- [ ] Verification mechanism (re-scan after remediation)

<details>
<summary><strong>Reference answer</strong> (expand after writing your own)</summary>

This is an automation configuration, not a single prompt. A good answer describes:

**Trigger:** GitHub Actions workflow completion webhook for the `sast-scan` workflow on the `main` branch.

**Filter:** Only trigger when the scan reports CRITICAL or HIGH findings (check the scan artifact for finding count > 0). Do NOT trigger on PRs authored by `devin-ai-integration[bot]`.

**Prompt template:**

```
The latest SAST scan on {{repo_name}} (branch: main,
commit: {{commit_sha}}) found {{finding_count}}
CRITICAL/HIGH findings.

Scan results are at: {{scan_artifact_url}}

Remediate all CRITICAL and HIGH findings. For each:
1. Read the finding details (CWE, affected file, line)
2. Apply the recommended fix
3. Run the test suite to verify no regressions

After all remediations:
- Re-run the SAST scan to verify 0 CRITICAL/HIGH
  remain
- Document fixes in SECURITY_REMEDIATION.md
```

**Safeguards:**
- Filter: exclude events from `devin-ai-integration[bot]`
- Max 3 sessions per commit SHA (idempotency)
- Max 1 concurrent remediation session per repo

</details>

---

<a id="debug-exercises"></a>
## Debug Exercises

For each scenario, identify: (1) what happened, (2) what went wrong, and (3) what the practitioner should have done differently.

### Debug Exercise 1: The Infinite Loop

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

### Debug Exercise 2: The Wrong Fix

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

### Debug Exercise 3: The Missing Environment

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

---

<a id="gaps--future-content"></a>
## Gaps & Future Content

The following materials would strengthen the Delivery track but are not yet available:

- [ ] Video: recorded session walkthrough showing the PR feedback loop from prompt to merge
- [ ] Video: live example of parent-child orchestration across multiple repos
- [ ] Interactive prompt builder tool — input a task description, get a structured prompt with all components filled in
- [ ] Environment configuration lab — step-by-step setup of blueprints, secrets, and MCP servers from zero
- [ ] Prompt library — curated collection of production-quality prompts organized by task type (security, migration, testing, feature dev)
- [ ] Advanced Playbook design — multi-phase Playbooks with conditional logic, error recovery, and human approval gates
- [ ] Platform configuration exercises — hands-on practice configuring blueprints, Knowledge notes, and automations
- [ ] Recorded walkthroughs of each debug exercise showing the actual session logs
- [ ] Performance benchmarking guide — how to measure and optimize prompt-to-merge cycle time
- [ ] Team onboarding checklist — step-by-step guide for rolling out Devin to a new delivery team

---

## After This Workshop

Once you have built the tactical skills covered here, you are ready for domain-specific deep dives:

| Next Step | What You'll Do |
|-----------|---------------|
| [General Workshop](../../general/) | Hands-on labs across security, modernization, and feature dev — apply what you learned here |
| [Specialties — Technical Domains](../../specialties/technical-domains/) | Deep dives into COBOL modernization, .NET cloud-native, framework upgrades, data migration |
| [Specialties — Job Families](../../specialties/job-families/) | Role-specific lab sequences for AppDev, Digital Engineering, QA |

These workshops assume you have the platform fundamentals, prompt engineering skills, and troubleshooting techniques covered in this track.
