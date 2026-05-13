# Workshop: Platform-Conformant Microservice Decomposition

## Overview

| | |
|---|---|
| **Focus** | Monolith-to-microservices decomposition with IaC conformance on Kubernetes |
| **Duration** | 1.5–2 hours (75 min lab + 15 min review/discussion) |
| **Audience** | DevOps, platform engineering, solution architects |
| **Key Modules** | [Platform-Conformant Microservice Decomposition](../../modules/cloud-infrastructure/platform-conformant-microservice-decomposition.md) |

## Workshop Narrative

Decomposing a monolith is not just about extracting code — the new service must be deployable, observable, and conformant with organizational platform standards. This workshop has Devin read four repositories simultaneously to decompose a bounded context while generating all the IaC artifacts.

**What makes this workshop unique:** The extracted service must conform to organizational platform standards — namespace placement, network policies, resource quotas, monitoring integration, and GitOps deployment. This mirrors real enterprise decomposition work where "it compiles" is not enough; the service must be deployable and observable within the platform.

## Structure

A single lab with four phases:

| Time | Phase | Activity |
|------|-------|----------|
| 0:00 | **Setup** | Verify repos on Devin machine, create participant branch |
| 0:05 | **Phase 1 — Launch** | Submit decomposition prompt, Devin starts working |
| 0:10 | **Phase 2 — Explore** | Use AskDevin and DeepWiki while Devin works |
| 0:35 | **Phase 3 — Review** | Review PRs, leave feedback comments, watch Devin iterate |
| 0:60 | **Phase 4 — Extend** | Bonus challenges (extract another service, add tracing) |
| 1:15 | **Wrap-up** | Discussion, compare results across participants |

## Repos Required

All four repos must be added via **Settings > Machine configuration > Add repository** before the lab:

