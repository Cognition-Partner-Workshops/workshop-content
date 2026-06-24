# Cloud Agent vs. Local Agent

<a id="toc"></a>
## Table of Contents

- [The Local Agent Mental Model](#the-local-agent-mental-model)
- [The Cloud Agent Mental Model](#the-cloud-agent-mental-model)
- [Why the Distinction Matters](#why-the-distinction-matters)
- [When Cloud Is the Better Choice](#when-cloud-is-the-better-choice)
- [The Release-Control Problem](#the-release-control-problem)
- [Exploration Activity](#exploration-activity)
- [Key Takeaways](#key-takeaways)

---

<a id="the-local-agent-mental-model"></a>
## The Local Agent Mental Model

Most engineers' experience with AI assistance looks like this:

```
You ← constantly steering → AI Assistant
 │                              │
 ├── "complete this line"       │
 ├── "refactor this function"   │
 ├── "explain this error"       │
 └── "write a test for this"    │
                                │
     Your machine, your context, your control
```

The local agent is an **extension of your hands**. It operates in your editor, on your filesystem, within your active attention. You provide continuous guidance — accepting or rejecting suggestions, steering direction, and maintaining oversight of every keystroke.

This model works well when:
- You already know what to build and just need typing acceleration
- The task requires your real-time judgment at every step
- You're exploring a problem and thinking through the design as you go
- The scope is a single file or function

<a id="the-cloud-agent-mental-model"></a>
## The Cloud Agent Mental Model

A cloud agent operates differently:

```
You → delegate task → Cloud Agent (own VM)
 │                        │
 │  (context-switch       ├── clones repo
 │   to other work)       ├── reads codebase
 │                        ├── implements solution
 │                        ├── runs tests
 │                        ├── iterates on failures
 │                        └── opens PR
 │                              │
 └──── review PR ←─────────────┘
```

The cloud agent is a **team member receiving an assignment**. It works on its own isolated machine, retrieves its own context, makes its own decisions about implementation, verifies its own work, and delivers a finished artifact (typically a pull request) for your review.

You interact with it the same way you'd interact with a junior engineer: assign a task, check the output, leave feedback if needed.

<a id="why-the-distinction-matters"></a>
## Why the Distinction Matters

The failure mode for most teams adopting cloud agents is **treating them like local agents**. This manifests as:

| Local Agent Habit | Cloud Agent Equivalent | Problem |
|-------------------|----------------------|---------|
| Watch it type in real time | Watch the session timeline | Wastes your time — you could be doing other work |
| Intervene constantly | Send messages mid-session | Interrupts the agent's flow; often unnecessary |
| Keep the scope to one function | Give it a one-line task | Underutilizes the agent's ability to handle complex, multi-file work |
| Stay in your editor | Avoid delegating | Never builds the muscle of releasing control |

The mindset shift: **you are not the driver anymore. You are the reviewer.**

Your job becomes:
1. Define the task clearly (acceptance criteria, target repo, expected behavior)
2. Let go — context-switch to something else
3. Review the output when it arrives
4. Provide feedback via PR comments if needed

<a id="when-cloud-is-the-better-choice"></a>
## When Cloud Is the Better Choice

A cloud agent is the better tool when any of these are true:

| Signal | Why Cloud Wins |
|--------|---------------|
| **The task is well-defined and can be verified** | The agent can run tests, lint, and CI to confirm its work without your involvement |
| **You'd rather be doing something else** | Delegation frees your attention for higher-value work |
| **The task spans multiple files or systems** | The agent can navigate an entire codebase, not just your open buffer |
| **The work is repetitive across targets** | The agent can handle N instances while you review the output |
| **The task requires a clean environment** | The agent's VM eliminates "works on my machine" issues |
| **You want an audit trail** | Every action is logged in the session timeline; the PR is the deliverable |
| **The task has a long execution time** | Builds, migrations, and large test suites run on the agent's machine, not yours |

A local agent remains the better choice when:
- You're actively designing and need real-time feedback on your thinking
- The task requires your subjective judgment at every step (naming, UX, architecture)
- You're pair-programming and the AI is your copilot, not an independent worker
- The scope is tiny (a few lines, a quick refactor in one file)

<a id="the-release-control-problem"></a>
## The Release-Control Problem

The hardest part of adopting a cloud agent is psychological: **releasing control**.

Engineers are trained to maintain tight feedback loops. We run tests after every change, we re-read our own code, we verify locally before pushing. Handing a task to something that works in a separate environment — out of sight — feels uncomfortable.

Three things that make this easier:

1. **The PR is your safety net.** Nothing reaches production without your explicit approval. The agent proposes; you decide. The merge button is always yours.

2. **The session timeline is your visibility.** You can always open the session and see exactly what the agent did — every shell command, every file edit, every decision point. Transparency replaces the need for real-time oversight.

3. **Start small, build trust.** Begin with low-risk tasks: dependency bumps, test additions, documentation updates. As you see consistent quality in the output, gradually delegate more complex work.

The teams that get the most value from cloud agents are those that have internalized this: **reviewing a PR takes minutes; implementing it takes hours.** If the agent can produce a reviewable PR, you've converted hours of implementation into minutes of review.

---

<a id="exploration-activity"></a>
## Exploration Activity

**Try this:** Open the Devin web app and look at a recently completed session (your own or a shared one). Notice:
- How the session started (what prompt was given)
- The session timeline — what the agent did independently
- The final output (PR or artifact)
- How long the session ran vs. how long the review took

Ask yourself: "If I had done this task manually, how long would it have taken? And what else could I have been doing in that time?"

---

<a id="key-takeaways"></a>
## Key Takeaways

- A local agent extends your hands; a cloud agent extends your team
- The fundamental shift is from **driving** to **delegating and reviewing**
- Cloud agents are better for well-defined, verifiable, multi-step work that doesn't require your real-time judgment
- The PR feedback loop is the safety mechanism — nothing ships without human approval
- The psychological barrier (releasing control) is the hardest part; start with low-risk tasks to build trust
- Time spent watching an agent work in real time is time wasted — delegate, context-switch, review later
