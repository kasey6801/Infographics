# PMI Talent Triangle Introduction: How It Was Made

A plain-language walkthrough of the single-page introduction to the PMI Talent Triangle. This explains what the page does, how it was built and verified, and how to recreate or adapt it yourself.

---

## 1. What it is

A self-contained HTML page that introduces the PMI Talent Triangle for project and program managers maintaining PMI certifications. For each of the three categories (Ways of Working, Power Skills, Business Acumen) it covers four things: what the category means, the specific skills PMI counts in it, where the PMBOK Guide develops it, and what typically qualifies for PDUs.

The page has eight sections:

| Section | What it does |
|---|---|
| **Header** | Title, dated eyebrow, and a one-paragraph statement of scope. |
| **What the Talent Triangle is** | History (2015 introduction, 2022 renames), the CCR handbook definition, how the triangle structures certification maintenance, and how the PMBOK Guide Eighth Edition maps to it. Four stat tiles summarize the key numbers. |
| **Figure 1** | An inline SVG of the triangle: rounded corners, a hub circle for the project professional, and a labeled chip at each corner with sample skills from PMI's official flyer. |
| **Three category sections** | Collapsible panels, one per category, each with an overview, a "was:" badge showing the pre-2022 name, skill chips, PMBOK anchors with page citations, and typical qualifying PDU content. The Power Skills panel includes a callout on organizational change management. |
| **Quick comparison** | A table pairing each category with its core question and its primary PMBOK anchors, plus a note on the edition cited. |
| **References** | APA reference list with hanging indents and live links. |

---

## 2. The build process, step by step

### Step 1: Draft the content

The page started as a summary of the three categories, written from the definitions used in PDU claim analysis: Ways of Working is the how of delivery, Power Skills is the people side, Business Acumen is the organizational context. It was then reframed as a general introduction with a fixed four-part treatment per category so the three sections read in parallel.

### Step 2: Ground every claim in primary sources

- **PMI CCR handbook (PDF).** The pmi.org HTML pages return HTTP 403 to automated fetching, but the `/-/media/` PDF URLs download normally. The handbook PDF was fetched and its text extracted with pypdf. Page 6 supplied the verbatim definition of the triangle and the rule that certification holders must earn a minimum number of PDUs in each skill area.
- **PMI Talent Triangle flyer (PDF).** Fetched the same way. It supplied the official skill lists used in the chips and in the Figure 1 corner labels.
- **Project Management Academy articles.** Three blog articles supplied the history (2015 introduction, 2022 renames) and supporting statistics.
- **Prosci.** The seven-times statistic in the change management callout came from Prosci research pages, gathered via site-restricted search because prosci.com also blocks direct fetching.
- **PMBOK Guide Eighth Edition (2025).** All PMBOK citations were verified against a licensed member copy. The Eighth Edition names the three categories directly: The Standard for Project Management lists ways of working, power skills, and business acumen among the competencies of the project management team (Standard pp. 29-31). Because the Standard and the Guide are paginated separately in one volume, citations mark Standard or Guide pages.

### Step 3: Apply the CC_Infographics design language

The page follows the field-guide style defined in `CC_Infographics/STYLE_GUIDE.md`: a paper-and-ink palette driven by CSS variables, monospace uppercase eyebrows and labels, hard offset shadows, cards with 1.5px ink borders, a fixed top-right theme toggle with a no-flash script in the head, and dark-mode overrides under `[data-theme="dark"]`. System fonts only, no external resources, no em dashes anywhere.

### Step 4: Build Figure 1 analytically

There was no renderer in the loop, so the SVG geometry was computed rather than eyeballed:

- The triangle corners are rounded with quadratic Bezier segments on a single path.
- The hub circle sits at the triangle's incenter, not its centroid, so the gap to all three sides is even.
- Text fit inside the circle was checked by comparing chord widths against estimated text widths at each line's vertical position.
- Corner chips carry a duplicate offset rectangle as a hard shadow, matching the card shadow style.

Screenshot feedback drove two fixes: enlarging the hub circle when text overflowed it, and moving the circle from centroid to incenter when it touched the base.

### Step 5: Edit turn by turn, with a human in the loop

