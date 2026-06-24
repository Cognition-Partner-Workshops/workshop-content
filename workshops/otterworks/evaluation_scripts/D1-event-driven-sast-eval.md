# Evaluation: D1 — Event-Driven SAST Pipeline

## Blocking Criteria

- [ ] Participant can explain the complete Trivy path: PR → scan → findings → Devin webhook → fix → re-scan → verify/escalate
- [ ] Participant can explain bot-loop prevention and why it is critical
- [ ] Participant can explain the escalation path (MAX_FIX_ATTEMPTS → GitHub Issue)

## Recommended Criteria

- [ ] Participant also traced the SonarCloud path and can explain differences from Trivy path
- [ ] An extension was proposed or implemented (third scanner, improved Devin prompt, notifications)
- [ ] Participant can articulate the toolchain-agnostic pattern: `[Scanner] → webhook → Devin → PR`
- [ ] Participant discussed applying this pattern beyond security (compliance, quality gates, performance)
