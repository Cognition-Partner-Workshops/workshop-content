# Kubernetes Manifest Generation

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Repositories](#repositories)
  - [petclinic-microservices](#petclinic-microservices)
  - [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

Pick a repo below, copy the **Step 1** prompt into a new Devin session, and let Devin analyze the application and generate production-ready K8s manifests. No prerequisites beyond repo access.

---

## Challenge

Generate Kubernetes manifests (Deployment, Service, Ingress, HPA) from application analysis. Devin examines the app's resource needs and creates production-ready K8s configurations.

## Target Outcomes

- Complete K8s manifest set (Deployment, Service, Ingress, HPA, ConfigMap)
- Resource limits and requests tuned to application profile
- Kustomize or Helm chart structure for environment management
- PR with K8s manifests and deployment guide

## What Participants Will Learn

- How Devin analyzes application code to determine resource requirements and deployment topology
- How Devin generates production-ready Kubernetes manifests with proper health checks and resource limits
- Devin's ability to structure manifests for multi-environment deployment using Kustomize or Helm
- How to evaluate generated K8s configurations for production readiness

## Devin Features Exercised

- Application profiling
- Kubernetes knowledge
- YAML authoring
- Infrastructure design

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## Repositories

### <a id="petclinic-microservices"></a>petclinic-microservices

**Repository:** [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices)

Spring Cloud microservices (API Gateway, Customers, Visits, Vets, Config Server, Discovery Server) — each service needs its own K8s manifest set.

#### Step 1: Paste into Devin

```
Analyze the microservices in petclinic-microservices and generate Kubernetes manifests for deploying them. For each service (api-gateway, customers-service, visits-service, vets-service, config-server, discovery-server), create: Deployment with health checks (liveness and readiness probes using Spring Actuator endpoints), Service, ConfigMap for application properties, and HPA configured based on the service's role. Create an Ingress resource routing external traffic through the API gateway. Use Kustomize with a base and overlays for dev and prod environments.
```

#### Step 2: Research with Ask Devin

- *"What ports do each of the services in petclinic-microservices expose? What health check endpoints are available via Spring Actuator?"*
- *"What are the startup dependencies between services? Should we use init containers or readiness gates to enforce ordering?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the service architecture — particularly the startup ordering (Config Server first, then Discovery Server, then all other services). This determines readiness probe configuration and deployment strategy.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are resource limits reasonable for each service? Check that the Config Server and Discovery Server have adequate memory for their roles
- **Leave a comment** asking Devin to add a NetworkPolicy restricting traffic so only the API Gateway is externally accessible
- **Watch Devin respond** and push a follow-up commit

---

### <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot monolith with a Next.js frontend — generate K8s manifests for deploying as separate backend and frontend services.

#### Step 1: Paste into Devin

```
Analyze uc-spring-boot-upgrade-microservice-extraction and generate Kubernetes manifests for deploying the Spring Boot backend and Next.js frontend as separate services. Create: Deployments with appropriate health checks, Services, Ingress with path-based routing (/api/* to backend, /* to frontend), ConfigMaps for environment configuration, HPA for the backend based on CPU utilization, and a PersistentVolumeClaim for the SQLite database. Use a Helm chart structure with values files for dev and prod.
```

#### Step 2: Research with Ask Devin

- *"What are the resource requirements for the Spring Boot backend and Next.js frontend? What ports do they use?"*
- *"The backend uses SQLite — how should we handle database persistence in Kubernetes? Should we recommend migrating to PostgreSQL for production?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the backend-frontend communication pattern and database setup. Note the SQLite dependency — this affects the persistence strategy in K8s manifests.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the Ingress routing correct? Check that API requests proxy to the backend and all other paths serve the frontend
- **Leave a comment** asking Devin to add a PostgreSQL StatefulSet as an alternative to SQLite with PVC
- **Watch Devin respond** and push a follow-up commit

---

## Key Takeaways

- Devin generates K8s manifests by analyzing actual application code (ports, health endpoints, resource profiles) rather than using generic templates
- Manifest generation is a strong fit for Devin because it requires reading application code and translating it into infrastructure — a cross-domain task that benefits from Devin's ability to hold both contexts simultaneously
- Environment-specific overlays (Kustomize) or values files (Helm) are generated automatically, reducing the risk of environment configuration drift

## Going Further

Kubernetes manifest generation connects to **IaC drift detection as scheduled sessions** (see [Platform Capabilities → Scheduled Sessions](../../reference/general-themes/platform-capabilities.md)):

- **Scheduled manifest validation** — Run a recurring Devin session that applies `kubeval` or `kubeconform` against all manifests in the repo and opens PRs to fix schema violations as Kubernetes versions evolve
- **Resource limit audits** — A periodic session compares declared resource limits against actual utilization metrics (via Prometheus/Datadog MCP) and opens PRs to right-size over- or under-provisioned workloads
- **Security policy compliance** — Scheduled sessions run `kube-linter` or OPA/Gatekeeper policy checks against manifests, remediating findings before they reach the cluster
