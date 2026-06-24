# Lab: Monorepo CI Pipeline Audit

## Objective

Audit the OtterWorks CI/CD pipeline, understand the monorepo change-detection strategy, identify gaps in the current workflow configuration, and propose or implement improvements.

## What's Wrong

OtterWorks uses four GitHub Actions workflows to build, test, scan, and deploy all its services and frontends from a single monorepo. The CI strategy is documented in `docs/CI_STRATEGY.md`, but the documentation may have drifted from the actual workflow files. Additionally, some services have weaker CI steps than others, and the change-detection filters may miss edge cases (e.g., shared dependency changes that should trigger downstream service builds).

Specific areas to investigate:

- **Change detection gaps:** The `dorny/paths-filter` configuration in `ci.yml` only watches each service's own directory. Changes to `shared/` (OpenAPI specs, event schemas, proto definitions) or `infrastructure/` do not trigger downstream service rebuilds — even though services depend on these shared artifacts.
- **Inconsistent CI rigor:** Some services run lint + test + build, others only run a subset. The CI strategy doc has a status table, but does it match reality?
- **Missing integration tests:** The `test-api-flows` target runs black-box API tests, but these are not wired into the CI pipeline.
- **Docker build pipeline:** `docker-build.yml` builds and pushes to ECR on every push to `main`, but there is no image scanning step between build and push.
- **Security scan timing:** `security-scan.yml` runs on PRs but is separate from the main CI pipeline — findings do not block merge.

## Where to Look

- `docs/CI_STRATEGY.md` — **Read this first.** Documents the intended CI architecture and per-service status.
- `.github/workflows/ci.yml` — Main CI pipeline with `dorny/paths-filter` change detection.
- `.github/workflows/docker-build.yml` — Docker image build and ECR push pipeline.
- `.github/workflows/security-scan.yml` — Security scanning workflow (Trivy, npm audit, pip-audit, bundle-audit).
- `.github/workflows/sast-auto-remediate.yml` — Event-driven SAST remediation (Trivy + SonarCloud → Devin API).
- `Makefile` — Build, test, lint, and coverage targets for all services.
- `sonar-project.properties` — SonarCloud configuration for polyglot analysis.
- `shared/` — Shared OpenAPI specs, event schemas, and proto definitions that services depend on.

## What Done Looks Like

- [ ] Compared `docs/CI_STRATEGY.md` against the actual workflow files — documented any drift
- [ ] Identified at least 2 change-detection gaps where `shared/` or cross-service changes should trigger downstream builds
- [ ] Proposed or implemented a fix for the change-detection gaps (e.g., add `shared/**` to multiple service filters)
- [ ] Verified that all services have consistent CI steps (lint, test, build) — or documented which services are missing steps and why
- [ ] Optionally: added the `test-api-flows` target to the CI pipeline as a post-build integration test stage
- [ ] Updated `docs/CI_STRATEGY.md` if any drift was found

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Read `docs/CI_STRATEGY.md` to understand the intended architecture. Then open `.github/workflows/ci.yml` and compare the `paths-filter` configuration against the strategy doc. Are all services listed? Do the filter paths match what the doc says?

### Hint 2 — Specific Direction

Look at the `dorny/paths-filter` configuration in `ci.yml`. Notice that changes to `shared/openapi/search-service.yaml` will NOT trigger a rebuild of the search-service. Ask Devin to identify all services that depend on files in `shared/` and propose updated filter rules.

### Hint 3 — Approach

Ask Devin: "Read `docs/CI_STRATEGY.md` and `.github/workflows/ci.yml`. Compare them and identify any drift between the documentation and the actual pipeline configuration. Then identify which services depend on files in `shared/` and propose updated `paths-filter` rules that would trigger those services when shared artifacts change."

## Time Budget

- ~15 minutes: Read CI strategy doc and compare with workflow files
- ~15 minutes: Identify change-detection gaps and cross-service dependencies
- ~20-30 minutes: Propose or implement fixes for the most impactful gaps
