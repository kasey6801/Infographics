---
description: Refresh a report's "Latest Developments" with new Europe/Asia/Canada items — dated block on top, primary-country flags, official-source links, manual review, then push.
argument-hint: [path to the report folder or its index.html]
---

# /cad-defense-update — "Latest Developments" update protocol

You are updating the **"Latest Developments"** section of an existing HTML infographic report
(the same workflow used for `CC_Infographics/CAD-NATO-Defense-Spending/index.html`). Follow this
protocol exactly. The target report is: **$ARGUMENTS**
(if empty: use the currently open `index.html`, or ask which report folder to update).

## 0. Orient (read before researching)
- Locate the report's `index.html`. The file is large — do not read it whole; `grep` for the
  `Latest Developments` heading and read that section + the nav, the styling for
  `latest-header` / `callout-kf`, and the `Active Watch Threads` cards.
- Note the **date of the current latest block**, its **prose style/depth**, the **existing
  items** (so new ones don't duplicate), and the report's **themes** (derive them from the
  report itself — e.g. for the NATO/Canada report: NATO 5% spending, CPSP submarines,
  F-35/Gripen, GCAP/ETTA, M-346, HIMARS, Arctic/NORAD, EU SAFE, primes TKMS/Saab/Leonardo/
  Hanwha, the NATO Summit).

## 1. Research parameters (SEARCH)
- **Knowledge may be stale → you MUST use live web search/fetch.** Launch **3 parallel research
  agents**, one each for **Canada, Europe, Asia**.
- **Time window:** developments NEWER than the current block's date (and any notable items it
  missed) — roughly the last 7–10 days.
