# Obsidian Wikilink Rules + File Reference Convention

## Decision Tree — When to Use Which Reference Form

볼트 안에서 다른 파일을 가리킬 때 **3가지 표기** 중 하나를 선택. 잘못 쓰면 (a) 깨진 링크, (b) Inbox 에 빈 파일 자동 생성, (c) LLM 이 컨텍스트로 못 가져감 등의 문제 발생.

| 상황 | 표기 | 예시 |
|------|------|------|
| **Vault 내부 파일** 일반 참조 (default) | `[[wikilink]]` | `[[canonical]]`, `[[🏛 CMDS Head Quarter]]` |
| **에이전트가 작업 시 자동 로드해야 할** 핵심 파일 | `@` import | `@.claude/rules/wikilink-rules.md`, `@1. Identity/canonical.md` |
| **Vault 외부 경로** (`/DEV/` · `~/.claude/skills/` · `/Users/...` 등) | 백틱 코드 | `` `/Users/yohankoo/DEV/9yohan-constellation/index.html` `` |
| 외부 URL · 라이브 사이트 | 그대로 | `https://9yohan.cmdspace.work` |
| 코드 식별자 · 명령어 · handle | 백틱 코드 | `` `kepler.map` ``, `` `vercel deploy --prod` `` |

**Default 는 `[[wikilink]]`**. `@` import 와 백틱은 명확한 이유가 있을 때만 사용.

---

## 1. `[[wikilink]]` — Default for Vault-Internal References

### Syntax

| Pattern | Usage |
|---------|-------|
| `[[Note Name]]` | Basic link |
| `[[Note Name\|Display Text]]` | Aliased link |
| `[[Note Name#Heading]]` | Link to heading |
| `[[Note Name^block-id]]` | Link to block |
| `![[Note Name]]` | Embed file |
| `![[image.png]]` | Embed image |

### Rules

1. **Always use wikilinks `[[]]`** for internal references, NOT markdown links
2. **Wikilinks in YAML must be quoted**: `"[[link]]"` not bare `[[link]]`
3. **Array fields** (`author:`, `attendees:`, `aliases:`): Use array format with quoted wikilinks
4. **Emoji prefixes are PART of the filename — never strip them**. Files with 📜/📚/🏛/🏷/📎/📦/🔖/📈/🎹/📘 prefixes require the exact emoji in the wikilink. If you write `[[Schema는 Harness다]]` instead of `[[📜 Schema는 Harness다]]`, Obsidian treats it as a missing link and **auto-creates an empty placeholder file in `00. Inbox/`** when clicked. This pollutes the inbox with orphan files.
5. **Verify before linking**: Before writing a wikilink to a file with an emoji prefix, use Glob/Bash to confirm the exact filename including the prefix. Never guess.
6. **Use aliases for cleaner display**: If the visible text shouldn't show the emoji, use the aliased form: `[[📜 Long Title|Display Text]]` — the link still resolves correctly because the target before `|` is exact.

### Examples in This Vault

- `[[🏛 CMDS Head Quarter]]` — Main hub
- `[[📚 620 Generative AI]]` — CMDS category
- `[[구요한]]` — People note (no prefix)
- `[[🏷 Meeting Notes]]` — Index page
- `[[📜 Schema는 Harness다 - Karpathy LLM Wiki와 CMDS의 구조적 동치에 관한 보고서|Schema는 Harness다 보고서]]` — Aliased link with emoji-prefixed target

### Anti-Pattern (DO NOT)

```markdown
❌ [[Schema는 Harness다 보고서]]                    # Missing 📜 prefix → creates empty file in Inbox
❌ [[CMDS Head Quarter]]                          # Missing 🏛 prefix → creates empty file
❌ [[620 Generative AI]]                          # Missing 📚 prefix → creates empty file
```

```markdown
✅ [[📜 Schema는 Harness다 - Karpathy LLM Wiki와 CMDS의 구조적 동치에 관한 보고서]]
✅ [[🏛 CMDS Head Quarter]]
✅ [[📚 620 Generative AI]]
```

---

## 2. `@` Import — When the Agent Must Auto-Load the File

