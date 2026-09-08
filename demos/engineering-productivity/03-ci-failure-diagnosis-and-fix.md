# CI/CD Failure Diagnosis and Fix — Red Pipeline to Documented Root Cause

The `CI Pipeline` workflow is red on the branch. The failing job is the Go
`api-gateway` service and the step is `go test -race`. Devin reads the Actions
log rather than asking someone to paste it, isolates the data race the detector
reported, fixes the shared state, adds a test that fails under `-race` without
the fix, reruns the pipeline, and writes up the root cause in the PR.

## Table of Contents

- [Quick Start](#quick-start)
- [Before](#before)
- [Part 1 — Read the Failure Where It Happened](#part-1)
- [Part 2 — Fix, Prove, Rerun](#part-2)
- [Part 3 — Runtime Follow-Through](#part-3)
- [Part 4 — Event-Driven Variant](#part-4)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Branch `workshop-<attendee_id>` from `workshop` is pushed and its `CI Pipeline`
run has finished red (see the [README](README.md#quick-start)). Paste this into
a new Devin session:

```
In Cognition-Partner-Workshops/otterworks, the CI Pipeline workflow is failing on branch workshop-<attendee_id>. Use the GitHub Actions run for the latest push to that branch: identify the failing job and step, pull the job log, and quote the exact failure (for the api-gateway job it is the go test -race step). Diagnose the root cause in services/api-gateway/internal/proxy/router.go and the tests in services/api-gateway/internal/proxy/router_test.go, explain why the race detector fires and why go vet and go build did not catch it. Fix it so that the per-route upstream error counter and the UpstreamErrorCounts snapshot are safe under concurrent requests while keeping the request_id field on 502 responses, and add or adjust a test that fails with go test -race before the fix and passes after. Run go vet ./... and go test -race ./... in services/api-gateway locally, push to workshop-<attendee_id>, wait for the CI Pipeline run and confirm the api-gateway job is green. In the PR description, targeting the workshop branch, include a root-cause section with the log excerpt, the reason, and the fix, plus links to the failing and passing runs.
```

---

<a id="before"></a>
## Before

- `workshop` carries a recent change to `services/api-gateway/internal/proxy/router.go`
  that counts upstream failures per route (`recordUpstreamError`,
  `UpstreamErrorCounts`) and adds `request_id` to 502 bodies. The counter is a
  plain package-level `map[string]int` written from the proxy `ErrorHandler`
  and the circuit-breaker path and read by the snapshot function.
- `services/api-gateway/internal/proxy/router_test.go` exercises unreachable
  upstreams concurrently, so `go test -race` (the exact command in
  `.github/workflows/ci.yml`, job `api-gateway`) reports `WARNING: DATA RACE`
  and fails the job. `go vet` and `go build` pass — the failure only exists at
  test time under the race detector.
- Every push to `workshop-<attendee_id>` runs `CI Pipeline` (per-service jobs
  gated by `detect-changes`) and `cd-tenant.yml`, which deploys the branch to
  the attendee's tenant regardless of CI status — so the racy gateway is also
  what is running at `https://api-t-<attendee_id>.otterworks.app`.

---

<a id="part-1"></a>
## Part 1 — Read the Failure Where It Happened

The session should start in GitHub Actions, not in the editor. Expect it to:

- List runs for the branch, pick the latest `CI Pipeline` run, and identify
  `api-gateway` as the failed job and `go test -race -coverprofile=coverage.out ./...`
  as the failed step.
- Quote the race report from the log — two goroutines, one writing in
  `recordUpstreamError`, one writing or reading in `recordUpstreamError` /
  `UpstreamErrorCounts`, with file:line references into `router.go`.
- State plainly why static checks passed: an unsynchronized map is valid Go;
  the race is a runtime property that only the detector observes.

If the session starts by opening `router.go` and reasoning from the code alone,
ask it to attach the log excerpt first — the point is that the evidence came from
the pipeline.

---

<a id="part-2"></a>
## Part 2 — Fix, Prove, Rerun

Acceptable fixes are a `sync.Mutex`/`sync.RWMutex` around the map or
`sync.Map`/atomic counters, as long as `UpstreamErrorCounts()` still returns a
copy and the 502 body keeps `request_id`. What matters more is the proof:

- A test that hammers `recordUpstreamError` and `UpstreamErrorCounts` from many
  goroutines, which Devin shows failing under `-race` on the old code and
  passing on the new.
- `go vet ./...` and `go test -race ./...` green locally in `services/api-gateway`.
- A push, then the session waits on the new `CI Pipeline` run and confirms the
  `api-gateway` job is green — it should link both the red and the green run
  in the PR body.

---

<a id="part-3"></a>
## Part 3 — Runtime Follow-Through

`cd-tenant.yml` redeploys the gateway on the same push. Confirm the behavior
that motivated the original change still works on the tenant — a 502 from a
missing upstream carries a request ID you can grep in the pod log:

```
curl -s https://api-t-<attendee_id>.otterworks.app/api/v1/reports/does-not-exist -H "Authorization: Bearer $TOKEN"
```

Expected on an unreachable route: `{"error":"service unavailable","target":"...","request_id":"..."}`.
With cluster read access, `kubectl -n otterworks-<attendee_id> logs deploy/api-gateway | grep <request_id>`
shows the matching `proxy error` line — the log correlation the change was for.

---

<a id="part-4"></a>
## Part 4 — Event-Driven Variant

Instead of pasting the prompt, a Devin Automation on the `workflow_run`
(`conclusion: failure`) event for `CI Pipeline` can start the same session for
every red run on a `workshop-*` branch. The automation prompt is the same
one-liner with the run URL substituted from the event payload:

```
In Cognition-Partner-Workshops/otterworks, the CI Pipeline run at {{run_url}} on branch {{branch}} failed. Identify the failing job and step from the job logs, quote the failure, find the root cause in the affected service, fix it with a regression test, run that service's CI commands locally, push to the same branch, and confirm the rerun is green. Target the PR at the workshop branch and include a root-cause section with links to the failing and passing runs. If the failure is infrastructure or a flaky external dependency rather than code, do not change code: rerun the job once and report what you found instead.
```

The last sentence is the important one — the automation should know when *not*
to change code.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The diagnosis started from the Actions log and the race detector's report, not
  from reading the diff — the same place an on-call engineer would start.
- The fix came with a test that reproduces the failure under `-race`, and the
  PR links the red run and the green run as evidence.
- The pipeline and the deployed tenant are the same push; Devin verified the
  intended behavior (request-ID correlation) survived on the running service.
- Wired to the `workflow_run` event, this is a standing response to red CI,
  with an explicit instruction to stop and report when the cause is not code.
