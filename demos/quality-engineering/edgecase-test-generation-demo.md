# Positive/Negative Edge-Case Test Generation — Demo

A single linear demo showing Devin acting as quality-engineering capacity:
proving — not eyeballing — that a service's test suite covers positive,
negative, and boundary edge cases. A mutant harness plants realistic bugs into
the code and checks whether the tests notice. Devin runs the harness, finds
the survivors (bugs the suite misses), writes the missing edge-case tests,
and drives the harness to green: **every planted bug killed by a test**.

The prompts below invoke the `!edgecase-tests` Devin Playbook — the reusable
procedure — whose source lives in the code repo at
[`otterworks/.workshop/playbooks/edgecase-test-generation.devin.md`](https://github.com/Cognition-Partner-Workshops/otterworks/blob/main/.workshop/playbooks/edgecase-test-generation.devin.md).
The repo-specific `make edgecase-verify` mechanics come from that repo's Skill
(`.agents/skills/edgecase-test-generation/SKILL.md`), auto-loaded whenever
Devin works in the repo.

## Table of Contents

- [Quick Start](#quick-start)
- [Why This Is a Capacity Problem](#capacity)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Closes the Gaps](#part-1)
  - [Act 1 — Orient over the suite and the harness](#act-1)
  - [Act 2 — Run the harness: the survivors are the work queue](#act-2)
  - [Act 3 — Close one gap live, with verification](#act-3)
  - [Act 4 — Finish the queue and go green](#act-4)
- [Part 2 — Review the PR](#part-2)
- [Scaling Out: Fan-Out, Schedules, Automations](#scaling)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Everything runs locally in the `otterworks` repo — no infrastructure needed:

```bash
cd services/document-service && poetry install --no-root && cd ../..
make edgecase-list       # the catalog of planted bugs
make edgecase-verify     # plant each bug, run the tests, report KILLED/SURVIVED
```

On `main` the run ends red — that is the point. Six of the eight planted bugs
survive, because the existing suite tests only the happy path.

---

<a id="capacity"></a>
## Why This Is a Capacity Problem

Every engineering team knows its edge-case coverage is thin — empty inputs,
hostile inputs, off-by-one boundaries, contract guarantees nobody asserted.
The work never gets scheduled: it is repetitive, it competes with feature
delivery, and no single gap ever feels urgent until one ships as an incident.

This is exactly the shape of work a cloud autonomous agent absorbs as
**additional engineering capacity** rather than a tool an engineer drives:

- **It runs off the team's calendar.** The session below runs unattended on
  its own machine; nobody pairs with it. The same playbook runs nightly on a
  schedule or fires from an Automation when coverage regresses — quality work
  stops competing with sprint capacity at all.
- **The methodology is codified, not tribal.** The `!edgecase-tests` playbook
  encodes the procedure once; every session — and every teammate who invokes
  it — runs the same loop with the same exit criteria.
- **The output is verified, so review is cheap.** The engineer's role shrinks
  to reviewing a PR whose correctness is already machine-checked: every new
  test demonstrably fails when the code is wrong. That is the difference
  between delegating work and delegating *finished* work.
- **It parallelizes like infrastructure, not like headcount.** One
  orchestrating session can fan a child session out per service, each in its
  own isolated VM — the backlog across a whole platform compresses from weeks
  of deferred toil into one reviewed wave of PRs.

---

<a id="repositories"></a>
## Repositories

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) —
  the polyglot microservices platform. This demo targets the Python
  `document-service` and its pytest suite, with the mutant harness in
  `qa/edgecase/`, the playbook source in `.workshop/playbooks/`, and the repo
  Skill in `.agents/skills/`.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | State |
|---|---|
| **Before** | `main`: the harness, the 8-bug catalog, and a happy-path test suite. `make edgecase-verify` reports **2 KILLED / 6 SURVIVED** — six realistic bugs the suite cannot see. |
| **After** | A PR branch adding edge-case tests to `tests/test_document_service.py`. `make edgecase-verify` reports **all 8 KILLED**; the suite itself still passes on unmutated code. |

The verification loop is the heart of the demo. A test only counts if it fails
when the code is wrong — so the harness plants each cataloged bug, runs the
suite, and restores the code:

- **KILLED** — a test failed while the bug was planted: real coverage.
- **SURVIVED** — the suite stayed green with a live bug: a proven gap, mapped
  to a concrete positive/negative/boundary edge case.

Line coverage can't make this distinction; a survivor is executed by tests
that assert too little to notice it. Application code never changes — only
test files — so the demo is repeatable and `main` stays the durable
before-state.

---

<a id="part-1"></a>
## Part 1 — Devin Closes the Gaps

<a id="act-1"></a>
### Act 1 — Orient over the suite and the harness

Ask Devin (or DeepWiki) about the estate before starting — coverage questions
typically take minutes instead of an afternoon of code reading:

- *"In otterworks document-service, which DocumentService methods have no
  negative-input tests?"*
- *"What does the qa/edgecase harness do and how does it decide KILLED vs
  SURVIVED?"*

<a id="act-2"></a>
### Act 2 — Run the harness: the survivors are the work queue

Kick off the session:

```
!edgecase-tests

In Cognition-Partner-Workshops/otterworks, close the edge-case coverage gaps in
document-service. Run `make edgecase-verify` first and treat the surviving
mutants as the work queue. Add the missing positive/negative/boundary tests to
services/document-service/tests/test_document_service.py only — no application
code, catalog, or harness changes. Finish with a full harness run showing every
mutant KILLED, and include the before/after harness output in the PR
description.
```

Devin's first harness run reproduces the before-state:

```
mutant    EDGE-PAGINATE-ZERO-TOTAL ... KILLED
mutant    EDGE-DELETE-MISSING-TRUE ... KILLED
mutant    EDGE-SEARCH-ESCAPE-DROPPED ... SURVIVED
mutant    EDGE-EXPORT-HTML-UNESCAPED ... SURVIVED
mutant    EDGE-PATCH-NOOP-VERSION-BUMP ... SURVIVED
mutant    EDGE-WORDCOUNT-WHITESPACE ... SURVIVED
mutant    EDGE-LIST-PAGE-OFFSET ... SURVIVED
mutant    EDGE-RESTORE-MISSING-VERSION ... SURVIVED

RESULT: FAIL — 6 mutant(s) survived; the suite is missing edge-case coverage
```

Six live bugs — wildcard injection in search, unescaped HTML export, a no-op
PATCH that bumps the version, whitespace-inflated word counts, a pagination
off-by-one, restore of a nonexistent version — and the existing suite is green
through all of them.

<a id="act-3"></a>
### Act 3 — Close one gap live, with verification

Watch one survivor closely: `EDGE-LIST-PAGE-OFFSET`. The mutant changes the
pagination offset from `(page - 1) * size` to `page * size`, silently skipping
the entire first page of results. The existing test seeds three documents and
asserts only the **total count** — which the mutated query still returns
correctly — so the suite stays green with the bug live.

Devin's fix is a boundary test that requests `page=1, size=2` and asserts the
**returned items**, not just the total. The per-mutant loop proves the kill:

```
make edgecase-verify MUTANT=EDGE-LIST-PAGE-OFFSET
# baseline  document-service ... green
# mutant    EDGE-LIST-PAGE-OFFSET ... KILLED
```

This is the credibility beat: the new test is not padding — it demonstrably
fails when the code is wrong, and the harness proves it.

<a id="act-4"></a>
### Act 4 — Finish the queue and go green

Devin repeats the loop for each survivor — an adversarial `%` wildcard search,
a markup-injection export, an empty PATCH asserting the version must **not**
change, a double-space word count — then finishes with the full run:

```
make edgecase-verify
# ...
# RESULT: PASS — all 8 mutants killed.
```

And confirms the suite still passes on unmutated code — a test that only
passes against mutated code would be wrong.

---

<a id="part-2"></a>
## Part 2 — Review the PR

Devin opens a PR containing only test-file changes. The review is fast because
the correctness question is already answered by the harness:

- The description carries the **before** (6 survivors) and **after** (all
  killed) harness output — the programmatic proof.
- Each new test names the edge case it pins down, grouped
  positive/negative/boundary/contract.
- Devin Review comments on the PR like any teammate's reviewer would — the
  same feedback loop the team already uses.

If a mutant had revealed a *real* application bug, the playbook requires Devin
to stop and report it rather than test around it — worth calling out while
scrolling the diff.

---

<a id="scaling"></a>
## Scaling Out: Fan-Out, Schedules, Automations

The single-service run generalizes into standing capacity:

- **Fan-out:** an orchestrating session spawns a child session per service —
  each with its own isolated VM, branch, and PR — and monitors them to green.
  The catalog's per-service structure (`mutants.yaml` keys each mutant by
  service) is what makes the split clean.
- **Scheduled sessions:** a nightly run of `!edgecase-tests` catches new
  survivors as the catalog grows or code changes — quality enforcement as a
  background process.
- **Automations:** wire `make edgecase-verify` into CI as a gate; when it
  fails, an Automation starts a session that closes the new gap and pushes a
  fix, typically before the team's morning standup.

Isolation is what makes this safe: each session runs on its own machine with
its own scoped credentials, so parallel runs and unattended schedules never
collide with each other or with the team's working branches.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **A test only counts if it fails when the code is wrong.** The mutant
  harness turns "do we have edge-case coverage?" from an opinion into a
  KILLED/SURVIVED table — programmatic verification, not "looks reasonable"
  review.
- **Survivors are the highest-value test backlog.** Each one is a realistic
  bug the current suite provably cannot see, mapped to a concrete
  positive/negative/boundary input class.
- **A cloud autonomous agent turns deferred quality work into capacity.** The
  playbook codifies the methodology, sessions run it unattended off the team's
  calendar, and machine-verified PRs shrink the human cost to review — so
  edge-case coverage stops losing the prioritization fight against features.
- **It scales like infrastructure.** Fan-out across services, nightly
  schedules, and CI-triggered Automations run the same `!edgecase-tests`
  procedure consistently, in isolated environments, without adding headcount.
