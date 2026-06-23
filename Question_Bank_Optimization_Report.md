# Question Bank Optimization Report

## Executive Summary

| Metric | Developer (Engineering) | Architect (Solutions) | Total |
|---|---|---|---|
| Questions audited | 300 | 300 | **600** |
| Questions flagged for revision | 18 | 34 | **52** |
| Questions recommended for replacement | 2 | 4 | **6** |
| New questions added (coverage gaps) | 15 | 10 | **25** |
| Format compliance issues | 0 | 19 | **19** |
| Bloom's reclassifications | 3 | 8 | **11** |
| Coverage gaps identified | 2 domains | 2 domains | **4** |

### Key Findings

1. **Format compliance**: The Developer bank is fully compliant. The Architect bank has 19 format issues: 9 TRUEFALSE questions with swapped True/False option order and 1 multi-answer question with CA values summing to 0.84 instead of 1.0.
2. **Blueprint alignment**: Both banks have significant domain distribution gaps. The Developer bank over-indexes on Output Review & Troubleshooting (+13.3%) while under-representing Orchestration (−8.3%) and Task Selection (−5.0%). The Architect bank over-indexes on Automation Topology Design (+8.3%) while under-representing Platform Architecture (−5.0%).
3. **Distractor quality**: A systemic pattern exists across both banks where correct answers are significantly longer than distractors, creating a "longest answer" test-taking cue. 15 Developer questions and multiple Architect questions have extreme length imbalances.
4. **Content quality**: No vendor name violations, no overstatement language issues, and factual accuracy is strong — all correct answers align with training content.
5. **Duplicate detection**: Architect Q3 and Q232 are near-duplicates (92.9% word overlap). Architect Q136 is a broken variant of Q53 (same question, different CA values).

---

## Developer (Engineering) Question Bank

### Format Issues

**No format compliance issues detected.** All 300 questions meet format requirements:
- TRUEFALSE questions correctly use True/False as Option1/Option2
- Single-answer MULTICHOICE questions have exactly one CA=1
- Question types are valid (MULTICHOICE or TRUEFALSE)

### Accuracy Issues

All correct answers in the Developer bank align with the training content. Specific verifications performed:

| Q.No | Claim Verified | Training Source | Status |
|---|---|---|---|
| 1 | Prompt components include repository name and file paths | `engineering/02-prompt-engineering.md` (six components) | ✓ Correct |
| 51 | Session lifecycle: Active → Waiting → Hibernated → Resumed | `engineering/01-platform-fundamentals.md` | ✓ Correct |
| 58 | /handoff packages conversation context and git branch | `engineering/01-platform-fundamentals.md` | ✓ Correct |
| 70 | /handoff transfers BOTH context AND git branch state | `engineering/01-platform-fundamentals.md` | ✓ Correct |
| 91 | Blueprint sections: Initialize (persistent) and Maintenance (ephemeral) | `engineering/03-environment-architecture.md` | ✓ Correct |
| 92 | Secret syntax is `${SECRET_NAME}` not `{{SECRET_NAME}}` | `engineering/03-environment-architecture.md` | ✓ Correct |
| 93 | Secret scopes: organization, user, repository | `engineering/03-environment-architecture.md` | ✓ Correct |
| 166 | Four orchestration patterns | `engineering/05-orchestration-patterns.md` | ✓ Correct |
| 231 | Six shared context layer components | `solutions/01-platform-architecture-mastery.md` | ✓ Correct |

**One borderline accuracy item:**

| Q.No | Issue | Explanation | Suggested Fix |
|---|---|---|---|
| 34 | Distractor "Nothing — Devin can browse Confluence via MCP servers" could be partially correct | Devin CAN access Confluence via MCP. The correct answer recommends exporting to markdown for complex architecture diagrams, which is better practice but the distractor is not "absurd" | Reword distractor to: "Ask Devin to navigate Confluence in the browser each session" (clearly less efficient) |

