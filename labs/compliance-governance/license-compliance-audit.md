# License Compliance Audit

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Scan dependencies for license conflicts, generate a Software Bill of Materials (SBOM), and produce a compliance report identifying any copyleft or restrictive licenses that could create legal issues.

## Quick Start

Paste this prompt into Devin to get started immediately:

```
Scan all dependencies in timesheet-app for license
compliance. Use npm sbom --sbom-format cyclonedx (or
install @cyclonedx/cyclonedx-npm if needed) to generate
an SBOM in CycloneDX format. Then analyze dependency
licenses — identify any copyleft (GPL, AGPL, LGPL) or
restrictive licenses that could create legal issues for
commercial use. Produce a LICENSE_COMPLIANCE_REPORT.md
with: a summary of license types found, a list of
flagged dependencies with risk ratings, and recommended
replacements for any problematic dependencies.
```

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

```
Scan all dependencies in timesheet-app for license
compliance. Use npm sbom --sbom-format cyclonedx (or
install @cyclonedx/cyclonedx-npm if needed) to generate
an SBOM in CycloneDX format. Then analyze dependency
licenses — identify any copyleft (GPL, AGPL, LGPL) or
restrictive licenses that could create legal issues for
commercial use. Produce a LICENSE_COMPLIANCE_REPORT.md
with: a summary of license types found, a list of
flagged dependencies with risk ratings, and recommended
replacements for any problematic dependencies.
```

### Step 2: Research with Ask Devin

- *"What licenses are used by timesheet-app's dependencies? Are any of them copyleft or incompatible with commercial use?"*
- *"What tools exist for automated license compliance checking in Node.js projects? How do they compare?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the dependency structure and which packages are critical vs. replaceable. This helps evaluate whether flagged dependencies can be swapped.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the SBOM include direct and transitive dependencies? Is the compliance report actionable?
- **Leave a comment** asking Devin to also check for dependencies with no declared license (which are legally risky)
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **SBOM generation**: CycloneDX SBOMs provide a machine-readable inventory of dependencies — increasingly required by supply chain security regulations and enterprise procurement
- **Copyleft detection**: GPL/AGPL dependencies in a commercial product can create distribution obligations — Devin flags these and suggests permissively licensed alternatives
- **Transitive license risk**: A direct dependency with an MIT license may pull in a transitive dependency with a GPL license — Devin traces the full dependency tree to catch these
- **No-license risk**: Dependencies with no declared license are legally ambiguous — they default to full copyright protection, making them riskier than copyleft in some contexts

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot/Gradle monolith with Java dependencies — try license auditing using Gradle plugins in a JVM ecosystem.

### Step 1: Paste into Devin

```
Scan all dependencies in
uc-spring-boot-upgrade-microservice-extraction for
license compliance. Add the
com.github.jk1.dependency-license-report Gradle plugin
(or a similar license reporting plugin) to generate a
full dependency license report. Also generate an SBOM
using CycloneDX Gradle plugin (org.cyclonedx.bom).
Analyze the results — flag any copyleft (GPL, AGPL),
restrictive, or unknown licenses. Create a
LICENSE_COMPLIANCE_REPORT.md with: license distribution
summary, flagged dependencies with risk levels, and
recommended actions.
```

### Step 2: Research with Ask Devin

- *"What Gradle plugins are available for license compliance reporting? Which one provides the most detailed output?"*
- *"Are there any dependencies in uc-spring-boot-upgrade-microservice-extraction with GPL or AGPL licenses that could affect distribution?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the dependency graph and build configuration. Look for which dependencies are compile-time vs. runtime — this affects license obligations.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Gradle plugin configuration look correct? Are license types properly categorized?
- **Leave a comment** asking Devin to add the license check as a Gradle task that can be run in CI
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Ecosystem-specific tooling**: npm and Gradle have different license auditing tools — Devin selects and configures the appropriate tooling for each ecosystem
- **Compile-time vs. runtime obligations**: Some licenses (like LGPL) have different obligations depending on whether the dependency is linked at compile time or runtime — Devin's analysis accounts for this
- **CI integration**: Adding the license check as a Gradle task enables automated enforcement on every PR — preventing new license conflicts from being introduced
- **Cross-ecosystem consistency**: Using CycloneDX for SBOM generation across both npm and Gradle projects produces a consistent format for organization-wide compliance reporting

---

## Going Further

- **Webhook-driven automation**: Connect your dependency update tool (Dependabot, Renovate) to Devin via webhooks so that every dependency change automatically triggers a license compliance check. If a new dependency introduces a copyleft license, Devin opens a PR flagging the issue and suggesting alternatives — catching conflicts before they reach production.
- **Divide and conquer with child sessions**: When auditing license compliance across an organization's repos, use a parent Devin session to define the license policy (allowed/flagged/blocked license types), then spawn child sessions — one per repo — to scan in parallel. Each child generates an SBOM and compliance report, and the parent aggregates results into an organization-wide license inventory.
- **Scheduled recurring analysis**: Configure a quarterly scheduled Devin session to regenerate SBOMs and run license compliance checks. This ensures your compliance documentation stays current as dependencies evolve — useful for SOC 2, ISO 27001, and enterprise procurement reviews that require up-to-date software inventories.
