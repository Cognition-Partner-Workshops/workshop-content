# Quality Engineering — The Mutation Gate Demo

A single linear demo that shows Devin measuring whether a test suite can
actually catch bugs — then making it provably better. The mutation gate plants
deterministic, realistic bugs (mutants) into a service's source and runs the
service's own suite against each one; a mutant the suite fails to kill is a
proven test-coverage hole, not an opinion. Devin runs the gate on OtterWorks'
`search-service`, finds a real authentication-bypass hole the suite would ship
silently, kills it with focused tests, ratchets the committed baseline down,
and lands the work as a PR that Devin Review critiques. The same loop then runs
unattended: fanned out across services with child sessions, on a weekly cadence
with a scheduled session, and event-driven through an Automation that reacts to
CI failures.

The prompts below invoke the `!qe-mutation-gate` Devin Playbook — the reusable
hardening procedure — whose source lives in the code repo at
[`otterworks/.workshop/playbooks/qe-mutation-gate.devin.md`](https://github.com/Cognition-Partner-Workshops/otterworks/blob/main/.workshop/playbooks/qe-mutation-gate.devin.md).
The repo-specific mechanics (Makefile targets, config and baseline paths,
onboarded services, fail-closed conditions) come from that repo's Skill
(`.agents/skills/qe-mutation-gate/SKILL.md`), auto-loaded whenever Devin works
in the repo.

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Why Mutation Testing, Not Coverage Percent](#why)
- [Part 1 — Devin Hardens One Service](#part-1)
  - [Act 1 — Orient over the quality estate](#act-1)
  - [Act 2 — Run the gate and kill a real coverage hole, live](#act-2)
  - [Act 3 — The PR + Devin Review loop](#act-3)
- [Part 2 — Scale It Out](#part-2)
  - [Act 4 — Fan out across services with child sessions](#act-4)
  - [Act 5 — Put it on a cadence: scheduled sessions](#act-5)
  - [Act 6 — Event-driven quality: Automations](#act-6)
- [Part 3 — Confirm the Result](#part-3)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

The verification loop is one command — it verifies the clean suite is green,
runs the deterministic mutant set, and compares survivors against the committed
baseline ledger:

```bash
make qe-mutation-setup SERVICE=search-service   # one-time dependency install
make qe-mutation SERVICE=search-service         # the gate
```

The gate fails closed on: a red clean suite, a missing baseline, a source
fingerprint mismatch, a new surviving mutant, or a stale baseline entry for a
mutant now killed. Every baseline change requires an audited
`REBASELINE_REASON` recorded in the ledger.

<a id="repositories"></a>
## Repositories

| Repo | Role |
|---|---|
| [`otterworks`](https://github.com/Cognition-Partner-Workshops/otterworks) | The polyglot microservices app under test. `main` is the golden before-state: the harness (`qe/mutation/`), the baseline ledger, the Playbook source, and the Skill all live there. The hardening work Devin produces lands on working branches as PRs. |

<a id="why"></a>
## Why Mutation Testing, Not Coverage Percent

Line coverage says which lines *ran* during tests; it says nothing about what
the tests *assert*. A suite can execute an authentication check on every run
and still never notice if the check is inverted. The mutation gate measures the
thing that matters: **can this suite tell a buggy program from a correct one?**
Every surviving mutant is a specific, reproducible bug — `file:line:col` and
the exact operator flip — that the current suite would ship. That turns "our
tests feel thin" into a ranked, actionable work list, and turns "the tests
pass" into a claim with teeth.

---

<a id="part-1"></a>
## Part 1 — Devin Hardens One Service

<a id="act-1"></a>
### Act 1 — Orient over the quality estate

Start a session and let Devin map what quality controls already exist and where
the gaps are:

```
Survey the quality-engineering surface of this repo: the test suites per
service, the API-flow and contract tests under tests/, CI in
.github/workflows/ci.yml, and the mutation gate under qe/. Which services are
onboarded to the mutation gate, what does its baseline currently record, and
what does that say about test quality in search-service? Summarize — don't
change anything yet.
```

Devin reads the estate in minutes — the Skill gives it the mutation-gate
mechanics, and DeepWiki-style repo orientation typically surfaces the
per-service suites, the black-box API flows, and the CI matrix without hand
holding. The punchline of the survey: `search-service`'s baseline ledger
records **43 surviving mutants out of 60 run** — 43 known, named coverage
holes — including several on the authentication middleware.

<a id="act-2"></a>
### Act 2 — Run the gate and kill a real coverage hole, live

Invoke the playbook with a small, verifiable kill budget aimed at the scariest
survivors:

```
!qe-mutation-gate

Service: search-service
Kill budget: the two survivors on app/middleware/auth.py line 51 (the bool-And
and cmp-Eq mutants), plus up to 3 more nearby auth survivors if the same tests
cover them.
Work on your own branch off main.
```

What happens, and why it is credible:

1. Devin runs `make qe-mutation SERVICE=search-service` — the gate verifies the
   clean suite is green, runs the 60-mutant deterministic set, and PASSes
   against the committed baseline of 43 known survivors.
2. It opens `app/middleware/auth.py:51`:

   ```python
   if svc_key and svc_key == expected_key:
   ```

   Both mutants on this line survived. With `and → or`, any non-empty key
   authenticates; with `== → !=`, only *wrong* keys authenticate. Either
   mutation is an **authentication bypass the test suite cannot see** — the
   line has coverage; the decision has no assertions.
3. Devin writes the negative-path tests the suite was missing: a request with a
   wrong internal key must be rejected; the correct key must be accepted. The
   clean suite stays green.
4. It re-runs the gate — which now **fails closed**: the baseline still lists
   the two mutants as allowed survivors, but they are killed. A stale ledger is
   itself a gate failure; reality and the recorded baseline must always match.
5. It ratchets the baseline down with an audited reason
   (`make qe-mutation-baseline SERVICE=search-service REBASELINE_REASON="..."`)
   and re-runs to green.

The divergence caught here is real, not staged: the gate found it, the fix is
an assertion on the actual security decision, and the whole exchange is
reproducible from the deterministic seed. If you remember one beat, remember
this one — the confidence comes from the gate failing closed twice (once on the
coverage hole, once on the stale ledger), not from anyone eyeballing the tests.

<a id="act-3"></a>
### Act 3 — The PR + Devin Review loop

Devin opens the PR itself: the new tests, the ratcheted baseline ledger, and
the before/after gate summary pasted into the PR body (the report file is a
git-ignored CI artifact by design — generated output does not churn diffs).

Then the second AI enters: **Devin Review** comments on the PR automatically.
This is where the collaboration model shows — the work is not a black box:

- Review reads the diff in context: it can flag a killing test that asserts on
  an incidental (a log string, an import side effect) rather than the changed
  behavior, or a baseline entry removed without a matching test.
- The rebaseline reason is *in the diff* (recorded in the ledger), so the
  reviewer — human or AI — sees the audit trail, not just the numbers.
- Ask the session to resolve the review threads before handoff:

```
Address the Devin Review comments on your PR. For anything you disagree with,
reply on the thread with the evidence (the gate report line or the test that
kills the mutant) instead of changing code.
```

Every unit of quality work in this demo lands the same way: a branch, a PR, a
green gate summary in the body, and a reviewed thread — the review loop is the
delivery mechanism, not an afterthought.

---

<a id="part-2"></a>
## Part 2 — Scale It Out

<a id="act-4"></a>
### Act 4 — Fan out across services with child sessions

One session orchestrates; children do the units of work in parallel, each in
its own isolated VM, on its own branch, opening its own verified PR:

```
Act as the orchestrator for a mutation-hardening wave. Spawn one child session
per unit of work:

1. search-service: !qe-mutation-gate with a kill budget of 5 survivors in
   app/services/sqs_consumer.py, on its own branch.
2. document-service: its clean suite is red on main (stale auth headers in
   tests/test_documents_api.py after the JWT hardening) — repair the suite
   without weakening the service's auth, then onboard it to the mutation gate
   in qe/mutation/config.yaml with an initial audited baseline, on its own
   branch.

Monitor both children to a green gate and an open PR each, then report the
combined before/after survivor counts.
```

Isolation is what makes this safe: each child has its own VM and workspace, so
two sessions mutating source files in place never collide, and a failed child
leaves no shared state to clean up. The document-service child also shows the
honest edge of the tool — the gate *refuses* to run on a red clean suite, so
repairing the stale tests is the onboarding work, not something to paper over.

<a id="act-5"></a>
### Act 5 — Put it on a cadence: scheduled sessions

The same playbook runs unattended. Create a scheduled session (Settings →
Scheduled Sessions, or via the API) with a prompt like:

```
!qe-mutation-gate

Service: search-service
Kill budget: 3 survivors, highest-risk first (auth, money math, state
machines before formatting/logging).
Work on your own branch. If the gate fails for any reason other than the
survivors you killed, stop and report instead of rebaselining.
```

Weekly, every run ratchets the baseline down by a few survivors and lands as a
reviewed PR. Because mutant selection is deterministic (same source + seed +
cap → same mutant set) and the baseline is committed, every scheduled run is
comparable to the last — the ledger history *is* the quality trend line.

<a id="act-6"></a>
### Act 6 — Event-driven quality: Automations

[Automations](https://docs.devin.ai/product-guides/automations) start sessions
from events instead of a clock. Two that fit this loop naturally:

- **On CI failure on a PR** — trigger a session with the failing PR: if the
  mutation gate failed with a *stale baseline entry*, the contributor killed a
  survivor without ratcheting; Devin ratchets the ledger with an audited reason
  and pushes the fix. If it failed with a *new survivor*, the PR reduced test
  quality; Devin writes the killing test or flags the regression on the thread.
- **On PR opened touching a gated service** — trigger a session that runs the
  gate on the PR branch and posts the before/after survivor delta as a comment,
  so every change to `search-service` carries its test-quality impact next to
  Devin Review's findings.

The trigger changes; the procedure does not — it is the same `!qe-mutation-gate`
playbook, which is the point of codifying it once.

---

<a id="part-3"></a>
## Part 3 — Confirm the Result

Close the loop where the work actually lives:

1. **The PR page** — the gate summary in the body (mutants run / killed /
   survived, before → after), the ratcheted ledger in the diff with its audited
   reason, and the resolved Devin Review threads. This is the unit-of-work
   proof.
2. **The gate itself, re-run from a clean checkout of the PR branch:**

   ```bash
   make qe-mutation SERVICE=search-service
   ```

   PASS with a strictly smaller `allowed_survivors` list — anyone can reproduce
   the claim in one command, which is the whole confidence story.
3. **The ledger history** — `git log -p qe/mutation/baselines/search-service.json`
   shows the survivor count ratcheting down PR by PR, each step with a recorded
   reason. Test quality is now a number with an audit trail, not a feeling.

<a id="key-takeaways"></a>
## Key Takeaways

- **Test quality became measurable.** A surviving mutant is a named,
  reproducible bug the suite would ship — the gate turned "our tests feel thin"
  into `auth.py:51: authentication bypass, unasserted` and a one-command proof.
- **The verification loop fails closed, twice.** A new coverage hole fails the
  gate; so does a stale ledger after a fix. Devin goes green by doing the work
  (real assertions + an audited ratchet), never by editing the evidence.
- **A real bug beat any narration.** The auth-middleware bypass was found by
  the gate on the actual codebase, fixed against the actual behavior, and
  killed on the re-run — nothing staged.
- **Devin Review closes every unit of work.** Each PR ships with the gate
  summary as evidence and an automated reviewer on the diff — the collaboration
  model is visible, not a black box.
- **Automations and schedules make it durable.** The same `!qe-mutation-gate`
  playbook runs live, weekly on a schedule, and event-driven on CI failures —
  codified once, consistent everywhere.
- **Isolation makes parallelism safe.** Child sessions each mutate source in
  their own VM on their own branch; a wave of hardening runs concurrently with
  zero shared state.