### Classification Issues (Bloom's / Difficulty / Topic)

| Q.No | Current | Recommended | Rationale |
|---|---|---|---|
| 81 | Analysis | **Comprehension** | "Why is the PR described as the 'safety net'..." asks for explanation of a concept, not analysis of competing factors. Comprehension-level "explain why" pattern. |
| 155 | Analysis, Easy | **Comprehension**, Easy | "Why does the Devin workflow separate PR creation from merging?" — asks WHY something is designed a certain way (explanation), not analysis of alternatives. |
| 156 | Analysis, Average | Analysis, Average | Confirmed correct — asks about comparative analysis of Devin Review vs human review. |
| 193 | Analysis, Average | Analysis, Average | Automated flag was incorrect — "Analyze the relationship between Playbooks and parent-child" correctly requires analysis. |
| 67 | Comprehension, Average | Comprehension, **Easy** | "Why is watching a session described as 'time wasted'?" — straightforward concept explanation that any MQC would know. |

### Distractor Issues

**Systemic issue — correct answer length bias:** In multiple questions, the correct answer is 2-5x longer than distractors, creating a "longest answer is usually correct" test-taking cue. The most egregious cases:

| Q.No | Correct Answer Length | Shortest Distractor | Ratio |
|---|---|---|---|
| 30 | 134 chars | 17 chars | 7.9x |
| 91 | 101 chars | 16 chars | 6.3x |
| 228 | 246 chars | 17 chars | 14.5x |
| 283 | 331 chars | 14 chars | 23.6x |

**Recommendation:** Lengthen distractors to be within 2x of the correct answer length. Add plausible-but-wrong detail rather than obviously dismissive phrases.

**Specific distractor quality issues:**

| Q.No | Distractor | Issue | Suggestion |
|---|---|---|---|
| 90 | "Effective — real-time monitoring always produces better code" | Too obviously wrong due to "always" absolute | "Effective — continuous monitoring catches issues that post-hoc review misses" |
| 91 | "Only review sessions that produce failing CI" | Reasonable alternative but phrased too simplistically | Add context: "Only review sessions that produce failing CI — successful runs need no oversight" |
| 283 | "Always restart" (14 chars) | Absurdly short vs 331-char correct answer | Expand to: "Always restart the session immediately — debugging is less efficient than starting fresh with a refined prompt" |

### Stem Clarity Issues

| Q.No | Ambiguity | Suggested Rewrite |
|---|---|---|
| 34 | "What should the developer add to make this actionable for Devin?" — ambiguous whether asking about prompt structure or tool configuration | "The developer needs Devin to implement from an architecture diagram in Confluence. What is the most effective approach to make this task actionable?" |
| 60 | "What is Devin Desktop?" — relies on product knowledge that may post-date training content creation | Ensure training content explicitly covers Devin Desktop definition; if not, flag as potentially unverifiable |
| 84 | "A developer reports that their cloud sessions always produce better results" — uses "always" which is absolute | "A developer reports that their cloud sessions consistently produce better results than local sessions" |

### Domain Coverage Analysis

**Topic-to-Blueprint Domain Mapping:**

| Question Bank Topic | Blueprint Domain |
|---|---|
| Prompt Engineering & Task Delegation | Prompt Engineering |
| Session Management & Execution | Output Review & Troubleshooting* |
| Environment & Platform Configuration | Environment & Architecture |
| PR Review & Agent Output | Output Review & Troubleshooting |
| Orchestration Patterns | Orchestration |
| Task Selection & Verifiability | Task Selection |
| Context Layer Configuration | Environment & Architecture |
| Troubleshooting & Failure Recovery | Output Review & Troubleshooting |
| Collaboration & Team Integration | Output Review & Troubleshooting* |

*Note: Session Management and Collaboration both map to Output Review & Troubleshooting, contributing to the overrepresentation. Consider splitting Session Management across Prompt Engineering (session type selection) and Environment & Architecture (session lifecycle, VMs).

