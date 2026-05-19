# Configuration Management & Feature Flags

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Implement configuration management and feature flags — externalize application configuration, add feature flag support, and create environment-specific configuration profiles. This exercises Devin's ability to separate deployment configuration from application code.

## Target Outcomes

- Configuration externalized to environment variables with sensible defaults
- Feature flag system integrated (LaunchDarkly, Unleash, or custom implementation)
- Environment-specific configuration profiles (dev, staging, production)
- Feature flag used to gate a specific feature with runtime toggle
- PR with configuration management improvements

## What Participants Will Learn

- How Devin externalizes hardcoded configuration values
- Feature flag implementation patterns (boolean flags, percentage rollouts, user targeting)
- Environment-specific configuration management
- The separation of deployment configuration from application code

## Devin Features Exercised

- Configuration refactoring
- Library integration (feature flag SDKs)
- Environment variable management
- Multi-environment configuration
- PR creation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express application — externalize configuration and add feature flags.

### Step 1: Paste into Devin

> Implement configuration management for timesheet-app: (1) Create a config module that reads all settings from environment variables with sensible defaults (PORT, DATABASE_URL, LOG_LEVEL, etc.), (2) Add dotenv support with .env.example documenting all variables, (3) Implement a simple feature flag system using a JSON config file or environment variables, (4) Use a feature flag to gate a new "dark mode" UI toggle — when the flag is off, the toggle is hidden. Open a PR.

### Step 2: Research with Ask Devin

- *"What configuration values in timesheet-app are currently hardcoded that should be externalized?"*
- *"What's the simplest feature flag implementation that doesn't require an external service?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to identify all hardcoded configuration values across the codebase. Plan the externalization to cover all settings.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are all configuration values properly externalized? Is the .env.example complete?
- **Leave a comment** asking Devin to add a feature flag for A/B testing a new UI layout

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot application — use Spring Profiles and add feature flag support.

### Step 1: Paste into Devin

> Implement configuration management for uc-spring-boot-upgrade-microservice-extraction: (1) Create Spring Profile configurations for dev, staging, and production (application-dev.yml, application-staging.yml, application-prod.yml), (2) Externalize all hardcoded values to configuration properties, (3) Add Togglz or FF4J for feature flag support, (4) Use a feature flag to gate the GraphQL API — when the flag is off, only REST endpoints are available. Open a PR.

### Step 2: Research with Ask Devin

- *"What Spring Boot configuration properties are hardcoded vs. externalized in this project?"*
- *"Should we use Togglz, FF4J, or a custom @ConditionalOnProperty approach for feature flags?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the current configuration approach and identify what needs to be externalized. Map out which features could benefit from feature flags.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the Spring Profiles properly structured? Do sensitive values use environment variable placeholders?
- **Leave a comment** asking Devin to add a feature flag admin UI endpoint that shows all flags and their states
