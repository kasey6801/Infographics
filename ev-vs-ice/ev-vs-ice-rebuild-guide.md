# EV vs ICE Decision Guide, Rebuild Guide

This is the companion explainer for `index.html` (the EV vs ICE Decision Guide). It documents
how the page is put together, the math behind every number, and a single prompt at the end that
recreates the whole HTML file from scratch.

The guide is one self-contained HTML file: warm "field guide" styling, an interactive
total-cost-of-ownership (TCO) calculator that compares an electric vehicle (EV) against an
internal-combustion-engine vehicle (ICE), and a dual-pole gauge that swings toward whichever is
cheaper to own. Hybrids and plug-in hybrids are out of scope by design.

---

## 1. What it does

The reader enters their own numbers for two cars. The page computes the full cost to own each car
over a chosen number of years, shows the gap, and breaks down what is driving that gap. Everything
recomputes live on every keystroke or slider move. There is no server and no network call; all math
runs in the browser.

The output has four parts:

1. **A decision gauge.** A semicircular needle that leans toward the cheaper drivetrain (ICE pole on
   the left, EV pole on the right), with the dollar gap printed in the center.
2. **Two stacked cost bars**, one per car, split into Purchase, Energy/fuel, Maintenance,
   Financing, and Insurance.
3. **A "what's driving it" table** that names each gap (fuel, maintenance, insurance, financing,
   sticker) and which car wins it.
4. **Context flags** for very high or very low annual mileage.

---

## 2. File anatomy

A single `index.html` with three inline parts and no external dependencies:

- **`<head>`**: charset/viewport meta, a `referrer` meta, a tiny **no-flash theme script** (sets
  `data-theme="dark"` before paint), then the full `<style>` block.
- **`<body>`**: a fixed theme-toggle button, then a `.wrap` containing the header, a two-column
  `.grid` (left = input panels, right = sticky results card), and a footer.
- **`<script>`**: one IIFE holding all logic (unit handling, the `compute()` function, the reset
  handler, and the theme toggle).

Input panels (left column):

1. **How you drive** (sliders): distance per year, years you'll keep it.
2. **The two cars** (per-car number inputs): price, amount financed, finance APR, loan term,
   maintenance per year, insurance per year.
3. **EV rebates at time of purchase**: a single rebate amount applied to the EV only.
4. **Energy prices**: electricity rate, fuel price, and a collapsible "fine-tune" with EV
   efficiency and ICE fuel economy.

---

## 3. How the page works (data flow)

1. On load, the no-flash script picks the theme, then the main script wires listeners and calls
   `compute()` once.
2. Every input has `input`/`change` listeners that call `compute()`.
3. `compute()` reads the raw input values, converts them into a single **canonical unit system
   (imperial)**, runs all the math, then writes results back into the DOM (gauge transform, verdict
   text, bar segment widths, driver rows, flags).
4. The unit toggle and reset button reconfigure inputs and then call `compute()`.

The key architectural rule: **the math always runs in imperial internally.** Metric is purely a
display layer. Inputs are converted to imperial on read and results are converted back for display.
This makes the computed result invariant to the unit setting: the same physical scenario yields the
same totals whether shown in miles/gallons or kilometres/litres.

---

## 4. Inputs reference

| Input | id | Default | Unit (imperial) | Notes |
|---|---|---|---|---|
| Distance per year | `miles` | 12,000 | miles | slider 2,000-40,000 step 500 |
| Years kept | `years` | 7 | years | slider 1-15 step 1 |
| EV price | `evPrice` | 40,000 | $ | |
| ICE price | `gasPrice` | 40,000 | $ | |
| EV amount financed | `evFin` | 0 | $ | 0 means cash |
| ICE amount financed | `gasFin` | 0 | $ | 0 means cash |
| EV finance APR | `evApr` | 7 | % | |
| ICE finance APR | `gasApr` | 7 | % | |
| EV loan term | `term` | 5 | years | |
| ICE loan term | `termIce` | 5 | years | |
| EV maintenance / yr | `evMaintYr` | 700 | $ | |
| ICE maintenance / yr | `iceMaintYr` | 1,200 | $ | |
| EV insurance / yr | `evIns` | 1,900 | $ | |
| ICE insurance / yr | `iceIns` | 1,600 | $ | |
| EV rebate | `incentive` | 0 | $ | applied to EV price only |
| Electricity | `elec` | 18 | ¢/kWh | unit-neutral |
| Fuel | `gas` | 3.93 | $/gallon | becomes $/litre in metric |
| EV efficiency | `eff` | 3.5 | mi/kWh | becomes km/kWh in metric |
| ICE fuel economy | `mpg` | 32 | MPG | becomes L/100km in metric |

