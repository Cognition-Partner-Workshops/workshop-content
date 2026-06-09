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
CI workflow / webhook triggers Devin via v3 API
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

2. **CI workflow or webhook triggers Devin** — for event-driven pipelines,
   this runs in Cloud. For interactive triage, you start Devin from the CLI.

3. **Devin triages findings, applies fixes** — Devin reads the scan output,
   identifies the correct manifest files, and applies fixes per language.

4. **Push triggers re-scan** — the fix commit triggers the same CI pipeline.
   If findings are resolved, CI goes green.

5. **Escalation policy** — after a configurable number of attempts, the
   workflow opens a GitHub Issue for human review.

### Bot-loop prevention

- **Author check** — skip PRs authored by `devin-ai-integration[bot]`
- **Attempt counter** — stop after `MAX_FIX_ATTEMPTS`
- **Concurrency groups** — prevent duplicate sessions
- **One-time remediation** — comment-based check for `check_run` scanners

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
devin -- Triage the Trivy findings in scan.json. For
  each one, tell me the affected file, the CVE, and the
  recommended fix. Group by service directory.
```

Devin reads the scan output and responds with a structured triage summary.
For straightforward fixes, ask Devin to apply them directly:

```
devin -- Fix the HIGH and CRITICAL Trivy findings in
  services/collab-service/package.json and
  services/search-service/requirements.txt.
  Upgrade the vulnerable dependencies to the
  recommended versions and run each service's tests.
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
/handoff Create a GitHub Actions workflow called
  sast-auto-remediate.yml on the <repo-name> repo that:

  1. Triggers on pull_request events (opened, synchronize)
     for branches other than those authored by
     devin-ai-integration[bot].

  2. Runs a security scan using <scanner-name> targeting
     HIGH and CRITICAL severity findings.

  3. Parses the scan output and posts a PR comment
     summarizing findings by service/directory.

  4. If findings exist and fewer than 2 fix attempts
     have been made, calls the Devin v3 API to create a
     remediation session on the same branch.

  5. If findings persist after 2 attempts, opens a
     GitHub Issue labeled "security" and
     "needs-human-review".

  Include bot-loop prevention (author check + attempt
  counter) and concurrency groups.
```

The `/handoff` command packages the conversation context and creates a
Cloud session. The CLI prints the session URL; the pipeline runs
autonomously from there.

### Burn down an existing backlog

For repos with an existing vulnerability backlog, combine CLI triage with
Cloud remediation:

```
devin -- Review the Trivy scan results for <repo-name>.
  Create a SECURITY_BACKLOG.md listing all CRITICAL and
  HIGH findings organized by service directory.
```

Once the triage is complete, hand off bulk remediation to Cloud:

```
/handoff Using the SECURITY_BACKLOG.md in <repo-name>,
  fix all HIGH and CRITICAL findings. Group related
  fixes per service into single PRs. Run each service's
  tests before pushing. Skip findings in test files.
```

---

<a id="scaling-with-child-sessions"></a>
## Scaling with Child Sessions

For enterprise-scale remediation, hand off to Cloud and let the parent
session spawn children. Start a session and hand off:

```
/handoff You are coordinating a security remediation
  across the <repo-name> repository.

  Run the security scan and capture the output. Create a
  SECURITY_BACKLOG.md listing all CRITICAL and HIGH
  findings organized by service or directory.

  Then launch parallel child sessions — one per affected
  service — with scoped instructions:
  - Each child works only on its assigned service
    directory
  - Each child upgrades the vulnerable dependency, runs
    the service tests, and pushes to the same branch
  - After all children complete, summarize results in
    REMEDIATION_SUMMARY.md
```

Child sessions run on their own Cloud VMs. The parent monitors progress
and handles failures or escalations.

---

<a id="the-verification-loop"></a>
## The Verification Loop

1. **Same CI that found the problem re-scans after Devin's fix** — no manual
   verification step for dependency upgrades or well-documented fixes.

2. **Converging loop** — each iteration reduces the finding count. When
   findings reach zero, CI goes green.

3. **Bounded iterations** — the attempt counter ensures the loop terminates.
   After the configured maximum, the workflow escalates to humans.

4. **CLI for spot checks** — after Cloud remediation completes, use the CLI
   to verify locally:

   ```
   devin -- Check if there are any remaining HIGH or
     CRITICAL Trivy findings in this repo. Summarize
     what was fixed and what remains.
   ```

5. **Audit trail** — every scan result, Devin session link, and escalation
   issue is recorded in the PR timeline.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **CLI for triage, Cloud for pipelines** — use `devin` in your terminal
   for interactive scan triage and quick fixes. Use `/handoff` to transfer
   event-driven remediation pipelines to Cloud.

2. **Scanner-agnostic architecture** — the pattern works with any scanner.
   Run the scan locally, pass findings to Devin, or let CI trigger via
   the v3 API.

3. **Terminal-native workflow** — no browser required for triage. Start
   Devin in your project directory and work interactively.

4. **Closed-loop verification** — CI re-scans after every fix. Verify
   locally with the CLI for spot checks.

5. **Guardrails built in** — bot-loop prevention, concurrency groups,
   attempt counters, and human escalation.

6. **Cloud handoff for scale** — event-driven pipelines, scheduled scans,
   and child session fan-out all run in Cloud. The CLI is your on-ramp
   via `/handoff`.

---

For a complete worked example using Trivy + SonarCloud on a polyglot
monorepo, see
[Event-Driven SAST Remediation](use-cases/event-driven-sast-remediation-demo.md).
