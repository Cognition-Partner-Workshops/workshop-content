# Facilitator Guide

Guide for setting up and running a Hands-on Devin Workshop.

> **Quality standards:** All event content should follow the [Quality Checklist](quality-checklist.md). Review it before creating or editing event README files.

## Pre-Event Checklist (1 week before)

### Repos and Code
- [ ] Verify all required repos exist in `Cognition-Partner-Workshops` org
- [ ] Verify repos are set up on Devin's machine (check Devin admin panel)
- [ ] Clone repos locally to verify they're not broken
- [ ] Check for any new CVEs in repos used for security challenges (update descriptions if needed)
- [ ] Create any event-specific branches or GitHub Issues

### Runtime Resources
- [ ] Provision hosted application instances (if needed)
- [ ] Verify hosted apps are accessible and healthy
- [ ] Test credentials for hosted apps
- [ ] Document URLs and credentials in event README

### Participant Access
- [ ] Verify all participants have Devin account access
- [ ] Verify participants can access the GitHub org (if they need to view repos directly)
- [ ] Prepare event site with challenge instructions
- [ ] Send pre-event communication with setup instructions

### Facilitator Preparation
- [ ] Run through each selected challenge yourself (or with Devin) to identify gotchas
- [ ] Prepare "hint cards" for common stuck points
- [ ] Have backup challenges ready if primary ones are completed quickly
- [ ] Test screen sharing and presentation setup
- [ ] Review the [Quality Checklist](quality-checklist.md) against the event README
- [ ] If the event needs facilitator-specific pacing or recovery notes, create a `FACILITATOR_COMPANION.md` in the event directory (see [Quality Checklist § Facilitator Companion](quality-checklist.md#facilitator-companion))

## Day-of Checklist

### Setup (30 min before start)
- [ ] Verify hosted apps are running (health check endpoints)
- [ ] Open all relevant repos in browser tabs
- [ ] Prepare a Devin session (pre-loaded, ready to show)
- [ ] Test WiFi/network connectivity
- [ ] Set up screen for live Devin walkthrough

### Opening (15 min)
1. Welcome and introductions
2. Brief Devin overview (what it is, what it can do) — draw from [general themes](general-themes/) for positioning
3. Show the event site with challenge instructions
4. Explain the repo naming convention and where to find code
5. Point out the Devin Features checklist (Appendix)
6. Set expectations: "These are hands-on workshops, not exams"

### During the Workshop
- **Float and assist:** Walk around, check in on participants
- **Watch for:** Participants stuck on authentication, repo access, or environment issues
- **Encourage:** Trying different approaches, experimenting with Devin features
- **Remind:** Check the Devin Features checklist — try to discover features organically
- **Live walkthrough:** If many people are stuck on the same thing, do a quick walkthrough

### Common Issues and Solutions

| Issue | Solution |
|-------|---------|
| "Devin can't find the repo" | Verify repo is set up in Devin admin. Check repo name matches exactly. |
| "Devin is taking too long" | Normal for complex tasks. Show participants Session Insights to understand what Devin is doing. |
| "Devin made an error" | Use this as a teaching moment — show how to provide feedback to steer Devin. |
| "I don't know which challenge to pick" | Suggest starting with A1 (Linting) or D4 (UI Bug) — quick wins that build confidence. |
| "Can I use a different repo?" | Yes! The challenges list "recommended" repos but "Any" means any. Encourage experimentation. |
| "Devin's PR has conflicts" | Show how to ask Devin to resolve merge conflicts or rebase. |
| "The hosted app is down" | Fall back to local run instructions in runtime-resources.md. |

### Closing (15 min)
1. Ask participants to share their most interesting Devin session
2. Review the Devin Features checklist — who completed the most?
3. Collect feedback (survey link or verbal)
4. Share next steps: how to use Devin in their daily work
5. Remind them to revoke any PATs created during the workshop

## Post-Event

- [ ] Collect and review participant feedback
- [ ] Document any issues discovered during the workshop
- [ ] Update challenge modules if problems were found
- [ ] Run a final [Quality Checklist](quality-checklist.md) pass on the event README before archiving
- [ ] Archive event-specific resources (move event directory to `events/archive/`)
- [ ] Send follow-up email with:
  - Links to Devin documentation
  - Links to their PRs/sessions from the workshop
  - Contact info for questions

## Workshop Format Variations

### Lightning Walkthrough (1 hour)
- Facilitator-driven
- Show 2–3 challenges live
- Participants watch, then try one challenge in the remaining time
- Best for: executive audiences, first introductions

### Hands-on Half Day (4 hours)
- 15 min intro → 4-5 challenges → 15 min closing
- Mix categories: start easy (A1), build up (B1, C2), finish with participant choice
- Best for: technical teams, first workshops

### Full Day Deep Dive (8 hours)
- Morning: structured labs (guided)
- Afternoon: open exploration (participant choice)
- Include breaks, lunch, and discussion sessions
- Best for: dedicated enablement, teams adopting Devin

### Multi-Session Series
- Weekly 2-hour sessions over 4 weeks
- Each session covers one category
- Participants build on previous sessions
- Best for: ongoing enablement programs

## Proposing New Repos

If participants want to bring their own representative codebase:

1. Email brian.smitches@cognition.ai with:
   - Repo URL (or description if private)
   - What use case it would serve
   - License information
2. New repos will be evaluated against the [repo quality criteria](../catalog/repos.md)
3. Approved repos will be imported following the [naming convention](repo-naming-convention.md)