`@path/to/file.md` 는 Claude Code 의 **import directive**. wikilink 와 다른 기능적 의미:

- `[[wikilink]]` = Obsidian 의 *navigation hyperlink* (사람이 클릭해서 이동). LLM 도 보지만 자동 로드는 X.
- `@path/to/file.md` = Claude Code 의 *context import* (LLM 컨텍스트에 파일 본문이 자동 삽입). 사람에겐 plain text.

### When to Use

다음 중 하나라도 해당하면 `@` import 사용:

1. **세션 시작 시 반드시 같이 로드되어야** 의미 있는 파일 (예: `CLAUDE.md` 가 `@.claude/rules/indentation-rules.md` 를 import — 이 룰 없이 LLM 이 인덴테이션 실수)
2. **프로젝트 README/entry point** 가 작업 시 같이 읽혀야 할 정본을 명시할 때 (예: 9요한 README 가 `@1. Identity/canonical.md` 를 import — README 만 보고 canonical 안 읽으면 매핑 모름)
3. **Cascade 가 필요한 룰** (CLAUDE.md → rules → sub-rules)

### When NOT to Use

- 단순 navigation 목적 (사용자가 클릭해서 이동만 원하는 경우) → `[[wikilink]]`
- 너무 많이 import 하면 컨텍스트 낭비 — **꼭 자동 로드돼야 의미 있는 것만**
- 운영 디테일·시나리오·아카이브는 보통 import 대상 아님 (필요 시 LLM 이 별도 Read)

### Path Syntax

- **Vault root 기준 상대경로**: `@CLAUDE.md`, `@.claude/rules/wikilink-rules.md`
- **현재 파일 위치 기준 상대경로**: `@./neighbor.md`, `@../parent-folder/file.md`
- **같은 폴더 내**: 파일명만으로 OK (`@ecosystem-plan.md`)
- **하위 폴더**: 폴더 경로 포함 (`@1. Identity/canonical.md`)

### Examples

```markdown
# CLAUDE.md (vault root)
@.claude/rules/indentation-rules.md
@.claude/rules/wikilink-rules.md
@.claude/rules/frontmatter-standard.md

# 9yohan/README.md (project entry point)
@1. Identity/canonical.md
@2. Implementation/constellation.md
@ecosystem-plan.md
```

### Pairing Pattern: `@` import + `[[wikilink]]`

CLAUDE.md 가 사용하는 표준 패턴 — *기능* 과 *navigation* 양쪽 충족:

```markdown
> @CLAUDE.md → [[CLAUDE.md]] — Claude Code specific (precedence: 1)
> @AGENTS.md → [[AGENTS.md]] — Other AI coding agents (precedence: 2)
```

`@` 가 LLM 자동 로드 · `[[]]` 는 사람이 클릭해서 이동.

### Anti-Pattern

```markdown
❌ @[[CLAUDE.md]]                # @ + wikilink 혼용 — 둘 다 깨짐
❌ @"CLAUDE.md"                  # 따옴표 불필요
❌ @/Users/yohankoo/...          # 절대경로는 동작은 하나 portable 하지 않음
❌ 모든 관련 파일을 @ import     # 컨텍스트 낭비 — 핵심 정본만 import
```

---

## 3. 백틱 코드 — Vault 외부 경로 · 코드 식별자

다음은 wikilink 대상이 아니므로 **백틱 코드**로 표기:

- Vault 외부 절대경로: `` `/Users/yohankoo/DEV/9yohan-constellation/` ``
- `~/.claude/skills/...` 같은 글로벌 스킬 경로
- 셸 명령어: `` `vercel deploy --prod` ``
- 코드 심볼: `` `kepler.map` ``, `` `Task Packet` ``
- 외부 라이브러리·패키지 이름

### Why Not Wikilink?

Vault 외부 파일은 Obsidian 이 resolve 하지 못함. wikilink 로 쓰면 빈 placeholder 가 생성되거나 깨진 링크로 남음.

---

## 4. 파일 이동·개명·삭제 → 의존 파일 갱신 (Dependency Update) ⚠️

