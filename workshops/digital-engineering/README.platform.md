# Workshop: Digital Engineering

**Other variants:** [Cloud only](README.md)

## Overview

| | |
|---|---|
| **Focus** | CI/CD pipelines, DevOps automation, cloud infrastructure, observability, and platform engineering — the engineering practices that accelerate delivery |
| **Duration** | Flexible (3 tracks, 3 labs each) |
| **Audience** | Platform engineers, DevOps teams, SREs, cloud architects, and engineering leaders building delivery infrastructure |
| **Tracks** | **Track A:** CI/CD & Delivery Automation · **Track B:** Cloud Infrastructure & Platform Engineering · **Track C:** Observability & Incident Response |
| **Delivery** | **Devin Desktop + Devin Cloud** — you work from Desktop, delegate to Cloud, and review results without leaving the editor |

## Workshop Narrative

This workshop covers the three pillars of digital engineering: automating delivery pipelines, managing cloud infrastructure as code, and building observability into production systems. Each track is self-contained — participants can follow all three in sequence for a full-day experience, or focus on one or two tracks in a shorter session.

In this variant you use **Devin Desktop** as your primary interface. Instead of switching between a web app and a browser-based code review tool, you stay in the editor: explore code with Cascade or Devin Local, delegate implementation tasks to Devin Cloud, monitor progress in the Agent Command Center, and review PRs with one-click checkout — no manual `git fetch` or branch switching required.

The tracks are designed to show Devin working across the delivery lifecycle:
- **Track A** shows Devin building and fixing CI/CD pipelines — creating workflows, resolving CI failures, automating releases, and managing feature flags
- **Track B** shows Devin managing infrastructure as code — translating between IaC tools, generating Kubernetes manifests, setting up GitOps, and building platform-conformant services
- **Track C** shows Devin as an SRE partner — setting up observability, investigating incidents, automating remediation, and detecting anomalies

## Platform Context

This workshop uses **Devin Desktop + Devin Cloud**. You will use Desktop as your primary interface — exploring code locally, delegating tasks to Cloud, and reviewing results without leaving the editor.

The workflow across each lab follows the platform continuum:

1. **Create a Space** in Desktop for this workshop to group sessions, PRs, and files
2. **Explore code locally** using Cascade or Devin Local for research and understanding
3. **Delegate to Cloud** — send implementation tasks to Devin Cloud from Desktop
4. **Monitor in the Agent Command Center** — the Kanban board shows session status at a glance
5. **Review PRs in-editor** — one-click checkout of cloud-generated PRs, no browser switching

> **Tip:** Devin Desktop supports multiple agents via the Agent Client Protocol (ACP). Alongside Devin Local and Devin Cloud, you can also use third-party agents (Codex CLI, Claude Agent, Gemini CLI, and others) from the same interface.

## Getting the Most from This Workshop

> **Devin Cloud works autonomously on its own machine.** Once you delegate a task from Desktop, the cloud agent runs independently — you don't need to watch it. Continue exploring code locally, move on to the next lab, or review a previous PR. You'll see the session move to "Ready for review" in the Agent Command Center when it opens a PR.

A few tips to maximize your hands-on time:

- **Set up a Space first.** Create a Space for the workshop in Desktop. New sessions started from the Space automatically inherit the project context.
- **Explore locally, implement in Cloud.** Use Cascade or Devin Local for the research and exploration steps (Step 2 in each lab), then delegate the implementation to Cloud (Step 1). This mirrors real-world usage: think locally, execute in the cloud.
- **Monitor sessions in the Agent Command Center.** The Kanban board shows sessions by status — In Progress, Ready for Review, Completed. When running parallel labs, this is your control panel.
- **Review PRs with one-click checkout.** When a cloud session opens a PR, click to check it out in the editor. Read the diff, run tests locally, and leave review comments — all without leaving Desktop.
- **Try parallel sessions.** Several labs suggest running multiple Devin sessions at once. Delegate them from Desktop, then watch them progress in the Agent Command Center.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item during a session, accept it — this is how teams build a shared context layer. Platform conventions (naming, tagging, resource limits) are especially valuable as Knowledge. Knowledge configured in Desktop is shared across local and cloud agents.
- **Leave PR comments to steer Devin.** Infrastructure PRs benefit from review — ask Devin to add security groups, adjust resource limits, or fix pipeline stages. Comment directly from the editor.

---

## Table of Contents

