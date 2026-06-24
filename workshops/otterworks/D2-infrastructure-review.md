# Lab: Infrastructure as Code Review

## Objective

Audit the Terraform modules and Helm charts that define OtterWorks' cloud infrastructure and Kubernetes deployments. Identify misconfigurations, missing best practices, and inconsistencies between the IaC definitions and the running system.

## What's Wrong

OtterWorks has two layers of Terraform (platform + application) and 13 Helm charts — one per deployable service (11 backend services + 2 frontends). The infrastructure was built incrementally, and not all modules follow the same conventions. Common issues in real-world IaC include missing resource tagging, overly permissive IAM policies, hardcoded values that should be variables, missing health probes in Helm charts, and inconsistent resource limits.

Areas to investigate:

- **Terraform modules** (`infrastructure/terraform/modules/`): 8 modules covering storage, database, cache, messaging, search, auth, monitoring, and IRSA. Are they consistently structured? Do they follow Terraform best practices (variable validation, output documentation, lifecycle rules)?
- **Platform Terraform** (`platform/terraform/modules/`): 3 modules for VPC, EKS, and ECR. These are the foundation — are they production-ready?
- **Helm charts** (`infrastructure/helm/`): 13 charts, one per deployable service. Do they all have proper health probes, resource limits, network policies, and service monitors?
- **K8s base manifests** (`infrastructure/k8s/`): Namespace, resource quotas, and limit ranges. Are the quotas appropriate for the services deployed?
- **Consistency:** Do the Terraform outputs match what the Helm charts expect as input? Are there hardcoded values that should flow through from Terraform outputs?

## Where to Look

- `infrastructure/terraform/main.tf` — Root module that composes all application infrastructure
- `infrastructure/terraform/modules/` — 8 sub-modules: `auth`, `cache`, `database`, `irsa`, `messaging`, `monitoring`, `search`, `storage`
- `infrastructure/terraform/variables.tf` — Input variables and defaults
- `infrastructure/terraform/terraform.tfvars.example` — Example variable values
- `platform/terraform/` — VPC, EKS cluster, and ECR repository modules
- `infrastructure/helm/<service>/` — Helm chart for each service (13 charts)
- `infrastructure/k8s/namespace.yaml` — Namespace definition
- `infrastructure/k8s/resourcequota.yaml` — Cluster resource quotas
- `infrastructure/k8s/limitrange.yaml` — Default container resource limits
- `ARCHITECTURE.md` — Infrastructure section documents the intended AWS resources

## What Done Looks Like

- [ ] Audited at least 2 Terraform modules for best practices (variable validation, outputs, tagging, lifecycle rules)
- [ ] Audited at least 2 Helm charts for Kubernetes best practices (health probes, resource limits, security context, network policies)
- [ ] Identified at least 3 concrete issues or improvements across the IaC
- [ ] Proposed or implemented fixes for the most impactful findings
- [ ] Verified Terraform validates cleanly: `cd infrastructure/terraform && terraform init && terraform validate`
- [ ] Verified Helm charts lint cleanly: `helm lint infrastructure/helm/<service>`
- [ ] Optionally: checked that the K8s resource quotas and limit ranges are appropriate for the services

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Start with `infrastructure/terraform/modules/database/` — the RDS module is a good example to audit because databases have many security and operational concerns (encryption, backup, multi-AZ, deletion protection). Then check `infrastructure/helm/api-gateway/` as the first Helm chart to review.

### Hint 2 — Specific Direction

Ask Devin to compare the Helm chart values across all 12 services. Are resource limits consistent? Do all charts define `livenessProbe` and `readinessProbe`? Do any charts have `securityContext` with `runAsNonRoot: true`?

### Hint 3 — Approach

Ask Devin: "Audit `infrastructure/terraform/modules/database/` for Terraform best practices: variable validation, output documentation, encryption at rest, backup configuration, deletion protection, and resource tagging. Then audit `infrastructure/helm/api-gateway/` for Kubernetes best practices: health probes, resource limits, security context, and network policies. List all findings with severity and suggested fixes."

## Time Budget

- ~15 minutes: Review Terraform module structure and pick 2 modules to deep-dive
- ~15 minutes: Review Helm chart structure and pick 2 charts to deep-dive
- ~20-30 minutes: Document findings and propose or implement fixes