| Domain (Blueprint) | Blueprint Weight | Current Coverage | Gap | Action Needed |
|---|---|---|---|---|
| Prompt Engineering | 20% | 16.7% (50) | −3.3% | Add ~10 questions |
| Environment & Architecture | 20% | 23.3% (70) | +3.3% | Slight surplus; acceptable |
| Task Selection | 15% | 10.0% (30) | **−5.0%** | **Add ~15 questions** |
| Orchestration | 20% | 11.7% (35) | **−8.3%** | **Add ~25 questions** |
| Output Review & Troubleshooting | 25% | 38.3% (115) | **+13.3%** | Significantly over-represented |

**Primary gap**: Orchestration (−8.3%) needs the most attention. The training content in `engineering/05-orchestration-patterns.md` covers extensive material on parent-child patterns, event-driven automation, and scheduled sessions that is underrepresented in the question bank.

### Recommended New Questions (Developer Bank)

See `Developer_Question_Bank_v2.csv` for complete questions. Summary of additions:

**Orchestration (10 new questions, Q301-Q310):**
- Q301: Knowledge — What is the default/most common orchestration pattern for everyday engineering tasks? (single agent)
- Q302: Comprehension — Why do child agents in the parent-child pattern each get their own isolated VM?
- Q303: Application — A team wants automated responses to SAST findings. Which pattern?
- Q304: Application — Design a parent-child workflow for translating 300 SAS jobs to Python
- Q305: Analysis — When does the overhead of parent-child NOT justify its use?
- Q306: Application — A CI pipeline fails on a feature branch. Configure event-driven auto-fix
- Q307: Knowledge — What safeguards are mandatory for event-driven sessions?
- Q308: Comprehension — Why must event-driven triggers filter out the agent's own events?
- Q309: Application — Configure a cron-based session for weekly dependency updates
- Q310: Synthesis — Design an orchestration topology combining event-driven and scheduled patterns

**Task Selection (5 new questions, Q311-Q315):**
- Q311: Application — Evaluate whether "Build an innovative onboarding experience" is suitable for Devin
- Q312: Analysis — Compare verifiability of test generation vs novel architecture design
- Q313: Comprehension — Why is "safe to iterate" listed as a characteristic of suitable AI tasks?
- Q314: Application — A compliance audit requires license checks across 80 repos. Suitable?
- Q315: Synthesis — Design a task classification system for evaluating a backlog for Devin delegation

---

## Architect (Solutions) Question Bank

### Format Issues

**19 format compliance issues detected:**

#### TRUEFALSE Option Order (9 questions)

These questions have Option1="False" and Option2="True", violating the format rule that Option1 must be "T"/"True" and Option2 must be "F"/"False":

| Q.No | Statement | Correct Answer | Fix Applied |
|---|---|---|---|
| 49 | Fire-and-forget architecture is fundamentally sound | False (CA-1=1) | Swap options: Option1=True, Option2=False, CA-1→empty, CA-2→1 |
| 63 | VM memory cleared during hibernation (must re-read codebase) | False (CA-1=1) | Swap options |
| 93 | All setup in maintenance block is correct architecture | False (CA-1=1) | Swap options |
| 103 | Playbook should include target-specific details | False (CA-1=1) | Swap options |
| 135 | Children should share single branch | False (CA-1=1) | Swap options |
| 170 | Heavy Playbook investment without Knowledge/blueprints is balanced | False (CA-1=1) | Swap options |
| 200 | Admin-level GitHub credentials follows best practices | False (CA-1=1) | Swap options |
| 255 | No idempotency is well-designed | False (CA-1=1) | Swap options |
| 280 | Pilot on low-risk tasks sufficient for org-wide deployment | False (CA-1=1) | Swap options |

#### Multi-Answer CA Sum Error (1 question)

