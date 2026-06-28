# Infographics Style Guide

The shared design language for this collection: a warm "field guide" look (paper and ink),
built as fully self-contained single HTML files. Distilled from the landing page, the WARP
"What can your ISP see?" guide, and the Book Club guide. Use it as the default for new pages.

## Hard rules (non-negotiable)

- **One self-contained HTML file.** No CDN links, no external scripts or stylesheets, no web
  fonts, no analytics/trackers/runtime network calls. Inline CSS and minimal inline JS only.
  If a library is unavoidable (for example Chart.js), vendor it inline into a `<script>`.
- **System font stack only. Never Google Fonts.**
- **No em dashes** in prose. Use commas, colons, periods, or middots.
- **MIT License footer** on every page, linking to the repo `LICENSE`.
- **Accessibility:** semantic HTML, `aria-label` on icon buttons, visible `:focus-visible`
  outlines (stamp-red), strong contrast.
- **External links:** `target="_blank" rel="noopener"`. Consider
  `<meta name="referrer" content="no-referrer">`.

## Palette

Warm paper-and-ink tones, declared as CSS variables in `:root`.

```css
:root{
  --sans:system-ui,-apple-system,"Segoe UI",Roboto,Helvetica,Arial,sans-serif;
  --mono:ui-monospace,"SF Mono",Menlo,Consolas,"Liberation Mono",monospace;
  --paper:#E9E1CF;     /* page background        */
  --paper-2:#E1D8C2;   /* secondary surface      */
  --card:#F3EDDD;      /* card surface           */
  --ink:#1C1A15;       /* text, bold borders, inverted-panel backgrounds */
  --ink-soft:#4A463C;  /* muted text             */
  --line:#C5BAA0;      /* subtle borders/dividers*/
  --stamp-red:#B23A2E; /* primary accent: links, eyebrows */
  --seal-green:#2E6149;/* secondary accent: .md links, "safe" */
  --warp:#E8590C;      /* optional sparing brand-orange */
  --shadow:0 2px 0 rgba(28,26,21,.14); /* hard "printed" offset shadow */
}
```

The `body` sits on `--paper` with two faint radial gradients: a soft white highlight at the
top-left and a soft dark tint at the bottom-right.

## Typography and layout

- **Mono, uppercase, wide tracking** (letter-spacing ~`.12-.18em`) for eyebrows, section
  labels, tags, and code.
- **`h1`** heavy (800-900 weight), responsive with `clamp()`, tight letter-spacing. Body
  `line-height` 1.55-1.6.
- **Centered `.wrap`:** `max-width` ~720-760px for reading pages, ~980px for galleries and
  the landing page; `padding:0 22px`.
- Bold **1.5px `var(--ink)` borders** plus the hard offset shadow create the field-guide feel.

## Components

- **Cards:** `var(--card)` or `--paper-2` background, 1.5px border, radius 6-10px, hard shadow.
  Hover lifts with `translateY(-2px)` and a larger offset shadow.
- **Card grid:** `repeat(auto-fill,minmax(280px,1fr))` with `align-items:stretch`. Make cards
  equal height (`li{display:flex}` + `.card{flex:1}`) and pin the action row to the bottom
  (`margin-top:auto`). Cap descriptions at four lines with `-webkit-line-clamp:4` so cards stay
  uniform. **Never shrink the font to make a card fit; cap the text instead.**
- **Card action links:** a mono `.82rem` row with a top-border divider, for example
  `Open guide →` (stamp-red) and `How this was made (.md) →` (seal-green). Link `.md` writeups
  to the GitHub rendered blob view, not the raw file.
- **Inverted dark panels** (terminal, logbook, emphasis blocks): `background:var(--ink)` with
  `color:var(--paper)`. Keep these dark in dark mode.
- **CTA buttons and numbered-step circles:** stamp-red background with cream (`#F3EDDD`) text.
- **Code:** inline `code` with a faint tinted background; `pre` blocks dark
  (`#1C1A15` background, `#E9E1CF` text) in both themes.

## Dark mode (standard on every page)

Override the variables under `[data-theme="dark"]` on `<html>`:

```css
[data-theme="dark"]{
  color-scheme:dark;
  --paper:#17150F; --paper-2:#1F1B14; --card:#221E17;
  --ink:#ECE3D0;   --ink-soft:#B3AA96; --line:#403829;
  --stamp-red:#E08A7C; --seal-green:#69B294;
  --shadow:0 2px 0 rgba(0,0,0,.45);
}
```

- Fix hardcoded literals in dark: tone the body white-gradient highlight down to about `.04`,
  flip any hardcoded light backgrounds to dark surfaces and dark-on-light text to light, and
  keep red **button** backgrounds at `#B23A2E` so cream text stays high-contrast.
- **Toggle:** a fixed top-right `#theme-toggle` button (mono) showing `🌙 Dark` / `☀️ Light`.
- **No flash:** a head `<script>` sets the theme before paint from `localStorage('theme')`,
  falling back to `matchMedia('(prefers-color-scheme: dark)')`. The click handler flips
  `data-theme` and persists to `localStorage` under the shared key **`theme`**, so the whole
  site stays in sync. Light is the absence of `data-theme`; dark is `data-theme="dark"`. A
  deliberately dark-first page (for example a developer dashboard) may invert this default.
- **Chart.js:** canvas colors are set in JavaScript and CSS cannot reach them. Use neutral
  axis and grid colors that read in both themes, or re-render the charts on toggle.

## Project conventions

- Each infographic lives in its own folder as `index.html` plus a companion explainer `.md`,
  giving a clean URL such as `/warp-isp-overview/`.
- The landing page (`index.html` at the repo root) groups guides into labelled sections and
  links to each folder with relative links.
- `.gitignore` is kept local only and is not tracked in this repo.
