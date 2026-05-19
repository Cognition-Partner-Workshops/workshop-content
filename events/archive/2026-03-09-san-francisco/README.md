# Workshop: Hands-on Devin Workshop — San Francisco

## Event Details

| | |
|---|---|
| **Date** | 2026-03-09 |
| **Location** | San Francisco, CA |
| **Event Site** | https://partner-workshops.devinenterprise.com |

## Featured Labs

This event features 2 structured labs using purpose-built repositories:

### Lab 1 — Framework Upgrade & Refactor: Monolith → Microservices (60 min)
- **Module:** [Framework Upgrade](../../../modules/migration-modernization/framework-upgrade.md#uc-spring-boot-upgrade-microservice-extraction) + [Containerization & Microservice Extraction](../../../modules/migration-modernization/containerization-microservice-extraction.md#uc-spring-boot-upgrade-microservice-extraction)
- **Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)
- **Objective:** Take an older Java monolith (Java 11 + Spring Boot 2.6.3) and modernize it — you choose whether to focus on the upgrade, the microservice extraction, or both

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

> Upgrade uc-spring-boot-upgrade-microservice-extraction from Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2. Handle the javax to jakarta namespace migration, update Gradle build configuration, fix any deprecations, and ensure all tests pass. Open a PR with the changes.

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the domain boundaries in uc-spring-boot-upgrade-microservice-extraction? Which bounded context would be easiest to extract as a microservice?"*
- *"What's the best order to tackle the javax to jakarta migration, the Gradle plugin updates, and the deprecated API removals?"*
- Use the refined understanding to start a **second session** — try extracting a microservice, or upgrade with a different strategy

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to browse the architecture, domain model, and dependency graph. Use what you learn to try something different:
- Extract a different bounded context (Articles, Users, or Comments) as a containerized microservice
- Have Devin produce an architecture decision record (ADR) for how the monolith should be decomposed
- Run parallel sessions — one upgrading the framework, one extracting a service
- Try the Angular upgrade on a different repo alongside the Spring Boot upgrade

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, practice the feedback loop:
- **Review the diff** — does the upgrade look complete? Are there files Devin missed?
- **Leave a comment on the PR** asking Devin to fix something (e.g., *"This still uses javax.servlet — please update to jakarta.servlet"* or *"Can you also add a Dockerfile?"*)
- **Watch Devin respond** to your PR comment and push a fix — this is how real teams work with Devin
- Try leaving both general comments and inline code comments to see how Devin handles each

See the full challenge details for [Framework Upgrade](../../../modules/migration-modernization/framework-upgrade.md) and [Containerization & Microservice Extraction](../../../modules/migration-modernization/containerization-microservice-extraction.md) for more ideas.

- **Target Outcomes (any of these count):**
  - Application builds and tests on Java 17+ / Spring Boot 3.x
  - One extracted microservice with its own Dockerfile and REST API
  - Docker Compose to run the full stack locally
  - Architecture decision record or migration report
  - PR with review comments and Devin's responses

### Lab 2 — CVE Remediations & Regulatory Code Standards (60 min)
- **Module:** [Upgrade Dependencies](../../../modules/security/upgrade-dependencies.md#uc-cve-remediation-regulatory-compliance) + [Remediate Vulnerabilities](../../../modules/security/remediate-vulnerabilities.md#uc-cve-remediation-regulatory-compliance) + [Shift Left Security](../../../modules/security/shift-left-security.md#uc-cve-remediation-regulatory-compliance)
- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Objective:** A Spring Boot 2.6.3 service has accumulated vulnerable dependencies (Spring4Shell, SnakeYAML RCE, SQLite JDBC RCE, and more). Scan, remediate, and add automated compliance checks
- **Known CVEs:** See the [full CVE findings report](../../../modules/security/remediate-vulnerabilities.md#uc-cve-remediation-regulatory-compliance) for a breakdown of all 18+ known vulnerabilities by severity (5 Critical, 8 High, 5 Medium)

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

> Run `./gradlew dependencyCheckAnalyze` on uc-cve-remediation-regulatory-compliance to identify dependency CVEs. Remediate the top 5 most critical findings (CVSS >= 7.0) — start with Spring Boot 2.6.3, SnakeYAML 1.29, and sqlite-jdbc 3.36.0.3. Re-run the scan to verify the fixes. Create a `SECURITY_REMEDIATION.md` documenting the before/after results and open a PR.

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the known CVEs in uc-cve-remediation-regulatory-compliance's dependencies? Which ones are CRITICAL severity?"*
- *"What's the safest upgrade path for Spring Boot 2.6.3 — should we go to 2.7.x first or jump straight to 3.x?"*
- Use the analysis to start a **second session** — try adding a GitHub Actions security scanning workflow, or upgrade Spring Boot to 3.x instead of just patching

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the codebase architecture. Use what you learn to try different approaches:
- Have Devin add **SBOM generation** (CycloneDX) to the build
- Ask Devin to add a **GitHub Actions workflow** that runs Trivy or OWASP Dependency-Check on every PR
- Try adding **SonarQube scanning** — the repo has a pre-configured `docker-compose.sonarqube.yml`
- Ask Devin to add **pre-commit hooks** for secrets detection (gitleaks)

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, practice the feedback loop:
- **Review the diff** — did Devin upgrade the right dependencies? Are there any it missed?
- **Leave a comment on the PR** asking Devin to fix something (e.g., *"Can you also add a CI workflow that fails on CRITICAL CVEs?"* or *"The SnakeYAML version still has CVE-2022-1471 — please upgrade to 2.x"*)
- **Watch Devin respond** to your PR comment and push a fix — this is how real teams work with Devin
- Try leaving both general comments and inline code comments to see how Devin handles each

See the full challenge details for [Upgrade Dependencies](../../../modules/security/upgrade-dependencies.md), [Remediate Vulnerabilities](../../../modules/security/remediate-vulnerabilities.md), and [Shift Left Security](../../../modules/security/shift-left-security.md) for more ideas.

- **Target Outcomes (any of these count):**
  - OWASP Dependency-Check report with high/critical CVEs remediated
  - SBOM generated (CycloneDX or SPDX)
  - Secure coding checks added: SAST via SonarQube or Trivy
  - CI gating workflow: builds fail on policy violations
  - `SECURITY_REMEDIATION.md` with before/after evidence
  - PR with review comments and Devin's responses

## Additional Challenges

Participants may also attempt any challenge from the full [module catalog](../../../modules/) as creative inspiration. The labs above are the primary structured activities.

## Repos Required on Devin's Machine

- [x] uc-spring-boot-upgrade-microservice-extraction
- [x] uc-cve-remediation-regulatory-compliance

## Repo Duplication Notes

Labs 1 and 2 both originate from `spring-boot-realworld-example-app` (Cluster C1 in [catalog](../../../catalog/repos.md)). They are intentionally separate repos so each lab gets an isolated starting point with no risk of cross-contamination during the workshop.

## Context

- **Audience:** Technology teams
- **Focus:** Framework modernization (Java/Spring Boot upgrade, microservice extraction) and security remediation (CVE scanning, SAST, shift-left CI)

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../../modules/devin-features/README.md) throughout the day.
