# PMI Standard for AI in PPPM Overview: How It Was Made

A plain-language walkthrough of the single-page overview of The Standard for Artificial Intelligence in Portfolio, Program, and Project Management (PMI, 2026). This explains what the page does, how it was built and verified, and how to recreate or adapt it yourself.

---

## 1. What it is

A self-contained HTML page (`index.html`, originally built as `PDU_ai-standard-pppm-overview_2026-07-12.html` in the PDU claim workflow) that summarizes PMI's first-edition AI standard (ANSI/PMI 26-007-2028) for project and program managers. Every claim carries an inline APA citation to the standard's printed page numbers, and every figure is an original SVG recreation adapted from the standard's diagrams.

The page has five layers:

| Layer | What it does |
|---|---|
| **Executive summary** | Three paragraphs on the standard's purpose, its three structural pillars (eight principles, five performance domains, a seven-phase AI life cycle), and the human-in-the-loop thread, followed by four stat tiles (8 / 5 / 7 / 3). |
| **Key points at a glance** | Ten linked cards, one per major section of the standard, each with a one-sentence summary and page range. |
| **Ten collapsible sections** | `details`/`summary` panels covering the standard end to end: core concepts, the eight principles, the five performance domains, life cycle tailoring, AI in portfolio / program / project management, the use framework, ethics and legal considerations, and the appendices. An Expand all / Collapse all toolbar sits above them. |
| **Six inline SVG figures** | Recreations of the principles hub (Fig. 2-1), performance domains (Fig. 3-1), portfolio heat map (Fig. 5-2), stakeholder evaluation quadrants (Fig. 5-9), the Seven Patterns of AI (Fig. 6-1), and a seven-phase life cycle pipeline tagged with each phase's dominant tailoring approach. |
| **Takeaways and references** | Eight cited takeaways for practitioners and an APA reference entry, plus a note that the figures are adaptations and the source copy is a licensed PMI member benefit for personal reference only. |

---

## 2. The build process, step by step

### Step 1: Source the standard

The source is the licensed PMI member PDF of the standard (276 pages, first edition, 2026). It is a member benefit marked not for distribution, so the overview was written as original prose that summarizes and cites the standard rather than reproducing its text, and the figures were rebuilt from scratch rather than extracted.

### Step 2: Map the structure before writing

The standard's skeleton was extracted first: eight principles (pp. 13-44), five performance domains (pp. 45-104), the seven-phase AI life cycle and tailoring guidance (pp. 105-140), the three PPPM-level chapters (pp. 141-202), the use framework (pp. 203-218), ethics and legal considerations (pp. 219-237), and the appendices (pp. 241-244). Each became one collapsible section, and the glance cards mirror the same map so the reader can jump by topic.

### Step 3: Write with citation discipline

Every paragraph cites the standard inline in APA author-date-page form, for example (PMI, 2026, pp. 17-18). Citations use the document's printed page numbers, not PDF sheet numbers. Where the overview compresses a list (threats, use cases, legal areas), the page range covers the full span in the source so a reader can verify the complete list.

### Step 4: Rebuild the figures analytically

Six figures were drawn as inline SVG with no renderer in the loop: node-and-hub diagrams for the principles and domains, two-by-two quadrant grids for the heat map and stakeholder evaluation, a ring for the Seven Patterns, and a left-to-right pipeline for the life cycle. The life cycle figure adds one editorial layer: an approach tag per phase (Hybrid, Adaptive, or Predictive) derived from the standard's own prose descriptions, with the caption stating that the tags summarize what the standard describes. Each figure carries a caption crediting the source figure number and page.

### Step 5: Verify against the source

A full accuracy review compared the finished page against the standard: bibliographic details (edition, ANSI designation, ISBN), the structural counts, every figure's fidelity to its source, and spot-checks of the page citations across all ten sections, the takeaways, and the reference entry. The review confirmed the content and citations were accurate, with only minor observations (internal section numbering differs from the standard's, one threat omitted from an illustrative list, and the life cycle tags noted as interpretation).

