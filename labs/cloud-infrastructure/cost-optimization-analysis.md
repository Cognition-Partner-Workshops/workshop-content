# Cost Optimization Analysis

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
  - [calcom-infra](#calcom-infra)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

Pick a repo below, copy the **Step 1** prompt into a new Devin session, and let Devin analyze the infrastructure for cost savings. No prerequisites beyond repo access.

---

## Challenge

Analyze cloud infrastructure configurations and recommend cost optimizations. Review resource sizing, identify unused resources, and suggest right-sizing or architectural changes to reduce costs.

## Target Outcomes

- Cost analysis report with current resource inventory
- Right-sizing recommendations with projected savings
- Identified unused or underutilized resources
- PR with optimized configurations and cost analysis document

## What Participants Will Learn

- How Devin analyzes infrastructure-as-code to identify cost optimization opportunities
- How Devin estimates cost impacts of resource sizing changes
- Devin's ability to recommend architectural alternatives that reduce cloud spend
- How to evaluate cost optimization recommendations for risk and feasibility

## Devin Features Exercised

- Infrastructure analysis
- Cost modeling
- Terraform/IaC understanding
- Technical writing

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## Repositories

### <a id="timesheet-infra"></a>timesheet-infra

**Repository:** [timesheet-infra](https://github.com/Cognition-Partner-Workshops/timesheet-infra)

AWS infrastructure (EC2, ECR, VPC) for a Node.js application — review resource sizing and identify optimization opportunities.

#### Step 1: Paste into Devin

```
Analyze the AWS infrastructure defined in timesheet-infra's Terraform configurations. Create a cost optimization report that includes: a current resource inventory with estimated monthly costs, right-sizing recommendations for EC2 instances based on the application's workload profile, identification of any over-provisioned or unused resources, and specific Terraform changes to implement the optimizations. Consider alternatives like Fargate vs EC2, reserved instances vs on-demand, and S3 lifecycle policies.
```

#### Step 2: Research with Ask Devin

- *"What EC2 instance types and sizes are used in timesheet-infra? Are they appropriate for a Node.js Express application?"*
- *"Are there any resources defined in Terraform that might be over-provisioned for a timesheet application's typical workload?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the infrastructure architecture. Pay attention to resource sizing, storage configurations, and any resources that seem over-provisioned for the application's needs.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the cost estimates realistic? Check that the optimization recommendations don't compromise application reliability
- **Leave a comment** asking Devin to add a comparison table showing on-demand vs reserved instance pricing for the recommended instance types
- **Watch Devin respond** and push a follow-up commit

---

### <a id="calcom-infra"></a>calcom-infra

**Repository:** [calcom-infra](https://github.com/Cognition-Partner-Workshops/calcom-infra)

Infrastructure configuration for deploying calcom — a more complex cloud setup with database, caching, and application tiers to analyze for cost efficiency.

#### Step 1: Paste into Devin

```
Analyze the infrastructure configurations in calcom-infra and create a comprehensive cost optimization report. Review: compute resource sizing for the application tier, database instance sizing and storage allocation, caching layer configuration, network transfer costs, and any unused or redundant resources. Provide specific recommendations with estimated monthly savings for each optimization. Consider managed vs self-hosted tradeoffs, auto-scaling configurations, and storage tier optimization.
```

#### Step 2: Research with Ask Devin

- *"What cloud resources are provisioned in calcom-infra? What are the most expensive components based on the resource types and sizes?"*
- *"Are there any auto-scaling configurations? Could the infrastructure benefit from scaling to zero during off-peak hours?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the infrastructure topology and resource interdependencies. Identify which tiers (compute, database, cache) represent the largest cost and where right-sizing would have the most impact.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the savings estimates account for the application's actual usage patterns? Check that database right-sizing recommendations include performance impact analysis
- **Leave a comment** asking Devin to add a cost projection showing monthly savings over 12 months with recommended changes
- **Watch Devin respond** and push a follow-up commit

---

## Key Takeaways

- Devin can read Terraform, CloudFormation, and CDK to build a resource inventory and estimate costs without needing cloud console access
- Cost optimization is a recurring concern — Devin can surface savings opportunities that teams deprioritize during feature sprints
- Right-sizing recommendations should always be paired with risk analysis; Devin balances cost reduction against reliability constraints

## Going Further

Cost optimization is well suited to **scheduled Devin sessions** (see [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md)):

- **Weekly IaC drift detection** — Schedule a Devin session to run `terraform plan` against live infrastructure and flag any drift from the checked-in configuration. Drift often introduces cost surprises (manually scaled instances left at higher tiers, forgotten resources)
- **Monthly cost audit** — A recurring session analyzes resource utilization trends and opens a PR with updated right-sizing recommendations
- **Tag compliance checks** — Scheduled sessions verify that all resources carry the required cost-allocation tags, opening PRs to add missing tags so FinOps dashboards stay accurate
