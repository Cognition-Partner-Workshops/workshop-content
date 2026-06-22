# Mutation Testing

<a id="table-of-contents"></a>
## Table of Contents
- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [timesheet-app](#timesheet-app)
- [Going Further](#going-further)

## Repositories

- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [timesheet-app](#timesheet-app)

---

<a id="challenge"></a>
## Challenge

Set up mutation testing (PIT for Java, Stryker for JavaScript) to evaluate test suite effectiveness. Find and fix surviving mutants to improve test quality. Mutation testing goes beyond line coverage — it measures whether your tests actually detect real bugs. This is the kind of deep quality analysis that teams rarely prioritize but Devin can perform routinely.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Set up PIT (pitest) mutation testing for
uc-spring-boot-upgrade-microservice-extraction. Configure
the Gradle pitest plugin, run the mutation analysis
against the service and controller layers, and generate
a mutation coverage report. Identify the top surviving
mutants and add or improve JUnit tests to kill them.
```

<a id="target-outcomes"></a>
## Target Outcomes

- Mutation testing framework configured and running
- Mutation score report generated
- Tests added or improved to kill surviving mutants
- PR with mutation testing setup and improved tests

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin configures mutation testing frameworks for different tech stacks
- How Devin interprets mutation testing reports to identify weak test coverage
- Devin's ability to write targeted tests that kill surviving mutants
- The difference between code coverage and mutation-based test effectiveness
- How scheduled mutation analysis can continuously monitor test suite health

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Test quality analysis beyond line coverage
- Build tool configuration (Gradle plugins, npm packages)
- Targeted test generation based on mutation reports
- PR creation with mutation analysis evidence

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot 2.6.3 monolith with an existing JUnit test suite and Gradle build — ideal for PIT mutation testing.

### Step 1: Paste into Devin

```
Set up PIT (pitest) mutation testing for
uc-spring-boot-upgrade-microservice-extraction. Configure
the Gradle pitest plugin, run the mutation analysis
against the service and controller layers, and generate
a mutation coverage report. Identify the top surviving
mutants and add or improve JUnit tests to kill them.
```

### Step 2: Research with Ask Devin

- *"What is the current test coverage of uc-spring-boot-upgrade-microservice-extraction? Which packages have the weakest coverage that mutation testing would expose?"*
- *"What PIT mutators are most valuable for a Spring Boot REST API? Should we use the default set or add custom mutators?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the service layer logic and identify which methods have complex branching — these are where surviving mutants are most likely.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the new tests target specific surviving mutants, or are they generic? Check that each new test has a clear assertion tied to a mutation
- **Leave a comment** asking Devin to focus on killing mutants in a specific service class
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- Mutation testing reveals tests that pass by coincidence — high line coverage does not mean high fault detection
- PIT mutators that flip conditionals and remove return values find the most impactful test gaps
- Targeted test generation based on surviving mutants produces higher-value tests than random generation

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express backend with Jest tests — suitable for Stryker mutation testing.

### Step 1: Paste into Devin

```
Set up Stryker mutation testing for the backend of
timesheet-app. Configure Stryker with the Jest test
runner, run mutation analysis against the API routes and
validation logic, and generate a mutation score report.
Identify surviving mutants and add or improve Jest tests
to kill them.
```

### Step 2: Research with Ask Devin

- *"What test coverage gaps exist in timesheet-app's backend? Which route handlers have the most complex validation logic?"*
- *"What Stryker mutators are most relevant for Express.js API code? Should we focus on conditional boundary mutants or method call mutants?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the API route structure and validation schemas. Focus on routes with business logic like hours validation (0-24 range) and client ownership checks.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Stryker configuration target the right source files? Are the new tests specific enough to kill individual mutants?
- **Leave a comment** asking Devin to improve mutation coverage for a specific route handler
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- Stryker for JavaScript provides the same mutation analysis capability as PIT for Java — the concept is language-agnostic
- Surviving mutants in validation logic are particularly dangerous — they represent undetected input handling bugs
- Mutation score is a more honest metric than line coverage for assessing test suite quality

---

<a id="going-further"></a>
## Going Further

### Scheduled Mutation Analysis

Configure a weekly Devin scheduled session to run mutation testing and track mutation score trends:

- Run PIT/Stryker against the latest codebase
- Compare mutation score against the previous week's baseline
- If the mutation score drops, generate targeted tests to kill new surviving mutants
- Open a PR with the improved tests and a trend report

### Event-Driven Mutation Checks

Trigger mutation analysis in CI when test files change — ensuring that new tests actually detect faults:

```
PR modifies test files
    → CI triggers mutation analysis on changed modules
    → Devin session starts if mutation score drops:
       "Mutation score dropped in module X. Analyze
        surviving mutants and add tests to kill them."
```

### Divide and Conquer Across Modules

For large codebases, spawn child Devin sessions to run mutation analysis in parallel — one per module or package. Each child generates a mutation report and targeted tests, and the parent aggregates results into a quality dashboard.
