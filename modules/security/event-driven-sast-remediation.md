# Event-Driven SAST Remediation

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Architecture](#architecture)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [timesheet-app](#timesheet-app)
- [uc-cve-remediation-regulatory-compliance](#uc-cve-remediation-regulatory-compliance)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-cve-remediation-regulatory-compliance](#uc-cve-remediation-regulatory-compliance)

---

## Challenge

Build an event-driven security architecture where SAST tools automatically scan user commits on new branches when PRs are opened by non-Devin contributors, then route the branch reference to Devin to remediate findings autonomously. This creates a closed-loop "scan → triage → fix → re-scan" pipeline where human developers commit code and Devin handles the security remediation.

This is an **enterprise integration pattern** — it demonstrates Devin operating as a background security agent triggered by CI events, not as a tool a developer manually invokes.

## Quick Start

Paste this prompt into Devin to get started immediately:

```
Analyze the existing .github/workflows/sast-scan.yml in
timesheet-app. This workflow triggers Devin to fix SonarQube
findings. Extend this pattern to create a new workflow called
sast-auto-remediate.yml that:
(1) triggers on PRs opened by users other than
    devin-ai-integration[bot],
(2) runs npm audit --json and Trivy container scan,
(3) if HIGH or CRITICAL findings are found, posts a PR
    comment summarizing the findings and triggers a Devin
    session via the Devin API to remediate them on the
    same branch,
(4) includes a re-scan step that verifies the fix.
Document the architecture in an ARCHITECTURE.md.
```

## Architecture

```
Developer opens PR
        ↓
GitHub Actions triggers SAST scan (OWASP DC / Trivy / SonarQube)
        ↓
Scan finds HIGH/CRITICAL findings?
    YES → GitHub Actions calls Devin API with:
          - branch ref
          - scan report artifact URL
          - remediation instructions
        ↓
Devin creates a fix commit on the same branch
        ↓
CI re-runs scan automatically (push trigger)
        ↓
Findings resolved? → PR is unblocked for merge
```

## Target Outcomes

- GitHub Actions workflow that runs SAST on PRs opened by non-Devin authors
- Conditional step that calls the Devin API when findings exceed a severity threshold
- Devin session that reads the scan report, remediates findings, and pushes a fix commit to the PR branch
- Re-scan passes after Devin's fix (closed-loop verification)
- `ARCHITECTURE.md` documenting the event-driven flow, API integration, and escalation policy

## What Participants Will Learn

- How to integrate Devin into a CI/CD pipeline as an autonomous remediation agent
- Event-driven architecture patterns: scan → triage → fix → verify
- Devin API usage for programmatic session creation
- How to filter PR authors to avoid infinite loops (Devin fixing Devin's own PRs)
- Security policy enforcement: blocking merges until SAST findings are resolved

## Devin Features Exercised

- Devin API (programmatic session triggering)
- Branch-aware remediation (working on an existing PR branch)
- SAST report interpretation (OWASP DC XML, Trivy JSON, SonarQube)
- GitHub Actions authoring with conditional logic
- Automated PR comment responses

## Difficulty

Advanced

## Estimated Time

90 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Has existing `sast-scan.yml` and `pr-checks.yml` workflows that already demonstrate a version of this pattern. Participants will study the existing implementation and extend it into a full event-driven remediation pipeline.

### Step 1: Paste into Devin

```
Analyze the existing .github/workflows/sast-scan.yml in
timesheet-app. This workflow triggers Devin to fix SonarQube
findings. Extend this pattern to create a new workflow called
sast-auto-remediate.yml that:
(1) triggers on PRs opened by users other than
    devin-ai-integration[bot],
(2) runs npm audit --json and Trivy container scan,
(3) if HIGH or CRITICAL findings are found, posts a PR
    comment summarizing the findings and triggers a Devin
    session via the Devin API to remediate them on the
    same branch,
(4) includes a re-scan step that verifies the fix.
Document the architecture in an ARCHITECTURE.md.
```

### Step 2: Research with Ask Devin

- *"How does the existing sast-scan.yml workflow in timesheet-app trigger Devin? What API endpoint does it use and what parameters does it pass?"*
- *"What's the best way to prevent an infinite loop where Devin's fix commit triggers another scan that triggers another Devin session?"*
- Use the analysis to design a robust event-driven pipeline with proper circuit breakers

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the existing CI/CD setup and the `sast-scan.yml` pattern. Map out the full event flow and identify where the architecture can be extended.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the workflow correctly filter PR authors? Is the Devin API call properly configured?
- **Leave a comment** asking Devin to add an escalation path: if Devin's fix doesn't resolve findings after 2 attempts, open a GitHub Issue and assign it to a human reviewer
- **Watch Devin respond** and push a follow-up commit with the escalation logic

### Key Takeaways

- **Closed-loop remediation**: The scan → fix → re-scan cycle runs without human intervention for straightforward findings
- **Event-driven triggering**: Devin responds to CI signals automatically — no manual invocation needed
- **Bot-loop prevention**: Filtering PR authors by `devin-ai-integration[bot]` prevents infinite remediation cycles
- **Escalation policy**: After a configurable number of fix attempts, the pipeline escalates to a human reviewer

---

## <a id="uc-cve-remediation-regulatory-compliance"></a>uc-cve-remediation-regulatory-compliance

**Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)

No existing CI workflows — build the entire event-driven SAST pipeline from scratch. Has OWASP Dependency-Check and SonarQube Gradle plugins pre-configured for scanning.

### Step 1: Paste into Devin

```
Create a complete event-driven security remediation pipeline
for uc-cve-remediation-regulatory-compliance. Build a GitHub
Actions workflow sast-auto-remediate.yml that:
(1) triggers on pull_request events from non-bot authors,
(2) runs ./gradlew dependencyCheckAnalyze and captures
    the report,
(3) parses the report for findings with CVSS >= 7.0,
(4) if critical findings exist, calls the Devin API to
    create a session with the prompt "Remediate the
    CRITICAL and HIGH CVEs found in the dependency check
    report at [artifact URL]. Push fixes to branch
    [branch-ref]. Re-run ./gradlew
    dependencyCheckAnalyze to verify.",
(5) includes a verification step that re-runs the scan
    after Devin's commit.
Add author filtering to prevent bot loops. Document the
full architecture in EVENT_DRIVEN_SECURITY.md.
```

### Step 2: Research with Ask Devin

- *"What's the best way to parse OWASP Dependency-Check XML output in a GitHub Actions workflow to extract findings above a CVSS threshold?"*
- *"How should the Devin API session be configured to work on an existing branch rather than creating a new one?"*
- Design the workflow to handle edge cases: what if the scan finds no issues? What if Devin can't fix them?

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the Gradle build system and which security scanning plugins are pre-configured. Plan the workflow around the available tooling.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the OWASP DC report parsing robust? Does the Devin API call include enough context for effective remediation?
- **Leave a comment** asking Devin to add Slack notification integration: post a message when the scan finds issues, when Devin starts remediating, and when remediation is complete
- **Watch Devin respond** and iterate on the notification integration

### Key Takeaways

- **Building from scratch**: Creating a full event-driven security pipeline in a repo with no existing CI demonstrates the pattern end-to-end
- **CVSS threshold gating**: Parsing OWASP DC reports for severity scores enables targeted remediation of the most critical findings
- **Devin API integration**: Programmatic session creation turns CI into an autonomous remediation engine
- **Verification loop**: The re-scan step confirms that Devin's fixes actually resolve the detected vulnerabilities

---

## Going Further

- **Webhook-driven automation**: Connect your SAST scanner (SonarQube, Snyk, Checkmarx) to Devin via webhooks so that findings trigger remediation sessions automatically — no CI workflow changes needed. The pattern is tool-agnostic: any scanner that produces a parseable report can drive the pipeline.
- **Divide and conquer with child sessions**: When a scan produces findings across multiple repositories or modules, use a parent Devin session to triage findings and spawn child sessions — one per repo or one per finding category — to remediate in parallel. Each child works on its own branch and opens its own PR.
- **Scheduled recurring analysis**: Configure scheduled Devin sessions (daily or weekly) to run SAST scans proactively, remediating new findings before they accumulate. This shifts security from reactive (respond to findings) to proactive (prevent accumulation). Combine with the shared context layer (Knowledge notes for remediation patterns, playbooks for consistent fix methodology) so that scheduled sessions apply organizational security standards automatically.
