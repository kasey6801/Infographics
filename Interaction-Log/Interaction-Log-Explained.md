# The Interaction Log, Rebuild Guide

This is the companion explainer for `index.html` (the Interaction Log field-guide page). It documents
what the page shows, the design choices behind it, how the underlying log and its command actually
work, and a single prompt at the end that recreates the whole HTML file from scratch.

The page is one self-contained HTML file: warm "field guide" styling, five numbered panels, and a
static rendered sample of a log entry. It is an explainer about a workflow, not an interactive tool,
so there is no calculator and no live simulator. There is no server and no network call.

---

## 1. What the page documents

The Interaction Log itself is a separate file, `Interaction-Log.html`, kept at the root of the
workspace you want to track. That log is a running, self-contained record of Claude working sessions
across every project in that workspace. This page explains it in three beats: what it is, how to
create it, and why it is used.

The log groups activity by project folder, then by date, newest first. Each logging run adds one
combined entry for the session: the folder that changed most, every prompt that was sent, and a
short summary of what changed. It is produced and updated by the `/Interaction-log` command, never
by hand.

---

## 2. File anatomy

A single `index.html` with three inline parts and no external dependencies:

- **`<head>`**: charset and viewport meta, a `referrer` no-referrer meta, a tiny no-flash theme
  script that sets `data-theme="dark"` before paint, then the full `<style>` block.
- **`<body>`**: a fixed theme-toggle button, then a `.wrap` holding the header and five `.panel`
  sections, and a footer with the MIT License line.
- **`<script>`**: one small IIFE that flips `data-theme`, persists the choice to
  `localStorage('theme')` inside `try/catch`, and updates the toggle label. No other JavaScript.

The five panels:

1. **What it is.** The log as a single self-contained page at your workspace root, grouped by folder
   then date, newest first, with no backend.
2. **Anatomy of an entry.** A static replica of a real entry (folder header, dated section, numbered
   prompts, green-bulleted changes) followed by short callouts and the underlying markup in a dark
   `pre` block.
3. **How to create it.** The `/Interaction-log` command shown in an inverted dark command line, then
   the update algorithm as five numbered steps.
4. **House rules.** The formatting and safety constraints the command follows.
5. **Why it's used.** The rationale: one browsable record, accountability, structure that scales,
   and a portable, private, on-brand artifact.

---

## 3. How the log gets built (the command)

The page describes the behaviour of `/Interaction-log`, defined as a skill (for example, a
`.claude/skills/interaction-log/SKILL.md` folder in your workspace), run with the `/Interaction-log`
slash command. On each run the skill:

1. Captures today's date, checked live and never hardcoded, and the primary folder: the one that
   changed most this session, written as a path relative to your workspace root. If several folders
   were touched, the others are recorded inside the change summary, and the run still produces one
   combined entry.
2. Gathers every prompt from the session in order and verbatim, plus a short bullet list of what
   changed (files created or edited, and any other folders touched).
3. Finds the `<details class="folder">` block whose `<span class="path">` matches the primary folder.
   If it exists, that block is moved to the top of `<main id="log">`. If it does not, a new
   `<details class="folder" open>` is built and inserted first.
4. Inside that folder's `.folder-body`, looks for `<section class="entry" data-date="TODAY">`. If it
   is present, the new prompt `<li>` items are appended to `<ol class="prompts">` and the new change
   `<li>` items to `<ul class="changes">`. If it is absent, a fresh dated `<section>` is prepended as
   the first child.
5. Updates the folder's `<span class="count">` to the current number of dated sections, then reports
   one line: folder, date, and how many prompts and changes were logged.

If `Interaction-Log.html` does not exist yet, the skill first creates it from a built-in base
template (the same warm palette, a header, an empty `<main id="log">`, and expand and collapse
controls), so the first run works with no setup.

---

## 4. House rules the command follows

- HTML-escape everything logged: `&` becomes `&amp;`, `<` becomes `&lt;`, `>` becomes `&gt;`.
- Wrap file paths and identifiers in `<code>` so they render as code.
- No em dashes in the summary prose; use commas, colons, periods, or middots.
- Newest first, always: newest folder at the top of the log, newest date at the top of its folder.
- No external or network resources, ever. The page stays fully self-contained.
- Do not commit or push. The command leaves version control to you, since the log may sit outside a git repository.

---

## 5. Theming and style

The look is the shared "field guide" language: warm paper and ink, bold 1.5px ink borders, a hard
printed offset shadow, mono uppercase eyebrows and labels, and a heavy display headline.

Palette (CSS variables in `:root`, light is the default):

```css
--paper:#E9E1CF; --paper-2:#E1D8C2; --card:#F3EDDD;
--ink:#1C1A15;   --ink-soft:#4A463C; --line:#C5BAA0;
--stamp-red:#B23A2E;   /* links, eyebrows, prompt accents, step badges */
--seal-green:#2E6149;  /* change bullets, benefit list */
--shadow:0 2px 0 rgba(28,26,21,.14);
```

The static sample panel reuses the log's own entry styling so the replica looks exactly like the
real thing, minus the collapse behaviour (it is a static diagram, not a live `<details>`). The
numbered step badges and the panel number chips keep a hardcoded `#B23A2E` background with cream
text so the contrast stays high in dark mode, where `--stamp-red` lightens. The command line and the
markup `pre` block stay dark in both themes, as the style guide prescribes for inverted panels.