### Step 6: File it

The page was produced 2026-07-12 as a knowledge asset in the PDU claim workflow (`PDU_Claims/ai-standard-pppm_2026-07-12/`), then moved to `CC_Infographics/PMI-Standard-for-AI-in-PPPM/` after the accuracy review passed and renamed to `index.html` for a clean folder URL on the collection's GitHub Pages site.

### Step 7: Restyle to the collection's design language

The page was originally built in a blue report style (Segoe UI, white background, no dark mode). After moving into this collection it was restyled to the paper-and-ink field-guide style in `CC_Infographics/STYLE_GUIDE.md`: the shared CSS variable palette, mono uppercase eyebrows and section labels, 1.5px ink borders with hard offset shadows, a fixed top-right theme toggle with a no-flash head script and dark-mode overrides, stamp-red focus outlines, a print rule that forces the collapsibles open, and an MIT License footer. The six SVG figures were re-themed through CSS classes (ink hubs with paper text, paper-2 node boxes, a hardcoded stamp-red pill where cream text must stay high-contrast in dark mode) so they follow the theme toggle. All prose and citations were carried over unchanged, and a verification pass confirmed tag balance, zero em/en dashes, and no external resources beyond the LICENSE link.

---

## 3. The tech, in plain language

- **One HTML file.** Layout, styling, interactivity, and all six figures live in a single file. Open it in any browser; nothing to install.
- **No external resources.** No CDN scripts, web fonts, or remote images. The figures are inline SVG with `role="img"`, `aria-label`, and `<title>` elements, so they scale, print, and read aloud cleanly.
- **Native collapsibles.** The ten sections are `details`/`summary` elements, all collapsed by default. Expand all and Collapse all are two one-line JavaScript handlers, and a print rule forces every panel open on paper.
- **CSS variables for theming.** Light and dark are two sets of the same variable names. The theme toggle sets `data-theme` on the root element and stores the choice in localStorage; a two-line script in the head applies the stored theme before first paint so there is no flash. The SVG figures inherit the theme through CSS classes.
- **APA citations in plain HTML.** The reference list uses a hanging-indent class (`padding-left` plus negative `text-indent`), no list markup needed.

---

## 4. How to adapt it

- **New edition of the standard.** Page citations are the main maintenance surface. They are concentrated in the section summaries, figure captions, takeaways, and the reference entry; the prose itself describes durable concepts.
- **Different standard, same shell.** The executive summary, glance cards, collapsible sections, and takeaways pattern works for any long standard or handbook. Replace the section map and figures.
- **Restyle for another collection.** The palette, borders, and shadows are all declared once in `:root` (with dark-mode overrides under `[data-theme="dark"]`), and the SVG figures take their colors from shared CSS classes, so a re-skin means swapping one variable block.
- **Reuse the figures.** The hub-and-spoke, quadrant, ring, and pipeline SVG patterns fit any framework diagram; only labels and box positions change.

---

## 5. One-shot prompt to recreate this

