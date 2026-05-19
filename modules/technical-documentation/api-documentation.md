# API Documentation

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-data-source-migration-jdbc-normalization](#uc-data-source-migration-jdbc-normalization)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Generate or improve API documentation for an existing application. This includes creating OpenAPI/Swagger specifications, adding API endpoint descriptions, generating interactive documentation, and ensuring the documentation stays in sync with the code.

## Target Outcomes

- OpenAPI 3.0 specification generated from existing code
- Interactive API documentation available (Swagger UI or similar)
- All endpoints documented with request/response schemas, status codes, and examples
- PR with API documentation

## What Participants Will Learn

- How Devin reverse-engineers API contracts from existing code
- How Devin generates OpenAPI specifications with accurate schemas
- The difference between code-first and spec-first API documentation
- How to validate API documentation against the actual implementation

## Devin Features Exercised

- Codebase analysis (endpoint discovery)
- OpenAPI/Swagger specification authoring
- Documentation generation
- PR creation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express backend with REST endpoints for work entries, clients, and reporting.

### Step 1: Paste into Devin

> Generate an OpenAPI 3.0 specification for all REST endpoints in timesheet-app's backend. Include: endpoint paths, HTTP methods, request/response schemas, authentication requirements, status codes, and example payloads. Add swagger-ui-express to serve interactive documentation at /api-docs. Open a PR.

### Step 2: Research with Ask Devin

- *"What REST endpoints exist in timesheet-app? Are there any undocumented or inconsistent endpoints?"*
- *"Does the API follow REST best practices for status codes and error responses? Where does it deviate?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the full API surface. Cross-reference DeepWiki's auto-generated documentation with the OpenAPI spec to catch any gaps.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the request/response schemas match the actual implementation?
- **Leave a comment** asking Devin to add request validation middleware that enforces the OpenAPI spec

---

## <a id="uc-data-source-migration-jdbc-normalization"></a>uc-data-source-migration-jdbc-normalization

**Repository:** [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization)

Spring Boot loan service with REST endpoints — generate Springdoc OpenAPI documentation.

### Step 1: Paste into Devin

> Add Springdoc OpenAPI to uc-data-source-migration-jdbc-normalization. Annotate all REST controllers with @Operation, @ApiResponse, and @Schema annotations. Configure Swagger UI at /swagger-ui.html. Ensure all loan-related endpoints are documented with proper schemas, examples, and error responses. Open a PR.

### Step 2: Research with Ask Devin

- *"What API documentation libraries work best with Spring Boot 3.x? Is Springdoc the right choice?"*
- *"Are there any endpoints that return different response shapes for success vs. error that need multiple @ApiResponse annotations?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the API contracts. Verify that the generated OpenAPI spec accurately represents the data types, especially the legacy-to-modern schema differences.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the @Schema annotations accurate for the JPA entity types?
- **Leave a comment** asking Devin to also generate a Postman collection from the OpenAPI spec

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot monolith with both REST and GraphQL APIs — document the REST API surface with OpenAPI.

### Step 1: Paste into Devin

> Add Springdoc OpenAPI to uc-spring-boot-upgrade-microservice-extraction. Document all REST endpoints with @Operation annotations, request/response schemas, authentication requirements, and example payloads. Configure Swagger UI. Also document the GraphQL schema if possible (GraphQL Voyager or similar). Open a PR.

### Step 2: Research with Ask Devin

- *"How should we document both the REST and GraphQL APIs? Are there tools that can generate a unified API reference?"*
- *"Which API endpoints require authentication and which are public?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to map out the complete API surface across both REST and GraphQL layers.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the OpenAPI spec cover all REST endpoints? Is the GraphQL schema documented?
- **Leave a comment** asking Devin to add API versioning headers to the documentation
