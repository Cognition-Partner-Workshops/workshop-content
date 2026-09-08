# Story to PR — From an Otter Projects Ticket to a Verified Pull Request

A bug ticket sits in the tracker. Someone adds the `devin` label. Devin picks it
up, reproduces it against a live tenant, finds the fault across two services,
fixes it with tests, ships the branch to the tenant, re-verifies, and reports
back on the ticket while the board card moves from **Ready** to **In Review**.
No one pastes anything into a chat window.

## Table of Contents

- [Quick Start](#quick-start)
- [Before](#before)
- [Part 1 — Assign the Ticket](#part-1)
- [Part 2 — Watch Devin Work the Story](#part-2)
- [Part 3 — Verify on the Tenant](#part-3)
- [Part 4 — Manual Variant (no tracker)](#part-4)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

1. Open `https://projects.otterworks.app`, project **OTTER**, ticket **OTTER-7**.
2. Add the label `devin` (or set assignee to **devin**). The card moves to
   **In Progress** and a Devin session link appears on the ticket within a minute.
3. Follow the session; when the PR link lands on the ticket, run the curl check
   in [Part 3](#part-3) against `https://api-t-<attendee_id>.otterworks.app`.

If the tracker webhook is not wired in your org, use the paste-in prompt in
[Part 4](#part-4) — it is the same story in one line.

---

<a id="before"></a>
## Before

**The ticket.** `OTTER-7 — Document export returns 401 for an authenticated user`
(type bug, priority High, labels `document-service`, `api-gateway`). Observed:

- With a valid JWT, `POST /api/v1/documents/` returns `400 owner_id is required`
  unless `owner_id` is repeated in the body.
- With `owner_id` in the body, create and `GET /api/v1/documents/` succeed.
- `GET /api/v1/documents/{id}/export?format=markdown` with the same token returns
  `401 {"detail":"Authentication required"}`.

Acceptance criteria are on the ticket: owned-document CRUD and export work through
the gateway with only the JWT; another user's document is `403`; no token is
`401`; unit tests cover valid JWT, unverifiable JWT with forwarded identity, and
no identity; verified against a deployed `workshop-<id>` tenant. The ticket does
**not** say where the bug is — that is Devin's job. The same story is mirrored as
GitHub issue [#1507](https://github.com/Cognition-Partner-Workshops/otterworks/issues/1507).

**The code.** Two services are involved and neither is obviously wrong on its own:

- `services/api-gateway/internal/proxy/router.go` — validates the JWT and
  forwards the identity as `X-User-ID` to upstreams.
- `services/document-service/app/api/documents.py` — `_extract_user_id()` decodes
  the JWT itself when `JWT_SECRET` is set and only falls back to `X-User-ID`
  when it is not.

**The runtime.** The Helm values for the tenant decide which of those two paths
the pod actually takes — the answer is in the deployed `ConfigMap`/`Secret` for
`document-service`, not in the repo.

**The wiring.** Otter Projects (`demo-platform/otter-projects/`) dispatches to a
Devin Automation webhook. The automation prompt is the ticket rendered as one
line from the project's prompt template; the payload also carries
`callback_url` so the session posts progress back to
`POST /api/webhooks/devin?ticket=OTTER-7`. Setup is in
`demo-platform/otter-projects/docs/api.md` ("Devin Automation webhook"). In the
Demo org this is the automation named **"Otter Projects — ticket assigned to
Devin → story-to-PR session (OtterWorks)"** (webhook trigger, `run_as`
organization); its webhook URL and secret are configured on the deployed Otter
Projects instance, and the sessions it starts read `PROJECTS_API_KEY` from the
org's secrets to call back to the board.

---

<a id="part-1"></a>
## Part 1 — Assign the Ticket

On the OTTER board, open **OTTER-7** and add the `devin` label. Otter Projects:

1. Renders the prompt from the template — one line, ticket key, title,
   description, repo, acceptance criteria, callback URL.
2. POSTs it to the Devin Automation inbox with `X-Webhook-Secret` and an HMAC
   `X-OtterProjects-Signature`; retries three times on failure and records the
   delivery in the ticket's activity feed.
3. Moves the card to **In Progress** and writes an activity entry.

Within about a minute the session URL and ID appear on the ticket (the poller
mirrors session state every 60 s even if the session never calls back).

---

<a id="part-2"></a>
## Part 2 — Watch Devin Work the Story

Open the session from the ticket. What you should see, in order:

- **Reproduce first.** Devin registers a throwaway user on
  `https://api-t-<attendee_id>.otterworks.app`, creates a document, and hits
  export. It gets the same `401` the ticket describes and posts that as a comment
  on OTTER-7.
- **Read both hops.** It inspects the gateway director in `router.go` (identity
  is forwarded), then `_extract_user_id()` in `documents.py`, and notices the
  decode path swallows `PyJWTError` and never consults `X-User-ID` when a secret
  is configured.
- **Check the deployed config.** It reads the tenant's `document-service`
  environment (from Helm values in `infrastructure/helm/` or, with cluster
  access, from the namespace) to confirm which path the pod takes.
- **Fix with tests.** The fix lands in `documents.py` and
  `services/document-service/tests/`, covering the three identity cases in the
  acceptance criteria and the `owner_id`-from-JWT create path.
- **Run what CI runs.** `poetry run pytest --cov=app` in
  `services/document-service/`, then push to `workshop-<attendee_id>` so
  `cd-tenant.yml` redeploys the tenant.
- **Re-verify on the tenant.** Same curl sequence, now `200` with the export
  body; cross-user `403`; no token `401`.
- **Report back.** The PR link is posted to OTTER-7; the card moves to
  **In Review**.

If the session stalls on a decision (for example, whether the gateway should
strip a client-supplied `X-User-ID`), answer in the session — that is a real
security decision, and the ticket's acceptance criteria do not settle it.

---

<a id="part-3"></a>
## Part 3 — Verify on the Tenant

After the tenant redeploys, run the ticket's reproduction yourself:

```
API=https://api-t-<attendee_id>.otterworks.app; TOKEN=$(curl -s -X POST $API/api/v1/auth/register -H 'Content-Type: application/json' -d '{"email":"otter7-<attendee_id>@example.com","password":"Passw0rd!x","name":"Otter Seven"}' | jq -r .access_token); DOC=$(curl -s -X POST $API/api/v1/documents/ -H "Authorization: Bearer $TOKEN" -H 'Content-Type: application/json' -d '{"title":"export check","content":"# hi","content_type":"markdown"}' | jq -r .id); curl -s -o /dev/null -w '%{http_code}\n' "$API/api/v1/documents/$DOC/export?format=markdown" -H "Authorization: Bearer $TOKEN"
```

Expected: the document is created without `owner_id` in the body, and the last
line prints `200`. Then open the PR from the ticket: CI on the PR should be green
for `document-service`, and the PR description should include the before/after
tenant transcript.

---

<a id="part-4"></a>
## Part 4 — Manual Variant (no tracker)

If the Otter Projects webhook is not configured, paste this into a new session.
It is the same ticket in one line:

```
In Cognition-Partner-Workshops/otterworks, work OTTER-7 (mirrored as GitHub issue #1507): document export returns 401 for an authenticated user. Start from branch workshop-<attendee_id> (based on the workshop branch). First reproduce against the deployed tenant https://api-t-<attendee_id>.otterworks.app by registering a user, creating a document with only the JWT (note the 400 owner_id is required behavior), and calling GET /api/v1/documents/{id}/export?format=markdown; record the exact status codes. Then trace the identity path through services/api-gateway/internal/proxy/router.go and services/document-service/app/api/documents.py (_extract_user_id, _require_user_id, _ensure_owner) and the tenant's document-service configuration under infrastructure/helm to determine why the forwarded identity is lost. Fix it in document-service with unit tests in services/document-service/tests covering a valid JWT, an unverifiable JWT with X-User-ID forwarded by the gateway, and no identity, plus the create-without-owner_id case. Run poetry run pytest --cov=app in services/document-service, push to workshop-<attendee_id>, wait for the cd-tenant workflow to redeploy, and rerun the reproduction to show 200 for the owner, 403 for another user, and 401 with no token. Put the before/after tenant transcript and the root cause in the PR description and target the PR at the workshop branch.
```

When the session is done, post its PR URL to OTTER-7 with the tracker API so the
board reflects it:

```
curl -s -X POST "https://projects.otterworks.app/api/webhooks/devin?ticket=OTTER-7" -H "Authorization: Bearer $PROJECTS_API_KEY" -H 'Content-Type: application/json' -d '{"ticket":"OTTER-7","status":"finished","message":"PR opened","pr_url":"<PR URL>"}'
```

---

<a id="key-takeaways"></a>
## Key Takeaways

- The story came from the tracker, not from a prompt someone typed; the tracker
  is where the result went back. Swap Otter Projects for Jira and the shape is a
  single Automation rule.
- Devin reproduced on a live tenant before reading code, and re-verified on the
  same tenant after — the PR description carries runtime evidence, not just a
  passing unit test.
- The acceptance criteria were the spec; the ticket never named the root cause.
  Devin's job was to find it across a Go gateway and a Python service and the
  deployed configuration between them.
- The one open judgment call (trusting a forwarded header) surfaced as a question
  to the team, not a silent decision.
