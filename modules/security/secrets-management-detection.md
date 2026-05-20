# Secrets Management & Detection

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [timesheet-app](#timesheet-app)
- [uc-cve-remediation-regulatory-compliance](#uc-cve-remediation-regulatory-compliance)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-cve-remediation-regulatory-compliance](#uc-cve-remediation-regulatory-compliance)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Scan a codebase for hardcoded secrets, credentials, and sensitive data. Implement secrets detection in CI, migrate hardcoded values to environment variables, and add pre-commit hooks to prevent future secret leaks.

## Quick Start

Paste this prompt into Devin to get started immediately:

```
Install and run gitleaks on timesheet-app to scan for
hardcoded secrets and credentials. Migrate any findings
to environment variables using a .env.example file
(without actual secret values). Add a pre-commit hook
using husky that runs gitleaks on staged files. Add a
GitHub Actions step that fails PRs introducing new
secrets.
```

## Target Outcomes

- Secrets scan completed (gitleaks, trufflehog, or detect-secrets)
- Hardcoded secrets migrated to environment variables or config files
- Pre-commit hook installed for secrets detection
- CI step added to block PRs that introduce secrets
- PR with remediation and tooling

## What Participants Will Learn

- How Devin identifies hardcoded secrets and credentials in source code
- How to implement secrets detection as a preventive control
- The difference between detection (finding existing secrets) and prevention (blocking new ones)
- Best practices for secrets management in application code

## Devin Features Exercised

- Codebase scanning and pattern recognition
- CI/CD authoring (GitHub Actions, pre-commit hooks)
- Environment variable and configuration management
- PR creation with security tooling

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js application — scan for hardcoded API keys, database credentials, and JWT secrets in the codebase.

### Step 1: Paste into Devin

```
Install and run gitleaks on timesheet-app to scan for
hardcoded secrets and credentials. Migrate any findings
to environment variables using a .env.example file
(without actual secret values). Add a pre-commit hook
using husky that runs gitleaks on staged files. Add a
GitHub Actions step that fails PRs introducing new
secrets.
```

### Step 2: Research with Ask Devin

- *"Are there any hardcoded secrets, API keys, or database connection strings in timesheet-app?"*
- *"What's the current approach to configuration management? Are environment variables used consistently?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the configuration and authentication setup. Identify where secrets or credentials are referenced.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are secrets properly externalized? Is the `.env.example` complete?
- **Leave a comment** asking Devin to also add a `.gitignore` entry for `.env` files if missing

### Key Takeaways

- **Detection vs. prevention**: Gitleaks finds existing secrets (detection); pre-commit hooks block new ones (prevention) — both layers are needed
- **Environment variable migration**: Moving secrets to `.env.example` with placeholder values documents what configuration is required without exposing actual credentials
- **CI enforcement**: A GitHub Actions step that fails on secret detection makes the policy enforceable, not just advisory
- **Husky integration**: Pre-commit hooks catch secrets before they enter git history — far easier than scrubbing history after the fact

---

## <a id="uc-cve-remediation-regulatory-compliance"></a>uc-cve-remediation-regulatory-compliance

**Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)

Spring Boot application — scan for hardcoded database credentials, Spring Security secrets, and application properties with sensitive values.

### Step 1: Paste into Devin

```
Install and run gitleaks on
uc-cve-remediation-regulatory-compliance to scan the
full git history for leaked secrets. Also review
application.properties and application.yml for
hardcoded credentials. Migrate all sensitive
configuration to environment variable placeholders
(e.g., ${DB_PASSWORD}). Add a GitHub Actions workflow
step that runs gitleaks on every PR.
```

### Step 2: Research with Ask Devin

- *"What sensitive configuration values are hardcoded in the Spring Boot application properties?"*
- *"Does the git history contain any committed secrets that need to be rotated?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the application configuration and database connection setup.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are Spring Boot property placeholders used correctly for externalized config?
- **Leave a comment** asking Devin to add documentation on how to configure the required environment variables

### Key Takeaways

- **Git history scanning**: Gitleaks scans the full commit history, not just the current working tree — secrets committed and later deleted are still exposed
- **Spring Boot externalization**: Using `${VARIABLE}` placeholders in `application.properties` is the idiomatic Spring Boot approach to secrets management
- **Rotation awareness**: Any secrets found in git history should be considered compromised and rotated, even after removal from the current codebase
- **CI gating**: Adding gitleaks as a PR check prevents new secrets from being introduced going forward

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot monolith — scan for secrets in application config, JWT signing keys, and database credentials.

### Step 1: Paste into Devin

```
Run gitleaks on
uc-spring-boot-upgrade-microservice-extraction to
detect any hardcoded secrets. Review the application
configuration for hardcoded database URLs, JWT secrets,
and API keys. Externalize all sensitive values to
environment variables. Add a pre-commit hook for
secrets detection.
```

### Step 2: Research with Ask Devin

- *"What JWT signing mechanism is used? Is the signing key hardcoded or externalized?"*
- *"Are there any test fixtures or seed data files that contain realistic-looking credentials?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the authentication and authorization configuration. JWT secrets and database credentials are the primary targets.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the JWT secret properly externalized? Are test fixtures using obviously fake credentials?
- **Leave a comment** asking Devin to also add a `.gitleaks.toml` config to tune detection rules for this project

### Key Takeaways

- **JWT secret externalization**: Hardcoded JWT signing keys are a critical vulnerability — externalizing them to environment variables is the minimum viable fix
- **Test fixture hygiene**: Test data that looks like real credentials can trigger false positives in scanning tools; using obviously fake values (e.g., `test-secret-do-not-use`) prevents this
- **Custom detection rules**: A `.gitleaks.toml` config lets you tune sensitivity — allowing known test values while catching real secrets
- **Cross-cutting concern**: Secrets management applies to every layer of the stack (application config, test data, CI variables, deployment scripts)

---

## Going Further

- **Webhook-driven automation**: Connect gitleaks or trufflehog to Devin via a CI webhook so that any secret detection event automatically triggers a remediation session. Devin reads the finding, migrates the secret to an environment variable, and opens a PR — closing the loop without human triage.
- **Divide and conquer with child sessions**: When scanning an organization with many repos, use a parent Devin session to run gitleaks across repositories in bulk, triage findings by severity, and spawn child sessions — one per repo — to remediate in parallel. Each child externalizes secrets and adds prevention tooling for its repo.
- **Scheduled recurring analysis**: Configure a weekly scheduled Devin session to run gitleaks against your repos' latest commits, catching any secrets that bypass pre-commit hooks (e.g., commits pushed from CI or automation). Combine with Devin's shared context layer — store your `.gitleaks.toml` rules as a Knowledge note so that every scan session uses the same detection configuration.
