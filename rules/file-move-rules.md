# file-move-rules — 볼트 파일·폴더 이동 규칙

> 정본. 에이전트가 볼트에서 파일이나 폴더를 옮길 때 반드시 따른다.
> 근거 사고: 2026-08-23, 9yohan 정본을 `00. Inbox/…/2026-04-19-9yohan-orchestration/` 에서
> `70. Outputs/74. Projects/9yohan Constellation/` 으로 옮기면서 `CMDS.md`·`📖 900 Divisions.md`의
> 포인터가 **넉 달간 조용히 죽어 있었다.** 케플러(OpenClaw)가 실제로 그 경로를 열려다 실패해 발견.

## 왜 조용히 깨지는가

깨진 참조는 두 종류인데, **한 종류만 감시되고 있었다.**

| 참조 형태 | 예 | Obsidian `unresolved` | 이동 시 |
|---|---|---|---|
| 위키링크 | `[[70. Outputs/…/canonical]]` | ✅ 잡힘 | Obsidian 안에서 옮기면 자동 갱신 |
| **백틱 경로** | `` `70. Outputs/…/canonical.md` `` | ❌ **안 잡힘** | **절대 자동 갱신 안 됨** |

백틱 경로는 링크 그래프에 존재하지 않는다. 그래서 `obsidian unresolved` 로는 영원히 못 찾는다.
게다가 `mv`·스크립트로 옮기면 위키링크조차 자동 갱신되지 않는다 (Obsidian이 관여하지 않으므로).

## 규칙

1. **이동 전에 참조를 먼저 센다.**
   ```bash
   OLD="2026-04-19-9yohan-orchestration"      # 옮길 대상의 식별 조각
   grep -rln "$OLD" "$VAULT" --include="*.md"
   ```
   0건이면 그냥 옮긴다. 1건 이상이면 2번으로.

2. **옮긴 뒤 같은 턴에서 참조를 갱신한다.** 다음 턴·나중으로 미루지 않는다.
   - 새 경로가 **실존하는지 먼저 확인**하고 치환한다. 존재하지 않는 곳으로 바꾸면 고아를 옮긴 것뿐이다.
   - 치환 규칙을 여러 개 쓸 때는 **긴 패턴부터**. 짧은 패턴이 먼저 매칭되면 앞부분이 남아
     `00. Inbox/…/9yohan Constellation/…` 같은 **존재하지 않는 혼종 경로**가 생긴다
     (2026-08-23 실제 발생 — 수리 중에 같은 실수를 냈다).

3. **역사 기록은 고치지 않는다.** 데일리·위클리·발행 기록·요한 스크래치·감사 노트처럼
   "그때 그랬다"를 적은 문서는 과거 경로가 남아 있는 게 정확한 기록이다.
   살아있는 포인터("지금 여기를 보라")만 고친다.

4. **끝나면 검사기를 돌린다.**
   ```bash
   python3 ~/.claude/scripts/vault-path-audit.py          # 요약
   python3 ~/.claude/scripts/vault-path-audit.py --verbose # 파일:줄 전량
   ```
   종료코드 1 = 깨진 경로형 참조 있음. 제외 목록: `~/.claude/vault-path-audit-ignore.txt`.
   특정 줄만 넘기려면 그 줄에 `path-audit: ignore` 를 남긴다.

## 검사기가 보는 것 / 안 보는 것

- **본다**: 볼트 최상위 관례(`NN. …`)로 시작하는 백틱 경로·위키링크가 실존하지 않을 때.
- **안 본다**: 개념 위키링크(`[[어떤 개념]]`) — 젤텔카스텐의 미작성 링크는 깨진 게 아니라 아직 안 쓴 것이다.
  템플릿 자리표시자(`{env}`, `YYYY-MM-DD-…`), 생략부호(`90. Settings/...`), 외부 레포 경로, 코드블록 안.
- 교차 볼트 참조(위키가 마더십 경로를 문서화)는 **정상으로 인정**한다 — 전 볼트 합집합으로 해석한다.

## 관련

- [[wikilink-rules]] · [[directory-structure]] · [[file-creation-rules]]
- 주간 점검: `~/.claude/scripts/weekly-measure-all.sh` 가 매주 자동 실행하고 `~/.claude/logs/vault-path-audit.log` 에 적재