파일을 **이동(move)·개명(rename)·삭제(delete)** 하면 그 파일을 가리키던 다른 파일들의 링크가 깨진다. **항상** 변경 전후로 인바운드 의존성을 찾아 함께 갱신할 것. "갱신할 때 의존성 체크"는 파일 변경 작업의 *기본값*이다.

### Rule

1. **변경 전 인바운드 전수 검색**: `grep -rl "<old-basename>" "<vault>"` (또는 Obsidian `backlinks file=<name>`). wikilink(`[[X]]`) · 임베드(`![[X]]`) · `@import` · 본문 텍스트 참조 모두 확인.
2. **basename 이 바뀌면 모든 인바운드 `[[old]]` → `[[new]]`** 로 갱신. 표시 텍스트 유지가 필요하면 aliased 형식 `[[new-basename|보이던 텍스트]]`. (표 셀 안에서는 `|` 를 `\|` 로 이스케이프.)
3. **이동만 하고 basename 이 그대로면** wikilink 는 안 깨짐(Obsidian 은 basename 으로 resolve) → wikilink 갱신 불필요. 단 `@import`(경로 기반) · 절대경로 · `![[...]]` 임베드 경로는 점검.
4. **`@import`(CLAUDE.md 등)** 는 basename 이 아니라 *경로*로 resolve → 이동 시 반드시 경로 갱신.
5. **삭제 시**: 인바운드가 남으면 깨진 링크 / Inbox 빈 placeholder 생성. 단 일부 unresolved 링크는 *의도된 지식 씨앗*이므로 무조건 지우지 말 것(판단 후 처리).
6. **인덱스·MOC·프로젝트 허브** 노트는 거의 항상 인바운드 보유 → 개명 시 1순위 갱신 대상.

### Checklist (파일 변경 시 매번)

- [ ] 변경 전 `grep -rl "<old-name>"` 로 인바운드 전수 확인
- [ ] basename 변경 시 모든 `[[old]]` → `[[new]]` (표 셀은 `\|`)
- [ ] `@import` 경로 / 인덱스·MOC·허브 링크 갱신
- [ ] 변경 후 `grep -rl "<old-name>"` 재실행 → **0 건** 확인

---

## Quick Reference Card

```
Vault 내부 .md → [[wikilink]]              (default)
   ↓ 단, 에이전트가 자동 로드해야 한다면
Vault 내부 .md → @path/to/file.md          (LLM context import)

Vault 외부 / 코드 / 명령어 → `백틱`        (no wikilink possible)
URL / 라이브 사이트 → 그대로               (https://...)
```

---

## 5. `%%...%%` 주석 = 에이전트 지시 메모 (Agent Directive Comments) — 2026-08-17

사용자가 노트 본문에 남기는 `%%...%%` Obsidian 주석은 단순 메모가 아니라 **AI 에이전트를 위한 프롬프트 지시**다 (예: `%%이거 검증 필요함%%`, `%%내 노트들 연결할 것%%`).

### Rule

1. **노트를 처리(정리·포맷·갱신)할 때 `%%...%%` 를 발견하면 지시로 읽고 이행한다** — 검증, 연결, 조사, 보완 등.
2. **이행 후에는 지시 주석을 결과물로 교체한다** (지시문을 남겨두지 않음). 이행 내용은 본문에 자연스럽게 녹이고, 검증류는 `검증:` 접두로 결과를 명시.
3. **이행 불가능하면** 주석을 보존하고 사용자에게 무엇이 막혔는지 보고한다.
4. **예외 — 기능성 주석은 지시가 아니다**: 숨은 태그 컨벤션 (`%% #John #John4 %%` 등 콜아웃 내 태그 은닉)은 그대로 보존한다. 판별 기준: 문장형(동사 포함)이면 지시, 태그/데이터만 있으면 기능성.

### Examples

- `%%이거 검증 필요함%%` → 사실 확인 후 `- 검증: ...` 부연으로 교체
- `%%내 노트들 연결할 것%%` → 볼트 검색 후 관련 노트 wikilink 블록으로 교체
- `%% #John #John4 %%` → 보존 (숨은 태그)

