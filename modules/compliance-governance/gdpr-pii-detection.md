# GDPR/PII Detection

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [timesheet-app](#timesheet-app)
- [Online-Banking-System-using-Java](#online-banking-system-using-java)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [Online-Banking-System-using-Java](#Online-Banking-System-using-Java)

---

## Challenge

Scan the codebase for PII handling patterns — personal data storage, transmission, logging, and processing. Identify GDPR compliance gaps and suggest data protection improvements.

## Quick Start

Paste this prompt into Devin to get started immediately:

```
Scan timesheet-app for GDPR and PII compliance issues.
Analyze the entire codebase to identify where personal
data (names, emails, passwords, user identifiers) is
stored, processed, transmitted, and logged. Check for:
unencrypted PII in the database, PII leaked in logs or
error messages, missing data anonymization, lack of
consent mechanisms, and insecure data transmission.
Create a PII_COMPLIANCE_REPORT.md with a data flow map,
a list of compliance gaps rated by severity
(Critical/High/Medium/Low), and recommended fixes.
Implement the highest-priority fixes (e.g., removing
PII from logs, adding password hashing if missing).
```

## Target Outcomes

- PII data flow map showing where personal data is stored, processed, and transmitted
- List of GDPR compliance gaps with severity ratings
- Recommended fixes for data protection (encryption, anonymization, consent)
- PR with PII handling improvements and compliance documentation

## What Participants Will Learn

- How Devin traces data flows to identify where personal information is stored, processed, and transmitted
- How Devin identifies GDPR compliance gaps in existing codebases
- How to evaluate PII exposure risk across different layers (API, database, logs)
- How Devin generates actionable compliance documentation from code analysis

## Devin Features Exercised

- Code pattern analysis for PII identification
- Security assessment and data flow tracing
- Cross-layer analysis (API, database, logging)
- Compliance documentation generation

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express timesheet application that handles user data including names, emails, and authentication credentials — a realistic target for PII analysis.

### Step 1: Paste into Devin

```
Scan timesheet-app for GDPR and PII compliance issues.
Analyze the entire codebase to identify where personal
data (names, emails, passwords, user identifiers) is
stored, processed, transmitted, and logged. Check for:
unencrypted PII in the database, PII leaked in logs or
error messages, missing data anonymization, lack of
consent mechanisms, and insecure data transmission.
Create a PII_COMPLIANCE_REPORT.md with a data flow map,
a list of compliance gaps rated by severity
(Critical/High/Medium/Low), and recommended fixes.
Implement the highest-priority fixes (e.g., removing
PII from logs, adding password hashing if missing).
```

### Step 2: Research with Ask Devin

- *"Where does timesheet-app store or log personal user data? Are there any places where PII could leak into logs or error responses?"*
- *"What GDPR-required mechanisms (consent, right to erasure, data portability) are missing from timesheet-app?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the authentication flow, database schema, and API endpoints. Focus on how user data flows through the Express middleware, database layer, and any external integrations.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — did Devin correctly identify PII in relevant layers (API, database, logs)? Are the severity ratings appropriate?
- **Leave a comment** asking Devin to also add a data retention policy implementation or a user data export endpoint (GDPR right to portability)
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Cross-layer PII tracing**: Devin follows personal data from API input through middleware, database storage, and log output — revealing exposure points at each layer
- **Log leakage**: PII in application logs is a common and often overlooked GDPR violation — Devin identifies where personal data appears in `console.log`, error handlers, and debug output
- **Severity-driven prioritization**: Rating compliance gaps by severity (Critical/High/Medium/Low) helps teams focus remediation on the highest-risk issues first
- **Actionable documentation**: The `PII_COMPLIANCE_REPORT.md` provides both a technical data flow map and a prioritized remediation checklist — useful for auditors and developers alike

---

## <a id="Online-Banking-System-using-Java"></a>Online-Banking-System-using-Java

**Repository:** [Online-Banking-System-using-Java](https://github.com/Cognition-Partner-Workshops/Online-Banking-System-using-Java)

Java banking application handling sensitive customer financial and personal data — a high-stakes PII environment where compliance gaps have real regulatory impact.

### Step 1: Paste into Devin

```
Scan Online-Banking-System-using-Java for GDPR and PII
compliance issues. This is a banking application
handling highly sensitive customer data. Analyze the
codebase to identify: where customer PII (names,
addresses, account numbers, financial data) is stored
and processed, whether sensitive data is encrypted at
rest and in transit, if PII appears in log output or
exception messages, and whether there are data access
controls. Create a PII_COMPLIANCE_REPORT.md with a data
flow map for customer information, a severity-rated
list of compliance gaps, and recommended fixes.
Implement critical fixes (e.g., masking PII in logs,
adding input validation for sensitive fields).
```

### Step 2: Research with Ask Devin

- *"What customer PII does Online-Banking-System-using-Java handle? Is any of it stored in plaintext or logged without masking?"*
- *"What encryption or data protection mechanisms are currently in place for sensitive financial data in this application?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the data model and transaction flow. Banking applications typically handle account numbers, transaction details, and personal identification — these are high-sensitivity PII categories under GDPR.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — did Devin identify financial data (account numbers, transaction amounts) as PII in addition to personal data?
- **Leave a comment** asking Devin to add data masking utilities that can be reused across the application for consistent PII handling
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Financial PII sensitivity**: Banking data (account numbers, transaction amounts, balances) is regulated under both GDPR and financial regulations (PCI DSS, SOX) — requiring stricter controls than general PII
- **Encryption at rest and in transit**: Devin checks whether sensitive data is encrypted in the database and whether API endpoints use TLS — both are baseline GDPR requirements
- **Log masking**: Financial data in logs is a critical compliance finding — Devin implements masking utilities that replace sensitive values with redacted placeholders
- **Domain-aware analysis**: Devin adjusts its analysis based on the application domain — a banking app triggers checks for financial regulations that a timesheet app would not

---

## Going Further

- **Webhook-driven automation**: Connect a deployment pipeline to Devin so that every production release automatically triggers a PII compliance scan. Devin analyzes the latest code for new PII handling patterns, updates the compliance report, and opens a PR with any required fixes — ensuring compliance documentation stays current with the codebase.
- **Divide and conquer with child sessions**: When auditing PII handling across a microservices estate, use a parent Devin session to define the PII categories and compliance checklist, then spawn child sessions — one per service — to scan in parallel. Each child produces a per-service compliance report, and the parent aggregates them into an organization-wide PII data flow map.
- **Scheduled recurring analysis**: Configure a quarterly scheduled Devin session to re-run PII compliance analysis, catching new data flows introduced by recent code changes. Combine with Knowledge notes that encode your organization's PII classification rules (which fields are PII, which require encryption, which must be masked in logs) so that every scan applies the same criteria consistently.
