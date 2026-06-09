# Security Remediation with Devin — Desktop + Cloud Demo

**Other variants:** [Cloud only](general.md) | [CLI variant](general.local.md)

Devin operates as a background security agent — scanning, triaging, fixing,
and re-scanning in a closed loop. This variant runs from Devin Desktop:
delegate scan-fix tasks to Cloud, monitor remediation sessions in the Agent
Command Center, and review security fix PRs with one-click checkout in your
editor.

<a id="toc"></a>
## Table of Contents

- [The Pattern](#the-pattern)
- [Running It Live from Desktop](#running-it-live)
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
   issues, SAST findings, license violations. The scanner runs as part of
   CI or on a schedule.

2. **Devin Automation triggers a remediation session** — a
   [Devin Automation](https://docs.devin.ai/product-guides/automations)
   fires on the event you configure: a GitHub `check_run` failure, a PR
   comment, a new GitHub Issue, a webhook, or a schedule. The Cloud
   session runs on its own VM.

3. **Devin triages findings, applies fixes** — Devin reads the scan output,
   identifies the correct manifest files, upgrades vulnerable dependencies
   or fixes code-level issues, and runs the affected service tests.

4. **Push triggers re-scan** — the fix commit triggers the same CI pipeline.
   If findings are resolved, CI goes green.

5. **Escalation policy** — after a configurable number of attempts, the
   automation's invocation limit stops firing new sessions. The final
   session opens a GitHub Issue for human review.

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
## Running It Live from Desktop

### Set up the scanner from Desktop

Open Devin Desktop and create a new session. Paste the prompt below, then
delegate to Cloud — the Cloud agent runs on its own VM while you continue
working locally.

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
3. Applies fixes in the correct manifest or source file.
4. Runs each affected service's tests.
5. Pushes the fix to the same branch.

Cap at 2 invocations per PR. Set an ACU limit of 50 per
session.
```

The automation fires each time the scanner reports findings. Devin handles
remediation automatically.

### Monitor in the Agent Command Center

Once the Cloud session is running, open the Agent Command Center (Kanban
view) in Desktop. The session card shows:

- **Status** — in progress, waiting for review, or completed
- **PR link** — click to open the PR directly in your editor
- **Session timeline** — every scan result and fix attempt

Use the Kanban to track multiple remediation sessions across repos. Group
related sessions using **Spaces** — for example, a "Security Sprint" Space
that collects all active remediation work.

### Review security fix PRs in your editor

When Devin opens a PR, Desktop shows a notification. Use **one-click
checkout** to pull the branch into your editor and review the changes
locally:

1. Click the PR notification in Desktop
2. Desktop checks out the branch in your editor
3. Review the diff — Devin's changes are scoped to the affected manifests
   and source files
4. Run tests locally if needed, then approve and merge

### Burn down an existing backlog from Desktop

Create a session in Desktop with this prompt, then delegate to Cloud:

```
Review the security scan results for otterworks.
Triage all HIGH and CRITICAL findings by severity.
For each finding:

1. Identify the affected file and the fix.
2. Check out a branch from main.
3. Apply the fix in the correct manifest/source file.
4. Run the affected service's tests.
5. Push the fix.

Group related fixes per service into single PRs.
Skip findings in test files.
```

Monitor progress in the Agent Command Center as PRs appear.

---

<a id="scaling-with-child-sessions"></a>
## Scaling with Child Sessions

For enterprise-scale remediation, delegate the parent session from Desktop
to Cloud. The parent triages findings and spawns one child session per
service.

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

The Agent Command Center shows the parent session and all child sessions
as separate cards. Use Spaces to group them into a single view for tracking.

---

<a id="the-verification-loop"></a>
## The Verification Loop

1. **Same CI that found the problem re-scans after Devin's fix** — no manual
   verification step for dependency upgrades or well-documented fixes.

2. **Converging loop** — each iteration reduces the finding count. When
   findings reach zero, CI goes green.

3. **Bounded iterations** — the attempt counter ensures the loop terminates.
   After the configured maximum, the workflow escalates to humans.

4. **Desktop visibility** — the Agent Command Center shows the status of
   every remediation session. Failed sessions surface immediately; you can
   review the escalation issue directly in your editor.

5. **Audit trail** — every scan result, Devin session link, and escalation
   issue is recorded in the PR timeline.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **Desktop as your security command center** — delegate remediation to
   Cloud, monitor progress in the Agent Command Center, and review fix PRs
   with one-click checkout in your editor.

2. **Scanner-agnostic architecture** — the pattern works with most scanners
   that produce parseable output. Swap the scan step; the Devin Automation
   stays the same.

3. **Spaces for organization** — group related remediation sessions into
   Spaces for a unified view of your security sprint.

4. **Closed-loop verification** — CI re-scans after every fix. No manual
   verification step.

5. **Safeguards built in** — automation invocation limits, ACU caps,
   trigger conditions, and human escalation after max attempts.

6. **Cloud for project workflows, Local Agent for individual tasks** —
   project-based configurations and automations run on Cloud agents.
   Individual developer workflows — tight human-in-the-loop triage,
   subagents, agent skills — are accelerated with the Devin Local Agent
   in Desktop.


