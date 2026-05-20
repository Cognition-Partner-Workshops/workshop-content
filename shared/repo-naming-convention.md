# Repository Naming Convention

All repositories in the [Cognition-Partner-Workshops](https://github.com/Cognition-Partner-Workshops) org follow a naming convention designed for high-cardinality discovery.

## Categories

| Category | Naming Pattern | Discovery Model | Examples |
|----------|---------------|-----------------|---------|
| **Multi-repo apps** | `<appname>-<component>` | Identity-first — the app name IS the grouping key. Related repos cluster alphabetically. | `eventflow-order-service`, `petclinic-angular`, `ordermanager-monolith` |
| **Tech stacks** (`ts-`) | `ts-<lang>-<framework-or-project>` | Tech-first — people go here when they care about a specific technology. Include the primary language. | `ts-java-spring-boot-realworld`, `ts-cobol-carddemo`, `ts-python-aws-personalize` |
| **Use cases** (`uc-`) | `uc-<task-description>` | Task-first — people go here when they care about the exercise (tech-agnostic). Name describes the task/tool, NOT the underlying tech stack. | `uc-cve-remediation-regulatory-compliance`, `uc-legacy-modernization-cobol-to-java` |
| **Industry verticals** (`i-`) | `i-<industry>-<identity>` | Domain-first — industry-specific repos. | `i-banking-loan-processing` _(none yet)_ |
| **Well-known forks** | Keep upstream name | Recognition — everyone already knows these names. | `keycloak`, `fineract`, `openmrs-core` |
| **Internal tooling** | Descriptive name | Self-explanatory. | `workshop-metadata`, `operator`, `platform-engineering-shared-services` |

## Rules

1. **Always lowercase, always hyphens.** No underscores, no mixed case: `eventflow-order-service` (not `app_eventflow-order-service` or `EventFlow_OrderService`).
2. **Choose the category that matches the primary reason the repo exists.** Tech stack showcase → `ts-`. Lab exercise → `uc-`. Multi-component app → identity-first.
3. **`ts-` repos must include the primary language** in the name: `ts-java-spring-boot-realworld` (not `ts-spring-boot-realworld`).
4. **`uc-` repos describe the task, not the tech stack.** The tech is irrelevant to the use case name: `uc-cve-remediation-sonarqube` (not `uc-cve-remediation-spring-boot`).
5. **Multi-repo apps need no prefix.** The app name clusters them naturally when sorted alphabetically.
6. **Inspect repo contents before categorizing.** Don't assume based on the name alone — check what's inside.

## Choosing a Category

| Scenario | Category | Reasoning |
|----------|----------|-----------|
| "We need frontend + backend + infra repos for EventFlow" | App (`eventflow-*`) | Multiple repos for one application — app name groups them |
| "We need a Spring Boot codebase to show the tech stack" | `ts-java-spring-boot-*` | The tech stack drove the need |
| "We need a lab for CVE remediation using SonarQube" | `uc-cve-remediation-sonarqube` | The task/exercise drove the need (tech-agnostic) |
| "We forked Keycloak for IAM workshops" | `keycloak` | Well-known project, keep upstream name |

## Repo Description Format

Set meaningful descriptions via the GitHub API:

```
Lab N: [Lab Title] — [One-line description]. Source: [upstream repo if imported]
```

Example:
```
Lab 2: Framework Upgrade and Refactor — Upgrade Java 11/Spring Boot 2.6 monolith to Java 17+/Spring Boot 3.x, extract a microservice. Source: spring-boot-realworld-example-app
```