- [x] [app_dotnet-angular-monolith](https://github.com/Cognition-Partner-Workshops/app_dotnet-angular-monolith) — .NET 8 + Angular 17 monolith (source)
- [x] [app_dotnet-angular-monolith-iac](https://github.com/Cognition-Partner-Workshops/app_dotnet-angular-monolith-iac) — Helm chart, Dockerfile, ArgoCD patterns (context)
- [x] [platform-engineering-shared-services](https://github.com/Cognition-Partner-Workshops/platform-engineering-shared-services) — EKS cluster, namespaces, monitoring (context)
- [x] [app_dotnet-angular-microservices](https://github.com/Cognition-Partner-Workshops/app_dotnet-angular-microservices) — landing repo for decomposed services + service-level IaC

### Branch Convention

Each participant works on a dedicated branch: **`workshop-<participant>`** (e.g., `workshop-alice`, `workshop-bob`). This prevents conflicts when multiple participants run the lab simultaneously.

### Participant Requirements

- [ ] Devin account on partner-workshops.devinenterprise.com
- [ ] GitHub access to `Cognition-Partner-Workshops` org (for PR review)
- [ ] Browser (Chrome recommended)

---

## Lab Walkthrough

### Phase 1 — Launch the Decomposition (5 min)

Create a new Devin session and paste the following prompt. Replace `<participant>` with your name (e.g., `workshop-alice`):

> Decompose the Inventory module from `app_dotnet-angular-monolith` into a standalone microservice.
>
> Work on branch `workshop-<participant>` in both repos.
>
> Use these repos as context for platform patterns and IaC standards:
> - `platform-engineering-shared-services` — defines the platform standard (namespaces, network policies, monitoring, ArgoCD)
> - `app_dotnet-angular-monolith-iac` — contains the existing Helm chart, Dockerfile, and ArgoCD patterns to follow
>
> Deliverables:
> 1. **New .NET 8 Web API** for the inventory-service with its own models, controllers, services, and EF Core DbContext
> 2. **Angular 17 frontend components** for inventory management
> 3. **Dockerfile** — multi-stage build following the pattern in `app_dotnet-angular-monolith-iac/docker/Dockerfile`
> 4. **Helm chart** — deployment, service, network policy, service monitor, HPA (follow `app_dotnet-angular-monolith-iac/helm/`)
> 5. **ArgoCD application manifests** for dev and staging (follow `app_dotnet-angular-monolith-iac/argocd/`)
> 6. **GitHub Actions CI/CD pipeline** — build, test, push to ECR, trigger ArgoCD sync
> 7. **Monolith refactoring** — replace in-process Inventory calls with an HTTP client that calls the new service
>
> Push the new inventory-service code and all service-level IaC to `app_dotnet-angular-microservices` on branch `workshop-<participant>`. Create a PR.
> Push the monolith refactoring changes to `app_dotnet-angular-monolith` on branch `workshop-<participant>`. Create a PR.
> Build and test both services locally to verify they work together.

**What to expect:** Devin will read all four repos, analyze the domain boundaries, create a task plan, and begin extracting the Inventory module. This typically takes 10–15 minutes to produce initial PRs.

### Phase 2 — Explore While Devin Works (25 min)

While Devin is working autonomously, use these features to deepen your understanding:

#### AskDevin

- *"Analyze the domain boundaries in app_dotnet-angular-monolith. Which modules are tightly coupled and which have clean boundaries? What shared code exists between the Inventory module and other modules?"*
- *"Look at the Helm chart in app_dotnet-angular-monolith-iac and the namespace provisioning in platform-engineering-shared-services. What Kubernetes resources does a new microservice need to conform to the platform standard?"*
- *"What's the best HTTP client pattern in .NET 8 for service-to-service communication? Should we use IHttpClientFactory with typed clients or Refit?"*

#### DeepWiki

Open each repo's DeepWiki page to understand the architecture:

1. **app_dotnet-angular-monolith** — Understand the module structure, shared models, dependency graph between Orders/Products/Customers/Inventory. Identify what code belongs exclusively to Inventory vs. what is shared.
2. **platform-engineering-shared-services** — Understand the namespace provisioning pattern, network policy defaults, and monitoring setup. This defines what the new service must conform to.
3. **app_dotnet-angular-monolith-iac** — Understand the Helm chart structure, Dockerfile build stages, ArgoCD application configuration, and CI/CD pipeline. The new service's IaC should mirror these patterns.

#### Monitor Devin's Progress

Watch the session as Devin works. Key milestones to look for:
- Devin reads all four repos and identifies the Inventory bounded context
- Devin creates the new .NET 8 Web API project structure
- Devin generates the Helm chart, Dockerfile, and ArgoCD manifests
- Devin refactors the monolith to use an HTTP client
- Devin runs tests and creates PRs

### Phase 3 — Review PRs and Give Feedback (25 min)

Once Devin creates PRs, review them on GitHub:

#### Review the Monolith PR

- Does the HTTP client correctly replace all in-process Inventory calls?
- Is the `InventoryServiceClient` properly registered in DI?
- Are there any leftover direct references to Inventory models or DbSets?

#### Review the Microservices PR

- Does the inventory-service follow the platform standard?
- Does the Helm chart include network policies, service monitors, and HPA?
- Does the ArgoCD manifest target the correct namespace (`decomposition-dev`, `decomposition-staging`)?
- Does the directory structure match the expected layout?

**Expected directory structure in app_dotnet-angular-microservices:**
```
app_dotnet-angular-microservices/
├── inventory-service/
│   ├── src/                          <- .NET 8 Web API + Angular frontend
│   ├── tests/                        <- Unit and integration tests
│   ├── docker/
│   │   └── Dockerfile                <- Multi-stage build
│   ├── helm/
│   │   └── inventory-service/        <- Helm chart (deployment, service, networkpolicy, servicemonitor, hpa)
│   ├── argocd/
│   │   ├── application-dev.yaml
│   │   └── application-staging.yaml
│   └── .github/
│       └── workflows/
│           └── build-push.yaml       <- CI/CD pipeline
└── README.md
```

#### Leave Feedback Comments

Try leaving one of these PR comments and watch Devin respond:

- *"Add circuit breaker and retry logic to the InventoryServiceClient using Polly"*
- *"The Helm chart is missing a PodDisruptionBudget — add one with minAvailable: 1"*
- *"Add integration tests that verify the HTTP communication between the monolith and inventory-service"*

**What to expect:** Devin will read the comment, understand the request, implement the change, and push a new commit to the PR. This feedback loop typically takes 3–5 minutes per comment.

### Phase 4 — Bonus Challenges (15 min)

If time permits, extend the session with these follow-up prompts:

- *"Extract the Customers module next, following the same pattern you used for Inventory. Add it to app_dotnet-angular-microservices on the same workshop branch with its own Helm chart and ArgoCD manifests."*
- *"Add OpenTelemetry distributed tracing to both the monolith and inventory-service so we can trace requests across the HTTP boundary."*
- *"Create a Grafana dashboard JSON that shows inventory-service request rate, error rate, and p95 latency using the ServiceMonitor metrics."*

---

## Duration Variants

| Duration | Phases | Notes |
|----------|--------|-------|
| **60 min** | Phases 1–3 only | Skip bonus challenges, shorten explore time to 15 min |
| **90 min** (default) | All four phases | Full experience with bonus challenges |
| **2 hours** | All four phases + extended review | More time for PR feedback loop and second decomposition |

---

## What Participants Will Learn

- How Devin analyzes multiple repos to understand platform standards and architectural patterns
- How Devin decomposes a monolith by extracting a bounded context into a standalone service
- How Devin generates production-grade IaC (Helm, ArgoCD, Dockerfile, CI/CD) that conforms to organizational standards
- Service communication patterns (HTTP client replacing in-process calls)
- How to use context repositories to guide Devin's architectural decisions
- The difference between platform-level infrastructure and service-level infrastructure

## Devin Features Exercised

| Feature | Where Used |
|---------|-----------|
| Multi-repo context awareness | Phase 1 — Devin reads 4 repos simultaneously |
| Architectural analysis | Phase 1 — domain boundary identification |
| Code extraction and refactoring | Phase 1 — .NET and Angular code |
| IaC generation | Phase 1 — Helm, Dockerfile, ArgoCD, GitHub Actions |
| Unit testing | Phase 1 — Devin writes and runs tests |
| PR creation | Phase 1 — PRs in both repos |
| AskDevin | Phase 2 — pre-session architectural planning |
| DeepWiki | Phase 2 — codebase exploration across repos |
| PR comment feedback loop | Phase 3 — iterate on Devin's output |

---

## Key Concepts

### Platform-Level vs. Service-Level Infrastructure

| Layer | What It Contains | Where It Lives |
|-------|-----------------|----------------|
| **Platform-level** | EKS cluster, VPC, namespaces, monitoring stack, ArgoCD, ingress controller | `platform-engineering-shared-services` |
| **Service-level** | Dockerfile, Helm chart, ArgoCD app manifest, CI/CD pipeline | `app_dotnet-angular-microservices` (per-service directory) |

The platform team owns the platform-level infrastructure. Each service team owns their service-level infrastructure but must conform to the platform standard (namespace placement, network policies, resource quotas, monitoring labels).

### The Four Repos

| Repo | Role | Modified? |
|------|------|-----------|
| `app_dotnet-angular-monolith` | Source — extract the Inventory module from here | Yes — refactored to use HTTP client |
| `app_dotnet-angular-microservices` | Landing — new service code + service-level IaC goes here | Yes — new inventory-service directory |
| `platform-engineering-shared-services` | Context — defines what the platform expects | No — read-only reference |
| `app_dotnet-angular-monolith-iac` | Context — provides IaC patterns to follow | No — read-only reference |

## Key Takeaways

- **"Code extraction + IaC generation in one session"** — Devin handles both the code and the infrastructure
- **"Platform conformance by reading existing patterns"** — Devin reads the platform standard repos and generates conformant artifacts
- **"Multi-repo coordination"** — Devin works across 4 repos simultaneously

---

## Facilitator Notes

### Before the Lab

1. Verify all 4 repos are on the Devin machine snapshot (Settings > Machine configuration)
2. Confirm participants have Devin accounts and GitHub access
3. Pre-assign participant names (for `workshop-<participant>` branches) or let participants choose

### During the Lab

- **Phase 1 is hands-off** — participants submit the prompt and wait. Use this time to explain the architecture on a whiteboard or walk through the repos.
- **Phase 2 is exploratory** — encourage participants to use AskDevin and DeepWiki actively. This is a good time for questions.
- **Phase 3 is the most interactive** — participants review real PRs and leave real feedback. Walk around and help participants craft good PR comments.
- **If Devin finishes early** — move to Phase 4 bonus challenges or have participants compare results.

### Common Issues

- **Devin creates a separate repo instead of pushing to the landing repo:** The prompt explicitly says to push to `app_dotnet-angular-microservices`. If Devin tries to create a new repo, leave a comment redirecting it.
- **Branch conflicts between participants:** Each participant uses their own `workshop-<participant>` branch, so conflicts should not occur. If they do, it means two participants used the same name.
- **IaC doesn't match platform standard:** This is a good teaching moment. Have the participant leave a PR comment asking Devin to fix the conformance issue.

### After the Lab

- [ ] Collect participant feedback — especially: did Devin's IaC conform to the platform standard?
- [ ] Archive workshop branches if needed
- [ ] Update [DA8 module](../../modules/cloud-infrastructure/platform-conformant-microservice-decomposition.md) if issues were discovered
- [ ] Share session recordings/artifacts with participants

---

## Related Modules

| Module | Relationship |
|--------|-------------|
| [Platform-Conformant Microservice Decomposition](../../modules/cloud-infrastructure/platform-conformant-microservice-decomposition.md) | Core module for this lab |
| [Containerization & Microservice Extraction](../../modules/migration-modernization/containerization-microservice-extraction.md) | Similar pattern with Java/Spring Boot (simpler — no platform conformance) |
| [CI/CD Pipeline](../../modules/devops-cicd/cicd-pipeline.md) | Complementary — build pipelines for the extracted service |
| [Observability & Monitoring](../../modules/observability-sre/observability-monitoring.md) | Complementary — add dashboards and alerting for the new service |
