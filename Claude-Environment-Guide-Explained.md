# Claude Environment Guide — How It Was Made

A plain-language walkthrough of how the **Claude Environment** infographic
(`claude-env-infographic.html`) was created: what it covers, the thinking and the
many rounds of iteration behind it, and a one-shot prompt you can paste into Claude to
recreate the whole thing from scratch.

---

## 1. What it is

A single, self-contained HTML page that gives an **experienced developer who is new to
Claude** a visual tour of the Claude environment. Wherever it matters, it distinguishes
between the three surfaces a developer will meet: **Web** (claude.ai), **Desktop**, and
**IDE / CLI** (Claude Code).

It is organised into these sections:

| Section | What it covers |
|---|---|
| **Surfaces** | Three environment cards (Web, Desktop, IDE/CLI), each calling out what is unique to that surface — local file access on Desktop, MCP config, subagents in Claude Code — plus the effective context-window limit for each. |
| **Sidebar & input controls** | A mockup of the actual sidebar hierarchy (New Chat, Projects, Starred, Recents, Account) and a full breakdown of the **"+" attach menu**, with Web / Desktop / All badges showing what is available where. |
| **Markdown file management** | Project scope vs environment/personal scope for `CLAUDE.md` (root, nested, local, global), `.claude/rules/`, `MEMORY.md`, and skills — plus how each interacts with `/compact` and git. |
| **Model selection** | Opus 4.8, Sonnet 4.6 (default), Haiku 4.5, and Fable 5 (suspended), with model strings, context/output windows, and links to the official docs. |
| **Claude Code commands** | Built-in slash commands and CLI commands, **each as a clickable card** linking to the relevant official doc page, with a concrete one-line usage example. |
| **Creating your own slash commands** | The modern `SKILL.md` approach, scope levels, frontmatter, dynamic context injection, and how commands are invoked. |
| **Core concept cards** | Skills, Connectors, MCP, Platform/API, Context management, Prompt engineering, and Projects — each an expanded, linked card with a short usage example. |

A **light/dark mode toggle** sits in the top-right of the header.

---

## 2. The build process, step by step

The guide was built in many small passes rather than one big drop. Each pass either added
a section or tightened accuracy.

### Step 1 — Pin down audience and scope

The brief was specific: an overview of the Claude environment for an **experienced developer
new to Claude**, differentiating Web / Desktop / IDE where appropriate, and covering the
sidebar, the "+" menu, model selection, and a set of core concepts (commands, skills,
connectors, MCP, the API, context management, and prompt best-practices) presented as
**linked cards**.

### Step 2 — Ground every claim in the official docs

Claude changes quickly, so the content was checked against Anthropic's documentation rather
than written from memory. Over successive passes, **every card was pointed at a specific page
under `code.claude.com/docs/en/`** so the infographic doubles as a launchpad into the real
docs.

### Step 3 — Make commands and concepts actionable

Each slash command, each CLI command, and each core concept became **its own clickable card**
linking to the exact doc page, with a one-line example showing what the command does or what a
real usage looks like (a custom command file, a `SKILL.md`, a connector prompt, an MCP config,
an API call, a prompt-caching snippet, an XML-structured prompt).

### Step 4 — Add "Creating your own slash commands"

A full-width panel was added covering how to build a command the modern way: `SKILL.md`
anatomy (YAML frontmatter, dynamic context injection, the instruction body), the scope levels
(enterprise / personal / project / legacy), and explicit vs automatic invocation.

### Step 5 — Add markdown file management

A section was added distinguishing **project scope** (commit to git: `CLAUDE.md` at root,
nested `CLAUDE.md`, `CLAUDE.local.md`, `.claude/rules/`, project skills) from
**environment / personal scope** (`~/.claude/CLAUDE.md`, auto-written `MEMORY.md`, personal
skills/agents, and enterprise-managed files), with a note on load order and what survives
`/compact`.

