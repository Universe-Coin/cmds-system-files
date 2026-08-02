---
date created: 2026-04-23T21:04
date modified: 2026-05-04T00:30
---
# Frontmatter Standard (Required Properties)

Every note in this vault MUST include these 7 required properties:

```yaml
---
type:           # Note type (note, meeting, people, terminology, curriculum, channel, CMDS, etc.)
aliases: []     # Alternative names (array format)
description: "" # 1-2 sentence English summary for LLMs — ALWAYS wrap in double quotes (see rules #6–#7)
author:
  - "[[구요한]]"  # Author as quoted wikilink array
date created:   # YYYY-MM-DD or YYYY-MM-DDTHH:mm (ISO 8601)
date modified:  # YYYY-MM-DD or YYYY-MM-DDTHH:mm (ISO 8601)
tags: []        # Relevant tags (array format)
---
```

## Rules

1. **Wikilinks in YAML must be quoted**: `"[[link]]"` not `[[link]]`
2. **Date format**: Always ISO 8601 — `YYYY-MM-DD` or `YYYY-MM-DDTHH:mm`
3. **Array format**: Use hyphen + space for arrays (author, tags, aliases)
4. **CamelCase for compound words**: `myRate`, `totalPage`, `startReadDate` (⚠️ `rating` 사용 금지 → 반드시 `myRate`)
5. **Status values** (5 options): `unread` / `reading` / `inProgress` / `completed` / `archived`
6. **`description` must be in English**: 1-2 sentences describing what the note contains and when an LLM should reference it. This is a machine-readable hint for AI agents (Claude Code, Gemini CLI, ChatGPT, etc.) to decide relevance in future sessions. Write it as a skill/tool description — specific, action-oriented, no fluff.
	- ✅ Good: `"Meeting minutes from 2026-04-07 LG AX camp retrospective. Contains CEO feedback summary and next-action items."`
	- ❌ Bad: `"회의록입니다"` (Korean, non-descriptive)
	- ❌ Bad: `"This is a note"` (no signal for relevance)
7. **`description` must be wrapped in double quotes `"..."`**: Long free-text strings (esp. `description`) must always use double-quote form. YAML 1.2 forbids `": "` (colon + space) and `" #"` (space + hash) inside plain (unquoted) scalars — they silently break the parser, causing description to truncate or corrupt all subsequent frontmatter fields. This is not a style preference; it is a parser-correctness requirement.
	- ✅ Safe: `description: "Draft curriculum ... Operations: 3 main + 6 assistants ..."`
	- ❌ Breaks Obsidian Properties panel: `description: Draft curriculum ... Operations: 3 main ...` (plain scalar with embedded `: `)
	- **Rule of thumb**: if the value contains any `:`, `#`, `[`, `]`, `{`, `}`, `,`, `&`, `*`, `?`, `|`, `>`, `!`, `%`, `@`, or spans beyond a short phrase, quote it. For multi-line text use `>-` (folded) or `|-` (literal) block scalars instead.
8. **No numeric tags**: Obsidian tags must contain at least one non-numeric character. Numeric-only values in `tags:` (e.g. `2`, `15`, `22`) break the Properties panel rendering (yellow warning). Never harvest body references like `#2` / `#22` (pipeline numbers, issue numbers) into `tags:` — they are not tags. In the **body**, when writing a `#숫자` reference (e.g. pipeline item number), wrap it in backticks — `` `#22` `` — so Obsidian does not parse it as a tag attempt.
	- ✅ `tags: [us-trip, starlink]` + body: ``파이프라인 `#22` 와 연결``
	- ❌ `tags: [us-trip, 2, 22, 15]` / body: `파이프라인 #22와 연결` (tag 오인 수집 → Properties 깨짐)

## Optional Properties

- `CMDS:` — CMDS category reference (quoted wikilink)
- `index:` — Index reference (quoted wikilink)
- `status:` — One of 5 standard values above

### `CMDS:` vs `index:` — Direction Rule ⚠️

Per 🏛 CMDS Guide (authoritative):

| Property | Points to | Examples |
|----------|-----------|----------|
| `CMDS:` | **📚 specific subcategory** (2nd-level, N01–N99) | `"[[📚 102 Topics]]"`, `"[[📚 210 Literature Reviews]]"`, `"[[📚 240 Books]]"`, `"[[📚 491 Codes]]"`, `"[[📚 840 Lectures]]"` |
| `index:` | **🏷 Index note** (aggregator in `90. Settings/96. Index/`) | `"[[🏷 Research Notes]]"`, `"[[🏷 Meeting Notes]]"`, `"[[🏷 Books]]"`, `"[[🏷 People]]"`, `"[[🏷 Prompts]]"`, `"[[🏷 Syntax and Codes]]"`, `"[[🏷 Lecture Notes]]"` |

**Common mistakes to avoid**:

- ❌ `CMDS: "[[📖 100 Themes]]"` (📖 top-level is conceptual; never a frontmatter value)
- ❌ `index: "[[📚 102 Topics]]"` (📚 belongs in `CMDS:`, not `index:`)
- ✅ `CMDS: "[[📚 102 Topics]]"` + `index: "[[🏷 Research Notes]]"`

**Exception — system files**: the 9 system files (CLAUDE.md, AGENTS.md, ANTIGRAVITY.md, CMDS.md, 🏛 CMDS Guide, 🏛 CMDS Head Quarter, BRAIN.md, BRAIN_PROMPT.md, DESIGN.md) are vault-top-level navigation documents and MAY use 🏛 hub notes (`"[[🏛 CMDS Head Quarter]]"`, `"[[🏛 CMDS Guide]]"`) in `index:`. Normal notes must still use 🏷 Index notes only. Do not "fix" system-file frontmatter to 🏷.

**Default 🏷 per CMDS range** (pick the closest fit, override if content dictates):

| CMDS range | Default `index:` |
|------------|-----------------|
| `📚 10X` (Themes) | `[[🏷 Research Notes]]` |
| `📚 2XX` (Literature) | `[[🏷 Research Notes]]` · `[[🏷 Books]]` for 240 |
| `📚 491 Codes` · `📚 493 Scripts` | `[[🏷 Syntax and Codes]]` |
| `📚 492 Prompts` | `[[🏷 Prompts]]` |
| `📚 5XX` (Products) | `[[🏷 Guideline]]` · `[[🏷 References]]` |
| `📚 6XX` (Specialties) | `[[🏷 Research Notes]]` |
| `📚 802 Articles` | `[[🏷 Draft Article]]` · `[[🏷 Outcomes]]` |
| `📚 840/841` (Lectures/Curriculum) | `[[🏷 Lecture Notes]]` |
| `📚 831 Consulting` | `[[🏷 Meeting Notes]]` · `[[🏷 Project Notes]]` |
| `📚 820 Research` | `[[🏷 Research Notes]]` |

The 📖 top-level names (📖 100 Themes, 📖 200 Literature …) are **conceptual labels** used in prose and UI copy, never inside frontmatter wikilinks.