The refinement phase ran as a structured edit session rather than a single rewrite, and it was human-directed throughout: the author reviewed the rendered page, decided what to change and in what order, and issued one instruction at a time. The tool never queued up its own list of improvements or edited ahead of an instruction. The mechanics:

- **A numbered section list as the shared map.** The document was divided into eight numbered sections (header, "What the Talent Triangle is" card, Figure 1, the three category sections, the comparison card, references). The list was re-presented after every turn, so the author always had the full menu in front of them and could name the next target by number: "2. Change this to a bulleted list", "5. Shorten this callout".
- **One section, one change per turn.** Each human instruction stated the section and the change. The edit was applied with a targeted find-and-replace against the file, not a regeneration, so every other section stayed byte-identical and nothing changed that the author had not asked for.
- **Standing conventions enforced on every edit.** Four rules applied regardless of what the instruction asked for: the CC_Infographics style guide, no em dashes, APA citation accuracy (if a content change made a citation wrong, the citation was fixed in the same turn), and no external resources beyond reference hyperlinks.
- **Verification before reporting, human judgment after.** The script in Step 6 ran after every edit. The report back was one line: what changed plus the verification result, followed by the section list. The author then inspected the result in the browser and either accepted it, refined the same section again, or moved on. Several sections took multiple rounds before they passed human review; the caption of Figure 1 was revised for readability twice, and two category sections were re-tightened after a first rewrite still read too soft.

The turns covered several kinds of work:

- **Wording and tone.** Phrase swaps ("This introduction gives one page to each" became "This document provides an overview"), converting prose to bulleted lists, and repeated tightening under the instruction "less flowery language, make it shorter and fact based".
- **Citation upgrades.** A secondary blog citation for the triangle's definition was replaced with the primary source, which meant fetching the CCR handbook PDF mid-turn, extracting its text, and locating the definition on page 6 before editing.
- **Figure fixes from screenshots.** The user pasted screenshots of Figure 1 twice. The first showed text overflowing the hub circle (fixed by enlarging the radius and repositioning the lines); the second showed the circle touching the triangle base (fixed by moving the center from the centroid to the incenter, with clearances recomputed).
- **Structural additions and trims.** The change management callout was added in one turn with its Prosci citation, then shortened to four sentences in a later turn when it read too long.
- **The edition realignment.** When the licensed Eighth Edition text was attached, one pass updated the structure paragraph, all three PMBOK anchor paragraphs, the comparison table, the caveat note, and the reference entry, replacing model-knowledge page numbers with verified ones.
- **Presentation defaults.** Late turns collapsed the category sections on initial load and removed the PMP minimum column from the comparison table.

The payoff of the format: small, reviewable diffs; a verification pass on every change instead of only at the end; and a human, not the tool, deciding the order, scope, and acceptance of every change. The automated checks caught mechanical faults; the judgment calls on tone, accuracy, and layout stayed with the person reading the page.

### Step 6: Verify after every edit

A small Python script ran after each change:

- **Tag balance** via `html.parser` with a void-element list that includes SVG primitives, so a missing closing tag is caught immediately.
- **Dash scan** with a regex for em and en dashes; the count must be zero.
- **External-resource scan** confirming the only outbound URLs are reference hyperlinks.
- **Word count** to keep the page within its one-to-three-page target.

---

## 3. The tech, in plain language

- **One HTML file.** Layout, styling, interactivity, and the figure all live in a single file. Open it in any browser; nothing to install.
- **No external resources.** No CDN scripts, web fonts, or remote images. The figure is inline SVG, so it themes with the page and prints cleanly.
- **CSS variables for theming.** Light and dark are two sets of the same variable names. The theme toggle sets `data-theme` on the root element and stores the choice in localStorage; a two-line script in the head applies the stored theme before first paint so there is no flash.
- **Native collapsibles.** The category sections are `details`/`summary` elements. The Expand all and Collapse all buttons are a few lines of JavaScript. A print rule forces all panels open on paper.
- **APA citations in plain HTML.** Hanging indents come from a `padding-left` plus negative `text-indent` class, no list markup needed.

---

## 4. How to adapt it