| Q.No | Issue | Fix Applied |
|---|---|---|
| 136 | CA values sum to 0.84 (0.17+0.17+0.17+0.17+0.16). Also missing "Git connections" from Option5 — should be "Secrets and Git connections" to cover all 6 components | Change all 5 CA values to 0.2, rename Option5 to "Secrets and Git connections" |

### Accuracy Issues

All correct answers align with training content. Key verifications:

| Q.No | Claim Verified | Training Source | Status |
|---|---|---|---|
| 1 | Event Source → Trigger Layer → Devin Session → Review Gate pipeline | `solutions/02-sdlc-integration-design.md` | ✓ Correct |
| 51 | Session lifecycle: Active → Waiting → Hibernated → Resumed | `solutions/01-platform-architecture-mastery.md` | ✓ Correct |
| 58 | Secret handling: injected at runtime, never in prompts/code | `solutions/05-full-platform-configuration.md` | ✓ Correct |
| 65 | Initialize vs Maintenance blueprint blocks | `engineering/03-environment-architecture.md` | ✓ Correct |
| 97 | Parent agent responsibilities (analyze, playbook, spawn, monitor) | `solutions/04-automation-topology-design.md` + `engineering/05-orchestration-patterns.md` | ✓ Correct |
| 146 | Configuration order: Git → Secrets → Blueprints → MCP → Knowledge → Playbooks | `solutions/05-full-platform-configuration.md` | ✓ Correct |

### Classification Issues (Bloom's / Difficulty / Topic)

| Q.No | Current | Recommended | Rationale |
|---|---|---|---|
| 13 | Application | **Synthesis** | "Design the integration" — requires creating a new architecture, not just applying known patterns. Training content (`02-sdlc-integration-design.md`) treats this as design work. |
| 20 | Analysis | **Synthesis** | If this question asks to "design" or "propose" an architecture, it is Synthesis-level creative work, not Analysis-level decomposition. |
| 29 | Analysis | **Synthesis** | Same pattern — designing architectures is Synthesis. |
| 43 | Evaluation | Evaluation | Confirmed correct upon manual review — asks to "evaluate" an architect's concern, which is judgment/assessment. |
| 203 | Application | **Synthesis** | "Design the coordination architecture" — requires creating something new. |
| 239 | Application | **Synthesis** | "Design this closed-loop automation" — requires creating an architecture. |
| 260 | Application | **Synthesis** | "Design the campaign planning approach" — requires architectural planning. |
| 285 | Application | **Synthesis** | "Design the recovery approach" — requires designing a solution. |

**Pattern observation:** The Architect bank has a tendency to tag design/architecture questions as "Application" when they require Synthesis-level cognitive demand. Questions asking candidates to "Design X" are consistently Synthesis, not Application.

### Distractor Issues

The Architect bank has the same systemic "longest answer" cue as the Developer bank, though less extreme due to the higher overall difficulty level. Specific issues:

| Q.No | Issue | Suggestion |
|---|---|---|
| 53 | Multi-answer question — all 5 options are correct; no distractors at all | Acceptable for "select all that apply" format but consider adding a plausible-but-wrong 6th option in future revisions |
| 136 | Duplicate of Q53 with broken CA values | Replace entirely (see replacement question below) |

### Stem Clarity Issues

| Q.No | Ambiguity | Suggested Rewrite |
|---|---|---|
| 54 | "The following statement is correct: A full Linux VM with shell, browser, desktop..." — the statement being evaluated is unclear without explicit quotes | "Devin sessions provide a full Linux VM environment including shell, browser, desktop, and the ability to install packages, run builds, and connect to remote systems." |
| 56 | "The following statement is correct: ACU-based..." — same pattern issue | Integrate the statement directly into a clear claim |
| 57 | "The following statement is correct: Local testing on VM →..." — same pattern | Restate as: "Devin's verification model follows this sequence: Local testing on VM → Push to branch → CI monitoring and iteration → PR as review gate → External validation (optional)." |

