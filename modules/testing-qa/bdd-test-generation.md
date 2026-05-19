# BDD Test Generation

## Repositories

- [uc-bdd-test-generation-cucumber](#uc-bdd-test-generation-cucumber)
- [ts-java-swagger-petstore](#ts-java-swagger-petstore)

---

## Challenge

Generate BDD test cases from REST API Swagger/OpenAPI definitions and integrate them into an executable Cucumber test suite. This exercises Devin's ability to read API specifications, generate Gherkin scenarios, and produce executable step definitions.

## Target Outcomes

- Gherkin feature files generated from a Swagger/OpenAPI specification
- Executable Cucumber step definitions that validate API behavior
- Test suite integrated with Maven build lifecycle
- Coverage of happy paths, error cases, and edge cases
- PR with generated tests and documentation

## What Participants Will Learn

- How Devin parses OpenAPI/Swagger specifications and extracts testable endpoints
- How Devin generates meaningful BDD scenarios (not just trivial status code checks)
- Devin's ability to produce executable Cucumber/Gherkin tests in Java
- How to evaluate AI-generated test quality and coverage

## Devin Features Exercised

- API specification analysis
- BDD scenario generation (Gherkin syntax)
- Java/Spring Boot test development
- Maven build integration
- PR creation with test evidence

## Difficulty

Intermediate

## Estimated Time

60 minutes

---

## <a id="uc-bdd-test-generation-cucumber"></a>uc-bdd-test-generation-cucumber

**Repository:** [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber)

Spring Boot + Cucumber BDD test framework for REST API validation. Imported from RedFroggy/spring-cucumber-rest-api (MIT license).

### Step 1: Paste into Devin

> Review the uc-bdd-test-generation-cucumber codebase. This is a Cucumber BDD framework for testing REST APIs. Add new Gherkin feature files that test a Petstore-style API (pets CRUD: create, read, update, delete, list). Include scenarios for: successful CRUD operations, validation errors (missing required fields), not-found cases, and pagination. Implement the corresponding step definitions. Open a PR.

### Step 2: Research with Ask Devin

- *"What Cucumber best practices should be followed for REST API testing — should scenarios be independent or can they share state?"*
- *"How should authentication be handled in the BDD scenarios — per-scenario setup or shared background?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the existing Cucumber configuration, step definition patterns, and how the framework maps Gherkin steps to REST API calls. Use this to ensure new scenarios follow established patterns.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the Gherkin scenarios readable by non-developers? Do they describe business behavior rather than implementation details?
- **Leave a comment** asking Devin to add data-driven scenarios using Cucumber Scenario Outlines with Examples tables

---

## <a id="ts-java-swagger-petstore"></a>ts-java-swagger-petstore

**Repository:** [ts-java-swagger-petstore](https://github.com/Cognition-Partner-Workshops/ts-java-swagger-petstore)

Swagger Petstore sample API — use as the target API specification for BDD test generation.

### Step 1: Paste into Devin

> Review the ts-java-swagger-petstore Swagger/OpenAPI specification. Generate a complete set of Gherkin feature files covering all Petstore API endpoints (pets, store, users). For each endpoint, include scenarios for: successful operations, invalid input, authentication failures, and boundary conditions. Save the feature files in a `features/` directory with one file per API resource. Open a PR.

### Step 2: Research with Ask Devin

- *"Which Petstore API endpoints have the most complex request/response schemas? How should those be represented in Gherkin?"*
- *"Should the generated scenarios include performance assertions (e.g., response time thresholds)?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the full API surface and data models. Identify which endpoints have the most complex validation rules and would benefit from the most thorough BDD coverage.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the feature files cover edge cases like empty lists, maximum field lengths, and concurrent updates?
- **Leave a comment** asking Devin to add a README documenting how to run the generated features against a live Petstore instance
