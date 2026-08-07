# Test Coverage and Flaky-Test Triage — Quality Engineering Demo

Cal.com runs its Playwright suite across an eight-shard matrix and retries
failures twice in CI. That keeps the pipeline moving, but a test that passes on
its second attempt can disappear into a green check. This demo makes that
signal legible, closes gaps in the flows that matter, and puts a nightly agent
on the flake backlog.

<a id="toc"></a>
## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before and After](#before-after)
- [Part 1 — The Baseline](#part-1)
- [Part 2 — Fan Out Coverage Gaps](#part-2)
- [Part 3 — Nightly Flaky Triage](#part-3)
- [Part 4 — Devin Review](#part-4)
- [Part 5 — The Quality Gate](#part-5)
- [Part 6 — Shared Context](#part-6)
- [What Still Needs a Human](#what-still-needs-a-human)
- [Key Takeaways](#key-takeaways)

<a id="quick-start"></a>
## Quick Start

Run the existing Cal.com commands from the repository root:

```bash
yarn test
yarn e2e
yarn test-playwright
```

`yarn test` expands to `TZ=UTC vitest run`. `yarn e2e` runs the
`@calcom/web` Playwright project, while `yarn test-playwright` runs Playwright
with `playwright.config.ts`. The repository is a Yarn 4 / Turbo monorepo.
Install the repository dependencies before running these commands. E2E runs
need the seeded database described by the repository's E2E instructions.

The secondary repository uses Java and Maven. Its Maven wrapper is
`petclinic-rest-api/mvnw`; its API collections live under
`petclinic-rest-api/src/test/postman/`, and its JMeter plan is
`petclinic-rest-api/src/test/jmeter/petclinic-jmeter-crud-benchmark.jmx`.

Start with Part 1. Paste the baseline prompt into Devin and let the agent
produce the first quality artifact before changing a test.

<a id="repositories"></a>
## Repositories

- **calcom** — the primary TypeScript/JavaScript scheduling application and
  Yarn 4 / Turbo monorepo. Its Playwright configuration is
  `playwright.config.ts`. The `@calcom/web` project reads
  `apps/web/playwright` and contains 80 E2E spec files. The
  `@calcom/app-store` project reads `packages/app-store/` and contains 2
  E2E spec files. The `@calcom/embed-core` project reads
  `packages/embeds/embed-core/` and contains 6 E2E spec files. The
  `@calcom/embed-react` project reads `packages/embeds/embed-react/` and
  contains 1 E2E spec file. Those project trees contain 94 unique
  `*.e2e.ts`/`*.e2e.tsx` files in the checked-out `main` snapshot.
  Playwright sets `retries` to 2 in CI and 0 locally, retains traces on
  failure, and limits headless runs to 10 failures.

- **petclinic-rest-api** — the secondary Java Spring REST API repository.
  Its Maven configuration is `pom.xml`; JaCoCo has `prepare-agent`,
  `report`, and `check` executions, excluding generated
  `rest/dto` and `rest/api` packages. It has 21 Java files below
  `src/test/java`, plus Postman assets under `src/test/postman/` and the
  JMeter plan at `src/test/jmeter/petclinic-jmeter-crud-benchmark.jmx`.
  There are no GitHub Actions workflows in this repository today. One child
  session uses this repository to establish a coverage baseline beside the
  Cal.com work.

Cal.com also provides repository-specific guidance in `AGENTS.md`,
`agents/commands.md`, `agents/coding-standards.md`, and
`agents/knowledge-base.md`. The baseline uses those files as orientation
material rather than assuming a generic JavaScript test standard.

<a id="before-after"></a>
## Before and After

| Quality signal | Before | After |
|---|---|---|
| Escaped-defect rate | A pass on retry is hidden inside a green CI result. | The baseline and nightly digest expose pass-on-retry tests so the team can connect flakes to escaped defects. |
| Shard wall time | Eight E2E jobs run, but runtime is not tied to a per-flow quality view. | The baseline records shard wall time and maps slow or unstable flows to owners. |
| Critical-flow coverage | Booking, login/auth, and event-type coverage is scattered across specs. | A gap report identifies paths that matter and adds tests with failure evidence. |
| Quarantine debt | `test.skip` and `test.fixme` entries can carry TODOs without an owner or burn-down date. | `FLAKY_REGISTRY.md` records owner, first-seen date, trace link, and disposition. |
| CI quality gate | CI proves that the current suite passed. | A reusable gate checks whether changed product behavior has a corresponding test change and whether quarantine records are owned. |

This is team capacity, not a solo debugging exercise. The nightly run continues
regardless of who is on call, while the registry gives a QE manager an owned
backlog that can be burned down.

<a id="part-1"></a>
## Part 1 — The Baseline the Agent Produces Itself

An IDE assistant can help with one test while an engineer is present. A cloud
agent can rerun an eight-shard Playwright suite with repeat-each for hours,
overnight, across the monorepo. That capacity is the reason this loop starts
with an unattended baseline rather than an editor-side inspection.

Paste this prompt into Devin:

```
In the calcom repository, produce a test-quality baseline in
TEST_QUALITY_BASELINE.md. Do not invent counts: derive them from
playwright.config.ts and the files below.

Run the repository's existing commands where the environment permits:
`yarn test`, `yarn e2e`, and `yarn test-playwright`. Record the
command, exit status, duration, and any environment blocker.

Inventory the Playwright projects in playwright.config.ts:
- apps/web/playwright: 80 E2E spec files
- packages/app-store/: 2 E2E spec files
- packages/embeds/embed-core/: 6 E2E spec files
- packages/embeds/embed-react/: 1 E2E spec file
Record that .github/workflows/e2e.yml runs the web suite in
an eight-shard matrix and uploads blob-report-N artifacts.

Report per-project spec counts and the observed wall time for
each shard. Inventory test.skip and test.fixme in the Playwright
trees, preserving the TODO text and the file path. Include the
known TODOs in apps/web/playwright/profile.e2e.ts,
apps/web/playwright/login.e2e.ts, and
apps/web/playwright/event-types.e2e.ts.

Map specs to booking, login/auth, and event-type flows. Identify
flows with no direct spec evidence instead of treating nearby
tests as coverage. Review vitest.config.mts and report how V8
coverage can be collected for packages that carry booking logic.

Also flag test anti-patterns: specs with no assertions, assertions
on implementation detail, and unconditional waits. For each
finding record the file path, test title or enclosing block, and
why it weakens escaped-defect signal or increases runtime.

Write the artifact with these sections:
1. Run inventory
2. Playwright projects and shard wall time
3. Skip/fixme and TODO inventory
4. Critical-flow map
5. Vitest V8 coverage plan
6. Test-quality findings
7. Recommended next actions

End with a short summary of escaped-defect risk, shard runtime,
critical-flow coverage, and quarantine debt. Do not change
application code or test files in this step.
```

`TEST_QUALITY_BASELINE.md` comes back with the configured project counts, shard
wall times, pass-on-retry results, and a skip/fixme inventory with its TODO
text. Read the retry signal first, then the skipped-test paths: together they
show where a green suite may be hiding unstable or deferred coverage.

<a id="part-2"></a>
## Part 2 — Fan Out Coverage Gaps with Child Sessions

Use the baseline to divide the next wave into five bounded areas, with one
child session per area:

1. `apps/web/playwright` booking flows
2. `apps/web/playwright` event-types flows
3. `apps/web/playwright` login/auth flows
4. `packages/embeds/embed-core`
5. `petclinic-rest-api` JaCoCo gaps, excluding generated `rest/dto` and
   `rest/api` packages

Paste this prompt into the parent Devin session:

```
Act as the test-quality orchestrator for the calcom repository.
Read TEST_QUALITY_BASELINE.md and use its findings as the work
list. Spawn five child sessions, one per area:

1. calcom/apps/web/playwright booking flows
2. calcom/apps/web/playwright event-types flows
3. calcom/apps/web/playwright login/auth flows
4. calcom/packages/embeds/embed-core
5. petclinic-rest-api/src/test/java, using petclinic-rest-api/pom.xml
   JaCoCo configuration and excluding generated
   rest/dto and rest/api packages

Give each child its own branch and require it to inspect existing
patterns before writing a test. Each child must produce a scoped
section in COVERAGE_GAP_REPORT.md with:
- the behavior or path that matters
- the existing spec or Java test files reviewed
- the new or changed test file paths
- the command used to run the focused test
- evidence that the test fails when the protected behavior is
  broken, followed by evidence that it passes after restoration
- the expected effect on escaped-defect risk, runtime, or flow
  coverage

The failure evidence is mandatory. The child must invert or break
the behavior locally, confirm the focused test is red, restore the
behavior, and confirm the test is green. Do not keep the temporary
breakage. A test that cannot fail when its claimed behavior is
broken adds runtime and no signal.

After the children finish, aggregate their findings into
COVERAGE_GAP_REPORT.md in the calcom repository. Include a table
with area, test path, behavior protected, red-run evidence,
green-run evidence, and remaining risk. Do not pad assertions,
duplicate an existing test, or change generated files.
```

The parent returns an aggregated `COVERAGE_GAP_REPORT.md` with one section per
child and a table of protected behavior, paths, commands, and remaining risk.
Read the red-then-green evidence column first: a test with no red run is
assertion padding, not useful signal.

The `petclinic-rest-api` child uses the existing `pom.xml` JaCoCo rules and
does not claim generated REST DTO or API code as a meaningful gap. Its report
can reference the 21 Java files under `src/test/java`, `mvnw`, and the API-level
assets under `src/test/postman/` and `src/test/jmeter/`.

<a id="part-3"></a>
## Part 3 — Nightly Flaky Triage, Event-Driven and Unattended

Paste this prompt into Devin:

```
In the calcom repository, create
.github/workflows/nightly-flaky-triage.yml.

Follow the repository's scheduled-workflow convention used by
the cron-*.yml files in .github/workflows/. Run the Cal.com
Playwright web suite from playwright.config.ts across the same
eight-shard matrix used by .github/workflows/e2e.yml. Use a
repeat-each setting so intermittent failures can surface.

Keep the existing Playwright behavior: CI retries are configured
in playwright.config.ts, traces use retain-on-failure, and the
workflow uploads a blob report for each shard. Merge the blob
reports using the pattern in .github/workflows/merge-reports.yml.
Use .github/workflows/e2e-report.yml as the reference for report
publication and workflow-run handling.

Parse the merged results and identify:
1. tests that pass only after a retry; and
2. tests whose result differs across repeat runs.

A pass after a retry is the flake signal currently hidden by
retries: 2 in playwright.config.ts. For each flake, create a
payload containing:
- spec file path
- test title
- Playwright project and shard
- failure message
- trace artifact from retain-on-failure
- pass rate across repeat runs

When the flake list is non-empty, call the Devin v3 API with the
payload and a link to the merged report. Do not call Devin when
the list is empty. Write the run summary and the API response
reference to NIGHTLY_FLAKY_TRIAGE_SUMMARY.md, and document the
workflow's bounded retry and escalation behavior.

Add a loop guard: skip pull requests authored by
devin-ai-integration[bot], as the existing event-driven security
workflow pattern does. Keep credentials in GitHub Actions secrets
and do not write secrets to the repository.
```

With nobody watching, the scheduled run repeats the eight shards, merges their
blob reports, and sends Devin a payload containing the spec path, title,
project/shard, failure message, trace artifact, and repeat-run pass rate.

The triage session receives the payload and updates the registry.
Paste this second prompt into the triage session:

```
In the calcom repository, triage the flaky-test payload from the
nightly workflow. Read playwright.config.ts,
FLAKY_REGISTRY.md if it exists, and the relevant spec file before
editing anything.

Classify each flake as one of:
- a real race or ordering bug in product code
- test-infrastructure timing
- an external dependency or sandbox

For a product or test-infrastructure flake that can be fixed
safely, make the smallest fix, run the focused test, and run the
relevant Playwright project. Record the commands and results in
FLAKY_TRIAGE_REPORT.md.

For a flake that cannot be fixed in the bounded attempt budget,
record it in FLAKY_REGISTRY.md with:
- spec file path
- test title
- owner
- first-seen date
- classification
- trace link
- repeat-run pass rate
- next action

Do not use quarantine to hide a product race. Do not remove a
test or weaken an assertion without recording the reason. Stop
after two fix attempts per flake. If the flake remains, create a
GitHub Issue for human review and link it from FLAKY_REGISTRY.md.
Skip pull requests authored by devin-ai-integration[bot] so the
workflow cannot trigger an unbounded bot loop.

Write FLAKY_TRIAGE_REPORT.md with one row per input flake,
including classification, action, commands, result, registry
entry, and issue link. End with counts for fixed, quarantined,
and escalated flakes.
```

The triage split separates product races, test-infrastructure timing, and
external dependencies. Devin fixes within two attempts; unresolved cases go
to `FLAKY_REGISTRY.md` and a GitHub Issue instead of looping.

<a id="part-4"></a>
## Part 4 — Devin Review as the Test-Quality Reviewer

### A human PR arriving in Cal.com

When an incoming human PR reaches Devin Review, it reports on:

- whether the changed behavior has a test
- whether assertions express behavior rather than implementation detail
- whether the change adds unconditional waits
- whether a touched spec is already in `FLAKY_REGISTRY.md`
- whether the test's focused command and result are visible

The review should look like this:

```text
Test-quality review

The booking behavior in apps/web/playwright/booking-pages.e2e.ts
changed, but the PR adds no test for the new cancellation path.
Please add an assertion for the user-visible outcome rather than
asserting an internal request shape.

I also found an unconditional wait in the changed spec. Replace
the wait with a condition tied to the booking state, then record
the focused Playwright command and result.

Finally, this spec is listed in FLAKY_REGISTRY.md. The change
should either fix the registered flake or explain why the new
assertion remains valid while the owner investigates it.
```

Create the durable guideline with this prompt:

```
In the calcom repository, create
agents/test-quality-review-guideline.md alongside the existing
agents/coding-standards.md convention.

Write a concise guideline for Devin Review covering:
- behavior assertions versus implementation-detail assertions
- evidence that a new test can fail when its behavior is broken
- avoiding unconditional waits
- checking changed flows against FLAKY_REGISTRY.md
- requiring an owner before quarantine debt is accepted
- recording focused commands and results

Reference these existing repository files without modifying them:
AGENTS.md, agents/commands.md, agents/coding-standards.md,
agents/knowledge-base.md, playwright.config.ts, and
vitest.config.mts.

End with the expected review comment format: finding, file path,
behavior at risk, suggested change, and verification command.
```

The guideline gives Devin Review a durable test-quality vocabulary beside the
repository's existing agent guidance. Review comments can point to a behavior,
an owned flake, and a verification command instead of making a generic quality
claim.

### Closing the loop on Devin's own PR

A human reviewer can leave a concrete comment on the triage PR:

```text
Quarantining this hides a real booking race. Fix the race instead
of recording the spec as a permanent skip.
```

Devin reads the comment, fixes the race or synchronization on the same branch,
runs the focused command, and pushes the fix commit. The registry entry returns
through review rather than being accepted because the first run was green.

<a id="part-5"></a>
## Part 5 — The Quality Gate Closes in CI

Paste this prompt into Devin:

```
In the calcom repository, create a reusable workflow at
.github/workflows/test-quality-gate.yml using workflow_call.
Follow the reusable-workflow convention in existing files such as
.github/workflows/check-types.yml and
.github/workflows/unit-tests.yml.

The gate runs for a PR through the existing aggregation path in
.github/workflows/all-checks.yml and .github/workflows/pr.yml.
It must:

1. Map changed product source paths to the relevant changed spec
   files under apps/web/playwright or the relevant package test
   tree. Use repository paths, not guessed module names.
2. Flag a product change that ships with no corresponding test
   change.
3. Fail if a spec is removed from FLAKY_REGISTRY.md quarantine
   without an owner recorded for the remaining decision.
4. Post one summary comment containing changed source paths,
   changed test paths, warnings, and blocking findings.

Use the one child-produced petclinic-rest-api result as the
cross-stack input. Include its JaCoCo gap summary and exclusions
from petclinic-rest-api/pom.xml in the posted quality summary,
without creating a workflow in that repository.

Document the policy in agents/test-quality-review-guideline.md:
- missing tests for changed behavior are blocking
- ownerless quarantine removal is blocking
- a changed test with weak assertions is a warning for Devin
  Review unless the policy can prove the defect
- a missing direct mapping for an unrelated or generated path is
  a warning, not a guessed failure

Keep the gate bounded and explain its path-mapping rules in the
workflow summary. Do not modify Cal.com application behavior.
```

The gate blocks a product change with no corresponding test change and blocks
ownerless quarantine removal. It warns when mapping is ambiguous or when a
reviewer must judge whether an assertion expresses the intended contract.

<a id="part-6"></a>
## Part 6 — The Shared Context Layer

The quality loop improves when each run starts with the same context:

- **Knowledge notes** hold the flake ownership map, the repository's testing
  standards, and the flows considered revenue-critical.
- **DeepWiki** provides orientation over Cal.com before a child edits a
  booking, authentication, event-type, or embed spec.
- **Playbook:** `!triage-flaky-tests` makes the nightly classification,
  bounded attempt policy, registry update, and escalation path repeatable.
- **MCP integrations** file or update the quarantine ticket and publish the
  nightly digest without copying ticket state into ad hoc prompts.

<a id="what-still-needs-a-human"></a>
## What Still Needs a Human

- Approving a quarantine. This is a coverage decision, not just a code
  decision.
- Deciding whether an intermittent failure is an acceptable-risk product race
  or a release blocker.
- Supplying credentials and third-party sandboxes for app-store and integration
  specs.
- Controlling production and release-cut access.
- Judging whether a generated test encodes the intended contract or only the
  current behavior.

<a id="key-takeaways"></a>
## Key Takeaways

- A green retry can hide a flake; a baseline turns pass-on-retry into a
  reviewable quality signal.
- The useful target is escaped-defect rate, shard wall time, critical-flow
  coverage, and quarantine debt burn-down—not raw coverage percentage.
- Child sessions increase QE capacity when each child owns a bounded area and
  proves its test can go red before it goes green.
- Cal.com's eight-shard E2E matrix, blob reports, retained traces, and CI retry
  setting provide the evidence surface for unattended nightly triage.
- A flaky registry with an owner, first-seen date, trace, and next action keeps
  quarantine from becoming invisible debt.
- Devin Review applies the same behavior-first standard to human PRs and agent
  PRs, while a human comment can redirect an automated fix.
- Reusable CI gates, Knowledge, DeepWiki, playbooks, and MCP integrations turn
  a one-time analysis into an organization-specific quality loop.
