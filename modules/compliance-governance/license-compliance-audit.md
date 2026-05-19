# License Compliance Audit

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Scan all dependencies for license conflicts, generate a Software Bill of Materials (SBOM), and produce a compliance report identifying any copyleft or restrictive licenses that could create legal issues.

## Target Outcomes

- Complete SBOM in CycloneDX or SPDX format
- License compatibility report identifying conflicts
- Risk assessment for copyleft/restrictive licenses
- PR with SBOM, compliance report, and any recommended dependency replacements

## What Participants Will Learn

- How Devin analyzes dependency trees and identifies license types across ecosystems
- How Devin generates industry-standard SBOMs (CycloneDX, SPDX) from project metadata
- How to evaluate license compatibility risks in a mixed-dependency project
- How Devin recommends alternative dependencies when license conflicts are found

## Devin Features Exercised

- Dependency tree analysis across package managers (npm, Gradle)
- License identification and classification
- SBOM generation in standard formats
- Compliance reporting and risk assessment

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express application with npm dependencies — ideal for license auditing with npm-native tooling.

### Step 1: Paste into Devin

> Scan all dependencies in timesheet-app for license compliance. Use `npm sbom --sbom-format cyclonedx` (or install @cyclonedx/cyclonedx-npm if needed) to generate an SBOM in CycloneDX format. Then analyze all dependency licenses — identify any copyleft (GPL, AGPL, LGPL) or restrictive licenses that could create legal issues for commercial use. Produce a `LICENSE_COMPLIANCE_REPORT.md` with: a summary of all license types found, a list of flagged dependencies with risk ratings, and recommended replacements for any problematic dependencies. Open a PR with the SBOM file and compliance report.

### Step 2: Research with Ask Devin

- *"What licenses are used by timesheet-app's dependencies? Are any of them copyleft or incompatible with commercial use?"*
- *"What tools exist for automated license compliance checking in Node.js projects? How do they compare?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the dependency structure and which packages are critical vs. replaceable. This helps evaluate whether flagged dependencies can be swapped.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the SBOM include all direct and transitive dependencies? Is the compliance report actionable?
- **Leave a comment** asking Devin to also check for dependencies with no declared license (which are legally risky)
- **Watch Devin respond** and push a follow-up commit

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot/Gradle monolith with Java dependencies — try license auditing using Gradle plugins in a JVM ecosystem.

### Step 1: Paste into Devin

> Scan all dependencies in uc-spring-boot-upgrade-microservice-extraction for license compliance. Add the `com.github.jk1.dependency-license-report` Gradle plugin (or a similar license reporting plugin) to generate a full dependency license report. Also generate an SBOM using CycloneDX Gradle plugin (`org.cyclonedx.bom`). Analyze the results — flag any copyleft (GPL, AGPL), restrictive, or unknown licenses. Create a `LICENSE_COMPLIANCE_REPORT.md` with: license distribution summary, flagged dependencies with risk levels, and recommended actions. Open a PR with the SBOM, license report output, and compliance documentation.

### Step 2: Research with Ask Devin

- *"What Gradle plugins are available for license compliance reporting? Which one provides the most detailed output?"*
- *"Are there any dependencies in uc-spring-boot-upgrade-microservice-extraction with GPL or AGPL licenses that could affect distribution?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the dependency graph and build configuration. Look for which dependencies are compile-time vs. runtime — this affects license obligations.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Gradle plugin configuration look correct? Are all license types properly categorized?
- **Leave a comment** asking Devin to add the license check as a Gradle task that can be run in CI
- **Watch Devin respond** and push a follow-up commit
