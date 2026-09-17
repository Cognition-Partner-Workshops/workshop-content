# UI Delivery and Visual Verification — Frontend & Mobile Demo

A single linear demo showing Devin doing **visual** work: running a React admin
app in a browser on its own VM, measuring an accessibility baseline with axe,
fixing icon-only controls and contrast failures, attaching before/after
screenshots to the PR, wiring the whole check into a PR-triggered CI gate, then
fanning the same design-system fix across an Angular app, a second Angular app,
and a Flutter mobile app with child sessions. Devin Review sits on both sides of
the loop — reviewing a human's UI PR and closing feedback on Devin's own.

The through-line: front-end quality work is *measurable*. The claims in this demo
are numbers the reader can reproduce — violation counts, contrast ratios, node
counts — not "it looks right."

<a id="toc"></a>
## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Orient and Measure the Baseline](#part-1)
- [Part 2 — Fix the Defect, Prove It Visually](#part-2)
- [Part 3 — The Accessibility Pass (WCAG 2.1 AA)](#part-3)
- [Part 4 — Wire the Gate to a PR Event](#part-4)
- [Part 5 — Devin Review on UI Pull Requests](#part-5)
- [Part 6 — Fan the Design-System Change Across Apps](#part-6)
- [Part 7 — The Mobile Leg: Flutter on an Emulator](#part-7)
- [What Visual Verification Catches — and What It Misses](#limits)
- [What Still Needs a Human](#human-in-the-loop)
- [Outcomes the Team Measures](#outcomes)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Run the primary app locally so the reader can see what Devin is looking at:

```bash
cd ts-react-coreui-admin
npm install          # 311 packages, no peer-dep flags needed
npm start            # Vite dev server on http://localhost:3000
```

The app uses hash routing: the dashboard is at `http://localhost:3000/#/dashboard`
and the login page at `http://localhost:3000/#/login`.

Prerequisites: Node 22 and npm 10. The repo has an ESLint 9 config
(`npm run lint`) but **no test script and no accessibility tooling** — Part 1
creates that harness as the demo's first artifact.

---

<a id="repositories"></a>
## Repositories

- [ts-react-coreui-admin](https://github.com/Cognition-Partner-Workshops/ts-react-coreui-admin) —
  React 19.2 + CoreUI React 5.9 admin template built with Vite 7. 49 view files
  under `src/views/` and 13 shared components under `src/components/`
  (`AppHeader.js`, `AppSidebar.js`, `AppSidebarNav.js`, …). Styling lives in
  `src/scss/style.scss`. This is the primary app for the defect, the a11y pass,
  and the CI gate.
- [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular) —
  Angular 16.2 frontend for Spring PetClinic; 23 components across owners, pets,
  pet types, specialties, vets, and visits. `npm start` serves on port 4200.
- [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld) —
  Angular 21.1 RealWorld client with Vitest, Playwright (18 E2E specs/helpers),
  and existing workflows (`.github/workflows/lint.yml`, `playwright.yml`).
  Design tokens are declared as CSS custom properties in `src/styles.css`.
- [ts-dart-flutter-ecommerce](https://github.com/Cognition-Partner-Workshops/ts-dart-flutter-ecommerce) —
  Flutter e-commerce UI kit, Dart SDK constraint `>=2.19.0 <3.0.0`, 33
  `*page.dart` screens under `lib/screens/`, shared style constants in
  `lib/app_properties.dart`, and theme setup in `lib/main.dart`. Android
  application ID `com.example.flutter_ecommerce_template`. This is the mobile
  leg — it builds and runs on an emulator with no Android config changes.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Before (`main`) | After (Devin's branches) |
|---|---|---|
| **Accessibility** | Dashboard fails 8 axe rules across 84 nodes at WCAG 2.1 A/AA; no a11y tooling in the repo at all | Icon-only controls named, images described, chart canvases labeled, contrast pairs corrected; scan re-run and the count reported in the PR |
| **Verification** | No test script; UI correctness is a human eyeballing a browser | Playwright + `@axe-core/playwright` harness plus screenshot baselines, runnable locally and in CI |
| **Trigger** | Nothing runs on a UI PR except lint | `ui-quality-gate.yml` runs the scan on every PR and calls the Devin API when it regresses |
| **Scope** | One app, one engineer, one browser | The same token/a11y fix applied across two Angular apps and a Flutter app by child sessions |

The loop is: **run the app → measure → change → re-measure → attach the evidence
to the PR.** A UI change is "done" when the after-scan and the after-screenshot
are both in the PR, not when the diff looks plausible.

This is work an IDE assistant structurally cannot do. It needs a machine that
installs dependencies, starts a dev server, drives a real browser, boots an
emulator, and keeps doing it after the engineer closes their laptop — and in
Part 4 it stops being started by a human at all.

---

<a id="part-1"></a>
## Part 1 — Orient and Measure the Baseline

Open a Devin session on the repo and ask it to map the UI surface and then build
the measurement harness. There is nothing to measure with yet, so step one
creates it.

```
Using the Cognition-Partner-Workshops/ts-react-coreui-admin repo,
map the UI surface: the routes declared in src/routes.js, the
layout in src/layout/DefaultLayout.js, and the 13 shared
components in src/components/ (AppHeader.js, AppSidebar.js,
AppSidebarNav.js and friends). Report as a markdown table of
route -> view file -> shared components used, and note which
components render icon-only controls with no visible text label.
```

Devin typically returns the route table in a few minutes and flags the icon-only
controls in `src/components/AppHeader.js` — the sidebar toggler at lines 51-56
and the bell / list / envelope navigation links at lines 72-84 — as the
suspicious ones. Coverage depends on repo structure; DeepWiki over the repo is
what makes this fast on a codebase Devin has never seen.

Now have Devin stand the app up and measure it:

```
In ts-react-coreui-admin, add a UI verification harness under a
new tests/ui/ directory:

1. Add @playwright/test and @axe-core/playwright as devDependencies
   and an "test:ui" npm script.
2. Add tests/ui/a11y.spec.js that starts against the Vite dev
   server (npm start, http://localhost:3000), visits #/dashboard
   and #/login, runs AxeBuilder with tags wcag2a and wcag2aa, and
   writes the full violation JSON to tests/ui/reports/.
3. Add tests/ui/visual.spec.js that captures full-page screenshots
   of #/dashboard and #/login at 1280x720 and 375x667 into
   tests/ui/baselines/.
4. Run it and report a markdown table of violation id, impact, and
   node count for each route.
```

The baseline Devin reports on `main` today:

| Route | Result |
|---|---|
| `#/login` | 0 violations |
| `#/dashboard` | 8 violation ids, 84 affected nodes |

| Axe rule | Impact | Nodes | What it is |
|---|---|---:|---|
| `aria-progressbar-name` | serious | 31 | dashboard progress bars with `role="progressbar"` and no accessible name |
| `color-contrast` | serious | 13 | four distinct foreground/background pairs below AA |
| `listitem` | serious | 10 | list items outside a list parent |
| `role-img-alt` | serious | 9 | nine chart `<canvas role="img">` elements with no name |
| `button-name` | critical | 7 | `.sidebar-toggler`, `.header-toggler`, 5 card dropdown buttons |
| `image-alt` | critical | 7 | header avatar plus six table avatars |
| `link-name` | serious | 5 | the icon-only header nav links |
| `scrollable-region-focusable` | serious | 1 | scroll container not keyboard reachable |

The contrast failures are specific, which matters for the fix:

| Foreground | Background | Ratio | AA requires | Surface |
|---|---|---:|---:|---|
| `#ffffff` | `#f9b115` | 1.85:1 | 4.5:1 | `.bg-warning.card.text-white` |
| `#ffffff` | `#3399ff` | 2.94:1 | 4.5:1 | `.card.text-white.bg-info` |
| `#75787f` | `#212631` | 3.42:1 | 4.5:1 | sidebar `.nav-title` |
| `#ffffff` | `#e55353` | 3.68:1 | 4.5:1 | `.card.text-white.bg-danger` |

That table is the demo's spine. Everything after this is measured against it.

---

<a id="part-2"></a>
## Part 2 — Fix the Defect, Prove It Visually

Take the highest-impact defect first: the seven `button-name` and five
`link-name` nodes. A keyboard or screen-reader user reaches the header, hears
"link", and has no idea whether it opens messages or notifications. It is
invisible to a sighted mouse user, which is exactly why it survived 49 views.

```
In ts-react-coreui-admin, fix the critical and serious
accessible-name violations the harness reported on #/dashboard:

- src/components/AppHeader.js: the sidebar toggler (around lines
  51-56) and the icon-only bell, list, and envelope nav links
  (around lines 72-84) need accessible names.
- The header avatar and the six table avatar images need alt text
  that identifies the user, not decorative filler.
- The nine chart canvases rendered with role="img" need accessible
  names describing the data shown.
- The dashboard progress bars need accessible names.

Follow the existing CoreUI component conventions in the file - use
CoreUI props where they exist rather than raw DOM attributes. Then
re-run npm run test:ui and report the before/after violation table,
and attach the #/dashboard screenshots at 1280x720 and 375x667 from
tests/ui/baselines/ so the visual result is reviewable.
```

Devin runs the app on its own VM, applies the fix, re-runs the scan, and puts
three things in the PR body: the before table, the after table, and the
screenshots. The screenshots are the part a front-end reviewer actually looks at
first — they answer "did adding names to icon buttons change the layout?" in one
glance, and the answer needs to be no.

The verification beat to watch for: a plausible first cut adds
`aria-label="Notifications"` to a wrapper `<div>` rather than to the focusable
element. The scan does not go green — `link-name` still reports on the
`.nav-link[href="#"]` targets, because axe evaluates the accessible name of the
*interactive* node. Devin moves the label onto the link, re-runs, and the rule
clears. A visual review would have passed the first cut; the automated scan did
not.

> The screenshot pair proves the layout is unchanged. The axe delta proves the
> behavior changed. Neither one alone is sufficient, which is the whole argument
> for running both in the same loop.

---

<a id="part-3"></a>
## Part 3 — The Accessibility Pass (WCAG 2.1 AA)

Named standard, named rule set, measured result. The remaining categories are
contrast and structure, and contrast is where a design-system decision hides:
the failing pairs are CoreUI theme colors (`#f9b115` warning, `#3399ff` info,
`#e55353` danger) used as card backgrounds under white text. Overriding one card
does not fix it; the token has to change, or the text on that token has to.

```
In ts-react-coreui-admin, close the remaining WCAG 2.1 AA
violations on #/dashboard reported by npm run test:ui.

Contrast (13 nodes, four pairs):
- #ffffff on #f9b115 (1.85:1) on .bg-warning card surfaces
- #ffffff on #3399ff (2.94:1) on .bg-info card surfaces
- #ffffff on #e55353 (3.68:1) on .bg-danger card surfaces
- #75787f on #212631 (3.42:1) on sidebar .nav-title

Fix these in src/scss/style.scss as theme-level overrides - define
the corrected values as SCSS variables in one place rather than
patching individual views, so the 49 views under src/views/ inherit
the fix. Keep the brand hues recognizable; darken or lighten only
as far as AA requires and record the resulting ratio for each pair.

Also fix the listitem and scrollable-region-focusable violations.

Produce ACCESSIBILITY.md at the repo root documenting the standard
(WCAG 2.1 level AA), the tooling (axe-core via Playwright, tags
wcag2a and wcag2aa), the before/after violation table, and the
rules that automated scanning does not cover.
```

Two things make this a design-system change rather than a patch: the fix lands in
`src/scss/style.scss` as variables, and `ACCESSIBILITY.md` becomes the written
standard the next session inherits. Promote that standard into a **Knowledge
note** — "UI changes ship with an axe scan at WCAG 2.1 AA and before/after
screenshots; theme colors are corrected at the token level, never per view" — and
every future session on any front-end repo starts from that bar instead of
rediscovering it. Wrap the whole procedure in a **playbook** so it is one command
next time.

---

<a id="part-4"></a>
## Part 4 — Wire the Gate to a PR Event

So far a human started every session. Now the trigger moves to CI, and the
harness stops being something a developer remembers to run.

```
Create .github/workflows/ui-quality-gate.yml in
ts-react-coreui-admin:

Trigger: pull_request (opened, synchronize) on branches targeting
main, skipped when the PR author is devin-ai-integration[bot].

Job steps:
1. npm install, then npm run build and serve the built app.
2. Run the tests/ui harness: the axe scan on #/dashboard and
   #/login, and the screenshot capture at 1280x720 and 375x667.
3. Compare the axe results against a committed baseline file
   tests/ui/reports/baseline.json and the screenshots against
   tests/ui/baselines/. Upload the diff images as workflow
   artifacts.
4. Post a PR comment with a table of new violations by rule id and
   impact, plus links to the screenshot diff artifacts.
5. If there are new violations or visual diffs, and Devin has made
   fewer than 2 fix attempts on this PR, call the Devin API to
   create a session on the same head branch. Pass the PR number,
   head SHA, branch name, the axe violation JSON, and the artifact
   URLs in the prompt.
6. If violations persist after 2 attempts, open a GitHub Issue
   labeled accessibility and needs-human-review instead of calling
   Devin again.

Document the payload, the bot-loop guard, and the escalation policy
in docs/UI_QUALITY_GATE.md.
```

**The payload.** When a PR is pushed, the gate hands Devin the head branch, the
head SHA, the axe violation JSON (rule id, impact, and the failing target
selectors), and the URLs of the screenshot diff artifacts. That is enough for the
session to check out the branch, reproduce the failure locally against the same
selectors, fix it, re-run the harness, and push a commit — with no human in the
loop from PR push to fix commit.

**The closed loop.** The fix commit fires `synchronize`, the gate runs again, and
either the comment reports zero new violations or the attempt counter increments.
Two failed attempts and it stops calling Devin and files an issue for a human.

This is the part that is hard to do from an editor: it runs on a pull request
opened at 11pm by someone else, on a branch nobody has checked out.

---

<a id="part-5"></a>
## Part 5 — Devin Review on UI Pull Requests

Devin Review works both directions here, and front-end teams get an unusual
amount out of it because design-system drift is a review-time judgment that
reviewers routinely skip.

**Devin reviewing a human's PR.** Open a small PR by hand that looks entirely
reasonable — a new dashboard card that hardcodes `background-color: #f9b115`
with white text and adds an icon-only refresh button:

```bash
cd ts-react-coreui-admin
git checkout -b workshop-ui-review-demo main
# add the card to src/views/dashboard/Dashboard.js, then:
git commit -am "feat(dashboard): add refresh card"
git push origin workshop-ui-review-demo
```

Devin Review comments on the diff with the two regressions a human reviewer
typically lets through: the hardcoded hex bypasses the SCSS theme variables
introduced in Part 3 (and reintroduces the 1.85:1 pair), and the icon-only button
has no accessible name — the exact rule the CI gate will fail on. The review
arrives before CI finishes, on a PR nobody assigned.

**The loop closing on Devin's own PR.** Go back to the Part 3 PR and leave a
review comment as a human reviewer:

```
The darkened warning token now reads closer to brown than to the
brand amber. Keep the AA ratio but shift the correction to the
text side instead - use the dark body color on the warning surface
rather than white, re-run the scan, and post the new ratio and an
updated #/dashboard screenshot.
```

Devin pushes a follow-up commit with the corrected token, the new measured ratio,
and a fresh screenshot in the same thread. Review feedback on a UI change is
usually subjective; here it resolves against a number.

---

<a id="part-6"></a>
## Part 6 — Fan the Design-System Change Across Apps

One app is a fix. The same fix across a portfolio is the reason a team pays for
an agent. Run one orchestrator session that spawns a child per repo.

```
Act as the orchestrator for a UI accessibility and design-token
sweep across four Cognition-Partner-Workshops front-end repos,
using child Devin sessions so the repos are worked in parallel.

Spawn one child session per repo below. Give each child its own
branch, and tell it to follow the standard recorded in
ts-react-coreui-admin/ACCESSIBILITY.md: WCAG 2.1 AA, axe-core (or
the platform equivalent), fix theme colors at the token level, and
report a before/after violation table plus screenshots.

1. petclinic-angular (Angular 16, npm start on port 4200) - audit
   the 23 components under src/app/, starting with the list views
   (owners, pets, pettypes, specialties, vets, visits). Replace the
   hardcoded color in src/app/app.component.css with a shared
   token, and check the logo images in
   src/app/app.component.html for alt text.
2. ts-angular-realworld (Angular 21, bun run start on port 4200) -
   add alt text to the author images in
   src/app/features/article/components/article-meta.component.ts
   and article-comment.component.ts, and convert the click-handled
   <i> trash icon in article-comment.component.ts into a
   keyboard-focusable button with an accessible name. Extend the
   existing Playwright suite under e2e/ with the axe scan rather
   than creating a second harness. Consolidate remaining direct
   colors onto the CSS custom properties already declared in
   src/styles.css.
3. ts-react-coreui-admin - extend the Part 3 scan to the login and
   register views under src/views/pages/ and the theme views under
   src/views/theme/.
4. ts-dart-flutter-ecommerce (Flutter) - consolidate the direct
   Material colors in the ThemeData configured in lib/main.dart
   and the constants in lib/app_properties.dart into one token
   source, and add Semantics labels to the icon-only controls in
   lib/screens/main/components/custom_bottom_bar.dart. The app
   uses Semantics in only two places today
   (lib/screens/tracking_page.dart and
   lib/screens/product/view_product_page.dart).

Monitor the children until each reports a green scan on its branch.
Aggregate the results into a single markdown table of repo, rules
fixed, nodes remaining, and the token changes made, and list
any repo where the token fix could not be applied at the theme
level.
```

Four repos, three frameworks, one standard — and the standard came from a
Knowledge note and a document Devin wrote in Part 3, not from four separate
briefings. Each child works on its own branch, so nothing collides. The
aggregation matters as much as the fan-out: the orchestrator comes back with one
table a design-system owner can act on, including the repos where the fix could
*not* be centralized.

---

<a id="part-7"></a>
## Part 7 — The Mobile Leg: Flutter on an Emulator

Mobile is where "the agent has its own machine" stops being an abstraction: the
loop needs an SDK, a Gradle build, a booted emulator, and something driving taps.

```
In Cognition-Partner-Workshops/ts-dart-flutter-ecommerce, verify
the design-token and Semantics change from the fan-out on a real
Android emulator.

Setup: install the Android SDK (platform-tools, emulator,
platforms;android-34, build-tools;34.0.0, and the
system-images;android-34;google_apis;x86_64 image), point
JAVA_HOME at a Java 17 JDK, create an AVD, and boot it headless.
This app is Flutter 3.7 / Dart 2.19 era - AGP 7.2.0, Gradle 7.5,
and Kotlin 1.7.10 as declared in android/build.gradle - so no
Android config changes should be needed.

1. Run flutter pub get, flutter analyze, and flutter test, and
   summarize the analyzer findings by file.
2. Build the debug APK (flutter build apk --debug) and install it
   on the emulator.
3. Launch com.example.flutter_ecommerce_template and walk the auth
   flow with adb input taps: the welcome/login screen
   (lib/screens/auth/welcome_back_page.dart), the register screen
   (lib/screens/auth/register_page.dart), the forgot-password
   screen (lib/screens/auth/forgot_password_page.dart), and the
   OTP screen (lib/screens/auth/confirm_otp_page.dart). Capture a
   screenshot at each step with adb exec-out screencap.
4. Dump the UI hierarchy with adb shell uiautomator on the
   bottom-bar screen and confirm the icon-only controls in
   lib/screens/main/components/custom_bottom_bar.dart now expose
   Semantics labels.
5. Report the emulator image and API level, the APK build time,
   the analyzer findings, and attach the screenshots.
```

What that looks like when it runs: a headless API 34 x86_64 emulator boots, the
debug APK builds in about a minute, `adb install -r` reports `Success`, and the
app lands on the "Welcome Back" screen. Four `adb shell input tap` commands later
there are four screenshots — login, register, forgot password, confirm OTP — and
`adb shell uiautomator dump` returns the accessible labels as text, which is how
the `Semantics` change is verified rather than assumed. `flutter analyze` reports
33 issues on `main` (deprecated `accentColor` / `headline6` / `bodyText1` usages,
unused imports, and a missing `flutter_lints` reference at
`analysis_options.yaml:10`) — a second, separable backlog the same session can
quantify while it is there.

Three honest notes for anyone reproducing this:

- Both Flutter repos pin Dart below 3.0 (`>=2.19.0 <3.0.0` here,
  `>=2.12.0 <3.0.0` for `ts-dart-flutter-grocery`), so they need a Flutter
  3.7-era SDK. A current Flutter release with Dart 3 fails dependency resolution
  before compiling anything.
- The x86_64 emulator needs KVM access on the host; without it the boot stops at
  `x86_64 emulation currently requires hardware acceleration`. Java 11 on
  `JAVA_HOME` also breaks the SDK command-line tools with a class-version error —
  Java 17 fixes it.
- `ts-dart-flutter-grocery` does **not** build for Android as-is: it declares
  Kotlin 1.6.0 / AGP 4.0.1 / Gradle 6.5.1 / `compileSdkVersion 31` while its
  plugins now pull Kotlin 1.8.x artifacts, so `:app:compileDebugKotlin` fails on
  a metadata version mismatch. That toolchain repair is real work and a
  legitimate session of its own — it is not part of this thread. Use the
  e-commerce app for the mobile leg.

The same session that changed the widget also installed the SDK, built the APK,
booted the emulator, tapped through four screens, and returned the pictures. An
engineer never opened Android Studio.

---

<a id="limits"></a>
## What Visual Verification Catches — and What It Misses

**Catches, reliably:** missing accessible names, missing alt text, contrast
ratios below threshold, unlabeled form controls, invalid ARIA, heading-order and
landmark problems, and unintended layout shifts at the captured viewports.
Screenshot diffs catch a component that moved, resized, or lost styling.

**Catches partially:** keyboard navigation. Automated scans confirm an element is
focusable and named; they do not confirm the tab order makes sense or that a
custom widget behaves the way a keyboard user expects.

**Does not catch:** whether the design is *right*. Automated a11y tooling
typically surfaces on the order of a third to a half of real WCAG issues — the
machine-checkable ones. Screen-reader announcement quality, focus management
after a route change, motion and vestibular concerns, cognitive load, brand
fidelity, and "this is technically AA but ugly" need a person. Screenshot
diffs are also noisy: font rendering, animation frames, and dynamic data produce
false positives, which is why the Part 4 gate posts the diff for a human rather
than blocking on it outright.

**Does not replace a device lab.** One emulator image is not the Android fleet,
and a headless Chromium screenshot is not Safari on an iPhone.

---

<a id="human-in-the-loop"></a>
## What Still Needs a Human

- **Design judgment.** Whether the corrected warning token is still on-brand is a
  design call. Devin can hold the ratio; it cannot decide the hue.
- **Approvals and merges.** Each artifact above lands as a PR. Merging into
  `main`, and any release to an app store or production CDN, stays with the team.
  Signing keys and store accounts are not given to the agent in this demo.
- **Real assistive-technology testing.** A screen-reader user's read-through, and
  physical-device testing, remain human work.
- **Ambiguous requirements.** When a ticket says "make the dashboard feel
  lighter," someone has to define what shipped means before an agent can verify
  it.
- **Escalation.** The Part 4 gate deliberately stops after two attempts and files
  an issue. Repeat failures are a signal, not something to retry into.

---

<a id="outcomes"></a>
## Outcomes the Team Measures

Frame the result the way a front-end lead reports it upward:

| Measure | Before | After |
|---|---|---|
| **Design-to-merge cycle time** | An a11y or token fix waits for an engineer with the app running locally; small UI debt sits in the backlog for weeks because it never outranks feature work | Triggered by the PR event; a fix commit is typically pushed while the original author is asleep, and the reviewer sees numbers and screenshots instead of a diff to imagine |
| **UI regression escape rate** | Design-system drift and missing accessible names ship because reviewers cannot see them in a diff; found later by an audit or a user | Caught on the PR that introduces them, by the same scan on every PR, with the failing selector named |
| **Audit readiness** | An accessibility audit is a project | `ACCESSIBILITY.md` plus a per-PR scan history is a standing record against a named standard |
| **Coverage across the portfolio** | One app gets attention; the other three drift | One orchestrator session applies the same standard to four repos in parallel and reports where it could not |

The unit of value is not "Devin wrote a component." It is that the front-end
team's quality bar became executable, ran without them, and applied across the
apps they own.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Devin does visual work.** It runs the app on its own VM, drives a real
  browser, scans the rendered DOM, captures screenshots at multiple viewports,
  and boots an emulator for the mobile leg. The evidence lands in the PR.
- **UI quality becomes a number.** The demo moves from "8 rules, 84 nodes, four
  contrast pairs below AA" to a measured after-state. That is what makes a UI
  change reviewable and a UI standard enforceable.
- **The trigger stops being a human.** `ui-quality-gate.yml` turns a pull request
  into an a11y and visual scan, and a regression into a Devin session on the same
  branch — with a bot-loop guard and escalation to a human issue after two
  attempts.
- **Devin Review is a design-system reviewer.** It flags hardcoded hex values that
  bypass theme tokens and icon-only controls with no accessible name on human
  PRs, and it closes the feedback loop on Devin's own.
- **One standard, many apps.** Child sessions apply the same WCAG 2.1 AA and
  token discipline across React, two Angular apps, and Flutter in parallel, and
  aggregate back into one table — including the repos where the fix could not be
  centralized.
- **The context layer is why it is org-specific.** `ACCESSIBILITY.md`, a Knowledge
  note carrying the quality bar, a playbook wrapping the procedure, and DeepWiki
  over each repo mean session number five starts where session number four ended.
- **Automated verification is a floor, not a ceiling.** It reliably catches the
  machine-checkable failures and frees the team to spend review time on the
  judgment calls — design fidelity, focus management, and real assistive-
  technology testing — that still need a person.
