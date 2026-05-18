# Lab: Monorepo Security Sprint

## Objective

Run security scans across the OtterWorks monorepo, triage findings by severity, audit the `.trivyignore` file for overly broad suppressions, and remediate CRITICAL/HIGH CVEs.

## What's Wrong

The OtterWorks monorepo has security vulnerabilities planted across multiple services in different languages:

- **Node.js (collab-service):** Vulnerable `lodash` version with known command injection CVE
- **Python (search-service):** Vulnerable `urllib3` version with ReDoS CVE
- **Ruby (admin-service):** Known `activestorage` CVEs in the current Rails version
- **Java (report-service):** Guava 28 with multiple CVEs (but this service is excluded from scans — it's a separate upgrade exercise)
- **`.trivyignore`:** Contains overly broad entries that suppress real findings. Some entries are legitimate (acknowledged risks), others are lazy bulk ignores that hide real problems.

The security scans will find real vulnerabilities. The challenge is triaging them correctly: which are CRITICAL and need immediate remediation, which are HIGH and should be scheduled, and which are properly acknowledged in `.trivyignore`.

## Where to Look

- `Makefile` — Run `make security-scan` to execute all scanners
- `services/collab-service/package.json` — Node.js dependencies (check `lodash` version)
- `services/search-service/requirements.txt` — Python dependencies (check `urllib3` version)
- `services/admin-service/Gemfile` — Ruby dependencies
- `.trivyignore` — Suppressed CVEs. Are all suppressions justified?
- `security/scanning/trivy-config.yaml` — Trivy configuration (what severity levels are scanned?)
- `docs/labs/security-sprint-guide.md` — **Read this first.** Explains how to run scans, interpret output, and triage findings.

## What Done Looks Like

- [ ] Ran `make security-scan` and reviewed the output
- [ ] Triaged all CRITICAL/HIGH findings:
  - Identified which CVEs are real vulnerabilities vs. false positives
  - Identified which `.trivyignore` entries are justified vs. overly broad
- [ ] Remediated at least 2 CRITICAL/HIGH CVEs:
  - Updated vulnerable dependency versions in the affected service
  - Verified the fix does not break the service (tests still pass)
- [ ] Cleaned up `.trivyignore`:
  - Removed or narrowed overly broad suppressions
  - Added proper comments to justified suppressions
- [ ] Re-ran `make security-scan` and confirmed the remediated CVEs are gone

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Run `make security-scan` from the repo root. Read the output carefully. Focus on CRITICAL and HIGH severity findings. Read `docs/labs/security-sprint-guide.md` for guidance on interpreting the output.

### Hint 2 — Specific Direction

Look at the `.trivyignore` file. Some entries have comments like "Bulk ignore -- revisit in Q4" — these are suspicious. Check whether the suppressed CVEs are actually resolved or just hidden. Then look at `services/collab-service/package.json` for the `lodash` dependency and `services/search-service/requirements.txt` for `urllib3`.

### Hint 3 — Approach

Ask Devin: "Read `.trivyignore` and identify which entries are overly broad or no longer justified. Then read `services/collab-service/package.json` and `services/search-service/requirements.txt`, identify vulnerable dependencies, and upgrade them to the latest secure versions. Run the service tests after each upgrade to verify nothing breaks."

For parallel remediation: spin up one Devin session per service to fix vulnerabilities simultaneously.

## Time Budget

- ~20 minutes: Run scans, read output, triage findings
- ~20 minutes: Audit `.trivyignore` for overly broad suppressions
- ~30-40 minutes: Remediate 2-3 CVEs across different services
- Advanced participants: use parallel Devin sessions to remediate all services simultaneously
