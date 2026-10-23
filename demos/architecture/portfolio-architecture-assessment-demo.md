# Portfolio Architecture Assessment — Current State to Governed Target State

A single-thread demo for software architects and staff+ engineers: Devin builds
an accurate current-state map of a polyglot system it has never seen, fans out a
portfolio-scale assessment across three architecturally distinct codebases with
one child session per repo, aggregates a ranked and costed remediation plan,
lands the target-state decision as an ADR in the repo, proves the first seam
with a proof-of-concept PR, and then holds the line — Devin Review flags PRs
that violate the documented ADR, and a CI trigger turns layering violations
into hands-free conformance sessions. Architecture decisions get made with
evidence instead of opinion, and the guardrails hold without a review board.

<a id="toc"></a>
## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Part 1 — Current-State Map of a System Devin Has Never Seen](#part-1)
- [Part 2 — Portfolio Fan-Out: One Child Session per Repo, One Rubric](#part-2)
- [Part 3 — The Aggregated Remediation Plan](#part-3)
- [Part 4 — Land the Decision as an ADR](#part-4)
- [Part 5 — Prove the Seam: Proof-of-Concept PR](#part-5)
- [Part 6 — Devin Review as the Architecture-Conformance Reviewer](#part-6)
- [Part 7 — Event-Driven Conformance: The Guardrail That Runs Itself](#part-7)
- [What Still Needs a Human](#human-in-the-loop)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Paste this into Devin to start the thread — a current-state architecture map of
the OtterWorks platform, verified against the code rather than the docs:

```
Build a current-state architecture assessment of the
Cognition-Partner-Workshops/otterworks repo. Do not trust the
existing docs — derive the map from the code and then diff it
against ARCHITECTURE.md and docs/api-route-matrix.md, calling out
any drift you find.

Cover: every service under services/ (language, framework,
runtime version, datastore), the two frontends under frontend/,
how traffic flows through services/api-gateway (which backends
its config in services/api-gateway/internal/config/config.go
routes to), the event fan-out between services, and the three
Terraform roots (infrastructure/terraform, platform/terraform,
demo-platform/infra/terraform).

Write the result to docs/architecture/current-state.md with: a
service inventory table, a mermaid dependency diagram, a "docs
drift" section listing claims in ARCHITECTURE.md that no longer
match the code, and a tech-debt hotspot list ranked by blast
radius.
```

---

<a id="repositories"></a>
## Repositories

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) —
  polyglot platform with 12 services under `services/` spanning 10 languages
  (Go, Java, Rust, Python, TypeScript, Kotlin, Scala, Ruby, C#, plus a Python
  Flask variant), 2 frontends under `frontend/` (React/Next.js client, Angular
  17 admin dashboard), and three Terraform roots. Includes two deliberate
  legacy components: `services/report-service` (Java 8 / Spring Boot 2.5, with
  an `UPGRADE_GUIDE.md` documenting 11 upgrade axes) and
  `services/legacy-portal` (a Java 11 / Spring Boot 2.7 modular monolith with
  three bounded contexts, documented in its README as the decomposition
  "before" state). Has an `ARCHITECTURE.md` but **no ADRs** —
  `docs/SDLC-COVERAGE.md` names the missing `docs/adr/` directory as a known
  gap.
- [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices) —
  Java 17 / Spring Boot microservices: 8 Maven modules (API gateway, config
  server, Eureka discovery server, admin server, customers/vets/visits
  services, and a Spring AI GenAI service). Docs contain diagrams but no ADRs.
- [modular-monolith-ddd](https://github.com/Cognition-Partner-Workshops/modular-monolith-ddd) —
  .NET 8 modular monolith with 5 modules under `src/Modules/` (Administration,
  Meetings, Payments, Registrations, UserAccess), CQRS, architecture tests
  (NetArchTest), and — unique in this portfolio — a real ADR log:
  `docs/architecture-decision-log/` with 17 numbered records.

Three repos, three architectural styles (polyglot mesh, JVM microservices,
DDD modular monolith), and exactly one of them has written down *why* it is
shaped the way it is. That asymmetry is the demo.

---

<a id="part-1"></a>
## Part 1 — Current-State Map of a System Devin Has Never Seen

Run the Quick Start prompt above. This is the work that cannot happen inside
one engineer's IDE: mapping 12 services in 10 languages means reading Go
routing config, Rust and Scala build files, Rails and Ktor conventions, and
three Terraform roots in one pass — a multi-day whiteboard exercise for a
human architect, and one that goes stale the week after it's drawn.

While the session runs, open the repo's DeepWiki and ask orientation questions
alongside it — "which services publish to SNS?", "what does the API gateway
proxy and what bypasses it?". DeepWiki typically gives an accurate structural
answer in seconds (coverage depends on repo structure), which is how an
architect validates the agent's map instead of taking it on faith.

The deliverable to inspect: `docs/architecture/current-state.md` on Devin's
branch. The section that matters most is **docs drift** — the diff between
what `ARCHITECTURE.md` claims and what the code does. In most cases Devin
finds at least a handful of drift items in a repo this size; each one is a
place where the team's shared mental model is wrong. An architecture map
derived from code, with drift called out, is the evidence baseline the rest of
the thread builds on.

---

<a id="part-2"></a>
## Part 2 — Portfolio Fan-Out: One Child Session per Repo, One Rubric

A staff+ engineer's scope is a portfolio, not a repo. The same assessment now
runs across all three codebases in parallel — one child session per repo, each
scoring against a common rubric, with the parent session aggregating.

```
Act as the orchestrator for a portfolio architecture assessment
across three repos in the Cognition-Partner-Workshops org:
otterworks, petclinic-microservices, and modular-monolith-ddd.

Spawn one child Devin session per repo. Each child scores its
repo against this rubric and writes ASSESSMENT.md at the repo
root of a working branch:

1. Decision records — do ADRs or an equivalent decision log
   exist? Are they current? (modular-monolith-ddd has
   docs/architecture-decision-log/; check whether its records
   still match the code, e.g. module counts.)
2. Layering and coupling — are module/service boundaries
   enforced by anything (architecture tests, build rules), or
   only by convention?
3. Runtime currency — language/framework versions vs. current
   LTS, per service or module.
4. Blast radius — which components, if changed, force changes
   elsewhere? Derive from imports, shared schemas, and shared
   infra.
5. Operational surface — health checks, observability hooks,
   deploy path.

Each finding gets a severity (high/medium/low) and a T-shirt
remediation estimate (S/M/L) with one sentence of justification.

After all children complete, aggregate into a single
PORTFOLIO-ASSESSMENT.md in the otterworks repo under docs/:
a cross-repo scorecard table, the top 10 findings ranked by
severity-times-blast-radius, and a costed remediation sequence
(what to fix first and why, with the T-shirt sizes rolled up).
```

Each child inherits the org's shared context — knowledge notes about repo
conventions, the repos' `AGENTS.md` files, and DeepWiki indexes — so three
children produce three comparable documents rather than three house styles.
This is the difference between an agent as a team resource and an assistant in
one person's editor: the rubric runs identically N times, unattended, and the
results are commensurable.

---

<a id="part-3"></a>
## Part 3 — The Aggregated Remediation Plan

When the children report back, open `docs/PORTFOLIO-ASSESSMENT.md` on the
parent's branch. What to look for, per repo:

| Repo | Expected headline findings |
|---|---|
| `otterworks` | No ADRs (named as a gap in its own `docs/SDLC-COVERAGE.md`); `report-service` on Java 8 / Spring Boot 2.5 (its `UPGRADE_GUIDE.md` documents 11 upgrade axes); `legacy-portal` bundling three bounded contexts in one deployable; boundary enforcement by convention only |
| `petclinic-microservices` | No decision log at all; single-language stack scores well on currency; coupling concentrated in the config/discovery pair |
| `modular-monolith-ddd` | Has 17 ADRs *and* NetArchTest architecture tests — the portfolio's reference point — but typically with drift to flag: ADR 0004 says "divide the system into 4 modules" while `src/Modules/` now contains 5 |

That last row is the point worth dwelling on: even the best-governed repo in
the portfolio has a decision record that no longer matches the code. The
assessment doesn't just find missing governance — it finds *stale*
governance, which is harder to spot by eye and more corrosive, because the
team believes it's covered.

The ranked remediation sequence gives the architect what a consulting
engagement would cost weeks to produce: an evidence-backed, costed answer to
"what do we fix first?". The top item, in most cases: OtterWorks has the
largest blast radius and no decision log — so that's where the thread goes
next.

---

<a id="part-4"></a>
## Part 4 — Land the Decision as an ADR

Target-state proposals that live in slide decks don't govern anything. The
decision lands in the repo, in the format the portfolio's reference repo
already proved out.

```
In the Cognition-Partner-Workshops/otterworks repo, create the
docs/adr/ directory that ARCHITECTURE.md already references and
docs/SDLC-COVERAGE.md names as a gap. Use the MADR template, and
follow the numbering convention of
modular-monolith-ddd/docs/architecture-decision-log/ (0001-...,
0002-...).

Write two records:

1. docs/adr/0001-record-architecture-decisions.md — adopt ADRs,
   the template, and the rule that structural changes reference
   an ADR.

2. docs/adr/0002-decompose-legacy-portal-by-bounded-context.md —
   the target-state decision for services/legacy-portal: extract
   its three bounded contexts (announcements, user preferences,
   feedback — packages under
   com.otterworks.legacyportal.*, each with its own database
   schema, per the service README) into standalone services via
   the strangler pattern, starting with announcements. Context:
   cite the portfolio assessment findings. Decision: one context
   per service, no shared schemas, all cross-context calls go
   through service APIs. Consequences: include the layering rule
   that no code under com.otterworks.legacyportal.<context> may
   import from a sibling context package.

Also write docs/adr/README.md indexing the records and stating
the template and numbering convention.
```

Review the PR when it lands. The ADR is short, cites evidence from Parts 1–3,
and states a checkable rule ("no cross-context imports") rather than an
aspiration. Then capture the rule where future sessions inherit it: add a
knowledge note to the org —

> *When reviewing or modifying `services/legacy-portal` in otterworks, enforce
> `docs/adr/0002`: no imports across `com.otterworks.legacyportal.<context>`
> package boundaries, no cross-context foreign keys or shared tables.*

This is the shared context layer doing governance work: the rule now applies
to sessions and reviews run by anyone in the org, not just the person who
wrote the ADR.

---

<a id="part-5"></a>
## Part 5 — Prove the Seam: Proof-of-Concept PR

An ADR that has never touched code is still an opinion. The next session
proves the decision's first step is viable at exactly one seam — small enough
to review in minutes, real enough to build and test.

```
In Cognition-Partner-Workshops/otterworks, implement the first
strangler step of docs/adr/0002: prove the announcements bounded
context of services/legacy-portal can stand alone.

Scope — seam-level only, not a full extraction:

1. Create services/announcements-service as a minimal Spring
   Boot 3 / Java 17 service exposing the same announcements API
   the portal serves today (GET/POST /api/announcements,
   GET /api/announcements/{id},
   POST /api/announcements/{id}/publish), backed by the existing
   announcements schema. Reuse the domain code from
   com.otterworks.legacyportal.announcements, adapted to the new
   module.
2. Add contract tests that assert the new service's responses
   match the legacy portal's announcements endpoints
   shape-for-shape.
3. Add an ADR-conformance check: a test or build step in
   legacy-portal that fails if any class under one
   com.otterworks.legacyportal.<context> package imports from a
   sibling context package (mirroring how modular-monolith-ddd
   enforces module boundaries with NetArchTest).
4. Update docs/adr/0002 status notes with what the PoC proved
   and what the full extraction still needs.

Run the legacy-portal test suite (./mvnw test in
services/legacy-portal) and the new service's tests; both must
be green.
```

The PR that comes back is the viability evidence: the context really does
stand alone (the README's claim about clean schema separation held up), the
contract tests pin the behavior, and — the piece with lasting value — the
boundary rule from the ADR is now *executable*. Item 3 is the same move that
makes `modular-monolith-ddd` the portfolio's governance reference: the rule
lives in the build, not in a wiki.

---

<a id="part-6"></a>
## Part 6 — Devin Review as the Architecture-Conformance Reviewer

For this audience, the highest-leverage use of automated review is not style
nits — it's conformance to documented decisions. Two review loops close here.

**Loop 1 — Devin reviews the human's PR.** Open a small PR that violates the
ADR deliberately:

```bash
cd otterworks
git checkout -b workshop-adr-violation main
# In services/legacy-portal, add an import from
# com.otterworks.legacyportal.feedback into a class under
# com.otterworks.legacyportal.announcements (e.g., call
# the feedback average-rating logic from an announcements
# service class), commit, and push.
git push origin workshop-adr-violation
```

Open the PR against `main`. Devin Review picks it up automatically and, with
the ADR in the repo and the knowledge note from Part 4 in the org's context,
typically flags the cross-context import as a violation of `docs/adr/0002` —
citing the record, not a vague "consider decoupling". If the Part 5
conformance test merged first, CI fails on the same PR for the same reason:
the decision now has two independent enforcement points, neither of which is
a meeting.

**Loop 2 — the review loop closes on Devin's own PR.** Back on the Part 5
PoC PR, leave a review comment as the architect:

> The contract tests cover response shape but not error cases — add a test
> asserting the new service returns 404 for an unknown announcement id, same
> as the portal.

Devin picks up the PR comment, adds the test, and pushes — the same feedback
loop a human teammate follows. Review is symmetric: Devin reviews human PRs
against the ADR, and humans review Devin's PRs, with follow-up handled by the
agent rather than by whoever has spare cycles that afternoon.

---

<a id="part-7"></a>
## Part 7 — Event-Driven Conformance: The Guardrail That Runs Itself

Guardrails that depend on a human remembering to check don't hold. The last
step wires conformance to an event, following the trigger pattern the repo
already uses for security (`docs/EVENT_DRIVEN_SECURITY.md` and the
`sast-auto-remediate.yml` workflow).

```
Create a GitHub Actions workflow adr-conformance.yml in the
otterworks repo, modeled on the existing
.github/workflows/sast-auto-remediate.yml pattern:

1. Trigger: pull_request (opened, synchronize) where changed
   files match services/legacy-portal/** or docs/adr/**.
2. Skip PRs authored by devin-ai-integration[bot] (bot-loop
   prevention, same author check the SAST workflow uses).
3. Run the cross-context import check from the legacy-portal
   build. If it fails, post a PR comment naming the violated ADR
   and the offending imports.
4. On violation, call the Devin v3 API to create a session with
   this payload: the PR number, the head branch, the list of
   violating files, and instructions to either refactor the
   change to respect docs/adr/0002 on the same branch, or — if
   the change genuinely needs a cross-context call — to draft a
   superseding ADR under docs/adr/ and say so in a PR comment.
5. Cap at one remediation attempt per PR (comment-based guard,
   same as the SonarCloud path in the SAST workflow), then label
   the PR needs-architect-review.

Document the trigger, payload, and escalation policy in
docs/adr/README.md under an "Enforcement" section.
```

The trigger payload is the PR event; what the agent does with it is
hands-free: check, comment, fix or escalate. Re-open the Part 6 violation PR
(or push a new commit to it) and watch the workflow fire — the comment names
the ADR, the Devin session link appears, and either a conforming refactor or
a superseding-ADR draft comes back. This is architecture governance with the
review board replaced by a decision log, a build rule, and an agent on the
PR event — the humans only see the cases that genuinely need judgment.

The same pattern extends to drift detection: a scheduled Devin session (via
Devin Automations) can re-run the Part 1 current-state map monthly and diff
it against `docs/architecture/current-state.md`, so the map that opened this
demo keeps pace with the code instead of drifting the way `ARCHITECTURE.md` did.

**Before / after, in the terms an architecture team is measured on:**

| | Before | After |
|---|---|---|
| Current-state map | Whiteboard photo, stale in weeks | Derived from code, drift-checked, regenerated on a schedule |
| Decision records | None (otterworks), or stale (ADR 0004 vs. 5 modules) | ADR log with a checkable rule per structural decision |
| Enforcement | Architecture review board meeting, when someone remembers | Build rule + Devin Review + PR-event trigger, on typical PRs |
| Assessment cost | Weeks of consulting per repo | One rubric, one child session per repo, hours |
| Tech-debt plan | Opinion-ranked backlog | Severity-times-blast-radius ranked, T-shirt costed, evidence-cited |

---

<a id="human-in-the-loop"></a>
## What Still Needs a Human

Honest boundaries for this job function:

- **The decision itself.** Devin drafts the ADR and assembles the evidence;
  accepting the trade-offs (one context per service, strangler over rewrite)
  is the architect's call and their accountability. An agent-authored ADR
  nobody signed is worse than no ADR.
- **Rubric design.** The fan-out is only as good as the rubric. Choosing what
  the portfolio is scored on — and what severity means for this organization —
  is judgment work.
- **Exception adjudication.** The conformance workflow escalates PRs that
  claim a legitimate need to cross a boundary. Deciding whether to grant the
  exception or supersede the ADR is a human call; the automation's job is to
  make that call explicit instead of silent.
- **Cost sanity-checking.** T-shirt estimates from the assessment are
  starting points. Staffing, sequencing against product roadmaps, and
  production cutover risk live outside the repo and outside the agent's view.
- **Production access.** The PoC proves the seam in code and tests. Actual
  traffic migration, data backfill, and decommissioning the portal's
  announcements routes need production change management that no PR encodes.

Coverage caveats apply throughout: DeepWiki and current-state maps are
typically accurate but depend on repo structure; conformance checks catch the
rule they encode, not architectural erosion in general.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **Decisions with evidence instead of opinion.** The remediation plan is
   ranked by severity-times-blast-radius derived from code, and the ADR cites
   it. The architect argues from an assessment, not from seniority.
2. **Portfolio scale is the native unit.** One rubric, one child session per
   repo, comparable outputs aggregated by a parent — work that has no
   IDE-assistant equivalent because it spans repos, languages, and hours of
   unattended execution.
3. **The best-governed repo still drifted.** The portfolio's reference repo
   carries 17 ADRs and architecture tests, and its ADR 0004 still disagrees
   with its module count. Assessment finds stale governance, not just missing
   governance.
4. **ADRs become executable.** The layering rule moves from the decision
   record into a build check and a knowledge note, so it binds future
   sessions and future reviews — in most cases without anyone re-reading the
   ADR.
5. **Devin Review is the conformance reviewer.** It flags human PRs that
   violate a documented ADR, and the same review loop closes on Devin's own
   PRs — symmetric review, with follow-up handled by the agent.
6. **Guardrails hold without a review board.** A PR-event trigger, a
   one-attempt remediation guard, and a needs-architect-review escalation
   path mean humans only adjudicate genuine exceptions.
7. **Proof over proposal.** The seam-level PoC PR — one bounded context stood
   up behind contract tests — is what turns a target-state document into a
   plan the team believes.