- **Backfill rule (mandatory):** every update also runs ONE search pass over the gap BEFORE the
  window (from ~2 weeks before the previous block's date up to the window start), sweeping
  official readout feeds specifically. Rationale: windows tile forward, so an item missed once is
  missed forever; the Jun 15, 2026 Marconi/SAFE first-contract announcement sat in a PMO
  *readout* during the G7 and went uncaught for a month until the user flagged it.
- **Official-readout sweep, all 32 members (mandatory):** each research agent MUST scan,
  headline-level, the official head-of-government newsroom AND the defence-ministry newsroom of
  every NATO member in its assigned region (window + backfill gap; translate as needed), in
  addition to query-based searches. For Canada that means ALL FOUR pm.gc.ca feeds: news
  releases, statements, **readouts**, speeches (defence firsts have landed in readouts, not
  releases). The Asia agent covers the IP4 partners (Japan MOD/MOFA, Korea MND, Australia
  Defence, NZ Defence). Use the member feed roster in §1b.
- **Relevance filter:** only items material to THIS report's themes and its alliance/procurement
  narrative. Pass each agent the existing items so it doesn't repeat them.
- **Per finding, require:** one-line headline · 2–4 sentences with figures/names/dates · the exact
  date · source (publication + URL) · why it matters to the report. Prioritize items with
  confirmed dates; explicitly flag anything unconfirmed.

### 1b. Member feed roster (official newsrooms; verify a deep link resolves before citing, else use the domain landing page)

- 🇦🇱 kryeministria.al · mod.gov.al | 🇧🇪 premier.be · defence.belgium.be | 🇧🇬 gov.bg · mod.bg
- 🇨🇦 pm.gc.ca (`/en/news/news-releases`, `/en/news/statements`, `/en/news/readouts`, `/en/news/speeches`) · canada.ca DND newsroom · Defence Investment Agency newsroom
- 🇭🇷 vlada.gov.hr · morh.hr | 🇨🇿 vlada.gov.cz · army.cz | 🇩🇰 stm.dk · fmn.dk
- 🇪🇪 valitsus.ee · kaitseministeerium.ee | 🇫🇮 valtioneuvosto.fi · defmin.fi | 🇫🇷 elysee.fr · defense.gouv.fr
- 🇩🇪 bundesregierung.de · bmvg.de | 🇬🇷 primeminister.gr · mod.mil.gr | 🇭🇺 kormany.hu · honvedelem.hu
- 🇮🇸 government.is | 🇮🇹 governo.it · difesa.it | 🇱🇻 mk.gov.lv · mod.gov.lv | 🇱🇹 lrv.lt · kam.lt
- 🇱🇺 gouvernement.lu (defence section) | 🇲🇪 gov.me | 🇳🇱 government.nl · defensie.nl | 🇲🇰 vlada.mk · mod.gov.mk
- 🇳🇴 regjeringen.no (incl. Forsvarsdepartementet) | 🇵🇱 gov.pl (premier · national-defence/MON · Agencja Uzbrojenia) | 🇵🇹 portugal.gov.pt
- 🇷🇴 gov.ro · mapn.ro | 🇸🇰 vlada.gov.sk · mosr.sk | 🇸🇮 gov.si | 🇪🇸 lamoncloa.gob.es · defensa.gob.es
- 🇸🇪 government.se · FMV | 🇹🇷 tccb.gov.tr · msb.gov.tr | 🇬🇧 gov.uk (No10 · MoD) | 🇺🇸 whitehouse.gov · defense.gov (+ state.gov readouts)
- Alliance/EU: nato.int (news · official texts · **transcripts**) · consilium.europa.eu press · defence-industry-space.ec.europa.eu (DG DEFIS, incl. the SAFE page's disbursement log)
- IP4: mod.go.jp/mofa.go.jp · mnd.go.kr · defence.gov.au/minister.defence.gov.au · nzdf.mil.nz

## 2. Source-link parameters (LINKS)
- **Every entry gets ≥1 inline hyperlink**, **preferring an official government / NATO / EU
  readout** where one exists: `pm.gc.ca` (all four feeds, incl. `/en/news/readouts`), `canada.ca`,
  `nato.int` (incl. press-conference **transcripts**), `consilium.europa.eu`,
  `defence-industry-space.ec.europa.eu` (DG DEFIS / SAFE), `gov.uk`, `gov.pl` (MND / Armament
  Agency), other national MoD sites, and official company
  press releases for industry items. Fall back to reputable defence press (Reuters, Defense News,
  Naval News, Breaking Defense, Janes, Korea Times, **POLITICO / POLITICO Europe (`politico.eu`)**,
  etc.) only where no official readout exists.
- **`politico.eu` caveat:** it is a standing reference for European defence-policy items, but it
  **blocks Claude's fetcher outright** (not just a 403, the fetch fails). Reconstruct the story via
  `WebSearch`, syndications, and any Pro-headline variant, then **cross-reference the underlying
  claim to an official source** (EU/US/NATO/national) before citing. Cite POLITICO for the
  reporting and pair it with the official source for the substance; label anything that rests on
  POLITICO alone as reported, not confirmed.
- **Verify every embedded URL resolves before inclusion.** A bot-blocked **403** = treat as live
  (page exists); a **404** = drop to the authoritative domain's landing page rather than guess a
  slug. Confirm canonical URLs via `WebSearch` with `allowed_domains` when unsure.
- Use the report's existing link idiom: `<a href="…" target="_blank" rel="noopener">source</a>`.

## 3. Structure / formatting (STRUCTURE)
- Add a **NEW dated block on TOP**: `<h2 id="latest" class="latest-header">⚡ Latest Developments:
  <today's date></h2>`, immediately above the previous block. **Preserve prior block(s) below as
  history.**
- **Move the `id="latest"` anchor to the new block and REMOVE it from the previous header** so
  there is **exactly one `id="latest"`** (nav must jump to the newest).
- **Match the existing prose depth** — full `<p><strong>lead-in.</strong> detail with figures,
  names, dates…</p>` paragraphs, not bullets.
- **Tag every entry with the PRIMARY COUNTRY flag emoji** (the country the entry is chiefly
  about — e.g. 🇨🇦 🇩🇪 🇰🇷 🇯🇵 🇬🇧). For a **NATO-centric** entry use **🧭** as the NATO stand-in
  (Unicode has no NATO flag emoji; the compass echoes NATO's compass-rose emblem) — keep this
  marker consistent across updates.
- Group **Canada → Europe → Asia**. Add **one `callout callout-kf`** key-finding box for the
  load-bearing takeaway. Close the block with `<hr />`.

## 4. Accuracy guardrails ("fail loud")
- State only what's **confirmed**. **Never assert an unconfirmed outcome** (e.g. no "X won the
  contract" before an official announcement); mark expected-but-unconfirmed items as such, with
  the date.
- **Negative-claim rule:** never write "no X yet" / "first X still pending" / "X has not
  happened" on the basis of a window-scoped sweep. A universal negative requires its own
  targeted, **date-unrestricted** verification search before it goes in the report; absence of
  evidence inside a 7–10 day window is not evidence of absence. (This is exactly how the
  report briefly claimed Canada had "no SAFE awards yet" a month after the first one.)
- Use the new block's framing to **correct now-outdated facts** in the prior block — don't rewrite
  history wholesale.
- Keep figures precise and attributed (e.g. distinguish a broad trade-mission total from a
  defence-specific figure; convert relative dates to absolute).

## 5. Secondary consistency touch-ups
- Update any related **Active Watch Threads** card(s) and the **"Last curated"** date to today.

## 6. Review → push (HANDOFF)
- After editing, run `open index.html` to launch it in the browser for **manual review BEFORE any
  push**.
- **Do NOT push automatically.** Show `git status` / `git diff --stat`; **commit & push only after
  the user accepts** the review.
- Commit only the report file(s) changed; write a clear message describing the update.
  **Per the user's global rule: NO `Co-Authored-By: Claude` or any Claude attribution** in commit
  messages or PR descriptions. Push to the report repo's default branch (it's a personal
  GitHub-Pages site).

## 7. Verification checklist
- `grep -c 'id="latest"' index.html` → **1**; prior block intact below as history.
- Every new entry has a **primary-country flag** (🧭 for NATO) and **≥1 working source link**
  (official preferred).
- No entry asserts an unconfirmed outcome; dates/figures correct.
- Watch-thread card(s) and "Last curated" date updated; new `latest-header` + `callout-kf` render
  in the existing style.
