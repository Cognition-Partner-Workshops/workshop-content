# LLM Integration Patterns

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Notes](#notes)
- [Repositories](#repositories)
  - [timesheet-app](#timesheet-app)
  - [uc-document-review-automation](#uc-document-review-automation)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

1. Pick a repository below
2. Paste the sample prompt into Devin
3. Observe how Devin integrates an LLM service with proper error handling, rate limiting, and fallback behavior
4. Review the PR and leave feedback — Devin will iterate on your comments

---

## Challenge

Add LLM-powered features to existing applications — such as summarization, classification, or semantic search — with proper error handling, rate limiting, and fallback behavior. This exercises Devin's ability to integrate AI services into production codebases.

## Target Outcomes

- LLM integration with at least one feature (summarization, classification, or search)
- Proper error handling, rate limiting, and fallback behavior
- Configuration for API keys and model selection (not hardcoded)
- Usage documentation explaining the integration and how to configure it
- PR with LLM integration and usage documentation

## What Participants Will Learn

- How Devin integrates external AI/LLM services into existing application architectures
- How Devin handles production concerns like error handling, retries, and rate limiting
- How Devin structures prompt templates and manages LLM configuration
- The importance of specifying fallback behavior and error handling requirements in prompts
- How the **PR feedback loop** works — reviewers comment, Devin iterates, CI re-runs (see [Collaboration Model](../../shared/general-themes/collaboration-model.md))

## Devin Features Exercised

- API integration and service architecture
- Error handling and resilience patterns
- Prompt engineering and LLM configuration
- PR creation with integration documentation
- **Devin Review** — can catch hardcoded secrets, missing error handling, or prompt injection risks in LLM integrations (see [Platform Capabilities → Devin Review](../../shared/general-themes/platform-capabilities.md#devin-review))

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

## Notes

- Participants do not need real API keys — Devin should scaffold the integration with configurable API key settings and mock/stub modes for testing
- Good follow-up: have participants review each other's LLM integrations for prompt quality and error handling robustness
- Encourage specifying the exact LLM feature desired (summarization, classification, etc.) rather than leaving it open-ended
- API keys should be managed through environment variables, consistent with Devin's secrets management layer (see [Architecture Strengths → Shared Context Layer](../../shared/general-themes/architecture-strengths.md#shared-context-layer))

---

## Repositories

### <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

React + Node.js/Express timesheet application — add LLM-powered smart categorization or description summarization for timesheet entries.

#### Step 1: Paste into Devin

```text
Add an LLM-powered feature to timesheet-app that automatically categorizes and summarizes timesheet entries. Create a backend service that: (1) accepts a timesheet entry description and returns a suggested category (development, meeting, review, admin, etc.) and a one-line summary, (2) uses an OpenAI-compatible API with the model and API key configurable via environment variables, (3) includes a fallback that returns a default category and the original description if the LLM is unavailable, (4) implements rate limiting (max 10 requests/minute per user), and (5) exposes this as POST /api/entries/:id/categorize. Add a simple UI button that triggers categorization. Include a README section explaining configuration.
```

#### Step 2: Research with Ask Devin

- *"What is the structure of timesheet entries in timesheet-app? What fields are available as input for LLM-based categorization?"*
- *"What categories or tags already exist in the app? Should the LLM use existing categories or suggest new ones?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the entry data model and existing categorization patterns. The LLM integration should complement, not replace, any existing classification logic.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the integration handle API failures gracefully? Is the API key properly secured (not hardcoded)?
- **Leave a comment** asking Devin to add a batch categorization endpoint that processes multiple entries in a single LLM call

---

### <a id="uc-document-review-automation"></a>uc-document-review-automation

**Repository:** [uc-document-review-automation](https://github.com/Cognition-Partner-Workshops/uc-document-review-automation)

Document review automation repository — enhance with LLM-powered summarization and key finding extraction for reviewed documents.

#### Step 1: Paste into Devin

```text
Enhance uc-document-review-automation with LLM-powered document analysis. Add a service that: (1) takes a document (text or markdown) and produces a structured summary with key findings, risk areas, and action items, (2) classifies each finding by severity (critical, major, minor, informational), (3) uses an OpenAI-compatible API with configurable model and API key via environment variables, (4) includes retry logic with exponential backoff for transient API failures and a stub mode for testing without a live API, and (5) outputs results in a structured JSON format suitable for downstream processing. Add unit tests with mocked LLM responses. Include documentation explaining the integration architecture.
```

#### Step 2: Research with Ask Devin

- *"What document types and review workflows exist in uc-document-review-automation? What structure should the LLM output follow to be compatible?"*
- *"What existing automation does the repo provide? How should LLM-powered analysis integrate with the current review pipeline?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the document review pipeline and output formats. The LLM integration should produce output compatible with existing review workflows.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the prompt templates well-structured? Does the severity classification logic make sense for the document types?
- **Leave a comment** asking Devin to add a comparison mode that highlights differences between the LLM summary and any existing manual review notes

---

## Key Takeaways

- Devin can integrate LLM services into existing applications with production-grade error handling, rate limiting, and fallback behavior
- API keys and model configuration should use environment variables — never hardcoded — consistent with Devin's secrets management approach
- Stub/mock modes enable testing without live API access, making the integration CI-friendly
- The PR feedback loop lets reviewers evaluate prompt quality, error handling robustness, and security (no prompt injection risks)
- Devin Review can catch hardcoded secrets and missing error handling in LLM integration PRs

---

## Going Further

- **Ticket-driven LLM feature requests** — tag a Jira or GitHub Issue with `Devin:Implementation` and Devin picks it up automatically, scaffolds the LLM integration with proper configuration, and opens a PR linked back to the ticket (see [Design Patterns → Event-Driven Triggers](../../shared/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers))
- **Playbook-driven LLM integration** — create a playbook encoding your team's LLM integration standards (prompt template structure, error handling patterns, rate limiting, security checks) so every LLM feature follows consistent practices (see [Platform Capabilities → Playbooks](../../shared/general-themes/platform-capabilities.md#playbooks))
- **Scheduled prompt quality audits** — run Devin on a monthly schedule to review existing LLM prompts for quality, identify prompt injection risks, and check for deprecated model references (see [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md#scheduled-sessions))
