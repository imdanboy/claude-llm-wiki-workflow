# claude-llm-wiki-workflow

Claude Code 플러그인. 프로젝트별 `docs/` 디렉토리 기반의 LLM 친화 워크플로우를 스킬로 제공한다.

## 제공 스킬

3-tier — **공유** (vault 자동감지), **프로젝트 전용** (`project-*`), **홈 전용** (`home-*`).

| 스킬 | 분류 | 설명 |
|------|------|------|
| `/llm-wiki:moc-sync` | 공유 | vault 의 `index.md` (MOC) 를 각 노트 frontmatter description 만으로 카테고리별 인덱스로 재생성 |
| `/llm-wiki:digest` | 공유 | `raw/` 원본 자료 → 정제 노트 (topics/projects/wiki) + index 한 줄 추가 제안 |
| `/llm-wiki:project-init` | 프로젝트 | 신규 프로젝트에 `docs/{wiki,raw,task,logs,reports}/` 골격 + `_TEMPLATE.md` + `docs/index.md` 부트스트랩, CLAUDE.md 스니펫 안내 |
| `/llm-wiki:project-new-task` | 프로젝트 | 오늘 날짜의 작업 명세 `docs/task/YYYYMMDD.md` 생성 (기본: 직전 명세의 미완료 항목 carry-over, `--clean` 옵션) |
| `/llm-wiki:project-daily-report` | 프로젝트 | 오늘의 task spec + git log + logs 를 종합해 `docs/reports/YYYY-MM-DD.md` 작성 |
| `/llm-wiki:project-weekly-report` | 프로젝트 | 이번 ISO 주의 일일 보고들을 통합해 `docs/reports/weekly-YYYY-Www.md` 작성 |
| `/llm-wiki:project-wiki-sync` | 프로젝트 | 최근 작업 결과를 바탕으로 `docs/wiki/` 업데이트 diff 를 제안 (적용 전 사용자 승인) |
| `/llm-wiki:home-init` | 홈 | `~/wiki/` 골격 부트스트랩 (`topics/projects/journal/experiments/raw + _templates + index/todos/links`) |
| `/llm-wiki:home-journal-today` | 홈 | `~/wiki/journal/YYYY-MM-DD.md` frontmatter 갖춰 생성 |

## 설치

각 머신에서 한 번:

```
/plugin marketplace add git@github.com:imdanboy/claude-llm-wiki-workflow.git
/plugin install llm-wiki@claude-llm-wiki-workflow
```

업데이트:

```
/plugin marketplace update claude-llm-wiki-workflow
```

## 디렉토리 구조

```
.claude-plugin/marketplace.json    ← marketplace 카탈로그
plugins/llm-wiki/
├── .claude-plugin/plugin.json     ← 플러그인 매니페스트
└── skills/                         ← 스킬 정의 (각 디렉토리에 SKILL.md)
    ├── moc-sync/SKILL.md
    ├── digest/SKILL.md
    ├── project-init/SKILL.md
    ├── project-new-task/SKILL.md
    ├── project-daily-report/SKILL.md
    ├── project-weekly-report/SKILL.md
    ├── project-wiki-sync/SKILL.md
    ├── home-init/SKILL.md
    └── home-journal-today/SKILL.md
```

`v0.3.0` 부터 `commands/<name>.md` (legacy) → `skills/<name>/SKILL.md` 로 이전됨. Claude Code 의 두 표면은 `/<name>` 호출이 동일하나 공식 권장 구조가 `skills/` 디렉토리.

## 신규 프로젝트 부트스트랩

프로젝트 루트에서:

```
/llm-wiki:project-init
```

생성된 `docs/` 구조와 안내된 CLAUDE.md 스니펫을 붙여넣고 시작한다.

## 워크플로우 컨벤션

- `docs/task/YYYYMMDD.md` — 그날의 작업 명세. 항목 마커:
  - `[ ]` ready, `[WIP]` 사용자 작성 중 (실행 금지), `[DONE]` 완료
- `docs/logs/<date>/<slug>/` — 세션 중간 산출물 (gitignored 권장)
- `docs/reports/` — 일일 / 주간 보고 (git tracked)
- `docs/wiki/` — 영속 레퍼런스 (frontmatter 필수)
- `docs/raw/` — 위키 source raw 데이터
- `docs/index.md` — MOC (vault 라우팅 인덱스). vault root = `<repo>/docs/`, wikilink 는 leading-slash 형식 (`[[/wiki/note]]` — `/docs/` prefix 없음).

세부 컨벤션은 `~/wiki/WIKI-CONVENTIONS.md` 를 single source-of-truth 로 따른다.

자동 commit / push 는 절대 수행하지 않는다 — 모든 커밋은 사용자가 명시적으로.
