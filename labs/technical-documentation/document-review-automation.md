# Document Review Automation

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Repositories](#repositories)
  - [uc-document-review-automation](#uc-document-review-automation)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

Copy the **Step 1** prompt below into a new Devin session targeting the uc-document-review-automation repo. No prerequisites beyond repo access.

---

## Challenge

Implement and improve automated document review for loan correction processing. This exercises Devin's ability to work with document extraction pipelines, fuzzy matching algorithms, confidence-based decisioning, and audit logging for compliance.

## Target Outcomes

- Document extraction pipeline that handles PDF, image, and form-based inputs
- Field comparison engine with exact, fuzzy, and numeric matching strategies
- Decision agent with configurable confidence thresholds and escalation logic
- Audit agent that logs all decisions for compliance traceability
- PR with comparator improvements and unit tests

## What Participants Will Learn

- How Devin works with multi-strategy comparison engines (exact, fuzzy, numeric)
- How Devin implements confidence-based auto-approval decisioning
- Devin's ability to build compliance-oriented audit logging
- How to evaluate AI-generated document processing code

## Devin Features Exercised

- Multi-module Python development (extractors, comparators, agents)
- Algorithm implementation (fuzzy matching, Jaccard similarity)
- Configuration-driven architecture (YAML comparison rules)
- Unit test generation for comparison logic
- PR creation with architectural documentation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## Repositories

### <a id="uc-document-review-automation"></a>uc-document-review-automation

**Repository:** [uc-document-review-automation](https://github.com/Cognition-Partner-Workshops/uc-document-review-automation)

Python document review automation system with extraction, comparison, decisioning, and audit agents for loan processing workflows.

#### Step 1: Paste into Devin

```
Review the uc-document-review-automation codebase. The fuzzy_comparator currently uses Jaccard similarity on bigrams for name matching. Enhance it to also support Levenshtein distance as an alternative strategy, configurable via comparison_rules.yaml. Add unit tests comparing accuracy of both strategies on sample name pairs (with typos, abbreviations, and reordering).
```

#### Step 2: Research with Ask Devin

- *"What document types are most likely to have extraction errors? How should the decision agent weight confidence differently by document type?"*
- *"Should the audit agent support structured query capabilities (e.g., find all reviews with confidence below 0.8)?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the extraction-comparison-decision pipeline and the field routing logic in the comparison agent. Identify which document types would benefit from specialized extraction templates.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Levenshtein implementation handle Unicode characters correctly?
- **Leave a comment** asking Devin to add a batch processing CLI that reads a directory of documents and outputs a comparison report

---

## Key Takeaways

- Document review automation combines algorithm implementation (fuzzy matching) with configuration-driven architecture (YAML rules) — Devin handles both aspects in a single session
- Compliance-oriented audit logging is a cross-cutting concern that Devin integrates consistently across the agent pipeline
- The comparison strategy pattern (Jaccard vs. Levenshtein, configurable via YAML) is a clean extensibility model that Devin generates naturally when the prompt specifies configurability

## Going Further

Document review connects to **documentation-on-code-change triggers** (see [When to Use Devin → Capacity-Constrained](../../shared/general-themes/when-to-use-devin.md)):

- **PR-triggered comparison rule updates** — When new document types are added to the system, trigger a Devin session that analyzes the new extraction templates and suggests appropriate comparison rules (field mappings, matching strategies, confidence thresholds)
- **Scheduled accuracy audits** — Run a recurring session that evaluates the comparator's accuracy against a labeled test set, identifies degrading strategies, and opens PRs with tuned thresholds or alternative algorithms
- **Compliance report generation** — Periodic sessions query the audit log, generate compliance reports (approval rates, escalation frequency, false positive analysis), and commit them to a reports directory for regulatory review
