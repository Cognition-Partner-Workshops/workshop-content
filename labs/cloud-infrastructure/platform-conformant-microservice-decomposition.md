# Platform-Conformant Microservice Decomposition

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Prerequisites](#prerequisites)
- [Branch Convention](#branch-convention)
- [Notes](#notes)
- [Repositories](#repositories)
  - [ordermanager-monolith](#ordermanager-monolith)
  - [ordermanager-microservices](#ordermanager-microservices)
  - [platform-engineering-shared-services](#platform-engineering-shared-services)
  - [ordermanager-iac](#ordermanager-iac)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

This is a multi-repo advanced lab. Ensure all four repos listed in [Prerequisites](#prerequisites) are connected in Devin's Settings > Repositories. Then copy the **Step 1** prompt from the [ordermanager-monolith](#ordermanager-monolith) section into a new Devin session.

---

## Challenge

Decompose a bounded context from a monolith into a standalone microservice that conforms to an existing platform engineering standard. The new service must include its own IaC (Helm chart, Dockerfile, ArgoCD manifests, CI/CD pipeline) that follows the patterns defined in the platform-engineering-shared-services repo. This exercises Devin's ability to analyze domain boundaries, extract code, generate infrastructure-as-code, and ensure the result conforms to organizational platform standards.

## Target Outcomes

- One bounded context (Inventory) extracted as a standalone .NET 8 Web API microservice
- Monolith refactored to call the new service via HTTP (replacing in-process calls)
- Dockerfile with multi-stage build following the IaC repo pattern
- Helm chart with deployment, service, network policy, service monitor, and HPA
- ArgoCD application manifests for dev and staging environments
- GitHub Actions CI/CD pipeline (build, test, push to ECR, ArgoCD sync)
- All IaC conforming to the platform-engineering-shared-services standard (namespaces, resource quotas, network policies)
- PR in the monolith repo with the refactored code
- PR in ordermanager-microservices with the new service code and service-level IaC

## What Participants Will Learn

- How Devin analyzes multiple repos to understand platform standards and architectural patterns
- How Devin decomposes a monolith by extracting a bounded context into a standalone service
- How Devin generates production-grade IaC (Helm, ArgoCD, Dockerfile, CI/CD) that conforms to organizational standards
- Service communication patterns (HTTP client replacing in-process calls)
- How to use context repositories to guide Devin's architectural decisions
- The difference between platform-level infrastructure and service-level infrastructure

## Devin Features Exercised

- Multi-repo context awareness (reading platform standard + IaC patterns + monolith source)
- Architectural analysis and domain boundary identification
- Code extraction and refactoring (.NET, Angular)
- Infrastructure-as-code generation (Helm, Dockerfile, ArgoCD, GitHub Actions)
- Unit testing and integration testing
- PR creation and PR comment responses
- DeepWiki for codebase exploration across multiple repos
- AskDevin for pre-session architectural planning

## Difficulty

Advanced

## Estimated Time

75 minutes

## Prerequisites

The following repos must be added to the Devin machine via Settings > Repositories:
- `platform-engineering-shared-services` — the platform standard (EKS, namespaces, network policies, monitoring)
- `ordermanager-monolith` — the .NET 8 + Angular 17 monolith to decompose
- `ordermanager-iac` — the existing IaC patterns (Helm chart, Dockerfile, ArgoCD manifests)
- `ordermanager-microservices` — landing repo where decomposed services and service-level IaC are pushed

All four repos are in the [Cognition-Partner-Workshops](https://github.com/Cognition-Partner-Workshops) GitHub org.

## Branch Convention

Each participant works on a dedicated branch: **`workshop-<participant>`** (e.g., `workshop-alice`, `workshop-bob`). This prevents conflicts when multiple participants run the lab simultaneously against the same repos.

Include the branch name in your Devin session prompt so that PRs target the correct branch.

## Notes

- This challenge uses .NET 8 and Angular 17 — a different tech stack from the Java/Spring Boot decomposition in [Containerization & Microservice Extraction](../migration-modernization/containerization-microservice-extraction.md)
- The key differentiator is **platform conformance** — the extracted service must follow the patterns in `platform-engineering-shared-services` and `ordermanager-iac`
- The Inventory bounded context is recommended as the extraction target because it has clear boundaries and moderate complexity
- All new service code and service-level IaC goes into `ordermanager-microservices` — no need to create separate repos per service
- Each participant uses their own `workshop-<participant>` branch to avoid conflicts

---

## Repositories

### <a id="ordermanager-monolith"></a>ordermanager-monolith

**Repository:** [ordermanager-monolith](https://github.com/Cognition-Partner-Workshops/ordermanager-monolith)

.NET 8 + Angular 17 monolith (OrderManager) with four tightly coupled modules: Orders, Products, Customers, and Inventory. All modules share a single SQLite database via Entity Framework Core. The Inventory module manages stock levels, warehouse locations, and low-stock alerts.

**Context Repositories:**
- [platform-engineering-shared-services](https://github.com/Cognition-Partner-Workshops/platform-engineering-shared-services) — Terraform modules for EKS, Helm values for ArgoCD/Prometheus/Grafana, namespace provisioning with resource quotas and network policies
- [ordermanager-iac](https://github.com/Cognition-Partner-Workshops/ordermanager-iac) — Helm chart, multi-stage Dockerfile, ArgoCD application manifests, CI/CD pipeline for the monolith

#### Step 1: Paste into Devin

```
Decompose the Inventory module from ordermanager-monolith into a standalone microservice.

Work on branch `workshop-<participant>` in both repos.

Use these repos as context for platform patterns and IaC standards:
- `platform-engineering-shared-services` — defines the platform standard (namespaces, network policies, monitoring, ArgoCD)
- `ordermanager-iac` — contains the existing Helm chart, Dockerfile, and ArgoCD patterns to follow

Deliverables:
1. New .NET 8 Web API for the inventory-service with its own models, controllers, services, and EF Core DbContext
2. Angular 17 frontend components for inventory management
3. Dockerfile — multi-stage build following the pattern in `ordermanager-iac/docker/Dockerfile`
4. Helm chart — deployment, service, network policy, service monitor, HPA (follow `ordermanager-iac/helm/`)
5. ArgoCD application manifests for dev and staging (follow `ordermanager-iac/argocd/`)
6. GitHub Actions CI/CD pipeline — build, test, push to ECR, trigger ArgoCD sync
7. Monolith refactoring — replace in-process Inventory calls with an HTTP client that calls the new service

Push the new inventory-service code and all service-level IaC to `ordermanager-microservices` on branch `workshop-<participant>`. Create a PR.
Push the monolith refactoring changes to `ordermanager-monolith` on branch `workshop-<participant>`. Create a PR.
Build and test both services locally to verify they work together.
```

#### Step 2: Research with Ask Devin

Use these questions before or during the session to improve results:

- *"Analyze the domain boundaries in ordermanager-monolith. Which modules are tightly coupled and which have clean boundaries? What shared code exists between the Inventory module and other modules?"*
- *"Look at the Helm chart in ordermanager-iac and the namespace provisioning in platform-engineering-shared-services. What Kubernetes resources does a new microservice need to conform to the platform standard?"*
- *"What's the best HTTP client pattern in .NET 8 for service-to-service communication? Should we use IHttpClientFactory with typed clients or Refit?"*

#### Step 3 (Optional): Read the DeepWiki

Open each repo's DeepWiki page to understand the architecture before starting:

1. **ordermanager-monolith** — Understand the module structure, shared models, dependency graph between Orders/Products/Customers/Inventory. Identify what code belongs exclusively to Inventory vs. what is shared.
2. **platform-engineering-shared-services** — Understand the namespace provisioning pattern, network policy defaults, and monitoring setup. This defines what the new service must conform to.
3. **ordermanager-iac** — Understand the Helm chart structure, Dockerfile build stages, ArgoCD application configuration, and CI/CD pipeline. The new service's IaC should mirror these patterns.

#### Step 4 (Optional): Review & Give Feedback

- **Review the monolith PR** — Does the HTTP client correctly replace all in-process Inventory calls? Is the `InventoryServiceClient` properly registered in DI? Are there any leftover direct references to Inventory models or DbSets?
- **Review the new service** — Does it follow the platform standard? Does the Helm chart include network policies, service monitors, and HPA? Does the ArgoCD manifest target the correct namespace (`decomposition-dev`, `decomposition-staging`)?
- **Leave a comment** asking Devin to improve something specific:
  - *"Add circuit breaker and retry logic to the InventoryServiceClient using Polly"*
  - *"The Helm chart is missing a PodDisruptionBudget — add one with minAvailable: 1"*
  - *"Add integration tests that verify the HTTP communication between the monolith and inventory-service"*
- **Watch Devin respond** to your PR comment and push a fix

#### Step 5: Bonus Challenges

If time permits, extend the session with these follow-up prompts:

- *"Extract the Customers module next, following the same pattern you used for Inventory. Add it to ordermanager-microservices on the same workshop branch with its own Helm chart and ArgoCD manifests."*
- *"Add OpenTelemetry distributed tracing to both the monolith and inventory-service so we can trace requests across the HTTP boundary."*
- *"Create a Grafana dashboard JSON that shows inventory-service request rate, error rate, and p95 latency using the ServiceMonitor metrics."*

---

### <a id="ordermanager-microservices"></a>ordermanager-microservices

**Repository:** [ordermanager-microservices](https://github.com/Cognition-Partner-Workshops/ordermanager-microservices)

This is the **landing repository** for all decomposed microservices and their service-level IaC. Each service lives in its own directory with source code, Dockerfile, Helm chart, ArgoCD manifests, and CI/CD pipeline.

**Expected directory structure after decomposition:**
```
ordermanager-microservices/
├── inventory-service/
│   ├── src/                          ← .NET 8 Web API + Angular frontend
│   ├── tests/                        ← Unit and integration tests
│   ├── docker/
│   │   └── Dockerfile                ← Multi-stage build
│   ├── helm/
│   │   └── inventory-service/        ← Helm chart (deployment, service, networkpolicy, servicemonitor, hpa)
│   ├── argocd/
│   │   ├── application-dev.yaml
│   │   └── application-staging.yaml
│   └── .github/
│       └── workflows/
│           └── build-push.yaml       ← CI/CD pipeline
├── customers-service/                ← (bonus challenge)
│   └── ...                           ← Same structure as inventory-service
└── README.md
```

Each participant pushes to their own `workshop-<participant>` branch.

---

### <a id="platform-engineering-shared-services"></a>platform-engineering-shared-services

**Repository:** [platform-engineering-shared-services](https://github.com/Cognition-Partner-Workshops/platform-engineering-shared-services)

This is a **context repository** — it is not modified during the challenge. It defines the platform standard that the extracted microservice must conform to.

**Key patterns to reference:**
- `terraform/modules/namespaces/` — Namespace provisioning with resource quotas, limit ranges, and RBAC
- `k8s/network-policies/` — Default-deny network policies that new services must work within
- `helm-releases/` — Shared Helm values for ArgoCD, Prometheus/Grafana, ingress-nginx, cert-manager
- `terraform/modules/ecr/` — ECR repository provisioning for container images

---

### <a id="ordermanager-iac"></a>ordermanager-iac

**Repository:** [ordermanager-iac](https://github.com/Cognition-Partner-Workshops/ordermanager-iac)

This is a **context repository** — it provides the IaC patterns that the new service's infrastructure should follow.

**Key patterns to reference:**
- `helm/ordermanager/` — Helm chart structure (deployment, service, ingress, network policy, service monitor, HPA)
- `helm/ordermanager/values-dev.yaml` / `values-staging.yaml` — Environment-specific values
- `docker/Dockerfile` — Multi-stage build pattern for .NET + Angular applications
- `argocd/application-dev.yaml` / `application-staging.yaml` — ArgoCD Application manifest format
- `ci/build-push.yaml` — GitHub Actions workflow for build, test, ECR push

---

## Key Takeaways

- Multi-repo awareness is a differentiator: Devin reads the platform standard, the existing IaC patterns, and the monolith source simultaneously to produce conformant output
- Decomposition is a cross-cutting concern — it touches application code, infrastructure, CI/CD, and deployment configuration in a single workflow
- Platform conformance is verifiable: the Helm chart, network policies, and resource limits can be checked against the platform-engineering-shared-services definitions

## Going Further

Microservice decomposition connects to **IaC drift detection as scheduled sessions** (see [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md)):

- **Scheduled platform conformance audits** — After decomposition, run a recurring Devin session that compares each service's Helm chart against the platform standard and opens PRs to remediate any drift (e.g., missing network policies, outdated resource limits)
- **Automated service scaffolding** — When a new bounded context is identified for extraction, trigger a Devin session with a playbook that generates the service skeleton, Helm chart, ArgoCD manifests, and CI/CD pipeline — all conformant to the platform standard from day one
- **Cross-service dependency monitoring** — Periodic sessions analyze HTTP client configurations across decomposed services to detect circular dependencies or missing circuit breakers
