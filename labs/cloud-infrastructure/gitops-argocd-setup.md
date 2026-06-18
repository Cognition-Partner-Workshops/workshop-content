# GitOps & ArgoCD Setup

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Repositories](#repositories)
  - [platform-engineering-shared-services](#platform-engineering-shared-services)
  - [ordermanager-microservices](#ordermanager-microservices)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

Pick a repo below, copy the **Step 1** prompt into a new Devin session, and let Devin generate ArgoCD application manifests with environment overlays. No prerequisites beyond repo access.

---

## Challenge

Configure GitOps deployment pipelines with ArgoCD application manifests. Set up automated sync policies, health checks, and rollback strategies for microservice deployments.

## Target Outcomes

- ArgoCD Application manifests for target services
- GitOps directory structure with environment-specific overlays
- Automated sync policies and health check configuration
- PR with ArgoCD manifests and deployment documentation

## What Participants Will Learn

- How Devin understands Kubernetes deployment patterns and translates them into ArgoCD configurations
- How Devin structures GitOps repositories with environment separation (dev/staging/prod)
- Devin's ability to configure sync policies, health checks, and rollback strategies
- How to evaluate GitOps configurations for production readiness

## Devin Features Exercised

- Kubernetes and ArgoCD understanding
- YAML authoring
- GitOps pattern implementation
- PR creation

## Difficulty

Advanced

## Estimated Time

75 minutes

---

## Repositories

### <a id="platform-engineering-shared-services"></a>platform-engineering-shared-services

**Repository:** [platform-engineering-shared-services](https://github.com/Cognition-Partner-Workshops/platform-engineering-shared-services)

Platform engineering repository with shared infrastructure services — the natural home for GitOps deployment configurations.

#### Step 1: Paste into Devin

```
Set up a GitOps deployment pipeline in platform-engineering-shared-services using ArgoCD. Create ArgoCD Application manifests for at least 2 services, using an app-of-apps pattern. Configure the directory structure with base manifests and environment-specific overlays (dev, staging, prod) using Kustomize. Set up automated sync policies with self-healing enabled, configure health checks for deployments, and define a rollback strategy.
```

#### Step 2: Research with Ask Devin

- *"What services are defined in platform-engineering-shared-services that need GitOps deployment? What Kubernetes resources already exist?"*
- *"Should we use the app-of-apps pattern or ApplicationSets for managing multiple services? What are the tradeoffs for this repo's scale?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the existing infrastructure and service definitions. Identify which services should be managed by ArgoCD and what their deployment dependencies are.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the sync policies appropriate? Check that automated sync is only enabled for non-production environments
- **Leave a comment** asking Devin to add a notification configuration that alerts on sync failures
- **Watch Devin respond** and push a follow-up commit

---

### <a id="ordermanager-microservices"></a>ordermanager-microservices

**Repository:** [ordermanager-microservices](https://github.com/Cognition-Partner-Workshops/ordermanager-microservices)

.NET and Angular microservices application — a multi-service deployment target for GitOps configuration.

#### Step 1: Paste into Devin

```
Create ArgoCD Application manifests for deploying ordermanager-microservices using GitOps. Set up a deployment directory with Kustomize bases for each microservice (frontend, backend API, database). Configure ArgoCD sync policies with automated sync for dev, manual sync for production, and sync waves to control deployment ordering. Add health checks and resource hooks for database migrations.
```

#### Step 2: Research with Ask Devin

- *"What microservices make up ordermanager-microservices? What are the deployment dependencies between them?"*
- *"How should we handle database migrations in a GitOps workflow — sync waves, resource hooks, or a separate Job?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the service architecture and inter-service dependencies. This determines the correct sync wave ordering for ArgoCD.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the sync wave ordering correct? Check that database migrations run before application deployments
- **Leave a comment** asking Devin to add a canary deployment strategy for the frontend service
- **Watch Devin respond** and push a follow-up commit

---

## Key Takeaways

- Devin can generate ArgoCD Application manifests with environment-specific overlays by analyzing existing Kubernetes resources and service dependencies
- GitOps configuration is mostly boilerplate with environment-specific values — a strong fit for Devin to generate quickly and accurately
- Sync policies should differ by environment: automated sync for dev/staging, manual approval for production

## Going Further

GitOps configurations pair naturally with **IaC drift detection as scheduled sessions** (see [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md)):

- **Scheduled drift detection** — Run a recurring Devin session that compares the Git-declared state against the live cluster state (`argocd app diff`) and opens a PR if drift is detected
- **Automated manifest updates** — When a new service version is released, a Devin session triggered by a CI event can update the image tag in the GitOps repo and open a PR for review
- **Configuration audits** — Periodic sessions review ArgoCD Application manifests for best-practice compliance (e.g., resource limits set, health checks configured, sync policies appropriate for the environment)
