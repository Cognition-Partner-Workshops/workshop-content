# Lab: Event-Driven SAST Pipeline

<a id="toc"></a>
## Contents

- [Objective](#objective)
- [Why This Matters](#why-this-matters)
- [Quick Start](#quick-start)
- [How the Pipeline Works](#how-the-pipeline-works)
- [The Lab](#the-lab)
- [Where to Look](#where-to-look)
- [What Done Looks Like](#what-done-looks-like)
- [Hints](#hints)
- [Key Takeaways](#key-takeaways)
- [Time Budget](#time-budget)

<a id="objective"></a>
## Objective

Trace, understand, and extend the OtterWorks event-driven security pipeline where SAST findings in pull requests automatically trigger Devin sessions for remediation — creating a closed-loop "find → fix → verify" cycle with zero human intervention.

## Why This Matters

This lab showcases the most powerful Devin enterprise pattern: **event-driven automation**. Instead of a human noticing a security finding, creating a ticket, assigning it, and waiting for someone to fix it, the pipeline detects → triggers → fixes → verifies in a continuous loop. This is the difference between "Devin as a tool you invoke" and "Devin as an always-on team member that responds to signals."

<a id="quick-start"></a>
## Quick Start

Read `docs/EVENT_DRIVEN_SECURITY.md` then trace `.github/workflows/sast-auto-remediate.yml` — document both the Trivy and SonarCloud paths end-to-end. Jump to [Step 1](#the-lab).

## How the Pipeline Works

OtterWorks has a working event-driven SAST remediation workflow (`.github/workflows/sast-auto-remediate.yml`) that runs two parallel scan paths on every PR:

1. **Trivy path:** Scans for dependency CVEs (HIGH + CRITICAL). If findings exist, triggers a Devin session via webhook to fix the vulnerable dependencies. Devin's fix push triggers a re-scan (closed loop). After `MAX_FIX_ATTEMPTS`, escalates to a GitHub Issue.
2. **SonarCloud path:** Runs a code quality scan. If the quality gate fails, triggers a one-time Devin session to address the findings.

Both paths include bot-loop prevention (skips PRs authored by `devin-ai-integration[bot]`) and escalation when automated remediation is insufficient.

## The Lab

### Step 1 — Trace the Flow (15 min)

Read the architecture documentation and trace the complete event chain:

```
Read docs/EVENT_DRIVEN_SECURITY.md and
.github/workflows/sast-auto-remediate.yml. Trace the
complete flow for both the Trivy and SonarCloud paths.
For each path, document:
1. What triggers the scan
2. How findings are detected
3. How the Devin webhook is called (URL, payload)
4. What context Devin receives in the prompt
5. How the closed loop works (fix → re-scan → verify)
6. How bot loops are prevented
7. What happens when remediation fails (escalation)
```

### Step 2 — Understand the Safeguards (10 min)

The pipeline has critical safeguards. Investigate:

```
In .github/workflows/sast-auto-remediate.yml, explain:
1. Why does the workflow skip PRs from
   devin-ai-integration[bot]?
2. What would happen WITHOUT this check?
3. How does MAX_FIX_ATTEMPTS work? What value is it
   set to?
4. The workflow uses the synchronize event — why is
   this important for the re-scan loop?
5. What is the escalation path when automated fixes
   don't resolve the finding?
```

### Step 3 — Propose an Extension (20 min)

Now extend the pipeline. Choose one:

**Option A — Add a Third Scanner Path:**
```
Read .github/workflows/sast-auto-remediate.yml and add
a third parallel job for secret scanning (e.g., using
gitleaks or trufflehog). Follow the same pattern as the
Trivy and SonarCloud jobs: scan → detect findings →
trigger Devin webhook → Devin remediates (rotates
secrets, removes hardcoded values). Include bot-loop
prevention and escalation.
```

**Option B — Improve the Devin Prompt:**
```
Read the Devin webhook payload in
sast-auto-remediate.yml. The prompt Devin receives could
include more context to improve fix quality. Propose
improvements: include the specific affected files, the
CVE details with links, which tests to run after fixing,
and the service's dependency file path. Update the
workflow to include this richer context.
```

## Where to Look

- `docs/EVENT_DRIVEN_SECURITY.md` — **Read this first.** Full architecture documentation with flow diagrams.
- `.github/workflows/sast-auto-remediate.yml` — The workflow implementation. Two jobs: `sast-scan` (Trivy) and `sonarcloud-gate` (SonarCloud).
- `.github/workflows/security-scan.yml` — Separate "scan and report" workflow. Compare with the "scan and fix" approach.
- `security/scanning/trivy-config.yaml` — Trivy configuration (severity thresholds, skip directories).
- `.trivyignore` — Suppressed CVEs respected by the auto-remediation workflow.
- `sonar-project.properties` — SonarCloud project configuration.
- `docs/CI_STRATEGY.md` — CI architecture context; section on SAST auto-remediation.

## What Done Looks Like

- [ ] Can explain the end-to-end flow: PR opened → scanner detects → Devin webhook → Devin fixes → re-scan → resolved or escalated
- [ ] Can explain bot-loop prevention and why it is critical
- [ ] Can explain the escalation path and MAX_FIX_ATTEMPTS
- [ ] Proposed or implemented one extension to the pipeline
- [ ] Can articulate the pattern: `[Event Source] → [Trigger] → [Devin Session] → [PR] → [Verify]` — and how this applies beyond security (incidents, compliance, quality gates)

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Read `docs/EVENT_DRIVEN_SECURITY.md` top to bottom — it is comprehensive. Then open the workflow YAML and match each section of the doc to the actual implementation.

### Hint 2 — The Webhook Call

Find the `curl` command in the workflow that calls the Devin API. Look at the JSON payload — what information does Devin receive? Is it enough to produce a good fix?

### Hint 3 — The Bigger Pattern

The event-driven pattern is toolchain-agnostic: swap Trivy for Snyk, SonarCloud for Semgrep, GitHub Actions for GitLab CI — the topology stays the same. The design pattern is: `[SAST Tool] → webhook → [Trigger Layer] → Devin → PR`.

## Key Takeaways

- Event-driven automation means Devin responds to signals, not manual prompts — this scales to hundreds of PRs
- Closed-loop verification (fix → re-scan → verify) is what separates automation from "fire and forget"
- Every automation needs safeguards: bot-loop prevention, max retries, escalation to humans
- The pattern is toolchain-agnostic — swap scanners freely, the topology stays the same

## Time Budget

- ~45-60 minutes for trace + safeguards + one extension
- Step 1 is the most important — understanding the flow is more valuable than coding an extension
