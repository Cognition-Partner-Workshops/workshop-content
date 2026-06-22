# Cloud-Native Refactor

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Challenge](#challenge)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Going Further](#going-further)
- [Notes](#notes)
- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Quick Start

Paste this prompt into Devin to try cloud-native refactoring:

```
Make timesheet-app more cloud-native: add a /health
endpoint, externalize all configuration to environment
variables, add structured JSON logging with Winston,
create a Dockerfile with multi-stage build, and add a
Kubernetes deployment manifest with resource limits and
readiness probes.
```

---

## Repositories

- [timesheet-app](#timesheet-app) — Node.js timesheet application
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction) — Spring Boot backend

---

## Challenge

Take an application built for traditional deployment and refactor it for cloud-native operation. This covers health check endpoints, externalized configuration, structured logging, Docker containerization, and Kubernetes manifests with proper resource limits and probes. The goal is a production-ready container that follows twelve-factor app principles.

## What Participants Will Learn

- How Devin applies twelve-factor app principles to an existing codebase
- How health check endpoints, externalized config, and structured logging make apps cloud-ready
- How to create production-quality Dockerfiles with multi-stage builds and minimal attack surface
- How Kubernetes manifests define resource limits, readiness/liveness probes, and deployment configuration
- The difference between "containerized" (has a Dockerfile) and "cloud-native" (designed for dynamic environments)

## Devin Features Exercised

- Code refactoring for cloud-native patterns
- Infrastructure file generation (Dockerfile, Kubernetes manifests)
- Configuration externalization
- Logging framework integration
- PR creation with structured descriptions
- AskDevin for architecture decisions

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

## Going Further

- **Child sessions for parallel refactoring**: When making a portfolio of applications cloud-native, spawn one child session per application. Each child follows the same cloud-native refactoring playbook (health check, config externalization, structured logging, Dockerfile, K8s manifest).
- **Playbook-driven cloud readiness**: Encode the cloud-native checklist (12-factor compliance, health checks, structured logging, Dockerfile, K8s manifest) as a playbook. Apply it to every application in the portfolio.
- **Scheduled compliance audits**: Configure a weekly scheduled session that checks all applications against the cloud-native checklist and reports compliance gaps.
- **Event-driven hardening**: Connect a webhook so that when a new service is created, Devin automatically adds cloud-native infrastructure files.
- **Team-based review**: Cloud-native refactoring PRs touch infrastructure files (Dockerfile, K8s manifests) that often require platform team review. Multiple reviewers can provide feedback simultaneously.

## Notes

- The Node.js app (timesheet-app) and the Spring Boot app use different cloud-native patterns — compare the approaches
- For Spring Boot, Actuator provides health/info endpoints out of the box — the exercise is about enabling and configuring them
- For Node.js, health endpoints and structured logging must be added manually
- Both apps should have externalized configuration (no hard-coded database URLs, ports, or secrets)
- The Kubernetes manifests are deployment-only (no Helm charts or Kustomize) — they demonstrate the basic K8s resource model

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js timesheet application. Traditional deployment model — needs cloud-native refactoring for health checks, externalized configuration, structured logging, containerization, and Kubernetes readiness.

### Step 1: Paste into Devin — Cloud-Native Refactor (Node.js)

```
Make timesheet-app more cloud-native: add a /health
endpoint, externalize all configuration to environment
variables, add structured JSON logging with Winston,
create a Dockerfile with multi-stage build, and add a
Kubernetes deployment manifest with resource limits and
readiness probes.
```

### Step 2: Research with Ask Devin

- *"What configuration is currently hard-coded in timesheet-app? What should be externalized to environment variables?"*
- *"What logging does timesheet-app currently use? What's the best structured logging approach for Node.js?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the application structure, current configuration patterns, and deployment assumptions. Plan the refactoring order.

### Step 4 (Optional): Review & Give Feedback

- **Review the Dockerfile** — is it multi-stage? Does it use a minimal base image? Are there unnecessary files in the image?
- **Review the K8s manifest** — are resource limits reasonable? Do the probes point to the correct health endpoint?
- **Leave a comment** asking Devin to add a liveness probe that is different from the readiness probe
- **Leave a comment** asking Devin to add a ConfigMap for non-secret configuration

### Key Takeaways

- **Twelve-factor compliance**: Devin applies cloud-native principles systematically — externalized config, health endpoints, structured logging, minimal containers
- **Production-ready artifacts**: The output includes a Dockerfile and K8s manifest that are ready for real deployment, not just toy examples

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot backend. Uses Spring Boot Actuator for health endpoints but needs additional cloud-native hardening.

### Step 1: Paste into Devin — Cloud-Native Refactor (Spring Boot)

```
Make uc-spring-boot-upgrade-microservice-extraction more
cloud-native: enable Spring Boot Actuator with /health and
/info endpoints, externalize database configuration to
environment variables, add graceful shutdown configuration,
create a Dockerfile with multi-stage build, and add a
Kubernetes deployment manifest with liveness and readiness
probes pointing to Actuator.
```

### Step 2: Research with Ask Devin

- *"What cloud-native features does uc-spring-boot-upgrade-microservice-extraction already have? What's missing?"*
- *"What's the best way to externalize the database configuration in a Spring Boot app — application.yml, environment variables, or Spring Cloud Config?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the current configuration, dependency injection, and deployment setup. Identify which cloud-native patterns are already in place and which need to be added.

### Step 4 (Optional): Review & Give Feedback

- **Review the Actuator configuration** — are the right endpoints exposed? Is sensitive information protected?
- **Compare with timesheet-app** — how do the Node.js and Spring Boot cloud-native approaches differ?
- **Leave a comment** asking Devin to add graceful shutdown handling for in-flight requests

### Key Takeaways

- **Framework-aware refactoring**: Devin leverages Spring Boot Actuator for health and info endpoints rather than building them from scratch
- **Multi-stack cloud readiness**: The same cloud-native principles (health checks, externalized config, structured logging, minimal containers) apply to both Node.js and Spring Boot — but the implementation details differ
