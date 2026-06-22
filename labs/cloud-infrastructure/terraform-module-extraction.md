# Terraform Module Extraction

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Repositories](#repositories)
  - [timesheet-infra](#timesheet-infra)
  - [ordermanager-iac](#ordermanager-iac)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

Pick a repo below, copy the **Step 1** prompt into a new Devin session, and let Devin refactor monolithic Terraform into reusable modules. No prerequisites beyond repo access.

---

## Challenge

Refactor monolithic Terraform configurations into reusable, composable modules. Extract common patterns (networking, compute, storage) into versioned modules with clear interfaces.

## Target Outcomes

- Extracted Terraform modules with clear input/output interfaces
- Module documentation with usage examples
- Refactored root module using the extracted child modules
- PR with modularized Terraform and migration guide

## What Participants Will Learn

- How Devin identifies repeated patterns and extraction opportunities in Terraform code
- How Devin designs module interfaces with appropriate input variables and outputs
- Devin's ability to refactor infrastructure code while preserving behavior
- How to evaluate Terraform modules for reusability and composability

## Devin Features Exercised

- Infrastructure-as-Code understanding
- Refactoring
- Module design
- PR creation

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## Repositories

### <a id="timesheet-infra"></a>timesheet-infra

**Repository:** [timesheet-infra](https://github.com/Cognition-Partner-Workshops/timesheet-infra)

AWS infrastructure for hosting a Node.js application — Terraform configurations covering EC2, ECR, networking, and IAM.

#### Step 1: Paste into Devin

```
Analyze the Terraform configurations in timesheet-infra and refactor them into reusable modules. Extract at least 3 modules: networking (VPC, subnets, security groups), compute (EC2 instance, IAM roles), and container registry (ECR repository, lifecycle policies). Each module should have clear input variables, outputs, and a README with usage examples. Refactor the root module to use the extracted child modules. Ensure `terraform plan` shows no changes after refactoring.
```

#### Step 2: Research with Ask Devin

- *"What Terraform resources exist in timesheet-infra? Which ones are tightly coupled and which can be cleanly extracted into modules?"*
- *"Are there any Terraform state considerations when extracting modules? How do we ensure the refactoring doesn't trigger resource recreation?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the infrastructure architecture and resource dependencies. Identify which resources form natural module boundaries (networking, compute, storage).

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the module interfaces follow Terraform best practices? Check that variables have descriptions, types, and sensible defaults
- **Leave a comment** asking Devin to add validation blocks for critical input variables (e.g., CIDR ranges, instance types)
- **Watch Devin respond** and push a follow-up commit

---

### <a id="ordermanager-iac"></a>ordermanager-iac

**Repository:** [ordermanager-iac](https://github.com/Cognition-Partner-Workshops/ordermanager-iac)

Infrastructure-as-Code for a .NET/Angular monolith deployment — Terraform configurations for cloud infrastructure provisioning.

#### Step 1: Paste into Devin

```
Analyze the Terraform configurations in ordermanager-iac and extract reusable modules. Identify common infrastructure patterns and create modules for: networking (VPC/VNET, subnets, NSGs), application hosting (App Service/compute, scaling rules), and data storage (database, blob storage). Design each module with clear variable interfaces and output values. Refactor the root configuration to compose these modules.
```

#### Step 2: Research with Ask Devin

- *"What cloud provider and Terraform resources are used in ordermanager-iac? Is this AWS, Azure, or multi-cloud?"*
- *"What patterns are repeated across the Terraform files that would benefit from module extraction?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the infrastructure topology and resource relationships. Map out which resources depend on each other to determine the best module boundaries.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the module boundaries clean? Check that there are no circular dependencies between modules
- **Leave a comment** asking Devin to add output values that enable cross-module references (e.g., networking module outputs subnet IDs consumed by compute module)
- **Watch Devin respond** and push a follow-up commit

---

## Key Takeaways

- Module extraction is a refactoring task — Devin can verify behavioral equivalence by confirming `terraform plan` shows no changes after restructuring
- Well-designed module interfaces (typed variables with descriptions and defaults) make infrastructure composable and reusable across projects
- Devin identifies natural module boundaries by analyzing resource dependency graphs, which often aligns with how teams think about infrastructure layers (networking, compute, storage)

## Going Further

Terraform module extraction connects to **IaC drift detection as scheduled sessions** (see [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md)):

- **Scheduled `terraform plan` drift checks** — After extracting modules, run a recurring Devin session that executes `terraform plan` against each environment and opens a PR if drift is detected. This catches manual changes made outside the IaC pipeline
- **Module version bumps** — When shared modules are updated, a scheduled session can identify downstream consumers and open PRs to bump module versions and verify compatibility
- **Policy-as-code enforcement** — Periodic sessions run `tfsec`, `checkov`, or Sentinel against the modularized Terraform and remediate any policy violations
