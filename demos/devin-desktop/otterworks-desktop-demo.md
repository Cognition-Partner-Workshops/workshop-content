# Devin Desktop: Visual Feature Development on OtterWorks

A linear demo showcasing **Devin Desktop** — the real-time browser and GUI
interaction capability — by building a dark mode feature on the OtterWorks
collaborative platform. The audience watches Devin start the application,
navigate the UI, implement the feature, and visually verify it works across
multiple pages — all on the live Desktop.

## Table of Contents

- [Why This Demo](#why-this-demo)
- [Prerequisites](#prerequisites)
- [Part 1 — Start the App and Show the Current State](#part-1)
- [Part 2 — Build Dark Mode Live](#part-2)
- [Part 3 — Visual Verification on Desktop](#part-3)
- [Alternative Prompts](#alternative-prompts)
- [Key Takeaways](#key-takeaways)

---

<a id="why-this-demo"></a>
## Why This Demo

Devin Desktop is most valuable when a task requires **visual judgment** — things
that can't be verified by unit tests alone:

| Scenario | Why Desktop Matters |
|----------|-------------------|
| UI feature development | Must see the rendered output to confirm correctness |
| Visual bug fixes | Need to identify broken layouts, wrong colors, overlapping elements |
| Responsive design | Requires resizing the browser and checking breakpoints |
| E2E testing | Click through flows, verify transitions, check loading states |
| Accessibility audits | Navigate with keyboard, inspect focus rings, verify contrast |

This demo uses **dark mode** because the change is immediately visible and
universally understood — the entire app switches color scheme, and the audience
can watch it happen in real time.

---

<a id="prerequisites"></a>
## Prerequisites

- The [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks)
  repo connected to the Devin organization
- Node.js 20+ available in the Devin environment (included in the repo snapshot)
- ~5 GB RAM for the Next.js dev server

No backend services are required — the web-app runs standalone with its Next.js
dev server (API calls will 404 gracefully; the UI chrome renders fully).

---

<a id="part-1"></a>
## Part 1 — Start the App and Show the Current State

Open a new Devin session on the `otterworks` repo. Paste the following prompt:

```
In the otterworks repo, start the Next.js web-app dev server
(frontend/web-app) and open http://localhost:3000 in the browser.
Navigate to the login page, then to the dashboard, and take note of
the current light-only color scheme. I want to see the app running on
your Desktop.
```

**What the audience sees on Desktop:**

1. Devin installs dependencies (`npm install` in `frontend/web-app`)
2. Devin starts the dev server (`npm run dev`)
3. Chrome opens to `localhost:3000` — the OtterWorks landing page appears
4. Devin navigates to `/login`, then `/dashboard`
5. The app renders in its default light theme (white backgrounds, gray borders,
   otter-blue accents)

This establishes the "before" state that the audience will compare against.

---

<a id="part-2"></a>
## Part 2 — Build Dark Mode Live

Once the app is running, paste the feature prompt:

```
Add dark mode support to the OtterWorks web-app (frontend/web-app):

1. Enable Tailwind's class-based dark mode in tailwind.config.ts
2. Create a theme store (zustand) that persists the user's preference
   to localStorage and respects the system preference as default
3. Add a dark/light toggle button to the sidebar (bottom section,
   near the Settings link) using a Sun/Moon icon from lucide-react
4. Update the AppShell layout, Sidebar, and top header bar to use
   dark: variants (dark backgrounds, light text, adjusted borders)
5. Update the dashboard page stat cards and storage bar for dark mode
6. After making changes, refresh the browser on Desktop, toggle dark
   mode ON, and navigate to Dashboard, Files, and Settings to verify
   the dark theme renders correctly on each page

Keep changes focused — only update the layout shell, sidebar, header,
and dashboard. Don't refactor other pages yet.
```

**What the audience sees on Desktop:**

1. Devin edits `tailwind.config.ts` — adds `darkMode: "class"`
2. Devin creates a theme store with `zustand` (similar to the existing
   `ui-store.ts` pattern)
3. Devin adds the toggle icon in the sidebar component
4. Devin adds `dark:` utility classes to `app-shell.tsx`, `sidebar.tsx`, and
   the dashboard page
5. The dev server hot-reloads — Chrome shows the changes immediately
6. Devin clicks the toggle — **the entire app switches to dark mode**
7. Devin navigates to Dashboard → Files → Settings, confirming each page

The visual payoff is immediate: the audience watches the theme flip live.

---

<a id="part-3"></a>
## Part 3 — Visual Verification on Desktop

After the feature is built, Devin performs visual verification that would be
impossible with unit tests alone:

- **Toggle persistence:** Devin refreshes the page and confirms dark mode
  persists (localStorage)
- **System preference fallback:** Devin checks that with no stored preference,
  the OS dark-mode setting is respected
- **Navigation consistency:** Devin clicks through multiple routes to verify no
  page "flashes" white
- **Contrast check:** Devin inspects that text remains readable against dark
  backgrounds

Devin then opens a PR with the changes. The PR description includes screenshots
captured from the Desktop showing before/after states.

---

<a id="alternative-prompts"></a>
## Alternative Prompts

These prompts showcase different Desktop strengths with the same repo. Use them
as follow-up demos or substitutes depending on audience interest.

### Visual Bug Fix (responsive layout)

```
In the otterworks web-app (frontend/web-app), start the dev server and
open http://localhost:3000/dashboard in the browser. Resize the browser
window to 768px wide (tablet breakpoint). The stat cards grid and
sidebar overlap on tablet — identify the layout issue visually, fix the
responsive breakpoints in the AppShell and dashboard page, then resize
back and forth between mobile/tablet/desktop to verify the fix.
```

### Keyboard Shortcuts Modal

```
In the otterworks web-app (frontend/web-app), add a keyboard shortcuts
help dialog:

1. Create a modal component that shows a grid of shortcuts (Ctrl+K for
   search, Ctrl+N for new document, Ctrl+/ for help, etc.)
2. Wire Ctrl+/ to open/close the modal globally
3. Start the dev server, open the browser on Desktop, and press Ctrl+/
   to verify the modal appears with proper styling and can be dismissed
   with Escape or clicking outside
```

### Accessibility Audit via Desktop

```
In the otterworks web-app (frontend/web-app), start the dev server and
run a manual accessibility audit using the browser:

1. Navigate to /login and tab through all interactive elements — verify
   focus rings are visible on every focusable element
2. Navigate to /dashboard and check that all images have alt text and
   all buttons have accessible labels
3. Open Chrome DevTools > Lighthouse > Accessibility and run an audit
4. Fix any issues found (missing labels, low contrast, missing alt
   text) and re-run to verify the score improves

Show me the before/after Lighthouse scores on Desktop.
```

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **Desktop enables visual verification** — dark mode, responsive layouts, and
   accessibility checks require human-like visual judgment that no unit test
   captures.

2. **Real-time observation builds trust** — the audience watches Devin work in
   the browser, click buttons, resize windows, and verify output. There's no
   "trust me, it works" — you see it happen.

3. **Hot-reload + Desktop = tight feedback loop** — Devin edits code, the dev
   server hot-reloads, and the result is immediately visible in Chrome. No
   manual rebuild step.

4. **Desktop complements code-only workflows** — most Devin tasks don't need
   Desktop. But UI development, visual QA, and browser-based debugging are
   qualitatively different tasks where seeing the screen is essential.

5. **Screenshots in PRs** — Devin captures what it sees on Desktop and embeds
   screenshots in the PR description, giving reviewers visual proof without
   running the app themselves.
