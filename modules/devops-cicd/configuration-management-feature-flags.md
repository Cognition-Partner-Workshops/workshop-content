# Configuration Management & Feature Flags

<a id="table-of-contents"></a>
## Table of Contents
- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

<a id="challenge"></a>
## Challenge

Implement configuration management and feature flags — externalize application configuration, add feature flag support, and create environment-specific configuration profiles. This exercises Devin's ability to separate deployment configuration from application code. Configuration externalization is a refactoring task that touches many files — Devin systematically finds and replaces hardcoded values while preserving behavior.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Implement configuration management for timesheet-app:
(1) Create a config module that reads settings from
environment variables with sensible defaults (PORT,
DATABASE_URL, LOG_LEVEL, etc.), (2) Add dotenv support
with .env.example documenting all variables, (3) Implement
a simple feature flag system using a JSON config file or
environment variables, (4) Use a feature flag to gate a
new "dark mode" UI toggle — when the flag is off, the
toggle is hidden.
```

<a id="target-outcomes"></a>
## Target Outcomes

- Configuration externalized to environment variables with sensible defaults
- Feature flag system integrated (LaunchDarkly, Unleash, or custom implementation)
- Environment-specific configuration profiles (dev, staging, production)
- Feature flag used to gate a specific feature with runtime toggle
- PR with configuration management improvements

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin externalizes hardcoded configuration values
- Feature flag implementation patterns (boolean flags, percentage rollouts, user targeting)
- Environment-specific configuration management
- The separation of deployment configuration from application code
- How Devin Review catches hardcoded configuration values in PRs

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Configuration refactoring
- Library integration (feature flag SDKs)
- Environment variable management
- Multi-environment configuration
- PR creation with configuration documentation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express application — externalize configuration and add feature flags.

### Step 1: Paste into Devin

```
Implement configuration management for timesheet-app:
(1) Create a config module that reads settings from
environment variables with sensible defaults (PORT,
DATABASE_URL, LOG_LEVEL, etc.), (2) Add dotenv support
with .env.example documenting all variables, (3) Implement
a simple feature flag system using a JSON config file or
environment variables, (4) Use a feature flag to gate a
new "dark mode" UI toggle — when the flag is off, the
toggle is hidden.
```

### Step 2: Research with Ask Devin

- *"What configuration values in timesheet-app are currently hardcoded that should be externalized?"*
- *"What's the simplest feature flag implementation that doesn't require an external service?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to identify all hardcoded configuration values across the codebase. Plan the externalization to cover all settings.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are all configuration values properly externalized? Is the .env.example complete?
- **Leave a comment** asking Devin to add a feature flag for A/B testing a new UI layout

### Key Takeaways

- Configuration externalization follows the 12-factor app methodology — environment variables as the configuration interface
- .env.example files serve as documentation and onboarding aids for new developers
- Simple feature flags (JSON config or environment variables) are sufficient for many use cases — external services add complexity

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot application — use Spring Profiles and add feature flag support.

### Step 1: Paste into Devin

```
Implement configuration management for
uc-spring-boot-upgrade-microservice-extraction:
(1) Create Spring Profile configurations for dev, staging,
and production (application-dev.yml, application-staging
.yml, application-prod.yml), (2) Externalize all hardcoded
values to configuration properties, (3) Add Togglz or
FF4J for feature flag support, (4) Use a feature flag to
gate the GraphQL API — when the flag is off, only REST
endpoints are available.
```

### Step 2: Research with Ask Devin

- *"What Spring Boot configuration properties are hardcoded vs. externalized in this project?"*
- *"Should we use Togglz, FF4J, or a custom @ConditionalOnProperty approach for feature flags?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the current configuration approach and identify what needs to be externalized. Map out which features could benefit from feature flags.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the Spring Profiles properly structured? Do sensitive values use environment variable placeholders?
- **Leave a comment** asking Devin to add a feature flag admin UI endpoint that shows all flags and their states

### Key Takeaways

- Spring Profiles provide built-in environment-specific configuration — Devin structures them following Spring Boot conventions
- Togglz/FF4J provides a feature flag admin UI out of the box, reducing the need for custom tooling
- Gating the GraphQL API behind a feature flag enables gradual rollout of the new API surface

---

<a id="going-further"></a>
## Going Further

### Event-Driven Configuration Audits

When a PR introduces new hardcoded values, automatically trigger a Devin session to externalize them:

```
PR introduces hardcoded configuration values
    → Devin Review flags the hardcoded values
    → Devin session starts: "Externalize the hardcoded
       values introduced in this PR. Add them to the
       config module with environment variable support
       and update .env.example."
    → Devin pushes a fix commit
```

### Scheduled Configuration Drift Detection

Configure a weekly Devin session to audit configuration management:

- Scan for new hardcoded values that should be externalized
- Verify that all environment-specific profiles are consistent (same keys, different values)
- Check for unused configuration properties
- Validate that sensitive values are not committed to the repository
- Open a PR with any fixes found

### Feature Flag Lifecycle Management

Use a scheduled Devin session to audit feature flags:

- Identify flags that have been enabled in production for more than 30 days — these should be cleaned up (remove the flag, keep the feature)
- Identify flags that are disabled in all environments — these are dead code
- Generate a feature flag inventory report showing each flag's status across environments
