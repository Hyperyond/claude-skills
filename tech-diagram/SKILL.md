---
name: tech-diagram
description: Create polished dark-themed architecture or flow diagrams as self-contained HTML files using CSS Grid layouts. Use when the user asks for system architecture, infrastructure, user-flow, sequence, or process diagrams. Two layouts supported — "architecture" (tiered: clients → API → services → data) and "flowchart" (vertical sequential step cards). All output is a single self-contained .html file with inline styles, optional Google Font, and a built-in PNG/PDF export toolbar.
---

# Tech Diagram Skill (HTML/CSS)

Produces a single self-contained `.html` file rendering a diagram using **CSS Grid + semantic boxes** — no SVG, no client-side framework. Diagrams reflow naturally on viewport changes and never have overlapping text (the historical pain point with SVG layouts).

Two layouts are supported. **Pick one** based on what the user is asking for:

| Pattern | Template | Use when… |
|---|---|---|
| **flowchart** | `resources/flowchart-template.html` | Showing a sequence of steps over time (user journey, request lifecycle, deployment pipeline, onboarding). Vertical step cards, each with columns: step number · what happens · owner · API call · service. |
| **architecture** | `resources/architecture-template.html` | Showing a system at rest (modules, persistence, integration boundaries). Tiered top-to-bottom layout: clients → API surface → services → data, with cross-cut sidebands. |

Both share the same design tokens (palette, font, export toolbar) defined in `resources/design-system.md`. **Read that file before customizing** — it specifies the colors, semantics, and spacing rules.

## When to invoke this skill

Trigger phrases:

- "draw a system / architecture / infrastructure / topology diagram"
- "show me the data flow / user flow / request flow / process"
- "make a flowchart of …"
- "diagram the auth flow / API surface / deployment / pipeline"

Refuse / redirect if:

- The user wants a Mermaid diagram inline in a Markdown file — that's a one-liner, no skill needed
- The user wants editable / interactive diagrams (drawio, Figma) — this skill produces static-looking HTML
- The user wants a UML class diagram or ERD — better tools exist (PlantUML, dbdiagram.io)

## How to produce a diagram

1. **Pick the template** (flowchart or architecture) based on the user's intent.
2. **Copy** the template to the target output path (typically `docs/` or wherever the user asks).
3. **Read `resources/design-system.md`** to refresh on the palette and component patterns.
4. **Customize** the placeholders. Each template has `[PLACEHOLDER]` markers and HTML comments explaining what to fill in.
5. **Preserve the toolbar** — the `⋯` export menu with Copy/PNG/PDF actions ships with both templates. Do not strip the html2canvas / jsPDF CDN scripts or the `id="report-container"` on the outermost div, or export will break.
6. **Validate constraints** before claiming done:
    - Output is a single `.html` file (no external CSS, no JS frameworks)
    - Google Font link is the only external resource besides the two export scripts
    - No SVG — only HTML elements + CSS
    - Renders correctly when opened from `file://`
7. **Stay honest** — if the user gives you incomplete info, leave `[TBD]` or `[verify with X]` placeholders rather than inventing details. Diagrams that look authoritative but fabricate field names cause real harm downstream.

## Color palette (semantic, not decorative)

Palette is derived from the **Anthropic brand**: warm dark surface + orange / blue / green accents + mid-gray neutrals. The mapping is **semantic** — use the right color for the right component type:

| Semantic role | Anthropic color | Token |
|---|---|---|
| Frontend / Client / UI | Blue `#6a9bcc` | `--cyan` |
| Backend service / module | Green `#788c5d` | `--emerald` |
| Persistence / Database / Storage | Mid Gray `#b0aea5` | `--violet` |
| API contract / Integration / Bus | Orange `#d97757` (primary accent) | `--orange` |
| External system / Hand-off / Security boundary | Burnt orange `#a35138` (+ dashed border) | `--rose` |
| Neutral / Generic / External actor | Mid Gray (text-muted) | `--text-muted` |

> Token names (`--cyan`, `--emerald`, etc.) are historical labels — the hex values come from the Anthropic brand palette. Full mapping table is in `resources/design-system.md`.

Background is always `#141413` (Anthropic Dark). Body text is `#faf9f5` (Anthropic Light) for emphasis, `#b0aea5` (Anthropic Mid Gray) for secondary, `#7a7870` for muted notes.

## Typography

Two Anthropic brand fonts, loaded from Google Fonts in a single request:

- **Poppins** (Arial fallback) — headings, labels, badges, pills, UI chrome
- **Lora** (Georgia fallback) — body paragraphs ("reading content")
- System monospace — only inside `<code>` snippets for technical content

Sizes:

- Page title: `1.5rem` / weight 700
- Card title: `0.78–0.9rem` / weight 600
- Body / detail: `0.72–0.78rem` / muted color
- Pills / badges: `0.7–0.72rem` / weight 500–600
- Tiny labels (legends, footnotes): `0.65–0.7rem`

## File structure of generated diagram

```html
<!DOCTYPE html>
<html>
<head>
  <!-- title, fonts, html2canvas + jsPDF scripts, inline <style> -->
</head>
<body>
  <div class="container" id="report-container">   <!-- captured by export -->
    <div class="header">…title + toolbar…</div>
    <div class="arch-container or flow-container">
      …the actual diagram boxes…
    </div>
    <div class="cards">…3 summary info cards…</div>
    <p class="footer">…</p>
  </div>
  <script>…copyAsImage, downloadPNG, downloadPDF…</script>
</body>
</html>
```

## Anti-patterns (learned the hard way)

- **Do NOT use SVG with `<text>` elements** for the main diagram body. Text won't wrap, fonts render inconsistently, and any layout change requires recomputing pixel coordinates. CSS Grid + flex handle wrapping for free.
- **Do NOT put placeholder API paths into the diagram without flagging them.** Add a note at the bottom: "Endpoints shown are illustrative — final contract belongs in the OpenAPI spec."
- **Do NOT invent technical details** (table columns, framework choices, mechanisms) just because they make the diagram look more complete. Flag uncertainty with ⚠️ or "TBD".
- **Do NOT skip the responsive media queries** in the template — Tier 3 / step columns collapse to 2-col then 1-col at narrower widths, which the templates already handle.
- **Do NOT modify the export toolbar markup** — the buttons assume specific class names and the `ignoreElements` filter in the scripts looks for `.toolbar`. If toolbar is removed, do it cleanly by removing both the markup and the entire `<script>` block.

## Reference docs

- [design-system.md](resources/design-system.md) — palette, component patterns, spacing rules (read this before customizing)
- [flowchart-template.html](resources/flowchart-template.html) — sequential step layout (contains illustrative placeholder steps you can replace)
- [architecture-template.html](resources/architecture-template.html) — tiered system layout (contains illustrative placeholder tiers you can replace)
