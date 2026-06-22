# Test Framework Migration

<a id="table-of-contents"></a>
## Table of Contents
- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [petclinic-angular](#petclinic-angular)
- [ts-angular-realworld](#ts-angular-realworld)
- [Going Further](#going-further)

## Repositories

- [petclinic-angular](#petclinic-angular) — source (Karma/Jasmine → Vitest/Playwright)
- [ts-angular-realworld](#ts-angular-realworld) — reference target (already uses Vitest + Playwright)

---

<a id="challenge"></a>
## Challenge

Migrate from deprecated test frameworks to modern alternatives. This challenge covers the full migration lifecycle: analyzing existing tests, setting up the new framework, converting tests, and verifying parity. Test framework migrations are large-scale, pattern-driven tasks — exactly the kind of work where Devin adds the most value by handling repetitive conversions while engineers review for correctness.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Migrate the test infrastructure in petclinic-angular from
Karma/Jasmine to Vitest. Analyze all .spec.ts files,
convert them to Vitest syntax, update the test
configuration, and ensure all tests pass with the new
runner. Remove the Karma/Jasmine dependencies.
```

<a id="target-outcomes"></a>
## Target Outcomes

- Test framework migrated from Karma/Jasmine to Vitest (unit) and Playwright (E2E)
- All existing tests converted and passing under the new framework
- Old framework dependencies removed
- Migration runbook documenting the conversion patterns
- PR with the migrated test suite

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin analyzes existing test patterns and maps them to new framework equivalents
- How Devin handles bulk test file conversion while preserving test intent
- How to use a reference repository to guide the migration target architecture
- Devin's ability to iterate: convert → run → fix → re-run
- How divide-and-conquer patterns (child sessions per test file) can accelerate large migrations

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Multi-file codebase transformation
- Build system configuration (karma.conf.js → vitest.config.ts)
- Test framework API mapping
- PR creation with migration documentation

## Difficulty

Intermediate

## Estimated Time

60 minutes

---

## <a id="petclinic-angular"></a>petclinic-angular

**Repository:** [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular)

Angular application currently using Karma + Jasmine for unit tests and Protractor for E2E. Migrate to Vitest + Playwright.

### Step 1: Paste into Devin

```
Migrate the test infrastructure in petclinic-angular from
Karma/Jasmine to Vitest. Analyze all .spec.ts files,
convert them to Vitest syntax, update the test
configuration, and ensure all tests pass with the new
runner. Remove the Karma/Jasmine dependencies.
```

### Step 2: Research with Ask Devin

- *"What are the key differences between Karma/Jasmine and Vitest for Angular testing? What patterns require manual attention during migration?"*
- *"Are there any Angular-specific test utilities (TestBed, ComponentFixture) that behave differently under Vitest vs. Karma?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the test architecture and identify which test files use the most complex Karma/Jasmine features (async testing, custom matchers).

### Step 4 (Optional): Review & Give Feedback

**Leave a feedback comment** and watch Devin respond:
- *"This test still imports from @angular/core/testing with a Karma-specific setup — please update to the Jest/Vitest equivalent"*
- *"The Protractor-style element selectors need to be converted to Playwright locators"*
- *"Add the migration runbook to the PR description so reviewers can understand the conversion patterns"*

### Key Takeaways

- Test framework migrations are highly pattern-driven — once Devin learns the mapping, it applies consistently
- The migration runbook is as valuable as the code changes — it documents the patterns for future reference
- Angular's TestBed configuration changes between frameworks and needs careful attention

---

## <a id="ts-angular-realworld"></a>ts-angular-realworld

**Repository:** [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld)

Angular app that already uses Vitest + Playwright — use as a **reference target** for the migration, or as a second repo for parallel comparison.

### Step 1: Paste into Devin

```
Review the test infrastructure in ts-angular-realworld.
This repo already uses Vitest for unit tests and
Playwright for E2E tests. Analyze the test configuration
patterns, helper utilities, and test structure. Then apply
what you learn to petclinic-angular — use this repo as
the reference implementation for the migration target.

Specifically:
1. Document the Vitest configuration pattern
   (vitest.config.ts, test setup files, Angular-specific
   configuration)
2. Document the Playwright configuration pattern
   (playwright.config.ts, test helpers, authentication
   fixtures)
3. Create a migration guide comparing the Karma/Jasmine
   patterns in petclinic-angular to the equivalent
   Vitest/Playwright patterns in this repo
```

### Step 2: Research with Ask Devin

- *"What are the key differences between the Karma+Jasmine configuration in petclinic-angular and the Vitest configuration in ts-angular-realworld?"*
- *"Are there any Angular-specific testing utilities that differ between the two setups?"*

### Step 3 (Optional): Read the DeepWiki

Compare both repos' DeepWiki pages side-by-side. Look for differences in test helpers, mock strategies, and component testing approaches.

### Step 4 (Optional): Review & Give Feedback

- **Review the migration guide** — is it detailed enough for a developer to follow without Devin's help?
- **Leave a comment** asking Devin to add before/after code examples for the most common conversion patterns

### Key Takeaways

- Using a reference repo gives Devin a concrete target architecture — better results than abstract migration instructions
- Migration guides document the patterns for future manual migrations in other repos
- Side-by-side comparison reveals subtle differences that automated migration tools miss

---

<a id="going-further"></a>
## Going Further

### Divide and Conquer for Large Migrations

For repos with many test files, spawn child Devin sessions to convert files in parallel:

1. Parent session analyzes the test suite and groups files by complexity
2. Spawns child sessions — one per group of test files
3. Each child converts its files and verifies they pass
4. Parent merges results and runs the full suite for integration verification

### Event-Driven Migration Verification

After the initial migration, set up a CI check that flags any remaining references to the old framework:

```
PR introduces a new test file
    → CI checks for Karma/Jasmine imports
    → If found, Devin session starts:
       "New test file uses Karma/Jasmine syntax. Convert
        to Vitest following the migration guide."
```

### Playbook for Framework Migrations

Capture the migration methodology as a Playbook that can be applied to other Angular repos with the same Karma/Jasmine → Vitest migration path. The Playbook encodes the conversion patterns, configuration templates, and verification steps.
