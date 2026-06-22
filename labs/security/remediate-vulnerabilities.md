# Remediate Vulnerabilities

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
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

Use local SAST tools to identify and remediate the most critical preexisting vulnerabilities in a repository. Both dependency-level CVEs and code-level security issues are in scope.

## Quick Start

Paste this prompt into Devin to get started immediately:

```
Run npm audit on timesheet-app to identify dependency
vulnerabilities. Then run Trivy (install if needed) to
scan for additional issues. Remediate all critical and
high severity findings. Re-run scans to verify fixes.
Create a SECURITY_REMEDIATION.md with before/after
evidence.
```

## Target Outcomes

- OWASP Dependency-Check report generated (before and after remediation)
- SonarQube analysis completed with security hotspots reviewed
- High/critical vulnerabilities patched or upgraded
- Secure coding checks added: format/lint + static analysis (SAST)
- `SECURITY_REMEDIATION.md` with before/after evidence
- PR with remediations documented

## What Participants Will Learn

- How Devin runs and interprets local SAST tools (OWASP Dependency-Check, SonarQube)
- How Devin interprets CVE details (CVSS scores, affected versions, fix versions)
- The difference between dependency vulnerabilities and code-level vulnerabilities
- How to evaluate whether Devin's fixes are correct and complete
- The scan → fix → re-scan verification pattern

## Devin Features Exercised

- Local tool execution (Gradle plugins, Docker Compose)
- SAST report interpretation
- Security analysis and code remediation
- Build verification
- PR creation with security evidence

## Difficulty

Intermediate

## Estimated Time

60 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js application with Trivy scanning already configured.

### Step 1: Paste into Devin

```
Run npm audit on timesheet-app to identify dependency
vulnerabilities. Then run Trivy (install if needed) to
scan for additional issues. Remediate all critical and
high severity findings. Re-run scans to verify fixes.
Create a SECURITY_REMEDIATION.md with before/after
evidence.
```

### Step 2: Research with Ask Devin

- *"What security scanning tools are already configured in timesheet-app? How can their coverage be expanded?"*
- *"Beyond dependency vulnerabilities, are there any code-level security issues (SQL injection, XSS, etc.)?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the authentication flow and data handling. These are the areas most likely to have security-relevant code issues.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — did Devin address both dependency and code-level vulnerabilities?
- **Leave a comment** asking Devin to also add a CI workflow that fails on CRITICAL CVEs
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Dual-layer scanning**: Combining `npm audit` (dependency CVEs) with Trivy (container and filesystem scanning) catches issues at multiple levels
- **Before/after evidence**: The `SECURITY_REMEDIATION.md` provides auditable proof that remediation was effective
- **Scan → fix → re-scan**: The verification loop confirms fixes actually resolve the detected issues — not just suppress warnings
- **Incremental hardening**: Starting with dependency upgrades, then adding code-level fixes, builds a layered security posture

---

## <a id="uc-cve-remediation-regulatory-compliance"></a>uc-cve-remediation-regulatory-compliance

**Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)

Spring Boot 2.6.3 with known CVEs, pre-configured with OWASP Dependency-Check and SonarQube Gradle plugins for local SAST scanning.

### Known CVEs

The repo ships with Spring Boot 2.6.3 and several outdated dependencies. Key findings participants will encounter:

**Critical (CVSS 9.0+):**

| CVE | Dependency | Version | CVSS | Description |
|-----|-----------|---------|------|-------------|
| CVE-2022-22965 | Spring Framework | 5.3.15 | 9.8 | **Spring4Shell** — RCE via data binding on JDK 9+ |
| CVE-2022-1471 | SnakeYAML | 1.29 | 9.8 | Unsafe deserialization — arbitrary code execution |
| CVE-2022-22978 | Spring Security | 5.6.1 | 9.8 | Regex bypass in authorization rules |
| CVE-2022-31692 | Spring Security | 5.6.1 | 9.8 | Forward/include dispatch bypass |
| CVE-2023-32697 | sqlite-jdbc | 3.36.0.3 | 8.8 | RCE when JDBC URL is attacker-controlled |

**High (CVSS 7.0-8.9):**

