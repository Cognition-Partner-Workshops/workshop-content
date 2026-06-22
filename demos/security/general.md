# Security Remediation with Devin — General Demo

**Other variants:** [Desktop + Cloud](general.platform.md) | [CLI variant](general.local.md)

Devin operates as a background security agent — scanning, triaging, fixing,
and re-scanning in a closed loop. The pattern is scanner-agnostic: Trivy,
SonarCloud, Semgrep, Snyk, or most tools that produce parseable output. It
works across most languages and repository layouts.

<a id="toc"></a>
## Table of Contents

- [The Pattern](#the-pattern)
- [Running It Live](#running-it-live)
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

1. **Scanner detects vulnerabilities** — dependency CVEs, code quality issues,
   SAST findings, license violations. The scanner runs as part of CI (on
   `pull_request`, `push`, or `check_run` events) or on a schedule.

2. **Devin Automation triggers a remediation session** — a
   [Devin Automation](https://docs.devin.ai/product-guides/automations)
   fires on the event you configure: a GitHub `check_run` failure, a PR
   comment, a new GitHub Issue, a webhook from an external scanner, or a
   schedule. You define the trigger and conditions once; Devin starts a
   Cloud session automatically each time the event fires, with the scan
   output and a remediation prompt scoped to the repo's language and
   package manager.

3. **Devin triages findings, applies fixes** — Devin reads the scan output,
   identifies the correct manifest files (`package.json`, `Gemfile`,
   `go.mod`, `Cargo.toml`, `requirements.txt`, etc.), upgrades vulnerable
   dependencies or fixes code-level issues, and runs the affected service
   tests before pushing.

4. **Push triggers re-scan** — the fix commit triggers the same CI pipeline.
   The scanner re-runs. If findings are resolved, CI goes green and the PR
   is ready to merge. If findings persist, the loop repeats.

5. **Escalation policy** — after a configurable number of attempts, the
   automation's invocation limit stops firing new sessions. The final
   session opens a GitHub Issue labeled `security` and
   `needs-human-review`. The right humans get notified; Devin does not
   loop forever.

### Automation safeguards

[Devin Automations](https://docs.devin.ai/product-guides/automations)
include built-in controls:

- **Invocation limit** — cap how many times the automation fires per time
  window (e.g., at most 10 per hour) to prevent runaway loops
- **ACU limit** — set a maximum compute budget per session to prevent
  runaway remediation
- **Trigger conditions** — filter which events fire the automation (e.g.,
  only `conclusion = failure`, only specific repos or branches)
- **Network policy** — restrict which external hosts automation sessions
  can reach

---

<a id="running-it-live"></a>
## Running It Live

### Set up the scanner in CI

Paste this into Devin to create a scanner workflow on the otterworks repo:

```
Create a GitHub Actions workflow called security-scan.yml
on the Cognition-Partner-Workshops/otterworks repo that:

1. Triggers on pull_request events (opened, synchronize).
2. Runs a Trivy scan targeting HIGH and CRITICAL severity
   findings.
3. Reports results as a GitHub check run.
4. Posts a PR comment summarizing findings by service
   directory.
```

### Create the Devin Automation

Navigate to **Automations** in the Devin web app. Describe what you want
in the chat input:

```
When a security scan check run fails on
Cognition-Partner-Workshops/otterworks, start a Devin
session that:

1. Reads the scan findings attached to the check run.
2. Triages HIGH and CRITICAL findings by service directory.
3. Applies fixes — dependency upgrades or code changes —
   in the correct manifest or source file.
4. Runs each affected service's tests.
5. Pushes the fix to the same branch.

Cap at 2 invocations per PR. Set an ACU limit of 50 per
session.
```

The automation fires each time the scanner reports findings. Devin handles
remediation automatically — no custom CI job needed to call the API.

### Trigger the pipeline

Open a PR as a human author on the otterworks repo. Watch the CI checks
tab:

1. The scan workflow runs and finds vulnerabilities
2. The findings are posted as a PR comment
3. The Devin Automation fires — a remediation session starts
4. Devin pushes a fix commit, triggering a re-scan
5. If findings are resolved, CI goes green

### Burn down an existing backlog

For repos with an existing vulnerability backlog, paste this prompt:

```
Review the security scan results for otterworks.
Triage all HIGH and CRITICAL findings by severity.
For each finding:

1. Identify the affected file and the fix (dependency
   upgrade, code change, or configuration update).
2. Check out a branch from main.
3. Apply the fix in the correct manifest/source file.
4. Run the affected service's tests.
5. Push the fix.

Group related fixes per service into single PRs when
possible. Skip findings in test files.
```

---

<a id="scaling-with-child-sessions"></a>
## Scaling with Child Sessions

For enterprise-scale remediation — many findings across many services or
repositories — use parent-child orchestration. The parent session triages
findings and launches one child session per service for parallel
remediation.

```
You are coordinating a security remediation across the
Cognition-Partner-Workshops/otterworks repository.

Run the security scan and capture the output. Create a
SECURITY_BACKLOG.md listing all CRITICAL and HIGH
findings organized by service or directory.

Then launch parallel child sessions — one per affected
service — with scoped instructions:
- Each child works only on its assigned service directory
- Each child upgrades the vulnerable dependency, runs
  the service tests, and pushes to the same branch
- After all children complete, summarize results in
  REMEDIATION_SUMMARY.md
```

This pattern scales to multi-repo remediation as well: the parent session
iterates over a list of repositories and spawns a child session per repo,
each following the same remediation playbook. For a full walkthrough of
multi-repo parallel orchestration, see
[Portfolio-Scale Remediation](use-cases/portfolio-scale-remediation-demo.md).

---

<a id="the-verification-loop"></a>
## The Verification Loop

The closed loop is the core value of event-driven security remediation:

1. **Same CI that found the problem re-scans after Devin's fix** — no manual
   verification step for dependency upgrades or well-documented fixes.

2. **Converging loop** — each successful iteration reduces the finding count.
   When findings reach zero, CI goes green automatically.

3. **Bounded iterations** — the attempt counter ensures the loop terminates.
   After the configured maximum, the workflow escalates to humans.

4. **Custom automation triggers** — you define what fires the automation:
   a scan check run, a PR comment, a new GitHub Issue, or a webhook from
   an external scanner. Aggregate signal in your trigger layer; the
   automation kicks off Devin with full context each time.

5. **Audit trail** — every scan result, Devin session link, and escalation
   issue is recorded in the PR timeline. The full remediation history is
   visible in one place.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **Devin as a background security agent** — not a tool you open, but a
   service that responds to CI events. Developers commit code; Devin handles
   security remediation.

2. **Scanner-agnostic architecture** — the pattern works with most scanners
   that produce parseable output or GitHub `check_run` events. Swap the
   scan step; the Devin Automation and escalation logic stay the same.

3. **Polyglot at scale** — one pipeline handles multiple languages. Devin
   reads the correct manifest and runs the right test command for each
   ecosystem.

4. **Closed-loop verification** — the same CI that found the problem
   re-scans after Devin's fix. No manual verification step.

5. **Safeguards built in** — automation invocation limits, ACU caps,
   trigger conditions, and human escalation after max attempts.

6. **Team-based operation** — the pipeline, knowledge, and playbooks are
   shared across the organization. One engineer sets up the workflow; every
   subsequent PR benefits automatically.

---

For a complete worked example using Trivy + SonarCloud on a polyglot
monorepo, see
[Event-Driven SAST Remediation](use-cases/event-driven-sast-remediation-demo.md).
