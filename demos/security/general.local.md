# Security Remediation with Devin — CLI Demo

**Other variants:** [Cloud only](general.md) | [Desktop + Cloud](general.platform.md)

Devin operates as a background security agent — scanning, triaging, fixing,
and re-scanning in a closed loop. This variant starts from the terminal:
run scans locally, triage and fix interactively with the `devin` CLI, and
use `/handoff` to transfer long-running remediation pipelines to Cloud.

<a id="toc"></a>
## Table of Contents

- [The Pattern](#the-pattern)
- [Running It Live from the CLI](#running-it-live)
- [Scaling with Child Sessions](#scaling-with-child-sessions)
- [The Verification Loop](#the-verification-loop)
- [Key Takeaways](#key-takeaways)

---

<a id="the-pattern"></a>
## The Pattern

The universal closed-loop approach for event-driven security remediation:

```
Scanner detects vulnerabilities
        ↓
Devin Automation triggers a remediation session
        ↓
Devin triages findings, applies fixes per service/language
        ↓
Push triggers re-scan — closed loop
        ↓
Escalation: after N attempts, open an issue for human review
```

### Step by step

1. **Scanner detects vulnerabilities** — dependency CVEs, code quality
   issues, SAST findings, license violations.

2. **Devin Automation triggers a remediation session** — for event-driven
   pipelines, a [Devin Automation](https://docs.devin.ai/product-guides/automations)
   fires in Cloud. For interactive triage, you start Devin from the CLI.

3. **Devin triages findings, applies fixes** — Devin reads the scan output,
   identifies the correct manifest files, and applies fixes per language.

4. **Push triggers re-scan** — the fix commit triggers the same CI pipeline.
   If findings are resolved, CI goes green.

5. **Escalation policy** — after a configurable number of attempts, the
   automation's invocation limit stops firing. The final session opens a
   GitHub Issue for human review.

### Automation safeguards

[Devin Automations](https://docs.devin.ai/product-guides/automations)
include built-in controls:

- **Invocation limit** — cap how many times the automation fires per time
  window to prevent runaway loops
- **ACU limit** — set a maximum compute budget per session
- **Trigger conditions** — filter which events fire the automation (e.g.,
  only `conclusion = failure`, only specific repos)
- **Network policy** — restrict which external hosts automation sessions
  can reach

---

<a id="running-it-live"></a>
## Running It Live from the CLI

### Quick triage with the CLI

Start a Devin session with your scan results. Run your scanner first, then
pass the output to Devin:

```bash
trivy fs --severity HIGH,CRITICAL --format json . > scan.json
```

Then start Devin with the triage prompt:

```
devin -- Triage the Trivy findings in scan.json. For each one, tell me the affected file, the CVE, and the recommended fix. Group by service directory.
```

Devin reads the scan output and responds with a structured triage summary.
For straightforward fixes, ask Devin to apply them directly:

```
devin -- Fix the HIGH and CRITICAL Trivy findings in services/collab-service/package.json and services/search-service/requirements.txt. Upgrade the vulnerable dependencies to the recommended versions and run each service's tests.
```

### Hand off to Cloud for the full pipeline

The CLI handles one-off triage and quick fixes. For the full event-driven
pipeline — CI triggers, re-scans, escalation — hand off to Cloud using
the `/handoff` command inside a Devin session:

```
devin
```

Then within the session:

```
/handoff Remediate all HIGH and CRITICAL Trivy findings on the Cognition-Partner-Workshops/otterworks repo. Triage findings by service directory. For each finding, apply the fix (dependency upgrade or code change) in the correct manifest or source file. Run each affected service's tests before pushing. Group related fixes per service into single PRs.
```

The `/handoff` command packages the conversation context and creates a
Cloud session. The CLI prints the session URL; the remediation runs
autonomously from there.

For the full event-driven pipeline — where scans automatically trigger
remediation on every PR — set up a
[Devin Automation](https://docs.devin.ai/product-guides/automations) in
the Devin web app. Configure a GitHub `check_run` trigger on otterworks
and Devin starts a remediation session each time the scanner reports
findings.

### Burn down an existing backlog

For repos with an existing vulnerability backlog, combine CLI triage with
Cloud remediation:

```
devin -- Review the Trivy scan results for otterworks. Create a SECURITY_BACKLOG.md listing all CRITICAL and HIGH findings organized by service directory.
```

Once the triage is complete, hand off bulk remediation to Cloud:

```
/handoff Using the SECURITY_BACKLOG.md in otterworks, fix all HIGH and CRITICAL findings. Group related fixes per service into single PRs. Run each service's tests before pushing. Skip findings in test files.
```

---

<a id="scaling-with-child-sessions"></a>
## Scaling with Child Sessions

For enterprise-scale remediation, hand off to Cloud and let the parent
session spawn children. Start a session and hand off:

```
/handoff You are coordinating a security remediation across the Cognition-Partner-Workshops/otterworks repository. Run the security scan and capture the output. Create a SECURITY_BACKLOG.md listing all CRITICAL and HIGH findings organized by service or directory. Then launch parallel child sessions — one per affected service — with scoped instructions: each child works only on its assigned service directory, upgrades the vulnerable dependency, runs the service tests, and pushes to the same branch. After all children complete, summarize results in REMEDIATION_SUMMARY.md.
```

Child sessions run on their own Cloud VMs. The parent monitors progress
and handles failures or escalations.

---

<a id="the-verification-loop"></a>
## The Verification Loop

1. **Same CI that found the problem re-scans after Devin's fix** — no manual
   verification step for dependency upgrades or well-documented fixes.

2. **Converging loop** — each successful iteration reduces the finding count.
   When findings reach zero, CI goes green.

3. **Bounded iterations** — the attempt counter ensures the loop terminates.
   After the configured maximum, the workflow escalates to humans.

4. **CLI for spot checks** — after Cloud remediation completes, use the CLI
   to verify locally:

   ```
   devin -- Check if there are any remaining HIGH or CRITICAL Trivy findings in this repo. Summarize what was fixed and what remains.
   ```

5. **Audit trail** — every scan result, Devin session link, and escalation
   issue is recorded in the PR timeline.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **CLI for triage, Cloud for pipelines** — use `devin` in your terminal
   for interactive scan triage and quick fixes. Use `/handoff` to transfer
   event-driven remediation pipelines to Cloud.

2. **Scanner-agnostic architecture** — the pattern works with most scanners
   that produce parseable output. Run the scan locally, pass findings to
   Devin, or let a Devin Automation trigger on CI events.

3. **Terminal-native workflow** — no browser required for triage. Start
   Devin in your project directory and work interactively.

4. **Closed-loop verification** — CI re-scans after every fix. Verify
   locally with the CLI for spot checks.

5. **Safeguards built in** — automation invocation limits, ACU caps,
   trigger conditions, and human escalation after max attempts.

6. **Cloud handoff for scale** — event-driven pipelines, scheduled scans,
   and child session fan-out all run in Cloud. The CLI is your on-ramp
   via `/handoff`.

---

For a complete worked example using Trivy + SonarCloud on a polyglot
monorepo, see
[Event-Driven SAST Remediation](use-cases/event-driven-sast-remediation-demo.md).
