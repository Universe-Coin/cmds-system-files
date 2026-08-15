# File Creation Rules

## Code Output Location

**ALL code-related outputs MUST be saved in:** `00. Inbox/03. AI Agent/` under the appropriate environment subfolder.

| Subfolder | Agent | Machine |
|-----------|-------|---------|
| `03-1. Claude Code (MBP)/` | Claude Code | MacBook Pro |
| `03-2. Claude Code (Studio)/` | Claude Code | Mac Studio |
| `03-3. OpenClaw (MBP)/` | OpenClaw | MacBook Pro |
| `03-4. OpenClaw (Studio)/` | OpenClaw | Mac Studio |
| `03-5. Codex (MBP)/` | Codex | MacBook Pro |
| `03-6. Codex (Studio)/` | Codex | Mac Studio |
| `03-7. Antigravity (MBP)/` | Antigravity (Google) | MacBook Pro |
| `03-8. Antigravity (Studio)/` | Antigravity (Google) | Mac Studio |

**Auto-detection**: Check base path to determine machine:
- `/Users/yohankoo/Local Obsidian_MBP/` → MBP
- `/Users/yohankoo/Obsidian_Local/` → Studio

## File Naming Convention

- Include date: `YYYY-MM-DD-description.ext`
- Use descriptive names
- Examples: `2026-01-09-data-analysis.py`, `2026-01-09-meeting-summary.md`

## Session Link Frontmatter (새 노트 생성 시)

cmux 안의 Claude Code 세션에서 **볼트에 새 .md 노트를 만들 때**, 프론트매터에
생성 세션의 딥링크를 넣는다 — 노트에서 클릭 한 번으로 그 노트를 만든 세션으로
복귀 (세션이 죽어 있으면 같은 cwd에서 `claude --resume`으로 부활):

```bash
cmux-voice hooklink   # → omnicontrol://focus?workspace=…&cwd=…&session=…&revive=1
```

```yaml
session-link: "<hooklink 출력값 그대로>"
```

- hooklink 실패(OmniControl 데몬 다운·cmux 밖 실행) 시 **생략하고 진행** — 노트 생성을 막지 말 것
- 기존 노트 수정 시에는 추가하지 않는다 (생성 시점의 출생 기록만)
- 시스템 상세: `40. Docs/42. AI Generated/2026-08-02-session-link-딥링크-시스템.md` · OmniControl repo `docs/CMUX-GUIDE.md`

## Agent Worklog (작업 지식 축적, 2026-08-03)

에이전트 작업 기록의 물리적 홈은 `40. Docs/42. AI Generated/` 하위 5폴더 —
직접 파일을 만들지 말고 **`cmux-voice worklog` CLI로 생성**한다
(파일명 `YYYY-MM-DD-제목.md`·템플릿·session-link 자동):

| kind | 폴더 | 무엇을 |
|------|------|--------|
| `research` | `Research/` | 실측·조사로 확정한 사실 |
| `trouble` | `Troubleshooting/` | 재발 가능한 에러 해결 (증상/원인/해결/재발 방지) |
| `spec` | `Specs/` | 새 기능·시스템 구현 명세 |
| `module` | `Modules/` | 재사용 코드·패턴 |
| `review` | `Reviews/` | 프로젝트 회고 (**주간 회고는 weekly-review 잡 관할 — 여기 금지**) |

```bash
echo "<본문>" | cmux-voice worklog trouble "afplay 볼륨 무시" --project OmniControl
```

- 기록 기준·상시 규칙: 전역 `~/.claude/CLAUDE.md` "Agent Worklog" 섹션 (세션이 스스로 기록)
- 세이프티넷: OmniControl 스케줄 잡 `worklog-daily`(매일 22:17)가 당일 DEV 커밋을 훑어 미기록 유의미 작업을 보충 기록
- 42는 1차 축적층 — 영구 가치가 생기면 CMDS Process를 거쳐 `30. Permanent Notes`/위키로 승격
- 인덱스: `42. AI Generated/_INDEX Agent Worklog.md`

## Multi-File Project Folder Rule

When creating projects with multiple related files:
1. **FIRST** create an intermediate folder: `YYYY-MM-DD-project-name/`
2. **THEN** create all related files inside that folder

```
00. Inbox/03. AI Agent/03-5. Codex (MBP)/
└── 2026-01-18-project-name/
    ├── index.html
    ├── styles.css
    └── script.js
```

**Never** scatter related project files directly in subfolder root.

## Exception: Video Projects (Remotion / heavy deps)

Video projects with `node_modules` or large render artifacts MUST go to `/Users/yohankoo/DEV/video-projects/<name>/` instead of the vault. Only context/progress MD files stay in the vault.

See `video-project-workflow.md` for full rule.
