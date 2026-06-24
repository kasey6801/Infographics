# Gemini Starter Dashboard — How It Was Made

A plain-language walkthrough of the interactive onboarding dashboard for a marketing/sales professional new to Google Gemini. This explains what the dashboard does, the thinking behind it, and how to recreate or adapt it yourself — no coding background required.

---

## 1. What it is

A single, self-contained web page that onboards a customer-facing marketing/sales person to Google Gemini in four weeks. It's "no-code" in two senses: the dashboard teaches Gemini techniques that need no coding, and the dashboard itself is one file you can open, share, or host without any setup.

It has six sections:

| Section | What it does |
|---|---|
| **Start here** | Maps where Gemini lives across Workspace (Gmail, Docs, Sheets, Slides, Meet, the Gemini app, Gems, Workspace Studio). |
| **4-week plan** | A checkable roadmap that progresses from first wins to building reusable assistants and automation. |
| **Prompts** | A filterable, copy-to-clipboard library of role-specific prompts. |
| **Workflows** | Four repeatable routines that chain Gemini steps together. |
| **Build a Gem** | The Persona / Task / Context / Format formula plus four ready-to-paste starter Gems. |
| **Good habits** | Verification and data-boundary guardrails for customer-facing work. |

---

## 2. The build process, step by step

### Step 1 — Ground it in current reality

Gemini in Workspace changes fast, so the first move was checking what's actually true right now rather than relying on older knowledge. Key facts that shaped the content:

- Gemini is now bundled into paid Workspace tiers (not a separate add-on), with the full side panel from Business Standard up.
- **Gems** — reusable custom assistants you configure once and can link to Google Drive files — are the single biggest time-saver for this role.
- **Workspace Studio** lets you automate recurring tasks in plain English, no code.
- "Take notes for me" in Meet, Sheets Canvas dashboards, and one-shot deck generation in Slides are all live.

This grounding is why the roadmap leans on Gems and automation rather than generic "ask the chatbot" advice.

### Step 2 — Anchor everything to one role

Rather than a generic AI tour, every prompt, workflow, and Gem was written for one persona: a marketing/sales person who **helps customers learn about new products and services**. That meant emphasizing explaining, demoing, and following up — account briefs, plain-language explainers, FAQs, follow-up emails, customer decks.

### Step 3 — Design for "open and use," not "read and forget"

The goal was a working tool, not a static document. So it was built with:

- **Tabbed navigation** so the six sections don't overwhelm at once.
- **A checkable roadmap with saved progress** — ticking a task updates the progress ring and persists, so you can return and pick up where you left off.
- **Copy buttons** on every prompt and Gem, because the whole point is to paste them into Gemini.
- **A progress ring** for a quick sense of momentum.

### Step 4 — Brand it so it feels like Gemini

The look uses a blue → purple → pink → amber gradient echoing Gemini's identity, clean white cards, rounded corners, and Material-style spacing — so a new user feels they're in the right universe.

---

## 3. The tech, in plain language

You don't need any of this to use the dashboard, but here's what's under the hood:

- **One HTML file.** Everything — layout, styling, and interactivity — lives in a single file. No installs, no libraries to download, no build tools. You can open it in any browser.
- **Plain web technologies.** It uses standard HTML, CSS, and a bit of JavaScript. No frameworks, which keeps it portable and easy to tweak.
- **Content is data-driven.** The roadmap tasks, prompts, workflows, and Gems are stored as simple lists near the top of the script. Editing the wording means changing text in those lists — you don't touch the layout.
- **Progress is saved automatically.** Checked tasks are stored by the artifact so your progress survives between visits. There's a "Reset my progress" link if you want a clean slate.

---

## 4. How to adapt it

The dashboard was built to be personalized. The easiest changes:

