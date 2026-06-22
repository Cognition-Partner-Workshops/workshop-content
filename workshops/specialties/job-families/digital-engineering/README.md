# Workshop: Digital Engineering

## Overview

| | |
|---|---|
| **Focus** | CI/CD pipelines, DevOps automation, cloud infrastructure, observability, and platform engineering — the engineering practices that accelerate delivery |
| **Duration** | Flexible (3 tracks, 3 labs each) |
| **Audience** | Platform engineers, DevOps teams, SREs, cloud architects, and engineering leaders building delivery infrastructure |
| **Tracks** | **Track A:** CI/CD & Delivery Automation · **Track B:** Cloud Infrastructure & Platform Engineering · **Track C:** Observability & Incident Response |

## Workshop Narrative

This workshop covers the three pillars of digital engineering: automating delivery pipelines, managing cloud infrastructure as code, and building observability into production systems. Each track is self-contained — participants can follow all three in sequence for a full-day experience, or focus on one or two tracks in a shorter session.

The tracks are designed to show Devin working across the delivery lifecycle:
- **Track A** shows Devin building and fixing CI/CD pipelines — creating workflows, resolving CI failures, automating releases, and managing feature flags
- **Track B** shows Devin managing infrastructure as code — translating between IaC tools, generating Kubernetes manifests, setting up GitOps, and building platform-conformant services
- **Track C** shows Devin as an SRE partner — setting up observability, investigating incidents, automating remediation, and detecting anomalies

## Getting the Most from This Workshop

> **Devin works autonomously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Each lab has a "Paste into Devin" step and a separate "Review & Give Feedback" step. Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin will keep working in the background.
- **Try parallel sessions.** Several labs suggest running multiple Devin sessions at once. This mirrors real enterprise usage where Devin handles infrastructure work across many repos.
- **Use Ask Devin to understand existing infrastructure.** Before making changes to IaC or pipelines, use Ask Devin to understand the current state and dependencies.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item during a session, accept it — this is how teams build a shared context layer. Platform conventions (naming, tagging, resource limits) are especially valuable as Knowledge.
- **Leave PR comments to steer Devin.** Infrastructure PRs benefit from review — ask Devin to add security groups, adjust resource limits, or fix pipeline stages.

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

