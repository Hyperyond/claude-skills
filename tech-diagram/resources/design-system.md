# Design System — HTML/CSS Diagram

Shared design tokens, components, and spacing rules. Both `flowchart-template.html` and `architecture-template.html` follow this. Palette and typography are derived from the **Anthropic brand**: orange / blue / green accents on warm dark surface, Poppins headings, Lora body.

## Color tokens (CSS variables)

Use these as `var(--name)`. Don't introduce new colors — if a new role appears, decide which semantic role it maps to. Token names (`--emerald`, `--cyan`, `--violet`, etc.) are historical labels; the **actual hex values come from the Anthropic brand palette**, and the semantic mapping is documented below.

```css
:root {
  /* surfaces — Anthropic Dark theme */
  --bg:                #141413;                       /* Anthropic Dark */
  --panel:             rgba(40, 38, 33, 0.6);         /* lifted surface */
  --panel-strong:      rgba(40, 38, 33, 0.85);
  --border:            #2a2823;                       /* warm dark border */

  /* text */
  --text:              #faf9f5;                       /* Anthropic Light */
  --text-muted:        #b0aea5;                       /* Anthropic Mid Gray */
  --text-dim:          #7a7870;                       /* footnotes, dim notes */
  --text-faint:        #5a5852;                       /* column headers, labels */

  /* semantic palette */

  /* Backend service — Anthropic Green */
  --emerald:               #788c5d;
  --emerald-fill:          rgba(120, 140, 93, 0.15);
  --emerald-fill-strong:   rgba(120, 140, 93, 0.4);

  /* Frontend / client — Anthropic Blue */
  --cyan:                  #6a9bcc;
  --cyan-fill:             rgba(106, 155, 204, 0.2);
  --cyan-fill-strong:      rgba(106, 155, 204, 0.4);
  --cyan-fill-soft:        rgba(106, 155, 204, 0.12);

  /* API / bus / integration — Anthropic Orange (primary accent) */
  --orange:                #d97757;
  --orange-fill:           rgba(217, 119, 87, 0.2);
  --orange-fill-soft:      rgba(217, 119, 87, 0.1);

  /* External / boundary — burnt-orange derivative + dashed border convention */
  --rose:                  #a35138;
  --rose-fill:             rgba(163, 81, 56, 0.3);
  --rose-fill-soft:        rgba(163, 81, 56, 0.15);

  /* Persistence / storage — Anthropic Mid Gray (warm) */
  --violet:                #b0aea5;
  --violet-fill:           rgba(176, 174, 165, 0.12);
  --violet-fill-strong:    rgba(176, 174, 165, 0.28);
}
```

### Anthropic brand source (for reference)

If you need to add a new semantic role, pick from these official accents first:

| Anthropic name | Hex | Used in skill for |
|---|---|---|
| Dark | `#141413` | page background |
| Light | `#faf9f5` | primary text |
| Mid Gray | `#b0aea5` | secondary text + storage tokens |
| Light Gray | `#e8e6dc` | (reserved — use for very subtle highlights) |
| Orange (primary accent) | `#d97757` | API / integration / bus |
| Blue (secondary accent) | `#6a9bcc` | frontend / client |
| Green (tertiary accent) | `#788c5d` | backend service |

## Semantic mapping

| Component type | Use color | Why |
|---|---|---|
| Browser / mobile UI / SPA / any consumer-facing app | **cyan** | Reads as "front of house" |
| Server-side service / module / microservice | **emerald** | Reads as "engine room" |
| Postgres / Redis / S3 / event store / any table or persistence | **violet** | Calm, "at rest" |
| REST endpoint pill / OpenAPI / message broker / event bus | **orange** | Pops between layers |
| External vendor / regulator / 3rd-party hand-off / out-of-system | **rose** + dashed border | Reads as "edge / careful" |
| User / device / physical asset / generic actor | **slate** (use `--panel` background with `--text-muted` border) | Neutral |

## Typography

