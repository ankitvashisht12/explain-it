---
name: explain-it
description: Generate beginner-calibrated, self-contained HTML explainer sites for any codebase change or subsystem — a root primer page plus one page per topic, all linked by a shared sidebar. Use whenever the user asks to "explain this code/PR/diff/bug/architecture in simple terms", wants an "explainer", "walkthrough", "guide for someone with no context", "onboarding doc", or says "I have no context of this, explain like I'm a beginner". Also use when a reviewer wants a visual before/after story of a change instead of raw diffs.
license: MIT
metadata:
  author: ankitvashisht
  version: "1.0.0"
---

# explain-it

Turn code you have investigated into a small static HTML site that a smart
reader with **zero context on this codebase** can follow: a root page that
builds the mental model, one page per topic, and a sidebar connecting them.

The bar to hit: *"simple language that is not too simple and not too
difficult."* Never talk down; never assume repo knowledge.

## Workflow

### 1. Investigate before you explain

Never explain code you have not read. For every topic, read the actual
source: the changed lines, the enclosing function, and at least one caller,
so you can answer "when does this code even run?" with a true story. If a
claim came from someone else (a review comment, an issue), verify it against
the code first and say so if it doesn't hold.

### 2. Plan the site

- **One root page** (`index.html`): the primer. It builds the reader's
  mental model of the system in ~2 minutes and links to every topic page.
- **One page per topic** (`01-<slug>.html`, `02-<slug>.html`, …): each page
  explains one change, bug, decision, or subsystem. 3–7 pages is the sweet
  spot; merge topics rather than exceed ~9.
- **A shared sidebar** on every page listing all pages in reading order,
  with the current page highlighted. Pages are plain files linked with
  relative `<a href>` — the site must work opened directly from disk
  (file://), so no routers, no build step, no external JS.

Ask the user where to put the folder only if no location is obvious;
otherwise create a sensibly named folder next to the work (e.g.
`docs/explainers/<topic>/`) and say where you put it.

### 3. Write each page

Follow `references/writing-guide.md` for the method — read it before writing
your first page. The non-negotiables:

- **Primer first.** The root page introduces every domain concept the topic
  pages need, each with one concrete metaphor (e.g. "a release is a
  photocopy; a running job pins one photocopy"). Introduce a metaphor once,
  then reuse
  it verbatim everywhere. Never stack two metaphors for one concept.
- **Ground every topic in runtime.** Each topic page opens with a
  "When does this code even run?" story: 2–4 actor beats (User does X →
  System runs function Y → the tiny job inside it). Real function names in
  code font, alongside — never instead of — the plain-language story.
- **Show the change as a diff.** Old vs new code in red/green diff blocks
  with the file path in the header, trimmed to the crux, with 1–3 inline
  comments pointing at *why* — not narrating *what*.
- **Quantify the "why".** "Slow" is banned; "hundreds of KB per request,
  ~99% of the time for nothing" is the standard. Same for risk: name the
  concrete input/state and the wrong outcome.
- **Separate bugs from decisions.** If something is a defensible product
  choice rather than a defect, present it as a decision with the trade-off,
  not as a mistake.
- **End the root page with a scoreboard**: a status table of every item
  (done / proposed / parked) with its location in the codebase.

### 4. Build the HTML

Copy `references/site-template.html` as the starting point for every page —
it contains the design tokens, the sidebar, and one working example of every
component (story block, callout, diff block, status table, pills). Then:

- Keep each page fully self-contained (all CSS inline in the page). The
  sidebar is duplicated into each page with the current item marked
  `class="on"`.
- Follow `references/design-system.md` for tokens and component rules. Keep
  the palette, spacing, and type scale as given so multi-page sites look
  like one system. The template ships a light/dark toggle and a collapsible
  sidebar — carry both into every page rather than dropping them.
- No external dependencies except the Google Fonts link already in the
  template (the font stack falls back cleanly offline) and no JS beyond the
  template's theme-toggle snippet.

### 5. Verify before handing over

Open or render the root page and at least one topic page (or re-read them)
and check: every sidebar link resolves, every code identifier mentioned in
prose exists in the codebase with that exact spelling, every diff compiles
conceptually against the real file, and the scoreboard statuses are current.
Then tell the user the folder path and open the root page for them if the
environment allows.

## Common pitfalls

- Explaining from memory of the conversation instead of the code — the
  explainer inherits every stale claim. Re-read the source.
- Metaphor drift: calling it "photocopy" on one page and "frozen copy" on
  the next. One term, everywhere.
- Diff blocks that paste 60 lines verbatim. Trim to the crux; keep 2–4
  context lines around it.
- Prose that narrates the diff line by line. The diff shows *what*; prose
  only carries *why it matters* and *when it runs*.
- A sidebar that only exists on the index page. Every page carries the full
  sidebar.
- Baby-talk ("the computer remembers things in a special box"). The reader
  is smart — they just don't know *this* codebase.