| CVE | Dependency | Version | CVSS | Description |
|-----|-----------|---------|------|-------------|
| CVE-2022-22968 | Spring Framework | 5.3.15 | 7.5 | Data binding pattern bypass |
| CVE-2022-25857 | SnakeYAML | 1.29 | 7.5 | DoS via crafted YAML input |
| CVE-2022-42003 | jackson-databind | 2.13.1 | 7.5 | Deserialization issue |
| CVE-2022-42004 | jackson-databind | 2.13.1 | 7.5 | Deserialization issue |
| CVE-2022-42252 | Tomcat Embed | 9.0.56 | 7.5 | HTTP request smuggling |
| CVE-2023-24998 | Tomcat Embed | 9.0.56 | 7.5 | DoS via file upload |
| CVE-2022-23181 | Tomcat Embed | 9.0.56 | 7.0 | TOCTOU race condition |
| CVE-2023-6378 | Logback | 1.2.10 | 7.5 | DoS via crafted serialized data |

> **Tip:** There are 18+ known CVEs in total. Focus on CRITICAL and HIGH first.

### Pre-configured SAST Tools (both local — no cloud costs)

| Tool | What It Scans | Gradle Command | Report Location |
|------|--------------|----------------|-----------------|
| [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/) | Dependencies against the NVD for known CVEs | `./gradlew dependencyCheckAnalyze` | `build/reports/dependency-check-report.html` |
| [SonarQube Community Edition](https://www.sonarsource.com/open-source-editions/sonarqube-community-edition/) | Source code for vulnerabilities, code smells, bugs | `./gradlew sonar -Dsonar.token=<TOKEN>` | http://localhost:9000 dashboard |

### Step 1: Paste into Devin

```
Run ./gradlew dependencyCheckAnalyze on
uc-cve-remediation-regulatory-compliance to identify
dependency CVEs. Remediate the top 5 most critical
findings (CVSS >= 7.0) — start with Spring Boot 2.6.3,
SnakeYAML 1.29, and sqlite-jdbc 3.36.0.3. Re-run the
scan to verify the fixes. Create a
SECURITY_REMEDIATION.md documenting the before/after
results.
```

### Step 2: Research with Ask Devin

- *"What are the known CVEs in uc-cve-remediation-regulatory-compliance's dependencies? Which ones are CRITICAL severity?"*
- *"What's the safest upgrade path for Spring Boot 2.6.3 — should we go to 2.7.x first or jump straight to 3.x?"*
- Use the analysis to start a second session — try adding a GitHub Actions security scanning workflow, or upgrade Spring Boot to 3.x instead of just patching

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the codebase architecture. Use what you learn to try different approaches:
- Have Devin add **SBOM generation** (CycloneDX) to the build
- Ask Devin to add a **GitHub Actions workflow** that runs Trivy or OWASP Dependency-Check on every PR
- Try adding **SonarQube scanning** — the repo has a pre-configured `docker-compose.sonarqube.yml`
- Ask Devin to add **pre-commit hooks** for secrets detection (gitleaks)

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — did Devin upgrade the right dependencies? Are there any it missed?
- **Leave a comment** asking Devin to fix something (e.g., *"The SnakeYAML version still has CVE-2022-1471 — please upgrade to 2.x"*)
- **Watch Devin respond** and push a fix

> **Note:** SonarQube Community Edition runs locally via Docker (`docker-compose.sonarqube.yml` is included in the repo). OWASP Dependency-Check downloads the NVD database locally on first run. Neither tool requires a paid license or cloud account.

### Key Takeaways

- **Local SAST tooling**: Both OWASP Dependency-Check and SonarQube run entirely on Devin's VM — no cloud accounts or paid licenses required
- **Severity-driven prioritization**: Focusing on CVSS 7.0+ findings first addresses the highest-risk vulnerabilities within limited workshop time
- **Transitive dependency resolution**: Upgrading Spring Boot resolves multiple downstream CVEs (Spring Security, Tomcat) automatically
- **Iterative feedback**: Leaving PR comments that ask for specific fixes (e.g., "upgrade SnakeYAML to 2.x") demonstrates Devin's ability to respond to targeted review feedback

---

## Going Further

- **Webhook-driven automation**: Connect OWASP Dependency-Check or SonarQube to Devin via webhooks so that new HIGH/CRITICAL findings automatically trigger a remediation session — no manual scan-and-assign workflow needed.
- **Divide and conquer with child sessions**: When a dependency audit reveals vulnerabilities across multiple microservices, use a parent Devin session to triage the findings and spawn one child session per service. Each child upgrades its dependencies independently, opens its own PR, and the parent produces a consolidated remediation report.
- **Scheduled recurring analysis**: Configure a weekly scheduled Devin session to run `./gradlew dependencyCheckAnalyze` (or `npm audit`) and remediate new findings automatically. Over time, this prevents vulnerability backlogs from accumulating and keeps your dependency posture continuously up to date.
