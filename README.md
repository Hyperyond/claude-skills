<div align="center">

# 🎛️ claude-skills

**A personal collection of Claude Code skills**
_battle-tested from real work · or just nice enough to keep around_

[![Claude Code](https://img.shields.io/badge/Claude_Code-skill_pack-d97757?style=flat-square)](https://claude.ai/code)
[![License](https://img.shields.io/badge/license-personal-b0aea5?style=flat-square)](#)
[![Skills](https://img.shields.io/badge/skills-1-6a9bcc?style=flat-square)](#-skills)

</div>

---

## ✨ What's inside

Each top-level directory is a self-contained skill. Drop it into `~/.claude/skills/` or bundle it as a plugin — no build step, no dependencies, nothing to install.

## 📦 Skills

<table>
<tr>
<td width="50%" valign="top">

### 🧱 [`tech-diagram/`](tech-diagram/)

Polished dark-themed **architecture & flowchart diagrams** rendered as a single self-contained HTML file.

- CSS Grid layouts — no SVG, no framework
- Anthropic brand palette · semantic color tokens
- Built-in **PNG / PDF** export toolbar
- Two layouts: `architecture` (tiered) · `flowchart` (sequential)

</td>
<td width="50%" valign="top">

<sub>**Trigger phrases**</sub>

> _"draw a system / architecture diagram"_
> _"show me the request flow"_
> _"make a flowchart of …"_
> _"diagram the deployment pipeline"_

</td>
</tr>
</table>

## 🗂️ Skill layout

Every skill in this repo follows the same shape:

```
<skill-name>/
├── SKILL.md          # frontmatter + invocation rules
│                     # ↑ the harness reads ONLY this to decide when to trigger
└── resources/        # templates, reference docs, anything the skill loads at runtime
```

The `description` field in `SKILL.md`'s frontmatter is what gets matched against user intent. Make it **specific** and pack it with trigger phrases — not marketing copy.

## 🧭 Conventions

- 🎨 **Semantic over decorative** — colors map to roles (frontend / backend / storage / API / boundary), not aesthetics
- 🔒 **Self-contained outputs** — generated artifacts should render from `file://` with no dev server
- ⚠️ **Honesty rules** — diagrams and outputs flag illustrative vs. confirmed detail; never fabricate to look complete
- 📐 **One source of truth per spec** — when tokens are duplicated across files for self-containment, keep them in sync

Full authoring notes live in [`CLAUDE.md`](CLAUDE.md).

---

<div align="center">

<sub>Curated by [@Oliver](https://github.com/) · Quality bar: would I use this on a Monday morning?</sub>

</div>