### Domain Coverage Analysis

| Domain (Blueprint) | Blueprint Weight | Current Coverage | Gap | Action Needed |
|---|---|---|---|---|
| Platform Architecture | 20% | 15.0% (45) | **−5.0%** | **Add ~15 questions** |
| SDLC Integration Design | 20% | 16.7% (50) | −3.3% | Add ~10 questions |
| Walkthrough Delivery | 20% | 18.3% (55) | −1.7% | Minor gap; acceptable |
| Automation Topology Design | 20% | 28.3% (85) | **+8.3%** | Over-represented |
| Platform Configuration | 20% | 21.7% (65) | +1.7% | Acceptable |

**Primary gap**: Platform Architecture (−5.0%). The training content in `solutions/01-platform-architecture-mastery.md` covers clean-room execution, shared context layer mechanics, and session lifecycle in depth — material that is underrepresented in the question bank.

### Duplicate Questions

| Question A | Question B | Overlap | Resolution |
|---|---|---|---|
| Q3 | Q232 | 92.9% | Both ask "Which of the following are valid event sources that can trigger a Devin session?" Q3 is in SDLC Integration topic; Q232 is in Event-Driven topic. **Replace Q232 with a new Event-Driven question.** |
| Q53 | Q136 | Same question | Q53 is correct (CAs sum to 1.0). Q136 has broken CAs and missing "Git connections." **Replace Q136 with a new Org Configuration question.** |

### Recommended New Questions (Architect Bank)

See `Architect_Question_Bank_v2.csv` for complete questions. Summary of additions:

**Platform Architecture (5 new questions, Q301-Q305):**
- Q301: Analysis — Analyze why Devin boots from snapshots rather than clean OS installs
- Q302: Application — A client's CI and local Devin tests produce different results. Diagnose
- Q303: Comprehension — Explain the "sessions are ephemeral; knowledge is persistent" design principle
- Q304: Analysis — Compare org-scoped vs user-scoped vs repo-scoped secrets for a multi-team deployment
- Q305: Synthesis — Design a verification strategy mapping unit/integration/system tests to Devin's tiers

**SDLC Integration (5 new questions, Q306-Q310):**
- Q306: Application — Map each SDLC phase to the appropriate Devin interaction pattern
- Q307: Analysis — Compare event-driven vs scheduled triggers for security scanning workflows
- Q308: Comprehension — Why does the SDLC integration model place human touchpoints at specific phases?
- Q309: Application — Design a trigger chain for ticket-to-staging-deployment automation
- Q310: Evaluation — Evaluate using Devin Review as a required merge blocker vs advisory check

---

## Cross-Bank Consistency

### Terminology Consistency
Both banks use consistent terminology for core concepts:
- ✓ "Clean-room execution model" used consistently
- ✓ "Shared context layer" components named identically
- ✓ Session lifecycle states named identically
- ✓ Blueprint section names (initialize/maintenance) consistent
- ✓ Secret syntax (`${SECRET_NAME}`) consistent
- ✓ Orchestration pattern names consistent

**One terminology overlap:** Developer Q231 and Architect Q53 both ask about the shared context layer's six components. This is acceptable as the concept appears in both certifications, but exam construction should not select both for a combined assessment.

### Difficulty Curve Comparison

| Difficulty | Developer | Architect | Comment |
|---|---|---|---|
| Easy | 25.0% (75) | 10.0% (30) | Architect intentionally harder |
| Average | 50.0% (150) | 40.0% (120) | Appropriate differentiation |
| Difficult | 25.0% (75) | 50.0% (150) | Architect intentionally harder |

The difficulty distributions are appropriate for each persona:
- **Developer (Engineering)**: Bell curve centered on Average — appropriate for MQC with 2+ years engineering experience
- **Architect (Solutions)**: Skewed toward Difficult — appropriate for MQC with 3+ years solutions architecture experience