Two Anthropic-brand fonts loaded from Google Fonts in one request:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Lora:ital,wght@0,400;0,500;0,600;1,400&family=Poppins:wght@400;500;600;700&display=swap" rel="stylesheet">
```

| Element | Font | Why |
|---|---|---|
| Headings, labels, badges, pills, UI chrome | **Poppins** (Arial fallback) | Anthropic brand heading font · clean sans for UI |
| Body paragraphs (`.step__body p`, `.sideband__card p`, `.card li`, `.subtitle`, `.flow-notes p`, `.arch-notes p`, `.node p`) | **Lora** (Georgia fallback) | Anthropic brand body font · serif reads as "content" |
| `<code>` snippets | `ui-monospace, 'SF Mono', Menlo, monospace` | System mono for technical content |

Sizes:

- Page title: `1.5rem` / weight 700
- Card title: `0.78–0.9rem` / weight 600
- Body / detail: `0.72–0.78rem` / muted color
- Pills / badges: `0.7–0.72rem` / weight 500–600
- Tiny labels (legends, footnotes): `0.65–0.7rem`

Body line-height: `1.55`. Letter spacing: tightened for headings (`-0.025em`); widened for tiny labels (`0.08em`–`0.1em`).

## Reusable component patterns

### Node card (`.node`)

```html
<div class="node node--emerald">
  <h4>① Auth · Identity</h4>
  <p>Magic link / OTP · session · age + consent</p>
  <p class="tables">tables: users · sessions · email_verifications</p>
</div>
```

```css
.node {
  border-radius: 8px;
  padding: 0.75rem 0.85rem;
  border: 1.2px solid var(--text-dim);
  background: var(--panel);
  display: flex;
  flex-direction: column;
  gap: 0.35rem;
}
.node h4 { font-size: 0.78rem; font-weight: 600; }
.node p  { font-size: 0.68rem; color: var(--text-muted); line-height: 1.5; }
.node .tables { color: var(--violet); font-size: 0.66rem; }

.node--cyan    { background: var(--cyan-fill);    border-color: var(--cyan);    }
.node--emerald { background: var(--emerald-fill); border-color: var(--emerald); }
.node--violet  { background: var(--violet-fill);  border-color: var(--violet);  }
.node--rose    { background: var(--rose-fill-soft); border-color: var(--rose); border-style: dashed; }
.node--orange  { background: var(--orange-fill-soft); border-color: var(--orange); }
```

### Pill (API endpoint or short label)

```html
<span class="pill">POST /auth/start</span>
<span class="pill pill--rose">L3 hand-off (TBD)</span>
```

```css
.pill {
  display: inline-block;
  padding: 0.25rem 0.55rem;
  border-radius: 4px;
  font-size: 0.7rem;
  font-weight: 500;
  background: var(--orange-fill);
  border: 1px solid var(--orange);
  color: var(--orange);
  white-space: nowrap;
}
.pill--rose { background: var(--rose-fill-soft); border-style: dashed; color: var(--rose); border-color: var(--rose); }
```

### Badge (role / owner indicator)

```html
<span class="badge badge--cyan">Web UI</span>
```

```css
.badge {
  display: inline-block;
  padding: 0.35rem 0.75rem;
  border-radius: 999px;
  font-size: 0.72rem;
  font-weight: 500;
  white-space: nowrap;
}
.badge--cyan    { background: var(--cyan-fill-strong);  border: 1px solid var(--cyan);    color: var(--cyan);    }
.badge--emerald { background: rgba(6, 78, 59, 0.6);     border: 1px solid var(--emerald); color: var(--emerald); }
.badge--rose    { background: var(--rose-fill);         border: 1px solid var(--rose);    color: var(--rose);    }
```

### Connector arrow (vertical, between cards)

```html
<div class="connector" aria-hidden="true"></div>
```

```css
.connector {
  width: 2px;
  height: 26px;
  background: var(--text-dim);
  margin: 0 auto;
  position: relative;
}
.connector::after {
  content: '';
  position: absolute;
  bottom: -2px; left: 50%;
  transform: translateX(-50%);
  border-left: 5px solid transparent;
  border-right: 5px solid transparent;
  border-top: 7px solid var(--text-dim);
}
```

### Sideband (cross-cutting concern)

Used for audit logs, integration layers — anything that touches multiple cards but doesn't fit a tier:

```html
<div class="sideband">
  <div class="sideband__card is-violet">
    <h4>Cross-cut · Audit Log</h4>
    <p>Every service writes here. Audit-grade timestamps for regulator use.</p>
    <p class="meta">table: audit_log · retention TBD</p>
  </div>
