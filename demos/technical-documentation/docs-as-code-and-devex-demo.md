# Docs-as-Code and Developer Experience — Polyglot Monorepo Demo

A single-thread demo showing Devin as the documentation agent for the OtterWorks
polyglot monorepo: it audits what the docs claim against what the code does,
regenerates the API reference from the source of truth, rewrites the onboarding
path and then proves it by following it on a clean machine, and gets wired to a
`main`-branch merge event so a docs PR opens by itself the next time code and
docs diverge. Devin Review closes the loop in both directions — reviewing the
docs PR for accuracy against the code, and flagging a human code PR that changes
behavior without touching the docs.

## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [Before, After, and What "Correct" Means](#before-after)
- [Part 1 — Audit the Drift on a System Devin Has Never Seen](#part-1)
- [Part 2 — Regenerate the API Reference from the Source of Truth](#part-2)
- [Part 3 — The Onboarding Path, Verified by Following It](#part-3)
- [Part 4 — Wire the Docs-Drift Agent to a Merge Event](#part-4)
- [Part 5 — Devin Review, Both Directions](#part-5)
- [Part 6 — Fan Out Across Services with Child Sessions](#part-6)
- [Part 7 — Changelog and Release Notes on a Schedule](#part-7)
- [The Shared Context Layer](#context-layer)
- [What Still Needs a Human](#human-in-the-loop)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Paste this into Devin to produce the drift audit the rest of the thread works
from:

```
Repo: Cognition-Partner-Workshops/otterworks (branch main).

Audit the repo's documentation against the code and write the result
to docs/DOCS-DRIFT-AUDIT.md.

Compare, at minimum:
1. The Services table in README.md against the directories under
   services/.
2. Directory paths cited in ARCHITECTURE.md against the directories
   that exist on main.
3. The gateway route claims in docs/api-route-matrix.md against the
   ServiceRoutes() map in
   services/api-gateway/internal/config/config.go.
4. The specs in shared/openapi/ against the services that expose HTTP
   routes through the gateway.
5. The runbooks in docs/runbooks/ against the services in
   docker-compose.yml.

Output format: one Markdown table with the columns Claim | Where the
claim lives (file:line) | What the code actually does (file:line) |
Severity (blocking / misleading / cosmetic), followed by a
"Recommended fixes" section ordered by severity. Cite a real file and
line for every row and do not list a finding you have not verified in
the source.
```

Prerequisites: read access to the otterworks repo. Nothing needs to be running
locally for Parts 1, 2, and 4–7; Part 3 uses Docker and `make` on Devin's VM.

---

<a id="repository"></a>
## Repository

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — a
  polyglot monorepo: 12 directories under `services/` (Go, Java, Rust, Python
  ×2, Node.js, Kotlin, Scala, Ruby, C#, plus a Java modular monolith) and two
  frontends under `frontend/`. Documentation on `main` today: `README.md`,
  `ARCHITECTURE.md`, `AGENTS.md`, seven runbooks in `docs/runbooks/`, three
  OpenAPI specs in `shared/openapi/`, and `docs/api-route-matrix.md`. Two of
  the twelve service directories carry a `README.md`
  (`services/analytics-service/`, `services/legacy-portal/`). There is no
  `CHANGELOG.md` and no docs-related workflow in `.github/workflows/`.

The repo is the right fixture for this demo precisely because its docs are
*mostly* good — the drift is the realistic kind that accumulates when code moves
faster than the people writing about it.

---

<a id="before-after"></a>
## Before, After, and What "Correct" Means

| | Documentation state on `main` | After the thread |
|---|---|---|
| **Service inventory** | `README.md` documents 11 backend services; `services/` holds 12 directories — `services/legacy-portal/` appears in neither `README.md` nor `ARCHITECTURE.md` | Each service directory present on `main` appears in the inventory with language, port, and dependencies |
| **Paths** | `ARCHITECTURE.md` describes the web frontend at `frontend/web-app/`; the directory on `main` is `frontend/client-app/` | Paths in prose resolve to directories that exist |
| **API reference** | `docs/api-route-matrix.md` records three "gateway prefix gaps" (`/api/v1/templates`, `/api/v1/folders`, `/api/v1/reports`) that `ServiceRoutes()` in `services/api-gateway/internal/config/config.go` now routes | A route reference generated from `ServiceRoutes()`, with the stale gap notes retired |
| **Per-service docs** | 2 of 12 service directories have a `README.md`; 3 of the gateway-fronted services have an OpenAPI spec in `shared/openapi/` | Service READMEs and specs to one template, produced by parallel child sessions |
| **Trigger** | Docs are updated when a human remembers | `.github/workflows/docs-drift-guard.yml` opens a docs PR on merges to `main` that touch code without touching docs |

"Correct" here is not "reads well." It is **the claim matches the code at a
cited file and line**, and the setup instructions execute on a clean machine.
That is the bar Devin is held to for the rest of the thread, and it is the bar
Devin Review enforces in Part 5.

---

<a id="part-1"></a>
## Part 1 — Audit the Drift on a System Devin Has Never Seen

Run the Quick Start prompt. Devin has no prior exposure to this codebase and no
tribal knowledge of it; it reads the repo, and where the repo has been indexed,
DeepWiki gives it a head start on the service topology (coverage depends on repo
structure).

What comes back in `docs/DOCS-DRIFT-AUDIT.md` typically includes:

| Claim | Where the claim lives | What the code does |
|---|---|---|
| 11 backend services | `README.md` Services table | `services/` holds 12 directories; `services/legacy-portal/` is undocumented outside its own README |
| Web frontend at `frontend/web-app/` | `ARCHITECTURE.md` § 11 | The directory on `main` is `frontend/client-app/` |
| "Gateway currently has no `/api/v1/templates` prefix in `ServiceRoutes`" | `docs/api-route-matrix.md` | `ServiceRoutes()` maps `/api/v1/templates` to the document service |
| "`/api/v1/folders` is not currently in `ServiceRoutes`" | `docs/api-route-matrix.md` | `ServiceRoutes()` maps `/api/v1/folders` to the file service |
| "Gateway config does not currently route `/api/v1/reports`" | `docs/api-route-matrix.md` | `ServiceRoutes()` maps `/api/v1/reports` to the report service |

Two things are worth pausing on. First, the last three rows are not typos — they
were **true when they were written**. The gateway grew routes and the matrix
did not, which is the exact failure mode this thread automates away. Second,
nobody on the team had to know where the routes were defined; the agent found
`ServiceRoutes()` by following the code.

Open the audit file in the session and check a row against its cited line before
moving on. An audit you cannot spot-check is not an audit.

---

<a id="part-2"></a>
## Part 2 — Regenerate the API Reference from the Source of Truth

The route matrix should not be maintained by hand at all. Generate it from the
code that actually does the routing.

```
Repo: Cognition-Partner-Workshops/otterworks.

Rewrite docs/api-route-matrix.md so the gateway prefix table is
generated from the ServiceRoutes() map in
services/api-gateway/internal/config/config.go rather than
maintained by hand.

Requirements:
- One row per prefix in ServiceRoutes(), in the order the map is read
  after sorting by prefix: prefix | backend service | source of the
  target URL (the Config field) | the service directory under
  services/.
- Include the non-/api/v1 prefixes (for example /socket.io) and note
  which service serves them.
- Delete the "Known route and behavior gaps to verify" entries that
  the current ServiceRoutes() map contradicts, and keep any that are
  still true, with a line reference for each.
- Add a header note stating that the table is derived from
  ServiceRoutes() and naming the file and function to re-derive it
  from.
- Do not change the "Critical user-flow routes" table's test-coverage
  column unless a referenced test file no longer exists under
  tests/api/.

Then extend shared/openapi/ with a spec for the auth service that
follows the structure of shared/openapi/document-service.yaml
(openapi 3.0.3, info/servers/paths, operationId and summary on every
operation). Derive the paths and payloads from the auth service source
under services/auth-service/, not from the route matrix, and note in
the spec description which source files each path group came from.
```

Two source-of-truth rules are doing the work here: the route table comes from
the router config, and the OpenAPI spec comes from the service's own handlers.
Devin never copies from one document into another — a document that quotes
another document is how drift propagates.

The commit lands as a PR. Leave it open; Part 5 reviews it.

---

<a id="part-3"></a>
## Part 3 — The Onboarding Path, Verified by Following It

A README is a claim about what happens when you follow it. Devin can test that
claim the only way it can honestly be tested — by running it on a machine that
has never seen the project.

```
Repo: Cognition-Partner-Workshops/otterworks.

Act as an engineer joining this team today with only repo access.
Follow README.md § "Quick Start (Local Development)" literally, in
order, on your VM. Do not use knowledge from elsewhere in the repo to
fill gaps, and do not skip a step because you can infer it.

Run: make infra-up, then make up, then check the service endpoints
the README claims are available.

Record, as you go:
- every command that failed, with the exact error;
- every step that required knowledge the README does not state
  (an env var, a tool version, a wait, a port already in use);
- how long each phase took.

Write the result to docs/ONBOARDING.md with these sections:
"Prerequisites verified on a clean machine", "Step-by-step, with the
gaps filled in", "Known failure modes and what they mean", and
"First useful change" — a walkthrough of making a trivial change to
one service and running that service's tests, using the per-service
commands in the Makefile (see the lint and test targets).

Note in "Known failure modes" that main carries deliberately planted
bugs per AGENTS.md, and identify which failures are planted rather
than environmental. Do not change application code.
```

This step is the clearest illustration of why the work belongs to a cloud agent
rather than an editor plugin: it needs a **clean machine** and roughly 12 GB of
RAM to bring the stack up, and it takes as long as it takes. An engineer's
laptop is already contaminated by their existing setup — that is precisely why
their README works for them and not for the new hire.

`AGENTS.md` tells Devin that `main` is a "golden app" carrying deliberately
planted bugs (the `services/admin-service/config/environments/production.rb`
logger bug, for one). A docs agent that "fixed" those would destroy other labs.
Repo-level agent instructions are what keep the agent inside the lines.

---

<a id="part-4"></a>
## Part 4 — Wire the Docs-Drift Agent to a Merge Event

Everything so far was a session someone started. Now remove the human from the
trigger. The repo already has an event-driven precedent to mirror:
`.github/workflows/sast-auto-remediate.yml` calls the Devin API when a scanner
finds something. The docs equivalent fires on merges to `main`.

```
Repo: Cognition-Partner-Workshops/otterworks.

Create .github/workflows/docs-drift-guard.yml, modeled on the
existing .github/workflows/sast-auto-remediate.yml (reuse its secret
names, its bot-author guard, and its jq-escaped Devin API call
pattern).

Trigger: push to main.

Job logic:
1. Compute the changed paths for the merge commit.
2. Classify them: code paths are services/**, frontend/**,
   infrastructure/**, platform/**, and .github/workflows/**; docs
   paths are *.md, docs/**, and shared/openapi/**.
3. Exit successfully when no code path changed, or when a code path
   changed and a docs path changed in the same merge.
4. When code changed with no docs change, call the Devin API with a
   prompt containing: the merge commit SHA, the PR number and title,
   the changed code paths, and instructions to check those paths
   against README.md, ARCHITECTURE.md, docs/api-route-matrix.md, the
   matching shared/openapi/ spec, and the matching docs/runbooks/
   entry, then push a docs branch named docs/drift-<short-sha> with
   only documentation changes.
5. Skip when the merge author is devin-ai-integration[bot], and use a
   concurrency group keyed on the SHA so a re-run does not create a
   second session.
6. Post the session URL as a comment on the merged PR.

Also write docs/DOCS_DRIFT_AUTOMATION.md documenting the trigger, the
payload fields sent to the API, the path classification rules, the
bot-loop guard, and how to opt a path out.
```

**The payload and what the agent does with it.** The trigger is a `push` to
`main`; the payload the workflow forwards is the merge SHA, the PR number and
title, and the list of changed code paths. Devin turns that list into a scope:
a change under `services/api-gateway/` sends it to the route matrix, a change
under `services/document-service/app/api/` sends it to
`shared/openapi/document-service.yaml`, a change to `docker-compose.yml` or a service's startup
path sends it to the matching runbook in `docs/runbooks/`. It reads the diff,
decides whether a documented claim is now false, and either pushes a docs branch
or exits without one. No human typed anything.

Prove it with a real merge. Change a routed prefix and leave the docs alone:

```bash
cd otterworks
git checkout -b workshop-docs-drift main
# add a prefix to ServiceRoutes() in
# services/api-gateway/internal/config/config.go
git commit -am "feat(gateway): route a new prefix"
git push origin workshop-docs-drift
```

Merge the PR. The workflow classifies `services/api-gateway/**` as a code change
with no accompanying docs change, calls the API, and a session appears in the
Devin dashboard with the merge payload in its prompt. The docs branch shows up a
few minutes later, and the merged PR carries a comment linking the session.

The distinction that matters to a docs team: this runs at 2am on a merge made by
someone who has never met the technical writer. In most cases the drift is
caught in the same hour it is introduced, instead of at the next docs audit.

---

<a id="part-5"></a>
## Part 5 — Devin Review, Both Directions

Docs PRs are reviewed badly by humans, because checking a sentence against the
code it describes means opening the code. Devin Review does exactly that.

**Direction 1 — Devin reviews the docs PR from Part 2.** Open the PR from Part
2 and read the review comments. The useful ones are accuracy findings, not
style: a route row whose backend field disagrees with the `Config` field it
cites, an `operationId` in the new spec that no handler implements, a retired
"gap" note that is in fact still true. Fix what the review raises by pushing to
the same branch and let the review re-run — the loop closes on Devin's own work,
which is what makes an unattended docs PR safe to merge.

**Direction 2 — Devin reviews a human's code PR.** Open a PR that changes
documented behavior and deliberately leaves the documentation alone:

```bash
cd otterworks
git checkout -b workshop-review-drift main
# change a route prefix in ServiceRoutes() in
# services/api-gateway/internal/config/config.go
git commit -am "refactor(gateway): rename a route prefix"
git push origin workshop-review-drift
```

The review comments on the PR that a prefix documented in
`docs/api-route-matrix.md` no longer matches `ServiceRoutes()`, and names the
file to update. This is the cheap half of the loop: catching the divergence at
review time costs one comment, while catching it after merge costs a session, a
branch, and a second review.

Point the two directions at each other. Review flags drift on the way in
(Direction 2); the merge trigger from Part 4 catches what slips past (Direction
1 on the resulting docs PR). Neither one alone closes the gap.

---

<a id="part-6"></a>
## Part 6 — Fan Out Across Services with Child Sessions

Ten of the twelve directories under `services/` have no `README.md`. Writing
them in series is a week of a technical writer's life, and the tenth would be
better than the first — which is its own problem, because the point is that they
are the same.

```
Repo: Cognition-Partner-Workshops/otterworks.

Act as the orchestrator for a service-documentation sweep using child
Devin sessions.

First, write docs/SERVICE_README_TEMPLATE.md with these sections:
Purpose, Language and framework (with versions), Port, Public routes
(gateway prefix and direct), Configuration (environment variables and
where they are read), Dependencies (datastores, queues, other
services), Local development (build, run, test commands from the
Makefile), and Troubleshooting (link the matching docs/runbooks/ entry
where one exists). Follow the heading style of the existing
services/analytics-service/README.md so the sweep matches what the
repo already does.

Then spawn one child session per service directory under services/
that has no README.md today. Give each child: its single service
directory, the template, and instructions to derive every value from
that service's source — its Dockerfile, its manifest (go.mod,
build.gradle, pom.xml, Cargo.toml, pyproject.toml, requirements.txt,
package.json, build.gradle.kts, build.sbt, Gemfile, or .csproj), its
config loader, and its entry in docker-compose.yml. A child that cannot verify a field leaves it as
"unverified" rather than guessing. Each child works only in its own
service directory and pushes its own branch.

When the children finish, aggregate: write docs/README.md as an index
of the docs/ tree and the per-service READMEs, and report which
services had unverified fields and why.
```

Twelve services, eight-plus languages, one template, one wave. A child session
reading `Cargo.toml` for the Rust file service and one reading `build.sbt` for
the Scala analytics service produce the same document shape from completely
different ecosystems — parallelism gives the team consistency, not just speed.

The aggregation step is the part that cannot be delegated to twelve people
working independently: one session sees all twelve results and reports where the
sweep came up short.

---

<a id="part-7"></a>
## Part 7 — Changelog and Release Notes on a Schedule

The repo has no `CHANGELOG.md`. Generate one from history, then keep it current
on a cadence rather than by remembering.

```
Repo: Cognition-Partner-Workshops/otterworks.

Create CHANGELOG.md in Keep a Changelog format (Added / Changed /
Fixed / Removed) covering the last 90 days of merges to main.

Source of truth is the git history and the merged PR titles and
bodies — not existing documentation. For each entry give the change,
the affected service directory under services/ (or frontend/,
infrastructure/, platform/), and the PR number. Group entries by
service, then by category, most recent first. Omit merges that touch
only docs or CI config, and mark anything that changes a documented
route, an environment variable, or a Helm value as
"[operator-impacting]".

Also write docs/RELEASE_NOTES_PROCESS.md describing how the file is
regenerated, which sources are authoritative, and which entries need
a human decision before publication.
```

Then put it on a timer with a scheduled Devin session — weekly, same prompt
scoped to the last seven days, appending to the `Unreleased` section. Combined
with the merge trigger from Part 4, the docs surface has two clocks: an event
clock for correctness and a calendar clock for narrative.

---

<a id="context-layer"></a>
## The Shared Context Layer

Nothing above is generic documentation advice — the output is specific to this
org because the agent reads the org's context:

- **Repo-level agent instructions.** `AGENTS.md` in the otterworks repo is what
  told Devin in Part 3 that a crash-looping `admin-service` is a planted bug to
  document, not a bug to fix.
- **DeepWiki.** Repository indexing is why Part 1 could map twelve services in
  one session without a human explaining the topology (coverage depends on repo
  structure).
- **Knowledge notes.** House style, banned terms, the audience for each doc
  surface, and "the route matrix is derived, never hand-edited" belong in
  Knowledge so a session started by a webhook at 2am inherits them.
- **Playbooks.** Wrap Parts 1 and 2 as a `!docs-drift-sweep` playbook and the
  Part 4 workflow prompt calls it by name — each triggered session then follows
  the same procedure, and improving the playbook improves runs that have not
  happened yet. The otterworks repo already carries an executable Skill at
  `.agents/skills/synthetic-testdata-generation/SKILL.md`, which is the pattern
  for keeping repo-specific mechanics in the repo.
- **MCP integrations.** Where the org has connected a wiki or ticketing MCP
  server, the same session that pushes the docs branch can mirror the published
  page and close the docs ticket. Verify which MCP servers your org has
  connected before promising this in front of an audience.

The compounding effect is the point: each run of the drift agent that finds a
new class of divergence is a candidate line in the Knowledge note or the
playbook, so the next run starts smarter.

---

<a id="human-in-the-loop"></a>
## What Still Needs a Human

- **Merge approval.** Devin pushes docs branches and opens PRs; a person still
  approves and merges. For a public-facing docs site, that gate is the point.
- **Intent and audience.** The agent can prove that a claim matches the code. It
  cannot decide that a concept page is aimed at integrators rather than internal
  operators, or that a feature should not be documented yet because it is
  unreleased.
- **Deprecation and support commitments.** Language that creates an obligation —
  support windows, breaking-change notices, migration deadlines — is a product
  decision.
- **Anything requiring credentials the agent does not hold.** Verifying an
  onboarding path against a production or customer environment needs access a
  docs agent should not have. Part 3 works because the stack runs locally on the
  agent's VM.
- **Judging whether the code is wrong instead of the docs.** When a documented
  contract and the implementation disagree, sometimes the implementation is the
  defect. Devin surfaces the divergence and cites both sides; deciding which one
  moves is an engineering call.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Accuracy is verifiable, so it can be automated.** Each finding in this
  thread is "this claim at `file:line` disagrees with this code at `file:line`."
  That is a check a machine can run continuously and a reviewer can spot-check
  in seconds — unlike "is this well written."
- **The trigger is the product.** A merge to `main` that touches code without
  touching docs is an unambiguous signal, and
  `.github/workflows/docs-drift-guard.yml` turns it into a docs PR without
  anyone noticing the drift first. Docs stop depending on someone remembering.
- **This is not laptop work.** Bringing up a twelve-service stack to verify an
  onboarding path, sweeping every service directory in parallel, and responding
  to a merge at 2am are all things an in-editor assistant structurally cannot
  do.
- **Parallelism buys consistency.** One template, one wave of child sessions,
  one aggregated index — twelve services documented to the same standard across
  eight-plus languages, which is harder to achieve with twelve people than with
  twelve sessions.
- **Review runs in both directions.** Devin Review flags a human's code PR that
  changes documented behavior, and reviews Devin's own docs PR against the code
  it describes. The second half is what makes an unattended docs PR safe.
- **The team outcome is onboarding time and deflection.** Before: a new engineer
  reconstructs the setup path by asking, and the routing questions land in the
  platform team's channel because the route matrix is stale. After: a verified
  onboarding doc, a route reference derived from `ServiceRoutes()`, per-service
  READMEs, and a changelog that says what changed and who it affects. Track it
  as time-to-first-merged-PR for new hires and the volume of "is this endpoint
  real?" questions — both are measurable before and after, which is the case a
  docs lead can take to their manager.
