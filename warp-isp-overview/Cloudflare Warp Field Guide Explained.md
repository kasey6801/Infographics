# Cloudflare WARP Field Guide — Explained

This document summarises how the **"What can your ISP see?"** infographic
(`warp-isp-visibility-guide.html`) was built, the decisions made along the way,
and a single prompt you can reuse to regenerate the whole thing in one shot.

The deliverable itself is a **single, self-contained HTML infographic** that
explains Cloudflare WARP's privacy settings to a reader with little networking
knowledge. Every setting is framed around one central question: *what can your
ISP — and others on the wire — actually see?*

---

## 1. Research and grounding

Before writing anything, the underlying facts were verified by searching
Cloudflare's own documentation rather than relying on memory, because product
details change:

- **WARP modes** — "Traffic and DNS" (full tunnel) vs "DNS only" (lookups only).
- **WARP tunnel protocol** — MASQUE (HTTP/3 over QUIC/UDP, FIPS + post-quantum) vs WireGuard.
- **DNS protocols** — DNS over UDP (plaintext Do53, only safe inside the tunnel), DoH (self-encrypting over HTTPS), DoT (self-encrypting over a dedicated port).
- **Reset encryption keys** — regenerates the device keypair, re-registers, re-establishes the tunnel, pulls fresh config; a troubleshooting action, not an anonymity feature.
- **1.1.1.1 for Families** — None / Block Malware (1.1.1.2) / Block Malware + Adult (1.1.1.3); resolver returns `0.0.0.0` for blocked domains.
- **Zero Trust WARP** — the same client, but enrolled into an organisation's account it *inverts* the privacy model (logging, inspection, posture, optional HTTPS decryption).

## 2. Information architecture

- Picked a single organising idea: **ISP visibility**. Every option is rated by how much it leaves readable on the wire.
- Chose a **"redacted dossier"** metaphor — the ISP's logbook, progressively blacked out as protection increases. This is concrete and intuitive for non-technical readers.
- Defined the section flow: hero → who's on the wire → the redaction demo → the three switches → decision matrix → the catch → resources → footer.

## 3. Initial build

- Produced one self-contained HTML file in a warm "manila / dossier" aesthetic.
- Used weight-driven hierarchy, a monospace "log/stamp" voice for labels, and the guide's colour language: red = exposed, green = protected, orange = brand/structure.
- Added a gentle scroll-reveal and a hover/tap reveal for the redaction bars.

## 4. Iterative refinement

This was the bulk of the work. Changes, grouped by theme:

**Worked example**
- Switched the demo site to **wikipedia.org** with its real resolving IP, and made the black bars reveal the *actual* hidden value on hover/tap so the "peek at what leaked" tip is truthful.
- Corrected the "WARP off" log line to show the hostname (visible via DNS *and* via the TLS SNI) rather than an invented category.

**Decision matrix ("Choose your tunnel")**
- Rendered the mode as the **app's own dropdown control** (e.g. `Traffic and DNS (HTTPS) ▾`).
- Gave all three columns parallel labels: *If you want* → *Choose this mode* → *What your ISP sees*, with a three-segment visibility meter.

