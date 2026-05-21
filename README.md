# claude-llm-wiki-workflow

Claude Code 플러그인. 프로젝트별 `docs/` 디렉토리 기반의 LLM 친화 워크플로우를 슬래시 커맨드로 제공한다.

## 제공 슬래시 커맨드

| 커맨드 | 설명 |
|--------|------|
| `/llm-wiki:init` | 현재 프로젝트에 `docs/{wiki,raw,task,logs,reports}/` 골격 + `_TEMPLATE.md` 생성, CLAUDE.md 스니펫 안내 |
| `/llm-wiki:new-task` | 오늘 날짜의 작업 명세 `docs/task/YYYYMMDD.md` 를 생성 (기본은 직전 명세의 미완료 항목 carry-over) |
| `/llm-wiki:daily-report` | 오늘의 task spec + git log + logs 를 종합해 `docs/reports/YYYY-MM-DD.md` 작성 |
| `/llm-wiki:weekly-report` | 이번 ISO 주의 일일 보고들을 통합해 `docs/reports/weekly-YYYY-Www.md` 작성 |
| `/llm-wiki:wiki-sync` | 최근 작업 결과를 바탕으로 `docs/wiki/` 업데이트 diff 를 제안 (적용 전 사용자 승인) |

## 설치

각 머신에서 한 번:

```
/plugin marketplace add git@github.com:imdanboy/claude-llm-wiki-workflow.git
/plugin install llm-wiki
```

업데이트:

```
/plugin marketplace update claude-llm-wiki-workflow
```

## 신규 프로젝트 부트스트랩

프로젝트 루트에서:

```
/llm-wiki:init
```

생성된 `docs/` 구조와 안내된 CLAUDE.md 스니펫을 붙여넣고 시작한다.

## 워크플로우 컨벤션

- `docs/task/YYYYMMDD.md` — 그날의 작업 명세. 항목 마커:
  - `[ ]` ready, `[WIP]` 사용자 작성 중 (실행 금지), `[DONE]` 완료
- `docs/logs/<date>/<slug>/` — 세션 중간 산출물 (gitignored 권장)
- `docs/reports/` — 일일 / 주간 보고 (git tracked)
- `docs/wiki/` — 영속 레퍼런스
- `docs/raw/` — 위키 source raw 데이터

자동 commit / push 는 절대 수행하지 않는다 — 모든 커밋은 사용자가 명시적으로.