- [Track A: CI/CD & Delivery Automation](#track-a-cicd--delivery-automation)
  - [Lab A1 — Build a CI/CD Pipeline](#lab-a1--build-a-cicd-pipeline)
  - [Lab A2 — CI Failure Resolution](#lab-a2--ci-failure-resolution)
  - [Lab A3 — Release Management & Feature Flags](#lab-a3--release-management--feature-flags)
- [Track B: Cloud Infrastructure & Platform Engineering](#track-b-cloud-infrastructure--platform-engineering)
  - [Lab B1 — Infrastructure as Code Translation](#lab-b1--infrastructure-as-code-translation)
  - [Lab B2 — Kubernetes Manifests & GitOps](#lab-b2--kubernetes-manifests--gitops)
  - [Lab B3 — Platform-Conformant Service Deployment](#lab-b3--platform-conformant-service-deployment)
- [Track C: Observability & Incident Response](#track-c-observability--incident-response)
  - [Lab C1 — Observability Setup & Monitoring](#lab-c1--observability-setup--monitoring)
  - [Lab C2 — Incident Investigation & Automated Remediation](#lab-c2--incident-investigation--automated-remediation)
  - [Lab C3 — Anomaly Detection & Proactive Alerting](#lab-c3--anomaly-detection--proactive-alerting)
- [Additional Challenges](#additional-challenges)
- [Suggested Formats](#suggested-formats)
- [Repos Required](#repos-required)
- [Context](#context)
- [Devin Features Checklist](#devin-features-checklist)

---

## Track A: CI/CD & Delivery Automation

Track A demonstrates Devin building and managing delivery pipelines. Participants will create CI/CD workflows from scratch, debug and fix failing CI, and set up release management with feature flags.

### Lab A1 — Build a CI/CD Pipeline

- **Module:** [CI/CD Pipeline](../../modules/devops-cicd/cicd-pipeline.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js full-stack app (no existing CI)
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot app with Gradle (alternative)
- **Objective:** Create a production-ready CI/CD pipeline from scratch — build, test, lint, security scan, and deploy stages — using GitHub Actions

#### Step 1: Explore Locally with Cascade or Devin Local

Before delegating the implementation, use Cascade or Devin Local in Desktop to understand the project structure:
- Open the repo in Desktop and ask Cascade to summarize the build system, dependencies, and test setup
- Explore `package.json` (timesheet-app) or `build.gradle` (Spring Boot) to understand the build lifecycle
- Ask Devin Local what CI stages would be appropriate for this tech stack

#### Step 2: Delegate to Devin Cloud from Desktop

From your Space, delegate these as **parallel cloud sessions** — one for the backend pipeline, one for the frontend:

**Session A — Full Pipeline (timesheet-app):**
```
Create a GitHub Actions CI/CD pipeline for timesheet-app that handles both the backend and frontend. The pipeline should: (1) Install dependencies for both backend and frontend, (2) Run linting (ESLint), (3) Run unit tests with coverage reporting, (4) Build the production bundles, (5) Run a security audit (npm audit), (6) Upload test coverage as a build artifact. The pipeline should trigger on PRs to main and on push to main. Use a matrix strategy to test on Node 18 and Node 20.
```

**Session B — Gradle Pipeline (Spring Boot):**
```
Create a GitHub Actions CI/CD pipeline for uc-spring-boot-upgrade-microservice-extraction that: (1) Builds with Gradle, (2) Runs unit tests, (3) Runs integration tests, (4) Generates a JaCoCo coverage report, (5) Runs OWASP Dependency-Check, (6) Fails if any dependency has CVSS >= 7.0, (7) Caches Gradle dependencies between runs. Trigger on PRs and push to main.
```

> Monitor both sessions in the **Agent Command Center**. You'll see them move through In Progress → Ready for Review as they complete.

#### Step 3: Research While Cloud Works

While the cloud sessions run, continue exploring in Desktop:
- Ask Cascade: *"What are the best practices for GitHub Actions pipelines — caching strategies, matrix builds, artifact management?"*
- Ask Devin Local: *"How should a CI pipeline handle secrets for deployment steps?"*
- Explore DeepWiki for the repo to understand the build system in depth
- Consider creating a **Devin Knowledge item** capturing the team's pipeline conventions — Knowledge is shared across local and cloud agents

#### Step 4 (Optional): Review PRs in Desktop

When the cloud sessions open PRs, use **one-click checkout** to review them directly in the editor. Focus on **pipeline quality**:
- **Correctness:** Does the pipeline actually build and test the code correctly?
- **Performance:** Are dependencies cached? Is the pipeline parallelized where possible?
- **Security:** Are secrets handled properly? Is the security scan meaningful?

**Leave a review comment directly from Desktop** and watch the cloud session iterate:
- *"Add dependency caching to speed up the pipeline"*
- *"The security scan should fail the build on CRITICAL and HIGH vulnerabilities only"*
- *"Add a deployment step that deploys to staging when PR is merged to main"*

- **Key Takeaways:**
  - **"Devin understands build systems"** — it reads the project configuration to create appropriate CI steps for the tech stack
  - **"Pipeline as code is reviewable"** — the CI workflow goes through the same PR review process as application code
  - **"Parallel sessions for parallel concerns"** — backend and frontend pipelines can be developed simultaneously, both visible in the Agent Command Center
  - **"Knowledge captures pipeline standards"** — save the pipeline conventions as Devin Knowledge for consistent pipelines across repos. Knowledge is shared between local and cloud agents

- **Target Outcomes (any of these count):**
  - GitHub Actions workflow file with build, test, lint, and security stages
  - Pipeline triggers on PR and push to main
  - Dependency caching for performance
  - Coverage and security reports as build artifacts
  - PR with workflow file and review iterations

---

### Lab A2 — CI Failure Resolution

- **Module:** [CI Failure Resolution](../../modules/devops-cicd/ci-failure-resolution.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app
  - [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) — Spring Boot app (alternative)
- **Objective:** Investigate and fix a failing CI pipeline — read logs, identify the root cause, and implement a fix that gets CI back to green

#### Step 1: Investigate Locally First

Use Cascade or Devin Local in Desktop to analyze the CI failure before delegating the fix:
- Ask Cascade to read the CI configuration and identify what step is failing
- Use Devin Local to inspect the test files or dependency configuration locally
- This local investigation helps you write a more targeted prompt for Cloud

#### Step 2: Delegate the Fix to Devin Cloud

From your Space, delegate the fix:

**Option A — Fix Failing Tests (Node.js):**
```
The CI pipeline for timesheet-app is failing on the test step. Investigate the CI logs, identify which tests are failing and why, fix the underlying issues (not the tests unless the tests themselves are wrong), and get the pipeline back to green. Document what was wrong and how you fixed it in the PR description.
```

**Option B — Fix Security Scan Failures (Gradle):**
```
The CI pipeline for uc-cve-remediation-regulatory-compliance is failing because the OWASP Dependency-Check found CRITICAL vulnerabilities. Investigate which dependencies are flagged, upgrade them to versions without known CVEs, verify the build still passes, and re-run the security scan to confirm the issues are resolved, documenting which CVEs were resolved.
```

#### Step 3: Research While Cloud Works

While the cloud session works on the fix, continue in Desktop:
- Ask Cascade: *"What are the most common reasons CI pipelines fail? How do you systematically debug them?"*
- Ask Devin Local: *"How do you distinguish between flaky tests and genuine failures?"*
- Explore: *"What's the fastest way to identify the root cause of a failing CI step — should you reproduce locally first?"*

#### Step 4 (Optional): Review the Fix in Desktop

Use one-click checkout to review the fix PR. Focus on **root cause accuracy**:
- **Real fix:** Does the change fix the underlying issue or just work around it?
- **No side effects:** Does the fix break anything else?
- **Prevention:** Is there something to prevent this failure from recurring?

**Leave a review comment from Desktop:**
- *"Is this a flaky test or a genuine regression? Add a comment explaining the root cause."*
- *"The dependency upgrade might have breaking changes — check the changelog"*
- *"Add a test that specifically verifies the behavior that was broken"*

- **Key Takeaways:**
  - **"Devin reads CI logs"** — it understands build output, test failures, and security scan reports to identify root causes
  - **"Fix the code, not the pipeline"** — most CI failures indicate real code issues, not pipeline configuration problems
  - **"Local investigation sharpens cloud prompts"** — using Cascade or Devin Local to understand the failure first produces a more targeted cloud session
  - **"CI is a feedback loop"** — failing CI is a feature, not a bug. The system is telling you something is wrong

- **Target Outcomes (any of these count):**
  - Root cause of CI failure identified
  - Fix implemented and CI passing (green)
  - PR description documenting what failed and why
  - Preventive measures added (tests, checks, retry logic)
  - PR with fix and evidence of CI passing

---

### Lab A3 — Release Management & Feature Flags

- **Modules:** [Release Management](../../modules/devops-cicd/release-management.md) + [Configuration Management & Feature Flags](../../modules/devops-cicd/configuration-management-feature-flags.md)
- **Repositories:**
  - [ordermanager-monolith](https://github.com/Cognition-Partner-Workshops/ordermanager-monolith) — .NET 8 + Angular 17 OrderManager with IaC support
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app (alternative)
- **Objective:** Implement release management practices — semantic versioning, changelogs, release branches — and add feature flags for gradual rollout of new functionality

#### Step 1: Explore the Release Process Locally

Use Cascade or Devin Local in Desktop to understand the current release process:
- Ask Cascade to summarize how the app is currently versioned, built, and deployed
- Review the existing CI/CD configuration and identify what release steps are missing
- Use Devin Local to check whether the repo follows conventional commits

#### Step 2: Delegate to Devin Cloud

From your Space, delegate the implementation:

**Option A — Release Pipeline (.NET):**
```
Add release management to ordermanager-monolith: (1) Create a GitHub Actions workflow that automatically generates a CHANGELOG.md from conventional commits, (2) Implement semantic versioning using git tags, (3) Create a release workflow that builds a Docker image, tags it with the version, and creates a GitHub Release with release notes. Add a `RELEASING.md` documenting the release process.
```

**Option B — Feature Flags (Node.js):**
```
Add a feature flag system to timesheet-app. Implement a simple feature flag service that reads flags from a JSON config file. Add flags for: (1) 'enable_csv_export' — gates the CSV export feature, (2) 'enable_dark_mode' — gates a UI theme toggle, (3) 'enable_bulk_operations' — gates batch operations on work entries. Add a feature flag admin endpoint (GET /api/flags, PUT /api/flags/:name) and integrate flag checks into the relevant frontend components and API routes. Write tests for the feature flag service.
```

#### Step 3: Research While Cloud Works

While the cloud session works, continue in Desktop:
- Ask Cascade: *"What are the best practices for semantic versioning in a monorepo vs. a single-service repo?"*
- Ask Devin Local: *"What feature flag patterns exist — percentage-based rollout, user targeting, environment-based?"*
- Explore: *"How do teams typically manage feature flags in production — when should flags be cleaned up?"*

#### Step 4 (Optional): Review in Desktop

Use one-click checkout to review. Focus on **operational readiness**:
- **Release process:** Is the process clear and repeatable? Can a new team member follow it?
- **Flag hygiene:** How will stale flags be identified and removed?
- **Rollback:** If a release goes wrong, what's the rollback procedure?

**Leave a review comment from Desktop:**
- *"Add a pre-release validation step that runs smoke tests before publishing"*
- *"The feature flag config should support percentage-based rollout, not just on/off"*
- *"Add a CI check that fails if a feature flag has been in the codebase for more than 30 days"*

- **Key Takeaways:**
  - **"Automate the release"** — manual releases are error-prone. Automated pipelines ensure consistency
  - **"Feature flags decouple deploy from release"** — deploy code anytime, release features when ready
  - **"Conventional commits enable automation"** — structured commit messages allow automated changelog generation
  - **"Release documentation is part of the pipeline"** — changelogs and release notes should be generated, not hand-written

- **Target Outcomes (any of these count):**
  - Release workflow with semantic versioning and changelog generation
  - Feature flag service with admin API
  - Flag integration in frontend and backend code
  - `RELEASING.md` documenting the release process
  - PR with release infrastructure and review iterations

---

## Track B: Cloud Infrastructure & Platform Engineering

Track B demonstrates Devin managing infrastructure as code. Participants will translate between IaC tools, generate Kubernetes manifests, set up GitOps workflows, and build platform-conformant deployments.

### Lab B1 — Infrastructure as Code Translation

- **Module:** [IaC Translation](../../modules/cloud-infrastructure/iac-translation.md)
- **Repositories:**
  - [timesheet-infra](https://github.com/Cognition-Partner-Workshops/timesheet-infra) — Terraform-based hosting infrastructure
  - [platform-engineering-shared-services](https://github.com/Cognition-Partner-Workshops/platform-engineering-shared-services) — AWS CDK (TypeScript) platform infrastructure (alternative)
- **Objective:** Translate infrastructure definitions between IaC tools — Terraform to AWS CDK, CloudFormation to Terraform, or Helm to Kustomize — demonstrating Devin's ability to work across infrastructure tooling

#### Step 1: Understand the Infrastructure Locally

Use Cascade or Devin Local in Desktop to understand the existing IaC before translating:
- Ask Cascade to map out the Terraform resources and their dependencies
- Use Devin Local to list the AWS resources, security groups, and IAM roles defined
- Ask about edge cases: *"Are there any Terraform features here that don't map cleanly to CDK?"*

#### Step 2: Delegate the Translation to Devin Cloud

From your Space, delegate the translation:

**Option A — Terraform to CDK:**
```
Translate the Terraform infrastructure in timesheet-infra to AWS CDK (TypeScript). Preserve all resources, security groups, IAM roles, and networking configuration. The CDK stack should produce the same infrastructure as the Terraform code. Add a `MIGRATION_NOTES.md` documenting the mapping between Terraform resources and CDK constructs, any differences in behavior, and verification steps. Include CDK unit tests using the assertions module.
```

**Option B — CDK Module Extraction:**
```
The platform-engineering-shared-services repo has a large CDK stack. Extract the VPC and networking configuration into a reusable CDK construct library that can be shared across multiple stacks. The construct should accept parameters for CIDR ranges, availability zones, and subnet configuration. Add unit tests and a README explaining how to use the construct.
```

#### Step 3: Research While Cloud Works

While the cloud session works, continue in Desktop:
- Ask Cascade: *"What's the current infrastructure defined in timesheet-infra? What AWS resources does it create?"*
- Ask Devin Local: *"What are the main differences between Terraform and AWS CDK for the same infrastructure? Are there any Terraform features that don't map cleanly to CDK?"*
- Explore: *"What are the best practices for CDK construct libraries — how should they be structured for reuse?"*

#### Step 4 (Optional): Review in Desktop

Use one-click checkout to review the translated IaC. Focus on **translation fidelity**:
- **Same resources:** Does the CDK produce the same infrastructure as Terraform?
- **Security parity:** Are security groups, IAM policies, and encryption settings preserved?
- **Best practices:** Does the CDK code follow CDK conventions (constructs, stacks, app)?

**Leave a review comment from Desktop:**
- *"Add tags to all resources matching the Terraform tagging convention"*
- *"The VPC construct should use the CDK VPC construct instead of raw CfnVPC"*
- *"Add a comparison table in the migration notes showing Terraform → CDK resource mapping"*

- **Key Takeaways:**
  - **"IaC is a language choice"** — the underlying infrastructure is the same regardless of whether you use Terraform, CDK, or CloudFormation. Devin translates between them
  - **"Translation preserves intent"** — the goal isn't line-by-line conversion but producing identical infrastructure from a different tool
  - **"Migration notes capture decisions"** — documenting the mapping helps future teams understand why specific CDK patterns were chosen
  - **"Tests verify infrastructure"** — CDK assertions let you test your infrastructure code like application code

- **Target Outcomes (any of these count):**
  - IaC translated from one tool to another (e.g., Terraform → CDK)
  - `MIGRATION_NOTES.md` with resource mapping
  - Unit tests for the translated infrastructure
  - Same infrastructure resources produced by both tools
  - PR with translated code and documentation

---

### Lab B2 — Kubernetes Manifests & GitOps

- **Modules:** [Kubernetes Manifest Generation](../../modules/cloud-infrastructure/kubernetes-manifest-generation.md) + [GitOps & ArgoCD Setup](../../modules/cloud-infrastructure/gitops-argocd-setup.md)
- **Repositories:**
  - [ordermanager-iac](https://github.com/Cognition-Partner-Workshops/ordermanager-iac) — Helm charts, ArgoCD manifests, and CI/CD for the OrderManager application
  - [platform-engineering-shared-services](https://github.com/Cognition-Partner-Workshops/platform-engineering-shared-services) — EKS platform with ArgoCD and monitoring (context)
- **Objective:** Generate Kubernetes deployment manifests (Helm charts), configure GitOps-based deployment with ArgoCD, and set up automated sync from git to cluster

#### Step 1: Understand the Cluster Locally

Use Cascade or Devin Local in Desktop to understand the existing K8s setup:
- Ask Cascade to summarize the existing Helm chart structure and ArgoCD configuration
- Use Devin Local to compare the platform's conventions against Kubernetes best practices
- Explore what resource limits, health checks, and network policies the platform expects

#### Step 2: Delegate to Devin Cloud

From your Space, delegate the implementation:

**Option A — Helm Chart Creation:**
```
Create a production-ready Helm chart for a new microservice in ordermanager-iac. The chart should include: Deployment (with health checks, resource limits, rolling update strategy), Service (ClusterIP), HorizontalPodAutoscaler (min 2, max 10 replicas, 70% CPU target), ConfigMap for environment variables, ServiceAccount with IRSA annotation, and NetworkPolicy restricting ingress. Follow the existing chart patterns in the repo. Add a values.yaml with sensible defaults and a values-production.yaml overlay.
```

**Option B — ArgoCD Application Setup:**
```
Configure ArgoCD GitOps deployment for ordermanager-iac. Create ArgoCD Application manifests that: (1) Point to the Helm chart in this repo, (2) Configure auto-sync with self-heal and prune enabled, (3) Set up sync waves for proper deployment ordering (namespace → configmap → deployment → service → ingress), (4) Add health checks that ArgoCD uses to determine deployment success. Follow the ArgoCD patterns in platform-engineering-shared-services for reference.
```

#### Step 3: Research While Cloud Works

While the cloud session works, continue in Desktop:
- Ask Cascade: *"What's the current Helm chart structure in ordermanager-iac? What patterns does it follow?"*
- Ask Devin Local: *"What are Kubernetes best practices for production workloads — resource limits, pod disruption budgets, topology spread constraints?"*
- Explore: *"How does ArgoCD sync wave ordering work? What's the recommended ordering for microservice deployments?"*

#### Step 4 (Optional): Review in Desktop

Use one-click checkout to review the manifests. Focus on **production readiness**:
- **Resource limits:** Are CPU/memory requests and limits set appropriately?
- **Health checks:** Do liveness and readiness probes check the right endpoints?
- **Security:** Is the pod running as non-root? Are filesystem and capabilities restricted?

**Leave a review comment from Desktop:**
- *"Add a PodDisruptionBudget with minAvailable: 1 for high availability"*
- *"The readiness probe should check /health/ready not just /health"*
- *"Add topology spread constraints so pods aren't all on the same node"*

- **Key Takeaways:**
  - **"Helm charts are templates"** — they parameterize Kubernetes manifests so the same chart works across environments
  - **"GitOps = git as source of truth"** — ArgoCD ensures the cluster state matches what's in git, automatically reconciling drift
  - **"Production readiness is more than 'it runs'"** — health checks, resource limits, HPA, and network policies are required
  - **"Platform conventions via Knowledge"** — save the Helm chart patterns as Devin Knowledge so future services follow the same structure. Knowledge is shared across Desktop and Cloud agents

- **Target Outcomes (any of these count):**
  - Production-ready Helm chart with required Kubernetes resources
  - ArgoCD Application manifest with auto-sync configuration
  - Values files for different environments (dev/staging/production)
  - Network policies and security constraints
  - PR with Kubernetes manifests and review iterations

---

### Lab B3 — Platform-Conformant Service Deployment

- **Module:** [Platform-Conformant Microservice Decomposition](../../modules/cloud-infrastructure/platform-conformant-microservice-decomposition.md)
- **Repositories:**
  - [ordermanager-monolith](https://github.com/Cognition-Partner-Workshops/ordermanager-monolith) — .NET 8 + Angular 17 OrderManager monolith
  - [ordermanager-iac](https://github.com/Cognition-Partner-Workshops/ordermanager-iac) — Service IaC (Helm, Dockerfile, ArgoCD, CI/CD)
  - [platform-engineering-shared-services](https://github.com/Cognition-Partner-Workshops/platform-engineering-shared-services) — Platform standard (EKS, ArgoCD, monitoring, namespace provisioning)
- **Objective:** Deploy a service to a platform-engineering-managed Kubernetes cluster following the platform team's standards — Dockerfile, Helm chart, ArgoCD app, CI/CD pipeline, and namespace configuration that conform to the platform conventions

#### Step 1: Map Platform Requirements Locally

Use Cascade or Devin Local in Desktop to understand both the platform standards and the application:
- Ask Cascade: *"What are the platform standards defined in platform-engineering-shared-services? What does a service need to provide?"*
- Use Devin Local to explore the application's runtime requirements in ordermanager-monolith
- Review the existing IaC patterns in ordermanager-iac for conventions to follow

#### Step 2: Delegate to Devin Cloud

From your Space, delegate the deployment preparation:

```
Prepare the Orders module from ordermanager-monolith for deployment on the platform defined in platform-engineering-shared-services. Create all required platform-conformant artifacts: (1) Multi-stage Dockerfile optimized for .NET 8, (2) Helm chart following the platform's chart conventions (check existing charts in ordermanager-iac), (3) ArgoCD Application manifest pointing to the Helm chart, (4) GitHub Actions CI/CD pipeline that builds the Docker image, pushes to ECR, and updates the Helm values with the new image tag, (5) Namespace request following the platform's provisioning pattern (resource quotas, limit ranges, network policies). Reference the platform conventions in platform-engineering-shared-services for all standards. Add all artifacts to ordermanager-iac.
```

> This lab benefits from **parallel cloud sessions** — try deploying multiple modules simultaneously. Monitor them in the Agent Command Center's Kanban view.

#### Step 3: Research While Cloud Works

While the cloud session works, continue in Desktop:
- Ask Cascade: *"What namespace provisioning patterns does the platform use? What resource quotas and limit ranges are standard?"*
- Ask Devin Local: *"How does the CI/CD pipeline integrate with ArgoCD — image tag update strategy?"*
- Explore both repos' DeepWiki pages to understand platform expectations and application requirements

Try from Desktop:
- Have Devin Local add **Prometheus metrics** to the .NET service and note the ServiceMonitor pattern
- Ask Cascade about **horizontal pod autoscaling** based on custom metrics
- Delegate **multiple modules in parallel** to separate cloud sessions and monitor them in the Agent Command Center

#### Step 4 (Optional): Review in Desktop

Use one-click checkout to review. Focus on **platform conformance**:
- **Standards compliance:** Does the deployment follow the platform conventions?
- **Security:** Are IRSA roles scoped correctly? Is the network policy restrictive enough?
- **Operability:** Can the platform team manage this service with their standard tools?

**Leave a review comment from Desktop:**
- *"The Dockerfile should use the platform's approved base image from ECR"*
- *"Add Prometheus annotations to the pod spec for automatic scraping"*
- *"The CI pipeline should use the shared workflow defined in the platform repo"*

- **Key Takeaways:**
  - **"Platform conformance accelerates delivery"** — when services follow platform standards, they get monitoring, security, and deployment automation for free
  - **"Devin reads platform docs"** — it cross-references the platform conventions and applies them to a new service
  - **"IaC is per-service"** — each microservice owns its deployment artifacts (Dockerfile, Helm chart, pipeline), following the platform's patterns
  - **"Parallel decomposition from Desktop"** — multiple modules can be delegated to separate cloud sessions and monitored in the Agent Command Center

- **Target Outcomes (any of these count):**
  - Platform-conformant Dockerfile (multi-stage, approved base image)
  - Helm chart following platform conventions
  - ArgoCD Application manifest with proper sync configuration
  - CI/CD pipeline integrated with the platform's deployment flow
  - PR to the IaC repo with deployment artifacts

---

## Track C: Observability & Incident Response

Track C demonstrates Devin as an SRE partner. Participants will set up observability instrumentation, investigate production incidents, and build automated remediation workflows.

### Lab C1 — Observability Setup & Monitoring

- **Module:** [Observability & Monitoring](../../modules/observability-sre/observability-monitoring.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app (add observability)
  - [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices) — Spring Boot microservices with existing observability support (alternative)
- **Objective:** Add observability instrumentation to an application — structured logging, metrics exposition, distributed tracing, and health check endpoints

#### Step 1: Assess Current Observability Locally

Use Cascade or Devin Local in Desktop to understand the current observability state:
- Ask Cascade to scan the codebase for existing logging, metrics, and health check patterns
- Use Devin Local to identify which observability gaps exist (missing structured logging, no metrics endpoint, etc.)
- Review what monitoring infrastructure the app would need

#### Step 2: Delegate to Devin Cloud

From your Space, delegate the instrumentation:

**Option A — Node.js Observability (timesheet-app):**
```
Add production observability to timesheet-app: (1) Structured JSON logging using Winston or Pino (replace console.log calls), (2) Prometheus metrics endpoint at /metrics exposing request count, latency histograms, and error rate, (3) Health check endpoints at /health/live and /health/ready, (4) Request correlation IDs (X-Request-ID header) propagated through all log entries, (5) Error tracking with stack traces in structured format. Add a `docker-compose.monitoring.yml` that runs Prometheus and Grafana alongside the app, with a pre-configured Grafana dashboard showing the key metrics.
```

**Option B — Spring Boot Observability (microservices):**
```
Review the observability setup in petclinic-microservices. Add or improve: (1) Distributed tracing configuration using Micrometer Tracing (ensure trace IDs propagate across service calls), (2) Custom business metrics (appointment count, vet availability), (3) Grafana dashboards for the key service metrics, (4) Alert rules for error rate > 5% and p99 latency > 2 seconds. Document the observability architecture in an `OBSERVABILITY.md`.
```

#### Step 3: Research While Cloud Works

While the cloud session works, continue in Desktop:
- Ask Cascade: *"What are the three pillars of observability — logs, metrics, and traces? How do they complement each other?"*
- Ask Devin Local: *"What metrics should a REST API expose? What's the RED method (Rate, Errors, Duration)?"*
- Explore: *"What Grafana dashboard patterns are most useful for microservices — USE method, RED method, or custom?"*

#### Step 4 (Optional): Review in Desktop

Use one-click checkout to review the observability PR. Focus on **observability quality**:
- **Signal-to-noise:** Are the metrics meaningful for diagnosing real issues?
- **Performance:** Does instrumentation add noticeable latency?
- **Completeness:** Can you trace a request from ingress through services to the database?

**Leave a review comment from Desktop:**
- *"Add a histogram for database query latency — slow queries are the most common performance issue"*
- *"The structured logs should include the user ID for debugging user-reported issues"*
- *"Add a Grafana alert that fires when error rate exceeds 5% for 5 minutes"*

- **Key Takeaways:**
  - **"Observability is not logging"** — it's the combination of logs, metrics, and traces that gives you full visibility into production behavior
  - **"Metrics drive alerts"** — Prometheus metrics enable automated alerting on SLO violations
  - **"Correlation IDs connect the dots"** — a single request ID traced through logs and spans makes debugging distributed systems possible
  - **"Dashboards tell stories"** — a well-designed Grafana dashboard shows system health at a glance and enables drill-down for investigation

- **Target Outcomes (any of these count):**
  - Structured logging with correlation IDs
  - Prometheus metrics endpoint with RED metrics
  - Health check endpoints (liveness + readiness)
  - Grafana dashboard with pre-configured panels
  - PR with observability instrumentation and documentation

---

### Lab C2 — Incident Investigation & Automated Remediation

- **Modules:** [Incident Response & Triage](../../modules/observability-sre/incident-response-triage.md) + [Pod Remediation After Credential Rotation](../../modules/observability-sre/pod-remediation-credential-rotation.md)
- **Repositories:**
  - [eventflow-devin-integration](https://github.com/Cognition-Partner-Workshops/eventflow-devin-integration) — Azure Monitor alert → Devin investigation pipeline
  - [uc-pod-remediation-credential-rotation](https://github.com/Cognition-Partner-Workshops/uc-pod-remediation-credential-rotation) — Automated Kubernetes pod remediation system (alternative)
- **Objective:** Set up automated incident investigation where alerts trigger Devin sessions that analyze logs, identify root causes, and open fix PRs — showing Devin as an on-call engineering partner

#### Step 1: Explore the Pipeline Locally

Use Cascade or Devin Local in Desktop to understand the alert-to-fix pipeline:
- Ask Cascade to trace the data flow: *"How does an Azure Monitor alert become a Devin session in eventflow-devin-integration?"*
- Use Devin Local to explore the existing alert handlers and webhook receiver code
- Identify what alert types are supported and where new handlers should go

#### Step 2: Delegate to Devin Cloud

From your Space, delegate the implementation:

**Option A — Alert-to-Fix Pipeline (EventFlow):**
```
Review the eventflow-devin-integration codebase. This system receives Azure Monitor alert webhooks and triggers Devin sessions to investigate production incidents. Set up the FastAPI webhook receiver locally. Simulate an alert for a "500 Internal Server Error" spike in the payment-service. Verify the system generates the correct investigation prompt for Devin. Add a new alert handler for "High Latency" alerts that generates an appropriate investigation prompt focused on database query performance and connection pool exhaustion. Write tests for the new handler.
```

**Option B — Credential Rotation Remediation:**
```
Review the uc-pod-remediation-credential-rotation system. This automates the remediation of pod failures after credential rotations. Set up the system locally and simulate a scenario where database credentials are rotated and pods start CrashLoopBackOff. Verify the system detects the failure, identifies it as credential-related, and generates the appropriate remediation steps. Add support for a new credential type (API keys for external services) that follows the same detection → approval → remediation pattern. Write tests.
```

> Monitor the cloud session from the Agent Command Center. This lab pairs well with Lab C1 — run both in parallel.

#### Step 3: Research While Cloud Works

While the cloud session works, continue in Desktop:
- Ask Cascade: *"How does the EventFlow alert-to-Devin pipeline work? What's the data flow from Azure Monitor alert to Devin session?"*
- Ask Devin Local: *"What are the most common Kubernetes pod failure patterns after credential rotation? How should each be remediated?"*
- Explore: *"What approval workflows should gate automated remediation? When is it safe to auto-remediate vs. requiring human approval?"*

#### Step 4 (Optional): Review in Desktop

Use one-click checkout to review. Focus on **operational safety**:
- **Blast radius:** Can the automated remediation make things worse?
- **Approval gates:** Are high-risk actions gated behind human approval?
- **Idempotency:** What happens if the remediation runs twice?

**Leave a review comment from Desktop:**
- *"Add rate limiting so the system doesn't create 100 Devin sessions for a cascading failure"*
- *"The remediation should verify the fix worked before closing the incident"*
- *"Add a dry-run mode that shows what the remediation would do without executing"*

- **Key Takeaways:**
  - **"Alerts → Devin → Fix"** — production alerts can trigger autonomous investigation and remediation sessions
  - **"Pattern matching enables automation"** — common incidents (credential rotation, OOM, connection pool) follow predictable patterns that can be automated
  - **"Safety gates prevent over-automation"** — high-risk remediations require approval; low-risk ones can auto-execute
  - **"Desktop as incident command"** — the Agent Command Center shows incident-response sessions alongside your regular work, giving you a unified view of both

- **Target Outcomes (any of these count):**
  - Alert webhook receiver handling production alerts
  - Investigation prompt generation for different alert types
  - Automated remediation for a specific failure pattern
  - Approval workflow for high-risk remediations
  - PR with new alert handlers and tests

---

### Lab C3 — Anomaly Detection & Proactive Alerting

- **Module:** [Volume Anomaly Detection](../../modules/observability-sre/volume-anomaly-detection.md)
- **Repositories:**
  - [uc-volume-anomaly-detection](https://github.com/Cognition-Partner-Workshops/uc-volume-anomaly-detection) — Python-based anomaly detection with z-score and seasonal decomposition
- **Objective:** Set up proactive anomaly detection that identifies issues before they cause outages — showing Devin building and extending a monitoring system that catches problems early

#### Step 1: Understand the Detection System Locally

Use Cascade or Devin Local in Desktop to understand the existing detection algorithms:
- Ask Cascade to explain the z-score and seasonal decomposition approaches used
- Use Devin Local to explore the test data and understand what constitutes an anomaly
- Identify extension points for new detectors

#### Step 2: Delegate to Devin Cloud

From your Space, delegate the extension:

```
Review the uc-volume-anomaly-detection system. This Python application detects volume-based anomalies using z-score analysis and seasonal decomposition. Set up and run the system locally. Then extend it: (1) Add a new detector type that uses rate-of-change analysis (detect when a metric is changing faster than historical norms, even if the absolute value is still within bounds), (2) Add a correlation engine that checks whether volume anomalies in one service coincide with changes in upstream services, (3) Add a recommendation engine that suggests runbook actions based on the anomaly pattern and correlated services. Write tests for the new detector and correlation engine.
```

#### Step 3: Research While Cloud Works

While the cloud session works, continue in Desktop:
- Ask Cascade: *"What anomaly detection algorithms does uc-volume-anomaly-detection currently implement? How do z-score and seasonal decomposition complement each other?"*
- Ask Devin Local: *"What's the difference between reactive alerting (threshold breach) and proactive alerting (anomaly detection)? When is each appropriate?"*
- Explore: *"How should anomaly detection handle seasonality — weekly patterns, time-of-day effects, holiday traffic?"*

#### Step 4 (Optional): Review in Desktop

Use one-click checkout to review. Focus on **detection quality**:
- **False positive rate:** Will this alert too often (alert fatigue)?
- **Detection latency:** How quickly after an anomaly starts will it be detected?
- **Actionability:** When an anomaly is detected, does the alert include enough context to act on?

**Leave a review comment from Desktop:**
- *"Add a configurable sensitivity threshold — some services need tighter detection than others"*
- *"The correlation engine should include a confidence score"*
- *"Add historical false positive tracking so the system learns which patterns are benign"*

- **Key Takeaways:**
  - **"Proactive > reactive"** — anomaly detection catches issues while they're forming, before users are impacted
  - **"Correlation reduces noise"** — linking anomalies across services reduces false positives and identifies cascading failures
  - **"Runbook recommendations accelerate response"** — when the system suggests actions, engineers respond faster
  - **"Continuous improvement"** — anomaly detection systems get better over time as they learn normal patterns

- **Target Outcomes (any of these count):**
  - New anomaly detector (rate-of-change or alternative algorithm)
  - Cross-service correlation engine
  - Runbook recommendation system
  - Tests verifying detection accuracy
  - PR with new detection capabilities

---

## Additional Challenges

Participants who finish early or want to explore further can attempt any challenge from the full [module catalog](../../modules/). Recommended extras:

| Challenge | Module | Repo | Track | Difficulty |
|-----------|--------|------|-------|------------|
| Cost Optimization Analysis | [Cost Optimization](../../modules/cloud-infrastructure/cost-optimization-analysis.md) | platform-engineering-shared-services | B | Intermediate |
| Terraform Module Extraction | [Terraform Module Extraction](../../modules/cloud-infrastructure/terraform-module-extraction.md) | timesheet-infra | B | Intermediate |
| PR Review Automation | [PR Review](../../modules/devops-cicd/pr-review-automation.md) | Any | A | Beginner |
| Incident Response & Triage | [Incident Response](../../modules/observability-sre/incident-response-triage.md) | ordermanager-monolith | C | Advanced |
| CI/CD Pipeline | [CI/CD Pipeline](../../modules/devops-cicd/cicd-pipeline.md) | Any app repo | A | Intermediate |

## Suggested Formats

| Format | Recommended Approach |
|--------|---------------------|
| Full day | All 3 tracks: Track A (morning) + Track B (midday) + Track C (afternoon) |
| Half day | 2 tracks: Choose any two tracks based on audience interest |
| Short session | 1 full track + 1 lab from another track |
| Sampler | Pick 1 lab from each track (e.g., A1 + B2 + C1) for breadth |
| Single lab | A1 (CI/CD pipeline) or C2 (incident response) recommended for impact |

## Repos Required

### Track A (CI/CD & Delivery Automation)
- [ ] timesheet-app
- [ ] uc-spring-boot-upgrade-microservice-extraction (optional, for Lab A1 Session B)
- [ ] uc-cve-remediation-regulatory-compliance (optional, for Lab A2 Option B)
- [ ] ordermanager-monolith (for Lab A3 Option A)

### Track B (Cloud Infrastructure & Platform Engineering)
- [ ] timesheet-infra (for Lab B1)
- [ ] platform-engineering-shared-services (for Labs B1, B2, B3 — as reference)
- [ ] ordermanager-iac (for Labs B2, B3)
- [ ] ordermanager-monolith (for Lab B3)

### Track C (Observability & Incident Response)
- [ ] timesheet-app (for Lab C1 Option A)
- [ ] petclinic-microservices (optional, for Lab C1 Option B)
- [ ] eventflow-devin-integration (for Lab C2 Option A)
- [ ] uc-pod-remediation-credential-rotation (optional, for Lab C2 Option B)
- [ ] uc-volume-anomaly-detection (for Lab C3)

## Context

- **Audience:** Platform engineers, DevOps teams, SREs, and cloud architects building and managing delivery infrastructure
- **Focus:** The full digital engineering lifecycle — pipelines, infrastructure, and observability — demonstrating Devin as a platform and operations partner across the Desktop + Cloud continuum
- **Devin value themes woven throughout:**
  - Desktop as the primary interface — explore locally, delegate to Cloud, review in-editor
  - Agent Command Center for monitoring parallel cloud sessions
  - Spaces for organizing workshop tasks, sessions, and PRs
  - One-click checkout for reviewing cloud-generated PRs without leaving Desktop
  - ACP extensibility — use multiple agents (Devin Local, Cascade, third-party) alongside Cloud
  - Knowledge and Playbooks for capturing platform standards, shared across local and cloud agents
  - Scheduled sessions for ongoing infrastructure hygiene (drift detection, cost reports)
  - Cross-repo work — platform changes that span multiple repositories
  - Long-running task handling — delegate to Cloud while you continue coding locally

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../modules/devin-features/README.md) throughout the session.
