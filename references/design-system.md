# Design system — tokens and component rules

The template (`site-template.html`) already implements everything here. This
file exists so you can modify pages confidently without breaking the system.

## Principles

- **Self-contained pages.** All CSS inline in each page's `<style>`. No
  shared .css file (file:// friendliness beats DRY here), no build, and no
  JS beyond the one theme-toggle snippet in the template's `<head>`.
- **One system across pages.** Same tokens, same components, same sidebar on
  every page. A multi-page site must feel like one document.
- **Warm light by default, dark on demand.** The default palette is the
  Claude-style warm one: cream paper, clay accent, near-black ink. Dark is a
  second set of values for the same tokens — never restyle components
  individually for it.

## Tokens (warm light default)

```css
:root{
  color-scheme:light;
  --primary:#c2603d;                /* clay accent — swap hue for the project's brand if it has one */
  --accent-50:#f8f3ef; --accent-100:#f3e8e1; --accent-200:#e9d6ca; --accent-700:#9c4a2c;
  --bg:#faf9f5; --surface:#ffffff; --border:#e6e2d8; --border-subtle:#f0ece2;
  --text:#1f1e1c; --text-2:#46443f; --text-3:#75726a;
  --green-50:#eaf4ec; --green-700:#2f6b45;   /* diff add, done pill  */
  --red-50:#fbeeea;   --red-700:#a8412a;     /* diff del             */
  --amber-50:#fbf1de; --amber-700:#8a6220;   /* warnings, idea pill  */
  --warn-border:#e8d4a8; --good-border:#c3ddcb;
  --sans:"Plus Jakarta Sans",-apple-system,BlinkMacSystemFont,sans-serif;
  --serif:"Instrument Serif",Georgia,"Times New Roman",serif;
  --mono:"JetBrains Mono",ui-monospace,Menlo,monospace;
}
```

## Theme toggle (light / dark)

The same tokens carry a dark set — warm charcoal paper, the brighter clay
`#d97757`, off-white ink — declared twice in the template: once under
`:root[data-theme="dark"]` and once inside
`@media(prefers-color-scheme:dark)` scoped to
`:root:not([data-theme="light"])`. Result: the page follows the reader's OS
until they press the button, after which their choice wins on every page.

The ☾/☀ button sits in the sidebar header (`.themebtn`); its glyph is pure
CSS `content`, and a ~10-line inline script in `<head>` flips `data-theme`
on `<html>` and remembers it in `localStorage` (wrapped in `try/catch`, since
`file://` storage can be blocked). Keep the script in the head — moving it to
the end of the body causes a light flash on dark-mode machines.

Three faces, loaded from Google Fonts with system fallbacks — never mix in a
fourth:

- **`--serif` — Instrument Serif** for titles, headings and the page
  subtitle (`h1`, `h2`, `h3`, `.sub`, sidebar brand). It ships in weight 400
  only: always set `font-weight:400` and get emphasis from size, never bold.
  Sizes: h1 40px/1.15, h2 28px/1.25, h3 20px, `.sub` 19px/1.5; letter-spacing
  −0.005 to −0.01em.
- **`--sans` — Plus Jakarta Sans** (400/500/600/700) for body copy and UI
  chrome (paragraphs, lists, sidebar links, chips, callouts, diff headers).
  Body 15px, line-height 1.7.
- **`--mono` — JetBrains Mono** for code, identifiers, file paths, numbers,
  and every small uppercase label (eyebrow, WHO pills, status pills, table
  headers).

Prose max-width 72ch, `.sub` 60ch. Radii: 12–14px cards, 99px pills. Borders are 1px
`var(--border)`; no drop shadows — separation comes from the border and the
cream/white contrast between `--bg` and `--surface`.

## Layout with sidebar

Two-column grid: fixed 230px sidebar + fluid content column (max 880px).
The sidebar is `position:sticky; top:0; height:100vh; overflow-y:auto` and
is **repeated in every page's HTML** with the current page's link marked
`class="on"`. Below 800px it becomes a horizontal scrolling strip at the top
(the template includes the media query).

It is **collapsible with no JS**: a visually-hidden checkbox (`.navcb`) sits
immediately before `.shell`, the ☰ `<label for="nav">` toggles it, and
`.navcb:checked + .shell` narrows the grid column to a 56px rail with the
title and links hidden. Keep the checkbox as the direct previous sibling of
`.shell` or every `+` selector breaks. Collapse state is per page load by
design — no storage, nothing to keep in sync.

Sidebar contents, in order: site title + the ☾/☀ and ☰ buttons in one row
(`.sidetop`), then one link per page in reading order, numbered the same as
the filenames (`01`, `02`, …). Never
list anchors of other pages in the sidebar — in-page anchors belong to the
chip TOC at the top of each page.

## Components

- **Eyebrow**: mono, 11px, uppercase, letter-spaced, `--primary`. Marks the
  category above each h2.
- **Chip TOC** (root page + long pages): pill links, `--accent-100`
  background, `--accent-700` text.
- **Story block** (`.story`): surface card; each beat is a row of a mono
  uppercase WHO pill (`--accent-100`/`--accent-700`) + plain-language WHAT.
- **Callouts** (`.callout.why|.warn|.good`): icon + text row on the tinted
  50-shade background with a matching darkened border; body text `--text-2`,
  bold terms `--text`.
- **Diff block** (`.diff`): surface card; header row = plain-language title
  left, mono file path right; `<pre>` body where each line is a
  `span.ln` with `.del` (red), `.add` (green), or `.ctx` (dim); inline
  comments in `.cm`. Horizontal scroll inside the block, never the page.
- **Status pills** (`.pill.done|.idea`): mono 10.5px, green for done,
  amber for proposed/parked.
- **Scoreboard** (`.status` table): mono uppercase headers, top-aligned
  cells, wrapped in an `overflow-x:auto` div.
- **Inline code**: `--accent-100` background, `--accent-700` text, 12.5px
  mono. Use for every identifier, path, and literal value in prose.

## Accessibility / quality floor

- Real `<a>` links everywhere (keyboard reachable); `:focus-visible` outline
  in `--primary` (template includes it).
- Color is never the only signal in diffs: keep `-`/`+` glyphs or rely on
  the inline comments; the template prefixes are optional but recommended.
- Contrast: body text on `--bg` and `--surface` meets 4.5:1 with the given
  tokens — if you change colors, re-check.
- Wide content (tables, diffs) scrolls inside its own container; the page
  itself never scrolls horizontally.
