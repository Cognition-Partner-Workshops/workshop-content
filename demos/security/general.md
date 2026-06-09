# Security Remediation with Devin — General Demo

**Other variants:** [Desktop + Cloud](general.platform.md) | [CLI variant](general.local.md)

Devin operates as a background security agent — scanning, triaging, fixing,
and re-scanning in a closed loop. The pattern is scanner-agnostic: Trivy,
SonarCloud, Semgrep, Snyk, or any tool that produces parseable output. It
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
CI workflow / webhook triggers Devin via v3 API
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

2. **CI workflow or webhook triggers Devin** — a GitHub Actions job (or
   equivalent) calls the Devin v3 API with the scan results and a remediation
   prompt. The prompt includes the branch, the scanner output, and
   instructions scoped to the repo's language and package manager.

3. **Devin triages findings, applies fixes** — Devin reads the scan output,
   identifies the correct manifest files (`package.json`, `Gemfile`,
   `go.mod`, `Cargo.toml`, `requirements.txt`, etc.), upgrades vulnerable
   dependencies or fixes code-level issues, and runs the affected service
   tests before pushing.

4. **Push triggers re-scan** — the fix commit triggers the same CI pipeline.
   The scanner re-runs. If findings are resolved, CI goes green and the PR
   is ready to merge. If findings persist, the loop repeats.

5. **Escalation policy** — after a configurable number of attempts
   (`MAX_FIX_ATTEMPTS`), the workflow stops calling Devin and opens a GitHub
   Issue labeled `security` and `needs-human-review`. The right humans get
   notified; Devin does not loop forever.

### Bot-loop prevention

Every event-driven pipeline needs guardrails:

- **Author check** — skip PRs authored by `devin-ai-integration[bot]` to
  prevent self-triggering
- **Attempt counter** — track Devin's commits on the branch and stop after
  the configured maximum
- **Concurrency groups** — prevent duplicate sessions from spawning on the
  same PR
- **One-time remediation** — for scanners like SonarCloud that fire
  `check_run` events, use a comment-based check to prevent re-triggering

---

<a id="running-it-live"></a>
## Running It Live

### Set up the event-driven pipeline

Paste this into Devin to create a scanner-agnostic remediation workflow on
any repository:

```
Create a GitHub Actions workflow called
sast-auto-remediate.yml on the <repo-name> repo that:

1. Triggers on pull_request events (opened, synchronize)
   for branches other than those authored by
   devin-ai-integration[bot].

2. Runs a security scan using <scanner-name> (e.g.,
   Trivy, Semgrep, or Snyk) targeting HIGH and CRITICAL
   severity findings.

3. Parses the scan output and posts a PR comment
   summarizing findings by service/directory.

4. If findings exist and fewer than 2 fix attempts have
   been made, calls the Devin v3 API to create a
   remediation session on the same branch with a prompt
   scoped to the repo's languages and package managers.

5. If findings persist after 2 attempts, opens a GitHub
   Issue labeled "security" and "needs-human-review"
   with the full findings summary.

The workflow should include bot-loop prevention
(author check + attempt counter) and concurrency groups
to prevent duplicate sessions.
```

### Trigger the pipeline

Open a PR as a human author on the target repo. Watch the CI checks tab:

1. The scan job runs and finds vulnerabilities
2. The findings are posted as a PR comment
3. The Devin API is called — a remediation session starts
4. Devin pushes a fix commit, triggering a re-scan
5. If findings are resolved, CI goes green

### Burn down an existing backlog

For repos with an existing vulnerability backlog, paste this prompt:

```
Review the security scan results for <repo-name>.
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
<repo-name> repository.

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
each following the same remediation playbook.

---

<a id="the-verification-loop"></a>
## The Verification Loop

The closed loop is the core value of event-driven security remediation:

1. **Same CI that found the problem re-scans after Devin's fix** — no manual
   verification step for dependency upgrades or well-documented fixes.

2. **Converging loop** — each iteration reduces the finding count. When
   findings reach zero, CI goes green automatically.

3. **Bounded iterations** — the attempt counter ensures the loop terminates.
   After the configured maximum, the workflow escalates to humans.

4. **Dual-scanner support** — different scanners can run in parallel on the
   same PR. Trivy catches dependency CVEs instantly; SonarCloud catches
   code-level issues after analysis. Both route to Devin through the same
   v3 API.

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
   scan step; the Devin API integration and escalation logic stay the same.

3. **Polyglot at scale** — one pipeline handles multiple languages. Devin
   reads the correct manifest and runs the right test command for each
   ecosystem.

4. **Closed-loop verification** — the same CI that found the problem
   re-scans after Devin's fix. No manual verification step.

5. **Guardrails built in** — bot-loop prevention, concurrency groups,
   attempt counters, and human escalation after max attempts.

6. **Team-based operation** — the pipeline, knowledge, and playbooks are
   shared across the organization. One engineer sets up the workflow; every
   subsequent PR benefits automatically.

---

For a complete worked example using Trivy + SonarCloud on a polyglot
monorepo, see
[Event-Driven SAST Remediation](use-cases/event-driven-sast-remediation-demo.md).