Excluded on purpose: registration and resale value (they vary more by person and model than by
drivetrain). Maintenance and insurance defaults are rough placeholders meant to be replaced with
real figures.

---

## 5. The calculations (the math)

All formulas below are in canonical imperial units. Let `years` be the ownership period and
`totalMiles = miles * years`.

### Per-distance fuel cost

```
evPerMile  = elec / eff          // $/kWh divided by mi/kWh  -> $/mile
icePerMile = gasGal / mpg         // $/gallon divided by MPG  -> $/mile
```

`elec` is the entered cents/kWh divided by 100 to get $/kWh.

### Lifetime fuel/energy

```
evFuel  = totalMiles * evPerMile
iceFuel = totalMiles * icePerMile
```

### Maintenance and insurance

Both are flat annual costs scaled by the ownership period (they do not scale with mileage):

```
evMaint    = evMaintYr  * years
iceMaint   = iceMaintYr * years
evInsTotal = evIns      * years
iceInsTotal= iceIns     * years
```

### Financing interest

Only the interest paid is a cost; the principal is already part of the purchase price, so financing
adds interest on top. Interest uses the standard fixed-rate amortization formula:

```
loanInterest(P, APR, termYears):
    if P <= 0: return 0                      // cash purchase
    r = (APR / 100) / 12                      // monthly rate
    n = termYears * 12                        // number of payments
    if r <= 0: return 0                       // 0% financing
    payment = P * r * (1+r)^n / ((1+r)^n - 1)
    return payment * n - P                    // total interest

evInterest  = loanInterest(evFin,  evApr,  term)
iceInterest = loanInterest(gasFin, gasApr, termIce)
```

### Purchase and rebate

The rebate reduces the EV price only:

```
evBuy = max(0, evPrice - rebate)
```

### Totals and the verdict

```
evTotal  = evBuy   + evFuel  + evMaint  + evInterest  + evInsTotal
iceTotal = gasCar  + iceFuel + iceMaint + iceInterest + iceInsTotal
delta    = iceTotal - evTotal             // positive => EV is cheaper to own
```

Verdict logic, with a dead band so near-ties read honestly:

```
if |delta| < 1500: "Too close to call on cost"
elif delta > 0:    "EV is cheaper to own"  by delta
else:              "ICE is cheaper to own" by -delta
```

### The gauge needle

The needle angle is a soft-clamped function of the gap so large gaps saturate rather than spin off:

```
norm  = tanh(delta / 9000)        // -1..1, positive leans toward EV
angle = norm * 82 degrees          // clockwise (right) = EV pole
```

The needle and the center number are tinted EV-green for an EV win, ICE-orange for an ICE win, or
neutral inside the dead band.

### The stacked bars

Each bar's segments are widths relative to the larger of the two totals, so the cheaper car's bar
is visibly shorter:

```
maxTot = max(evTotal, iceTotal, 1)
segmentWidthPercent = value / maxTot * 100
```

Segment order per bar: Purchase, Energy/fuel, Maintenance, Financing, Insurance. Their sum equals
that car's total, so the bar is a faithful breakdown.

### The "what's driving it" gaps

Each row reports a difference and which car wins it:

```
fuelGap    = iceFuel     - evFuel          // + => EV saves on fuel
maintGap   = iceMaint    - evMaint         // + => EV saves on maintenance
insGap     = iceInsTotal - evInsTotal      // + => EV saves on insurance
stickerGap = gasCar      - evBuy           // + => EV net price is lower (EV cheaper)
financing  = compare evInterest vs iceInterest (lower interest wins)
```

Note the sticker direction: `gasCar - evBuy` is positive when the EV's post-rebate price is the
lower of the two, which means EV is cheaper. (This was a bug fixed during QA, where the label had
been inverted.)

### Unit conversions

Constants: `KM = 1.60934` (km per mile), `LPG = 3.78541` (litres per US gallon), and the
US-MPG to L/100km bridge `235.215`.

On read (metric display values into canonical imperial):

```
miles  = km / KM
$/gal  = $/L * LPG
mi/kWh = (km/kWh) / KM
MPG    = 235.215 / (L/100km)
```

On display (canonical to metric):

```
¢/km   = (¢/mile) / KM
```

Switching units snaps the distance slider to a round value in the new unit (for example 12,000 mi
becomes 19,000 km), a small rounding step that is expected for a stepped slider.

---

## 6. Theming and style

The look is the shared "field guide" language: warm paper and ink, bold 1.5px ink borders, a hard
printed offset shadow, mono uppercase eyebrows and labels, and a heavy display headline.

Palette (CSS variables in `:root`, light is the default):

