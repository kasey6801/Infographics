# Code as a Project Management Tool: How This Was Made

**Last updated:** 2026-07-12

## What this is

A single-page overview of the [Claude Code: Project Process Guide v1.2](https://github.com/kasey6801/learn-by-building/blob/main/ClaudeCode-Getting-Started/ClaudeCode-Getting-Started_v1.2.md), written for project managers who are new to using code as a working tool. It lives at `claude-code-for-project-managers/index.html` in the Infographics collection.

## The angle

The source guide is a full reference: tool installation, terminal commands, GitHub setup, and a complete project process. This page does not repeat the reference material. Instead it makes one argument to one audience: the guide's process is project management, and a PM's existing skills (breaking problems into steps, defining success precisely, testing results critically) are the ones that matter.

The core device is a mapping table that translates each guide artifact into the PM practice it corresponds to:

| PM practice | Guide artifact |
| --- | --- |
| Change control | `CHANGE_LOG.md` numbered entries with reversal steps |
| Prioritized backlog | `Backlog.md` with P0 through P3 priorities |
| Definition of done | Acceptance criteria written before work starts |
| Quality audits | QC rounds in `QC.md` |
| Risk management | Security practices, `.env`, archive-before-delete |
| Knowledge management | `DESIGN_NOTES.md` |
| Effort tracking | `EFFORT_INVESTMENT.md` |

The Step 0 design-first phase is framed as planning before execution with five gates, and its Goldfish Comprehension Test is framed as the handover-document test: a session with no background must be able to pick up the work and proceed.

## Editorial choices

- **No terminal commands are shown.** The source guide itself says the command tables are a reference for understanding what Claude does on your behalf, not a syllabus. For this audience, showing bash would undercut the argument.
- **Content that made the cut:** the you-decide-Claude-executes division of labor, the four-tool table in project-office terms, the artifact mapping, Step 0, the AI-does-what-you-ask safety habits, build-versus-buy criteria for a first project, and the condensed setup checklist.
- **Content that did not:** JavaScript vs. Python explainers, curl and git command tables, the release workflow details, and the documentation update matrix. All remain one click away in the source.
- Structure follows the collection style guide: warm paper-and-ink palette, system fonts, one self-contained HTML file, dark mode with the shared `theme` localStorage key, MIT license footer, no em dashes.

## Sources

- Kasey6801. (2026). *Claude Code: Project process guide v1.2*. GitHub. https://github.com/kasey6801/learn-by-building/blob/main/ClaudeCode-Getting-Started/ClaudeCode-Getting-Started_v1.2.md
- Rensin, D. (2026). *Elephants, goldfish, and the new golden age of software engineering*. Medium. https://drensin.medium.com/elephants-goldfish-and-the-new-golden-age-of-software-engineering-c33641a48874
