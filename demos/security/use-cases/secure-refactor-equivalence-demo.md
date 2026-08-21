# Secure Refactor with Functional Equivalence — Change the Code, Prove the Behavior Didn't

A single-thread demo showing Devin closing security findings that no version bump
can fix: SQL built by string concatenation, a path-traversing file read, and a
share token derived from MD5. Devin characterizes what each class does *before*
touching it, reproduces each exploit, refactors with the secure pattern, and then
proves with commands that every externally observable behavior is byte-identical
and only the attacks changed.

<a id="toc"></a>
## Table of Contents

- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Part 1 — Orient: What Is Actually Broken, and Where](#part-1)
- [Part 2 — Characterize the Before-State](#part-2)
- [Part 3 — Reproduce the Exploits First](#part-3)
- [Part 4 — Refactor, Interface Unchanged](#part-4)
- [Part 5 — The Divergence the Gate Caught](#part-5)
- [Part 6 — Prove Functional Equivalence](#part-6)
- [Part 7 — Runtime Proof Against the Running App](#part-7)
- [Part 8 — Fan Out One Finding Per Session](#part-8)
- [Part 9 — Run It Unattended: CI, Schedules, Automations](#part-9)
- [Part 10 — Confirm It in the Dashboards](#part-10)
- [Key Takeaways](#key-takeaways)

---

<a id="prerequisites"></a>
## Prerequisites

1. **The `!secure-refactor-equivalence` playbook** is registered in your Devin
   organization. Its source is
   `.workshop/playbooks/secure-refactor-equivalence.devin.md` on otterworks.
2. **The harness and the findings are on `main`:** `security/equivalence/` and the
   `eq-*` targets in the `Makefile`, plus the three flawed classes in
   `document-service`. `main` is the before-state on purpose, which is what makes
   this repeatable.
3. **Parts 1–6 need no deployment** — the loop is a seeded fixture and a test
   suite inside the session's own VM. Part 7 brings the stack up locally with
   `make up`.

---

<a id="quick-start"></a>
## Quick Start

Paste this into Devin to run the whole loop for one finding.

```
!secure-refactor-equivalence

Finding: OW-SEC-401, SQL injection in services/document-service/app/services/document_query_repository.py
Repo: Cognition-Partner-Workshops/otterworks

Characterize the current behavior, reproduce the injection, refactor the class with parameter binding and an ORDER BY allow-list keeping the public interface unchanged, then prove with the harness that every contract case is byte-identical and the attack cases are closed. Work on your own branch — main stays vulnerable on purpose.
```

---

<a id="repositories"></a>
## Repositories

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) —
  polyglot monorepo: 11 backend services across 8 languages, plus two TypeScript
  frontends (`docs/SDLC-COVERAGE.md`). The findings live in the Python
  `document-service`. The equivalence harness is `security/equivalence/`; the
  repo-specific mechanics are in
  `.agents/skills/secure-refactor-equivalence/SKILL.md`, which Devin loads
  automatically when it works in this repo. The runtime probe suite it reuses in
  Part 7 is the existing `security/dast/`.

---

<a id="part-1"></a>
## Part 1 — Orient: What Is Actually Broken, and Where

Start from where a security report stops: three findings and a service nobody on
the call has read.

```
A security assessment flagged three source-level findings in the OtterWorks document-service. These need code changes, not version bumps.

Before changing anything, report for each finding: the file, the class, the exact methods, the CWE, the insecure pattern in the code today, and the HTTP route that reaches it.

Use make eq-list and answer with the table.
```

```
finding     cwe      module            subject                                                          contract/attack    evidence
----------  -------  ----------------  ---------------------------------------------------------------  -----------------  ---------------
OW-SEC-401  CWE-89   document-service  DocumentQueryRepository._where/count_documents/search_documents  13/4               matches before-state
OW-SEC-402  CWE-22   document-service  ExportArchive.read_export                                        6/2                matches before-state
OW-SEC-403  CWE-328  document-service  ShareLinkService.mint_token/verify_token                         3/1                matches before-state
```

The subject of each finding is a class and a method list, not a file — which is
the customer's actual question: *can the affected method be refactored and shown
to behave the same?* Read the first one and the flaw is not subtle:

```python
if title_contains:
    clauses.append(f"lower(title) LIKE lower('%{title_contains}%')")
```

The filter values, the sort column, the direction, the limit and the offset are
all interpolated into the statement text. `GET /api/v1/documents/?title=…`
reaches it; `GET /api/v1/documents/exports?name=…` reaches the archive read; and
`GET /api/v1/documents/shared?document_id=…&token=…` reaches a token that is
`md5(document_id + a salt that is in the source)`.

Note the last column of that table. Evidence state is reported alongside the
finding, so "we have a recording to compare against" is never assumed.

---

<a id="part-2"></a>
## Part 2 — Characterize the Before-State

This is the half most remediation work skips: pin what the code does *now*,
including the parts nobody would think to write down.

```
Characterize the current externally observable behavior of all three subjects before any change: inputs, outputs, ordering, side effects and error cases, at the class level and through the HTTP routes.

Run make eq-baseline and make eq-tests, and tell me what is under contract and what the attack cases assert.
```

```
OW-SEC-401  baseline  contract-title-fragment-is-case-insensitive              contract  ok
OW-SEC-401  baseline  contract-count-matches-filter                            contract  ok
OW-SEC-401  baseline  contract-sort-title-ascending                             contract  ok
OW-SEC-401  baseline  contract-limit-and-offset                                contract  ok
OW-SEC-401  baseline  contract-http-filtered-list                               contract  ok
OW-SEC-402  baseline  contract-missing-export-raises-file-not-found             contract  ok
OW-SEC-402  baseline  attack-traversal-reads-outside-the-archive                attack    ok    still exploitable (expected before-state)
OW-SEC-403  baseline  attack-offline-token-forgery                              attack    ok    still exploitable (expected before-state)

baseline: ok
```

```
make eq-tests

OW-SEC-401 [document-service]: ok - 49 passing, 9 failing (recorded 49 passing)
```

Two details make this evidence rather than a screenshot:

- **Every case is reproducible from one seed.** `security/equivalence/seed/document-service.json`
  is the only fixture setup — six documents across two owners (one deleted, one
  template), two files inside the export archive and one outside it. A case that
  needs its own setup step is a case the replay cannot reproduce, and it is
  rejected.
- **The recording is fingerprinted against everything that can change what it
  observes** — the subject sources, the seed, the case file, the emitter.
  Change any of those inputs and the gate reports `stale` and exits `2`, which
  is not a pass; drop a recorded case from the case file and it grades as
  `missing`. The evidence file itself is a committed artifact guarded by review
  of its diff plus an audited re-record path (`ALLOW_RERECORD=1` with a stored
  `REASON`) — and `eq-exploit` re-derives the attack verdict from the running
  code with the recording ignored, so the recording is never the only witness.

The nine failing tests are pre-existing on `main` — mutating endpoints called
without an auth header. The gate compares against the **recorded pass list**
rather than "everything green", so known failures cannot hide a new one and
nobody is tempted to "fix" the suite to get a clean run.

---

<a id="part-3"></a>
## Part 3 — Reproduce the Exploits First

A fix for a vulnerability you never demonstrated is a guess.

```
Before refactoring, prove each finding is real. Run make eq-exploit and show me, for each attack case, the request or call that fires it and what it returns today.
```

```
make eq-exploit

finding     case                                          status  observation
OW-SEC-401  attack-content-type-tautology-crosses-owners  fail    [{"content": "# {{title}}", "content_type": "text/markdown", …
OW-SEC-401  attack-title-quote-breaks-the-statement       fail    OperationalError: unrecognized token: "') O…
OW-SEC-401  attack-sort-parameter-is-injectable           fail    ProgrammingError: You can only execute one statement at a time
OW-SEC-401  attack-http-error-based-probe                 fail    {"body": {"detail": "Invalid filter: (sqlite3.OperationalError) …
OW-SEC-402  attack-traversal-reads-outside-the-archive    fail    "SUPPLIER_API_KEY=sk-fixture-not-a-real-key\n"
OW-SEC-403  attack-offline-token-forgery                  fail    {"forged_token_accepted": true}

exploit: fail (exploitable)
```

Exit `1`, and every line is a concrete claim rather than a scanner category:
`content_type=x' OR '1'='1` returns another owner's documents through an
owner-scoped API; `name=../../../../etc/passwd` reads a file that belongs to no
export archive; the sort parameter executes a second statement; and a token
computed offline from the source-readable salt is accepted as a valid share link.

`eq-exploit` deliberately ignores the recording and re-derives the verdict from
the running code, so it answers "is it still exploitable *right now*" — the
question you want answered after the refactor, by the same detector code that
answered it before.

---

<a id="part-4"></a>
## Part 4 — Refactor, Interface Unchanged

```
Refactor all three subjects with the secure pattern for each finding. Constraints: the public interface stays exactly as it is — same class names, same method names, same signatures, same return shapes — and no behavior changes except the attacks closing.

Explain each pattern you applied and what it preserves.
```

The three patterns, and the part of each that is easy to get wrong:

| Finding | Pattern applied | The trap |
|---|---|---|
| `OW-SEC-401` | bind every filter value as a parameter; resolve `ORDER BY` from an allow-list of sortable columns and directions; bind `LIMIT`/`OFFSET` | an allow-list that rejects an input the API accepted yesterday is a behavior change; unbound `ORDER BY` is the one place binding does not apply |
| `OW-SEC-402` | resolve the path and require the resolved candidate to be contained by the resolved archive root | the raised error is part of the contract — a new message or a new exception type breaks callers |
| `OW-SEC-403` | HMAC-SHA256 over the document id under a configured secret, compared with `hmac.compare_digest`; random per-process secret when unconfigured, so it fails closed | the token length and the mint/verify round trip are the contract; changing the digest must not change the shape |

The interface constraint is checked, not trusted: the recording captured the
signatures of the subject methods, and a refactor that renames a parameter fails
the gate as an interface drift.

---

<a id="part-5"></a>
## Part 5 — The Divergence the Gate Caught

The SQL refactor is textbook — every value becomes a bound parameter:

```python
clauses.append("lower(title) LIKE lower(:title_contains)")
params["title_contains"] = title_contains
```

The injection is closed. The suite still passes. And the gate fails:

```
make eq-verify

OW-SEC-401  remediated  contract-title-fragment-is-case-insensitive   contract  fail   behaviour changed
OW-SEC-401  remediated  contract-count-matches-filter                 contract  fail   behaviour changed
OW-SEC-401  remediated  contract-unowned-filter-spans-owners          contract  fail   behaviour changed
OW-SEC-401  remediated  contract-http-filtered-list                   contract  fail   behaviour changed
OW-SEC-401  remediated  contract-folder-scoped-title-filter           contract  fail   behaviour changed
OW-SEC-401  remediated  attack-title-quote-breaks-the-statement       attack    ok     neutralised

OW-SEC-401/contract-title-fragment-is-case-insensitive: behaviour changed
```

The report says exactly what changed:

```json
{
  "id": "contract-title-fragment-is-case-insensitive",
  "recorded": { "outcome": "ok", "value": [ { "title": "Quarterly Revenue Report", "word_count": 8 } ] },
  "observed": { "outcome": "ok", "value": [] },
  "status": "fail"
}
```

The wildcards were in the old *statement* (`'%{title}%'`), so moving the value
into a bound parameter silently turned a substring search into an exact match.
Document search returns nothing for every partial term — a total feature outage,
delivered by a change that closes a real SQL injection and passes the service's
own test suite. `%` belongs on the value now:

```python
params["title_contains"] = f"%{title_contains}%"
```

```
make eq-verify

remediated: ok
```

This is why the loop exists. Nothing in that diff looks wrong, the security
objective was genuinely achieved, and the only thing standing between it and
production is a recorded behavior that a command compares byte for byte.

---

<a id="part-6"></a>
## Part 6 — Prove Functional Equivalence

```
Now prove the whole claim: run make eq-verify, make eq-exploit and make eq-tests, and tell me exactly what each one proves and what it would take for each to be wrong.
```

Three gates pointing in three different directions:

```
make eq-verify     remediated: ok        every contract case byte-identical to the recording; attacks flipped to neutralised
make eq-exploit    exploit: ok (closed)  re-derived from the running code, recording ignored
make eq-tests      tests: ok             49 passing, 9 failing — the recorded pass list, unchanged
```

- **`eq-verify`** is the functional-equivalence claim: 22 contract cases across
  the three subjects — including result ordering, pagination totals, the HTTP
  response bodies, and the `FileNotFoundError` message — compared as bytes
  against the before-state recording. Order is never canonicalized, because
  ordering *is* the business rule for a sorted list endpoint.
- **`eq-exploit`** is the security claim, and it is deliberately independent of
  the recording so a green result cannot be manufactured by editing evidence.
- **`eq-tests`** is the regression gate: the service's own suite compared against
  the recorded pass list.

And the statuses are a closed set — `ok`, `fail`, `missing`, `stale`,
`unmeasured`, `unrecorded`, `no-verdict` — mapped to exit `0`, `1`, or `2` for
anything inconclusive. A finding with no evidence, stale evidence, or a case the
emitter never measured fails closed. A typo in a status cannot silently become a
pass.

The PR carries all of it: each finding with CWE, file, class and methods; the
pattern applied; the before/after gate output; the suite counts; and the explicit
statement of preserved behavior with the contract case list backing it. The
harness reports are git-ignored generated output, so they attach as CI artifacts
and the summary lines go in the body.

---

<a id="part-7"></a>
## Part 7 — Runtime Proof Against the Running App

Unit-level evidence is necessary and not sufficient — the customer's question
ends at the running service. The repo already has the probe suite, so the answer
is to rerun the *original* attack, not to write a new one.

```
Bring the stack up locally, rebuild document-service from the refactored code, and re-run the original probes for these findings against the running app:

  make up
  docker compose up -d --build document-service
  make dast-verify FINDING=DAST-SQLI-ERROR-BASED DAST_TARGET=http://localhost:8080
  make dast-verify FINDING=DAST-PATH-TRAVERSAL-EXPORT DAST_TARGET=http://localhost:8080
  make dast-verify FINDING=DAST-SHARE-TOKEN-FORGERY DAST_TARGET=http://localhost:8080

Attach the request and response evidence for each probe before and after, then run the full suite with make dast-scan.
```

The rebuild step is the one people skip. Probe the old image and you get a
"closed" verdict for code that is not running; the harness has no way to know,
so the discipline has to be in the loop. Never point a scan at a shared tenant —
these probes are for the local stack.

The path-traversal probe also earns its keep as a piece of test code: it sends
its percent-encoded payload in the raw query string, because passing `..%2f`
through a client's parameter encoder re-encodes the `%` and the server sees
harmless literal text. A probe that cannot express its own payload on the wire
reports "not vulnerable" forever.

---

<a id="part-8"></a>
## Part 8 — Fan Out One Finding Per Session

Three findings in one service is one session. A remediation backlog is not.

```
Here are the remaining findings from the assessment. Launch one child session per finding, each with the !secure-refactor-equivalence playbook, its own branch off main and its own scoped PR.

Every child runs the full loop — eq-baseline, eq-exploit, refactor, eq-verify, eq-exploit, eq-tests — and reports in the same evidence format.

Monitor them and give me a table of finding, CWE, subject class, pattern applied, session, PR and gate result.
```

Each child gets its own VM, its own scoped credentials and its own branch, so
three refactors of the same service proceed simultaneously without colliding —
and none of them can push to the before-state that the next run depends on.
Isolation is what makes the parallelism safe rather than exciting.

Then the report a security team can actually file:

```
Produce a single auditor-ready report covering all findings: for each one the CWE, the file/class/method, the insecure pattern, the secure pattern applied, the evidence that the exploit is closed, the evidence that behavior is unchanged, the residual risk and any compensating control still needed.

Include the commands and their exit codes so a reader can re-derive every claim.
```

Residual risk is a required field, not a flourish. The export route has no
authentication in this fixture, so "traversal closed" and "this endpoint should
be authenticated and tenant-scoped at the edge" are two different statements and
the report has to make both.

---

<a id="part-9"></a>
## Part 9 — Run It Unattended: CI, Schedules, Automations

**On every pull request.** `.github/workflows/equivalence-gate.yml` runs
`eq-gate`, `eq-exploit-refactored` and `eq-tests` and uploads the reports as
artifacts. `eq-exploit-refactored` demands a closed exploit verdict, re-derived
from the running code, from every finding whose subject changed — a refactor
that deletes its attack coverage exits `3` instead of passing on the contract
cases alone. `eq-gate` picks
the stage per finding from the fingerprints: a subject that still matches the
recording is graded as the before-state, a changed subject is graded as a
refactor. A branch cannot choose the easier contract for itself.

**On a schedule.** A **scheduled Devin** replaces the quarterly "are the fixes
still in place" spreadsheet:

```
Every Monday at 07:00 UTC, run make eq-list and make eq-gate on Cognition-Partner-Workshops/otterworks and report which findings are still open, which have evidence recorded, and any finding whose evidence has gone stale. Do not change any code.
```

**On an event.** A [Devin Automation](https://docs.devin.ai/product-guides/automations)
starts the loop from the alert rather than the standup: when a SAST or DAST
finding lands on a webhook, launch `!secure-refactor-equivalence` scoped to that
finding, on its own branch, and let it arrive as a PR with the equivalence
evidence already attached. Because the verification is executable, the automation
proves its own fix before a human opens it.

---

<a id="part-10"></a>
## Part 10 — Confirm It in the Dashboards

| Where | What to open | What it proves |
|---|---|---|
| **GitHub Actions** → the PR's *Checks* tab | the `equivalence` job | the gate and the suite green on a clean runner, on the branch, rather than on someone's laptop |
| **Actions** → *Artifacts* | `equivalence-reports` | `grade-remediated.json`, `exploit.json`, `tests.json` — per-case status a ticket tracker or audit dashboard can ingest |
| **Actions** → the DAST workflow | `dast-report.md` from Part 7 | the runtime verdict per probe, with request and response evidence |
| **The PR** → *Files changed* and *Conversation* | the refactor diff, the preserved signatures, Devin Review's comments | how small a correct fix is, and the review loop around it |
| **Devin** → the parent session | the child-session table from Part 8 | each finding, its branch, its PR and its gate result in one view |

Open `grade-remediated.json` last. Every case is listed with its policy
(`contract` or `attack`), its recorded value and its observed value — so
"functional output unchanged" is a record you can re-derive case by case, not a
sentence someone wrote in a PR description.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **Characterize before you refactor.** The recording in Part 2 is what makes
   "behavior unchanged" a command instead of an opinion. Written after the change,
   it only documents whatever the change did.

2. **The gate caught what the diff and the suite could not.** Parameter binding
   closed a real SQL injection, the service's tests passed, and document search
   silently returned nothing for every partial term. That is the failure mode this
   loop exists to make impossible.

3. **Separate "must not change" from "must change".** Contract cases stay
   byte-identical; attack cases have to flip. One comparator, two policies —
   without the split the gate is either too strict to pass or too loose to notice
   a regression.

4. **The interface is part of the contract.** Signatures are captured with the
   behavior, so a refactor that renames a parameter fails as interface drift
   rather than surfacing at a caller later.

5. **Evidence that is never the only witness.** Recordings are fingerprinted
   against the source, seed, cases and emitter; re-recording needs an audited
   reason; `stale`, `missing` or `unmeasured` exits `2` instead of passing; and
   the exploit verdict is independently re-derived from the running code in CI.
   A red gate is a real divergence or a broken fixture — both get fixed at the
   root.

6. **Unit evidence, then runtime evidence.** Re-run the *original* probe against a
   rebuilt image. Skipping the rebuild is how a "remediated" verdict gets issued
   for code that is not running.

7. **Isolation makes the fan-out safe.** One child session per finding, each with
   its own VM, credentials and branch, each opening its own PR with the same
   evidence format — while the before-state stays untouched so the next run starts
   from the same place.

8. **The playbook makes every run the same.** `!secure-refactor-equivalence` is
   the procedure; the repo Skill supplies the findings, commands and fixture
   mechanics. The presenter, the children, the weekly sweep and the
   alert-triggered automation all execute the identical loop.
