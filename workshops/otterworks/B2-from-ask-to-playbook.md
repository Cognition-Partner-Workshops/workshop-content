# Lab: From Ask to Playbook

## Objective

Experience the Devin workflow progression: start with an ad-hoc ask, refine it into a repeatable session, then codify the methodology as a Devin Playbook that anyone on your team can run with consistent results.

## Why This Matters

Most teams start with Devin by typing one-off requests. That works, but the real value compounds when you codify successful patterns into Playbooks. A Playbook encodes institutional methodology — the same senior-engineer-quality approach runs every time, for every team member, without re-explaining context.

This lab uses the OtterWorks incident investigation flow (from B1) as the worked example, but the pattern applies to any repeatable task.

## The Progression

### Step 1 — Ad-Hoc Ask (5 min)

Open Devin and paste this prompt:

```
Look at the OtterWorks search-service code in
services/search-service/app/api/search.py. The suggest
endpoint is returning 500 errors. Find the bug and fix it.
```

Observe: Devin finds and fixes the bug, but the approach is ad-hoc. It may or may not check the right logs, run the right tests, or follow your team's incident response process.

### Step 2 — Refined Session with Context (10 min)

Now paste a more structured prompt that encodes your team's methodology:

```
Investigate a production incident in the OtterWorks
search-service. Follow this process:

1. Read the runbook at docs/runbooks/search-suggest-500.md
2. Check the chaos flag status in the service code
3. Trace the request flow from the API endpoint through
   to the MeiliSearch client
4. Identify the root cause with specific file and line
5. Implement a fix that handles the error gracefully
6. Run the service tests to verify: cd services/search-service
   && python -m pytest
7. Document the root cause and fix in the PR description
```

Observe: The output is more consistent and follows your process. But you had to write all that context every time.

### Step 3 — Codify as a Playbook (20 min)

Now create a Devin Playbook that encodes this methodology permanently:

1. Go to your Devin settings and create a new Playbook
2. Name it: `otterworks-incident-investigation`
3. Write the Playbook steps based on what worked in Step 2:

```
name: OtterWorks Incident Investigation
description: Investigate and fix a production incident in the
  OtterWorks platform following SRE methodology.

steps:
  - Read the relevant runbook in docs/runbooks/ for the
    affected service
  - Check if a chaos flag is active by searching for
    chaos-related Redis keys in the service code
  - Trace the request flow from the API gateway through
    to the backend service
  - Identify the root cause with specific file, function,
    and line number
  - Implement a fix that handles the error gracefully
    (not just removing the chaos flag)
  - Run the affected service's test suite to verify
  - Check related services for cascading effects
  - Include root cause analysis in the PR description
```

### Step 4 — Run the Playbook (10 min)

Invoke your new Playbook on a different scenario:

```
Run playbook: otterworks-incident-investigation

Incident: The notification-service is returning 400 errors
on every API call. The health endpoint is healthy but
functional endpoints reject requests.
```

Observe: Devin follows the same methodology you codified — reads runbooks, traces flows, tests fixes — without you re-specifying the process.

## What Done Looks Like

- [ ] Completed Step 1: ad-hoc ask produced a fix but with inconsistent methodology
- [ ] Completed Step 2: refined prompt with explicit process produced better, more consistent output
- [ ] Completed Step 3: created a Devin Playbook encoding the investigation methodology
- [ ] Completed Step 4: ran the Playbook on a different incident and got consistent results
- [ ] Can articulate the value difference: one-off ask vs. repeatable Playbook

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

If you did B1 first, you already know the chaos scenarios. Use the search-suggest-500 scenario for Steps 1-2 since you know the expected root cause. For Step 4, try the notification processing failure scenario.

### Hint 2 — Playbook Tips

Good Playbooks are specific about the process but generic about the input. Don't hardcode "search-service" — write steps that work for any service. Include verification steps (run tests, check related services) that humans often skip under pressure.

### Hint 3 — The Automation Angle

After creating the Playbook, discuss: this Playbook could be triggered automatically by the same Grafana alert webhook from B1. Instead of a raw prompt, the webhook invokes the Playbook with the incident details as input. That is the Ask → Session → Playbook → Automation progression.

## Key Takeaways

- Ad-hoc asks work but don't scale — every team member writes a different prompt and gets different quality
- Playbooks encode institutional methodology — the senior engineer's approach runs every time
- The progression is: try it once (ask) → refine it (session) → codify it (playbook) → automate it (scheduled/event-driven)
- Playbooks are a one-time investment that compounds across every session in the org

## Time Budget

- ~45-60 minutes for all 4 steps
- Steps 1-2 are fast (~15 min); Step 3 (writing the Playbook) takes the most thought
- Advanced participants: connect the Playbook to the B1 webhook for full automation
