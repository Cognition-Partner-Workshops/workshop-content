# Two Frontends, One Platform — Frontend Engineering Demo

A single linear demo showing Devin working the way frontend teams actually do:
two production frontends — a React 18 / Vite user SPA and an Angular 17 admin
dashboard — sharing one API on the OtterWorks platform. Devin orients over both
codebases, ships a user-visible feature in the React app with component tests,
mirrors the same UX pattern in the Angular app, verifies its own work visually
in a browser with screenshots attached to the PR, and deploys the change to an
ephemeral branch tenant you can open next to the live baseline.

The live anchor is the golden app at
[https://t-main.otterworks.app](https://t-main.otterworks.app) — the user SPA
tracking `main`, perpetual, and never mutated by demos. Every change in this
demo lands on a branch and is viewed on its own tenant hostname, side by side
with t-main.

## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Ships Across Both Frontends](#part-1)
  - [Act 1 — Orient over two frontend stacks](#act-1)
  - [Act 2 — Ship a user-visible feature in the React SPA](#act-2)
  - [Act 3 — Mirror the pattern in the Angular admin dashboard](#act-3)
  - [Act 4 — Deploy a branch tenant and compare in the browser](#act-4)
- [Part 2 — Run the Produced Artifact](#part-2)
- [Confirming Completion](#confirming-completion)
- [Where This Goes Next](#going-next)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

The two frontends and their verified gates, from the otterworks repo root:

```bash
# React user SPA
cd frontend/client-app
npm ci
npm run lint      # eslint over src
npm test          # vitest run
npm run build     # tsc --noEmit && vite build
npm run dev       # Vite dev server on port 3000

# Angular admin dashboard
cd frontend/admin-dashboard
npm ci
npm run build     # ng build --configuration production
npm start         # ng serve on port 4200
```

Open [https://t-main.otterworks.app](https://t-main.otterworks.app) to see the
deployed golden app (health check at `/api/health`).

> Command caveats, verified against the repo: the root `Makefile`'s `test` and
> `lint` targets still reference a stale `frontend/web-app` path — use the
> per-app npm scripts above instead. In the Angular app, `ng lint` has no
> configured lint target, and the Karma test job is soft-failed in CI
> (`npm run lint || true`, `npm test || true` in `.github/workflows/ci.yml`),
> so the production build is its reliable gate. The React app's lint, test,
> and build all run strict in CI.

---

<a id="repository"></a>
## Repository

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — a
  polyglot collaborative file-storage and document-editing platform: 11 backend
  services behind a Go API gateway, plus the two frontends this demo lives in:
  - **`frontend/client-app`** — the user SPA: React 18, Vite, TypeScript,
    Vitest with React Testing Library and jest-dom as devDependencies, and
    Playwright end-to-end specs under `e2e/`. Pages live under `src/pages/`
    (`files.tsx`, `notifications.tsx`, `search.tsx`, ...) with shared
    components under `src/components/` (for example
    `components/files/share-dialog.tsx`).
  - **`frontend/admin-dashboard`** — the operator console: Angular 17 with
    Angular Material, Karma/Jasmine specs colocated with components. Pages
    live under `src/app/pages/` (`users/`, `audit/`, `incidents/`, ...);
    `users/users.component.spec.ts` is the reference spec style.

  Both frontends call the same API gateway; on a deployed tenant the SPA's
  nginx proxies `/api/v1/*` to it, so one backend serves both UIs.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | React user SPA | Angular admin dashboard |
|---|---|---|
| **Before** | `main`: the Files page (`src/pages/files.tsx`) lists files and folders with grid/list toggle, sorting, upload, and sharing — but no way to narrow the listing by file type | `main`: the Audit page (`src/app/pages/audit/audit.component.ts`) lists audit events with search, action, and severity filters — and no spec file |
| **After** | a PR branch with type-filter chips on the Files page, Vitest + React Testing Library component tests, and Devin's own browser screenshots in the PR | the same PR with the audit table's filters covered by a new Karma/Jasmine spec following the `users` spec style |

The verification loop has three layers, and the demo exercises each one:

1. **Component tests** — `npm test` (Vitest) in the React app asserts the
   filter logic; a new Jasmine spec asserts the Angular filter behavior.
2. **Visual verification** — Devin runs the app in its own browser, exercises
   the change, and attaches before/after screenshots to the PR, so reviewers
   see the UI, not just the diff.
3. **A deployed tenant** — pushing a `demo-<id>` branch triggers
   `.github/workflows/cd-tenant.yml`, which builds only the changed services
   and deploys an ephemeral tenant at `https://t-<id>.demo.otterworks.app`
   (72h TTL), browsable next to the untouched t-main baseline.

---

<a id="part-1"></a>
## Part 1 — Devin Ships Across Both Frontends

<a id="act-1"></a>
### Act 1 — Orient over two frontend stacks

Start by asking Devin to map both frontends. With DeepWiki over the repo,
Devin typically orients over an unfamiliar codebase in minutes (coverage
depends on repo structure).

```
In the Cognition-Partner-Workshops/otterworks repo, map the two frontends:

1. frontend/client-app — the React 18 / Vite user SPA. Explain the routing in
   src/App.tsx, how src/pages/files.tsx renders the file listing (grid/list
   toggle, sorting, selection, upload), and how API calls flow through
   src/lib/api-client.ts to the gateway. Confirm which npm scripts exist for
   lint, test, and build, and what the Vitest setup in vite.config.ts covers.

2. frontend/admin-dashboard — the Angular 17 admin console. Explain the
   routes in src/app/app.routes.ts, how the audit page
   (src/app/pages/audit/audit.component.ts) filters events, and the
   Karma/Jasmine spec conventions in
   src/app/pages/users/users.component.spec.ts.

Finish with a short summary of how the two apps share the API gateway, and
which CI jobs in .github/workflows/ci.yml gate each frontend.
```

Expected: a tour of both stacks — the React router and Files page state, the
Angular standalone components and Material table patterns, the shared
`/api/v1` gateway contract, and the CI picture (the `web-app` job runs lint,
test, and build strictly; the `admin-dashboard` job soft-fails lint and test
and gates on the production build).

<a id="act-2"></a>
### Act 2 — Ship a user-visible feature in the React SPA

The core beat. The Files page lists everything; users want to narrow it by
type. Devin implements the feature, writes component tests, and verifies it
visually in its own browser.

```
In the Cognition-Partner-Workshops/otterworks repo, add a file-type filter to
the Files page of the React user SPA (frontend/client-app).

Feature: on src/pages/files.tsx, add a row of filter chips — All, Documents,
Images, Other — that narrows the visible files by extension while leaving
folders visible. The active chip is highlighted, the filter composes with the
existing sort options, and an empty filtered result shows a short "No matching
files" message. Follow the existing Tailwind styling and component patterns in
src/components/files/.

Tests: add Vitest + React Testing Library component tests next to the page
(the vitest include is src/**/*.{test,spec}.{ts,tsx}; RTL and jest-dom are
already devDependencies — add a jsdom test environment if one is needed).
Cover: default shows everything, each chip narrows correctly, folders remain
visible under any chip, and the empty-filter message. Run npm run lint,
npm test, and npm run build in frontend/client-app and get them green.

Visual verification: run the app with npm run dev, exercise the chips in your
browser, and attach before/after screenshots of the Files page to the PR
description.
```

Expected: a focused diff on `files.tsx` plus a new colocated test file, a
green `lint` / `test` / `build` run, and a PR whose description includes
Devin's own screenshots of the filter working — the reviewer sees the UI
change without checking out the branch.

The PR feedback loop applies here like anywhere else: Devin Review comments
on the diff, and follow-up requests ("make the chips keyboard-navigable")
go straight to the same session, which pushes to the same branch.

<a id="act-3"></a>
### Act 3 — Mirror the pattern in the Angular admin dashboard

Same platform, second framework. The admin Audit page already has the
filtering UX — what it lacks is a spec. Devin closes the gap following the
repo's own conventions, showing the same session moving between React and
Angular without missing a beat.

```
In the Cognition-Partner-Workshops/otterworks repo, the Angular admin
dashboard's audit page (frontend/admin-dashboard/src/app/pages/audit/
audit.component.ts) filters audit events by search text, action, and
severity, but has no spec file.

Write audit.component.spec.ts next to it, following the structure and style
of src/app/pages/users/users.component.spec.ts (Karma/Jasmine, standalone
component setup, mocked services). Cover: events render into the table, the
search text narrows results, the action and severity filters narrow results
and compose with search, and clearing filters restores the full list.

Run npm test in frontend/admin-dashboard with a headless Chrome and report
the results; if headless Chrome is unavailable in the environment, verify the
spec compiles by running npm run build and say so explicitly. Note in the PR
that CI currently soft-fails the admin lint and test jobs
(.github/workflows/ci.yml), so this spec is a step toward making that gate
strict.
```

Expected: one new spec file mirroring the `users` spec conventions, an honest
report of how it was verified, and a talking point for the roadmap: the
Angular gate is soft today, and adding real specs is how a team earns the
right to remove the `|| true`.

<a id="act-4"></a>
### Act 4 — Deploy a branch tenant and compare in the browser

The finale: see the feature deployed, next to the untouched baseline. Pushing
a `demo-<id>` or `workshop-<id>` branch triggers `cd-tenant.yml`, which
detects that only `frontend/client-app` changed, builds just that image, and
deploys an ephemeral tenant.

```
In the Cognition-Partner-Workshops/otterworks repo, deploy the file-type
filter change from the feature branch to an ephemeral tenant.

Create a branch named demo-<short-id> (pick a short RFC-1123-safe id, e.g.
demo-ftypes1) containing the client-app changes, and push it. That triggers
.github/workflows/cd-tenant.yml, which builds only the changed services and
deploys tenant t-<short-id> with a 72h TTL.

Watch the workflow run to completion, then verify in your browser:
1. https://t-<short-id>.demo.otterworks.app shows the Files page with the
   new filter chips working.
2. https://t-main.otterworks.app is unchanged — no chips; the golden app
   never receives demo changes.
Attach side-by-side screenshots of both tenants to the PR description and
report the tenant URL.
```

Expected: a CI run that builds one image instead of thirteen, a live tenant
URL anyone in the room can open, and the before/after visible in two browser
tabs — the branch tenant with the feature, t-main without it. The tenant
reaps itself after the TTL; nothing needs manual cleanup.

The admin dashboard is deployed inside each tenant's namespace but is not
routed on the public tenant hostname — the Angular work from Act 3 is
verified through its spec and, when a live look is wanted, `npm start`
against the tenant API.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifact

Reproduce Devin's verification locally from the PR branch:

```bash
cd frontend/client-app
npm ci
npm run lint
npm test          # the new Files-page component tests run here
npm run build
npm run dev       # open http://localhost:3000 and use the filter chips
```

And the Angular side:

```bash
cd frontend/admin-dashboard
npm ci
npm run build
npm test          # requires a headless Chrome on the machine
```

Then open the two tenants from Act 4 in adjacent tabs:

- `https://t-<short-id>.demo.otterworks.app` — the feature, deployed
- [https://t-main.otterworks.app](https://t-main.otterworks.app) — the
  baseline, untouched

---

<a id="confirming-completion"></a>
## Confirming Completion

The demo is complete when four things are true:

**1. The React gate is green with new tests.** `npm run lint`, `npm test`,
and `npm run build` pass in `frontend/client-app`, and the test run includes
the new Files-page component tests — not just the pre-existing suite.

**2. The PR shows the UI, not just the diff.** The PR description carries
Devin's own screenshots: the Files page before and after, and the Act 4
side-by-side of the branch tenant next to t-main.

**3. The Angular spec exists and matches repo conventions.**
`audit.component.spec.ts` sits next to the component it tests, structured
like `users.component.spec.ts`, with an explicit note on how it was verified.

**4. The baseline is untouched.** t-main still renders the Files page without
filter chips; the feature lives on the branch and its ephemeral tenant,
ready for review and merge.

---

<a id="going-next"></a>
## Where This Goes Next

- **Make the Angular gate strict** — with real specs accumulating, remove the
  `|| true` from the `admin-dashboard` CI job and add an `ng lint` target, so
  both frontends get the same bar the React app already has.
- **Event-driven sessions** — a failed `web-app` CI job or a Devin Review
  comment can start a session automatically that pushes a fix to the same
  branch. See [Automations](https://docs.devin.ai/product-guides/automations).
- **Child-session fan-out** — a backlog of UI polish items (empty states,
  keyboard navigation, loading skeletons) can run as parallel child sessions,
  each on its own `demo-<id>` branch with its own tenant to review.
- **Knowledge and playbooks** — the verified per-app commands and the
  "screenshots in the PR" convention belong in Knowledge notes and a playbook,
  so future sessions inherit them without rediscovery.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **One session, two frameworks.** Devin moved between a React 18 / Vite SPA
  and an Angular 17 Material dashboard in a single thread, following each
  codebase's own conventions — colocated Vitest tests on one side, Jasmine
  specs on the other — rather than imposing one style on both.
- **Visual verification is part of the loop.** For frontend work, a green
  test run is necessary but not sufficient; Devin runs the app in its own
  browser and attaches screenshots, so the PR shows the pixels reviewers
  actually care about.
- **Deployment is the final proof.** The branch-tenant model turns "it works
  on my machine" into a URL: every change gets its own hostname next to the
  perpetual t-main baseline, and reaps itself when the TTL expires.
- **Honest gates beat green gates.** The demo names the real state of the
  repo — a stale Makefile path, a soft-failed Angular CI job, a missing lint
  target — and shows how adding tests is the path to tightening them, not
  papering over them.