</div>
```

```css
.sideband { display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; margin-top: 1.5rem; }
.sideband__card { padding: 1.1rem 1.3rem; border-radius: 10px; border-width: 1.2px; border-style: solid; }
.sideband__card.is-violet { background: var(--violet-fill); border-color: var(--violet); }
.sideband__card.is-violet h4 { color: var(--violet); }
.sideband__card.is-orange { background: var(--orange-fill-soft); border-color: var(--orange); }
.sideband__card.is-orange h4 { color: var(--orange); }
.sideband__card p { color: var(--text-muted); font-size: 0.74rem; margin-bottom: 0.4rem; }
.sideband__card .meta { color: var(--orange); font-size: 0.7rem; margin-top: 0.7rem; }
```

### Info cards (summary at bottom of page)

Three-up grid showing key takeaways. Same `.card` pattern in both templates:

```html
<div class="cards">
  <div class="card">
    <div class="card-header"><div class="card-dot emerald"></div><h3>Title</h3></div>
    <ul>
      <li>• point one</li>
      <li>• point two</li>
    </ul>
  </div>
</div>
```

## Spacing rules

- Outer page padding: `2rem`
- Container max-width: `1280px` (centered)
- Diagram container internal padding: `1.75rem`
- Card-to-card vertical gap: `26px` connector OR `0.85rem` grid gap
- Border radius: `6–10px` on small elements, `1rem` (16px) on outer containers
- Grid gaps: `0.85rem` between sibling cards, `1rem` between tiers

## Background grid

Both containers paint a subtle 40px grid using CSS `linear-gradient` (tuned to the Anthropic warm dark surface):

```css
background-image:
  linear-gradient(rgba(42, 40, 35, 0.5) 1px, transparent 1px),
  linear-gradient(90deg, rgba(42, 40, 35, 0.5) 1px, transparent 1px);
background-size: 40px 40px;
background-position: -1px -1px;
```

## Responsive breakpoints

```css
@media (max-width: 1024px) {
  /* multi-column tiers collapse to 2 columns */
  .tier { grid-template-columns: 1fr; }
  .tier--clients .tier__body,
  .tier--services .tier__body,
  .tier--data .tier__body { grid-template-columns: repeat(2, 1fr); }
  /* flowchart step cards: collapse owner/api/service into below-body rows */
  .flow-headers { display: none; }
  .step { grid-template-columns: 56px 1fr; row-gap: 0.75rem; }
  .step__owner, .step__api, .step__service { grid-column: 2; }
}
@media (max-width: 640px) {
  /* single column on phones */
  .tier--clients .tier__body,
  .tier--services .tier__body,
  .tier--data .tier__body { grid-template-columns: 1fr; }
}
```

## Export toolbar (must keep intact)

The two CDN scripts at the top of the file and the three `<script>` functions at the bottom together implement Copy / PNG / PDF export via html2canvas. The toolbar is **collapsed by default behind a `⋯` button** to keep the header clean. Click it to reveal the three buttons.

CDN scripts (versions pinned, SRI hashes verified):

```html
<script src="https://cdn.jsdelivr.net/npm/html2canvas@1.4.1/dist/html2canvas.min.js" integrity="sha384-ZZ1pncU3bQe8y31yfZdMFdSpttDoPmOZg2wguVK9almUodir1PghgT0eY7Mrty8H" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/jspdf@2.5.2/dist/jspdf.umd.min.js" integrity="sha384-en/ztfPSRkGfME4KIm05joYXynqzUgbsG5nMrj/xEFAHXkeZfO3yMK8QQ+mP7p1/" crossorigin="anonymous"></script>
```

Required markup:
- `<div class="container" id="report-container">` wraps everything captured
- `<div class="toolbar">` has `.toolbar-actions` (collapsed) + `.toolbar-toggle` (the `⋯`)
- The three buttons call `copyAsImage(this)`, `downloadPNG(this)`, `downloadPDF(this)`

If the user explicitly doesn't want export, remove **both** the markup AND the entire `<script>` block at the bottom — don't leave half of it.

## Honesty rules

Diagrams are easy to misread as authoritative. Apply these checks:

1. Anything you can't verify in the user's source material gets a ⚠️ marker or "TBD" label
2. Field names, API paths, table columns: if not in the spec, write `[illustrative]` in a footnote
3. Mechanisms that are "open points" in the source spec must show as such (e.g., dashed border, "(TBD)" suffix)
4. Add a permanent "Notes" strip in the footer reminding the reader what's illustrative vs confirmed
