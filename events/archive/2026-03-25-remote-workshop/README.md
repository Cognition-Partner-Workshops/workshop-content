# Platform-Conformant Microservice Decomposition — 60-Minute Hands-on Lab

## Event Details

| | |
|---|---|
| **Variant Name** | Platform-Conformant Microservice Decomposition (60 min) |
| **Based On** | [workshops/platform-microservice-decomposition](../../../workshops/platform-microservice-decomposition/) |
| **Focus** | Monolith-to-microservices decomposition with IaC conformance on Kubernetes |
| **Duration** | 60 minutes |
| **Audience** | General |
| **Facilitator** | TBD |
| **Event Site** | Remote |

## Table of Contents

- [Abstract](#abstract)
- [Agenda](#agenda)
- [Lab Walkthrough](#lab-walkthrough)
  - [Opening (5 min)](#opening-5-min)
  - [Phase 1 — Launch the Decomposition (5 min)](#phase-1-launch-the-decomposition-5-min)
  - [Phase 2 — Explore While Devin Works (15 min)](#phase-2-explore-while-devin-works-15-min)
  - [Phase 3 — Interact and Refine (25 min)](#phase-3-interact-and-refine-25-min)
  - [Wrap-up (10 min)](#wrap-up-10-min)
- [Devin Features Exercised](#devin-features-exercised)
- [Devin Features Checklist](#devin-features-checklist)
- [Key Takeaways](#key-takeaways)
- [Differences from Full 90-Minute Version](#differences-from-full-90-minute-version)
- [Facilitator Notes](#facilitator-notes)
  - [Before the Lab](#before-the-lab)
  - [During the Lab](#during-the-lab)
  - [Common Issues](#common-issues)
- [Related Resources](#related-resources)

## Abstract

> Participants use Devin to decompose the Inventory bounded context from a .NET 8 + Angular 17 monolith into a standalone, platform-conformant microservice — complete with Helm chart, ArgoCD manifests, Dockerfile, CI/CD pipeline, and HTTP-based service communication.
>
> Devin reads four repositories simultaneously: the monolith source, existing IaC patterns, the platform engineering standard, and a landing repo for the new service. From a single prompt, Devin extracts the module, generates production-grade infrastructure-as-code, refactors the monolith, and creates PRs in both repos.
>
> **60-minute format:** This is a compressed version of the [full 90-minute lab](../../../workshops/platform-microservice-decomposition/). Bonus challenges and extended exploration are omitted. Participants launch the decomposition, explore with AskDevin/DeepWiki while Devin works, then review PRs and leave feedback.

---

## Agenda

| Time | Phase | Activity |
|------|-------|----------|
| 0:00 | **Opening** | Welcome, Devin overview, explain the 4-repo architecture |
| 0:05 | **Phase 1 — Launch** | Participants submit the decomposition prompt |
| 0:10 | **Phase 2 — Explore** | Use AskDevin and DeepWiki while Devin works |
| 0:25 | **Phase 3 — Interact** | Steer Devin in-session, refine prompts, explore Devin features |
| 0:50 | **Wrap-up** | Discussion, compare results, share next steps |

---

## Lab Walkthrough

### Opening (5 min)

1. Welcome and introductions
2. Brief Devin overview — what it is, what it can do
3. Explain the 4-repo architecture using the table below:

| Repo | Role | Modified? |
|------|------|-----------|
| `ordermanager-monolith` | Source — extract the Inventory module from here | Yes — refactored to use HTTP client |
| `ordermanager-microservices` | Landing — new service code + service-level IaC goes here | Yes — new inventory-service directory |
| `platform-engineering-shared-services` | Context — defines what the platform expects | No — read-only reference |
| `ordermanager-iac` | Context — provides IaC patterns to follow | No — read-only reference |

4. Set expectations: Devin will take 10-15 minutes to produce initial PRs — that's normal for a complex multi-repo task

### Phase 1 — Launch the Decomposition (5 min)

Create a new Devin session and paste the following prompt. Replace `<participant>` with your name:

```
Decompose the Inventory module from @Cognition-Partner-Workshops/ordermanager-monolith into a standalone microservice.

Work on branch workshop-mason in both repos.

Use these repos as context for platform patterns and IaC standards:
- @Cognition-Partner-Workshops/platform-engineering-shared-services — defines the platform standard (namespaces, network policies, monitoring, ArgoCD)
- @Cognition-Partner-Workshops/ordermanager-iac — contains the existing Helm chart, Dockerfile, and ArgoCD patterns to follow

Deliverables:
- New .NET 8 Web API for the inventory-service with its own models, controllers, services, and EF Core DbContext
- Angular 17 frontend components for inventory management
- Dockerfile — multi-stage build following the pattern in ordermanager-iac/docker/Dockerfile
- Helm chart — deployment, service, network policy, service monitor, HPA (follow ordermanager-iac/helm/)
- ArgoCD application manifests for dev and staging (follow ordermanager-iac/argocd/)
- GitHub Actions CI/CD pipeline — build, test, push to ECR, trigger ArgoCD sync
- Monolith refactoring — replace in-process Inventory calls with an HTTP client that calls the new service

Push the new inventory-service code and all service-level IaC to @Cognition-Partner-Workshops/ordermanager-microservices on branch workshop-mason. Push the monolith refactoring changes to ordermanager-monolith on branch workshop-mason. Build and test both services locally to verify they work together.
```

### Phase 2 — Explore While Devin Works (15 min)

While Devin works autonomously, use these features to deepen your understanding:

#### AskDevin

Open a new AskDevin conversation and try:

- *"Analyze the domain boundaries in ordermanager-monolith. Which modules have clean boundaries and which are tightly coupled?"*
- *"Look at the Helm chart in ordermanager-iac and the namespace provisioning in platform-engineering-shared-services. What Kubernetes resources does a new microservice need to conform to the platform standard?"*

AskDevin uses advanced code search to produce detailed, accurately cited answers grounded in your codebase. Notice how it references specific files and line numbers in its responses.

> **Tip:** AskDevin is ideal for exploring and planning *before* starting a session. You can also **start a Devin session directly from an AskDevin conversation** — the prompt is automatically tailored with context from your discussion, leading to better results.

#### DeepWiki

Open each repo's DeepWiki page. Focus on:

1. **ordermanager-monolith** — module structure, shared models, dependency graph between Orders/Products/Customers/Inventory
2. **platform-engineering-shared-services** — namespace provisioning pattern, network policy defaults, monitoring setup

#### View Devin's Machine

While Devin is working, open the right side panel in the session by selecting the icon in the top right of the screen to see a live view of Devin's machine. This lets you:

- See the terminal output and file system in real time
- Observe how Devin navigates between repos
- Optionally run commands yourself in the IDE (e.g., `ls` the project structure, check a file Devin created)

#### Monitor Devin's Progress

Watch the session. Key milestones to look for:
- Devin reads all four repos and identifies the Inventory bounded context
- Devin creates the new .NET 8 Web API project structure
- Devin generates the Helm chart, Dockerfile, and ArgoCD manifests
- Devin refactors the monolith to use an HTTP client
- Devin runs tests and creates PRs

### Phase 3 — Interact and Refine (25 min)

While Devin works (or once it finishes), try these prompting exercises. Pick at least two:

#### Exercise A: Steer Devin Mid-Session

Provide one of these points of feedback directly in the Devin session chat to steer its behavior while it's still working:

**Ideally we have given Devin a prompt detailed enough for it to handle everything, but if it misses something, this is a good opportunity to show how to correct it:**

- *"Also add a PodDisruptionBudget to the Helm chart with minAvailable: 1"*
- *"Use Polly for circuit breaker and retry logic in the InventoryServiceClient"*
- *"Make sure the ArgoCD manifests target the decomposition-dev and decomposition-staging namespaces"*

Notice how Devin incorporates your feedback into its current work without starting over.

#### Exercise B: Generate a Refined Prompt with AskDevin

Now that you've seen what Devin produces, use AskDevin to craft a **better** version of the original prompt:

- *"Based on what you know about ordermanager-monolith and platform-engineering-shared-services, write me an optimized Devin session prompt to extract the Customers module as a microservice. Include specific file paths and patterns to follow."*

Compare the generated prompt to the one you used in Phase 1 — is it more specific? Would it produce better results?

Using Ask Devin is a great way to iteratively build up the relevant context and constraints for a task based on the underlying implementation details. By doing this, you can create more targeted and effective prompts for future tasks.

#### Exercise C: Create a Knowledge Item

Create a Knowledge entry in Devin that captures the platform conformance rules:

1. Go to **Sessions**
2. Send this prompt to Devin:

> Create a new Knowledge item titled "Platform Conformance Standard"
> Include rules like: *"All new microservices must include a Helm chart with network policy, service monitor, and HPA. ArgoCD manifests must target decomposition-dev and decomposition-staging namespaces. Dockerfiles must use multi-stage builds following the pattern in ordermanager-iac."*

3. Navigate to **Setting > Knowledge** and see the new knowledge items once Devin completes the task.

This simulates how teams encode organizational standards so that every Devin session follows them automatically.

Navigate to "Sessions" > "Explore Advanced Capabilities" > "Manage Knowledge". Here we can use Devin to help us manage and curate our Knowledge Items at scale. 

#### Exercise D: Review Session Insights

Open **Session Insights** for your running session:

- Review the timeline of activities — what did Devin do first? How did it plan its approach?
- Check the session size (ACU consumption)
- Hypothesize: how could you have gotten the same result with a shorter/cheaper session? (Hint: more specific file paths, fewer deliverables, or providing the Knowledge item from Exercise C upfront)

#### Exercise E: Launch a Parallel Session

Start a **second Devin session** with a different task against the same repos:

- *"Analyze the domain boundaries in ordermanager-monolith and produce a decomposition roadmap. For each module (Orders, Products, Customers, Inventory), assess coupling, shared models, and extraction difficulty. Output a markdown report with a recommended extraction order."*

This demonstrates running parallel sessions — one doing code work, one doing analysis.

#### Exercise F: Create a Playbook

After observing the decomposition workflow, create a Playbook that codifies it as a repeatable process:

You may also do this directly in the session by asking Devin to "Create a Playbook" using the session interface.

1. Go to **Settings > Playbooks**
2. Create a new playbook titled "Microservice Extraction Playbook"
3. Define the steps Devin should follow for any bounded context extraction:
   - Read the platform standard repos for IaC patterns
   - Identify the target module's boundaries and shared dependencies
   - Extract the module into a new .NET 8 Web API
   - Generate Helm chart, Dockerfile, ArgoCD manifests conformant to the platform standard
   - Refactor the monolith to use an HTTP client
   - Run tests and create PRs

Playbooks turn one-off prompts into reusable organizational workflows. Next time someone needs to extract a different module, they can invoke this playbook instead of writing a detailed prompt from scratch.

### Wrap-up (10 min)

1. Ask participants to share their most interesting finding from the PR review
2. Compare results across participants — did Devin make the same architectural decisions?
3. Highlight the key takeaway: code extraction + IaC generation + platform conformance in a single session
4. Point to the [full 90-minute version](../../../workshops/platform-microservice-decomposition/) and take-home extensions for continued learning
5. Collect feedback

---

## Devin Features Exercised

| Feature | Where Used |
|---------|-----------|
| Multi-repo context awareness | Phase 1 — Devin reads 4 repos simultaneously |
| Architectural analysis | Phase 1 — domain boundary identification |
| Code extraction and refactoring | Phase 1 — .NET and Angular code |
| IaC generation | Phase 1 — Helm, Dockerfile, ArgoCD, GitHub Actions |
| AskDevin | Phase 2 — architectural planning |
| DeepWiki | Phase 2 — codebase exploration |
| AskDevin → Session handoff | Phase 2 — plan in AskDevin, then launch a session with rich context |
| Take control of machine | Phase 2 — observe Devin's environment live |
| In-session feedback | Phase 3 — steer Devin mid-session via chat |
| Knowledge items | Phase 3 — encode platform standards for reuse |
| Session Insights | Phase 3 — analyze ACU consumption and session timeline |
| Parallel sessions | Phase 3 — run analysis alongside code extraction |
| Playbooks | Phase 3 — codify the decomposition workflow for reuse |

## Devin Features Checklist

- [ ] Submit a multi-repo decomposition prompt (Phase 1)
- [ ] Use AskDevin to analyze architecture (Phase 2)
- [ ] Start a Devin session directly from an AskDevin conversation (Phase 2)
- [ ] Explore repos with DeepWiki (Phase 2)
- [ ] Take control of Devin's machine to observe live (Phase 2)
- [ ] Steer Devin mid-session with in-chat feedback (Phase 3 — Exercise A)
- [ ] Use AskDevin to generate a refined prompt (Phase 3 — Exercise B)
- [ ] Create a Knowledge item for platform standards (Phase 3 — Exercise C)
- [ ] Review Session Insights and ACU consumption (Phase 3 — Exercise D)
- [ ] Run a parallel Devin session (Phase 3 — Exercise E)
- [ ] Create a Playbook for repeatable decomposition (Phase 3 — Exercise F)

---

## Key Takeaways

- **"Code extraction + IaC generation in one session"** — Devin handles both the code and the infrastructure
- **"Platform conformance by reading existing patterns"** — Devin reads the platform standard repos and generates conformant artifacts
- **"Multi-repo coordination"** — Devin works across 4 repos simultaneously

## Differences from Full 90-Minute Version

| Aspect | 60-min version | 90-min version |
|--------|---------------|----------------|
| Bonus challenges | Omitted | Extract another service, add tracing, Grafana dashboards |
| Explore phase | 15 min (2 AskDevin prompts, 2 DeepWiki repos) | 25 min (3 AskDevin prompts, 3 DeepWiki repos) |
| Interact phase | 25 min (in-session steering, Knowledge, prompting exercises) | 25 min (GitHub PR review + feedback comments) |
| Wrap-up | 10 min | 15 min |

For the full version with bonus challenges, see [platform-microservice-decomposition](../../../workshops/platform-microservice-decomposition/).

---

## Facilitator Notes

### Before the Lab

1. Verify all 4 repos are on the Devin machine snapshot (Settings > Machine configuration)
2. Confirm participants have Devin accounts and GitHub access
3. Pre-assign participant names (for `workshop-<participant>` branches)

### During the Lab

- **Opening is critical** — use the 4-repo architecture table to orient participants. Without this context, the prompt is overwhelming.
- **Phase 1 is hands-off** — once participants submit the prompt, use the wait time to walk through the repos on screen or answer questions.
- **Phase 2 moves fast** — suggest participants pick either AskDevin or DeepWiki rather than trying both. Monitor the room to see when Devin starts producing PRs.
- **Phase 3 is the most valuable** — this is where learning happens. Walk around and help participants craft good PR comments. Encourage everyone to leave at least one comment.
- **Time management** — if Devin takes longer than expected to produce PRs, shift the balance: extend Phase 2 and shorten Phase 3. The PR review is more impactful but can be done with even 15 minutes.

### Common Issues

- **Devin creates a separate repo instead of pushing to the landing repo:** The prompt explicitly says to push to `ordermanager-microservices`. If Devin tries to create a new repo, have the participant leave a comment redirecting it.
- **Branch conflicts between participants:** Each participant uses their own `workshop-<participant>` branch, so conflicts should not occur.
- **IaC doesn't match platform standard:** Good teaching moment — have the participant leave a PR comment asking Devin to fix it.

---

## Related Resources

| Resource | Link |
|----------|------|
| Full 90-min event | [platform-microservice-decomposition](../../../workshops/platform-microservice-decomposition/) |
| Core module | [Platform-Conformant Microservice Decomposition](../../../modules/cloud-infrastructure/platform-conformant-microservice-decomposition.md) |
| Workshop template | [workshops/platform-microservice-decomposition](../../../workshops/platform-microservice-decomposition/) |
| Facilitator guide | [shared/facilitator-guide.md](../../../shared/facilitator-guide.md) |
| Devin Features Appendix | [modules/devin-features](../../../modules/devin-features/README.md) |