```css
--paper:#E9E1CF; --paper-2:#E1D8C2; --card:#F3EDDD;
--ink:#1C1A15;   --ink-soft:#4A463C; --line:#C5BAA0;
--stamp-red:#B23A2E;   /* links, eyebrows, CTAs, slider thumbs */
--seal-green:#2E6149;  /* EV accent */
--warp:#E8590C;        /* sparing brand orange */
--shadow:0 2px 0 rgba(28,26,21,.14);
```

Drivetrain accents are kept separate from the link color: **EV uses seal-green**, **ICE uses a
combustion orange**, and stamp-red stays reserved for links, eyebrows, and the step badges. The
five stacked-bar segments are their own earth-tone variables (`--seg-buy`, `--seg-maint`,
`--seg-fin`, `--seg-ins`) plus the EV/ICE accent for energy.

Dark mode is standard. The variables are overridden under `[data-theme="dark"]` on `<html>`, the
body's white highlight gradient is toned down, and the EV/ICE/segment colors get lighter variants
so they read on dark surfaces. A fixed top-right toggle flips `data-theme`, persists the choice to
`localStorage('theme')`, and updates its own label. A head script applies the saved (or
system-preferred) theme before first paint to avoid a flash. All `localStorage` access is wrapped
in `try/catch` so a sandboxed preview that blocks storage degrades gracefully instead of throwing.

---

## 7. Accessibility and security

- Semantic structure, labelled inputs, an `aria-label`ed icon-free toggle button, a radiogroup for
  units, and stamp-red `:focus-visible` outlines. The reset control is keyboard operable
  (`role="button"`, `tabindex`, Enter/Space).
- No external resources, no fonts to download, no analytics, no network calls.
- The only `innerHTML` writes are built from hardcoded class literals plus numbers that have passed
  through `parseFloat` and formatting, so no user-entered string is ever inserted as markup. There
  is no XSS surface.

---

## 8. One-shot rebuild prompt

Paste the block below to regenerate the HTML file from scratch.

