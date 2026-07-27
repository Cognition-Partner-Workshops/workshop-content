# Production Incident Response on a Polyglot Platform — Event-Driven SRE Demo

A single-thread demo showing Devin working an incident on the OtterWorks
polyglot platform the way an on-call engineer does: a Grafana alert fires, a
webhook opens a Devin session with the alert payload, Devin reproduces the
failure against the local stack, traces the root cause across a service
boundary (the symptom is in Kotlin, the injected malformed payload is created
by the Ruby admin service), lands a fix PR with
a regression test, completes the runbook, writes the postmortem, and leaves
behind a Knowledge note so the next occurrence is triaged faster.

The trigger wiring already exists in the repo — Grafana Unified Alerting →
webhook → `admin-service` → Devin v3 API. This demo runs it end to end.

<a id="toc"></a>
## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [The Trigger Wiring](#trigger-wiring)
- [Before and After](#before-after)
- [Part 1 — The Alert Fires and the Session Opens Itself](#part-1)
- [Part 2 — Reproduce Against the Local Stack](#part-2)
- [Part 3 — Root Cause and the Language Boundary](#part-3)
- [Part 4 — Fan Out: Event Contract Audit with Child Sessions](#part-4)
- [Part 5 — The Fix PR and the Regression Test](#part-5)
- [Part 6 — Devin Review on Both Sides of the PR](#part-6)
- [Part 7 — The Postmortem Artifact](#part-7)
- [Part 8 — The Knowledge Note That Shortens the Next One](#part-8)
- [What Still Needs a Human](#human-in-the-loop)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Bring up the local stack with observability, then seed it:

```bash
cd otterworks
make infra-up      # postgres, redis, localstack, meilisearch,
                   # prometheus, grafana, jaeger, otel-collector, fluent-bit
make up seed=1     # 13 application services + seed data
```

Surfaces used in this demo:

| Surface | URL | Notes |
|---------|-----|-------|
| Grafana | `http://localhost:3001` | user `admin`, password `otterworks` |
| Prometheus | `http://localhost:9090` | scrape interval 15s |
| Jaeger | `http://localhost:16686` | distributed traces |
| Admin dashboard | `http://localhost:4200` | Incidents page + Demo Controls |
| Admin service API | `http://localhost:8089` | alert ingest, chaos, incidents |

For the hands-free path in Part 1, `admin-service` needs `DEVIN_API_KEY` and
`DEVIN_ORG_ID` set (both are read from the environment in
`docker-compose.yml`), plus `ALERT_WEBHOOK_SECRET` and `CHAOS_SECRET`.

---

<a id="repository"></a>
## Repository

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) —
  a collaborative file storage and document editing platform built as a
  polyglot microservice mesh. `services/` holds 12 service directories
  spanning Go (`api-gateway`), Java (`auth-service`, `report-service`,
  `legacy-portal`), Rust (`file-service`), Python (`document-service`,
  `search-service`), Node.js (`collab-service`), Kotlin
  (`notification-service`), Scala (`analytics-service`), Ruby
  (`admin-service`), and C# (`audit-service`); `frontend/` holds an Angular
  admin dashboard and a React client app. `observability/` carries six Grafana
  dashboards, Prometheus scrape config and alert rules, Grafana Unified
  Alerting rules and contact points, OpenTelemetry collector config, and
  Fluent Bit logging config. `docs/runbooks/` holds seven runbooks, several
  with unfinished sections.

Services communicate over REST through the Go gateway and asynchronously over
SNS/SQS. The async path is where this incident lives.

---

<a id="trigger-wiring"></a>
## The Trigger Wiring

Four files make this incident hands-free. Read them before starting so the
automation is not a black box:

| File | Role |
|------|------|
| `observability/prometheus/prometheus.yml` | Scrapes the gateway, services, frontends, OTel collector, and Redis/Postgres exporters every 15s |
| `observability/grafana/provisioning/alerting/alert-rules.yml` | Grafana Unified Alerting rules in the `OtterWorks` folder, including `NotificationConsumerProcessingErrors` (uid `notification-consumer-errors`, severity `critical`) |
| `observability/grafana/provisioning/alerting/contact-points.yml` | Webhook receiver `otterworks-webhook-receiver` → `POST http://admin-service:8089/api/v1/admin/alerts/ingest`, `Authorization: Bearer $ALERT_WEBHOOK_SECRET` |
| `services/admin-service/app/controllers/api/v1/admin/alerts_controller.rb` | `#ingest` accepts the Grafana payload, maps firing alerts to `Incident` records, deduplicates against open incidents, and calls `DevinSessionService` when auto-investigate is on |

The alert rule that drives this thread evaluates:

```
sum(rate(notifications_processing_errors_total{job="notification-service"}[2m]))
```

The payload Grafana posts is its standard Unified Alerting body — `status`,
and an `alerts` array carrying `labels.alertname`, `labels.severity`,
`labels.affected_service`, `annotations.summary`,
`annotations.description`, `annotations.runbook_url`, and `startsAt`. The
controller turns those fields into the incident title, severity, affected
service, and description, then
`services/admin-service/app/services/devin_session_service.rb` posts to the
Devin v3 API with a prompt that embeds the incident details and the service
map. `DEVIN_API_KEY` and `DEVIN_ORG_ID` come from the environment; the session
ID and URL are written back onto the `Incident` record
(`services/admin-service/app/models/incident.rb`) and rendered on the
Incidents page.

The auto-investigate switch is Redis-backed and lives behind
`GET`/`PUT /api/v1/admin/settings/auto_investigate`
(`services/admin-service/app/services/admin_settings_service.rb`). With it
off, the alert still creates an incident and a human presses **Launch Devin**
(`POST /api/v1/admin/incidents/:id/trigger_session`). With it on, nobody
presses anything.

---

<a id="before-after"></a>
## Before and After

| | Detection → first hypothesis | Artifacts left behind |
|---|---|---|
| **Before** | Alert pages a human; the human opens Grafana, finds the failing consumer, reads Kotlin they may not own, then goes looking for who produces the messages. The cross-service hop is the expensive part. | A resolved incident and a Slack thread |
| **After** | The webhook opens a session with the alert payload already in it. Devin reproduces, traces the producer/consumer contract across services, and reports a root cause before the on-call engineer has finished reading the page. | Fix PR with a regression test, completed runbook, postmortem, Knowledge note, and an event-contract audit across the async fleet |

The unit of improvement is MTTR — specifically the *triage* segment, the
window between "alert fires" and "we know which service and which line". On a
polyglot mesh that segment is dominated by cross-language reading, and it is
the segment that a cloud agent can start on at 03:00 with nobody watching.
Toil drops in the same motion: the runbook and postmortem that normally get
deferred are produced as part of the incident, not after it.

---

<a id="part-1"></a>
## Part 1 — The Alert Fires and the Session Opens Itself

Turn auto-investigate on:

```bash
curl -X PUT http://localhost:8089/api/v1/admin/settings/auto_investigate \
  -H 'Content-Type: application/json' \
  -d '{"enabled": true}'
```

Inject the failure. The notification consumer's parser is switched to strict
mode by a Redis chaos flag:

```bash
curl -X POST http://localhost:8089/api/v1/admin/chaos \
  -H 'Content-Type: application/json' \
  -H "X-Chaos-Secret: $CHAOS_SECRET" \
  -d '{"service": "notification-service", "scenario": "consumer_strict_schema"}'
```

`services/admin-service/app/controllers/api/v1/admin/chaos_controller.rb` sets
`chaos:notification-service:consumer_strict_schema` in Redis with a 600-second
TTL and starts `ChaosProbeService` to generate traffic so the metric moves.
On an EKS tenant the equivalent is
`./scripts/inject-bug.sh <ATTENDEE_ID> notification-schema`, which writes the
same Redis key with a `CHAOS_TTL` that defaults to 3600 seconds.

Open Grafana at `http://localhost:3001` and select the **OtterWorks Chaos
Scenarios** dashboard
(`observability/grafana/dashboards/chaos-scenarios.json`). The
*Notification Service - Consumer Processing Errors* row starts climbing, and
*SQS Queue Depth (Approximate)* climbs with it — messages fail to parse, are
never deleted, and reappear after the visibility timeout.

Within the rule's evaluation window the alert transitions to firing, Grafana
posts to the contact point, and the Incidents page at
`http://localhost:4200` shows a new critical incident for
`notification-service` **with a Devin session link already attached**. Nobody
opened Devin. The session prompt built by `devin_session_service.rb` carries
the alert summary, the severity, the affected service, and the service map.

This is the part that cannot happen in an editor: the work starts from a
metric crossing a threshold, not from a person typing.

---

<a id="part-2"></a>
## Part 2 — Reproduce Against the Local Stack

Follow the session link from the incident card. To drive the same
investigation from a fresh session, paste:

```
You are on call for the OtterWorks platform
(Cognition-Partner-Workshops/otterworks). A critical Grafana alert
NotificationConsumerProcessingErrors is firing, defined in
observability/grafana/provisioning/alerting/alert-rules.yml with the
expression:

  sum(rate(notifications_processing_errors_total{job="notification-service"}[2m]))

Reproduce the failure against the local stack, do not fix anything yet:

1. Bring the stack up with `make infra-up` then `make up seed=1`.
2. Confirm the symptom from telemetry, not from the code: query
   Prometheus at localhost:9090 for the alert expression above and for
   the SQS queue depth series used by the "SQS Queue Depth
   (Approximate)" panel in
   observability/grafana/dashboards/chaos-scenarios.json.
3. Capture the failing log lines from the notification-service
   container.
4. Identify the exact code path that emits the
   notifications.processing.errors counter, with file and line.

Report a reproduction summary as a markdown table with columns
Signal | Source | Observed value | What it rules in or out.
```

Devin brings the stack up, reads the metric series, pulls
`docker compose logs notification-service`, and lands on
`services/notification-service/src/main/kotlin/com/otterworks/notification/consumer/SqsConsumer.kt`
— the `processingErrorsCounter` registered as
`notifications.processing.errors`, incremented on the path where
`parseMessage` returns `null`.

The reproduction summary distinguishes *consumer is down* (it is not — the
consumer keeps polling and the health endpoint is green) from *consumer cannot
parse what it is being handed*. Queue depth climbing while the service reports
healthy is the tell, and it is the reason a dashboard-only triage sends people
in the wrong direction.

---

<a id="part-3"></a>
## Part 3 — Root Cause and the Language Boundary

The symptom is in Kotlin. The payload that breaks it is produced by the Ruby
admin service, and the same investigation exposes a second, latent contract
drift between the Rust producer and the Kotlin consumer.

```
Continue the OtterWorks incident investigation. The failing code path
is parseMessage in
services/notification-service/src/main/kotlin/com/otterworks/notification/consumer/SqsConsumer.kt,
which deserializes into SqsNotificationMessage from
services/notification-service/src/main/kotlin/com/otterworks/notification/model/NotificationEvent.kt.

Find where the messages on that queue are produced. Cross the service
boundary:

1. Read services/file-service/src/events.rs and list every field the
   Rust FileEvent struct serializes onto SNS, including the camelCase
   renames and the fields that are skipped when None.
2. Diff that field set against the Kotlin SqsNotificationMessage data
   class field by field, distinguishing latent unknown-field drift from
   the malformed timestamp used by the active chaos probe.
3. Read the second consumer of the same events,
   services/search-service/app/services/sqs_consumer.py, and explain
   what _normalize_event has to do to handle the payload shapes it
   receives.
4. State which parser settings in SqsConsumer.kt make the difference
   between the tolerated state and the failing state.

Report as: (a) a field-by-field contract table with columns
Field | Rust producer | Kotlin consumer | Python consumer, (b) a
one-paragraph root cause statement naming the originating service and
the surfacing service, (c) the blast radius — which other consumers of
this topic are exposed to the same drift.
```

What Devin surfaces:

- `services/admin-service/app/services/chaos_probe_service.rb:117-128`
  sends a `file_shared` payload whose `timestamp` is an integer Unix epoch.
  The strict Kotlin parser rejects that integer where
  `SqsNotificationMessage.timestamp` is a `String`.
- `services/file-service/src/events.rs` serializes `FileEvent` with
  `#[serde(rename_all = "camelCase")]`, emits an RFC 3339 string timestamp,
  and conditionally emits `folderId`, `name`, `mimeType`, and `sizeBytes`.
- `SqsNotificationMessage` in the Kotlin model declares `eventType`,
  `fileId`, `ownerId`, `sharedWithUserId`, `documentId`, `commentId`,
  `userId`, `actorId`, `mentionedUserId`, and `timestamp` — and none of the
  optional file metadata fields the Rust producer sends.
- The consumer's normal parser is `Json { ignoreUnknownKeys = true;
  isLenient = true }`. That tolerance is the only thing that has been holding
  unknown file metadata fields out of the consumer's way. The chaos flag
  swaps in a parser with `ignoreUnknownKeys = false` and `isLenient = false`,
  and the integer timestamp becomes a hard deserialization failure on the
  probe payloads.
- `services/search-service/app/services/sqs_consumer.py` is corroborating
  evidence rather than a second bug: `_normalize_event` already branches
  between the snake_case-with-nested-payload shape emitted by the document
  service and the flat camelCase shape emitted by the file service. Two
  consumers, two ad-hoc normalizations, no shared contract.

The root cause statement is precise: the consumer fails because the Ruby
`ChaosProbeService` sends an integer timestamp where the Kotlin model requires
a string, and the Rust producer separately emits optional metadata fields the
Kotlin model never declared. Two producers, one consumer, no shared schema —
and lenient parsing hiding the gap. Fixing only the Kotlin service treats the
symptom.

An engineer who owns the Kotlin service typically does not own the Rust
service, and may not read Rust at all. This hop across an ownership and
language boundary is where incident triage on a polyglot platform actually
spends its time.

---

<a id="part-4"></a>
## Part 4 — Fan Out: Event Contract Audit with Child Sessions

One drifted contract implies others. Fan the question out instead of asking it
serially:

```
Coordinate an async event contract audit across the OtterWorks repo
(Cognition-Partner-Workshops/otterworks) using child sessions — one
child per service that publishes to or consumes from SNS/SQS.

Each child covers exactly one service directory under services/ and
reports:
- the event payload types it publishes or consumes, with file and line
- the field names and types on the wire, including serialization
  renames
- how it handles unknown or missing fields (tolerant, strict, or
  normalizing)

Aggregate the child results into docs/EVENT_CONTRACTS.md with:
- one table per SNS topic: Field | Producer type | Consumer types |
  Mismatch
- a "Drift Register" section listing every field a producer emits that
  at least one consumer does not declare, ranked by how many consumers
  are exposed
- a short section on which mismatches would become incidents if a
  consumer tightened its parser

Reference the notification-service and file-service mismatch already
identified as the worked example.
```

Each child reads one service in one language and reports on the same schema;
the parent aggregates. The output is a durable artifact — the platform's async
contract surface, written down once — rather than a per-incident finding that
evaporates when the incident closes. This is a fleet-wide read that no single
engineer would be asked to do at 03:00.

---

<a id="part-5"></a>
## Part 5 — The Fix PR and the Regression Test

```
Fix the OtterWorks notification failure path and make its event contract
explicit.

The active chaos path is
services/admin-service/app/services/chaos_probe_service.rb, which publishes
a file_shared payload with an integer timestamp. Separately,
services/file-service/src/events.rs publishes FileEvent fields
(folderId, name, mimeType, sizeBytes) that
SqsNotificationMessage in
services/notification-service/src/main/kotlin/com/otterworks/notification/model/NotificationEvent.kt
does not declare. The consumer survives only because parseMessage in
consumer/SqsConsumer.kt uses a tolerant parser.

Make the contract explicit rather than tolerated:
1. Align the Kotlin event model with the fields the Rust producer
   actually emits, keeping unknown-field tolerance as a deliberate
   forward-compatibility choice rather than an accident, and document
   that choice in a comment on the parser configuration.
2. Add regression tests to
   services/notification-service/src/test/kotlin/com/otterworks/notification/consumer/SqsConsumerTest.kt
   that parse (a) the malformed chaos-probe payload and (b) payloads
   byte-identical to what
   services/file-service/src/events.rs emits for file_uploaded and
   file_shared — including the SNS envelope form — and assert the
   expected strict/tolerant behavior and that the identity fields are
   populated.
3. Add a test in services/file-service/src/events.rs asserting the
   serialized field names of FileEvent, so a rename on the producer
   side breaks the producer's own test suite.
4. Complete the unfinished "Resolution Steps" and "Post-Incident"
   sections of docs/runbooks/notification-processing-failure.md with
   the commands you actually ran, including how to confirm queue depth
   recovering.

Verification gate: `cd services/notification-service && ./gradlew test`
and `cd services/file-service && cargo test` both green. Report the
test output in the PR description.
```

The regression test is the point. A fix that only edits the consumer leaves
the next producer change free to break it again; a serialization assertion on
the Rust side makes the contract enforceable from both ends, in both
languages, in CI. The runbook edit converts a one-time investigation into
something the next responder can execute.

Clear the chaos flag and confirm recovery:

```bash
curl -X DELETE http://localhost:8089/api/v1/admin/chaos \
  -H "X-Chaos-Secret: $CHAOS_SECRET"
```

Reset also resolves open incidents for the chaos-managed services, and the
Grafana alert transitions to resolved — which posts to the same webhook and
auto-resolves the incident record through
`AlertsController#resolve_incident`. The loop closes on the same wiring it
opened on.

---

<a id="part-6"></a>
## Part 6 — Devin Review on Both Sides of the PR

**Devin reviews the human's PR.** Producer-side changes are what start
incidents like this one, so put the review where the risk is. Open a PR on
`otterworks` that adds a field to `FileEvent` in
`services/file-service/src/events.rs` — the ordinary, reasonable-looking
change an engineer makes when a new file attribute is needed — and request a
Devin review on it.

The review to expect is not a style pass: it is "this struct is a published
contract, here are the consumers that deserialize it
(`services/notification-service/src/main/kotlin/com/otterworks/notification/consumer/SqsConsumer.kt`,
`services/search-service/app/services/sqs_consumer.py`), and here is which of
them declares the new field." That comment is only possible because the
reviewer read across repositories' worth of services, which is exactly what
the audit in Part 4 wrote down.

**The loop closes on Devin's own PR.** On the fix PR from Part 5, leave review
comments and let Devin iterate:

- *"The Kotlin test asserts the flat payload. Add the SNS envelope form too —
  `parseMessage` unwraps `SnsEnvelope` on the retry path and that branch is
  untested."*
- *"Do not widen the parser and the model at the same time without saying
  which one is the contract. State it in the runbook."*

Devin pushes follow-up commits to the same branch and replies on the threads.
The review conversation, not a chat window, is where the correction happens —
so the whole team sees the reasoning and the reviewer of record is a human.

---

<a id="part-7"></a>
## Part 7 — The Postmortem Artifact

```
Write the postmortem for the OtterWorks notification processing
incident as docs/postmortems/notification-consumer-schema-drift.md
(create the docs/postmortems directory). Base it on this session's
actual work, not on a template.

Include:
- Impact: what users lost (notifications for shared files and document
  updates) and for how long, derived from the alert firing and
  resolving timestamps
- Detection: the alert that fired, its expression, and the path from
  Prometheus scrape to Grafana rule to admin-service webhook to
  incident record
- Timeline: alert fired, session opened, reproduction confirmed, root
  cause identified, fix PR opened, alert resolved — with the commands
  and file paths at each step
- Root cause: the Ruby ChaosProbeService payload uses an integer timestamp
  while the strict Kotlin SqsNotificationMessage parser requires a string;
  note the separate producer/consumer schema divergence in
  services/file-service/src/events.rs and the lenient parsing that masks its
  unknown fields
- Contributing factor: no shared schema definition or contract test
  across the SNS event surface
- What went well / what was slow
- Action items as a checklist table with columns Action | Owner type |
  Linked artifact, referencing the fix PR, the regression tests, and
  docs/EVENT_CONTRACTS.md

Cross-link the postmortem from
docs/runbooks/notification-processing-failure.md.
```

The postmortem is written from the session's own record — the commands that
were run, the files that were read, the timestamps on the alert — so the
timeline is reconstructed rather than remembered. This is the artifact teams
most reliably skip when the incident is over and the pager is quiet.

---

<a id="part-8"></a>
## Part 8 — The Knowledge Note That Shortens the Next One

The last step is what makes the second occurrence cheaper than the first.

```
Draft a Devin Knowledge note for the OtterWorks repository, as
markdown I can paste into the Knowledge settings, that would let a
future session triage this class of incident faster.

Trigger description: when investigating a notification-service or
async event processing incident in
Cognition-Partner-Workshops/otterworks.

Body must include:
- the SNS/SQS event flow: which services publish, which consume, and
  the two payload shapes on the wire
- the producer of record for file events
  (services/file-service/src/events.rs) and the two consumers
  (notification-service Kotlin, search-service Python)
- the diagnostic order that worked: alert expression, then queue depth
  versus consumer health, then parser configuration and the chaos probe
  payload, then the producer struct
- the chaos flag that reproduces it
  (chaos:notification-service:consumer_strict_schema) and how to set
  and clear it
- pointers to docs/runbooks/notification-processing-failure.md,
  docs/postmortems/notification-consumer-schema-drift.md, and
  docs/EVENT_CONTRACTS.md

Keep it under 400 words and write it as guidance for an agent, not as
prose for a human reader.
```

Paste the result into a Knowledge note scoped to the repo. Combined with
DeepWiki over `otterworks` and the repo's own `AGENTS.md`, the next session
that opens from this alert starts at the diagnostic order that worked instead
of rediscovering it — that is the shared context layer doing its job. The
runbook, the postmortem, and `docs/EVENT_CONTRACTS.md` live in the repo where
humans read them; the Knowledge note is the same information addressed to the
agent.

Wire the loop shut by making the trigger reference it: when an alert includes
`annotations.runbook_url`, `AlertsController#build_description` passes it
through into the incident description, which becomes part of the prompt
`DevinSessionService` sends. The current notification alert does not set that
annotation; point it at the completed runbook so the next auto-opened session
starts with it.

---

<a id="human-in-the-loop"></a>
## What Still Needs a Human

Be explicit about the boundary, because for SRE it is the whole conversation.

**Production access.** In this demo Devin reproduces and fixes against the
local stack and a workshop EKS tenant, not production. The agent reads
production *signals* — the alert payload, and metrics or logs exposed through
read-only integrations — and writes code and documents. Granting an agent
mutating access to a production cluster is a separate decision with a separate
approval path, and nothing here assumes it.

**Mitigation versus remediation.** The fast path in a real incident is often a
rollback, a feature-flag flip, or a scale-out — actions with immediate
customer impact. Those stay with the on-call engineer. Devin's contribution is
the durable fix and the evidence behind it, running in parallel with human
mitigation rather than in place of it.

**Merge and deploy.** The fix PR is review-ready, not merged. A human approves
it, and the normal release path deploys it.

**Severity and comms.** Customer impact classification, status page updates,
and regulatory notification are judgment calls tied to accountability the
agent does not hold.

**Chaos as a stand-in.** The chaos flag makes a real code path fail on demand
by sending a malformed timestamp payload; it also enables an adjacent
strict-schema contract audit. It is not a guarantee that the same
investigation shape fits any incident. Coverage depends on how much of the
failure is visible in code and telemetry — infrastructure-level and capacity
incidents typically expose less to a code-reading agent.

**Where the value concentrates.** Triage, cross-service root cause,
regression tests, runbooks, postmortems, and contract audits — the reading and
writing work. Not the decision to fail over.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **The session opens itself.** A metric crosses a threshold, Grafana posts
   to `admin-service`, and a Devin session exists with the alert payload in
   it. No human is in the path between the alert and the start of the
   investigation — which is what makes it useful at 03:00.

2. **Cross-service root cause is the expensive part of MTTR.** The symptom
   was a Kotlin deserialization failure; the injected malformed payload came
   from the Ruby chaos probe. Devin also read the Rust producer and Python
   consumer to identify adjacent schema drift and produced a field-by-field
   contract table. That hop is what an agent removes from the triage window.

3. **The reproduction is telemetry-first.** Queue depth climbing while the
   consumer reports healthy is the signal that separates "service is down"
   from "service cannot parse what it is handed" — and it comes from
   Prometheus, not from reading code.

4. **Child sessions turn one finding into fleet coverage.** One drifted
   contract became `docs/EVENT_CONTRACTS.md`: one child per publishing or
   consuming service, aggregated into a drift register the team keeps.

5. **Devin Review sits where the risk is.** Reviewing a producer-side change
   against the list of consumers that deserialize it catches the next
   occurrence before it is an incident, and the same review loop closes on
   Devin's own fix PR in the PR conversation.

6. **The artifacts that normally get skipped get produced.** The completed
   runbook, the postmortem reconstructed from the session's own record, and
   the Knowledge note are all incident output, not follow-up tickets — so the
   toil backlog does not grow with each page.

7. **Team outcome, not individual productivity.** The measures the on-call
   rotation and its manager care about — triage time per incident, share of
   incidents with a written postmortem, runbook completeness, repeat-incident
   rate — move together, because the context is shared rather than held by
   whoever happened to be paged.
