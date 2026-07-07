# Context & Rebuild Guide — "NATO Defense Spending Through a Canadian Lens"

> **Purpose of this document.** This is the complete context, architecture, data, and editorial-decision record for the report titled **"NATO Defense Spending Through a Canadian Lens"** (an analysis of Canadian military procurement 2025–2026 viewed through the lens of NATO defence-spending obligations). It exists so the project can be handed to **Claude Code** for ongoing update and maintenance on GitHub. It accompanies two deliverables:
>
> - `NATO_Defence_Spending_Canadian_Lens.html` — the rendered, interactive report (self-contained single file)
> - `NATO_Defence_Spending_Canadian_Lens.md` — the markdown source of the report's prose/tables
>
> This guide contains: (1) what the project is, (2) the full technical architecture, (3) all validated data and its sources, (4) every editorial decision and convention established, (5) the complete build/QA procedure, and (6) **a one-shot recreation prompt** at the end.

---

## 1. Project Overview

### What it is
A thorough analytical **overview** of Canada's 2025–2026 military procurement wave (~C$81.8B Budget 2025 commitment; NATO 5%-of-GDP-by-2035 pledge), framed through NATO defence-spending obligations. Its purpose is **to inform understanding, not to drive specific decisions**. It is **not professional investment advice** and not a recommendation to take any action; it is policy/defence analysis with an investment-landscape section that descriptively maps publicly-traded exposure and catalysts (explicitly framed as "could affect sentiment," never "should buy/sell"). Treat it as a briefing that surfaces facts, trends, and the general flow of capital — readers draw their own conclusions and, for any financial decision, consult a licensed advisor.

### Knowledge cutoff / temporal anchor
The report is written as of **~June 26, 2026**. Key live event at time of writing: the **CPSP submarine decision** (Canadian Patrol Submarine Project) expected late June / early July 2026, before the NATO Summit on **July 7, 2026**. The two finalists are **TKMS** (German Type 212CD) and **Hanwha Ocean** (South Korean KSS-III). This binary decision drives much of the analysis.

### Audience & tone
Informed defence-policy/finance reader. Rigorous, neutral, evenhanded. Honest about uncertainty (signed contract vs. preferred supplier vs. still-in-definition). British/Canadian spelling ("Defence") in body, though the **title** uses US "Defense" (deliberate — keep as-is unless instructed).