```text
Build a single self-contained HTML file: an interactive "EV vs ICE Decision Guide" that compares
the total cost of ownership of an electric vehicle (EV) against an internal-combustion-engine
vehicle (ICE). Hybrids are out of scope; state that in an explainer line under the title.

HARD CONSTRAINTS
- One self-contained HTML file. No CDNs, external scripts, stylesheets, web fonts, analytics, or
  any network/runtime calls. Inline CSS and inline JS only.
- System font stacks only (sans: system-ui,-apple-system,"Segoe UI",Roboto,Helvetica,Arial; mono:
  ui-monospace,"SF Mono",Menlo,Consolas,"Liberation Mono"). Never Google Fonts.
- No em dashes anywhere; use commas, colons, periods, or middots.
- Include a "referrer" no-referrer meta and an MIT License line in the footer linking to LICENSE.
- No localStorage usage except wrapped in try/catch.

STYLE (warm "field guide", paper and ink)
- Light default with a standard dark mode. Palette as CSS variables in :root:
  --paper:#E9E1CF; --paper-2:#E1D8C2; --card:#F3EDDD; --ink:#1C1A15; --ink-soft:#4A463C;
  --line:#C5BAA0; --stamp-red:#B23A2E; --seal-green:#2E6149; --warp:#E8590C;
  --shadow:0 2px 0 rgba(28,26,21,.14).
- Dark mode overrides under [data-theme="dark"] on <html>: --paper:#17150F; --paper-2:#1F1B14;
  --card:#221E17; --ink:#ECE3D0; --ink-soft:#B3AA96; --line:#403829; --stamp-red:#E08A7C;
  --seal-green:#69B294; --shadow:0 2px 0 rgba(0,0,0,.45).
- Drivetrain accents: EV = seal-green, ICE = a combustion orange (about #C2410C light, #F0883C
  dark). Keep stamp-red for links, eyebrows, step badges, and slider thumbs.
- Cards/panels: --card background, 1.5px solid --ink border, radius ~10px, hard offset --shadow.
  Mono uppercase eyebrows and section labels with wide letter-spacing. Heavy clamp() h1. Body
  line-height ~1.58. Centered .wrap, max-width ~980px, padding 0 22px. Body sits on --paper with
  two faint radial gradients (white highlight top-left toned to .04 in dark, dark tint bottom-right).
- Fixed top-right theme toggle button (mono) showing "moon Dark" / "sun Light", aria-labelled. A
  head script sets the theme before paint from localStorage('theme') with a prefers-color-scheme
  fallback (try/catch). Light is the absence of data-theme; dark is data-theme="dark".
- Global stamp-red :focus-visible outlines.

LAYOUT
- Header: mono stamp-red eyebrow, an h1 "EV vs ICE" (EV in green, ICE in orange), a one-line lede,
  and an explainer paragraph defining EV and ICE and stating hybrids/PHEVs are excluded.
- A units segmented toggle (Imperial / Metric) above the panels.
- Two-column grid (collapses to one on narrow screens): left = input panels, right = a sticky
  results card.

INPUT PANELS (left)
1) "How you drive": two range sliders. Distance per year (default 12000 mi, 2000-40000 step 500)
   and Years kept (default 7, 1-15 step 1). Show the live value next to each.
2) "The two cars": per-car number inputs in EV | ICE pairs: price (both 40000), amount financed
   (0/0), finance APR % (7/7), loan term yr (5/5), maintenance per year (700/1200), insurance per
   year (1900/1600). Note that 0 financed means cash and that maintenance/insurance defaults are
   placeholders.
3) "EV rebates at time of purchase": one rebate amount (default 0) applied to the EV price only.
   Note that post-purchase tax credits are not included.
4) "Energy prices": Electricity cents/kWh (18) and Gasoline $/gallon (3.93). A collapsible
   "Fine-tune" holds EV efficiency mi/kWh (3.5) and ICE MPG (32), plus a note that registration is
   excluded.

UNITS
- Keep all math in canonical imperial internally; metric is display only. Constants KM=1.60934,
  LPG=3.78541, and 235.215 for US-MPG <-> L/100km.
- Metric labels: Kilometres per year, Gasoline $/litre, EV efficiency km/kWh, ICE fuel use L/100km,
  and "Cost to fuel, per km". On read, convert km->miles (/KM), $/L->$/gal (*LPG), km/kWh->mi/kWh
  (/KM), L/100km->MPG (235.215 / value). Switching units snaps the distance slider to a round value
  in the new unit and re-displays converted numbers.

MATH (run live on every input)
- totalMiles = miles * years.
- evPerMile = (cents/100)/effMiPerKwh; icePerMile = $perGal / mpg.
- evFuel = totalMiles*evPerMile; iceFuel = totalMiles*icePerMile.
- evMaint = evMaintYr*years; iceMaint = iceMaintYr*years.
- evInsTotal = evIns*years; iceInsTotal = iceIns*years.
- loanInterest(P,APR,termYears): if P<=0 return 0; r=(APR/100)/12; n=termYears*12; if r<=0 return 0;
  payment=P*r*(1+r)^n/((1+r)^n-1); return payment*n-P. Apply per car with its own amount/APR/term.
- evBuy = max(0, evPrice - rebate).
- evTotal = evBuy+evFuel+evMaint+evInterest+evInsTotal.
- iceTotal = icePrice+iceFuel+iceMaint+iceInterest+iceInsTotal.
- delta = iceTotal-evTotal (positive => EV cheaper).
- Guard against bad input: coerce blanks to 0; use positive fallbacks for efficiency/MPG/rates so
  there is no divide-by-zero.

RESULTS CARD (right)
- A semicircular SVG gauge (viewBox 0 0 320 200, center 160,160, radius ~128): an orange "ICE" arc
  and label on the left, a green "EV" arc and label on the right, a center tick, and a needle that
  rotates by angle = tanh(delta/9000)*82 degrees (clockwise/right = EV). Tint the needle and the
  big center number green/orange/neutral by who wins. Respect prefers-reduced-motion.
- A verdict block: a label, the dollar gap, and a sentence. If |delta| < 1500 say "Too close to
  call on cost"; else "EV/ICE is cheaper to own by $X over N years".
- Two stacked cost bars (EV and ICE), each titled "EV/ICE: Total Cost of Ownership" with the total.
  Segments in order Purchase, Energy/fuel, Maintenance, Financing, Insurance; each width =
  value/max(evTotal,iceTotal,1)*100%. A legend with five swatches.
- A "What's driving it" table with rows: Cost to fuel per mile/km (show both cars in cents),
  Fuel/energy over N yrs, Maintenance gap, Insurance gap (full period), Financing interest (show
  both interests), Sticker gap (after EV rebates). For each gap, color and label the winner; sticker
  uses gasCar - evBuy positive => EV cheaper.
- Context flags below: if canonical miles >= 18000 show a green "high mileage favors EV" note (with
  the distance in the active unit); if miles <= 6000 and EV is winning show a neutral "low mileage
  weakens the case" note.
- A keyboard-operable "reset to defaults" control (role=button, tabindex, Enter/Space) that also
  returns units to Imperial.

FOOTER
- Default driving assumptions (12,000 miles per year, kept 7 years), a note that default running
  costs reflect recent US figures, and an MIT License line linking to LICENSE.

Verify before finishing: the page is fully self-contained, the math is invariant to the unit
toggle, there are no em dashes, and the only innerHTML writes use hardcoded literals plus formatted
numbers (no user strings).
```
