# IaC Translation

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
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

Copy the **Step 1** prompt below into a new Devin session to translate Terraform to AWS CDK. No prerequisites beyond repo access.

---

## Challenge

Convert infrastructure as code from one ecosystem to another. This exercises Devin's understanding of cloud resource semantics across different IaC paradigms — declarative (Terraform, CloudFormation) vs. imperative (CDK, Pulumi).

## Target Outcomes

- IaC translated to the target ecosystem with all resources preserved
- Translated code validates/synthesizes successfully
- Resource definitions, outputs, and variable handling preserved
- PR with translated IaC

## What Participants Will Learn

- How Devin translates between IaC paradigms (declarative vs. imperative)
- Devin's understanding of cloud resource semantics across tool ecosystems
- How to validate IaC translations (synth, plan, diff)

## Devin Features Exercised

- IaC comprehension and translation
- Tool installation (CDK, Pulumi CLI)
- Build verification
- PR creation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## Repositories

### <a id="timesheet-infra"></a>timesheet-infra

**Repository:** [timesheet-infra](https://github.com/Cognition-Partner-Workshops/timesheet-infra)

Terraform infrastructure code for hosting the timesheet application. Translate to AWS CDK, CloudFormation, or Pulumi.

#### Step 1: Paste into Devin

```
Convert the Terraform configuration in timesheet-infra to AWS CDK using TypeScript. Preserve all resource definitions, outputs, and variable handling. Create a new cdk/ directory with the translated code. Verify it synthesizes correctly with `cdk synth`.
```

#### Step 2: Research with Ask Devin

- *"What Terraform resources are defined in timesheet-infra? What AWS services do they provision?"*
- *"What's the best CDK pattern for translating Terraform modules — L1 constructs (raw CloudFormation) or L2 constructs (higher-level abstractions)?"*
- Try a different translation target (Pulumi, CloudFormation) and compare the output

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the infrastructure topology. Map out the Terraform resources and their relationships to ensure nothing is lost in translation.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the CDK code produce equivalent CloudFormation to the Terraform plan?
- **Leave a comment** asking Devin to add unit tests for the CDK constructs using the CDK assertions library
- **Watch Devin respond** and push a follow-up commit

---

## Key Takeaways

- IaC translation preserves resource semantics while changing the authoring paradigm — Devin handles the boilerplate mapping so participants can focus on evaluating the output
- Validation is straightforward: `cdk synth` or `terraform plan` confirms functional equivalence without deploying anything
- Trying multiple translation targets (CDK, Pulumi, CloudFormation) on the same source highlights how Devin adapts to different tool conventions

## Going Further

IaC translation pairs well with **scheduled drift detection sessions** (see [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md)):

- **Scheduled IaC drift detection** — After translating to a new IaC tool, run a recurring Devin session that compares `cdk synth` output against the live CloudFormation stack. If drift is detected, Devin opens a PR with the corrective changes
- **Multi-tool parity checks** — If your organization maintains IaC in both Terraform and CDK, a scheduled session can diff the synthesized outputs and flag any divergence
- **IaC linting and best-practice enforcement** — Periodic sessions run `cdk-nag`, `tflint`, or `checkov` against the translated code and open PRs to remediate any findings
