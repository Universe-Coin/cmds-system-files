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
- `wikiVaultRelated:` / `mainVaultRelated:` — **볼트 간 상호참조 (2026-08-27 표준)**: mothership 노트 → 위키 페이지는 `wikiVaultRelated:`, 위키 페이지 → mothership 노트는 `mainVaultRelated:`. 값은 advanced-uri 마크다운 링크 배열 — `"[LLM Wiki: {page}](obsidian://advanced-uri?vault=CMDS_LLM_Wiki&filepath={URL-encoded path}.md)"`. 대상 볼트에 Advanced URI 플러그인이 없으면 기본형 `obsidian://open?vault=...&file={path without .md}` 폴백. 액션명 `adv-uri` 오타·콜론 뒤 공백 금지. 형식 정본: `.claude/rules/wikilink-rules.md` §6.
- `published:` / `publishedUrl:` / `publishedChannels:` — **발행 추적 (2026-08-26 채택)**: 외부 채널에 발행된 콘텐츠의 마스터 노트에는 발행 시점에 `published: true` (boolean 체크박스) + `publishedUrl:` (정본 URL, 예: `https://jisan.cmdspace.work/posts/{slug}/`) + `publishedChannels:` (배열 — `blog`/`threads`/`x`/`linkedin`/`kakao`/`newsletter`)를 기입한다. 발행 전 초안·SNS 캠페인 노트는 `published: false`로 시작 (cmds-sns-promo 스킬 컨벤션과 동일). URL이 여러 채널이면 정본(블로그) URL을 `publishedUrl:`에, 나머지는 `publishedChannels:`로. OSMU 매핑 정본: 지산 프로젝트 `08-발행-매트릭스.md` §6.
- `model:` / `effort:` — **AI 작성 노트 표기** (2026-08-17 채택, LLM Wiki 페르소나 컨벤션 이식): 에이전트가 작성·대필한 노트는 정확한 모델 ID 와 reasoning effort 를 기록한다 — `model: "claude-fable-5[1m]"` · `effort: "xhigh"`. `author:` 는 `"[[구요한]]"` 유지 (볼트 소유자), 모델 필드가 AI 작성자 기록. 데일리 노트는 추가로 `timezone:` (KST) 과 `dailyStatus:` (pending → filled → final) 를 사용 (`.claude/commands/daily.md` 정본). **기입 의무 범위 (2026-08-25 확장, 레인 감사 후속)**: 데일리 노트만이 아니라 에이전트가 생성하는 모든 산출물 — `70. Outputs/74. Projects/**` · `00. Inbox/03. AI Agent/agents/**` · `40. Docs/42. AI Generated/**` — 에 `model:` 을 기입한다. 이 필드가 채워지면 폴더 휴리스틱 없이 사람/기계 레인을 판별할 수 있다 ([[2026-08-23-lane-classification-audit]]).

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