### Step 6 — Keep it current (June 2026)

The models and features were refreshed to reflect recent changes: **Opus 4.8** (released
May 28, with a Fast mode and large code-review gains), **Fable 5** (released June 9, then
**suspended June 12** under a US government export-control directive, flagged in red),
the retirement of old pinned model IDs (June 15) and the Opus 4.1 deadline (August 5), and
Claude Code additions — **Dynamic Workflows**, **auto-mode safety** that blocks destructive
git/terraform commands, `/fast`, `/cd`, the Chrome extension (beta), and Computer Use
(preview).

### Step 7 — Get the model facts right

Context-window figures were corrected and then refined to be **per-surface**, which is the
detail that matters most to a developer:

| Surface | Effective context | Notes |
|---|---|---|
| Web chat | 200K | 500K on Enterprise for select models |
| Desktop / Cowork | 200K | Auto-summarises long tasks |
| Claude Code | **1M** | Opus 4.8 / Sonnet 4.6 on paid plans (Pro must enable usage credits); Haiku stays at 200K |

Per-model: Opus 4.8 — 1M context / 128K output; Sonnet 4.6 — 1M / 64K; Haiku 4.5 — 200K / 64K.

### Step 8 — QA hardening (two passes)

- **Security:** every `target="_blank"` link got `rel="noopener noreferrer"` (anti
  reverse-tabnabbing), and a `<meta name="referrer" content="no-referrer">` was added.
- **Dead code:** unused CSS (`.md-scope-sub`) and a dead JS selector block were removed.
- **No inline JS:** `onmouseover`/`onmouseout` handlers were replaced with CSS `:hover`.
- **Accuracy:** `/review` was un-deprecated (it is a current command, distinct from
  `/code-review`), and two broken `/settings` links were corrected to `/claude-directory`.
- **Confirmed:** no Google Fonts (system font stack only), and fully self-contained — no
  external scripts, CDN imports, or stylesheets.

### Step 9 — Light/dark toggle

A theme toggle was added: a `data-theme` attribute on `<html>` flips a comprehensive set of
CSS-variable and inline-style overrides between dark and light.

---

## 3. The tech, in plain language

You don't need any of this to use the guide, but here's what's under the hood:

- **One HTML file.** Layout, styling, and the small amount of interactivity all live in a
  single file. No installs, no libraries, no build step.
- **System fonts only.** No Google Fonts and no web-font downloads — it uses the fonts already
  on your device, so it loads instantly and works offline.
- **Fully self-contained.** No CDN links, no external scripts, no external stylesheets.
- **Cards are the building block.** Model cards, command cards, and concept cards are each a
  clickable link to the matching official doc page.
- **Privacy- and safety-minded links.** External links open in a new tab with
  `rel="noopener noreferrer"`, and the page sends no referrer header.

---

## 4. How to adapt it

- **Re-scope the audience.** Aim it at a non-developer, or at a team standardising on Claude Code.
- **Swap or extend the concept cards.** Add or remove cards as your stack changes.
- **Keep the models current.** Update the model cards and context-window figures as Anthropic
  ships new models or changes limits.
- **Restyle it.** Change the palette, or make light mode the default.

To make any of these, just ask Claude in plain language — for example,
*"update the model cards to the latest Claude lineup and re-check every context window against
the official docs"* — and it will edit the file.

---

## 5. One-shot prompt to recreate this

Paste the prompt below into Claude to generate the infographic from scratch. It is written to
be self-contained so you can reuse or adapt it anytime.

