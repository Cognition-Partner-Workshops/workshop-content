# Security Antipatterns

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [timesheet-app](#timesheet-app)
- [Online-Banking-System-using-Java](#online-banking-system-using-java)
- [uc-cve-remediation-regulatory-compliance](#uc-cve-remediation-regulatory-compliance)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [Online-Banking-System-using-Java](#online-banking-system-using-java)
- [uc-cve-remediation-regulatory-compliance](#uc-cve-remediation-regulatory-compliance)

---

## Challenge

Discover security flaws or design antipatterns preexisting in application source code with Devin's help. This goes beyond dependency scanning to examine the code itself for OWASP Top 10 vulnerabilities, authentication weaknesses, and insecure patterns.

## Quick Start

Paste this prompt into Devin to get started immediately:

```
Perform a security code review of timesheet-app against
the OWASP Top 10. Focus on: authentication weaknesses
(email-only, no password), SQL injection risks in the
SQLite queries, XSS vulnerabilities in the React
frontend, and CSRF protection. For each finding,
explain the vulnerability, its severity, and provide a
recommended fix. Implement fixes for the top 3 most
critical findings.
```

## Target Outcomes

- Security code review report with findings categorized by severity
- At least 3 critical findings identified with recommended fixes
- Optionally: fixes implemented for the most critical findings
- PR with findings report and optional fixes

## What Participants Will Learn

- How Devin performs code-level security analysis (beyond dependency scanning)
- Devin's understanding of security design patterns
- How to validate AI-generated security assessments

## Devin Features Exercised

- Deep codebase analysis
- Security pattern recognition
- Report generation
- Optional: code remediation and PR creation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express application with email-only auth (no password) and potential SQL injection paths.

### Step 1: Paste into Devin

```
Perform a security code review of timesheet-app against
the OWASP Top 10. Focus on: authentication weaknesses
(email-only, no password), SQL injection risks in the
SQLite queries, XSS vulnerabilities in the React
frontend, and CSRF protection. For each finding,
explain the vulnerability, its severity, and provide a
recommended fix. Implement fixes for the top 3 most
critical findings.
```

### Step 2: Research with Ask Devin

- *"What authentication mechanism does timesheet-app use? What are its security weaknesses?"*
- *"Are there any SQL queries that use string concatenation instead of parameterized queries?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the authentication flow and data access layer. Map out where user input flows through the system — these are the primary attack surfaces.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the security fixes correct? Do they introduce any usability regressions?
- **Leave a comment** asking Devin to also add input validation middleware for API endpoints

### Key Takeaways

- **Code-level vs. dependency-level**: Security antipatterns live in application code, not in dependency versions — they require code analysis, not just `npm audit`
- **OWASP Top 10 coverage**: Devin systematically checks for injection, broken auth, XSS, CSRF, and other OWASP categories
- **Severity-driven fixes**: Implementing fixes for the top 3 findings demonstrates prioritized remediation within a time-boxed session
- **Attack surface mapping**: Understanding how user input flows through the system (API → middleware → database) reveals where injection risks concentrate

---

## <a id="online-banking-system-using-java"></a>Online-Banking-System-using-Java

**Repository:** [Online-Banking-System-using-Java](https://github.com/Cognition-Partner-Workshops/Online-Banking-System-using-Java)

Java banking application — likely has multiple security antipatterns given the domain sensitivity.

### Step 1: Paste into Devin

```
Perform a security code review of
Online-Banking-System-using-Java against the OWASP
Top 10. A banking application should have strong
security — identify authentication bypass risks,
injection vulnerabilities, hardcoded credentials,
insecure session management, and missing encryption.
Implement fixes for the top 3 most critical findings.
```

### Step 2: Research with Ask Devin

- *"What authentication and authorization mechanisms does this banking app use? Are they appropriate for financial transactions?"*
- *"Are there any hardcoded secrets, API keys, or credentials in the source code?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the transaction flow and account management logic. Financial operations are the highest-risk code paths.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the security fixes appropriate for a financial application?
- **Leave a comment** asking Devin to add audit logging for financial transactions

### Key Takeaways

- **Domain-aware analysis**: Banking applications have elevated security requirements — Devin's review adjusts its expectations based on the application domain
- **Financial transaction security**: Authentication, authorization, and audit logging are non-negotiable for financial operations
- **Hardcoded credential risk**: Credentials in a banking application represent a critical severity finding, not just a best-practice violation
- **Defense in depth**: Security fixes in a banking context typically span multiple layers — auth, encryption, input validation, and logging

---

## <a id="uc-cve-remediation-regulatory-compliance"></a>uc-cve-remediation-regulatory-compliance

**Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)

Spring Boot application — review for OWASP Top 10 issues in addition to dependency-level CVEs.

### Step 1: Paste into Devin

```
Perform a security code review of
uc-cve-remediation-regulatory-compliance beyond its
dependency vulnerabilities. Look for: SQL injection via
MyBatis dynamic SQL, insecure Spring Security
configuration, missing CSRF protection, exposed
actuator endpoints, and hardcoded secrets. For each
finding, explain severity and recommended fix.
Implement fixes for the top 3.
```

### Step 2: Research with Ask Devin

- *"Is the Spring Security configuration in this app following best practices? Are there any overly permissive rules?"*
- *"Are there any MyBatis mappers using `${}` (string substitution) instead of `#{}` (parameterized)?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the security configuration and API endpoint permissions. Identify endpoints that should require authentication but don't.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the fixes follow Spring Security best practices?
- **Leave a comment** asking Devin to also add rate limiting to the authentication endpoints

### Key Takeaways

- **Framework-specific patterns**: MyBatis `${}` vs. `#{}` is a Spring/MyBatis-specific injection risk that requires framework knowledge to detect
- **Actuator exposure**: Spring Boot actuator endpoints can leak system internals (health, env, beans) if not secured — a common misconfiguration
- **CSRF in REST APIs**: Spring Security enables CSRF by default, but misconfigured REST APIs may disable it inappropriately
- **Configuration review**: Security antipatterns often hide in configuration files (`SecurityConfig`, `application.properties`), not just in business logic code

---

## Going Further

- **Webhook-driven automation**: Connect Devin Review to your PR workflow so that every new PR automatically receives a security antipattern analysis. Findings appear as PR comments, and Devin can open follow-up fix PRs for critical issues — creating a continuous security review layer.
- **Divide and conquer with child sessions**: When auditing multiple applications for security antipatterns (e.g., across a microservices estate), use a parent Devin session to define the audit checklist (based on OWASP Top 10) and spawn child sessions — one per service — to perform the review in parallel. Each child produces a findings report and optional fixes for its assigned service.
- **Scheduled recurring analysis**: Configure a monthly scheduled Devin session to re-run security antipattern analysis against your critical applications. As code changes land, new antipatterns can be introduced — periodic review catches them before they reach production. Store your OWASP checklist as a Knowledge note so that every review session applies the same criteria consistently.
