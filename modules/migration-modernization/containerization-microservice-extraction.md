# Containerization & Microservice Extraction

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
- [petclinic-microservices](#petclinic-microservices)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Quick Start

Paste this prompt into Devin to try containerizing a microservices application:

```
Analyze the microservices architecture of
petclinic-microservices. Add Docker health checks to all
services, create a unified Docker Compose file with proper
networking, and add a reverse proxy (nginx or Traefik) in
front of the services. Ensure all services start and
communicate correctly.
```

---

## Repositories

- [petclinic-microservices](#petclinic-microservices) — Spring Cloud microservices
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction) — Monolithic Spring Boot app for domain extraction

---

## Challenge

Use Devin to containerize and improve the operational readiness of a microservices application, then extract a domain from a monolith into a standalone microservice. This exercises Devin's ability to work with Docker, Docker Compose, networking, health checks, and domain decomposition.

## What Participants Will Learn

- How Devin analyzes a microservices architecture and identifies operational gaps
- How Devin creates Dockerfiles, Docker Compose configurations, and networking setups
- How Devin identifies domain boundaries in a monolith and extracts services
- The difference between operational containerization (health checks, networking) and architectural decomposition (domain extraction)
- How reverse proxies unify multiple services behind a single entry point

## Devin Features Exercised

- Multi-file code generation (Dockerfiles, docker-compose.yml, nginx.conf)
- Architecture analysis and domain decomposition
- Build and runtime configuration
- PR creation with structured descriptions
- AskDevin for architecture decisions
- DeepWiki for understanding service dependencies

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

## Going Further

- **Child sessions for parallel extraction**: When decomposing a monolith with multiple domains, spawn one child session per domain extraction. Each child extracts its domain, creates the service, Dockerfile, and docker-compose entry, and opens a PR. The parent session integrates the results.
- **Playbook-driven containerization**: Encode the containerization process (analyze service, create multi-stage Dockerfile, add health checks, wire into Compose, add reverse proxy) as a playbook. Apply it to every new service.
- **Scheduled health check audits**: Configure a weekly scheduled session that audits all Dockerfiles and docker-compose files for missing health checks, outdated base images, or security issues.
- **Event-driven builds**: Connect a webhook so that when code is pushed, Devin automatically builds and tests the Docker image.
- **Team-based review**: Multiple engineers review the extraction PRs. PR comments trigger Devin to iterate on Dockerfile configurations or docker-compose networking.

## Notes

- The petclinic-microservices repo already has a microservices architecture — the exercise focuses on operational improvements (health checks, Compose, reverse proxy)
- The extraction exercise creates a new microservice from an existing monolith — this is a structural change, not just containerization
- For a shorter session, focus on one repo; for a longer session, do both sequentially or in parallel
- The docker-compose configuration should include proper service dependencies (depends_on with health check conditions)

---

## <a id="petclinic-microservices"></a>petclinic-microservices

**Repository:** [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices)

Spring Cloud microservices version of PetClinic. Multiple services (customers, visits, vets) with service discovery and API gateway.

### Step 1: Paste into Devin — Containerization

```
Analyze the microservices architecture of
petclinic-microservices. Add Docker health checks to all
services, create a unified Docker Compose file with proper
networking, and add a reverse proxy (nginx or Traefik) in
front of the services. Ensure all services start and
communicate correctly.
```

### Step 2: Research with Ask Devin

- *"What services does petclinic-microservices have? What ports do they use? What's the service discovery mechanism?"*
- *"What health check endpoints does each service expose? If they don't have one, what's the best way to add one?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the service architecture, communication patterns, and existing Docker configuration. Identify gaps in operational readiness.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Docker Compose setup work correctly? Are health checks configured for all services? Does the reverse proxy route traffic properly?
- **Leave a comment** asking Devin to improve something (e.g., *"The Dockerfile should use a multi-stage build"* or *"Add health check endpoints to all services"*)
- **Watch Devin respond** to your PR comment and push a fix
- Try leaving an inline comment on a specific line of the Dockerfile or docker-compose.yml

### Key Takeaways

- **Operational readiness**: Health checks, proper networking, and reverse proxies are essential for production-ready microservices — Devin adds them systematically
- **Infrastructure-as-code**: Docker Compose configurations codify the entire service topology, making local development reproducible

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot monolith with multiple domains (articles, comments, users). The Comments domain can be extracted into a standalone microservice.

### Step 1: Paste into Devin — Domain Extraction

```
Analyze the domain boundaries in
uc-spring-boot-upgrade-microservice-extraction. Extract the
Comments domain into a standalone Spring Boot microservice
with its own database, Dockerfile, and REST API. The monolith
should communicate with the comments microservice via HTTP.
Create a docker-compose.yml that runs both services.
```

### Step 2: Research with Ask Devin

- *"What are the domain boundaries in uc-spring-boot-upgrade-microservice-extraction? Which domain has the least coupling and would be easiest to extract?"*
- *"What shared entities or services exist between Comments and other domains? What's the minimum interface for the extracted service?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand domain coupling, shared models, and database access patterns. Plan the extraction to minimize changes to the monolith.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the extraction clean? Does the monolith still work without the extracted domain?
- **Leave a comment** asking Devin to add integration tests between the services

### Key Takeaways

- **Domain decomposition**: Devin identifies bounded contexts, extracts the least-coupled domain first, and creates a clean service boundary with HTTP communication
- **Divide-and-conquer**: Each domain extraction is independent work that can be done by a child session — parallelizing a multi-domain decomposition
