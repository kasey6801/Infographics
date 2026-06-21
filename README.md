# Infographics

A collection of self-contained infographics. Each guide is
a single HTML page with **no external dependencies** — no CDN links, no web fonts (system
font stack only), no trackers, and all CSS inline. Every page works offline and stands on
its own.

**Live site:** https://kasey6801.github.io/Infographics/

## Pages

- `index.html` — landing page linking to all guides
- `warp-isp-overview.html` — *What can your ISP see? — an overview of Cloudflare WARP*
- `gemini-onboarding-dashboard.html` — *Your Gemini Starter Dashboard (Marketing & Sales)*
- `about.html` — about the collection and its design principle

## "Explained" files

Each infographic ships with a companion `*explained*.md` file that documents how it was
built. These files lay out the step-by-step process used to create the guide, plus a
**one-shot prompt** you can use to re-create the infographic from scratch.

- `Cloudflare Warp Field Guide Explained.md` — accompanies `warp-isp-overview.html`
- `Gemini-dashboard-explained.md` — accompanies `gemini-onboarding-dashboard.html`

On the landing page, each guide's card links to its explained file via the
"How this was made (.md) →" link (which opens GitHub's rendered view).

## Local preview

```bash
python3 -m http.server 8000
# then open http://localhost:8000/
```

Or just open `index.html` directly in a browser.