```
Build a single, fully self-contained HTML infographic (one file: inline CSS and a small
amount of inline JS, no external libraries, no CDN links, no external stylesheets, NO Google
Fonts — use a system font stack only). It gives an EXPERIENCED DEVELOPER who is NEW TO CLAUDE
a visual overview of the Claude environment.

Before writing, ground every fact in Anthropic's official documentation at
https://code.claude.com/docs/en/ (and the model/pricing docs). Claude changes fast — verify
current models, commands, and context-window limits rather than relying on memory. Link each
card to the specific official doc page it describes.

Differentiate the three surfaces — Web (claude.ai), Desktop, and IDE/CLI (Claude Code) —
wherever it matters. Include these sections:

1. SURFACES — three environment cards (Web, Desktop, IDE/CLI). For each, list what is unique
   to it (e.g. local file access on Desktop, MCP config, subagents and Dynamic Workflows in
   Claude Code) and its EFFECTIVE context-window limit (Web chat 200K; Desktop/Cowork 200K;
   Claude Code 1M for Opus/Sonnet on paid plans, Haiku 200K).
2. SIDEBAR & INPUT CONTROLS — a mockup of the sidebar hierarchy (New Chat, Projects, Starred,
   Recents, Account) and a full breakdown of the "+" attach menu, with Web / Desktop / All
   badges showing where each option is available.
3. MARKDOWN FILE MANAGEMENT — distinguish PROJECT scope (commit to git: CLAUDE.md at root,
   nested CLAUDE.md, CLAUDE.local.md, .claude/rules/, project skills) from
   ENVIRONMENT/PERSONAL scope (~/.claude/CLAUDE.md, auto-written MEMORY.md, personal
   skills/agents, enterprise-managed). Note load order and what survives /compact.
4. MODEL SELECTION — cards for Opus 4.8 (default-capable, 1M context / 128K output),
   Sonnet 4.6 (default, 1M / 64K), Haiku 4.5 (200K / 64K), and Fable 5 (mark as suspended).
   Show model strings and link each card to the official model docs.
5. CLAUDE CODE COMMANDS — built-in slash commands AND CLI commands, EACH as its own clickable
   card linking to the relevant official doc page, with a concrete one-line usage example.
6. CREATING YOUR OWN SLASH COMMANDS — a panel covering the modern SKILL.md approach: an
   annotated SKILL.md (YAML frontmatter, dynamic context injection, instruction body), the
   scope levels (enterprise / personal / project / legacy), and explicit vs automatic
   invocation with $ARGUMENTS.
7. CORE CONCEPT CARDS — Skills, Connectors, MCP, Platform/API, Context management, Prompt
   engineering, and Projects. Give each an expanded description, a short code-style usage
   example, and a link to its official doc page.

Design & quality requirements:
- Clean, dense, developer-oriented dark theme, with a LIGHT/DARK toggle in the header
  (flip a data-theme attribute on <html> and override CSS variables + inline styles).
- Every external link must open in a new tab with rel="noopener noreferrer", and add
  <meta name="referrer" content="no-referrer"> to the head.
- No dead CSS or JS, no inline event handlers (use CSS :hover), no Google Fonts, and the
  whole thing must be one standalone file that works offline.
```

---

## 6. Setting it up on Claude

1. **Open Claude.** Go to [claude.ai](https://claude.ai) or the Claude desktop app.
2. **Pick a capable model.** Use a strong model (e.g. Claude Opus) for a complete, accurate build.
3. **Turn on web search / doc access.** This is what lets Claude verify current models, commands,
   and context limits against the official docs — the thing that keeps the guide correct.
4. **Paste the one-shot prompt** from Section 5 and send it.
5. **Let it build.** Claude researches the current docs, then produces the infographic as an
   interactive artifact beside the chat. Open it and click through the cards and the theme toggle.
6. **Iterate in plain language.** Ask for changes conversationally — *"add a card for X,"*
   *"re-check every context window,"* *"make light mode the default."* Claude edits the same file.
7. **Save or host it.** Download the HTML from the artifact, or commit it to a GitHub project
   space (as in this Infographics collection) and serve it with GitHub Pages.

> **Tip:** Because Claude evolves quickly, re-run a short "verify everything against the latest
> official docs" pass whenever you revisit the guide — that's how this one stayed current
> through several model and feature changes.
