# Infographics

A collection of self-contained infographics. Each guide is
a single HTML page with **no external dependencies** — no CDN links, no web fonts (system
font stack only), no trackers, and all CSS inline. Every page works offline and stands on
its own.

**Live site:** https://kasey6801.github.io/Infographics/

**Design reference:** [`STYLE_GUIDE.md`](STYLE_GUIDE.md) — the shared field-guide look, palette, and dark-mode pattern for new pages.

## Pages

- `index.html` — landing page linking to all guides
- `warp-isp-overview/` — *What can your ISP see? — an overview of Cloudflare WARP* (folder: `index.html` + explainer `.md`)
- `gemini-onboarding-dashboard/` — *Your Gemini Starter Dashboard (Marketing & Sales)* (folder: `index.html` + explainer `.md`)
- `claude-env-infographic/` — *Claude Environment* — interface reference: web, desktop, IDE/CLI (folder: `index.html` + explainer `.md`)
- `book-club-skill-guide/` — *The Book Club Skill* — install &amp; use a Claude reading-log skill (folder: `index.html` + explainer `.md`)
- `CAD-NATO-Defense-Spending/` — *Canadian NATO Defense Spending* — review of Canada's 2025-2026 military procurement through NATO obligations (folder: `index.html` + explainer `.md`)
- `CC_games_and_education/Chess_tutor/` — *Chess Tutor* — playable chess game with a from-scratch engine, tutor arrows, and a post-game accuracy report (folder: `index.html` + explainer `.md`)
- `how-this-landing-page-was-made.html` — *How this landing page was made* (a generic GitHub Pages walkthrough; no companion `.md`)
- `about.html` — about the collection and its design principle

## "Explained" files

Each infographic ships with a companion `*explained*.md` file that documents how it was
built. These files lay out the step-by-step process used to create the guide, plus a
**one-shot prompt** you can use to re-create the infographic from scratch.

- `warp-isp-overview/Cloudflare Warp Field Guide Explained.md` — accompanies `warp-isp-overview/index.html`
- `gemini-onboarding-dashboard/Gemini-dashboard-explained.md` — accompanies `gemini-onboarding-dashboard/index.html`
- `claude-env-infographic/Claude-Environment-Guide-Explained.md` — accompanies `claude-env-infographic/index.html`
- `book-club-skill-guide/Book-Club-Skill-Explained.md` — accompanies `book-club-skill-guide/index.html`
- `CAD-NATO-Defense-Spending/CONTEXT_AND_REBUILD_GUIDE.md` — accompanies `CAD-NATO-Defense-Spending/index.html`
- `CC_games_and_education/Chess_tutor/Chess-Tutor-Explained.md` — accompanies `CC_games_and_education/Chess_tutor/index.html`

On the landing page, each guide's card links to its explained file via the
"How this was made (.md) →" link (which opens GitHub's rendered view).

## Local preview

```bash
python3 -m http.server 8000
# then open http://localhost:8000/
```

Use a local server (as above) rather than opening `index.html` from the file system, so the
folder-based guide links (e.g. `warp-isp-overview/`) resolve to each folder's `index.html`.
