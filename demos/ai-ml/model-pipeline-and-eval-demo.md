# From Notebook to Monitored Pipeline — AI/ML Engineering Demo

A single-thread demo showing Devin taking a statistical anomaly-detection
model from an exploratory notebook to a tested, scheduled, drift-monitored
production pipeline — with an eval harness that measures every variant the
same way, a drift alert that starts a Devin session hands-free, and a
child-session fan-out that runs a detector experiment grid in parallel and
aggregates the results into one comparison. The audience is ML engineers and
data scientists; the outcomes are **time from experiment to production** and
**reproducibility of results**.

The demo runs on
[uc-volume-anomaly-detection](https://github.com/Cognition-Partner-Workshops/uc-volume-anomaly-detection),
a multi-agent Python system with real z-score and seasonal detectors, sample
transaction data, and a pytest suite. The repo has **no notebook today** —
the first step creates one, so the notebook-to-production arc starts from a
real artifact rather than a hypothetical.

<a id="toc"></a>
## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [The Thread at a Glance](#thread)
- [Part 1 — The Experiment: A Notebook in a Real Repo](#part-1)
- [Part 2 — Hardening: Notebook to Tested, Scheduled Pipeline](#part-2)
- [Part 3 — The Eval Harness: Measure Before You Trust](#part-3)
- [Part 4 — Devin Review: Reproducibility and Leakage on Every PR](#part-4)
- [Part 5 — Drift Monitor: An Alert Starts a Session Hands-Free](#part-5)
- [Part 6 — Fan Out the Experiment Grid with Child Sessions](#part-6)
- [What Still Needs a Human](#human-in-the-loop)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Paste this into Devin to create the exploratory notebook the rest of the
demo hardens:

```
In the Cognition-Partner-Workshops/uc-volume-anomaly-detection repo,
create an exploratory Jupyter notebook at
notebooks/volume_model_exploration.ipynb.

Load data/historical/sample_transactions.csv (columns: timestamp,
service_name, endpoint, count, error_count, avg_latency_ms,
p99_latency_ms). In the notebook:

1. Plot request volume per service over time.
2. Reproduce the repo's two existing detectors on this data —
   src/detectors/zscore_detector.py and
   src/detectors/seasonal_detector.py — and show which points each
   flags.
3. Prototype a third variant: a robust z-score using median and MAD
   instead of mean and standard deviation, tunable via a threshold
   parameter.
4. End with a markdown cell summarizing which detector flags what and
   what parameters were used.

Keep the notebook deterministic: fixed random seeds, pinned imports in
a first cell, no hidden state between cells (it should run top to
bottom with Restart & Run All). Add any new dependencies to
requirements.txt.
```

---

<a id="repository"></a>
## Repository

- [uc-volume-anomaly-detection](https://github.com/Cognition-Partner-Workshops/uc-volume-anomaly-detection) —
  volume-based anomaly detection for early issue identification. Python
  multi-agent system: statistical detectors
  (`src/detectors/zscore_detector.py`, `src/detectors/seasonal_detector.py`),
  agents that build baselines and correlate service health
  (`src/agents/anomaly_detector.py`, `src/agents/service_health.py`),
  a runbook-based recommendation engine
  (`src/agents/recommendation_engine.py`), sample historical data
  (`data/historical/sample_transactions.csv`), precomputed baselines
  (`data/baselines/baseline_config.json`), detection thresholds
  (`config/detection_rules.yaml`), and a pytest suite
  (`tests/test_detectors.py`).

---

<a id="thread"></a>
## The Thread at a Glance

```
Part 1: notebook created in the repo (the experiment)
        ↓
Part 2: notebook → src/pipeline/ + tests + scheduled workflow
        (a pre-existing test failure on main gets diagnosed on the way)
        ↓
Part 3: eval harness — injected labeled anomalies, precision/recall/F1
        per detector, EVAL_REPORT.md
        ↓
Part 4: Devin Review on a human's threshold-tweak PR and on
        Devin's own pipeline PR
        ↓
Part 5: scheduled drift monitor → drift detected → Devin v3 API
        session starts hands-free → rebaseline PR
        ↓
Part 6: parent session fans a detector grid out to child sessions,
        aggregates EXPERIMENT_COMPARISON.md
```

None of this runs inside one engineer's editor. The pipeline runs on a
schedule, the drift monitor fires while nobody is watching, the experiment
grid runs as parallel sessions on separate machines, and the review gate
applies to humans and agents alike.

---

<a id="part-1"></a>
## Part 1 — The Experiment: A Notebook in a Real Repo

Run the [Quick Start](#quick-start) prompt. Devin clones the repo, reads the
existing detectors, and builds the notebook against the real CSV.

While it works, ask DeepWiki (or Devin directly) how the pieces fit:

```
Using the Cognition-Partner-Workshops/uc-volume-anomaly-detection
repo, explain how src/agents/anomaly_detector.py orchestrates the
detectors in src/detectors/, where the seasonal baselines come from
(data/baselines/baseline_config.json), and which thresholds
config/detection_rules.yaml controls. Answer as a short architecture
summary with file references.
```

Expected: Devin maps the flow — the anomaly detector agent loads CSV
observations, builds seasonal baselines, then applies seasonal, z-score, and
latency detection; the recommendation engine matches findings against its
built-in runbook (`DEFAULT_RUNBOOK` in `src/agents/recommendation_engine.py`).

The notebook PR is the "before" state every ML team recognizes: an
experiment that works on the author's data, with parameters chosen by
eyeballing plots, and nothing that stops it from silently rotting. The rest
of the demo is about closing that gap.

---

<a id="part-2"></a>
## Part 2 — Hardening: Notebook to Tested, Scheduled Pipeline

The core beat. Paste:

```
In Cognition-Partner-Workshops/uc-volume-anomaly-detection, harden
notebooks/volume_model_exploration.ipynb into a production pipeline:

1. Extract the notebook's logic into src/pipeline/ as importable
   modules: data loading/validation (schema-check the CSV columns),
   baseline building, detection (reusing src/detectors/ plus the new
   robust z-score variant as src/detectors/robust_zscore_detector.py),
   and report generation. The notebook should be reducible to thin
   calls into these modules.
2. All parameters (thresholds, window sizes, input paths) come from
   config/detection_rules.yaml or CLI flags — nothing hard-coded.
3. Add pytest coverage for the new modules alongside the existing
   tests/test_detectors.py, including a fixture-based end-to-end run
   over data/historical/sample_transactions.csv.
4. Run the full existing test suite first and report any failures you
   find on main before making changes. If a failure traces to a code
   bug rather than a bad test, fix the code and explain the diagnosis
   in the PR description.
5. Add a GitHub Actions workflow
   .github/workflows/detection-pipeline.yml that runs the test suite
   on every PR and executes the pipeline on a daily cron schedule,
   uploading the detection report as a build artifact.
6. Document the run commands in the README.
```

**The verification beat (a real bug on main).** The existing suite does not
pass cleanly: `TestSeasonalDetector.test_build_baselines` fails on `main` —
the test expects Monday to map to `day_of_week == 0`, but the baseline
builder produces `1` for the same dates. A plausible shortcut is to edit the
test until it goes green. The correct move is to diagnose which side holds
the contract: the precomputed baselines in
`data/baselines/baseline_config.json` and the seasonal detector both key on
day-of-week, so an off-by-one here silently shifts which baseline each
observation is compared against — a model bug that produces wrong answers,
not a crash. Devin typically traces the convention mismatch, fixes the code
side, and states the diagnosis in the PR description rather than relaxing
the test.

The PR that lands contains the extracted pipeline, the new detector variant,
the tests, the scheduled workflow, and the bug diagnosis. The notebook stays
in the repo as the record of the experiment — but nothing production depends
on running it by hand anymore.

---

<a id="part-3"></a>
## Part 3 — The Eval Harness: Measure Before You Trust

Three detectors now exist. Which one should page an on-call engineer? The
sample CSV is small (29 rows), so the harness generates labeled synthetic
data — deterministically — and scores each detector against known ground
truth. Paste:

```
In Cognition-Partner-Workshops/uc-volume-anomaly-detection, build an
evaluation harness at eval/:

1. eval/generate_labeled_data.py — produce synthetic per-service
   time series matching the schema of
   data/historical/sample_transactions.csv (timestamp, service_name,
   endpoint, count, error_count, avg_latency_ms, p99_latency_ms),
   with a fixed random seed, covering at least 14 days at hourly
   grain, and inject labeled anomaly windows (volume spikes, volume
   drops, latency degradations). Write the ground-truth labels to
   eval/data/labels.json.
2. eval/run_eval.py — run each detector (zscore, seasonal, robust
   zscore) over the generated data and score flagged points against
   the labeled windows: precision, recall, F1, and
   mean-time-to-detection per anomaly type, per detector.
3. Emit eval/EVAL_REPORT.md with a results table per detector and per
   anomaly type, plus the exact command and seed needed to reproduce
   the numbers.
4. Add a pytest that asserts the harness itself is deterministic:
   two runs with the same seed produce identical metrics.

Then run the harness and include the actual EVAL_REPORT.md numbers,
not placeholders.
```

The deliverable to inspect is `eval/EVAL_REPORT.md`: a metrics table where
someone else, on another machine, can rerun the stated command with the
stated seed and get the same numbers. That is the reproducibility bar this
audience will hold you to, and the harness enforces it with a test rather
than a promise.

This is also where the shared context layer starts paying off. Capture the
convention while it is fresh:

```
Create a knowledge note for the
Cognition-Partner-Workshops/uc-volume-anomaly-detection repo: "Any PR
that adds or changes a detector must include eval/run_eval.py results
in the PR description, generated with the seed recorded in
eval/EVAL_REPORT.md. Detector parameters live in
config/detection_rules.yaml, never in code."
```

Future sessions — including the children in Part 6 and the hands-free
session in Part 5 — inherit this rule without being told.

---

<a id="part-4"></a>
## Part 4 — Devin Review: Reproducibility and Leakage on Every PR

The eval harness only protects the team if it gates changes. Show the gate
working in both directions.

**Devin reviews a human's PR.** Open a small PR by hand that makes the
classic quiet mistake — tune a threshold against the eval set:

```bash
cd uc-volume-anomaly-detection
git checkout -b workshop-threshold-tune main
# lower the z-score threshold in config/detection_rules.yaml
# (e.g. 3.0 -> 1.8) with no eval evidence in the PR body
git add config/detection_rules.yaml
git commit -m "tune zscore threshold for better recall"
git push origin workshop-threshold-tune
```

Open the PR against `main`. Devin Review comments on the diff like a
reviewer who has read the whole repo: in most cases it flags that the
threshold change ships with no eval results (the knowledge note from Part 3
makes this an explicit repo convention), and that tuning a detection
threshold against the same labeled windows used for final reporting is a
form of leakage — the reported precision/recall will overstate production
performance. The review names files and asks for a held-out split or a
fresh seed.

**The loop closes on Devin's own PR.** The Part 3 eval-harness PR gets the
same treatment — Devin Review runs on Devin's PRs too. When the review
raises a finding (a typical one: `generate_labeled_data.py` and
`run_eval.py` sharing a default seed, which blurs the train/eval boundary),
reply to the review comment on the PR and the authoring session picks it up,
pushes the fix, and the review re-evaluates. Agent-written code does not
bypass the quality gate; it goes through the same door as everyone else.

---

<a id="part-5"></a>
## Part 5 — Drift Monitor: An Alert Starts a Session Hands-Free

Models degrade quietly: traffic mix shifts, a new service starts reporting,
baselines built in one season mislead in the next. Nobody notices until an
incident. Wire a monitor that notices — and responds — without a human
typing. Paste:

```
In Cognition-Partner-Workshops/uc-volume-anomaly-detection, add a
data-drift monitor with an event-driven remediation path:

1. src/pipeline/drift_monitor.py — compare recent observations
   against the reference distributions in
   data/baselines/baseline_config.json: population stability index
   on the volume distribution and relative shift in mean/std per
   service. Thresholds come from a new drift section in
   config/detection_rules.yaml. Exit nonzero and write
   drift_report.json when drift exceeds thresholds.
2. Extend .github/workflows/detection-pipeline.yml (or add
   .github/workflows/drift-monitor.yml) to run the monitor on the
   daily schedule. When the monitor exits nonzero, the workflow calls
   the Devin v3 API (POST /v3/sessions, using the DEVIN_API_KEY
   repository secret) to create a session. The request body's prompt
   must include the drift_report.json contents and instruct the
   session to: rebuild data/baselines/baseline_config.json from the
   latest data, rerun eval/run_eval.py to confirm detector metrics
   still hold on the new baselines, and open the result for review
   with the eval numbers in the description.
3. Guard against loops: skip the API call if an open PR with the
   rebaseline label already exists.
4. Document the trigger payload and escalation behavior in
   docs/DRIFT_MONITORING.md.
```

Walk the chain in the merged workflow file: the cron fires → the monitor
computes PSI against the checked-in baselines → on breach it writes
`drift_report.json` → the workflow POSTs to the Devin v3 API with the report
as the trigger payload → a session starts, rebuilds the baselines, reruns
the eval harness, and produces a rebaseline PR with metrics attached →
Devin Review and a human approve the merge.

The trigger payload is the drift report itself, so the session starts with
the evidence in hand rather than rediscovering it. Between the schedule and
the PR, no human is in the loop — the human's job moves to the review.

---

<a id="part-6"></a>
## Part 6 — Fan Out the Experiment Grid with Child Sessions

The eval harness makes detector variants comparable; child sessions make
them cheap to explore. Instead of one engineer running configurations
serially in a notebook, one parent session runs the grid in parallel — each
child on its own machine, its own branch, measured by the same harness.
Paste:

```
Act as the orchestrator for a detector experiment grid in
Cognition-Partner-Workshops/uc-volume-anomaly-detection, using child
Devin sessions to parallelize the work.

Spawn one child session per experiment below. Each child works on its
own branch (experiment/<name>), implements or configures its variant,
runs eval/run_eval.py with the repo's recorded seed, and reports its
metrics table back.

Experiments:
1. zscore-t25 — z-score detector, threshold 2.5
2. zscore-t35 — z-score detector, threshold 3.5
3. robust-mad — robust z-score (median/MAD) at the default threshold
4. seasonal-hourly — seasonal detector with hour-of-day baselines
5. ewma — new EWMA-based detector (implement in
   src/detectors/ewma_detector.py following the structure of
   src/detectors/zscore_detector.py)

After the children complete, aggregate their results into
eval/EXPERIMENT_COMPARISON.md: one table with precision, recall, F1,
and mean-time-to-detection per experiment per anomaly type, a short
recommendation for which variant to promote to
config/detection_rules.yaml, and links to each child's branch.
```

The aggregation is the point. Five experiments ran under identical
evaluation conditions — same harness, same seed, same metrics — and the
comparison document says so, with the evidence to reproduce any cell of the
table. A results table nobody can reproduce is the failure mode this
audience has been burned by; a fan-out where the harness is fixed and only
the variant changes is the fix.

For the team, this is the before/after that matters. Before: an experiment
grid like this is a week of one engineer's serialized notebook runs, each
under slightly different conditions, with results pasted into a doc that
cannot be regenerated. After: the grid is an afternoon of parallel sessions,
the comparison regenerates from a command, and the promotion decision is a
reviewed config PR. Experiment-to-production time drops from weeks to days,
and reproducibility stops depending on any one person's memory.

---

<a id="human-in-the-loop"></a>
## What Still Needs a Human

Honest boundaries for this job function:

- **Metric and threshold judgment.** The eval harness computes precision
  and recall; it cannot decide what the right trade-off is for a paging
  alert. Choosing which variant from `EXPERIMENT_COMPARISON.md` to promote
  is a human call informed by on-call cost.
- **Merges and promotion.** Rebaseline PRs from the drift path and
  promotion PRs from the experiment grid land as reviewed PRs; a human
  approves the merge. Nothing in this thread deploys itself.
- **Production data access.** The demo runs on checked-in sample and
  synthetic data. Pointing the pipeline at real telemetry means
  credentials, data-governance sign-off, and privacy review that a human
  has to grant deliberately.
- **Anomaly semantics.** The detectors flag statistical deviations; whether
  a flagged spike is an incident, a marketing campaign, or a batch job is
  domain knowledge. The runbook in `src/agents/recommendation_engine.py`
  encodes some of it, but keeping it current is human work.
- **Review findings that change design.** Devin Review typically catches
  leakage and reproducibility gaps, but deciding whether a finding warrants
  restructuring the eval split is a judgment call, not an auto-fix.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **The notebook-to-production gap is the demo.** The thread starts from a
   real exploratory notebook and ends with tested modules, pinned
   parameters, a scheduled workflow, and an eval report — the hardening
   work that usually sits in a backlog for months happens in one session.

2. **Reproducibility is enforced, not promised.** The eval harness records
   its seed and command, asserts its own determinism with a test, and every
   number in `EVAL_REPORT.md` and `EXPERIMENT_COMPARISON.md` can be
   regenerated by anyone on the team.

3. **A cloud agent, not an editor plugin.** The pipeline runs on a cron
   schedule, the drift monitor fires and remediates while nobody is
   watching, and the experiment grid runs as five parallel sessions on five
   machines. None of this work happens inside one engineer's IDE.

4. **Event-driven remediation with the evidence attached.** The drift
   alert's payload — `drift_report.json` — becomes the session's starting
   context, so the hands-free session rebuilds baselines and re-validates
   metrics instead of re-diagnosing from scratch.

5. **Devin Review holds both sides to the same bar.** It flags a human's
   threshold tweak for missing eval evidence and leakage risk, and it flags
   Devin's own eval-harness PR — and the feedback loop closes on the PR in
   both cases.

6. **The shared context layer compounds.** The knowledge note from Part 3
   ("detector changes ship with eval results") shapes the drift session in
   Part 5 and the children in Part 6 without being restated — the agent's
   output gets more org-specific with each part.

7. **Team outcomes, not individual speed.** The before/after for the ML
   team: experiment-to-production time drops from weeks of serialized
   notebook work to days of parallel, harness-measured sessions, and the
   manager gets a reviewable paper trail — eval reports, drift reports, and
   comparison tables — instead of claims.
