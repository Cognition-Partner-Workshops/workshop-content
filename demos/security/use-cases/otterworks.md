# OtterWorks Application Threat Analysis with Runtime Verification

Devin reasons across the OtterWorks service mesh to find a multi-step attack
chain that no single-pattern scanner reports, confirms it against a **safe
ephemeral tenant** (never the perpetual golden app), then remediates and proves
the fix with a regression test. This is the proactive, application-layer
counterpart to the scanner-driven loop.

For the dependency-CVE scan → fix → re-scan thread on the same platform, see
[Security Scanning and Remediation on OtterWorks](../otterworks.md). For the
event-driven SAST automation that wires findings to Devin sessions
automatically, see
[Event-Driven SAST Remediation](event-driven-sast-remediation-demo.md) — this
use case does not duplicate that automation.

## Table of Contents

- [What Makes This Different](#what-makes-this-different)
- [Prerequisites](#prerequisites)
- [The Attack Surface](#attack-surface)
- [Safety Model](#safety-model)
- [Act 1 — Frame the Threat Model](#act-1)
- [Act 2 — Launch Threat Analysis](#act-2)
- [Act 3 — Confirm Against a Safe Ephemeral Tenant](#act-3)
- [Act 4 — Remediate, Test, and Re-Verify](#act-4)
- [Key Takeaways](#key-takeaways)

---

<a id="what-makes-this-different"></a>
## What Makes This Different

The scanners in OtterWorks CI — Trivy, Gitleaks, Semgrep — find known patterns:
dependency CVEs, committed secrets, and single-file SAST rules. They do not by
themselves reason about a chain that spans an API gateway written in Go, an auth
service in Java, and an admin plane in Ruby, where each link is individually
defensible but the sequence is not.

Two properties change the threat model here:

1. **Intelligence** — Devin traces a request from the gateway route table
   (`services/api-gateway/internal/config/config.go`) through the gateway JWT
   middleware (`services/api-gateway/internal/middleware/jwt.go`) into a
   downstream service's own authorization layer, and finds the boundaries where
   the two disagree.
2. **Runtime environment** — Devin deploys a disposable copy of the platform,
   constructs the request sequence, and shows whether it actually returns data it
   should not. Demonstrated exploitation in a controlled tenant, not a
   theoretical write-up.

The combination is one session: identify → confirm against a running tenant →
remediate → re-test, with evidence at each step.

---

<a id="prerequisites"></a>
## Prerequisites

- The [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks)
  repo, buildable in Devin's environment (`make up` for the local stack, or a
  pushed `demo-<id>` branch for a cluster tenant).
- The live golden app at
  [https://t-main.otterworks.app](https://t-main.otterworks.app) as a read-only
  reference for expected behavior (SPA at `/`, admin at `/admin`, health at
  `/api/health`).
- A safe target to attack — a local stack or an ephemeral tenant you own.
  **Never `t-main`.** See [Safety Model](#safety-model).

---

<a id="attack-surface"></a>
## The Attack Surface

Everything below is on `main` and is where the analysis in Act 2 concentrates.

**The `/api/v1/*` gateway routes** (`services/api-gateway/internal/config/config.go`,
`ServiceRoutes()`) fan out to the services:

```text
/api/v1/auth          -> auth-service
/api/v1/settings      -> auth-service
/api/v1/files         -> file-service
/api/v1/folders       -> file-service
/api/v1/documents     -> document-service
/api/v1/templates     -> document-service
/api/v1/collab        -> collab-service        (plus /socket.io)
/api/v1/notifications -> notification-service
/api/v1/preferences   -> notification-service
/api/v1/search        -> search-service
/api/v1/analytics     -> analytics-service
/api/v1/admin         -> admin-service
/api/v1/audit         -> audit-service
/api/v1/reports       -> report-service
```

**The auth model.** The gateway
(`services/api-gateway/internal/middleware/jwt.go`) requires a Bearer JWT on
every configured route except two exact public paths —
`/api/v1/auth/login`, `/api/v1/auth/register` — and the `/health`, `/metrics`,
and `/socket.io` prefixes. Downstream services may also enforce their own
authorization.
`auth-service`
(`services/auth-service/src/main/java/com/otterworks/auth/config/SecurityConfig.java`)
makes register / login / refresh `permitAll`, requires authentication elsewhere,
and gates
`/api/v1/auth/users/**` behind role `ADMIN`. Tokens are HS256/384 JWTs;
passwords use `BCryptPasswordEncoder(12)`.

**The admin plane.** `admin-service` (Rails,
`services/admin-service/config/routes.rb`) exposes `/api/v1/admin/*`. Its
`JwtAuthenticator` middleware (`services/admin-service/app/middleware/jwt_authenticator.rb`)
decodes the Bearer JWT and puts `sub`, `email`, and `role` into the Rack env,
but **excludes four paths** from that check:
`/health`, `/metrics`, `/api/v1/admin/alerts/ingest`, and `/api/v1/admin/chaos`.
The alert-ingest and chaos endpoints use their own shared-secret headers
(`X-Alert-Secret`, `X-Chaos-Secret`) — and the chaos controller
(`services/admin-service/app/controllers/api/v1/admin/chaos_controller.rb`)
**allows the
request when its secret is unset or empty** ("dev mode"). These boundary
disagreements — the gateway's public list vs. each service's own checks, and
middleware exclusions with secret-optional fallbacks — are exactly the seams
where a multi-step chain forms.

Candidate chains to have Devin evaluate:

**Middleware exclusion → unauthenticated admin action**

Reach a `/api/v1/admin/*` path the JWT middleware excludes, then use an unset or
empty secret where the controller permits it. Potential impact: an admin action
without a token.

**Role gap between gateway and service**

A valid non-admin token passes the gateway, but a downstream role check is
missing or weaker. Potential impact: privilege escalation.

**IDOR across services**

A predictable file or document ID reaches a service that does not verify
ownership. Potential impact: reading another tenant's or user's object.

**Trusted internal header**

A downstream service trusts a header the gateway is assumed to set or strip, and
a client forges it directly. Potential impact: authentication bypass.

---

<a id="safety-model"></a>
## Safety Model

Runtime confirmation means sending real attack traffic, so the target must be
disposable and owned by you.

- **Attack a tenant you created**, not the golden app. Push a `demo-<id>` branch;
  `.github/workflows/cd-tenant.yml` builds the changed services and deploys them
  to `t-<id>.demo.otterworks.app` (API at `api-t-<id>.demo.otterworks.app`) with
  a 72-hour TTL. Or run the stack locally with `make up`.
- **Never attack, chaos-inject, or otherwise modify
  [t-main.otterworks.app](https://t-main.otterworks.app).** It is the perpetual
  tenant tracking `main`, exempt from the reaper, and per the repo `AGENTS.md`
  cannot be checked in or bug-injected. Use it only as a read-only reference for
  what correct behavior looks like.
- **Do not touch `main`.** All analysis artifacts and fixes live on your branch.
- This is defensive work in an isolated environment — confirming a vulnerability
  exists so it can be fixed with confidence, entirely within a tenant you own.

---

<a id="act-1"></a>
## Act 1 — Frame the Threat Model

Open the repo and orient over the surface before asking for exploits. Paste:

```
Using the Cognition-Partner-Workshops/otterworks repo, map the
authentication and authorization boundaries of the /api/v1/*
surface.

Read:
- services/api-gateway/internal/config/config.go
  (ServiceRoutes) for the route -> service table
- services/api-gateway/internal/middleware/jwt.go for which
  paths the gateway leaves public vs. requires a Bearer JWT
- services/auth-service/src/main/java/com/otterworks/auth/config/SecurityConfig.java
  for the
  auth-service's own authorization rules
- services/admin-service/config/routes.rb and
  services/admin-service/app/middleware/jwt_authenticator.rb
  for the admin plane and which paths the JWT middleware
  excludes
- services/admin-service/app/controllers/api/v1/admin/chaos_controller.rb
  for how the excluded endpoints authenticate instead

Produce THREAT_MODEL.md that lists the /api/v1/* routes, who
is allowed to call it according to the gateway, who is allowed
according to the downstream service, and every place those two
answers disagree. Flag each disagreement as a candidate attack
boundary. Do not attempt any exploit yet.
```

The expected output is a boundary map: the gateway's public allow-list, each
service's own rules, and the disagreements between them — the admin JWT-middleware
exclusions, the secret-optional chaos endpoint, and any route where the gateway
and the service enforce different levels. These disagreements are the inputs to
Act 2.

---

<a id="act-2"></a>
## Act 2 — Launch Threat Analysis

Now ask for the chains. Paste:

```
Using THREAT_MODEL.md for the otterworks repo, identify
multi-step attack chains across the service boundaries — not
single-file findings a SAST rule would catch.

Concentrate on:
1. Endpoints reachable through the gateway whose downstream
   service performs a weaker (or no) authorization check than
   the gateway implies — including the admin-service paths
   excluded from its JWT middleware and any secret-optional
   fallback.
2. Object access where a predictable id (file, document,
   folder) reaches a service that does not verify ownership.
3. Any header or field a downstream service trusts because it
   assumes the gateway set or stripped it, that a client could
   set directly.

For each chain:
- Describe the full sequence step by step, naming the services
  and the exact routes/files/lines.
- Rate severity (Critical/High/Medium).
- Explain why a pattern-matching scanner (Trivy, Gitleaks,
  Semgrep with p/owasp-top-ten) would miss it — which of the
  repo's existing scanners already run in
  .github/workflows/security-scan.yml.

Produce THREAT_ANALYSIS.md ranked by severity. Pick the single
highest-severity chain and mark it as the one to confirm at
runtime next.
```

The `THREAT_ANALYSIS.md` output documents each chain with the file and line for
every step and, critically, a "why the scanners miss this" note — the argument
for application-layer analysis is that the finding survives a repo whose
Semgrep already runs `p/owasp-top-ten`.

---

<a id="act-3"></a>
## Act 3 — Confirm Against a Safe Ephemeral Tenant

The differentiating step: execute the top chain against a running tenant you
own. Paste:

```
Confirm exploitability of the top-severity chain from
THREAT_ANALYSIS.md against a SAFE target only.

Target rules — do not violate:
- Bring up a disposable target you own: either the local stack
  via `make up`, or push a branch demo-threat1 so
  cd-tenant.yml deploys a tenant at
  https://api-t-threat1.demo.otterworks.app.
- NEVER send attack traffic to https://t-main.otterworks.app.
  Use t-main only as a read-only reference for expected
  behavior.

Steps:
1. Seed a normal, non-admin user and any objects the chain
   needs (a second user's file for an IDOR, etc.).
2. Construct the request sequence against the tenant gateway,
   step by step, capturing each HTTP request and response.
3. Show the unauthorized outcome concretely — the status code
   and response body proving access that should have been
   denied — and contrast it with documented expected behavior
   where useful. Do not send attack traffic to t-main.

Produce EXPLOITATION_EVIDENCE.md with the exact requests,
responses, and the data or privilege the chain yielded. This
is defensive confirmation on a tenant we own, so we can fix it
with confidence.
```

When the target is available, Devin can seed realistic conditions and run the
sequence against `api-t-threat1.demo.otterworks.app`. The evidence is a concrete
transcript: "this request sequence, against a running tenant, returned this data
it should not have." If the chain is blocked in practice, that is recorded too —
a disproven hypothesis is a valid outcome and prevents a false-positive fix.

---

<a id="act-4"></a>
## Act 4 — Remediate, Test, and Re-Verify

Fix the confirmed chain and prove the fix with the same transcript plus a
regression test. Paste:

```
Remediate the confirmed chain in EXPLOITATION_EVIDENCE.md on
branch demo-threat1 in the otterworks repo.

1. Implement the fix at the correct boundary — the missing
   authorization or ownership check, the middleware exclusion
   that should not exist, or the secret-optional fallback that
   should fail closed. Fix it in the service that owns the
   boundary, following that service's existing patterns (Go
   middleware for api-gateway, Spring Security config for
   auth-service, the Rack middleware / controller for
   admin-service).
2. Re-run the exact request sequence from
   EXPLOITATION_EVIDENCE.md against the tenant and show it now
   returns 401/403 or a validation error.
3. Add a regression test that encodes the attack as an
   expectation:
   - a black-box flow test under tests/api/ (pytest, marked
     api_flow per tests/api/pytest.ini, run with
     `make test-api-flows` against the tenant gateway), and/or
   - a service-level test in the owning service
     (go test ./... for api-gateway, ./gradlew test for
     auth-service, bundle exec rspec for admin-service).
4. Run the owning service's test suite and the API flow suite;
   confirm no regressions.

Produce REMEDIATION_REPORT.md with the before transcript (chain
succeeds), the after transcript (chain blocked), and the new
test with its passing output. Keep every change on demo-threat1
— do not modify main or t-main.
```

The loop closes on itself: the same request that returned unauthorized data
should return `401`/`403` after the fix, and a new test in the suite fails if the
boundary ever regresses.
`t-main` was never touched — it remains the reference the tenant is compared
against.

The PR tells the whole story: threat model → analysis → runtime evidence → fix →
re-test, all on a disposable tenant. For automating the reactive scanner side of
this posture, cross-link to
[Event-Driven SAST Remediation](event-driven-sast-remediation-demo.md); this use
case stays focused on the proactive application-layer chain.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Multi-step chains live in the gaps between services.** The interesting
  finding is not a single vulnerable line — it is the gateway's public list
  disagreeing with a service's own checks, or an admin middleware exclusion with
  a secret-optional fallback. Devin reasons across Go, Java, and Ruby boundaries
  that no single-file rule spans.
- **Runtime confirmation changes the conversation.** "We think this is
  exploitable" becomes "we ran it against a tenant and here is the response,"
  which removes the false-positive triage that dominates SAST output.
- **The safety model is part of the technique.** Attack a disposable tenant you
  own; keep the perpetual [t-main](https://t-main.otterworks.app) as a read-only
  baseline; never touch `main`. Isolation is what makes demonstrated exploitation
  responsible.
- **The fix is verified, not assumed.** The exploit transcript is re-run after
  the fix, and a regression test encodes the attack so the boundary cannot
  silently regress.
- **Proactive and reactive compose.** This application-layer analysis finds what
  scanners cannot; the
  [event-driven SAST loop](event-driven-sast-remediation-demo.md) handles the
  dependency-CVE findings scanners do produce. Together they cover both breadth
  and continuous response.
