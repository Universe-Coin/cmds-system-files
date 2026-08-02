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