- **Swap the brackets for real details.** Replace `[product]`, `[Company]`, `[industry]` in the prompts and Gems with actual names to make them instantly usable.
- **Re-weight the role.** If the person is more sales (pipeline, quota) or more marketing (campaigns, content), adjust the roadmap tasks and prompt categories.
- **Match their Workspace tier.** Hide features they don't have, or note which tier unlocks what.
- **Change the pace.** Collapse the 4-week plan into a lighter 2-week version, or stretch it for a slower rollout.
- **Add your brand voice.** Drop in real tone guidance so the example outputs sound like the company.

To make any of these, just ask Claude in plain language — for example, *"Make all the prompts use Acme Analytics as the product and a friendly, no-jargon tone,"* and it will update the file.

---

## 5. One-shot prompt to recreate this

Paste the prompt below into Claude to generate the dashboard from scratch. It's written to be self-contained, so you can reuse or adapt it anytime.

```
Build a single self-contained HTML dashboard (one file, no external libraries,
no installs) that onboards someone NEW to Google Gemini who works in MARKETING
AND SALES and helps customers learn about new products and services. It must be
no-code and specific to Google Gemini + Google Workspace.

First, search the web to ground the content in CURRENT Gemini-in-Workspace
capabilities (Gems, Workspace Studio, the side panel across Gmail/Docs/Sheets/
Slides/Meet, "Take notes for me", Sheets Canvas, Deep Research). Don't rely on
older assumptions.

Include six tabbed sections:
1. "Start here" — a visual map of where Gemini lives across Workspace, plus the
   core idea that a Gem remembers context so you stop re-prompting.
2. "4-week plan" — a checkable roadmap: Week 1 Foundations & first wins,
   Week 2 weave it into customer work, Week 3 build your own Gems, Week 4
   workflows & automation. Each task has a title and one-line description.
3. "Prompts" — a filterable, copy-to-clipboard library of role-specific prompts
   (research & prep, email & outreach, explain a product, meetings & follow-up,
   content & decks, data & analysis). Note which Gemini surface each one uses.
4. "Workflows" — 3–4 repeatable multi-step routines (e.g. "call to follow-up in
   5 minutes") shown as numbered steps with the tool used at each step.
5. "Build a Gem" — teach the Persona / Task / Context / Format formula, then give
   4 ready-to-paste starter Gems with full instructions and a copy button.
6. "Good habits" — guardrails: verify before sending, data-privacy boundaries,
   the Docs linked-file limit, keep human judgment, turn winners into Gems,
   weekly review.

Interactivity & design:
- Tabbed navigation between sections.
- The roadmap checkboxes save progress automatically so it persists between
  visits, and update a circular progress ring at the top showing % complete.
  Include a "reset progress" link.
- Copy-to-clipboard buttons on every prompt and Gem.
- Gemini-style aesthetic: a blue→purple→pink→amber gradient, clean white cards,
  rounded corners, generous spacing. Mobile-friendly.
- Keep all content data-driven (store tasks/prompts/workflows/Gems as simple
  lists in the script) so it's easy to edit later.

Write all prompts and Gems for the customer-education angle (explaining,
demoing, following up), using [bracketed] placeholders for product/company names.
```

---

## 6. Setting it up on Claude

1. **Open Claude.** Go to [claude.ai](https://claude.ai) or the Claude desktop/mobile app.
2. **Pick a capable model.** Use a strong model (e.g. Claude Opus) for a polished, complete build.
3. **Turn on web search.** In the chat input settings, enable web search so Claude can ground the dashboard in the latest Gemini features. This is what keeps it accurate.
4. **Paste the one-shot prompt** from Section 5 and send it.
5. **Let it build.** Claude will research current Gemini capabilities, then create the dashboard as an interactive artifact that appears beside the chat. Open it and click through the tabs.
6. **Iterate in plain language.** Ask for changes conversationally — *"add our product name everywhere,"* *"make a 2-week version,"* *"only show Business Standard features."* Claude edits the same file.
7. **Save or share it.** Use the artifact's download/share options to export the HTML, host it, or — if you keep a GitHub project space — commit it there for reuse.

> **Tip:** If you want the dashboard tailored before it's even built, add your company, product, and brand tone to the end of the prompt. Claude will bake real examples in instead of leaving placeholders.