- **Update the edition.** When a new PMBOK edition ships, the citations are concentrated in four places: the structure paragraph in section 2, the "Where the PMBOK develops it" paragraph in each category section, the comparison table, and the reference list.
- **Swap the audience.** The "Typical qualifying PDU content" blocks are the most role-specific text. Rewrite them for a different certification or discipline and the rest of the page stands.
- **Reuse the figure.** The SVG triangle-plus-hub pattern works for any three-part model. Change the chip labels and corner skill lines; the geometry does not need to move.
- **Reuse the verification script.** The tag-balance and dash checks apply to any self-contained HTML document in this repository.

---

## 5. One-shot prompt to recreate this

```
Build a single self-contained HTML page (one file, no external resources, no
frameworks) introducing the PMI Talent Triangle for project and program
managers maintaining PMI certifications.

Ground the content in primary sources before writing:
- The PMI CCR handbook PDF (pmi.org /-/media/ URL) for the official definition
  of the triangle and the per-category PDU minimums. Note that pmi.org HTML
  pages block automated fetching but the PDF URLs work.
- The PMI Talent Triangle flyer PDF for the official skill lists.
- The PMBOK Guide Eighth Edition for where each category is developed, citing
  verified page numbers and marking Standard vs. Guide pages since the two
  parts are paginated separately.
- Project Management Academy articles for the 2015 introduction and 2022
  renames (Technical Project Management to Ways of Working, Leadership to
  Power Skills, Strategic and Business Management to Business Acumen).

Structure:
1. Header with a dated eyebrow label and a one-paragraph scope statement.
2. A card on what the Talent Triangle is: history, the handbook definition
   quoted with citation, the 2022 renames as a bulleted list, how the triangle
   structures certification maintenance, how the PMBOK Eighth Edition maps to
   it, and four stat tiles (3 categories, 8/8/8 PMP minimums, 2015, 2022).
3. An inline SVG figure: a rounded-corner triangle, a hub circle at the
   incenter labeled "The project professional", and a labeled chip at each
   corner with two lines of sample skills from the flyer. Include a caption
   with citations.
4. Three collapsible sections (details/summary), collapsed by default, with
   Expand all / Collapse all buttons. Each section: an "Overview:" line, a
   badge showing the pre-2022 name, a paragraph on scope, skill chips from the
   official lists, a "Where the PMBOK develops it" paragraph with page
   citations, and a "Typical qualifying PDU content" paragraph. In Power
   Skills, add a brief callout on organizational change management citing
   Prosci's finding that initiatives with excellent change management are
   seven times more likely to meet objectives.
5. A quick comparison card: table of category, core question, and primary
   PMBOK anchors, plus a one-line note naming the edition cited.
6. An APA reference list with hanging indents and live links
   (target="_blank" rel="noopener").

Design: paper-and-ink field-guide style. CSS variables for a warm paper
background, ink text, a stamp-red accent, and a seal-green secondary accent.
Monospace uppercase eyebrows, hard offset shadows (0 2px 0), cards with 1.5px
ink borders, dark mode via [data-theme="dark"] overrides, a fixed top-right
theme toggle persisted in localStorage with a no-flash head script, and a
print rule that forces the collapsibles open. System fonts only.

Rules: APA citations throughout, no em dashes anywhere, plain factual
language, and everything self-contained in the one file. After building, run
a verification pass: HTML tag balance, zero em/en dashes, and no external
resources beyond reference hyperlinks.
```

---

## 6. Setting it up on Claude

1. **Open Claude.** Use [claude.ai](https://claude.ai), the desktop app, or Claude Code if you want the file written directly to disk.
2. **Pick a capable model.** A strong model handles the SVG geometry and the citation discipline in one pass.
3. **Enable web fetching.** The build depends on pulling the PMI PDFs; without fetch access, supply the handbook and flyer text yourself.
4. **Paste the one-shot prompt** from Section 5 and send it.
5. **Verify the citations.** If you hold a PMI membership, check the PMBOK page ranges against your licensed copy before quoting them formally; page numbers differ between editions and the Standard and Guide restart pagination.
6. **Iterate in plain language.** Section-by-section instructions work well: name the section, state the change, and ask for a verification pass after each edit.