### Two output formats, one source of truth
The **markdown** is the canonical prose/table source. The **HTML** is generated from the markdown via a Python build (markdown library + surgical string injection) and then enhanced with hand-built interactive components (charts, tabbed table, nav) that markdown cannot express. **When editing: change the markdown, then re-apply the HTML enhancements — or edit both surgically in parallel** (the approach used throughout this project's later edits).

---

## 2. Document Structure (section order)

Both files share this section order. Each section has a theme emoji in **both** the heading and its "Jump to" nav link.

| # | id | Nav label | Heading |
|---|-----|-----------|---------|
| 1 | `tldr` | 📋 Overview | 📋 TL;DR |
| 1b | (no id) | — | ⚠️ Key Caveats (moved from below the Timeline to directly below the TL;DR, July 6, 2026; its stale "CPSP unresolved" line was corrected in the same move) |
| 2 | `invest` | 💹 Investment Landscape | 💹 Investment Landscape: Publicly-Traded Exposure to the Portfolio |
| 3 | `latest` | ⚡ Latest | ⚡ Latest Developments — June 26, 2026 |
| 3b | `framing` | (not in nav) | 📌 Key Strategic Framing: The Baseline and the Contested 2% (added July 6, 2026 — gives the formerly untitled baseline/2%-accounting bridge between the Latest blocks and the Arctic section its own h2 so it participates in section collapsing; the June 28 Latest note's "Key Strategic Framing section below" cross-reference now resolves to it) |
| 4 | `arctic` | ❄️ Arctic & Dual-Use 1.5% | ❄️ Arctic & Dual-Use Infrastructure: The 1.5% Question |
| 5 | `table` | 🗂️ Domain Summary Table | 🗂️ Domain Summary Table |
| 6 | `natospend` | 📊 NATO Spend | 📊 NATO Defence Spending by Country (% of GDP, 2020–2026) |
| 7 | `spend` | 💰 CAD Spend Review | 💰 Defence Spend Destination Review (CAD) |
| 8 | `euspend` | 🇪🇺 EU Spend Overview | 🇪🇺 EU Spend Overview: The Capital Flow of European Rearmament |
| 9 | `programs` | 🎯 Program Detail | 🎯 Program-by-Program: Capability and Integration Analysis |
| 10 | `synthesis` | (not in nav) | 🧭 Synthesis: Alliance Integration — The Big Picture |
| 11 | `europe` | (not in nav) | 🇪🇺 European-Language Perspectives: What Allies Are Saying |
| 12 | (md only) | — | 🍁 Regional Impact Overview |
| 13 | (md only) | — | 🪶 Indigenous and Reconciliation Dimensions |
| 14 | `timeline` | 🗓️ Timeline | 🗓️ Program Timeline |
| 16 | `tracker` | 🔗 Live Sources | 🔗 Live Source Tracker |
| 17 | `reading` | (not in nav) | 📚 Further Reading (APA 7th Edition) |

**Theme emoji map:** 📋 overview · 💹 investment · ⚡ latest · ❄️ Arctic · 🗂️ table · 📊 NATO chart · 💰 CAD spend · 🇪🇺 EU · 🎯 programs · 🧭 synthesis · 🍁 regional (Canada) · 🪶 Indigenous · 🗓️ timeline · ⚠️ caveats · 🔗 sources · 📚 reading.


---

## 3. Technical Architecture (HTML)

The HTML is a **single self-contained file** (~228KB) with no external dependencies except Chart.js from a CDN. Structure top-to-bottom:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset> <meta viewport> <title>NATO Defense Spending Through a Canadian Lens</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"
          crossorigin="anonymous" referrerpolicy="no-referrer"></script>   <!-- in HEAD, loaded once -->
  <style> ... all CSS, including CSS custom properties ... </style>
</head>
<body>
  <header class="doc-header"> eyebrow + h1 (title) + subtitle </header>
  <nav class="section-nav"> sticky "Jump to" bar with scrollToId() links </nav>
  <main><div class="container"> ...all sections (rendered from markdown + injected components)... </div></main>
  <script> spend-destination doughnut chart (IIFE) </script>
  <script> NATO spending bar chart (IIFE) </script>
  <script> toggle/nav script: scrollToId() + domain-table setView() + global anchor click handler (IIFE) </script>
</body>
```

### CSS custom properties (design tokens)
Defined in `:root`. Use these for any new styled elements (do not hardcode hex):
`--navy --blue --blue-lt --teal-dk --teal-lt --mint-lt --mint-dk --amber --amber-mid --amber-lt --green --red --slate --muted --text --bg --bg-alt --border`

Palette intent: navy/blue = primary/structure; teal = allied-operators; amber/mint = economic-impact & US-alternative column tints; red = Canada highlight & warnings.

### Key CSS rules to know
- `.section-nav{position:sticky;top:0;z-index:100}` — the sticky nav. **This is why scroll offset matters.**
- `h2[id],div[id],section[id]{scroll-margin-top:56px}` — makes anchored jumps clear the sticky nav.
- `main>.container>h2:not(.latest-header)` — boxed section headers (added July 6, 2026): #EBF4FF background, 1px #C7DBF4 border, 4px `var(--blue)` left accent, 4px radius, same shape as `h2.latest-header` (which keeps its amber palette). #EBF4FF/#C7DBF4 are user-specified accents outside the token list; each section keeps its own theme emoji.
- `.callout` / `.callout-kf` (key finding) / `.callout-warn` (red, investment disclaimer) / `.callout-note` — blockquotes become these.
- `.data-table` — applied to every `<table>`.
- `.icon-win`/`.icon-loss`/`.icon-warn` — wrap ✅/❌/⚠️ inline icons.
- Domain-table tab system classes: `.table-controls .tc-btn`, `.col-economic .col-us .col-origin .col-allied` (column tints).
- NATO chart card: `.natochart-card .ncc-controls .ncc-btn .ncc-canvas-wrap .ncc-legend .ncc-leg-item .ncc-sw .ncc-dash .ncc-note .ncc-sources .ncc-method`.

### The four `<script>` IIFEs (all balanced; A/B guard `typeof Chart`)

**A. Spend-destination doughnut** (`spendChart2` canvas, `setScenario2()` global)
- Toggle between TKMS and Hanwha CPSP scenarios.
- Data hardcoded in a `sc` object: each scenario has labels `l`, percentages `p`, C$B values `v`, plus `cs`/`fs` summary stats.
- Has `safeDraw()` fallback if Chart.js fails to load.

**B. NATO spending horizontal bar chart** (`natoChart` canvas, `natoSetYear()` global)
- 7-year toggle: 2020/2021/2022/2023/2024/2025/2026(est.).
- Data hardcoded in a `NATO` array (16 countries, each with `name`, `flag`, `isCanada`, and `y2020`–`y2026`).
- **Canada bars rendered red (#D62828), all others blue (#1B4F8A).**
- **Bars re-sort high→low by the selected year on every toggle** (sorts a copy: `NATO.slice().sort((a,b)=>b[yearKey]-a[yearKey])`).
- Custom `thresholdPlugin` (Chart.js plugin, `afterDatasetsDraw`) draws **2% and 3.5% dashed lines ON TOP of bars**, with label chips pinned to the **top** of the plot area (in a 28px `layout.padding.top` strip) so they never collide with the x-axis. Labels are horizontally clamped to stay inside the canvas.
- x-axis titled "Defence spending (% of GDP)"; max 5.8.

**C. Toggle/nav script** (defines `scrollToId()` FIRST, then domain-table `setView()`, then a global click handler)
- `scrollToId(id)` measures live `.section-nav` height and scrolls with that offset + 8px.
- `setView(name)` toggles the domain table's 3 column views via `data-view` attributes and `Set` membership.
- Global click handler catches any in-body `<a href="#...">` and routes through `scrollToId`.

**D. Section-collapse IIFE** (added July 6, 2026; generalized to ALL sections same day; sits after script C, before the footer)
- Runtime progressive enhancement: at load, walks every `h2` that is a direct child of `main > .container` (`:scope > h2` — every document section, including each dated Latest-Developments block), moves its following siblings up to the next `h2` into a generated `<div class="sec-body">`, adds class `sec-toggle` to the h2, and makes it a click/Enter/Space toggle with `role="button"`, `tabindex="0"`, `aria-expanded`, `aria-controls`.
- **Default state (per user, July 6, 2026):** every section starts COLLAPSED except `h2#tldr` — the page loads as the TL;DR plus a scannable list of boxed section titles. (`id="latest"` no longer affects defaults; it remains the nav anchor, and nav jumps auto-expand their target.) A `.collapsed` class on both h2 and body drives the CSS (`.sec-body.collapsed{display:none}` + inline rotating `▾` chevron via `h2.sec-toggle::after`, amber-tinted on latest headers).
- Injects **"Expand all / Collapse all"** links (`span.nav-tools`) at the right end of the sticky nav.
- Monkey-patches `window.scrollToId` so nav/anchor jumps into a collapsed section expand it first.
- **The `/cad-defense-update` authoring format is intentionally unchanged** — updates keep writing plain `h2.latest-header + <p>… + callout-kf + <hr />` blocks; the IIFE wraps whatever it finds, so new dated blocks become collapsible automatically. Every section must have an `h2` title to participate (the `framing` h2 was added for this reason). No-JS and print render fully expanded (`@media print` forces `.sec-body{display:block}` and hides the chevron).

### Domain Summary Table (the most complex component)
- 11 columns × 23 data rows. **Verified aligned: 11 `<col>` = 11 `<th>` = 11 `<td>`/row, all 23 rows uniform.**
- Markdown CANNOT render this (it uses `\|` escapes that break the table extension), so it is **hand-built HTML injected at build time**, NOT produced by the markdown converter.
- 3 toggle views via `setView()`: **Platform Overview** / **Canadian Economic Impact** / **Competitive Intelligence**. Each `<col>`, `<th>`, `<td>` carries a `data-view` attribute (`always`/`platform`/`economic`/`competitive`/`both`); JS shows/hides by Set membership.
- Column 0 (the "#" column) intentionally has `data-view="always"`.
- Color-coded columns: economic (mint/green tint), US-alternative (amber tint), country-of-origin (blue tint), allied-operators (teal tint).
- Inline icons: ✅ = US selected for that program; ❌ = US lost; ⚠️ = unverified.


---

## 4. All Validated Data + Sources

### 4.1 NATO defence spending as % of GDP (the bar chart dataset)
16 countries, 2020–2026. **2020–2023 = NATO published actuals; 2024–2025 = NATO estimates; 2026 = budgeted/projected (subject to revision).** Sorted high→low by selected year in the chart.

| Country | 2020 | 2021 | 2022 | 2023 | 2024 | 2025 | 2026(est) |
|---|---|---|---|---|---|---|---|
| 🇵🇱 Poland | 2.37 | 2.34 | 2.42 | 3.90 | 4.12 | 4.48 | 4.70 |
| 🇱🇹 Lithuania | 2.13 | 1.96 | 2.47 | 2.54 | 2.85 | 4.00 | 5.25 |
| 🇪🇪 Estonia | 2.33 | 2.01 | 2.16 | 2.73 | 3.43 | 3.38 | 5.40 |
| 🇱🇻 Latvia | 2.27 | 2.16 | 2.07 | 2.27 | 3.15 | 3.73 | 4.00 |
| 🇳🇴 Norway | 1.84 | 1.66 | 1.57 | 1.67 | 2.20 | 3.30 | 3.35 |
| 🇺🇸 United States | 3.69 | 3.46 | 3.45 | 3.49 | 3.21 | 3.20 | 3.22 |
| 🇬🇷 Greece | 2.80 | 3.59 | 3.54 | 3.01 | 3.08 | 3.10 | 3.10 |
| 🇩🇰 Denmark | 1.49 | 1.40 | 1.39 | 1.65 | 2.37 | 2.65 | 3.20 |
| 🇬🇧 United Kingdom | 2.41 | 2.24 | 2.21 | 2.33 | 2.33 | 2.60 | 2.65 |
| 🇫🇮 Finland | 1.49 | 1.78 | 1.74 | 2.45 | 2.41 | 2.60 | 2.90 |
| 🇩🇪 Germany | 1.41 | 1.34 | 1.39 | 1.52 | 2.12 | 2.30 | 3.20 |
| 🇳🇱 Netherlands | 1.46 | 1.42 | 1.44 | 1.59 | 2.05 | 2.20 | 2.50 |
| 🇫🇷 France | 2.01 | 1.95 | 1.89 | 1.90 | 2.06 | 2.15 | 2.25 |
| **🇨🇦 Canada** | **1.42** | **1.32** | **1.27** | **1.38** | **1.45** | **2.00** | **2.10** |
| 🇮🇹 Italy | 1.57 | 1.54 | 1.51 | 1.46 | 1.49 | 2.00 | 2.05 |
| 🇪🇸 Spain | 1.02 | 1.01 | 1.09 | 1.26 | 1.28 | 2.00 | 2.10 |

**Sources:** NATO Defence Expenditure of NATO Countries (2014–2025, pub. Aug 2025); SIPRI Military Expenditure Database (Apr 2026); European Parliament EU Member States' Defence Budgets (Mar 2026); UK Gov International Defence Statistics Bulletin 2025; national budgets for 2026. Iceland excluded (no armed forces). Note: pre-2024 figures are periodically restated by NATO against revised GDP; 2026 blends confirmed budgets (Germany, France) with stated targets (Baltics).

### 4.2 CAD Spend Destination — the 22-program portfolio (doughnut chart + per-program table)
Total portfolio **~C$252B** (lifecycle/full-program basis). The CPSP (~C$90B, 36% of portfolio) is a **binary** TKMS-vs-Hanwha scenario that swings ~C$32–36B between Continental Europe and South Korea.

**Aggregate by destination (the doughnut chart data):**

| Destination | TKMS scenario | Hanwha scenario |
|---|---|---|
| Canada | ~C$131B (~52%) | ~C$129B (~51%) |
| Continental Europe | ~C$44B (~17%) | ~C$8B (~3%) |
| USA | ~C$40B (~16%) | ~C$40B (~16%) |
| UK | ~C$16B (~6%) | ~C$22B (~9%) |
| Scandinavia | ~C$15B (~6%) | ~C$6B (~2%) |
| South Korea | — | ~C$39B (~15%) |
| Australia | ~C$2B (~1%) | ~C$2B (~1%) |
| Other | ~C$4B (~2%) | ~C$6B (~3%) |

**Key finding:** The USA's ~16% is a *floor* held up by 4 programs where no non-US alternative existed (F-35 72% US, P-8A 80%, MQ-9B 87%, HIMARS 82%). Every program where Canada had a real choice went non-US. Canada retains ~51–52% via ITB obligations + National Shipbuilding Strategy.

The full 22-row per-program table (with Canada/USA/UK/Continental Europe/Scandinavia/South Korea/Australia/Other splits + key assumptions per program) is in the markdown under "Defence Spend Destination Review (CAD)". Programs include: CPSP (submarines), River-class destroyers (~C$80B), JSS, Polar Icebreakers, F-35A, GlobalEye, MQ-9B, P-8A, A-OTHR, CAFCYBERCOM, ESCP-P, IFM howitzers, GBAD, CMAR, LVM, pistols, C19 remediation, DAME, HIMARS, CC-295, CC-330, M-346.

### 4.3 EU Spend Overview (the European rearmament capital-flow section)
Reframes from program-destinations to **funding architecture** (loan capacity authorised, much not yet contracted). Figures are directional/designed-intent, not settled.

Key validated figures:
- **ReArm Europe / Readiness 2030** (Mar 2025): unlocks **>€800B** over 4 years. 5 pillars: SAFE + National Escape Clause + cohesion redirect + EIB + private capital.
- **SAFE (Security Action for Europe):** **€150B** EU-backed loans, in force 29 May 2025. **Hard 35% non-EU component cap** (i.e. ≥65% EU/EEA-EFTA/Ukraine content). Joint procurement required. 45-yr loans, 10-yr grace. **19 states subscribed.**
- **SAFE allocations:** Poland **€43.7B** (largest; first €6.6B paid 29 May 2026; ~89% to Polish industry; 120+ projects), Romania €16.7B, France €15–16.2B, Hungary €16.2B, Italy €14.9B (*allocated but NOT signed as of May 2026*), Belgium €8.3B, Lithuania €6.37B, Portugal €5.84B, Latvia €3.49B, Bulgaria €3.26B, Estonia €2.34B, Slovakia €2.31B, Croatia €1.7B, Cyprus €1.18B, Finland/Spain €1B each, Greece €787M, Denmark €46.8M. **Germany did NOT apply** (own capital access + €100B fund). First 5 to sign: Poland, Lithuania, Croatia, Romania, Belgium.
- **EDIP (EU Defence Industry Programme):** €1.5B grants 2025–27 (incl. €300M Ukraine), adopted 8 Dec 2025; targets air defence, counter-drone, ammunition.
- **EU-27 total defence spend:** €218B (2021) → €343B (2024, +19%, =1.9% GDP) → ~€381B (2025) → projected >€450B by 2027. Joint procurement only ~18% (vs 35% EDA benchmark).
- **US-shift evidence:** Germany's 154 major 2025–26 purchases = only **8% to US** (was a top US client); Rheinmetall ~€88B pipeline. Poland pairs SAFE with heavy US buys (32 F-35A, Abrams, Patriot) from *national* budget. Co-production on EU soil counts toward 65%: Rheinmetall–Lockheed missile JV (DE, 10,000/yr), COMLOG (MBDA–Raytheon) Patriot, Diehl–Lockheed PAC-3 MSE.
- **2030 target:** 55% of weapons from European/Ukrainian makers; **40% joint procurement by 2027.**
- **Canada–SAFE:** Canada is first non-European country to participate (endorsed 19 Dec 2025; bilateral agreement concluded 15 June 2026).

**Key finding:** The shift from US procurement is real, structural, deliberate — but it's a redirection of the *marginal* euro, not decoupling. US displaced from the commodity middle (artillery, vehicles, ammo, drones); retains high-end niches (5th-gen air, certain missiles); keeps a foothold via co-production on European soil.

**EU sources:** European Commission (White Paper/SAFE pages); Consilium (SAFE, timeline); White & Case; Breaking Defense; CEPA; European Parliament EPRS.


### 4.4 Key procurement facts (established across the project, all validated)
- **CPSP:** Decision before NATO Summit (July 7, 2026). Only **1 of 4** Victoria-class subs operational (HMCS Corner Brook). VCM = C$1B bridge program (Safran C$118M periscope contract Feb 2026, install 2030–33). Hanwha KSS-III export variant ~2,800t (vs ~3,600t domestic). TKMS Type 212CD already in NATO data-sharing protocols; KSS-III would need bespoke Canadianization (NORAD integration risk, per Jeffrey Collins, UPEI).
- **F-35/Fighter:** Reframed as a fleet-size question, not binary F-35-vs-Gripen. Emerging mixed fleet: Option A ~30 F-35A + 60 Gripen E (La Presse, 30 May 2026); Option B 72–88 F-35A + 72 Gripen (~140 aircraft total, CBC 6 June 2026). Saab proposing Canadian Gripen assembly + sovereign Montreal data centre. Min. McGuinty confirms review continues, no timetable.
- **M-346 (FFLIT trainer):** Carney–Meloni launched negotiations at G7 Évian, 16 June 2026. ~30 aircraft, ~C$2–3B. Fills zero domestic jet-training capability since CT-155 Hawk retired Mar 2024. CAE = natural sim partner. Adds Italy as 4th EU bilateral defence partner.
- **HIMARS:** C$2.6B (NOT C$5B), 26 launchers, FMS finalised Jan 2026, announced 2 June 2026, deliveries from 2029. Chosen over K239 Chunmoo specifically for PrSM/future anti-ship (LRASM) Arctic capability — the decisive rationale.
- **River-class destroyers:** First delivery (HMCS Fraser) 2032–33. ~C$1.3B/yr GDP, ~9,500 jobs/yr. BAE Type 26 design, Rolls-Royce MT30, Lockheed Martin Canada CMS, built Irving Halifax.
- **How Canada reached 2% (FY2025–26, first time since late 1980s):** Three mechanisms — (1) **C$9.3B surge** June 2025 (largest driver, total >C$63B); (2) **Canadian Coast Guard transferred to DND portfolio** (~C$2B/yr; "creative accounting" per James Bezan; NATO definition ambiguity since CCG is unarmed/civilian; True North Strategic Review skeptical); (3) **CAF pay raise** 8 Aug 2025 (Pte +20% to C$52,044; NCM/officer to Lt-Col +13%; Col+ +8%; retroactive 1 Apr 2025, pensionable). The 1.5% Hague category is designed to accommodate this (CCG, CBSA, dual-use infra) — a preview of how Canada reaches 5% by 2035.
- **Arctic infrastructure (the 1.5% question):** NATO 1.5% "inter alia" deliberately open-ended; no accounting rules until 2029 review. Atlantic Council proposes a list (seaports, roads, airports) but warns of slippery slope (Italy Messina Bridge = cautionary example). Canada's program: **C$35–40B** (Carney, 12 Mar 2026). Components: C$1B Arctic Infrastructure Fund (Transport Canada + DND, dual-use); Grays Bay Road and Port (~C$1B, 230km road + deepwater port + airstrip, "dual-use potential subject to further assessment by National Defence," construction unlikely before 2030); Arctic Economic and Security Corridor (~400km); Northern Operational Support Hubs (C$2.67B); Forward Operating Locations (C$32B); Arctic airports (C$294M); Mackenzie Valley Highway (800km); Trade Diversification Corridors Fund (C$5B). Honest caveat: rules don't exist yet, much value is civilian/minerals-first.
- **Baseline capability gaps (the report's spine):** RCN ranks 33rd globally; 1–2 of 4 subs operational; CF-18 ~40% readiness; no airborne early warning of its own; no self-propelled artillery since 2005; North Warning System obsolete vs hypersonics.

### 4.5 Investment Landscape (publicly-traded exposure — NOT investment advice)
Leads with a red `.callout-warn` disclaimer. CPSP = single biggest binary catalyst (~C$60–120B lifecycle). Key names mapped:
- **Hanwha Aerospace** (KRX:012450) — already +193% in 2025, +154% in 2024, ATH Mar 2026 (~1.65M KRW); Canada incremental; the parent, not the sub builder.
- **Hanwha Ocean** (KRX:042660) — the actual KSS-III submarine builder; cleanest CPSP exposure.
- **TKMS/ThyssenKrupp** — Type 212CD builder; public-market availability via the spin-off worth verifying.
- **Saab** (STO:SAAB-B, ~481 SEK, off 749 high) — GlobalEye (preferred) + Gripen (under review); most material Canada exposure among European primes.
- **Leonardo** (BIT:LDO) — M-346; plans to double profits by 2030.
- **Canadian-listed pure plays** (most retail-accessible): CAE (TSX:CAE, ~7 programs via simulation), MDA Space (TSX:MDA), Telesat (TSX/NASDAQ:TSAT), Bombardier (TSX:BBD.B), Algoma Steel (TSX/NASDAQ:ASTL, C$345M conditional on Hanwha).
- Others: Airbus (EPA:AIR), General Dynamics (NYSE:GD, GDLS-Canada), Lockheed Martin (NYSE:LMT), Boeing (NYSE:BA, lost 3 Canadian competitions), Babcock (LON:BAB, Hanwha ISS partner), BAE Systems (LON:BA.).
Includes a dated catalyst calendar (CPSP decision; NATO Summit Jul 7; fighter review; M-346/GlobalEye signatures; Hanwha earnings Aug 7 2026).


---

## 5. Editorial Decisions & Conventions (the "why" record)

These decisions were established over the project's lifetime. Preserve them unless explicitly changed.

### Content & framing
1. **First-publication framing.** ALL version-history scaffolding was removed ("Previously mislabelled...", "corrected to...", "as previously stated...", "Third Edition", "not in previous edition", "carried in earlier versions"). Facts are stated plainly as current. **Do not reintroduce diff-against-prior-version language.**
2. **Title:** "NATO Defense Spending Through a Canadian Lens" (US "Defense" in title is deliberate). Subtitle: "Capability Gap Analysis · Spend Destination · Alliance Integration · European Perspectives" (no "A Decision-Ready Report —" prefix). Eyebrow: "Defence Policy Analysis · Canada · June 2026".
3. **TL;DR uses "Option D" structure:** a bold one-sentence takeaway, then three labeled mini-points — **The gap.** / **The response.** / **The tension.** — which double as a reading guide.
4. **Not investment advice.** The Investment Landscape section MUST keep its red disclaimer. It's descriptive exposure-mapping, not recommendations. (This disclaimer is NOT version-history; keep it.)
5. **Evenhanded/neutral** on contested points. Honest about uncertainty tiers (signed contract < preferred supplier < still-in-definition).
6. **CAD vs EU spend sections are deliberately different in method:** CAD traces program-level contract destinations (discrete programs, known suppliers); EU traces funding architecture (loan capacity, mostly not yet contracted). This asymmetry is intentional and explained in each section's methodology.
7. **Naming:** the CAD section heading is "Defence Spend Destination Review (CAD)"; nav label "💰 CAD Spend Review". EU is "EU Spend Overview".

### Data discipline
8. **Data-vintage caveat travels with citations**, not just in prose: 2020–2023 actuals / 2024–2025 estimates / 2026 projections. Present in both the NATO chart's `.ncc-method` footer AND in Further Reading.
9. **Sources cited in both the chart footer (clickable links) AND Further Reading (APA).** Every external link has `target="_blank" rel="noopener"`.
10. **Representative, not exhaustive** country sets are flagged as such (NATO chart = 16 countries; Iceland excluded).

### Visual/interaction
11. **Canada always red** (#D62828) in the NATO chart; others blue.
12. **NATO chart bars re-sort high→low** by the selected year.
13. **Threshold lines (2%, 3.5%) draw on top of bars; labels at the top strip** (never colliding with the x-axis). This was iterated several times — the final solution is `afterDatasetsDraw` + `layout.padding.top:28` + horizontal clamping.
14. **Sticky-nav offset:** all jump links use `scrollToId()` (measures nav height) + CSS `scroll-margin-top:56px`. Never use bare `scrollIntoView()` for nav (headings would hide behind the sticky bar).
15. **Nav order matches document order.** When reordering sections, reorder the nav too, and re-check the Chart.js load position (see pitfall #3 below).
16. **Every section heading and its nav link share a theme emoji.**
17. **System fonts only — NEVER use web fonts (MANDATORY, applies to all future documents).** The report renders entirely with locally-installed system fonts. Do NOT add Google Fonts, Adobe Typekit, Bunny Fonts, `@font-face`, `@import` of any font, or any `<link rel="stylesheet">` / `<link rel="preconnect">` pointing at a font service. The only permitted font stacks are: body `-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif`; monospace `"SF Mono",Consolas,monospace`; serif `Georgia,serif`. Rationale: the report must be a self-contained, offline-capable artifact with instant text rendering, no font flash / layout shift, and no third-party network calls for typography. The ONLY permitted external request in the entire file is the Chart.js library from cdnjs (which has a graceful fallback). **This rule is non-negotiable and applies to this document and every future document generated for this project.**


---

## 6. Build & QA Procedure (for maintenance)

### The build model
The HTML is produced by a Python script that: (1) converts the markdown with `markdown.Markdown(extensions=['tables','sane_lists'])`; (2) **replaces** the broken auto-rendered domain table with hand-built HTML; (3) **injects** the timeline table, source tracker, both charts, and the nav; (4) applies section anchor IDs + theme emojis; (5) converts blockquotes to `.callout` divs; (6) wraps ✅/❌/⚠️ in icon spans; (7) adds `class="data-table"` to tables.

**For incremental edits (the approach used in this project's later stages): edit the HTML and markdown surgically in parallel** using string replacement, rather than a full rebuild — a full rebuild risks clobbering the hand-built domain table, charts, and nav. Always `present_files` after.

### Recurring pitfalls (learned the hard way)
1. **Duplicated subtitles.** When adding an anchor id to a heading that already contains its subtitle, naive replacement like `<h2>X` → `<h2 id>X: subtitle` produces `X: subtitle: subtitle`. This bit `arctic`, `spend`, `synthesis`, `europe`, `invest`. **Always check for doubled subtitles after heading edits.**
2. **Domain table doesn't render from markdown** (the `\|` escapes break the table extension). It lives as injected HTML. Never expect the markdown converter to produce it.
3. **Chart.js load order.** Chart.js is in `<head>` so BOTH charts work regardless of section order. If you ever move it back into a section, any chart positioned *before* it will throw `ReferenceError: Chart is not defined`. Keep it in `<head>`.
4. **Script slicing for validation.** When checking brace/paren balance, extract complete `<script>...</script>` blocks via regex (`re.findall(r'<script>(.*?)</script>', html, re.DOTALL)`) — slicing from an inner token like `var NATO =` gives false off-by-one counts because it excludes the opening `(function(){`.
5. **SRI hashes.** Do NOT add a fabricated Subresource-Integrity hash to the Chart.js CDN tag — a wrong hash blocks the script entirely (worse than none). Keep `crossorigin`+`referrerpolicy` and rely on the charts' `typeof Chart` fallbacks. If true SRI is wanted, fetch the correct hash from cdnjs or vendor the library.

### QA checklist (run before every release)
- [ ] Tag balance: `div table thead tbody tr script style main nav header section h1-h3 p ul ol li span canvas colgroup` all balanced.
- [ ] No duplicate `id=` values.
- [ ] Every `scrollToId('X')` nav target has a matching `id="X"`.
- [ ] All 3 `<script>` blocks: braces, parens, brackets balanced.
- [ ] Chart.js loaded exactly once, in `<head>`; exactly 2 `new Chart(` calls.
- [ ] Domain table: `<col>` count == `<th>` count == per-row `<td>` count; all body rows uniform.
- [ ] No unused CSS classes (account for compound `.a.b` and JS-applied classes); no class used in markup without a rule (except intentional).
- [ ] No dead JS functions (every defined function referenced > once).
- [ ] Both `<canvas>` have `aria-label`; `<html lang>`, `<meta viewport>`, `<title>`, `<!DOCTYPE>`, charset all present.
- [ ] Every `target="_blank"` has `rel="noopener"`.
- [ ] No `innerHTML` from untrusted (URL/location) sources.
- [ ] No unconverted `**markdown**`, no literal `\|`, no duplicated subtitles.
- [ ] No version-history language reintroduced.
- [ ] **No web fonts.** Zero matches for `fonts.googleapis.com`, `fonts.gstatic.com`, `fonts.bunny.net`, `typekit`, `@font-face`, or `@import` of a font. Only system font stacks present. Only external request in the file is Chart.js from cdnjs.

### Validation snippet (Python)
```python
import re
html = open('NATO_Defence_Spending_Canadian_Lens.html').read()
# tag balance
for t in ['div','table','script','style','canvas','h2','tr','td']:
    o=len(re.findall(rf'<{t}[\s>]',html)); c=len(re.findall(rf'</{t}>',html))
    assert o==c, f'{t}: {o}/{c}'
# duplicate ids
from collections import Counter
ids=re.findall(r'id="([^"]+)"',html); assert not [k for k,v in Counter(ids).items() if v>1]
# nav resolves
assert all(t in set(ids) for t in re.findall(r"scrollToId\('([^']+)'\)",html))
# scripts balanced
for s in re.findall(r'<script>(.*?)</script>',html,re.DOTALL):
    assert s.count('{')==s.count('}') and s.count('(')==s.count(')')
# chart.js once, 2 charts
assert html.count('chart.umd.js')==1 and html.count('new Chart(')==2
# no web fonts (system fonts only)
for banned in ['fonts.googleapis.com','fonts.gstatic.com','fonts.bunny.net','typekit','@font-face','@import']:
    assert banned not in html, f'web font reference found: {banned}'
print('QA PASS')
```


---

## 7. Project Evolution (chronological change log)

The report was built and refined over an extended session. Major milestones, in order:

1. **Base report built** — research across NATO/SIPRI/EU/national sources; HTML+MD with tabbed 11-column domain table, spend-destination doughnut chart, sticky nav, QA/security passes.
2. **Title changed** to "NATO Defense Spending Through a Canadian Lens" (from "Canada's Military Procurement 2025–2026: A Decision-Ready Report"); removed old title from title bar, h1, a duplicate body h1, subtitle prefix.
3. **Investment Landscape section added** — publicly-traded exposure, red disclaimer, full company/ticker table, dated catalyst calendar. Initially placed late, later moved to after Overview.
4. **Arctic & Dual-Use Infrastructure section added** — the 1.5% NATO question; official NATO + Canadian stances; 7-project table; honest caveats about undefined rules.
5. **Code QA** — found/fixed: removed dead `.note` CSS + dead `col-cost` classes; added `crossorigin`/`referrerpolicy`/`safeDraw` fallback to Chart.js; removed a fabricated SRI hash.
6. **NATO spending bar chart added** — initially 2024–2026, Canada red. Then: threshold lines fixed (drawn on top, labels repositioned); extended to **2020–2026** (7-year toggle); bars made to **re-sort by selected year**; source links added to footer; data-vintage methodology note added to footer AND Further Reading.
7. **First-publication cleanup** — removed ALL version-history language from both files (the "Previously mislabelled 'Jan 2025'" timeline note, "Third Edition", "corrected to", etc.).
8. **TL;DR restructured** to Option D (bold takeaway + gap/response/tension).
9. **Section emojis added** to every heading and nav link; several duplicated subtitles fixed in the process.
10. **Navigation fixes** — Investment moved after Overview; nav reordered to match document order; sticky-nav scroll offset fixed (`scrollToId()` + `scroll-margin-top`); various nav labels aligned to their section headings ("Investment Landscape", "Domain Summary Table", "Arctic & Dual-Use 1.5%", "CAD Spend Review").
11. **NATO Spend moved before CAD Spend** — required moving Chart.js to `<head>` to preserve chart function.
12. **Renamed** CAD section to "Defence Spend Destination Review (CAD)" / nav "CAD Spend Review".
13. **EU Spend Overview section added** — mirrors CAD structure; SAFE/ReArm/EDIP capital-flow analysis; US-shift evidence; per-initiative table (large nations called out, small ones aggregated); EU sources added to Further Reading.
14. **Final QA + handoff** — removed dead `.td-milestone`/`.th-milestone` classes; added `aria-label` to spend chart canvas; generated fresh deliverables + this context document.

---

## 8. ONE-SHOT RECREATION PROMPT

> Use the prompt below to regenerate the entire report from scratch in a single pass (e.g. in Claude Code or a fresh Claude session). It assumes access to the data in Section 4 of this document. Paste this document alongside the prompt, or ensure the data tables above are available.

```
You are building a self-contained, single-file interactive HTML report plus a matching
markdown source. Title: "NATO Defense Spending Through a Canadian Lens" — an analysis of
Canada's 2025–2026 military procurement wave viewed through NATO defence-spending obligations.
Temporal anchor: ~June 26, 2026 (CPSP submarine decision pending before the July 7 2026 NATO
Summit; finalists TKMS vs Hanwha Ocean). Audience: informed defence-policy/finance reader.
Tone: rigorous, neutral, evenhanded, honest about uncertainty. British/Canadian spelling in
body ("Defence") but US "Defense" in the title. PURPOSE: a thorough overview to INFORM
UNDERSTANDING, not to drive specific decisions. THIS IS NOT PROFESSIONAL INVESTMENT ADVICE and
not a recommendation to take any action — the investment section is descriptive ("could affect
sentiment"), never prescriptive ("should buy/sell"); readers draw their own conclusions.

Use the data in the accompanying "Context & Rebuild Guide" Section 4 (NATO 2020–2026 spend table,
CAD 22-program spend-destination portfolio, EU SAFE/ReArm capital-flow figures, all procurement
facts and sources). Do not invent figures; use those validated numbers and cite their sources.

PRODUCE TWO FILES:
1. A markdown source with these sections in order, each with a theme emoji in the heading:
   📋 TL;DR · 💹 Investment Landscape · ⚡ Latest Developments · ❄️ Arctic & Dual-Use Infrastructure
   · 🗂️ Domain Summary Table · 📊 NATO Defence Spending by Country · 💰 Defence Spend Destination
   Review (CAD) · 🇪🇺 EU Spend Overview · 🎯 Program-by-Program · 🧭 Synthesis · 🇪🇺 European-Language
   Perspectives · 🍁 Regional Impact · 🪶 Indigenous & Reconciliation · 🗓️ Program Timeline ·
   ⚠️ Key Caveats · 🔗 Live Source Tracker · 📚 Further Reading.
2. A single self-contained HTML file (Chart.js 4.4.1 from cdnjs in <head> with crossorigin +
   referrerpolicy, loaded once; no other external deps).

REQUIRED CONTENT:
- TL;DR in "Option D" form: one bold takeaway sentence, then three labeled points — The gap. /
  The response. / The tension.
- Investment Landscape: lead with a RED "Not investment advice" callout; company/ticker exposure
  table; dated catalyst calendar. CPSP is the single biggest binary catalyst.
- Arctic section: NATO 1.5% "inter alia" + Canadian dual-use stance; 7-project table; caveat that
  NATO rules aren't defined until 2029.
- Domain Summary Table: 11 columns × 23 program rows, hand-built as HTML (markdown can't render
  the escaped pipes). Three toggle views via setView(): Platform Overview / Canadian Economic
  Impact / Competitive Intelligence, using data-view attributes on col/th/td. Color-coded columns.
  ✅/❌/⚠️ icons for US-selected / US-lost / unverified.
- CAD Spend Destination Review: methodology + caveats; 22-row per-program destination table;
  aggregate doughnut summary; key finding (US ~16% is a floor from 4 no-alternative programs);
  five observations.
- EU Spend Overview: MIRROR the CAD structure but reframe to funding architecture (loan capacity,
  mostly uncontracted). Methodology + caveats; per-initiative capital-flow table (call out Poland,
  Germany, France, Italy, Baltics specifically; aggregate smaller states); aggregate directional
  summary; key finding (US displaced from commodity middle, retains high-end, co-produces on EU
  soil); five observations.

REQUIRED INTERACTIVITY (3 inline <script> IIFEs, each guarding `typeof Chart`):
- Spend-destination DOUGHNUT (canvas#spendChart2, setScenario2()): toggle TKMS vs Hanwha CPSP
  scenarios; data hardcoded; safeDraw() fallback; aria-label.
- NATO spending HORIZONTAL BAR (canvas#natoChart, natoSetYear()): 7-year toggle 2020–2026;
  Canada bars RED (#D62828), others blue (#1B4F8A); BARS RE-SORT high→low by selected year;
  custom afterDatasetsDraw plugin draws 2% and 3.5% dashed lines ON TOP of bars with label chips
  pinned to the TOP plot strip (layout.padding.top:28) and horizontally clamped so they never
  touch the x-axis; x-axis titled; legend + clickable source links + data-vintage methodology
  note in the footer; aria-label.
- Toggle/nav script: define scrollToId(id) FIRST (measures live .section-nav height, scrolls with
  that offset +8px); then domain-table setView(); then a global handler for in-body #anchor links.

LAYOUT/STYLE:
- Sticky "Jump to" nav (.section-nav, position:sticky top:0). Nav order MUST match document order.
  Every nav link shares its section's theme emoji and uses scrollToId(). Add
  `h2[id],div[id],section[id]{scroll-margin-top:56px}`.
- Use CSS custom properties for all colors (navy/blue/teal/amber/mint/green/red/slate/muted +
  bg/border tokens). Blockquotes → .callout / .callout-kf (key finding) / .callout-warn (red).
- Header: eyebrow "Defence Policy Analysis · Canada · June 2026"; h1 = title; subtitle
  "Capability Gap Analysis · Spend Destination · Alliance Integration · European Perspectives".
- FONTS: system fonts ONLY. Use body stack `-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,
  sans-serif`, mono `"SF Mono",Consolas,monospace`, serif `Georgia,serif`. Never add Google Fonts,
  Typekit, @font-face, @import, or any external font <link>. No web fonts under any circumstances.

DATA-VINTAGE RULE: 2020–2023 = NATO actuals; 2024–2025 = estimates; 2026 = projections. State this
caveat in BOTH the NATO chart footer and Further Reading. Cite sources in BOTH chart footers
(clickable, target=_blank rel=noopener) AND Further Reading (APA).

DO: first-publication framing (NO version-history language anywhere). Keep the investment
disclaimer. Verify before finishing: all tags balanced, no duplicate ids, every scrollToId target
resolves, all 3 scripts brace/paren balanced, Chart.js once in <head>, exactly 2 new Chart() calls,
domain table 11 col == 11 th == 11 td/row across 23 uniform rows, both canvases have aria-label,
no duplicated subtitles, every target=_blank has rel=noopener, and NO web fonts (system font stacks only — never add Google Fonts/Typekit/@font-face/@import; the only external request permitted is Chart.js from cdnjs).

Output the markdown first, then the HTML. Run the QA checklist before declaring done.
```

---

## 9. File Manifest (for the GitHub repo)

| File | Role |
|------|------|
| `NATO_Defence_Spending_Canadian_Lens.html` | The rendered interactive report (deploy this) |
| `NATO_Defence_Spending_Canadian_Lens.md` | Markdown source of the report's prose/tables |
| `CONTEXT_AND_REBUILD_GUIDE.md` | **This file** — context, architecture, data, conventions, QA, one-shot prompt |

**Suggested repo workflow for Claude Code:**
- Treat the `.md` report as the editable source for prose/data changes.
- Treat the `.html` as a build artifact, but since the build script isn't checked in, **edit the HTML surgically in parallel** (string replacement) for now, following Section 6's pitfalls. A future improvement is to commit a `build.py` that regenerates the HTML from the markdown + component templates.
- Run the Section 6 QA checklist (and the validation snippet) before every commit.
- This guide is the source of truth for *why* things are the way they are — consult Section 5 before changing conventions, and update the change log (Section 7) as the report evolves.

