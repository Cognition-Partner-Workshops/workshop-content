# Containerization & Microservice Extraction

## Repositories

- [petclinic-microservices](#petclinic-microservices)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Extract a self-contained microservice from a monolith and containerize it. This exercises Devin's ability to analyze domain boundaries, extract shared code, create Dockerfiles, and wire services together with Docker Compose.

## Target Outcomes

- One bounded context extracted as a standalone Spring Boot service
- Dockerfile with multi-stage build for the extracted service
- Docker Compose configuration to run both services
- API communication between monolith and microservice
- PR with extraction changes and architecture documentation

## What Participants Will Learn

- How Devin analyzes domain boundaries and dependency graphs
- How Devin handles the complexity of extracting shared code
- Containerization best practices (multi-stage builds, health checks, env config)
- Service communication patterns (REST, shared contracts)
- How to use PR review comments to iterate on Devin's architectural decisions

## Devin Features Exercised

- Architectural analysis
- Code extraction and refactoring
- Docker/docker-compose authoring
- Multi-module project setup
- PR creation and PR comment responses
- DeepWiki for codebase exploration
- AskDevin for pre-session planning
- Parallel sessions (if extracting multiple services)
- GitHub PR feedback loop

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

## Notes

- If you already completed the framework upgrade (MM2), you can chain this challenge on top of the upgraded code
- There is no single right answer — different extraction strategies and containerization approaches are all valid

---

## <a id="petclinic-microservices"></a>petclinic-microservices

**Repository:** [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices)

Reference architecture showing what a fully decomposed microservices application looks like. Use this as a comparison target or as a starting point for further containerization improvements.

### Step 1: Paste into Devin

> Analyze the microservices architecture of petclinic-microservices. Add Docker health checks to all services, create a unified Docker Compose file with proper networking, and add a reverse proxy (nginx or Traefik) in front of the services. Ensure all services start and communicate correctly. Open a PR.

### Step 2: Research with Ask Devin

- *"What communication patterns do the PetClinic microservices use? Are there any single points of failure?"*
- *"What observability is missing from the Docker Compose setup (health checks, logging, metrics)?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the service decomposition, API contracts, and inter-service communication. Identify which services are tightly coupled.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Docker Compose setup handle service startup order correctly?
- **Leave a comment** asking Devin to add Kubernetes manifests on top of the Docker setup

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 monolith with three clear bounded contexts: Articles (CRUD, feed, favorites, tags), Users/Profiles (registration, authentication, following), and Comments (CRUD linked to articles). The Comments domain is the simplest extraction; Users/Profiles is the hardest because authentication is cross-cutting.

### Step 1: Paste into Devin

> Analyze the domain boundaries in uc-spring-boot-upgrade-microservice-extraction. Extract the Comments domain into a standalone Spring Boot microservice with its own database, Dockerfile, and REST API. The monolith should communicate with the comments microservice via HTTP. Create a docker-compose.yml that runs both services. Open a PR.

### Step 2: Research with Ask Devin

- *"What are the domain boundaries in uc-spring-boot-upgrade-microservice-extraction? Which bounded context would be easiest to extract and which would be hardest?"*
- *"If I extract the Articles domain from this monolith, what shared code and database tables will need to be handled? What communication pattern should I use?"*
- Use the refined analysis as your Devin session prompt — compare the result to your first attempt

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to see the module structure, domain model, and dependency graph. Identify which bounded contexts exist and how tightly coupled they are. Look at the test coverage — which domains have the best tests to serve as a safety net?

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the extraction look clean? Are there leftover dependencies on the monolith?
- **Leave a comment** asking Devin to improve something (e.g., *"The Dockerfile should use a multi-stage build"* or *"Add health check endpoints to both services"*)
- **Watch Devin respond** to your PR comment and push a fix
- Try leaving an inline comment on a specific line of the Dockerfile or docker-compose.yml