---

## 6. Cross-Vault 상호참조 표준 (마더십 ↔ 위키 볼트) — 2026-08-27

Obsidian wikilink 는 볼트 경계를 넘지 못한다. 볼트 간 참조는 **advanced-uri 마크다운 링크**가 표준이다 (`obsidian://open` 은 폴백).

### Frontmatter 필드 — 방향별로 이름이 다르다

| 방향 | 필드 | 값 형식 |
|------|------|---------|
| 마더십 노트 → 위키 페이지 | `wikiVaultRelated:` | 마크다운 링크 배열 (아래 형식) |
| 위키 페이지 → 마더십 노트 | `mainVaultRelated:` | 동일 형식, vault 만 반대 |

### 표준 형식 (advanced-uri)

```yaml
wikiVaultRelated:
  - "[LLM Wiki: Epistemic Infrastructure](obsidian://advanced-uri?vault=CMDS_LLM_Wiki&filepath=20.%20Wiki%2F21.%20Concepts%2FEpistemic%20Infrastructure.md)"
```

```yaml
# 위키 측 (역방향)
mainVaultRelated:
  - "[Mothership: 포맷은 사고를 강제한다](obsidian://advanced-uri?vault=CMDSPACE_Local_MBP&filepath=30.%20Permanent%20Notes%2F%ED%8F%AC%EB%A7%B7%EC%9D%80%20%EC%82%AC%EA%B3%A0%EB%A5%BC%20%EA%B0%95%EC%A0%9C%ED%95%9C%EB%8B%A4.md)"
```

작성 규칙:

1. **액션 이름은 `advanced-uri`** — `adv-uri` 는 존재하지 않는 액션 (링크 죽음). `obsidian://` 콜론 뒤 공백 금지.
2. **`filepath=` 는 볼트 루트 기준 전체 경로 + `.md` 확장자**, URL 인코딩 (공백 `%20`, `/` 는 `%2F`). 한글은 인코딩 없이도 동작하지만 공백·슬래시는 반드시 인코딩.
3. **링크 라벨은 `{볼트 별칭}: {페이지명}`** — `LLM Wiki: X` / `Mothership: X`.
4. **쓰기 전 대상 파일 실존 확인** (Glob/ls). 존재하지 않는 filepath 는 조용히 죽는 링크가 된다.
5. **본문 산문에서는** 텍스트 참조 `→ LLM Wiki: {page}` 도 계속 허용 (가벼운 언급용). 클릭 가능해야 하는 참조는 마크다운 링크 형식.

### 폴백 — Advanced URI 플러그인이 대상 볼트에 없을 때

advanced-uri 는 **대상 볼트에 `obsidian-advanced-uri` 플러그인이 설치·활성화**되어 있어야 동작한다. 대상 볼트의 `.obsidian/plugins/obsidian-advanced-uri/` 존재를 확인할 수 없거나 없으면, 플러그인 불요인 기본형으로 폴백:

```yaml
wikiVaultRelated:
  - "[LLM Wiki: Epistemic Infrastructure](obsidian://open?vault=CMDS_LLM_Wiki&file=20.%20Wiki%2F21.%20Concepts%2FEpistemic%20Infrastructure)"
```

폴백 형식 차이: 액션 `open` · 파라미터 `file=` · **`.md` 확장자 없이**. (2026-08 현재 마더십·CMDS_LLM_Wiki 양쪽 모두 플러그인 설치 상태 → 표준형 사용.)

### Anti-Pattern

```yaml
❌ "[X](obsidian: //advanced-uri?...)"        # 콜론 뒤 공백 — 프로토콜 인식 실패
❌ "[X](obsidian://adv-uri?...)"              # 존재하지 않는 액션 이름
❌ "[[LLM Wiki 페이지]]"                       # wikilink 는 볼트 경계 못 넘음 → Inbox 빈 파일 생성
❌ filepath=20. Wiki/21. Concepts/X.md        # 미인코딩 공백·슬래시 — 파라미터 파싱 깨짐
❌ open?file=...X.md                          # 폴백형에 .md 붙임 — "X.md.md" 탐색
```