- **Module:** [CI/CD Pipeline](../../../../labs/devops-cicd/cicd-pipeline.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js full-stack app (no existing CI)
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot app with Gradle (alternative)
- **Objective:** Create a production-ready CI/CD pipeline from scratch — build, test, lint, security scan, and deploy stages — using GitHub Actions

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

Run these as **parallel sessions** — one for the backend pipeline, one for the frontend:

**Session A — Full Pipeline (timesheet-app):**
```
Create a GitHub Actions CI/CD pipeline for timesheet-app that handles both the backend and frontend. The pipeline should: (1) Install dependencies for both backend and frontend, (2) Run linting (ESLint), (3) Run unit tests with coverage reporting, (4) Build the production bundles, (5) Run a security audit (npm audit), (6) Upload test coverage as a build artifact. The pipeline should trigger on PRs to main and on push to main. Use a matrix strategy to test on Node 18 and Node 20.
```

**Session B — Gradle Pipeline (Spring Boot):**
```
Create a GitHub Actions CI/CD pipeline for uc-spring-boot-upgrade-microservice-extraction that: (1) Builds with Gradle, (2) Runs unit tests, (3) Runs integration tests, (4) Generates a JaCoCo coverage report, (5) Runs OWASP Dependency-Check, (6) Fails if any dependency has CVSS >= 7.0, (7) Caches Gradle dependencies between runs. Trigger on PRs and push to main.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the best practices for GitHub Actions pipelines — caching strategies, matrix builds, artifact management?"*
- *"How should a CI pipeline handle secrets for deployment steps?"*
- *"What's the optimal balance between pipeline speed and thoroughness? Should security scans run on every PR or only on main?"*
- Consider creating a **Devin Knowledge item** capturing the team's pipeline conventions

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the build system. Try:
- Have Devin add **Docker image build and push** to the pipeline
- Ask Devin to add **PR status checks** that block merge until CI passes
- Try having Devin add **automatic versioning** using semantic-release or similar
- Ask Devin to add **parallel test sharding** for faster CI runs

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR, focus your review on **pipeline quality**:
- **Correctness:** Does the pipeline actually build and test the code correctly?
- **Performance:** Are dependencies cached? Is the pipeline parallelized where possible?
- **Security:** Are secrets handled properly? Is the security scan meaningful?

**Leave a feedback comment** and watch Devin respond:
- *"Add dependency caching to speed up the pipeline"*
- *"The security scan should fail the build on CRITICAL and HIGH vulnerabilities only"*
- *"Add a deployment step that deploys to staging when PR is merged to main"*

- **Key Takeaways:**
  - **"Devin understands build systems"** — it reads the project configuration to create appropriate CI steps for the tech stack
  - **"Pipeline as code is reviewable"** — the CI workflow goes through the same PR review process as application code
  - **"Parallel sessions for parallel concerns"** — backend and frontend pipelines can be developed simultaneously
  - **"Knowledge captures pipeline standards"** — save the pipeline conventions as Devin Knowledge for consistent pipelines across all repos

- **Target Outcomes (any of these count):**
  - GitHub Actions workflow file with build, test, lint, and security stages
  - Pipeline triggers on PR and push to main
  - Dependency caching for performance
  - Coverage and security reports as build artifacts
  - PR with workflow file and review iterations

---

### Lab A2 — CI Failure Resolution

- **Module:** [CI Failure Resolution](../../../../labs/devops-cicd/ci-failure-resolution.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app
  - [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) — Spring Boot app (alternative)
- **Objective:** Investigate and fix a failing CI pipeline — read logs, identify the root cause, and implement a fix that gets CI back to green

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

**Option A — Fix Failing Tests (Node.js):**
```
The CI pipeline for timesheet-app is failing on the test step. Investigate the CI logs, identify which tests are failing and why, fix the underlying issues (not the tests unless the tests themselves are wrong), and get the pipeline back to green. Document what was wrong and how you fixed it in the PR description.
```

**Option B — Fix Security Scan Failures (Gradle):**
```
The CI pipeline for uc-cve-remediation-regulatory-compliance is failing because the OWASP Dependency-Check found CRITICAL vulnerabilities. Investigate which dependencies are flagged, upgrade them to versions without known CVEs, verify the build still passes, and re-run the security scan to confirm the issues are resolved. documenting which CVEs were resolved.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the most common reasons CI pipelines fail? How do you systematically debug them?"*
- *"How do you distinguish between flaky tests and genuine failures?"*
- *"What's the fastest way to identify the root cause of a failing CI step — should you reproduce locally first?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add **retry logic** for flaky test steps
- Ask Devin to add **CI failure notifications** (Slack webhook or email)
- Try having Devin add a **CI health dashboard** that tracks build success rate over time

#### Step 4 (Optional): Review & Give Feedback

Focus on **root cause accuracy**:
- **Real fix:** Does the change fix the underlying issue or just work around it?
- **No side effects:** Does the fix break anything else?
- **Prevention:** Is there something to prevent this failure from recurring?

**Leave a feedback comment:**
- *"Is this a flaky test or a genuine regression? Add a comment explaining the root cause."*
- *"The dependency upgrade might have breaking changes — check the changelog"*
- *"Add a test that specifically verifies the behavior that was broken"*

- **Key Takeaways:**
  - **"Devin reads CI logs"** — it understands build output, test failures, and security scan reports to identify root causes
  - **"Fix the code, not the pipeline"** — most CI failures indicate real code issues, not pipeline configuration problems
  - **"Document the resolution"** — the PR description should explain what failed and why, so the team learns from it
  - **"CI is a feedback loop"** — failing CI is a feature, not a bug. The system is telling you something is wrong

- **Target Outcomes (any of these count):**
  - Root cause of CI failure identified
  - Fix implemented and CI passing (green)
  - PR description documenting what failed and why
  - Preventive measures added (tests, checks, retry logic)
  - PR with fix and evidence of CI passing

---

### Lab A3 — Release Management & Feature Flags

- **Modules:** [Release Management](../../../../labs/devops-cicd/release-management.md) + [Configuration Management & Feature Flags](../../../../labs/devops-cicd/configuration-management-feature-flags.md)
- **Repositories:**
  - [ordermanager-monolith](https://github.com/Cognition-Partner-Workshops/ordermanager-monolith) — .NET 8 + Angular 17 OrderManager with IaC support
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app (alternative)
- **Objective:** Implement release management practices — semantic versioning, changelogs, release branches — and add feature flags for gradual rollout of new functionality

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

**Option A — Release Pipeline (.NET):**
```
Add release management to ordermanager-monolith: (1) Create a GitHub Actions workflow that automatically generates a CHANGELOG.md from conventional commits, (2) Implement semantic versioning using git tags, (3) Create a release workflow that builds a Docker image, tags it with the version, and creates a GitHub Release with release notes. Add a `RELEASING.md` documenting the release process.
```

**Option B — Feature Flags (Node.js):**
```
Add a feature flag system to timesheet-app. Implement a simple feature flag service that reads flags from a JSON config file. Add flags for: (1) 'enable_csv_export' — gates the CSV export feature, (2) 'enable_dark_mode' — gates a UI theme toggle, (3) 'enable_bulk_operations' — gates batch operations on work entries. Add a feature flag admin endpoint (GET /api/flags, PUT /api/flags/:name) and integrate flag checks into the relevant frontend components and API routes. Write tests for the feature flag service.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the best practices for semantic versioning in a monorepo vs. a single-service repo?"*
- *"What feature flag patterns exist — percentage-based rollout, user targeting, environment-based?"*
- *"How do teams typically manage feature flags in production — when should flags be cleaned up?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add **canary deployment** configuration using the feature flags
- Ask Devin to add **flag lifecycle management** — auto-detect stale flags
- Try having Devin add **A/B testing** infrastructure using the feature flag system

#### Step 4 (Optional): Review & Give Feedback

Focus on **operational readiness**:
- **Release process:** Is the process clear and repeatable? Can a new team member follow it?
- **Flag hygiene:** How will stale flags be identified and removed?
- **Rollback:** If a release goes wrong, what's the rollback procedure?

**Leave a feedback comment:**
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

- **Module:** [IaC Translation](../../../../labs/cloud-infrastructure/iac-translation.md)
- **Repositories:**
  - [timesheet-infra](https://github.com/Cognition-Partner-Workshops/timesheet-infra) — Terraform-based hosting infrastructure
  - [platform-engineering-shared-services](https://github.com/Cognition-Partner-Workshops/platform-engineering-shared-services) — AWS CDK (TypeScript) platform infrastructure (alternative)
- **Objective:** Translate infrastructure definitions between IaC tools — Terraform to AWS CDK, CloudFormation to Terraform, or Helm to Kustomize — demonstrating Devin's ability to work across infrastructure tooling

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

**Option A — Terraform to CDK:**
```
Translate the Terraform infrastructure in timesheet-infra to AWS CDK (TypeScript). Preserve all resources, security groups, IAM roles, and networking configuration. The CDK stack should produce the same infrastructure as the Terraform code. Add a `MIGRATION_NOTES.md` documenting the mapping between Terraform resources and CDK constructs, any differences in behavior, and verification steps. Include CDK unit tests using the assertions module.
```

**Option B — CDK Module Extraction:**
```
The platform-engineering-shared-services repo has a large CDK stack. Extract the VPC and networking configuration into a reusable CDK construct library that can be shared across multiple stacks. The construct should accept parameters for CIDR ranges, availability zones, and subnet configuration. Add unit tests and a README explaining how to use the construct.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What's the current infrastructure defined in timesheet-infra? What AWS resources does it create?"*
- *"What are the main differences between Terraform and AWS CDK for the same infrastructure? Are there any Terraform features that don't map cleanly to CDK?"*
- *"What are the best practices for CDK construct libraries — how should they be structured for reuse?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add **CDK drift detection** that compares the stack to the deployed state
- Ask Devin to add **cost estimation** using the CDK cost analysis tools
- Try having Devin create **multiple environment configurations** (dev/staging/prod) using CDK context

#### Step 4 (Optional): Review & Give Feedback

Focus on **translation fidelity**:
- **Same resources:** Does the CDK produce the same infrastructure as Terraform?
- **Security parity:** Are security groups, IAM policies, and encryption settings preserved?
- **Best practices:** Does the CDK code follow CDK conventions (constructs, stacks, app)?

**Leave a feedback comment:**
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

- **Modules:** [Kubernetes Manifest Generation](../../../../labs/cloud-infrastructure/kubernetes-manifest-generation.md) + [GitOps & ArgoCD Setup](../../../../labs/cloud-infrastructure/gitops-argocd-setup.md)
- **Repositories:**
  - [ordermanager-iac](https://github.com/Cognition-Partner-Workshops/ordermanager-iac) — Helm charts, ArgoCD manifests, and CI/CD for the OrderManager application
  - [platform-engineering-shared-services](https://github.com/Cognition-Partner-Workshops/platform-engineering-shared-services) — EKS platform with ArgoCD and monitoring (context)
- **Objective:** Generate Kubernetes deployment manifests (Helm charts), configure GitOps-based deployment with ArgoCD, and set up automated sync from git to cluster

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

**Option A — Helm Chart Creation:**
```
Create a production-ready Helm chart for a new microservice in ordermanager-iac. The chart should include: Deployment (with health checks, resource limits, rolling update strategy), Service (ClusterIP), HorizontalPodAutoscaler (min 2, max 10 replicas, 70% CPU target), ConfigMap for environment variables, ServiceAccount with IRSA annotation, and NetworkPolicy restricting ingress. Follow the existing chart patterns in the repo. Add a values.yaml with sensible defaults and a values-production.yaml overlay.
```

**Option B — ArgoCD Application Setup:**
```
Configure ArgoCD GitOps deployment for ordermanager-iac. Create ArgoCD Application manifests that: (1) Point to the Helm chart in this repo, (2) Configure auto-sync with self-heal and prune enabled, (3) Set up sync waves for proper deployment ordering (namespace → configmap → deployment → service → ingress), (4) Add health checks that ArgoCD uses to determine deployment success. Follow the ArgoCD patterns in platform-engineering-shared-services for reference.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What's the current Helm chart structure in ordermanager-iac? What patterns does it follow?"*
- *"What are Kubernetes best practices for production workloads — resource limits, pod disruption budgets, topology spread constraints?"*
- *"How does ArgoCD sync wave ordering work? What's the recommended ordering for microservice deployments?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add **Prometheus ServiceMonitor** to the Helm chart for metrics scraping
- Ask Devin to add **pod disruption budgets** for high availability
- Try having Devin create a **Kustomize overlay** alternative to the Helm chart
- Ask Devin to add **Istio VirtualService** for traffic management

#### Step 4 (Optional): Review & Give Feedback

Focus on **production readiness**:
- **Resource limits:** Are CPU/memory requests and limits set appropriately?
- **Health checks:** Do liveness and readiness probes check the right endpoints?
- **Security:** Is the pod running as non-root? Are filesystem and capabilities restricted?

**Leave a feedback comment:**
- *"Add a PodDisruptionBudget with minAvailable: 1 for high availability"*
- *"The readiness probe should check /health/ready not just /health"*
- *"Add topology spread constraints so pods aren't all on the same node"*

- **Key Takeaways:**
  - **"Helm charts are templates"** — they parameterize Kubernetes manifests so the same chart works across environments
  - **"GitOps = git as source of truth"** — ArgoCD ensures the cluster state matches what's in git, automatically reconciling drift
  - **"Production readiness is more than 'it runs'"** — health checks, resource limits, HPA, and network policies are all required
  - **"Platform conventions via Knowledge"** — save the Helm chart patterns as Devin Knowledge so all future services follow the same structure

- **Target Outcomes (any of these count):**
  - Production-ready Helm chart with all required Kubernetes resources
  - ArgoCD Application manifest with auto-sync configuration
  - Values files for different environments (dev/staging/production)
  - Network policies and security constraints
  - PR with Kubernetes manifests and review iterations

---

### Lab B3 — Platform-Conformant Service Deployment

- **Module:** [Platform-Conformant Microservice Decomposition](../../../../labs/cloud-infrastructure/platform-conformant-microservice-decomposition.md)
- **Repositories:**
  - [ordermanager-monolith](https://github.com/Cognition-Partner-Workshops/ordermanager-monolith) — .NET 8 + Angular 17 OrderManager monolith
  - [ordermanager-iac](https://github.com/Cognition-Partner-Workshops/ordermanager-iac) — Service IaC (Helm, Dockerfile, ArgoCD, CI/CD)
  - [platform-engineering-shared-services](https://github.com/Cognition-Partner-Workshops/platform-engineering-shared-services) — Platform standard (EKS, ArgoCD, monitoring, namespace provisioning)
- **Objective:** Deploy a service to a platform-engineering-managed Kubernetes cluster following the platform team's standards — Dockerfile, Helm chart, ArgoCD app, CI/CD pipeline, and namespace configuration that all conform to the platform conventions

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Prepare the Orders module from ordermanager-monolith for deployment on the platform defined in platform-engineering-shared-services. Create all required platform-conformant artifacts: (1) Multi-stage Dockerfile optimized for .NET 8, (2) Helm chart following the platform's chart conventions (check existing charts in ordermanager-iac), (3) ArgoCD Application manifest pointing to the Helm chart, (4) GitHub Actions CI/CD pipeline that builds the Docker image, pushes to ECR, and updates the Helm values with the new image tag, (5) Namespace request following the platform's provisioning pattern (resource quotas, limit ranges, network policies). Reference the platform conventions in platform-engineering-shared-services for all standards. to ordermanager-iac.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the platform standards defined in platform-engineering-shared-services? What does a service need to provide to deploy on this platform?"*
- *"What namespace provisioning patterns does the platform use? What resource quotas and limit ranges are standard?"*
- *"How does the CI/CD pipeline integrate with ArgoCD — image tag update strategy?"*

#### Step 3 (Optional): Read the DeepWiki

Open both repos' **DeepWiki** pages to understand:
- The platform's expectations (from platform-engineering-shared-services)
- The application's runtime requirements (from ordermanager-monolith)
- The existing IaC patterns (from ordermanager-iac)

Try:
- Have Devin add **Prometheus metrics** to the .NET service and ServiceMonitor to the Helm chart
- Ask Devin to add **horizontal pod autoscaling** based on custom metrics
- Try deploying **multiple modules in parallel** using separate Devin sessions

#### Step 4 (Optional): Review & Give Feedback

Focus on **platform conformance**:
- **Standards compliance:** Does the deployment follow all platform conventions?
- **Security:** Are IRSA roles scoped correctly? Is the network policy restrictive enough?
- **Operability:** Can the platform team manage this service with their standard tools?

**Leave a feedback comment:**
- *"The Dockerfile should use the platform's approved base image from ECR"*
- *"Add Prometheus annotations to the pod spec for automatic scraping"*
- *"The CI pipeline should use the shared workflow defined in the platform repo"*

- **Key Takeaways:**
  - **"Platform conformance accelerates delivery"** — when services follow platform standards, they get monitoring, security, and deployment automation for free
  - **"Devin reads platform docs"** — it cross-references the platform conventions and applies them to a new service
  - **"IaC is per-service"** — each microservice owns its deployment artifacts (Dockerfile, Helm chart, pipeline), following the platform's patterns
  - **"Parallel decomposition"** — multiple modules can be prepared for deployment simultaneously in separate Devin sessions

- **Target Outcomes (any of these count):**
  - Platform-conformant Dockerfile (multi-stage, approved base image)
  - Helm chart following platform conventions
  - ArgoCD Application manifest with proper sync configuration
  - CI/CD pipeline integrated with the platform's deployment flow
  - PR to the IaC repo with all deployment artifacts

---

## Track C: Observability & Incident Response

Track C demonstrates Devin as an SRE partner. Participants will set up observability instrumentation, investigate production incidents, and build automated remediation workflows.

### Lab C1 — Observability Setup & Monitoring

- **Module:** [Observability & Monitoring](../../../../labs/observability-sre/observability-monitoring.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app (add observability)
  - [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices) — Spring Boot microservices with existing observability support (alternative)
- **Objective:** Add observability instrumentation to an application — structured logging, metrics exposition, distributed tracing, and health check endpoints

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

**Option A — Node.js Observability (timesheet-app):**
```
Add production observability to timesheet-app: (1) Structured JSON logging using Winston or Pino (replace console.log calls), (2) Prometheus metrics endpoint at /metrics exposing request count, latency histograms, and error rate, (3) Health check endpoints at /health/live and /health/ready, (4) Request correlation IDs (X-Request-ID header) propagated through all log entries, (5) Error tracking with stack traces in structured format. Add a `docker-compose.monitoring.yml` that runs Prometheus and Grafana alongside the app, with a pre-configured Grafana dashboard showing the key metrics.
```

**Option B — Spring Boot Observability (microservices):**
```
Review the observability setup in petclinic-microservices. Add or improve: (1) Distributed tracing configuration using Micrometer Tracing (ensure trace IDs propagate across service calls), (2) Custom business metrics (appointment count, vet availability), (3) Grafana dashboards for the key service metrics, (4) Alert rules for error rate > 5% and p99 latency > 2 seconds. Document the observability architecture in an `OBSERVABILITY.md`.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the three pillars of observability — logs, metrics, and traces? How do they complement each other?"*
- *"What metrics should a REST API expose? What's the RED method (Rate, Errors, Duration)?"*
- *"What Grafana dashboard patterns are most useful for microservices — USE method, RED method, or custom?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add **OpenTelemetry** instrumentation for vendor-neutral observability
- Ask Devin to create **SLO-based alerting** (e.g., 99.9% success rate over 30 days)
- Try having Devin add **log aggregation** with a local ELK or Loki stack

#### Step 4 (Optional): Review & Give Feedback

Focus on **observability quality**:
- **Signal-to-noise:** Are the metrics meaningful for diagnosing real issues?
- **Performance:** Does instrumentation add noticeable latency?
- **Completeness:** Can you trace a request from ingress through all services to the database?

**Leave a feedback comment:**
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

- **Modules:** [Incident Response & Triage](../../../../labs/observability-sre/incident-response-triage.md) + [Pod Remediation After Credential Rotation](../../../../labs/observability-sre/pod-remediation-credential-rotation.md)
- **Repositories:**
  - [eventflow-devin-integration](https://github.com/Cognition-Partner-Workshops/eventflow-devin-integration) — Azure Monitor alert → Devin investigation pipeline
  - [uc-pod-remediation-credential-rotation](https://github.com/Cognition-Partner-Workshops/uc-pod-remediation-credential-rotation) — Automated Kubernetes pod remediation system (alternative)
- **Objective:** Set up automated incident investigation where alerts trigger Devin sessions that analyze logs, identify root causes, and open fix PRs — demonstrating Devin as an always-on on-call engineer

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

**Option A — Alert-to-Fix Pipeline (EventFlow):**
```
Review the eventflow-devin-integration codebase. This system receives Azure Monitor alert webhooks and triggers Devin sessions to investigate production incidents. Set up the FastAPI webhook receiver locally. Simulate an alert for a "500 Internal Server Error" spike in the payment-service. Verify the system generates the correct investigation prompt for Devin. Add a new alert handler for "High Latency" alerts that generates an appropriate investigation prompt focused on database query performance and connection pool exhaustion. Write tests for the new handler.
```

**Option B — Credential Rotation Remediation:**
```
Review the uc-pod-remediation-credential-rotation system. This automates the remediation of pod failures after credential rotations. Set up the system locally and simulate a scenario where database credentials are rotated and pods start CrashLoopBackOff. Verify the system detects the failure, identifies it as credential-related, and generates the appropriate remediation steps. Add support for a new credential type (API keys for external services) that follows the same detection → approval → remediation pattern. Write tests.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"How does the EventFlow alert-to-Devin pipeline work? What's the data flow from Azure Monitor alert to Devin session?"*
- *"What are the most common Kubernetes pod failure patterns after credential rotation? How should each be remediated?"*
- *"What approval workflows should gate automated remediation? When is it safe to auto-remediate vs. requiring human approval?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add **alert deduplication** to prevent multiple Devin sessions for the same incident
- Ask Devin to add **runbook selection** based on alert type
- Try having Devin add **post-incident report generation** that summarizes what happened and how it was fixed

#### Step 4 (Optional): Review & Give Feedback

Focus on **operational safety**:
- **Blast radius:** Can the automated remediation make things worse?
- **Approval gates:** Are high-risk actions gated behind human approval?
- **Idempotency:** What happens if the remediation runs twice?

**Leave a feedback comment:**
- *"Add rate limiting so the system doesn't create 100 Devin sessions for a cascading failure"*
- *"The remediation should verify the fix worked before closing the incident"*
- *"Add a dry-run mode that shows what the remediation would do without executing"*

- **Key Takeaways:**
  - **"Alerts → Devin → Fix"** — production alerts can trigger autonomous investigation and remediation sessions
  - **"Pattern matching enables automation"** — common incidents (credential rotation, OOM, connection pool) follow predictable patterns that can be automated
  - **"Safety gates prevent over-automation"** — high-risk remediations require approval; low-risk ones can auto-execute
  - **"Devin is always on-call"** — unlike human engineers, Devin responds to alerts immediately at any hour

- **Target Outcomes (any of these count):**
  - Alert webhook receiver handling production alerts
  - Investigation prompt generation for different alert types
  - Automated remediation for a specific failure pattern
  - Approval workflow for high-risk remediations
  - PR with new alert handlers and tests

---

### Lab C3 — Anomaly Detection & Proactive Alerting

- **Module:** [Volume Anomaly Detection](../../../../labs/observability-sre/volume-anomaly-detection.md)
- **Repositories:**
  - [uc-volume-anomaly-detection](https://github.com/Cognition-Partner-Workshops/uc-volume-anomaly-detection) — Python-based anomaly detection with z-score and seasonal decomposition
- **Objective:** Set up proactive anomaly detection that identifies issues before they cause outages — demonstrating Devin building and extending a monitoring system that catches problems early

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Review the uc-volume-anomaly-detection system. This Python application detects volume-based anomalies using z-score analysis and seasonal decomposition. Set up and run the system locally. Then extend it: (1) Add a new detector type that uses rate-of-change analysis (detect when a metric is changing faster than historical norms, even if the absolute value is still within bounds), (2) Add a correlation engine that checks whether volume anomalies in one service coincide with changes in upstream services, (3) Add a recommendation engine that suggests runbook actions based on the anomaly pattern and correlated services. Write tests for the new detector and correlation engine.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What anomaly detection algorithms does uc-volume-anomaly-detection currently implement? How do z-score and seasonal decomposition complement each other?"*
- *"What's the difference between reactive alerting (threshold breach) and proactive alerting (anomaly detection)? When is each appropriate?"*
- *"How should anomaly detection handle seasonality — weekly patterns, time-of-day effects, holiday traffic?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page. Try:
- Have Devin add **adaptive thresholds** that adjust based on time-of-day and day-of-week
- Ask Devin to add **multi-variate anomaly detection** that considers multiple metrics simultaneously
- Try having Devin integrate the anomaly detection with the **alert-to-Devin pipeline** from Lab C2

#### Step 4 (Optional): Review & Give Feedback

Focus on **detection quality**:
- **False positive rate:** Will this alert too often (alert fatigue)?
- **Detection latency:** How quickly after an anomaly starts will it be detected?
- **Actionability:** When an anomaly is detected, does the alert include enough context to act on?

**Leave a feedback comment:**
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

Participants who finish early or want to explore further can attempt any challenge from the full [module catalog](../../../../labs/). Recommended extras:

| Challenge | Module | Repo | Track | Difficulty |
|-----------|--------|------|-------|------------|
| Cost Optimization Analysis | [Cost Optimization](../../../../labs/cloud-infrastructure/cost-optimization-analysis.md) | platform-engineering-shared-services | B | Intermediate |
| Terraform Module Extraction | [Terraform Module Extraction](../../../../labs/cloud-infrastructure/terraform-module-extraction.md) | timesheet-infra | B | Intermediate |
| PR Review Automation | [PR Review](../../../../labs/devops-cicd/pr-review-automation.md) | Any | A | Beginner |
| Incident Response & Triage | [Incident Response](../../../../labs/observability-sre/incident-response-triage.md) | ordermanager-monolith | C | Advanced |
| CI/CD Pipeline | [CI/CD Pipeline](../../../../labs/devops-cicd/cicd-pipeline.md) | Any app repo | A | Intermediate |

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
- **Focus:** The full digital engineering lifecycle — pipelines, infrastructure, and observability — demonstrating Devin as a platform and operations partner
- **Devin value themes woven throughout:**
  - Autonomous, off-machine execution — kick off infrastructure sessions and move on
  - Parallel sessions for provisioning multiple environments/services simultaneously
  - Ask Devin for understanding existing infrastructure before making changes
  - Knowledge and Playbooks for capturing platform standards and conventions
  - Scheduled sessions for ongoing infrastructure hygiene (drift detection, cost reports)
  - Cross-repo work — platform changes that span multiple repositories
  - Long-running task handling — Devin works on complex infrastructure while you focus on architecture

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../../../labs/devin-features/README.md) throughout the session.
