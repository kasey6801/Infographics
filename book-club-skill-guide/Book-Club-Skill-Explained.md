# The Book Club Skill — How This Was Made

The companion to the [**Install &amp; Use guide**](index.html). This document is the
behind-the-scenes record: what was built, the QA it went through, and the copy-paste **prompts to
recreate the whole workflow** from scratch.

Two things were built and wired together:

1. **The book-club skill** — a Claude skill that suggests books, leads discussions, and logs what
   you read. It lives at
   [`commands/book-club.md`](https://github.com/kasey6801/claude-skills/blob/main/commands/book-club.md).
2. **The Reading Log page** — a single self-contained HTML page (`index.html`) that renders the
   reading history. The skill creates this page and keeps it in sync with memory.

The key idea: **memory is the durable source of truth, and the HTML page is its visible view.** The
skill writes every confirmed entry to both, in the same turn — so logging a book *is* updating the
log, and the page can always be rebuilt from memory.

---

## How the Reading Log page was built

### 1. Pulled the data from memory

The reading history (books read, articles summarized, discussions held) already existed in Claude's
memory. That became the input — no database, no server. Every entry is written directly inside the
`index.html` file as a JavaScript array named `DATA` near the bottom, so the page works offline and
hosts as a single file.

### 2. Modeled each entry as a structured object

Every entry is one object inside `const DATA = [ … ];`. The fields:

| Field | Purpose |
|---|---|
| `type` | `book` / `article` / `discussion` — drives color, grouping, and the status chip |
| `title` | Display title |
| `author` | Person (books/articles) or a descriptor like `on Le Guin` / `cross-work: Pargin · Le Guin` (discussions) |
| `source` | Publication (articles) or `Discussion` (discussions); omitted for books |
| `year` | Publication year (books/articles); discussions have none |
| `added` | ISO date the entry entered the log |
| `status` | `done` / `next` (books), `art` (articles), `disc` (discussions) |
| `series` | e.g. `Hainish Cycle · 6`, `Discworld · 1` |
| `link` | Open Library work page (books + discussions) or article URL |
| `linkLabel` | Discussions only — the subject book's title |
| `summary` | 1–3 sentence description |
| `disciplines` | Tags from a fixed controlled vocabulary (see below) |
| `themes` | 2–4 free-text tags |
| `note` | Optional callout (open threads, "up next" watch-list) |

### 3. Resolved real links

- **Books and discussions** link to Open Library *work* pages, resolved by querying the Open
  Library `search.json` API by title + author and taking the work key (`/works/OL…W`). Discussions
  point at the work they're *about* and label the link with that book's title.
- **Articles** link to their own source URL (Substack caveat: use `[author].substack.com/p/[slug]`,
  not the `substack.com/@author/p-[id]` form, which can't be fetched directly).

### 4. Fixed the discipline vocabulary

Disciplines use a closed list of 14 terms so tags stay consistent instead of multiplying:

> macro economics · micro economics · sociology · anthropology · engineering · statistics ·
> finance · marketing · accounting · physics · chemistry · computer science · software
> engineering · communication

Themes, by contrast, are free-text.

### 5. Built the interface

- **Type filter** (All / Books / Articles / Discussions)
- **Multi-select facets** for authors, themes, and disciplines (OR within a facet, AND across
  facets; empty disciplines shown disabled)
- **Search** across titles, authors, sources, and tags
- **Dates** control with a basis toggle (date *Added* vs. *Published*) and an adaptive period
  selector for each basis
- **Sort**: Grouped (by type), Newest, or Oldest — on whichever date basis is active
- **Light/dark mode**, following the system setting by default
- **Collapsible entries** with a colored type edge and a status chip
- Responsive down to a 2-column and then single-column layout

### 6. QA hardening

Two review passes were run, each verified with `node --check` on the script and a parse of the
`DATA` array:

- Removed dead CSS and a redundant `.clear-btn[hidden]` rule.
- Fixed a dark-mode bug where body prose used a hardcoded color — replaced with a themeable
  `--prose` variable.
- Switched from an external web-font fetch to system fonts (faster, no network dependency, works
  offline).
- Hoisted the static type tally out of the render loop so it's computed once.
- Made the expand/collapse control's `aria-label` flip between "Expand entry" and "Collapse entry"
  for screen-reader accuracy.

**Security check:** no `localStorage`/`sessionStorage`, no `eval`; external links carry
`rel="noopener noreferrer"`; the search box value never reaches the DOM, so there's no injection
path. The page builds markup from the trusted static `DATA` only.

---

## How the skill drives the log

The skill was extended so that **every confirmed entry is written to the log file, not just to
memory.** On each new entry it:

1. **Confirms** the entry with you (title, author, finished vs. in-progress / which book a
   discussion is about).
2. **Writes it to memory** using the structured schema.
3. **Appends a matching object to `DATA`** — `added` set to today, `status` mapped from the entry
   type, links resolved (Open Library for books/discussions, source URL for articles), disciplines
   from the 14-term list, themes free-text.
4. **Updates the "Last updated" line** to today.
5. **Verifies** with `node --check` and an entry-count check before showing the file.
6. **Re-presents** the page.

**Summaries** are short (1–3 sentences) but reached differently: a **book** summary is a
spoiler-light framing from known details plus the discussion (not from reading the whole book); an
**article** summary is built by fetching the URL and condensing the text, noting any paywall.

**Self-contained, never fetches the log.** The only inputs are memory and the artifact in the
current conversation. If the page is absent (e.g. a fresh chat) the skill **regenerates it from
memory** using the embedded build spec — it does not download a previous copy from GitHub, GitHub
Pages, or a published URL. A published copy is output only, never an input.

```
You: "I finished The Telling."          <- a book
 or  "Summarize this article: <url>"    <- an article
 or  "Log our discussion."              <- a discussion
        |
        v
Skill confirms title / author / status
        |
        v
Builds the entry object (link · summary · tags · status · added date)
        |
        v
Writes to memory  -->  Appends the object to DATA[] in index.html
        |                          |
        +-------------+------------+
                      v
        node --check + entry-count verification + "Last updated" bumped
                      v
        Re-presents the updated Reading Log
```

---

## Prompts to recreate this workflow

You can rebuild the whole thing with the three prompts below.

### 1. Build the Reading Log page (one-shot)

Paste this into Claude (ideally inside a Project). It produces the page as an **empty shell** —
ready to receive entries — and ends with finished, QA'd code.

```text
Build me a single self-contained interactive HTML reading log, deliver it as one
file (index.html), then run two rounds of QA on it.

START EMPTY. The log holds no entries yet — initialize it with an empty data
array (`const DATA = [];`) so I can start logging my own books, articles, and
discussions. When there are no entries, show a friendly empty-state message; the
controls should still render, and the facets should populate automatically as
entries are added.

ENTRY FORMAT. Every entry I add later is one object in the `DATA` array, grouped
by type, with these fields:
- type: "book" | "article" | "discussion"
- title
- author: the person (books/articles); for discussions a short descriptor such
  as "on Le Guin" or "cross-work: A · B"
- source: publication (articles) or "Discussion" (discussions); omit for books
- year: publication year (books/articles); omit for discussions
- added: ISO date "YYYY-MM-DD"
- status: "done" or "next" (books), "art" (articles), "disc" (discussions)
- series: e.g. "Hainish Cycle · 6", "Discworld · 1", or ""
- link: Open Library *work* page for books and discussions; the article's own
  URL for articles
- linkLabel: discussions only — the subject book's title
- summary: 1–3 sentences
- disciplines: array drawn ONLY from this fixed 14-term list — macro economics,
  micro economics, sociology, anthropology, engineering, statistics, finance,
  marketing, accounting, physics, chemistry, computer science, software
  engineering, communication — tagged only where clearly applicable
- themes: 2–4 free-text tags
- note: optional; omit if none

FEATURES.
- Type filter bar: All / Books / Articles / Discussions.
- Multi-select facet dropdowns for Authors, Themes, and Disciplines: compact
  trigger plus a floating checkbox panel, per-option counts, OR within a facet
  and AND across facets. Authors and Themes are derived from the logged entries;
  Disciplines always lists the fixed 14 terms, with any that have no entries
  shown disabled.
- Search box matching title, author, source, and tags.
- Dates control with a basis toggle (Added ⇄ Published) and a period selector
  that adapts to the basis: for Added, All time / per-month buckets / a custom
  from–to date range; for Published, from-year and to-year selects. Build both
  period UIs once and just show/hide them so nothing reflows.
- Sort: Grouped (by type, default), Newest, Oldest — on whichever date basis is
  active. Entries with no publication date (e.g. discussions) collect in a
  "No publication date" group at the end under Published + sorted.
- Dark-mode toggle that defaults to the system setting and is session-only (no
  storage).
- Collapsible entries, each with a colored left edge by type, a status chip, and
  a meta line (byline · added date · source link).
- A bordered container, sticky controls, responsive down to two columns then one
  on narrow screens.
- A "Last updated [date]" line near the top of the page.

CONSTRAINTS.
- One file, fully self-contained: all CSS and JS inline, system fonts only, no
  external assets or network calls at runtime, so it works offline.
- All theme colors via CSS variables, including a `--prose` token for body text
  so dark mode has no hardcoded colors.
- No localStorage, sessionStorage, eval, Function, or document.write. Build the
  list markup from the static DATA only; never insert the search value into the
  DOM.

QA — RUN TWO ROUNDS, fixing what you find and re-verifying after each change:
- Round 1 (correctness + hygiene): syntax-check the script with `node --check`;
  confirm DATA is a valid (empty) array and the empty-state renders; remove any
  unused CSS or JS; confirm no hardcoded text colors survive in dark mode (body
  prose uses `--prose`); confirm the type tally is computed once, not inside the
  render loop; security scan — no storage/eval, rel="noopener noreferrer" on
  external links, user input never reaches innerHTML.
- Round 2 (polish + accessibility): re-scan for unused selectors or identifiers
  introduced by the round-1 edits; make the expand/collapse control's aria-label
  flip between "Expand entry" and "Collapse entry" with aria-expanded kept in
  sync; drop any redundant rules (e.g. an explicit [hidden]{display:none} that
  the global hidden attribute already handles); re-run the syntax checks before
  presenting.

FINALIZE. Set both the <title> and the on-page <h1> to "Reading Log". Name the
file index.html (the root file GitHub Pages serves). Present the final file.
```

### 2. Get (or recreate) the book-club skill

The finished skill is published — the simplest path is to use it directly:

> Install the book-club skill from
> [`commands/book-club.md`](https://github.com/kasey6801/claude-skills/blob/main/commands/book-club.md).
> It already contains the build spec above, the entry schema, the controlled discipline list, the
> link-resolution rules, and the "write to the log" workflow.

To regenerate an equivalent skill from a plain book-club assistant, prompt Claude:

```text
Take this book-club assistant skill and make it keep a Reading Log page in sync
with memory. Add:
- entry types for books, articles, and discussions (with a "summarize this
  article: <url>" flow and a "log our discussion" flow);
- a "Writing to the Reading Log File" step: on every confirmed entry, write to
  memory AND append a matching object to the page's `DATA` array, then verify
  (node --check + entry count), bump the "Last updated" date, and re-present it;
- a "Creating the Reading Log" step that embeds the one-shot build spec above, so
  the skill can create the page on request, auto-create it on the first entry if
  none exists, and regenerate it from memory if it's lost;
- the entry schema and the fixed 14-term discipline vocabulary;
- a rule that the skill is self-contained: never fetch the log from GitHub,
  GitHub Pages, or any external URL — memory and the current conversation are the
  only sources;
- setup and publishing guidance so a new user can install it and go.
Keep it self-contained in one markdown file.
```

### 3. Set it up in Claude

1. **Make a Project** ("Book Club" or "Reading Log") for persistent, isolated memory.
2. **Add the skill** — paste it into the Project's custom instructions or knowledge (Claude
   Chat/Project), or drop it in your commands directory for the `/book-club` slash command (Claude
   Code).
3. **Create the log** — say "create my reading log" (or just start logging and it auto-creates).
4. **Start logging** — "I finished [title]", "log some books", "summarize this article: <url>",
   "log our discussion." Entries flow to memory and into the page automatically.

Because the page is generated from memory, it's always reproducible — lose the file and the skill
rebuilds it.