**Accuracy corrections**
- Reframed the intro: the ISP logging your activity is the **default**, not the variable — the real questions are *how long they keep it* and *what they do with it*.
- Made clear that **DNS-only modes still leak destinations** (the meter shows "some", not "nothing").
- Noted **DNS over UDP** is only safe *inside* the full tunnel.
- Clarified that **filtering ≠ privacy** (1.1.1.1 for Families doesn't change what the ISP sees).
- Avoided the term "denial of service" (which means a DDoS attack) in favour of "blocked or restricted".

**Content added**
- "What your browsing data can be used for" cards: **Sold for profit, Profiling** (inferring political leanings, group affiliation, gender, relationship status, opinions, health), **Advertising, Throttling, Blocked or restricted** (geo-restrictions / censorship), **Handed over** (authorities *or* a private party such as a copyright holder), **Leaked in a breach**.
- "Who's on the wire": added an **Eavesdroppers** card (public-Wi-Fi hackers as the everyday risk, state actors as the rare extreme), a **device-level note** (the OS and apps read your data before it's on the wire — WARP can't protect that), and an **ISP lawful-access note** (direct/real-time access mandated in many countries).
- A **"The catch"** section: the trust shift from ISP to Cloudflare, plus a **consumer vs Zero Trust** contrast.
- A **"Go deeper"** section linking the WARP app and Cloudflare's docs.

**Polish**
- Made the redaction bars reveal on **tap** (touch devices don't fire `:hover`), with keyboard toggling and `role="button"` / `aria-pressed`.
- Removed **Google Fonts** entirely in favour of system font stacks — no external lookup, no IP leak to a third party, identical offline rendering.
- Made the "Who's on the wire" cards **equal height**.

## 5. Quality assurance

Two static-analysis passes were run on the file:

- **Unused code** — found and removed three dead CSS rules (`.eyebrow`, `.seg.on-green`, `a.skip`); confirmed the only "unused" classes flagged afterwards (`in`, `revealed`) are applied at runtime by the scripts.
- **Security** — no external scripts, no `http://` links, no inline event handlers, no `eval` / `innerHTML` / `document.write` / `javascript:` URLs, no `localStorage`; all `target="_blank"` links carry `rel="noopener noreferrer"`; only Cloudflare hosts referenced, over HTTPS.
- **Runtime** — headless load produced no console errors or warnings; interactive elements verified.

## Design principles applied throughout

- **Accuracy over flourish** — several claims were corrected mid-build.
- **Zero external dependencies** — system fonts, inline SVG/emoji, no third-party requests.
- **Accessibility** — keyboard operation, ARIA state, visible focus, and meaning never carried by colour alone.
- **Honest framing** — the guide states WARP's limits plainly (it shifts trust to Cloudflare; it can't protect data on your own device).

---

## One-shot prompt

Paste the following to regenerate the guide in a single pass:

```
Create a single, self-contained HTML infographic (one .html file, no build step,
no external requests) that explains Cloudflare WARP's privacy settings to a
person with little networking knowledge. Organise everything around one central
question: "What can your ISP see?"

DESIGN
- Warm "manila / dossier" aesthetic on a light parchment background; ink text;
  accents: red = exposed, green = protected, orange = brand/structure.
- System fonts only (a sans stack and a monospace stack via CSS variables).
  Do NOT load Google Fonts or any external resource.
- Use weight for hierarchy; a monospace "log/label/stamp" voice for eyebrows.
- Responsive (multi-column on desktop, reflow to 1–2 columns on mobile).
- Accessible: keyboard-operable interactions, role="button"/aria-pressed where
  relevant, visible focus rings, prefers-reduced-motion respected, and never
  rely on colour alone to convey meaning.
- No localStorage/sessionStorage. Any links use target="_blank" rel="noopener
  noreferrer". Icons are inline SVG or emoji — no image requests.

SECTIONS
1) Hero — headline "What can your ISP see?" and a lede making clear the ISP
   logging your activity is the DEFAULT; the real questions are how long they
   keep it and what they do with it. Below it, a grid of cards titled "What your
   browsing data can be used for": Sold for profit; Profiling (inferring
   political leanings, group affiliation, gender, relationship status, opinions,
   even health); Advertising; Throttling; Blocked or restricted (geo-restriction
   / censorship); Handed over (to authorities OR a private party such as a
   copyright holder via legal request); Leaked in a breach. Add a caveat that
   what's permitted varies by provider and country.
2) Who's on the wire — equal-height cards: Your device (with a red "WARP can't
   protect this" note: the OS and installed apps read your data before any
   request leaves; varies by privacy policy); Your ISP (with a neutral
   "lawful access" note: many countries mandate direct/real-time authority
   access); Eavesdroppers (public-Wi-Fi hackers as the everyday risk, network
   operators and state actors as the rare extreme); Cloudflare WARP (the tunnel).
3) "The same line, redacted three ways" — one ISP log line for a visit to
   wikipedia.org (use its real resolving IP), shown three times: (a) WARP off /
   plaintext DNS — lookup, destination IP, and hostname all readable; (b)
   encrypted DNS only — lookup redacted, but destination and hostname (via TLS
   SNI) still readable; (c) full WARP tunnel — everything redacted, leaving only
   "sealed tunnel → Cloudflare". Black redaction bars reveal the actual hidden
   value on hover AND on tap/click (touch-safe), with keyboard toggling.
4) The three switches — Mode (Traffic and DNS vs DNS only); DNS protocol (DNS
   over UDP = plaintext, only safe inside the tunnel; DoH; DoT); 1.1.1.1 for
   Families (None / Block malware / Block malware + adult). Note that filtering
   changes what YOU can reach, not what the ISP sees.
5) "Choose your tunnel" decision matrix — three parallel columns: "If you
   want" (goal) → "Choose this mode" (rendered as the app's dropdown control,
   e.g. Traffic and DNS (HTTPS) ▾, plus the filter) → "What your ISP sees" (a
   three-segment visibility meter + a short verdict). Cover: max privacy; fastest
   (Traffic and DNS + UDP); kids' device (+ adult filter); just hide lookups
   (DNS only — meter shows "some" because destinations still leak); don't route
   everything through one company (DNS only); and WARP off (sees everything).
6) "The catch" — WARP doesn't make you invisible; it shifts trust from your ISP
   to Cloudflare. Add a consumer-vs-Zero-Trust contrast: the same app, when
   enrolled by a workplace/school, is built so the organisation can see, log,
   filter, and even decrypt your traffic.
7) "Go deeper" — link cards to https://one.one.one.one/,
   https://developers.cloudflare.com/1.1.1.1/, and
   https://developers.cloudflare.com/warp-client/.
8) Footer — where to find these settings; a short disclaimer that it isn't
   affiliated with Cloudflare.

ACCURACY REQUIREMENTS
- The ISP sees a hostname both via the DNS lookup and via the TLS SNI; encrypting
  DNS alone does not hide the site you connect to.
- Full WARP hides destinations from the ISP; DNS-only modes do not.
- DNS over UDP relies on the tunnel for encryption.
- 1.1.1.1 for Families is content filtering, not privacy.
- Do not call access-blocking "denial of service" (that means a DDoS attack).
- Verify current product details against Cloudflare's docs before asserting them.

Finally, run a QA pass: remove unused CSS, confirm no external requests or unsafe
patterns, and check it renders without console errors.
```
