# Lab: Event-Driven SAST Pipeline

## Objective

Understand, trace, and extend the OtterWorks event-driven security pipeline where SAST findings in pull requests are automatically routed to Devin for remediation — creating a closed-loop "find → fix → verify" cycle with zero human intervention for straightforward vulnerabilities.

## What's Wrong

Nothing is broken — this lab is about understanding and extending a working automation pipeline. OtterWorks has an event-driven SAST remediation workflow (`sast-auto-remediate.yml`) that runs two parallel scan paths on every PR:

1. **Trivy path:** Scans for dependency CVEs (HIGH + CRITICAL). If findings exist, triggers a Devin session via webhook to fix the vulnerable dependencies. Devin's fix push triggers a re-scan (closed loop). After `MAX_FIX_ATTEMPTS`, escalates to a GitHub Issue.
2. **SonarCloud path:** Runs a code quality scan. If the quality gate fails, triggers a one-time Devin session to address the findings.

Both paths include bot-loop prevention (skips PRs authored by `devin-ai-integration[bot]`) and escalation when automated remediation is insufficient.

This pipeline demonstrates a key Devin enterprise pattern: **event-driven automation** where Devin acts as an always-on security engineer that responds to CI signals automatically.

## Where to Look

- `docs/EVENT_DRIVEN_SECURITY.md` — **Read this first.** Full architecture documentation with flow diagrams, scanner path details, bot-loop prevention, and configuration.
- `.github/workflows/sast-auto-remediate.yml` — The workflow implementation. Two jobs: `sast-scan` (Trivy) and `sonarcloud-gate` (SonarCloud). Both call the Devin webhook on findings.
- `.github/workflows/security-scan.yml` — The separate security scanning workflow (runs Trivy, npm audit, pip-audit, bundle-audit). Compare this with the SAST auto-remediation to understand the difference between "scan and report" vs. "scan and fix."
- `security/scanning/trivy-config.yaml` — Trivy configuration (severity thresholds, skip directories).
- `.trivyignore` — Suppressed CVEs. The auto-remediation workflow respects these suppressions.
- `sonar-project.properties` — SonarCloud project configuration for polyglot analysis.
- `docs/CI_STRATEGY.md` — CI architecture context; section on SAST auto-remediation.

## What Done Looks Like

- [ ] Can explain the end-to-end flow: PR opened → scanner detects finding → Devin webhook triggered → Devin fixes → push triggers re-scan → finding resolved (or escalated)
- [ ] Can explain bot-loop prevention: why PRs from `devin-ai-integration[bot]` are skipped, and how the `synchronize` re-scan still works on human PRs with Devin commits
- [ ] Can explain the escalation path: what happens after `MAX_FIX_ATTEMPTS` Trivy cycles, and why SonarCloud is one-time only
- [ ] Identified at least one improvement to the pipeline (e.g., add a third scanner path, improve the Devin prompt with more context, add Slack notifications on escalation, add SBOM generation)
- [ ] Optionally: proposed or implemented the improvement as a PR

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Read `docs/EVENT_DRIVEN_SECURITY.md` from top to bottom — it is the authoritative guide. Then open `.github/workflows/sast-auto-remediate.yml` and trace the flow: `sast-scan` job runs Trivy, parses findings, and calls the Devin webhook. The `sonarcloud-gate` job runs SonarCloud, checks the quality gate, and calls the Devin webhook if it fails.

### Hint 2 — Specific Direction

Look at the Devin webhook payload in the `sast-auto-remediate.yml` workflow. What context does Devin receive? Could the prompt be improved with more information (e.g., which specific files to look at, which tests to run after fixing, links to CVE details)?

### Hint 3 — Approach

Ask Devin: "Read `docs/EVENT_DRIVEN_SECURITY.md` and `.github/workflows/sast-auto-remediate.yml`. Explain the complete event-driven SAST remediation flow, including both the Trivy and SonarCloud paths. Then suggest 3 concrete improvements to the pipeline — things like adding a third scanner path (e.g., Semgrep for custom rules), improving Devin prompt context, or adding notifications."

## Time Budget

- ~20 minutes: Read the architecture doc and trace the workflow
- ~15 minutes: Understand bot-loop prevention and escalation
- ~20-30 minutes: Propose or implement one improvement

## Key Takeaways

- **Event-driven automation is the enterprise pattern** — Devin responds to CI signals, not manual prompts. This scales to hundreds of PRs.
- **Closed-loop verification** — Devin's fix push triggers a re-scan, creating an automated verify cycle.
- **Escalation is essential** — automated remediation is not guaranteed to work. The pipeline knows when to stop and ask a human.
- **Bot-loop prevention is subtle** — the system must distinguish "Devin's own PRs" from "Devin's commits on someone else's PR."
