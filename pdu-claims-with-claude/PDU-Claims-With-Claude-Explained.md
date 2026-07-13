# The pdu-claim-info skill guide: how this page was made

This guide introduces the `pdu-claim-info` Claude skill to project managers who are new
to agentic use of LLMs: what the skill is, how to install it from GitHub, how to use it,
and what its reports contain. The skill itself lives at
https://github.com/kasey6801/claude-skills (folder `skills/pdu-claim-info/`, packaged
bundle in `skills/dist/`).

## Source material

- The skill definition: `claude-skills/skills/pdu-claim-info/SKILL.md` (trigger rules,
  the annual live fetch of the PMI CCR Handbook, output structure, the 0.25 / 0.5 /
  0.75 / 1.0 PDU scale).
- The repo README's `pdu-claim-info` section: both install options (packaged `.skill`
  bundle and source-folder copy) and the usage triggers.
- Four real claim reports the skill generated on 2026-07-12 in `ClaudeDev/PDU_Claims/`
  (a 297-page PMI standard, two webinars, one podcast panel; 8.75 PDUs total). One of
  them, the Think Fast, Talk Smart panel (0.75 PDU, Power Skills), is shown on the page
  as a worked example.

## Build process

1. Read the skill's `SKILL.md` and the repo README so the install commands, triggers,
   and output structure on the page match the real skill, not a paraphrase.
2. Read the four generated claim reports and condensed one into the "A real example"
   card, keeping the details that show the skill's judgment: the over-claiming warning,
   the runtime-matched 0.75 value, and the unverified-date flag.
3. Followed the collection's `STYLE_GUIDE.md`: single self-contained HTML file, system
   font stack, paper-and-ink palette, hard offset shadows, mono uppercase section
   labels, dark `pre` blocks in both themes, dark mode via `data-theme` with the shared
   `theme` localStorage key, MIT footer.
4. Structured the page for the target audience: a short chatbot-vs-agent explanation, a
   prominent GitHub pointer panel with both install options, a four-step usage section
   with example prompts, the report-contents table, the worked example, and the PMI CCR
   reference table (PMP 60/8, PMI-ACP 30/4).
5. Included the skill's Knowledge Asset workflow as its own step: after each analysis
   the skill offers an Infographic, Report, or Presentation in .md, .pdf, or .pptx,
   asks for a save folder on first use (then remembers it via the `pdu_assets_folder`
   memory entry), and names files `PDU_<content-slug>_<date>.<ext>`.

## One-shot prompt to recreate it

> Build a single self-contained HTML guide in
> `CC_Infographics/pdu-claims-with-claude/` following the collection's STYLE_GUIDE.md
> (paper-and-ink field-guide look, system fonts, dark-mode toggle, MIT footer). The
> page introduces the pdu-claim-info Claude skill to project managers who are new to
> agentic LLM use. Read the skill's SKILL.md and the README section in the
> kasey6801/claude-skills repo first, and use the real install commands. Include: a
> short explanation of what a Claude skill is and why it is agentic rather than
> chatbot-style; a prominent dark panel pointing to
> https://github.com/kasey6801/claude-skills#pdu-claim-info with both install options
> as code blocks; a four-step usage walkthrough (drop in content; the skill triggers
> and fetches the live PMI CCR Handbook on first run; save the analysis as a knowledge
> asset in Infographic, Report, or Presentation form as .md, .pdf, or .pptx to a
> remembered assets folder; review and file the claim yourself at ccrs.pmi.org); a
> table of the report sections; one real worked example
> from the PDU_Claims folder; and the PMP / PMI-ACP PDU reference table. No em dashes.

## Notes

- The page stresses the human-in-the-loop boundary: the skill prepares the analysis and
  CCRS fields, the certificant verifies flagged items and submits the claim.
- CCR requirements cited from the PMI CCR Handbook, April 2026 edition; the skill
  fetches the live handbook on first run and prompts for an annual refresh.
