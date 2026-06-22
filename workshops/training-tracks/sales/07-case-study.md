# Case Study: Full Engagement Estimation

A comprehensive exercise applying all frameworks from this workshop to scope and estimate a complete engagement.

---

## Scenario

A large financial services firm wants to modernize 30 legacy Java services currently running on Spring Boot 2.3 with a monolithic deployment model. The desired end state:

- All services upgraded to Spring Boot 3.2
- Each service containerized with Docker and deployable to Kubernetes
- CI/CD pipelines created for each service (build, test, scan, deploy)
- Test coverage increased from current ~35% average to 70% minimum
- Three core services (payments, auth, risk-scoring) need architectural redesign to support the new microservice topology
- API documentation generated for all public endpoints

The client wants this completed in 4 months with a clear cost breakdown.

---

## What You Should Produce

Using the frameworks from this workshop, create:

1. **Campaign decomposition** — Break the engagement into campaigns, then into units of work
2. **Staffing model** — Which roles are needed and at what allocation
3. **Cost estimate** — Using the unit-of-work formula with complexity tiers
4. **Timeline** — Parallel vs. sequential execution plan
5. **Risk register** — Exception rates by campaign, mitigation strategies

---

## Reference Answer

### 1. Campaign Decomposition

| Campaign | Unit of Work | Count | Agent-Executable? |
|----------|-------------|-------|-------------------|
| Spring Boot Upgrade | One service upgrade | 30 | Yes — parallel |
| Containerization | One service Dockerfile + k8s manifests | 30 | Yes — parallel |
| CI/CD Pipeline | One service pipeline | 30 | Yes — parallel |
| Test Coverage | One service test expansion | 30 | Yes — parallel |
| Architecture Redesign | One service architecture (3 core services) | 3 | No — human judgment |
| Architecture Implementation | One redesigned service implementation | 3 | Yes — after design |
| API Documentation | One service API docs | 30 | Yes — parallel |

### 2. Staffing Model

| Role | Allocation | Responsibility |
|------|-----------|----------------|
| Lead Architect | Full-time | Architecture redesign, orchestration decisions, exception escalations |
| Senior Engineer (2) | Full-time | PR review, exception handling, quality gate |
| DevOps Engineer | Half-time | Environment setup, CI/CD template validation, infrastructure review |
| AI Agent Fleet | As needed | All parallel execution work |

**Total human headcount:** 3.5 FTE (vs. estimated 10-12 FTE for headcount-only approach)

### 3. Cost Estimate

**Spring Boot Upgrade (30 services):**

| Tier | Count | ACUs/Unit | Review Hrs | Exception Rate |
|------|-------|-----------|------------|----------------|
| Simple | 12 | 6 | 1.0 hr | 5% |
| Standard | 13 | 10 | 1.5 hrs | 15% |
| Complex | 5 | 14 | 3.0 hrs | 20% |

**Containerization (30 services):**

| Tier | Count | ACUs/Unit | Review Hrs | Exception Rate |
|------|-------|-----------|------------|----------------|
| Simple | 15 | 4 | 0.5 hr | 5% |
| Standard | 12 | 7 | 1.0 hr | 10% |
| Complex | 3 | 10 | 2.0 hrs | 15% |

**CI/CD Pipeline (30 services):**

| Tier | Count | ACUs/Unit | Review Hrs | Exception Rate |
|------|-------|-----------|------------|----------------|
| Template (most) | 25 | 3 | 0.5 hr | 5% |
| Custom | 5 | 8 | 1.5 hrs | 15% |

**Test Coverage (30 services):**

| Tier | Count | ACUs/Unit | Review Hrs | Exception Rate |
|------|-------|-----------|------------|----------------|
| Simple | 12 | 5 | 0.5 hr | 5% |
| Standard | 13 | 10 | 1.0 hr | 10% |
| Complex | 5 | 15 | 2.0 hrs | 15% |

**Architecture Redesign (3 services):** Human-delivered at architect rate — estimated 80 hrs total (discovery + design + documentation)

**Architecture Implementation (3 services):** 20 ACUs/service + 4 hrs review/service + 30% exception rate

**API Documentation (30 services):** 3 ACUs/service + 0.5 hrs review/service + 5% exception rate

**Orchestration overhead:** Playbook development (40 hrs) + Environment setup (20 hrs) + Campaign coordination (60 hrs) + Exception triage (40 hrs) = 160 hrs at blended rate

### 4. Timeline

| Month | Activities |
|-------|-----------|
| **Month 1** | Architecture redesign (3 services), playbook development, environment setup. Start Spring Boot upgrade campaign (all 30 services in parallel). |
| **Month 2** | Complete Spring Boot upgrades. Start containerization campaign (30 parallel). Start CI/CD pipeline campaign (30 parallel). Architecture implementation begins for first redesigned service. |
| **Month 3** | Complete containerization + CI/CD. Start test coverage campaign (30 parallel). API documentation campaign (30 parallel). Complete architecture implementations. |
| **Month 4** | Complete test coverage expansion. Integration testing. Final review. Buffer for exceptions and rework. |

**Key insight:** Campaigns that would be sequential in a headcount model run in parallel with agents. The architecture redesign (human-judgment work) is the critical path, not the volume work.

### 5. Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|-----------|
| Undocumented business logic in complex services | High | Medium | Higher exception rate (20%) for complex tier; architect review of all complex-tier PRs |
| Integration issues between upgraded services | Medium | High | Integration testing phase in Month 4; staged rollout (upgrade in dependency order) |
| Test coverage target unreachable for legacy code | Medium | Low | Accept 60% minimum for legacy modules; document gaps |
| Architecture redesign takes longer than estimated | Medium | High | Month 4 buffer; redesign is on critical path, so start early |
| CI/CD template doesn't fit all services | Low | Low | Custom tier (5 services) priced at higher ACU and review rate |