```
Build a single self-contained HTML page (one file, no external resources, no
frameworks) that gives project and program managers a complete overview of
The Standard for Artificial Intelligence in Portfolio, Program, and Project
Management (PMI, 2026, first edition, ANSI/PMI 26-007-2028). Work only from
the attached licensed PDF: summarize in original prose, never reproduce the
standard's text, and cite every claim inline in APA form using the printed
page numbers, for example (PMI, 2026, pp. 17-18).

Structure:
1. Header with title, source line (PMI, 2026, first edition, ANSI
   designation), and preparation date.
2. Executive summary: the standard's purpose and technology-agnostic stance
   (p. 1); its three pillars: eight principles (pp. 13-17), five performance
   domains (pp. 45-47), and a seven-phase AI life cycle (pp. 107-108); the
   human-in-the-loop thread (pp. 11-12); and AI as tool vs. deliverable
   (pp. 7-10). Add four stat tiles: 8 principles, 5 domains, 7 phases,
   3 PPPM levels.
3. A "Key points at a glance" grid of ten cards, each linking to a section
   below with a one-sentence summary and page range.
4. Ten collapsible details/summary sections with an Expand all / Collapse
   all toolbar, covering: purpose and core concepts (pp. 1-12); the eight
   principles in a two-column layout (pp. 13-44); the five performance
   domains with their checking-results measures (pp. 45-104); life cycle
   management and tailoring (pp. 105-140); AI in portfolio management
   including the heat map, use cases, KPIs, and ESG (pp. 141-157); AI in
   program management from adoption role through challenges (pp. 158-181);
   AI in project management from planning through performance evaluation
   (pp. 181-202); the use framework with business case elements and tool
   selection criteria (pp. 203-218); ethical and legal considerations
   including the ethics oversight committee and nine legal areas as a table
   (pp. 219-237); and the appendices on maturity models and regulations
   (pp. 241-244).
5. Six inline SVG figures, each an original recreation with a caption
   crediting the source figure and page: the eight principles around a
   human-in-the-loop hub (Fig. 2-1, p. 14); the five performance domains
   around a central node (Fig. 3-1, p. 47); a seven-phase life cycle
   pipeline tagged Hybrid / Hybrid / Adaptive / Hybrid / Adaptive /
   Adaptive / Predictive per the standard's phase descriptions
   (pp. 108-116); the complexity-vs-potential heat map with Quick Wins,
   Strategic Pursuits, Neutral, and Deferred quadrants (Fig. 5-2, p. 145);
   the support-vs-expertise stakeholder grid with Advocates, Champions,
   Skeptics, and Critics (Fig. 5-9, p. 199); and the Seven Patterns of AI
   ring (Fig. 6-1, p. 206). Give each SVG role="img", an aria-label, and a
   title element.
6. A takeaways section: eight cited, action-oriented bullets for
   practitioners (start with value, keep humans in the loop, treat data
   quality as a deliverable, tailor the life cycle, prioritize with the
   heat map, segment stakeholders, build governance and ethics in from day
   one, measure continuously).
7. An APA reference entry with the ANSI designation and ISBN, plus a
   closing note that the figures are adaptations, the source is a licensed
   PMI member benefit, and the overview is for personal reference and not a
   substitute for the standard.

Design: paper-and-ink field-guide style. CSS variables for a warm paper
background, ink text, a stamp-red accent, and a seal-green secondary
accent, with dark-mode overrides under [data-theme="dark"]. Monospace
uppercase eyebrows and section labels, hard offset shadows (0 2px 0),
cards and stat tiles with 1.5px ink borders, a fixed top-right theme
toggle persisted in localStorage under the shared key "theme" with a
no-flash head script, stamp-red :focus-visible outlines, a centered
reading column around 760px, a print rule that forces the collapsibles
open, and an MIT License footer. Theme the SVG figures through CSS
classes (ink hubs with paper text, paper-toned node boxes, line-colored
connectors) so they follow the toggle, keeping any red pill at #B23A2E
with cream text so contrast holds in dark mode. System fonts only,
minimal inline JavaScript, everything in the one file.

After building, verify: HTML tag balance, zero em/en dashes, no external
resources beyond reference hyperlinks and the LICENSE link, and a
citation audit that spot-checks every page reference against the PDF,
remembering that printed page numbers differ from PDF sheet numbers.
```

---

## 6. Setting it up on Claude

1. **Open Claude.** Use [claude.ai](https://claude.ai), the desktop app, or Claude Code if you want the file written directly to disk.
2. **Attach the standard.** The build depends on the licensed PMI PDF; attach your member copy to the conversation. Without it, the model cannot verify page numbers and should not guess them.
3. **Paste the one-shot prompt** from Section 5 and send it.
4. **Run the accuracy review as a second pass.** Ask Claude to re-read the finished page against the PDF and check every citation; this caught the fixable nits in the original build.
5. **Iterate in plain language.** Section-by-section instructions work well: name the section, state the change, and ask for a verification pass after each edit.
