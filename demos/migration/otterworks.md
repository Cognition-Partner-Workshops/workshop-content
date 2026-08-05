# In-Place Application Modernization — OtterWorks Demo

Modernize the application layer of a live, polyglot estate without changing what
users see: bring an intentionally legacy Java service from **Java 8 / Spring Boot
2.5** up to **Java 17 / Spring Boot 3.2** (`javax` → `jakarta`, dead frameworks
replaced, CVEs cleared), then **translate a Python service from Flask to
FastAPI** while holding its OpenAPI contract byte-for-byte. Contract and flow
tests are the parity gate, and the running product at
[t-main.otterworks.app](https://t-main.otterworks.app) is the proof that nothing
user-visible broke.

The two moves are the two kinds of application modernization every estate has:

| Move | Service | From → To | Parity gate |
|---|---|---|---|
| **Runtime / framework upgrade** | `report-service` (Java) | Java 8 / Boot 2.5.14 → Java 17 / Boot 3.2, `javax` → `jakarta`, SpringFox → springdoc, iText 5 → OpenPDF, JUnit 4 → 5, CVE-bearing libraries bumped | `make test-report`, `make build-report`, the `report-service` CI job, `tests/api/test_audit_analytics_report_flow.py` |
| **Language / framework translation** | `search-service` (Python) | Flask 3.0 (WSGI, sync) → FastAPI (ASGI, async) | `tests/contract/test_search_contract.py` against `shared/openapi/search-service.yaml`, plus the service's own `pytest` suite |

This is the layer **above** infrastructure migration. The companion demo
[`aws-cloud-native-modernization-demo.md`](./aws-cloud-native-modernization-demo.md)
takes the same estate through the R's of migration — moving backing services and
compute onto managed AWS targets. This thread never changes where the code runs;
it changes what the code *is*, and proves the behavior did not move with it.

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Does the Modernization](#part-1)
  - [Act 1 — Inventory the modernization debt](#act-1)
  - [Act 2 — Upgrade the legacy Java service](#act-2)
  - [Act 3 — Translate Flask to FastAPI against the contract](#act-3)
  - [Act 4 — What the gates catch that a diff review does not](#act-4)
  - [Act 5 — Fan the remaining debt out in parallel](#act-5)
- [Part 2 — Confirm It in the Live Product](#part-2)
- [Confidence = Programmatic Verification](#confidence)
- [Always-On: Event-Driven and Scheduled](#always-on)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

The two parity gates, from a clean checkout of
`Cognition-Partner-Workshops/otterworks`:

```bash
# Gate 1 — the Java service still builds and its tests still pass
make test-report          # cd services/report-service && mvn test
make build-report         # cd services/report-service && mvn package -DskipTests

# Gate 2 — the Python service still satisfies its OpenAPI contract
pip install pyyaml jsonschema requests pytest
SEARCH_SERVICE_URL=http://localhost:8087 \
  pytest tests/contract/test_search_contract.py -v
```

The full stack runs locally with `make up` (add `seed=1` to seed data), and the
black-box suite that exercises both services through the API gateway is
`make test-api-flows`.

The live product is already deployed: <https://t-main.otterworks.app> (user app
at `/`, admin dashboard at `/admin`, gateway health at `/api/health`).

---

<a id="repositories"></a>
## Repositories

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — the
  running platform: 11 backend services across 9 languages plus a React
  (`frontend/client-app`) and an Angular (`frontend/admin-dashboard`) frontend,
  on EKS. The two modernization targets are
  `services/report-service/` (Java 8 / Boot 2.5.14, with
  `UPGRADE_GUIDE.md` documenting 11 upgrade axes) and
  `services/search-service/` (Flask, with `TRANSLATION_GUIDE.md` documenting the
  FastAPI target and its pitfalls). The contract for search lives in
  `shared/openapi/search-service.yaml`; `services/document-service/` is the
  in-repo FastAPI reference implementation.
- [workshop-content](https://github.com/Cognition-Partner-Workshops/workshop-content)
  — the hands-on modules the two moves come from:
  `workshops/otterworks/A2-framework-upgrade.md` and
  `workshops/otterworks/A3-language-translation.md`.

Both target services are *inside* a running estate: `report-service` is reached
through the Go gateway at `/api/v1/reports` (port 8091), and `search-service`
backs the SPA's search page at `/search` via `/search` and `/search/suggest`
(port 8087). Nothing here is a standalone sample project — that is what makes the
parity requirement real.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Before (on `main`) | After (on a Devin branch) |
|---|---|---|
| `report-service` | Boot 2.5.14 parent, `<java.version>1.8</java.version>`, 16 `import javax.*` statements across 4 source files, `SecurityConfig extends WebSecurityConfigurerAdapter` with `antMatchers()`, SpringFox 3.0 `Docket` bean, iText 5.5.13.3 (AGPL), Guava 28.0-jre, Commons Lang 2.6, Commons IO 2.6, POI 4.1.2, OpenCSV 4.6, JUnit 4 + Mockito 3.12.4 | Boot 3.2.x parent on Java 17, `jakarta.*` throughout, a `@Bean SecurityFilterChain` with `requestMatchers()`, springdoc-openapi 2.x, OpenPDF, current library versions, JUnit 5 + Mockito 5 — same endpoints, same generated PDF/CSV/Excel outputs |
| `search-service` | Flask 3.0.2 + Gunicorn, `Blueprint`s in `app/api/{search,index,health}.py`, dataclass config, `jsonify` responses, sync MeiliSearch SDK calls, SQS polling on a background thread | FastAPI + Uvicorn, `APIRouter`s, Pydantic models, `async def` handlers, MeiliSearch calls off the event loop, an `asyncio` SQS consumer — identical paths, status codes, response bodies, metric names, and log event names |
| Guardrails | Trivy skips the legacy service (`--skip-dirs services/report-service`, three invocations in `.github/workflows/security-scan.yml`); the `security-scan` Makefile target prints `Report Service (skipped - legacy)`; the CI job pins `java-version: '8'` | The carve-outs are deleted and the CI job pins Java 17 — the service rejoins the scanned estate |

The verification loop sits between the two columns. **Parity does not mean "it
starts"**:

- For `search-service`, parity means the 18 tests in
  `tests/contract/test_search_contract.py` run *unchanged* against the FastAPI
  process and do no worse than the recorded Flask baseline — the
  `400` + `{"error": ...}` envelope for a missing `q`, the empty-suggestions
  200 for a one-character prefix, the `503` +
  `{"ready": false, "reason": "meilisearch_unavailable"}` readiness body, the
  four `search_service_*` Prometheus metric families, and the response schemas
  resolved out of the OpenAPI document. Record that baseline first: the Flask
  service on `main` passes 16 of the 18 and fails
  `test_index_document_missing_body` and `test_index_file_missing_body`, because
  Flask answers a missing JSON body with an HTML 400 instead of the documented
  `{"error": ...}` envelope. Parity is measured against that number, not against
  an assumed 18/18.
- For `report-service`, parity means the existing test suite passes on the new
  runtime (`make test-report`), the jar still builds (`make build-report`), and
  the report lifecycle still works end to end through the gateway
  (`tests/api/test_audit_analytics_report_flow.py`).

`main` keeps the "before" state, so the demo is repeatable and the comparison is
always available.

---

<a id="part-1"></a>
## Part 1 — Devin Does the Modernization

<a id="act-1"></a>
### Act 1 — Inventory the modernization debt

Start with the assessment an architect would otherwise do by hand: what is
actually out of date, what is coupled to what, and what order the changes have to
happen in.

```
In Cognition-Partner-Workshops/otterworks, produce a modernization
inventory for the application layer and write it to
MODERNIZATION_INVENTORY.md at the repo root.

Cover:
1. services/report-service — read pom.xml and UPGRADE_GUIDE.md, then
   verify each claim against the source. For every upgrade axis list the
   current version, the target version, the exact files and imports that
   change, and what depends on it happening first (for example Spring
   Boot 3 requires Java 17 and the jakarta namespace).
2. services/search-service — the only Flask service in the repo. List
   every endpoint, its Flask-specific construct (Blueprint, jsonify,
   current_app.config, before_request hooks), and the FastAPI equivalent,
   using services/document-service as the in-repo reference.
3. The guardrails that currently exempt the legacy service: the Trivy
   --skip-dirs entry in .github/workflows/security-scan.yml, the note in
   .trivyignore, the skipped step in the security-scan Makefile target,
   and the java-version pin in the report-service job of
   .github/workflows/ci.yml.

For each item give the verification command that proves it is done.
Output a table ordered by execution sequence, with a risk column, and
cite file paths and line numbers as evidence.
```

Expected: a sequenced, evidence-cited plan — 11 upgrade axes for the Java
service with the dependency ordering made explicit, an endpoint-by-endpoint
translation map for the Python service, and the list of CI/scanner exemptions
that only exist because the legacy service could not pass them (there are more
than the guides mention: the Trivy `--skip-dirs` argument appears three times).
The inventory should also flag what *not* to touch — the planted chaos branch in
`services/search-service/app/api/search.py` is a lab feature that carries over to
FastAPI unchanged. With DeepWiki
over the repo, Devin typically maps an unfamiliar estate this way in minutes
(coverage depends on repo structure). Save the inventory as a Knowledge note so
the sessions in the following acts start from the same shared context.

<a id="act-2"></a>
### Act 2 — Upgrade the legacy Java service

The core beat for the Java half. This is 11 interlocking changes across 25 source
files where the *middle* of the upgrade does not compile — the reason these
upgrades stall in a backlog.

```
Upgrade services/report-service in Cognition-Partner-Workshops/
otterworks from Java 8 / Spring Boot 2.5.14 to Java 17 / Spring Boot
3.2.x, following the 11 axes in services/report-service/UPGRADE_GUIDE.md
in the order that guide recommends.

Include:
- pom.xml: Java 17 source/target, Boot 3.2.x parent, and current
  versions for Apache POI, Commons IO, Guava, OpenCSV, and Mockito.
- javax.* -> jakarta.* across every source and test file; remove the
  explicit javax.servlet-api dependency.
- SecurityConfig.java: replace WebSecurityConfigurerAdapter with a
  @Bean SecurityFilterChain, and antMatchers/authorizeRequests with
  requestMatchers/authorizeHttpRequests. Keep the same permitted paths.
- SpringFox 3.0 -> springdoc-openapi 2.x: delete the Docket bean in
  config/SwaggerConfig.java and convert the @Api/@ApiOperation/@ApiModel
  annotations in the controller and model classes.
- iText 5.5.13.3 -> OpenPDF in service/PdfReportGenerator.java (license
  cleanup), and commons-lang 2.6 -> commons-lang3.
- JUnit 4 -> JUnit 5 and Mockito 3 -> 5 in src/test, with
  maven-surefire-plugin 3.x.
- Re-admit the service to the estate's guardrails: drop every
  --skip-dirs services/report-service argument in
  .github/workflows/security-scan.yml (three invocations: the two
  PR-gating steps and the full-baseline job), update the .trivyignore
  comment,
  un-skip the report-service step in the security-scan Makefile target,
  and set java-version to 17 in the report-service job of
  .github/workflows/ci.yml.

Do not change any endpoint path, request/response field, or generated
report format. Run `make test-report` and `make build-report` after each
axis and report which axis broke what. Finish with: the list of remaining
`import javax.` matches under src/ (expected: none outside javax.sql),
the final test output, and a CVE before/after summary for the upgraded
dependencies.
```

Expected: one branch where the compile is clean, `make test-report` reports
`Tests run: 44, Failures: 0` on Java 17, the PDF/CSV/Excel generators produce the
same artifacts, and the security carve-outs are gone — plus a per-axis account of
what broke mid-upgrade. Two of those breakages are the ones the guide does not
list, and they are the reason this work needs an engineer rather than a
find-and-replace:

- **Spring 6 rejects the HTTP client.** `config/AppConfig.java` builds a
  `RestTemplate` on Apache HttpComponents 4;
  `HttpComponentsClientHttpRequestFactory` in Spring 6 takes an hc5 client and
  no longer exposes `setReadTimeout(int)`. The fix is `httpclient5` plus timeouts
  moved onto the connection pool config.
- **OpenPDF has no `BaseColor`.** The iText 5 → OpenPDF swap is billed as a
  package rename, but `service/PdfReportGenerator.java` has to move to
  `java.awt.Color` with the same RGB values to keep the PDF layout identical.

The last prompt bullet is the part teams undervalue: the upgrade is only finished
when the service stops needing an exemption from the estate's own scanners and
CI. Trivy over the service goes from 131 findings (10 CRITICAL / 56 HIGH) to 80
(6 CRITICAL / 29 HIGH) — most of it from the Boot 2.5.14 → 3.2.5 transitive tree,
with the remainder needing a further Boot 3.3+ bump, which is the next
quarter's ticket rather than a blocker. On the PR, the proof is the checks
themselves: `report-service` now green on Java 17, `dependency-scan` green with
no `--skip-dirs` exemption, and `api-flow-tests` green through the gateway.

<a id="act-3"></a>
### Act 3 — Translate Flask to FastAPI against the contract

The Python half is a rewrite of the framework layer, not a version bump — and the
contract is what keeps it honest. The OpenAPI document is the spec, and the
contract suite is the enforcement.

```
Translate services/search-service in Cognition-Partner-Workshops/
otterworks from Flask to FastAPI, following
services/search-service/TRANSLATION_GUIDE.md and using
services/document-service (app/api/documents.py) as the in-repo FastAPI
reference for structure and dependency injection.

The contract is shared/openapi/search-service.yaml and it does not
change. Preserve exactly:
- every path, method, query parameter, header, and status code, including
  the trailing-slash form /api/v1/search/
- the error envelope: 400 with {"error": "..."} — not FastAPI's default
  422 with {"detail": [...]}
- /api/v1/search/suggest returning 200 with empty suggestions for a
  prefix shorter than 2 characters
- /health returning {"status": "alive", "service": "search-service"} and
  /health/ready returning 503 with
  {"ready": false, "reason": "meilisearch_unavailable"} when MeiliSearch
  is unreachable
- the four search_service_* Prometheus metric families with their label
  sets, and the structlog event names (search_executed,
  advanced_search_executed, api_document_indexed)
- X-User-ID tenant scoping on every search endpoint

Convert Blueprints to APIRouters, replace jsonify with Pydantic response
models, make the handlers async, keep the synchronous MeiliSearch SDK
calls off the event loop, and move the SQS consumer from a background
thread to an asyncio task. Update requirements.txt (drop flask,
flask-cors, gunicorn; add fastapi and uvicorn[standard]) and the
Dockerfile entrypoint.

Verify by running the service and then, unmodified:
  SEARCH_SERVICE_URL=http://localhost:8087 \
    pytest tests/contract/test_search_contract.py -v
plus `cd services/search-service && pytest`. Report the contract results
before and after any fixes, and diff `curl localhost:8087/metrics |
grep search_service_` against the Flask version.
```

Expected: an ASGI service with the same wire behavior, the contract suite green
without a single assertion edited (18 passed, up from the Flask baseline's 16 —
the translation *fixes* the two missing-body cases where Flask itself violated
the documented envelope), the 41 existing unit tests still passing, and
`/metrics` output with identical HELP/TYPE lines and label sets.

<a id="act-4"></a>
### Act 4 — What the gates catch that a diff review does not

FastAPI's defaults are *better* than Flask's for a new service and *wrong* for
this one. Three specific traps sit in this translation, and none of them is
visible as a suspicious-looking diff.

**1. The error envelope (caught by the contract).** Declared as typed query
parameters, `q` / `page` / `size` make FastAPI answer a bad request with `422`
and `{"detail": [{"loc": ["query", "q"], ...}]}` — where the spec documents
`400` and `{"error": "Query parameter 'q' is required"}`. The fix is to accept
the parameters as raw strings and parse them, with a
`RequestValidationError` handler emitting the documented envelope as a backstop.
The fix goes in the service, not the test: the test encodes what the SPA's search
page and every other caller already depend on.

**2. The trailing slash.** Flask ran with `strict_slashes=False`; FastAPI answers
`/api/v1/search` with a redirect unless both variants are registered (the
no-slash one with `include_in_schema=False` so the generated spec stays clean).

**3. Metrics that quietly stop counting failures (caught by Devin Review, not by
the contract).** The natural translation of Flask's `after_request` metrics hook
is a Starlette `BaseHTTPMiddleware` that increments
`search_service_requests_total` after `await call_next(request)`. That records
nothing when a handler raises: Starlette's `ServerErrorMiddleware` sits outside
user middleware, so the counter line never runs. Flask's `wsgi_app` did record
those 500s. Nothing in the contract suite fails — the endpoints all still behave
— but the estate's `search-suggest-500` chaos scenario and the 5xx alert in
`observability/grafana/provisioning/alerting/alert-rules.yml` are driven by that
exact counter, so they would have gone flat during a real outage. The reviewer
flagged it with the alert query as evidence; the fix wraps `call_next` in
`try/except`, records the 500 sample, and re-raises.

That is the layering worth taking away: the contract suite gates the wire
behavior, and automated review on the PR catches the operational regression the
contract cannot see.

<a id="act-5"></a>
### Act 5 — Fan the remaining debt out in parallel

The two moves in this thread are the flagships, not the whole backlog. The
remaining work is independent per service, which is exactly the shape that fans
out: one parent session spawns a child per unit, each on its own branch, each
opening its own verified PR.

```
Act as the orchestrator for an application-modernization wave across
Cognition-Partner-Workshops/otterworks, using child Devin sessions to
parallelize the work. Use MODERNIZATION_INVENTORY.md from the earlier
assessment session as the shared plan.

Spawn one child session per row. Give each child the repo, its own
branch, the rule that no endpoint path or response field may change, and
the verification command that must be green before it reports done:

1. report-service: Java 17 / Spring Boot 3.2 upgrade (11 axes in
   services/report-service/UPGRADE_GUIDE.md) — verify with
   `make test-report` and `make build-report`.
2. search-service: Flask -> FastAPI translation
   (services/search-service/TRANSLATION_GUIDE.md) — verify with
   tests/contract/test_search_contract.py against the running service.
3. Contract audit: reconcile every response field name in
   shared/openapi/*.yaml against the implementing service and the
   frontend callers in frontend/client-app/src/lib/api.ts, and report
   naming inconsistencies (for example notificationId vs
   notification_id) without changing behavior yet.
4. Dependency CVE sweep: run `make security-scan` and remediate the
   CRITICAL/HIGH findings that do not require a major-version framework
   jump, keeping .trivyignore entries only where a major upgrade is
   genuinely required.
5. Test coverage for the modernized services: run `make test-coverage`
   and raise coverage on services/search-service and
   services/document-service, adding tests that pin current behavior
   rather than asserting new behavior.

Monitor the children until each row's verification is green. Summarize
the results and call out every behavioral divergence a child caught.
```

Each child runs on its own machine with its own branch, so the rows never
collide, and the parent reports one rollup. The same fan-out pattern is how a
whole-estate upgrade wave gets run in a week instead of a quarter.

One scoping rule matters here: the estate already has a
`!dep_sweep` procedure for routine dependency and EOL upgrades that deliberately
excludes `services/report-service` as a separately handled legacy exercise. Rows
1 and 2 are that exception; rows 3-5 are the routine sweep. Keeping the two apart
is what stops a fan-out from producing five PRs that fight over the same files.

---

<a id="part-2"></a>
## Part 2 — Confirm It in the Live Product

Green tests are the gate; the running product is the proof. Deploy the verified
branch to its own ephemeral tenant and compare it against the golden app
side by side.

Push the verified work as a `demo-<id>` branch. That triggers
`.github/workflows/cd-tenant.yml`, which builds only the services the push
actually changed, deploys through the ops dashboard, and gives the tenant its own
hostname:

```bash
git push origin HEAD:demo-modernize
# -> tenant t-modernize at https://t-modernize.demo.otterworks.app
```

Then walk the comparison:

**1. The estate is healthy.** Open `/api/health` on the tenant — the gateway
reports `{"status":"healthy","service":"web-app"}`, same as
<https://t-main.otterworks.app/api/health>.

**2. Search still behaves — now on FastAPI.** Open `/search` in the user app and
type a query. The results list and the type-ahead suggestions come from
`/search` and `/search/suggest` in `frontend/client-app/src/lib/api.ts` — the
same calls, now served by an ASGI app. Open the same page on
<https://t-main.otterworks.app/search> (still Flask) and confirm they look
identical. Then open `/docs` on the translated service: FastAPI's generated
OpenAPI UI is the free upgrade that came with holding the contract.

**3. Reports still generate — now on Java 17.** The report lifecycle runs through
the gateway at `/api/v1/reports` (`401` unauthenticated, which is the route
answering). `tests/api/test_audit_analytics_report_flow.py` exercises create →
fetch → list → download → delete against it; run `make test-api-flows` against
the tenant and show the report flow test passing.

**4. Operations sees no change.** Open `/admin` → **Health** on the tenant: the
per-service health grid shows `report-service` and `search-service` healthy
alongside the nine services nobody touched. That grid — not a green test log — is
the answer to "did the upgrade destabilize anything."

**5. The before is untouched.** `t-main` tracks `main` and is perpetual: it still
runs Java 8 and Flask, so the comparison stays available for the next run. Never
inject chaos or destructive changes into `t-main` — ephemeral `demo-<id>` /
`workshop-<id>` tenants are where experiments live.

---

<a id="confidence"></a>
## Confidence = Programmatic Verification

The gates that make a modernization PR trustworthy here:

- **Contract tests** — `tests/contract/test_search_contract.py` validates a live
  service against `shared/openapi/search-service.yaml`: paths, status codes,
  error envelopes, readiness reason strings, and JSON schemas resolved from the
  spec.
- **Per-service suites** — `make test-report` (Maven/JUnit) and
  `cd services/search-service && pytest` prove the business logic survived the
  framework change.
- **Black-box flow tests** — `make test-api-flows` (`tests/api/`) drives both
  services through the Go gateway the way the frontend does.
- **CI on the modernized runtime** — `.github/workflows/ci.yml` builds only the
  services a PR changed; the `report-service` job flipping from
  `java-version: '8'` to `17` is itself part of the deliverable.
- **Security scanning with no carve-out** — `security-scan.yml` fails a PR only
  on *newly introduced* CRITICAL/HIGH findings, and the modernized service no
  longer needs the `--skip-dirs` exemption.
- **Devin Review** — an automated reviewer on every PR, plus the human review the
  PR feedback loop is built around.

A modernization is done when the estate's own gates accept the service without
special-casing it.

---

<a id="always-on"></a>
## Always-On: Event-Driven and Scheduled

The same work runs without a human starting it:

- **On a CI failure.** A failing build on a modernization branch is an event; an
  Automation hands the job logs to a session that pushes a fix to the same
  branch.
- **On a new advisory.** `security-scan.yml` runs weekly, and
  `sast-auto-remediate.yml` already wires scanner findings to a Devin webhook
  that opens a fix PR and escalates to an issue after a bounded number of
  attempts. Dependency drift becomes a queue that drains itself.
- **On a schedule.** A recurring session re-runs the contract suite against every
  service with an OpenAPI spec and reports drift between the spec and the
  implementation — before a caller finds it.

Point the same procedure at a shared playbook and a Knowledge note, and the
upgrade standard is enforced across services instead of living in one engineer's
head.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Application modernization is two distinct problems, and Devin does both.** A runtime/framework upgrade (Java 8 → 17, Boot 2.5 → 3.2, `javax` → `jakarta`) is 11 interlocking changes where the middle does not compile; a language/framework translation (Flask → FastAPI) is a rewrite of the framework layer that has to land on the same wire contract. This thread runs one of each against a live estate.
- **The gates layer, and each one catches what the other cannot.** FastAPI's default `422`/`{"detail": ...}` validation response is plausible, invisible in a diff review, and a breaking change for existing callers — the contract suite fails it. The ASGI metrics middleware that silently stops counting 500s passes every contract test — automated PR review caught that one, with the affected alert query as evidence.
- **Record the baseline before claiming parity.** The Flask service on `main` fails 2 of its own 18 contract tests; "the suite is green" only means something measured against that number.
- **An upgrade is not done until the exemptions are gone.** The finish line includes deleting the three Trivy `--skip-dirs` carve-outs, un-skipping the Makefile scan step, and flipping the CI `java-version` pin, so the service rejoins the estate's scanners and pipelines instead of staying quarantined — and the finding count drops from 131 to 80 on the way.
- **Nothing user-visible moved, and you can see that in a browser.** The modernized branch deploys to its own tenant, and the search page and report flow behave the same as the golden app at t-main — which keeps the "before" available for the next run.
- **This scales by fan-out, not by heroics.** One assessment session produces the plan; child sessions take a row each (upgrade, translation, contract audit, CVE sweep, coverage) on their own branches with their own verification commands, and the parent reports one rollup.
- **It layers with infrastructure migration.** This thread changes what the code is; the [AWS cloud-native demo](./aws-cloud-native-modernization-demo.md) changes where it runs. Same estate, same verification bar, and the two waves can run against the same repo.
