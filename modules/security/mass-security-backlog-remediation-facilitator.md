# Mass Security Backlog Remediation — Facilitator Notes

## Prerequisites

- This module builds on Remediate Vulnerabilities and Event-Driven SAST Remediation — participants should understand individual repo remediation before scaling to multi-repo
- The consolidated SAST report should be pre-generated before the session (or generated live in Step 1 as part of the lab track)

## Setup

- If the Devin API supports programmatic child session creation, use it. Otherwise, demonstrate the pattern with manual parallel sessions and explain the API-based automation
- The "parent" session can be a single Devin session where the facilitator manually triggers two child sessions — the orchestration pattern is the key learning

## Timing

- Allow 90 minutes for the full hands-on walkthrough
- The parent session (scanning + triage) typically takes 20–30 minutes
- Child sessions run in parallel and typically complete in 30–45 minutes each
- The 2-repo scope is intentionally small for a workshop; in production, the same pattern scales to 10+ repos
