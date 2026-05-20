# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A collection of Claude Code **skills** authored for use elsewhere (e.g. installed into `~/.claude/skills/` or shipped as plugins). There is no build, no test runner, and no application to run — each top-level directory is a self-contained skill consumed by the Claude Code harness.

Currently one skill ships from here:

- [tech-diagram/](tech-diagram/) — generates self-contained dark-themed HTML architecture / flowchart diagrams from two CSS Grid templates.

When the user asks to "add a skill" or "edit the skill", they almost always mean editing files inside `tech-diagram/` (or creating a sibling directory with the same shape).

## Skill layout

Every skill in this repo follows the standard Claude Code skill shape:

```
<skill-name>/
  SKILL.md             # frontmatter (name, description) + invocation rules
  resources/           # files the skill copies or references at runtime
```

`SKILL.md` is the **only** file the harness reads to decide when to trigger the skill. Its YAML frontmatter `description` is what gets matched against user intent — keep it specific and full of trigger phrases, not generic marketing copy.

`resources/` holds template files and reference docs that the skill instructs Claude to read, copy, or customize. They are not auto-loaded; the SKILL.md must explicitly tell Claude to read them.

## tech-diagram conventions

Editing this skill, three things have to stay consistent or output silently breaks:

1. **Two templates, one design system.** [`flowchart-template.html`](tech-diagram/resources/flowchart-template.html) and [`architecture-template.html`](tech-diagram/resources/architecture-template.html) both inline a copy of the design tokens documented in [`design-system.md`](tech-diagram/resources/design-system.md). If you change a token value (color, spacing), update it in **all three** files. The markdown doc is the canonical reference but the templates are self-contained — they don't import from it.

2. **Semantic color mapping is load-bearing.** Token names (`--cyan`, `--emerald`, `--violet`, `--orange`, `--rose`) are historical labels; hex values come from the **Anthropic brand palette**. The mapping is semantic, not decorative:

   | Role | Token | Anthropic color |
   |---|---|---|
   | Frontend / client | `--cyan` | Blue `#6a9bcc` |
   | Backend service | `--emerald` | Green `#788c5d` |
   | Persistence / storage | `--violet` | Mid Gray `#b0aea5` |
   | API / bus / integration | `--orange` | Orange `#d97757` (primary accent) |
   | External / boundary | `--rose` | Burnt orange `#a35138` + dashed border |

   Don't introduce new colors — decide which semantic role a new component maps to and reuse the existing token.

3. **Export toolbar is fragile.** The two CDN scripts (html2canvas, jsPDF — versions pinned with SRI hashes) at the top and the `copyAsImage` / `downloadPNG` / `downloadPDF` functions at the bottom depend on:
   - `id="report-container"` on the outermost div
   - `.toolbar` / `.toolbar-actions` / `.toolbar-toggle` class names
   - The `ignoreElements` filter inside the scripts looking for `.toolbar`

   If a user wants to drop export, remove **both** the markup and the entire `<script>` block — don't leave half of it.

## Honesty rules (don't fabricate diagram detail)

Diagrams read as authoritative even when their content is made up. The skill's `SKILL.md` enforces this, and any edits should preserve it:

- Unverified API paths, table columns, field names → mark `[illustrative]` or ⚠️ / TBD
- Open mechanisms → dashed border + `(TBD)` suffix
- Keep a "Notes" strip in the footer distinguishing illustrative from confirmed

## Anti-patterns to avoid in tech-diagram output

- **No SVG `<text>`** for diagram bodies — text won't wrap, fonts render inconsistently, layout changes need pixel math. CSS Grid + flex handle it for free.
- **No external CSS, no JS frameworks** — output is a single `.html` file. Only allowed external resources: Google Fonts (Poppins + Lora in one request) + the two pinned CDN scripts for export.
- **Must render correctly from `file://`** — don't introduce anything that needs a dev server.
- **Don't strip the responsive media queries** — Tier 3 / step columns collapse to 2-col then 1-col at narrower widths; templates already handle this.

## Adding a new skill

1. Create `<skill-name>/SKILL.md` with frontmatter (`name`, `description`) — the description must read like trigger phrases, not marketing.
2. Put any templates or reference docs in `<skill-name>/resources/`.
3. In `SKILL.md`, explicitly tell Claude to read its own resources before customizing — they aren't auto-loaded.
4. List "refuse / redirect" conditions so the skill doesn't get misapplied (see `tech-diagram/SKILL.md` for the pattern).
