# Lab: Test Coverage Blitz

## Objective

Identify the service with weakest test coverage, then use Devin to add meaningful tests.

## What's Wrong

Test coverage varies wildly across the OtterWorks services. Some services have good coverage with unit and integration tests. Others have minimal or no tests. Coverage configuration has been added across services (`.coveragerc`, Jest config, Jacoco, SimpleCov, etc.) but many services still have gaps.

The goal is not to achieve 100% coverage everywhere — it is to identify the weakest service and meaningfully improve its test suite.

## Where to Look

- Each service's test directory (patterns vary by language):
  - Python: `pytest --cov=app --cov-report=term-missing`
  - Node.js: `npm test -- --coverage`
  - Go: `go test -cover ./...`
  - Java: `./gradlew test jacocoTestReport` or `mvn test`
  - Rust: `cargo test`
  - Ruby: `bundle exec rspec` (SimpleCov report)
  - C#: `dotnet test`
- `Makefile` — Run `make test-coverage` to get coverage reports for all services
- Coverage config files:
  - `services/document-service/.coveragerc`
  - `services/search-service/.coveragerc`
  - `services/collab-service/package.json` (jest coverage config)
  - `services/auth-service/build.gradle` (Jacoco config)

## What Done Looks Like

- [ ] Ran `make test-coverage` (or individual service test commands) and reviewed coverage reports
- [ ] Identified the service with the weakest coverage (fewest tests, lowest line coverage, or most untested critical paths)
- [ ] Added at least 3-5 meaningful tests to the weakest service:
  - Tests cover actual business logic (not just "function exists" assertions)
  - Tests follow the existing test patterns and framework in the service
  - Tests pass when run with the standard test command
- [ ] Coverage improved measurably for the target service

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started
Run `make test-coverage` to get a baseline for all services. Look for services with 0% coverage, very few test files, or large untested modules.

### Hint 2 — Specific Direction
The document-service (Python/FastAPI) and notification-service (Kotlin/Ktor) tend to have room for improvement. Look at what endpoints and service methods exist vs. which ones have test coverage.

### Hint 3 — Approach
Ask Devin: "Run `make test-coverage` and identify the service with the lowest test coverage. Then read the service's source code and existing tests (if any), and add 5 meaningful unit or integration tests that cover the most critical untested code paths. Follow the existing test framework and patterns."

## Time Budget
- ~15 minutes: Run coverage reports, identify the weakest service
- ~30-45 minutes: Write and verify 3-5 meaningful tests
- Advanced participants: tackle multiple services or add integration tests
