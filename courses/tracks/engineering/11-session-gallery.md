# The Session Gallery: Patterns and Antipatterns

<a id="toc"></a>
## Table of Contents

- [How to Use the Gallery](#how-to-use-the-gallery)
- [The Six Patterns](#the-six-patterns)
- [The Sheet: Sessions Worth Reading](#the-sheet)
- [Near Misses: Prompts That Look Fine](#near-misses)
- [Warm-Up: The Obvious Failures](#warm-up)
- [Exercise: Diagnose Before You Read the Diff](#exercise)
- [Flows Worth Building Next](#flows-worth-building-next)
- [Key Takeaways](#key-takeaways)

---

Every other section of this track tells you what a good session looks like. This
one shows you real ones — sixteen that were set up to succeed and ten that were
not — so you can read the difference rather than take it on faith. Each row of
[the sheet](#the-sheet) links to a session you can open, scroll, and argue with.

The sixteen are organized around the work that dominates enterprise engagements:
large-scale legacy modernization, systematic tech debt, and repetitive brownfield
work run in parallel through the API. Most of them run against the same polyglot
monorepo, which is the point — one estate, thirteen services in nine languages, a
legacy VM-hosted monolith, two frontends, stored procedures, and a live
deployment tracking `main`. That is close enough to a real estate that the
sessions have to deal with real seams.

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
| **Capture the before-state first** | "Record the request and response for every route; that transcript is the parity contract" | On modernization work, the thing being preserved is behavior, and behavior that was never recorded cannot be shown to have survived |
| **Human-in-the-loop gate** | "Leave every decision pending. Do not answer your own questions." | Ambiguity is surfaced as questions, at the cheapest moment, instead of being resolved silently by whoever is least qualified to decide |
| **Fan-out with per-child proof** | One child per independent unit, each with its own namespace, branch, and green check | The same review bar applied N times in parallel rather than once in series — and isolation keeps the blast radius per child |
| **Trigger the loop, don't watch it** | A workflow, schedule, or automation that starts sessions on an event, with attempt limits and escalation | The work happens when the event happens. Attempt limits and bot-author filters are what keep it from becoming a loop that feeds itself |

<a id="the-sheet"></a>
## The Sheet: Sessions Worth Reading

Grouped by the kind of work rather than by repository. Every row is a single
session; the fan-out rows have children underneath them.

### Legacy modernization

| # | Theme | Session | Pattern to watch for |
|---|---|---|---|
| G01 | Discovery before change | [Estate map of the stored-procedure billing estate](https://partner-workshops.devinenterprise.com/sessions/6f12955cde2544d3b04b6c9209367c37) | Read-only orientation; the output is an inventory, not a diff |
| G03 | Rules archaeology | [Rule ledger with every decision pending](https://partner-workshops.devinenterprise.com/sessions/33897c92ecdb417e860f71128200c874) | Questions raised and nothing decided — the session waits on a human by design |
| G04 | Stored procedures to a service | [Extract the rating module](https://partner-workshops.devinenterprise.com/sessions/ea448bb8449040f7a1ad23dd590b3b07) | Two executable gates named in the prompt; approved rule ledger as the input |
| G05 | Legacy framework to microservices | [Extract the settlement and payments module](https://partner-workshops.devinenterprise.com/sessions/6e4e480dbf36418785f0b742460ffc94) | Golden transcripts pin behavior across the rewrite |
| G16 | Monolith to microservices | [Split one bounded context out of the monolith](https://partner-workshops.devinenterprise.com/sessions/0b6eba68d8904c4589dc3684f5cb8585) | Decomposing along a seam that already exists; both halves must build and test independently |
| G10 | Cloud replatform | [Search onto a managed search service, behind its contract](https://partner-workshops.devinenterprise.com/sessions/a301e5255a634eaea9c4689b954e0288) | An adapter seam plus a contract suite, with the default path unchanged |
| G14 | VM to container platform | [Move the VM-hosted portal onto the platform path](https://partner-workshops.devinenterprise.com/sessions/268d0546f24f439fbd9fd095b1e0f77a) | The before-state transcript recorded *before* anything moves; the old path stays runnable |
| G15 | Frontend framework migration | [Two dashboard features from Angular to React](https://partner-workshops.devinenterprise.com/sessions/54a3871196cf4cc8908ad48bb34af774) | Migration sliced by feature, with the API contract and per-state tests as the gate |

### Data and ETL

| # | Theme | Session | Pattern to watch for |
|---|---|---|---|
| G02 | Legacy ETL to a modern framework | [Regulatory SAS program to dbt models](https://partner-workshops.devinenterprise.com/sessions/f6a70af00966497b9aba02516c48209f) | Playbook macro plus source-parity reconciliation, not a code translation |

### Systematic tech debt

| # | Theme | Session | Pattern to watch for |
|---|---|---|---|
| G11 | Test automation | [Close the behavior gaps, not the coverage number](https://partner-workshops.devinenterprise.com/sessions/acaddce227a846c59e5f2ca57ccf3206) | Gaps enumerated before tests are written, then each new test proven load-bearing by breaking the code it covers |
| G12 | Framework and language upgrade | [Spring Boot 2.7 / Java 11 to current LTS](https://partner-workshops.devinenterprise.com/sessions/31b79d2a30744aa9a29fe44e475ce857) | Baseline test count captured first; every breaking change named with a reason |

### Security and compliance

| # | Theme | Session | Pattern to watch for |
|---|---|---|---|
| G06 | Runtime security | [Reproduce, fix, and prove a DAST finding](https://partner-workshops.devinenterprise.com/sessions/3ef739b8ab6846759da954e8e7c4589b) | The same attack failing before and passing after; the shared reference deployment stays read-only |
| G08 | Security automation | [Build the event-driven remediation workflow](https://partner-workshops.devinenterprise.com/sessions/043c3833ab934061bba354f5f616e323) | Attempt limits, bot-author filters, and escalation designed in from the start |
| G13 | Compliance-driven remediation at scale | [Consolidate the backlog, then fan out per service](https://partner-workshops.devinenterprise.com/sessions/b0c2e9aa776445f9a728a4fdf9bcbbaa) | Deferred findings need a stated reason; no child may quiet a scanner or touch another service |

### Feature delivery and orchestration

| # | Theme | Session | Pattern to watch for |
|---|---|---|---|
| G09 | Brownfield feature, full SDLC | [Account statement feature end to end](https://partner-workshops.devinenterprise.com/sessions/8b62cf9e2cc04c569b93478ad6f18b90) | Requirements and design as artifacts; the module's test task is the gate |
| G07 | Parallel brownfield work | [Fan out the remaining billing modules](https://partner-workshops.devinenterprise.com/sessions/974caf2df91343d4842fce824083ad17) | One child per module, each with its own namespace, branch, and gate — and a human approval step inside each child |

### Building the check itself

| # | Theme | Session | Pattern to watch for |
|---|---|---|---|
| S01 | Supplementary platform build | [Incident reproduction and verification harness](https://partner-workshops.devinenterprise.com/sessions/fe32b3ecb13c4d458aad6f385df108b7) | A check with three verdicts — pass, fail, and *inconclusive* — plus a legitimate-traffic assertion, so a fix cannot pass by refusing everybody |

<a id="near-misses"></a>
## Near Misses: Prompts That Look Fine

These are the interesting failures. Each one would pass a casual read — it names
a repository, a path, and a goal. Each one is also missing the single thing that
would make the result reviewable, and the failure mode is *specific to what is
missing*.

| # | Prompt in one line | The flaw | Session |
|---|---|---|---|
| N01 | "Get this service to 90% line coverage" | A measurable goal that measures the wrong thing — coverage rises whether or not anything is asserted | [session](https://partner-workshops.devinenterprise.com/sessions/a5bceed4881045ebb9fc61dc6e44f6a8) |
| N02 | "Bring every service's dependencies up to date, make CI green" | Nine ecosystems, one prompt, one gate — and "CI green" as the objective rewards quieting the build over understanding the break | [session](https://partner-workshops.devinenterprise.com/sessions/2f457ab871e34e778cec004fdf801593) |
| N03 | "Refactor the gateway; behavior must stay identical" | The right constraint with no executable way to check it, so "identical" ends up asserted rather than proven | [session](https://partner-workshops.devinenterprise.com/sessions/063079f4377142e6af187c242d08cb3f) |
| N04 | "Fix the flaky tests so the suite passes reliably" | A green pipeline as the goal rather than a diagnosis, which makes retries and skips the cheapest winning move | [session](https://partner-workshops.devinenterprise.com/sessions/7cf5cbc29d1d4f6aad06e61d6bb8144d) |
| N05 | "Modernize the dashboard; it should look the same" | Acceptance criteria only a human eye can evaluate, over a scope too large to review in one pass | [session](https://partner-workshops.devinenterprise.com/sessions/971ceb04251d4d768850a985fa03597f) |

Compare each near miss with its well-formed twin — N01 against G11, N02 against
G12, N05 against G15. The task is the same and the estate is the same. The
difference is entirely in what the prompt made checkable, which is the most
useful single comparison in this section.

<a id="warm-up"></a>
## Warm-Up: The Obvious Failures

Useful for a first pass, when the point is to see the failure mode at all rather
than to spot it in a plausible prompt: [make the codebase
better](https://partner-workshops.devinenterprise.com/sessions/1a87fef14a6b4757a41a21be05695f58) (no
scope), [rewrite every service in
Rust](https://partner-workshops.devinenterprise.com/sessions/763b0c1a86ec4267a86cabb1fc4403b7) (no
slice, no seam), [production is broken, fix
it](https://partner-workshops.devinenterprise.com/sessions/9473ca3b104543fba16c8976e1f19cb0) (no
reproduction), [change the pricing
engine](https://partner-workshops.devinenterprise.com/sessions/ed52a1b408274d009a4d3da095c5b64e)
(names code in no attached repository), and [skip the tests, push to
main](https://partner-workshops.devinenterprise.com/sessions/6a0eef4fb15a4a57a31fd5f8e3d8b540)
(gate bypass — worth reading for whether the session complies, negotiates, or
refuses).

<a id="exercise"></a>
## Exercise: Diagnose Before You Read the Diff

Work the five near misses in order. For each one, before scrolling past the
prompt, write down:

1. Which of the six prompt components — repository, file paths, expected
   behavior, acceptance criteria, verification mechanism, constraints — is
   missing or is present but unverifiable.
2. What the session will most likely do with the ambiguity.
3. The rewritten prompt you would have sent, in five lines or fewer.

Then scroll, and compare your rewrite with the well-formed twin. Where your
prediction was wrong, the interesting question is not whether the session behaved
well, but which piece of context you assumed it had.

Run it in reverse on the sixteen: delete one component from a good prompt and
predict which failure you have just created. This is the fastest way to learn
which components carry the most weight for which kind of work — a recorded
before-state matters most on modernization, acceptance criteria matter most on
features, and constraints matter most on anything touching a shared environment.

<a id="flows-worth-building-next"></a>
## Flows Worth Building Next

Each candidate is stated as the verification loop it would need, because a flow
without one is a presentation rather than a walkthrough.

| Candidate flow | The loop that would make it real | Groundwork |
|---|---|---|
| **Live incident to root cause** | Reproduce the symptom through the gateway, fix the owning service, and re-run the same probe — with a third verdict, "inconclusive", so a service that is simply down cannot read as a pass | Harness built in S01; the `!live-incident-rca` playbook is registered |
| **API contract drift** | The published specification replayed against the running services, failing on drift rather than on style | The contract suites already in the polyglot monorepo |
| **Dependency and EOL sweep** | The scanner re-run after the upgrade, plus each service's own tests, with an escalation path when a fix needs an architectural change | G12 and G13 between them cover the mechanics for one service and the fan-out for many |
| **Cost or performance regression** | A before-and-after number from the same load profile, captured in the PR — an adjective is not a result | Load-test scaffolding and the observability stack |
| **Batch window modernization** | The nightly job's output diffed against the legacy run for the same input date, to the cent | The existing batch job and its deterministic seed |

Two rules learned from building this set: **write the check before the thread** —
a walkthrough whose verification step is "read the output and see if it looks
right" typically drifts into a presentation. And **run the flow before you
document it** — a live run usually surfaces something the author's reading of the
code missed, and those findings are the most credible content in a thread.

<a id="key-takeaways"></a>
## Key Takeaways

- A session is worth reading when you can point at the moment something ran. All
  sixteen good sessions are organized around that moment; the ten failures are
  organized around its absence.
- The near misses matter more than the obvious failures. Every one of them names
  a repository, a path, and a goal — and still cannot be reviewed, because the
  acceptance criterion is a proxy metric, a human eye, or an unstated invariant.
- On modernization work specifically, the before-state is the deliverable that
  makes everything else checkable. Recording it costs minutes; reconstructing it
  after the fact is usually impossible.
- A session that stops to ask a question, or refuses an unsafe instruction, has
  behaved correctly. Judge the ending by whether it is honest, not by whether it
  is a PR.
- Reproducing a set this size is a scripted operation, not a morning of clicking:
  prompts come from the source threads, sessions are created through the API
  against a named organization, and the whole set stays reviewable afterwards.
