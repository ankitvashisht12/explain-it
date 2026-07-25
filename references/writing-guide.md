# Writing guide — how to hit "not too simple, not too difficult"

The reader model: a smart engineer (or technical founder) who has **never
seen this codebase**. They can follow any concept you explain; they can
follow zero concepts you don't. Calibrate every sentence to that reader.

## Contents

1. [The primer page](#1-the-primer-page)
2. [Metaphor discipline](#2-metaphor-discipline)
3. [The "when does this run" story](#3-the-when-does-this-run-story)
4. [Diff blocks](#4-diff-blocks)
5. [Callouts](#5-callouts)
6. [The scoreboard](#6-the-scoreboard)
7. [Prose rules](#7-prose-rules)
8. [Page anatomy checklist](#8-page-anatomy-checklist)

## 1. The primer page

The root page has one job: after ~2 minutes of reading, the reader holds the
minimum mental model needed for every topic page. Build it as:

1. **What the system is, in one paragraph.** Name the product concept first,
   the internal name second: "a sequence of steps (internally called a
   *pipeline*)".
2. **The core mechanism as a story with a metaphor.** Example: "Releasing
   takes a *photocopy* of all the steps. Every job that starts gets a sticky
   note — a *pin* — saying 'I run on version 13'."
3. **The rule/invariant the topics revolve around**, stated in one bold
   sentence: "a running job must only ever read its pinned photocopy."
4. **A one-line bridge to the topic pages**: what broke or what's being
   explained, and links.

Everything a topic page needs must be introduced here. If a topic page needs
a concept no other page needs, introduce it at the top of that page in the
same style instead.

## 2. Metaphor discipline

- One metaphor per concept, introduced **once** (bold, in the primer),
  reused **verbatim** everywhere after. "Photocopy" never becomes "frozen
  copy", "snapshot", or "clone" in prose.
- Keep the real identifier attached at first use — the actual field name
  (say `releases.configBlob`) in code font next to "photocopy" — then prefer
  the metaphor in prose and the identifier in diffs. The reader must be able to grep the codebase for
  everything you name.
- Metaphors must be mechanically faithful. A photocopy is good for a
  snapshot because editing the original genuinely doesn't change the copy.
  Don't pick a metaphor that breaks under the very behavior you're
  explaining.
- Three metaphors per site is plenty. If you need seven, your scope is too
  broad — split the site.

## 3. The "when does this run" story

Abstract code becomes concrete the moment the reader can picture the runtime
moment it fires. Every topic page opens with an actor-beat story:

- 2–4 beats, each `WHO → what happens`. WHO is a small set you keep
  consistent across the site: **User**, **Server/Engine**, **Worker**
  (or this system's equivalents).
- The last beat zooms into the tiny job the page is about: "one tiny job
  inside it: decide whether the label shows the human-readable name
  ('Español') instead of the raw code ('es')."
- Name the real entry-point function in a code font in the story
  ("runs a function called `handleRequest` — the front door for every
  incoming request"). Frequency/heat is part of the story: say it's the
  hottest path if it is.

## 4. Diff blocks

- Real code from the real files — never pseudo-code dressed as a diff. Trim
  to the crux: the lines that changed plus 2–4 context lines. A diff block
  over ~20 lines needs to justify itself.
- Header carries the repo-relative file path (and ~line number if helpful).
- Red lines = old, green = new, dim lines = unchanged context.
- Inline comments in the diff point at **why**, not what:
  `// free in-memory check, no DB` — not `// check if locale code`.
- One diff block per idea. Two changes = two blocks, each with its own
  sentence of setup.
- After the diff, one short paragraph: what a reader should notice, and any
  new helper/file it requires.

## 5. Callouts

Three kinds, used deliberately, at most ~2 per page section:

- **Cost/warning** (⚠): quantify the damage. Numbers, sizes, frequencies:
  "hundreds of KB of database read per request, ~99% of the time for
  nothing — and twice per request when the payload carries a file."
- **Why it's safe / why this works** (💡): the one-paragraph argument that
  the change preserves behavior, with the reasoning spelled out.
- **Decision, not a bug** (🤔): when something is a defensible product
  choice, frame the trade-off honestly and say who should decide. Never
  present a decision as a defect or vice versa.

## 6. The scoreboard

The root page ends with a status table — the reader's map of what's real:

- One row per item across the whole site: short description, status pill
  (**done/merged**, **proposed**, **parked — decision needed**, **in
  todos**), and the codebase location in code font.
- Statuses must be true at publish time. Update the scoreboard when the
  underlying work changes — a stale "proposed" that's actually merged
  destroys trust in the whole site.

## 7. Prose rules

- Short paragraphs (2–4 sentences), one idea each. Bold the load-bearing
  term of a paragraph — sparingly, it's a wayfinding device, not emphasis.
- Headers can be questions ("What's wrong?", "When does this code even
  run?") — the reader arrives with those exact questions.
- Ban vague quantifiers: "slow", "big", "sometimes", "expensive" — replace
  with the number, size, or the concrete triggering input.
- No undefined jargon. Every term is either common engineering vocabulary,
  introduced in the primer, or introduced inline in the same sentence.
- No baby-talk and no mock enthusiasm. Plain, direct, occasionally wry.
- Second person for the reader's actions, "the engine/system" for code —
  avoid "we" (ambiguous between authors, code, and reader).

## 8. Page anatomy checklist

Topic page, top to bottom:

1. Eyebrow label (category · one or two words) + title that states the
   point, not the topic ("Stop downloading the whole photocopy on every
   request", not "Dispatch performance").
2. "When does this code even run?" story block.
3. "What's wrong" (or "How it works") — prose with the quantified callout.
4. "The fix" / "The design" — diff block(s) + the why-safe callout.
5. Optional: "Decision, not a bug" callout where applicable.

Root page, top to bottom: title + one-paragraph promise of what the site
explains → chip-style table of contents linking every page → primer →
scoreboard.
