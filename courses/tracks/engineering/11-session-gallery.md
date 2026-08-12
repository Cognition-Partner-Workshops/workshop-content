# The Session Gallery: Patterns and Antipatterns

<a id="toc"></a>
## Table of Contents

- [How to Use the Gallery](#how-to-use-the-gallery)
- [The Six Patterns](#the-six-patterns)
- [The Five Antipatterns](#the-five-antipatterns)
- [The Sheet: Sessions Worth Reading](#the-sheet)
- [Exercise: Diagnose Before You Read the Diff](#exercise)
- [Flows Worth Building Next](#flows-worth-building-next)
- [Key Takeaways](#key-takeaways)

---

Every other section of this track tells you what a good session looks like. This
one shows you fifteen real ones — ten that worked and five that were set up to
fail — so you can read the difference rather than take it on faith. Each row of
[the sheet](#the-sheet) links to a session you can open, scroll, and argue with.

The good sessions are not curated highlights. They are the same prompts that
appear in the showcase threads under `demos/`, run unchanged, on the same
repositories, with whatever they produced. The failing five were written
deliberately: each one removes exactly one thing a session needs, so the failure
mode is isolated instead of tangled with four others.

<a id="how-to-use-the-gallery"></a>
## How to Use the Gallery

Read a session the way you would read a code review, in this order:

1. **The prompt.** Cover the rest of the session and predict what will happen.
2. **The first ten minutes.** What did the session go looking for, and did it find
   the repository, the build, and the check it needed?
3. **The verification step.** Find the moment where something *ran*. If you
   cannot find one, you have found the problem.
4. **The output.** A PR, a report, an explicit blocker, or a question for a human
   are all legitimate endings. Silence and self-congratulation are not.

The sessions live in a training organization, so they are reviewable long after
the run. Nothing in this gallery is merged into a repository's `main`.

<a id="the-six-patterns"></a>
## The Six Patterns

| Pattern | What it looks like in the prompt | Why it works |
|---|---|---|
| **Orient before mutating** | A read-only first session that produces an inventory or map, written to a file | The map becomes shared context — a Knowledge note or a committed artifact — so every later session and every child starts from the same understanding instead of re-deriving it |
| **Executable done-criteria** | The command that must be green, pasted into the prompt (`make procs-parity NS=demo`) | "Done" stops being a judgment call. The session knows when to stop and the reviewer knows what was proven |
| **Verification loop over judgment** | A parity, contract, or adversarial check that failed before the change and passes after | "The code looks correct" and "the code behaves identically" are different claims, and only one of them can be checked by a machine |
| **Human-in-the-loop gate** | "Leave every decision pending. Do not answer your own questions." | Ambiguity is surfaced as questions, at the cheapest moment, instead of being resolved silently by whoever is least qualified to decide |
| **Fan-out with per-child proof** | One child per independent unit, each with its own namespace, branch, and green check | The same review bar applied N times in parallel rather than once in series — and isolation keeps the blast radius per child |
| **Trigger the loop, don't watch it** | A workflow, schedule, or automation that starts sessions on an event, with attempt limits and escalation | The work happens when the event happens. Attempt limits and bot-author filters are what keep it from becoming a loop that feeds itself |

<a id="the-five-antipatterns"></a>
## The Five Antipatterns

| Antipattern | The prompt's missing piece | What it produces |
|---|---|---|
| **The unbounded improvement** | No scope, no acceptance criteria | A session that has to invent the task, and a diff nobody asked for and nobody can review as a unit |
| **The big-bang rewrite** | No slice, no seam, no parity contract | Weeks of work compressed into one prompt, with nothing to verify against and no reviewable increment |
| **The undiagnosed incident** | No reproduction, no environment, no target metric | A plausible fix to a symptom that was never located — the wrong layer, confidently patched |
| **The missing context** | Names files and modules that are in no attached repository | Time spent hunting for code that was never provisioned, and a guess at what the requester meant |
| **The gate bypass** | Explicitly instructs skipping tests, review, and branches | Either an unverified change nobody can trust, or a session that stops to push back — which is the correct outcome and worth watching |

Note the shape of the five: none of them is a hard task. Every one is a *cheap*
task made unreviewable by leaving out a component from
[Prompt Engineering for Agents](02-prompt-engineering.md). That is the point —
these failures are not caused by difficulty.

<a id="the-sheet"></a>
## The Sheet: Sessions Worth Reading

**Verified-good sessions** — ten use cases, chosen to cover the range rather than
the highlights.

| # | Use case | Session | Pattern to watch for | Source thread |
|---|---|---|---|---|
| G01 | Discovery / codebase understanding | [Estate map](https://partner-workshops.devinenterprise.com/sessions/6f12955cde2544d3b04b6c9209367c37) | Orient before mutating; output is an inventory, not a diff | `demos/migration/stored-procs-to-microservices-demo.md` |
| G02 | Data / ETL migration | [SAS to dbt on Databricks](https://partner-workshops.devinenterprise.com/sessions/f6a70af00966497b9aba02516c48209f) | Playbook macro plus source-parity controls | `demos/data-engineering/sas-to-databricks-demo.md` |
| G03 | Rules archaeology with a human gate | [Rule ledger, decisions pending](https://partner-workshops.devinenterprise.com/sessions/33897c92ecdb417e860f71128200c874) | Questions raised, nothing decided; the session waits on a human by design | `demos/migration/stored-procs-to-microservices-demo.md` |
| G04 | Stored procedures to a service | [Extract the rating module](https://partner-workshops.devinenterprise.com/sessions/ea448bb8449040f7a1ad23dd590b3b07) | Two executable gates named in the prompt | `demos/migration/stored-procs-to-microservices-demo.md` |
| G05 | Legacy framework to microservices | [Extract the settlement module](https://partner-workshops.devinenterprise.com/sessions/6e4e480dbf36418785f0b742460ffc94) | Golden transcripts pin behavior across the rewrite | `demos/migration/struts-to-microservices-demo.md` |
| G06 | Application security, runtime | [Reproduce, fix, prove a DAST finding](https://partner-workshops.devinenterprise.com/sessions/3ef739b8ab6846759da954e8e7c4589b) | The same attack failing before and passing after; the shared reference environment is read-only | `demos/security/use-cases/dast-remediation-demo.md` |
| G07 | Orchestration at scale | [Fan out the remaining modules](https://partner-workshops.devinenterprise.com/sessions/974caf2df91343d4842fce824083ad17) | One child per module, each with its own namespace, branch, and gate | `demos/migration/stored-procs-to-microservices-demo.md` |
| G08 | DevOps / CI-CD automation | [Event-driven remediation workflow](https://partner-workshops.devinenterprise.com/sessions/043c3833ab934061bba354f5f616e323) | Attempt limits, bot-author filters, and escalation designed in from the start | `demos/security/use-cases/event-driven-sast-remediation-demo.md` |
| G09 | Feature development, brownfield | [Account statement feature, full SDLC](https://partner-workshops.devinenterprise.com/sessions/8b62cf9e2cc04c569b93478ad6f18b90) | Spec and design as artifacts; the test task is the gate | `demos/application-development/banking-feature-sdlc-demo.md` |
| G10 | Cloud / infrastructure modernization | [Search replatform behind its contract](https://partner-workshops.devinenterprise.com/sessions/a301e5255a634eaea9c4689b954e0288) | An adapter seam and a contract suite; `main` behavior unchanged | `demos/migration/aws-cloud-native-modernization-demo.md` |

**Supplementary build** — not a use-case walkthrough, but the session that makes
the incident flow verifiable. Worth reading as an example of building the check
*before* the walkthrough that depends on it.

| # | Use case | Session | Pattern to watch for |
|---|---|---|---|
| S01 | Build the missing verification loop | [Incident reproduction harness](https://partner-workshops.devinenterprise.com/sessions/fe32b3ecb13c4d458aad6f385df108b7) | A check with three verdicts — pass, fail, and inconclusive — and a legitimate-traffic assertion so a fix cannot pass by refusing everybody |

**Antipattern sessions** — read the prompt first and predict the failure before
you scroll.

| # | Antipattern | Session | What to look for |
|---|---|---|---|
| B01 | Unbounded improvement | [Make the codebase better](https://partner-workshops.devinenterprise.com/sessions/1a87fef14a6b4757a41a21be05695f58) | How much of the session is spent choosing a task, and whether the result is reviewable as one change |
| B02 | Big-bang rewrite | [Rewrite every service in Rust](https://partner-workshops.devinenterprise.com/sessions/763b0c1a86ec4267a86cabb1fc4403b7) | Where the session tries to slice the work itself, and what it has to invent to do so |
| B03 | Undiagnosed incident | [Production is broken, fix it](https://partner-workshops.devinenterprise.com/sessions/9473ca3b104543fba16c8976e1f19cb0) | Which layer it decides to suspect, and on what evidence |
| B04 | Missing context | [Change the pricing engine](https://partner-workshops.devinenterprise.com/sessions/ed52a1b408274d009a4d3da095c5b64e) | Time spent looking for code that was never attached, and what it does when it cannot find it |
| B05 | Gate bypass | [Skip the tests, push to main](https://partner-workshops.devinenterprise.com/sessions/6a0eef4fb15a4a57a31fd5f8e3d8b540) | Whether the session complies, negotiates, or refuses — and which of those you would want from a teammate |

<a id="exercise"></a>
## Exercise: Diagnose Before You Read the Diff

Work the five antipattern sessions in order. For each one, before scrolling past
the prompt, write down:

1. Which of the six prompt components — repository, file paths, expected
   behavior, acceptance criteria, verification mechanism, constraints — is
   missing.
2. What the session will most likely do with the ambiguity.
3. The rewritten prompt you would have sent, in five lines or fewer.

Then scroll. Where your prediction was wrong, the interesting question is not
whether the session behaved well, but which piece of context you assumed it had.

For the good sessions, run the exercise in reverse: delete one component from the
prompt and predict which failure mode you have just created. This is the fastest
way to learn which components carry the most weight for which kind of work —
verification mechanisms matter most on migrations, acceptance criteria matter
most on features, and constraints matter most on anything touching a shared
environment.

<a id="flows-worth-building-next"></a>
## Flows Worth Building Next

The gallery covers ten use cases. These are the gaps, each stated as the
verification loop it would need — because a flow without one is a presentation,
not a walkthrough.

| Candidate flow | The loop that would make it real | Groundwork |
|---|---|---|
| **Live incident to root cause** | Reproduce the symptom through the gateway, fix the owning service, and re-run the same probe — with a third verdict, "inconclusive", so a service that is simply down cannot read as a pass | The harness is being built in S01; the `!live-incident-rca` playbook is registered |
| **Test-coverage gap closure** | Coverage delta plus a mutation-style check, so tests that assert nothing cannot count as coverage | Existing lab material on edge-case and coverage work |
| **API contract drift** | The published specification replayed against the running services, failing on drift rather than on style | The contract suites already in the polyglot monorepo |
| **Dependency and EOL sweep** | The scanner re-run after the upgrade, plus each service's own tests, with an escalation path when a fix needs an architectural change | The recurring sweep playbook already registered |
| **Cost or performance regression** | A before-and-after number from the same load profile, captured in the PR — an adjective is not a result | Load-test scaffolding and the observability stack |

Two rules learned from building the existing ten: **write the check before the
thread** — a walkthrough whose verification step is "read the output and see if
it looks right" always drifts into a presentation. And **run the flow before you
document it** — a live run typically surfaces something the author's reading of
the code missed, and those findings are the most credible content in the thread.

<a id="key-takeaways"></a>
## Key Takeaways

- A session is worth reading when you can point at the moment something ran; the
  ten good sessions are all organized around that moment.
- The five failures are not hard tasks badly done. Each one is an easy task with
  a single prompt component removed, which is why they are useful teaching
  material and why they are cheap to reproduce.
- A session that stops to ask a question or refuses an unsafe instruction has
  behaved correctly. Judge the ending by whether it is honest, not by whether it
  is a PR.
- Reproducing this gallery is a scripted operation, not a morning of clicking:
  the prompts come from the showcase threads verbatim, and the sessions are
  created through the API against a named organization so the whole set is
  reviewable afterwards.
