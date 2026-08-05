# OtterWorks Full-Stack Feature Slice — Polyglot Microservices SDLC Demo

A single linear demo of the everyday SDLC on a **polyglot microservices
platform**: Devin orients across 11 deployable backend microservices and two
frontends, ships a full-stack feature slice (Python/FastAPI
`document-service` plus the React `client-app` surface), backs it with tests in
both languages, and then the branch lands on a **live, browsable tenant** at
`t-<id>.demo.otterworks.app` — compared side by side against the golden
baseline at [https://t-main.otterworks.app](https://t-main.otterworks.app).
Along the way the browser catches a cross-language contract divergence that the
unit tests did not, and the PR review loop closes it.

This is the microservices counterpart to
[`banking-feature-sdlc-demo.md`](banking-feature-sdlc-demo.md), which runs the
same lifecycle inside a single Java monolith module. That demo's payoff is a
green test gate; this one's is a running deployment you can click through.

## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Ships the Slice](#part-1)
  - [Act 1 — Orient across the polyglot stack](#act-1)
  - [Act 2 — The backend half of the slice](#act-2)
  - [Act 3 — The React surface, on a tenant branch](#act-3)
  - [Act 4 — See it live, and close the divergence](#act-4)
  - [Act 5 — Fan the remaining slices out](#act-5)
- [Part 2 — Run the Produced Artifact](#part-2)
- [Confirming Completion](#confirming-completion)
- [Where This Goes Next](#going-next)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

The golden app is already running — open it before touching any code:

- User SPA: [https://t-main.otterworks.app](https://t-main.otterworks.app)
  (`/documents` and `/files` are the surfaces this demo changes)
- Health: [https://t-main.otterworks.app/api/health](https://t-main.otterworks.app/api/health)

The two verification gates for this slice, run per service (the aggregate
`make test` and `make lint` targets still point at a stale `frontend/web-app`
path, so use the per-service commands):

```bash
cd services/document-service && pytest          # FastAPI slice
cd frontend/client-app && npm test && npm run build   # React surface
```

The full local stack, if you want to exercise the slice before it deploys:

```bash
make up seed=1      # docker compose infra + services, then seed admin data
make test-api-flows # black-box API flows in tests/api against localhost:8080
```

`t-main` tracks `main`, is perpetual, and is read-only for this demo — all
changes ship on a tenant branch.

---

<a id="repository"></a>
## Repository

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — a
  collaborative file-storage and document-editing platform built as 11
  deployable backend microservices (Go `api-gateway`, Java 17
  `auth-service`, Rust `file-service`, Python/FastAPI `document-service`,
  Node/Yjs `collab-service`, Kotlin `notification-service`, Python/Flask
  `search-service`, Scala `analytics-service`, Ruby/Rails `admin-service`, C#
  `audit-service`, and an intentionally legacy Java 8 `report-service`) plus
  a separate legacy-portal service directory that is not on the Helm/EKS
  deployment path. It also includes a React `frontend/client-app` and an
  Angular `frontend/admin-dashboard`, deployed to EKS through a multi-tenant
  demo platform.

The slice in this demo touches four places:

| Layer | Path |
|---|---|
| API surface | `services/document-service/app/api/documents.py` |
| Model + schema | `services/document-service/app/models/document.py`, `app/schemas/document.py` |
| Client data layer | `frontend/client-app/src/lib/api.ts`, `src/types/index.ts` |
| Client UI | `frontend/client-app/src/pages/documents.tsx`, `src/components/documents/document-card.tsx` |

Repo context Devin inherits: `AGENTS.md` at the repo root, the
`.agents/skills/synthetic-testdata-generation/SKILL.md` Skill, and the
environment blueprint at `.devin/blueprint.yaml` (per-language build, test, and
lint commands). DeepWiki typically maps an unfamiliar service quickly; coverage
depends on repo structure.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | What you can see |
|---|---|---|
| **Before** | `main`: documents support create, read, update, soft delete, version restore, versions, comments, and templates — there is **no archive** concept | [https://t-main.otterworks.app/documents](https://t-main.otterworks.app/documents) — the golden baseline, with no Archived view |
| **After** | branch `demo-<id>`: archive/unarchive endpoints, an `is_archived` + `archived_at` model change, pytest coverage, and an Archived toggle plus an archived badge in the React client | `t-<id>.demo.otterworks.app/documents` — your tenant, deployed from the branch by `.github/workflows/cd-tenant.yml` |

Two gates guard the slice, and they catch different classes of defect:

- **Per-service tests** — `pytest` in `services/document-service`, `npm test`
  and `npm run build` in `frontend/client-app`. On the PR,
  `.github/workflows/ci.yml` runs the same commands for the changed paths only
  (`poetry run ruff check .` and `poetry run pytest --cov=app` for the document
  service; `npm run lint`, `npm test`, `npm run build` for the client).
- **The running tenant** — a push to `demo-<id>` triggers `cd-tenant.yml`,
  which builds images only for the changed services and syncs the tenant. The
  result is a hostname you can open next to `t-main` and compare.

> **On "done":** in a polyglot stack, "done" means the contract holds *across*
> the language boundary. Two green suites on either side of an HTTP call can
> still add up to a blank field in the browser — Act 4 is exactly that case.

---

<a id="part-1"></a>
## Part 1 — Devin Ships the Slice

<a id="act-1"></a>
### Act 1 — Orient across the polyglot stack

Start with the request path, not the file tree. Ask Devin to trace one call from
the React click through the Go gateway into the FastAPI service.

```
Using the Cognition-Partner-Workshops/otterworks repo, trace the
documents request path end to end and report it as a numbered flow.

Start at frontend/client-app/src/pages/documents.tsx, through
documentsApi in src/lib/api.ts and the axios instance in
src/lib/api-client.ts, into the Go gateway
(services/api-gateway/internal/config/config.go route map and
internal/proxy/router.go), and into the FastAPI routers registered
in services/document-service/app/main.py.

Answer specifically:
1. Which prefixes the gateway proxies to document-service, and
   whether a new endpoint under /api/v1/documents needs a gateway
   change.
2. How the client authenticates, and what header the gateway
   forwards downstream.
3. What key-casing transform the axios response interceptor
   applies, and how that compares to the field names in
   services/document-service/app/schemas/document.py.
4. Where documents state is persisted and how the tests in
   services/document-service/tests/ stand up a database.
```

Expected: `/api/v1/documents` and `/api/v1/templates` already map to
`document-service` in the gateway route map, so a new endpoint under the
documents prefix needs no gateway change; the client sends
`Authorization: Bearer <token>` and the gateway forwards the JWT subject as
`X-User-ID`; and — the detail that matters later — `transformKeys` in
`src/lib/api-client.ts` rewrites every response key from `snake_case` to
`camelCase`, while the FastAPI schemas are `snake_case`.

<a id="act-2"></a>
### Act 2 — The backend half of the slice

```
In Cognition-Partner-Workshops/otterworks, work on a branch named
demo-<id> (substitute your short id, lowercase letters and digits)
and add document archiving to the Python/FastAPI
services/document-service.

Follow the existing is_deleted / is_template patterns in
app/models/document.py:
- Add is_archived (bool, default false) and archived_at
  (nullable timestamp) to the Document model, expose both on
  DocumentResponse in app/schemas/document.py, and add the matching
  Alembic revision under alembic/versions/ alongside 001_initial_schema.py.
  Note that app/db/session.py initializes schema with
  Base.metadata.create_all(), so the revision is for parity, not for
  runtime startup.
- Add POST /api/v1/documents/{id}/archive and
  POST /api/v1/documents/{id}/unarchive in app/api/documents.py,
  delegating to app/services/document_service.py like the existing
  version-restore endpoint does. Archiving stamps archived_at; unarchiving
  clears it.
- Extend the list endpoint with an archived query parameter that
  defaults to excluding archived documents.

Add tests to services/document-service/tests/test_documents_api.py
covering: archive sets is_archived and archived_at, unarchive clears
both, archived documents are absent from the default list and present
with archived=true, and archiving an already-archived document is
idempotent. Get `pytest` green from services/document-service and
paste the summary line.
```

Expected: a green `pytest` run in `services/document-service`, the new
endpoints reachable through the existing gateway prefix, and no gateway or
Helm change required.

<a id="act-3"></a>
### Act 3 — The React surface, on a tenant branch

```
Staying on the demo-<id> branch of
Cognition-Partner-Workshops/otterworks, surface archiving in the
React client at frontend/client-app.

- Add archive and unarchive calls to documentsApi in src/lib/api.ts,
  following the shape of the existing restore call, and add an
  archived option to list.
- Extend the Document interface in src/types/index.ts to carry the
  new fields, matching the casing convention already used there
  (wordCount, createdAt, trashedAt).
- In src/pages/documents.tsx, add an Active / Archived toggle that
  refetches through react-query, and in
  src/components/documents/document-card.tsx add an archive action
  plus, for archived documents, a badge showing the archived date.
- Add a focused Vitest unit test for the card rendering the archived
  badge and date. The client has a Vitest config and an existing unit
  test under `src/lib`, but no established component-test suite. Keep
  `npm run lint`, `npm test`, and `npm run build` green in
  frontend/client-app.

Then push the demo-<id> branch. Report the run URL of the
cd-tenant.yml workflow it triggers, which services it rebuilt, and
the tenant hostname (t-<id>.demo.otterworks.app).
```

Expected: green `npm test` and `npm run build`, and a `cd-tenant.yml` run that
rebuilds only `document-service` and `client-app` — the changed services — then
syncs the tenant. Branches matching `workshop-**` or `demo-**` are what trigger
tenant CD; the tenant is created with a TTL if it does not exist yet.

<a id="act-4"></a>
### Act 4 — See it live, and close the divergence

Open the two hostnames side by side:

- `https://t-main.otterworks.app/documents` — golden baseline, no Archived view
- `https://t-<id>.demo.otterworks.app/documents` — the branch, with the toggle

The toggle works, the archived document moves out of the Active list, and the
badge appears. **But the archived date on the badge is blank.** Both suites are
green: the pytest test asserts the API returns `archived_at`, and the Vitest
test passes because its fixture was written from the FastAPI schema, in
`snake_case`.

The divergence lives between them. The axios response interceptor rewrites
response keys before any component sees them:

```ts
// frontend/client-app/src/lib/api-client.ts
function snakeToCamel(s: string): string {
  return s.replace(/_([a-z])/g, (_, c) => c.toUpperCase());
}
```

So the browser is handed `archivedAt`, while the type and the card read
`archived_at`. Neither gate could see it — one stops at the HTTP response, the
other starts from a hand-written fixture. Only the running app shows it.

```
On the demo-<id> branch of Cognition-Partner-Workshops/otterworks,
the archived date renders blank in the browser on the deployed
tenant, though pytest and Vitest are both green.

Root cause: transformKeys in
frontend/client-app/src/lib/api-client.ts rewrites response keys
from snake_case to camelCase, so the client receives archivedAt
while src/types/index.ts and
src/components/documents/document-card.tsx read archived_at.

Fix it on the client side of the boundary, not by changing the
FastAPI schema:
- Align the Document interface and the card with the camelCase
  convention already used for wordCount / createdAt / trashedAt.
- Rewrite the Vitest fixture so it is produced by the same
  transform the runtime uses, so a future casing mismatch fails the
  test instead of the browser.
- Sweep src/lib/api.ts and src/types/index.ts for any other field
  read in snake_case and report what you find.

Keep npm test and npm run build green, push to demo-<id>, and
report the cd-tenant.yml run that redeploys the tenant. Then
respond to the review comments on the pull request for this branch
and push the follow-ups.
```

The fix corrects the client to match the contract; the fixture change is what
makes the class of bug catchable next time. The redeploy is another
`cd-tenant.yml` run over the changed frontend only, and the date renders on
refresh.

Devin Review comments on the PR and Devin answers them on the same branch —
each round is another CI run plus another tenant redeploy, so reviewers read
the diff and click the running result.

<a id="act-5"></a>
### Act 5 — Fan the remaining slices out

The rest of the archive epic is independent work across different services and
languages, so run it as parallel child sessions from one parent.

```
Act as the orchestrator for an archive-epic wave on
Cognition-Partner-Workshops/otterworks, using child Devin sessions
to parallelize.

Spawn one child session per slice below. Give each child the repo,
its own demo-<id>-<slice> branch, and this contract: follow the
patterns already in the target service, add tests in that service's
own suite, keep the service's own lint and test commands green, and
verify the result on the tenant its branch deploys to. Remind each
child that the axios interceptor in
frontend/client-app/src/lib/api-client.ts camelCases response keys.

Slices:
1. Archive filter on the Rust file-service (services/file-service,
   src/handlers.rs + src/models.rs, cargo fmt/clippy/test).
2. Archived-document exclusion in the Python/Flask search-service
   (services/search-service).
3. Archive events in the C# audit-service
   (services/audit-service, dotnet test) so archiving is auditable.
4. An Archived column in the Angular admin dashboard
   (frontend/admin-dashboard).

Monitor the children until each slice has a green service suite and
a deployed tenant. Summarize which services each child rebuilt and
any cross-language contract mismatches they hit.
```

Each child owns a branch, a tenant, and a green suite — the same bar applied
four times in parallel instead of once in series. Note that tenant SNS/SQS
eventing is disabled by default (`T_WIRE_EVENTING="false"` in
`scripts/deploy-tenant.sh`), so event-driven paths in slices 2 and 3 are
verified by their test suites rather than on the tenant.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifact

Exercise the slice through the deployed gateway on your tenant:

```bash
TENANT=t-<id>.demo.otterworks.app
API=api-t-<id>.demo.otterworks.app

curl "https://$TENANT/api/health"

TOKEN=$(curl -sS -X POST "https://$API/api/v1/auth/login" \
  -H 'Content-Type: application/json' \
  -d '{"email":"<seeded-user>","password":"<seeded-password>"}' \
  | python3 -c 'import json,sys; print(json.load(sys.stdin)["accessToken"])')

# Archive a document, then confirm it leaves the default list
curl -sS -X POST "https://$API/api/v1/documents/$DOC_ID/archive" \
  -H "Authorization: Bearer $TOKEN"

curl -sS "https://$API/api/v1/documents" \
  -H "Authorization: Bearer $TOKEN"
curl -sS "https://$API/api/v1/documents?archived=true" \
  -H "Authorization: Bearer $TOKEN"
```

> The login response field is `accessToken`. `scripts/seed.py` seeds
> admin-service data and does not create or print auth-service credentials,
> so use a valid auth-service account (or register one through
> `/api/v1/auth/register`) before running live; the values above are
> illustrative.

Then re-run both gates locally to show the contract holds on either side of the
boundary:

```bash
cd services/document-service && pytest
cd frontend/client-app && npm test && npm run build
```

---

<a id="confirming-completion"></a>
## Confirming Completion

The slice is complete when four things are true. Walk through in this order:

**1. The feature is visible in a browser, on a URL.** Open
`https://t-<id>.demo.otterworks.app/documents` next to
[https://t-main.otterworks.app/documents](https://t-main.otterworks.app/documents).
The toggle, the archived badge, and the rendered archived date exist on one and
not the other. The deployment is the artifact, not a screenshot of a diff.

**2. Both language gates are green.** `pytest` in
`services/document-service` and `npm test` plus `npm run build` in
`frontend/client-app`, with the same commands re-run by `ci.yml` for the
changed paths on the PR.

**3. The casing divergence is caught by a test now, not by the browser.** Show
the rewritten Vitest fixture built through the same transform the runtime
applies, and show it failing if the field is read in `snake_case`. The bug that
escaped both suites is now inside one of them.

**4. `main` and `t-main` are untouched.** The golden app still has no archive
concept, and the whole slice — code, tests, and its own deployment — lives on
`demo-<id>`. That is what makes this safe to run repeatedly and concurrently by
several people at once.

---

<a id="going-next"></a>
## Where This Goes Next

- **Automations** — a ticket moving into implementation, or a red check on
  `demo-<id>`, can start a session that ships the fix and redeploys the tenant.
  See [Automations](https://docs.devin.ai/product-guides/automations).
- **Contract enforcement** — the casing divergence in Act 4 is the same class of
  defect the API contract audit covers in
  [`workshops/otterworks/C2-contract-audit.md`](../../workshops/otterworks/C2-contract-audit.md);
  `tests/contract/` and `tests/api/` are where that check becomes permanent.
- **Coverage and quality sweeps** — scheduled sessions can run the coverage
  blitz in [`workshops/otterworks/C3-test-coverage.md`](../../workshops/otterworks/C3-test-coverage.md)
  across the services this slice touched.
- **Incident response** — once a slice is live, the observability stack under
  `observability/` and the runbooks in `docs/runbooks/` carry it into
  production, which is where
  [`workshops/otterworks/B1-investigate-incident.md`](../../workshops/otterworks/B1-investigate-incident.md)
  picks up. Chaos injection (`scripts/inject-bug.sh`) runs on ephemeral tenants
  only — never on `t-main`.
- **Shared context** — Knowledge notes, the repo's `AGENTS.md` and
  `.agents/skills/`, and DeepWiki can help the next session and child
  sessions start with the gateway route map, the casing convention, and the
  per-service test commands already in hand.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The unit of work is a **full-stack slice across language boundaries** — a
  FastAPI model, schema, and endpoints plus a React data layer, type, and
  component, with tests in Python and TypeScript. Devin holds both halves of
  the contract at once, which is the hard part of a polyglot codebase.
- **The deployment is the demo.** A branch push to `demo-<id>` builds only the
  changed services and produces a browsable
  `t-<id>.demo.otterworks.app` you can put next to the golden
  [t-main](https://t-main.otterworks.app) baseline. Reviewers click the feature
  instead of imagining it from a diff.
- **Green suites on both sides of an HTTP call are not the same as a working
  feature.** The `snake_case` → `camelCase` interceptor let a blank field
  through both gates; the running tenant surfaced it in seconds.
- **The fix strengthens the gate, not just the code.** Rebuilding the test
  fixture through the runtime transform turns a whole class of casing bugs into
  test failures instead of browser surprises.
- Slices are **independent and parallelizable** — child sessions extend the same
  epic into Rust, Flask, C#, and Angular at once, each with its own branch, its
  own green suite, and its own tenant.
- The golden baseline stays **untouched and shared** while every change lives on
  a disposable tenant — higher throughput without giving up a stable reference
  point the whole team compares against.