Dark mode is standard: variables are overridden under `[data-theme="dark"]` on `<html>`, the body's
white highlight gradient is toned down, a fixed top-right toggle flips `data-theme` and persists to
`localStorage('theme')`, and a head script applies the saved or system-preferred theme before first
paint to avoid a flash. All `localStorage` access is wrapped in `try/catch`.

---

## 6. Accessibility and security

- Semantic structure, an `aria-label`ed toggle button, stamp-red `:focus-visible` outlines, and
  strong contrast in both themes.
- No external resources, no fonts to download, no analytics, no network calls.
- No user input and no `innerHTML` writes: the page is fully static markup, so there is no XSS
  surface.

---

## 7. One-shot rebuild prompt

Paste the block below to regenerate the HTML file from scratch.

```text
Build a single self-contained HTML file: a "field guide" explainer titled "The Interaction Log"
that documents an activity-log workflow. It is a static explainer, not an interactive tool: no
calculator, no simulator, no live demo.

HARD CONSTRAINTS
- One self-contained HTML file. No CDNs, external scripts, stylesheets, web fonts, analytics, or any
  network/runtime calls. Inline CSS and minimal inline JS only.
- System font stacks only (sans: system-ui,-apple-system,"Segoe UI",Roboto,Helvetica,Arial; mono:
  ui-monospace,"SF Mono",Menlo,Consolas,"Liberation Mono"). Never Google Fonts.
- No em dashes anywhere; use commas, colons, periods, or middots.
- Include a "referrer" no-referrer meta and an MIT License line in the footer linking to LICENSE.
- All localStorage access wrapped in try/catch.

STYLE (warm "field guide", paper and ink)
- Light default with a standard dark mode. Palette as CSS variables in :root:
  --paper:#E9E1CF; --paper-2:#E1D8C2; --card:#F3EDDD; --ink:#1C1A15; --ink-soft:#4A463C;
  --line:#C5BAA0; --stamp-red:#B23A2E; --seal-green:#2E6149; --shadow:0 2px 0 rgba(28,26,21,.14).
- Dark mode overrides under [data-theme="dark"] on <html>: --paper:#17150F; --paper-2:#1F1B14;
  --card:#221E17; --ink:#ECE3D0; --ink-soft:#B3AA96; --line:#403829; --stamp-red:#E08A7C;
  --seal-green:#69B294; --shadow:0 2px 0 rgba(0,0,0,.45).
- Panels: --card background, 1.5px solid --ink border, radius ~10px, hard offset --shadow. Mono
  uppercase section labels with wide letter-spacing and a stamp-red numbered chip. Heavy clamp() h1
  with one word accented in stamp-red. Body line-height ~1.58. Centered .wrap, max-width ~820px,
  padding 0 22px. Body on --paper with two faint radial gradients (toned to .04 in dark).
- Number-badge chips and step circles use a hardcoded #B23A2E background with cream (#F3EDDD) text so
  they stay high-contrast in dark mode.
- Any inverted panel (a command line, a code pre block) stays dark in both themes: #1C1A15 background,
  #E9E1CF text.
- Fixed top-right theme toggle (mono) showing "moon Dark" / "sun Light", aria-labelled. A head script
  sets the theme before paint from localStorage('theme') with a prefers-color-scheme fallback
  (try/catch). Global stamp-red :focus-visible outlines.

LAYOUT
- Header: mono stamp-red eyebrow "Workflow, Activity log", an h1 "The Interaction Log" with "Log"
  accented, and a one-line lede describing a self-contained activity log at a project root, grouped
  by folder then date, newest first, kept current with one command.
- Five numbered .panel sections:
  1) What it is: a single self-contained HTML file recording every session, grouped by folder then
     date, newest first, no backend or dependencies.
  2) Anatomy of an entry: render a STATIC replica of a log entry (do not use a live <details>): a
     folder header row (a rotated chevron, a monospace folder path, and a right-aligned date count),
     then a folder body with one dated section containing an ISO date heading, a "Prompts" label with
     a numbered list styled with a stamp-red left border and counter, and a "Summary of changes" label
     with a list of green triangle bullets whose file paths are wrapped in <code>. Follow with short
     callouts naming each part, then a dark <pre> block showing the entry markup (HTML-escaped).
  3) How to create it: show the command "/Interaction-log [optional note]" in an inverted dark line,
     then a numbered five-step list: capture date and primary folder; gather verbatim prompts and a
     change summary; find or create the folder <details> and move it to the top; find or create the
     dated section and append or prepend; refresh the date count and report one line. Add a note that
     the file is auto-created from a base template on first run.
  4) House rules: a bulleted list: HTML-escape logged text; wrap paths in <code>; no em dashes;
     newest first; no external resources; do not commit or push.
  5) Why it's used: a bulleted list of benefits: one browsable searchable record across projects;
     accountability (prompts plus changes per session); collapsible folders and newest-first scaling;
     portable and private, opens from disk, matches the collection's look.

FOOTER
- A line noting the page is self-contained (no CDN, web fonts, or runtime dependencies) and an MIT
  License line linking to LICENSE.

Verify before finishing: the page is fully self-contained, there are no em dashes, the sample entry
is static (no live collapse), and the toggle re-themes every panel and the sample cleanly.
```
