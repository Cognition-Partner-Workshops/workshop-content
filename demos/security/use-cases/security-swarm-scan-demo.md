# Security Swarm Scan — Demo

Security Swarm uses Agentic MapReduce to scan repositories for
vulnerabilities. A coordinator builds a threat model, divides the codebase
among parallel workers, and aggregates findings into actionable PRs.

<a id="toc"></a>
## Table of Contents

- [Navigate to Security](#navigate-to-security)
- [Start a Scan](#start-a-scan)
- [Choose a Repository](#choose-a-repository)
- [Interactive Mode: Review the Threat Model](#interactive-mode)
- [Accept or Refine the Threat Model](#accept-or-refine)
- [Watch Findings Arrive](#watch-findings)
- [Act on Findings](#act-on-findings)
- [Set Up Auto Scan](#set-up-auto-scan)
- [Create a Profile](#create-a-profile)
- [Key Takeaways](#key-takeaways)

---

<a id="navigate-to-security"></a>
## Navigate to Security

Open the Devin web app and select **Security** from the left sidebar. This
is the entry point for Security Swarm — separate from the general session
interface.

<a id="start-a-scan"></a>
## Start a Scan

Click **Start scan**. You have two modes:

- **Interactive** — review and refine the threat model before workers begin
  investigating. Use this for the first scan of a repository or when you
  want to steer the investigation.
- **Automatic** — the coordinator builds the threat model and launches
  workers without pausing for review. Use this for recurring scans where
  the threat model is already established.

Select **Interactive** for this demo.

<a id="choose-a-repository"></a>
## Choose a Repository

Select the target repository from the list of connected repos. Security
Swarm works on any repository accessible through your organization's Git
connections.

<a id="interactive-mode"></a>
## Interactive Mode: Review the Threat Model

The coordinator analyzes the repository and builds a threat model. This
takes a few minutes — the coordinator reads the codebase structure,
identifies entry points, and maps attack surfaces.

When the threat model is ready, it is presented for review. The threat
model includes:

- **Attack surfaces** — API endpoints, authentication flows, data
  ingestion paths, external integrations
- **Vulnerability categories** — RCE, SQLi, SSRF, auth bypasses,
  memory-safety issues, DoS vectors, chained exploits
- **Investigation units** — how the codebase will be divided among workers

<a id="accept-or-refine"></a>
## Accept or Refine the Threat Model

Review the threat model and decide:

- **Accept** — proceed with the current threat model. Workers begin
  investigating immediately.
- **Refine** — provide feedback to adjust the threat model. Add areas of
  concern, remove false positives, or reprioritize investigation units.
  The coordinator updates the model and presents it again.

Once accepted, the coordinator maps the investigation units and spawns
parallel workers — one per unit.

<a id="watch-findings"></a>
## Watch Findings Arrive

Workers investigate their assigned units independently. As each worker
completes its investigation, findings appear in the Security Swarm
dashboard:

- **Finding severity** — CRITICAL, HIGH, MEDIUM, LOW
- **Vulnerability type** — the category from the threat model
- **Location** — file path and line number
- **Evidence** — the worker's analysis of why this is a vulnerability
- **Suggested fix** — the recommended remediation

Findings arrive incrementally as workers complete their units. The
dashboard updates in real time.

<a id="act-on-findings"></a>
## Act on Findings

For each finding, you can:

- **Accept the fix** — Security Swarm opens a PR with the remediation
- **Dismiss** — mark as false positive or accepted risk
- **Investigate further** — ask for deeper analysis of a specific finding

PRs opened by Security Swarm follow the standard Devin workflow — review
the diff, leave comments, and Devin iterates.

<a id="set-up-auto-scan"></a>
## Set Up Auto Scan

To run Security Swarm on a recurring schedule:

1. Navigate to the repository's Security Swarm page
2. Select **Auto Scan**
3. Choose a cadence (daily, weekly, or custom cron)
4. Select the scan mode (Interactive or Automatic — Automatic is typical
   for recurring scans)
5. Enable the schedule

Auto Scan runs on the configured cadence and delivers findings through the
same dashboard. New findings since the last scan are highlighted.

<a id="create-a-profile"></a>
## Create a Profile

Profiles save scan configurations for reuse:

1. After completing a scan (or from the Security Swarm settings), select
   **Save as Profile**
2. Name the profile (e.g., "Weekly API Security Scan")
3. The profile captures: repository, threat model adjustments, scan mode,
   and any refinements made during Interactive mode

Profiles can be applied to future scans or Auto Scan schedules. They
ensure consistent scan configurations across runs and team members.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Agentic MapReduce** — Security Swarm divides the repository among
  parallel workers for broad coverage and deep investigation, then
  aggregates findings into a consolidated view
- **Threat-model-driven scanning** — the coordinator builds a threat model
  first, ensuring investigation is targeted rather than exhaustive brute
  force. Interactive mode lets you review and refine before workers start
- **Auto Scan for continuous security** — recurring scans catch new
  vulnerabilities as code evolves, without manual initiation
- **Profiles for consistency** — save scan configurations so recurring
  scans and team members use the same threat model and settings
- **Standard PR workflow** — findings are remediated through PRs, fitting
  into existing code review processes