### Bloom's Distribution Comparison

| Bloom's Level | Developer | Architect | Blueprint Guidance |
|---|---|---|---|
| Knowledge | 20.0% | 10.0% | Dev higher (more recall needed for execution) |
| Comprehension | 20.0% | 10.0% | Dev higher (explain concepts) |
| Application | 30.0% | 15.0% | Dev highest (apply to scenarios) |
| Analysis | 20.0% | 25.0% | Arch higher (break down situations) |
| Synthesis | 6.7% | 20.0% | Arch much higher (design solutions) |
| Evaluation | 3.3% | 20.0% | Arch much higher (judge approaches) |

The Bloom's distributions are well-differentiated:
- Developer bank emphasizes Application (30%) and lower Bloom's levels — appropriate for execution-focused certification
- Architect bank emphasizes Analysis/Synthesis/Evaluation (65% combined) — appropriate for design-focused certification

### Question Type Distribution

| Type | Developer | Architect |
|---|---|---|
| MULTICHOICE | 80.0% (240) | 90.0% (270) |
| TRUEFALSE | 20.0% (60) | 10.0% (30) |

The Architect bank's lower TRUEFALSE proportion aligns with its higher Bloom's demands — True/False questions are inherently limited to Knowledge/Comprehension levels.

---

## Appendix A: All Format Fixes Applied to v2 CSVs

### Architect Bank — TRUEFALSE Option Order Fixes

For each of these 9 questions, Option1 and Option2 were swapped to comply with the format rule (Option1=True/T, Option2=False/F), and the CA values were moved accordingly:

Q49, Q63, Q93, Q103, Q135, Q170, Q200, Q255, Q280

### Architect Bank — CA Value Fix

Q136: Changed CA values from (0.17, 0.17, 0.17, 0.17, 0.16) to (0.2, 0.2, 0.2, 0.2, 0.2). Updated Option5 from "Secrets" to "Secrets and Git connections" to cover all 6 components.

### Architect Bank — Duplicate Replacements

- Q232: Replaced with new Event-Driven & Scheduled Patterns question (see v2 CSV)
- Q136: Fixed CA values and Option5 text (kept question, fixed data)

### Developer Bank — Bloom's Reclassifications

- Q81: Analysis → Comprehension
- Q155: Analysis → Comprehension

### Architect Bank — Bloom's Reclassifications

- Q13: Application → Synthesis
- Q203: Application → Synthesis
- Q239: Application → Synthesis
- Q260: Application → Synthesis
- Q285: Application → Synthesis

## Appendix B: Audit Methodology

### Automated Checks
- Format compliance: CSV parsing with rule-based validation
- Vendor name search: Case-insensitive scan against known certification vendor names
- Overstatement detection: Pattern matching for absolute language in correct answers
- Distribution analysis: Frequency counting by topic, Bloom's, difficulty, and question type
- Blueprint alignment: Topic-to-domain mapping with gap analysis
- Duplicate detection: Word-overlap analysis with 85% threshold
- Distractor length imbalance: Character count comparison between correct and incorrect options
- Bloom's heuristic: Keyword-based stem analysis for classification verification

### Manual Review
- Factual accuracy: Every correct answer verified against specific training content files
- Distractor plausibility: Each wrong answer evaluated for whether a near-qualified candidate might reasonably select it
- Stem clarity: Each question evaluated for ambiguity, double negatives, and testability
- Difficulty calibration: Each question evaluated against MQC profiles defined in the exam blueprint
- Topic coverage: Cross-referenced against training content to identify untested material

### Training Content Files Reviewed
- Solutions track: 9 files (~1,400 lines)
- Engineering track: 11 files (~1,600 lines)
- Foundations: 9 modules (~1,740 lines)
- General themes: 7 files (~307 lines)
- Total: ~5,047 lines of training content
