# Event-Driven SAST Remediation — Facilitator Notes

## Prerequisites

- This module builds on Shift Left Security — participants should understand basic CI security scanning first
- The Devin API call requires an API key — have this pre-configured before the session
- The workflow must filter out PRs authored by `devin-ai-integration[bot]` to prevent infinite remediation loops

## Setup

- If Devin API access is not available, the "Devin API call" step can be simulated with a webhook or manual trigger
- Pre-verify that the existing `sast-scan.yml` workflow in timesheet-app is functional and that SonarQube Cloud integration is active

## Timing

- Allow 90 minutes for the full hands-on walkthrough
- Steps 1–2 typically take 45–60 minutes; Steps 3–4 are optional extensions
- The uc-cve-remediation-regulatory-compliance track (building from scratch) takes longer than the timesheet-app track (extending existing workflows)

## Key Hands-On Value

The primary value is showing Devin as an autonomous agent in a production-like pipeline, not as a tool someone manually opens. Emphasize the event-driven architecture: the developer's workflow does not change — they push code, and the security remediation happens in the background.
