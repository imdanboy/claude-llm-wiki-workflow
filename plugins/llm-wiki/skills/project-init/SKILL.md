---
description: 현재 프로젝트에 LLM 위키 워크플로우 골격을 부트스트랩한다 (docs/{wiki,raw,task,logs,reports}/ + index.md + todos.md + ideas.md + _TEMPLATE.md + AGENTS.md 정본 + CLAUDE/GEMINI import 안내)
---

# /llm-wiki:project-init

LLM 위키 + task spec + daily/weekly report 워크플로우를 현재 프로젝트에 부트스트랩한다.
이 커맨드는 멱등 (idempotent) — 이미 존재하는 파일은 덮어쓰지 않는다.

## 동작

1. **사전 점검**: 현재 디렉토리가 git 루트인지 `git rev-parse --show-toplevel` 로 확인. git 루트가 아니면 사용자에게 "여기서 진행할까요?" 물어보고 진행. 작업은 항상 `pwd` 기준.

2. **디렉토리 생성** (mkdir -p, 멱등):
   - `docs/wiki/`
   - `docs/raw/`
   - `docs/task/`
   - `docs/logs/`
   - `docs/reports/`

3. **`docs/task/_TEMPLATE.md`**: 존재하지 않을 때만 다음 내용으로 Write:

   ```markdown
   ---
   name: YYYYMMDD
   title: YYYY-MM-DD task spec
   description: <오늘 작업의 한 줄 요약 — TODO 채운 후 갱신>
   tags: [task]
   aliases: []
   created: YYYY-MM-DD
   updated: YYYY-MM-DD
   ---

   # YYYY-MM-DD

   ## TODO
   1. [ ] <짧은 제목>

   ## 1.
   **목표:**

   **컨텍스트:**

   **지시사항:**

   **완료 기준:**
   ```

4. **`docs/index.md`**: 존재하지 않을 때만 다음 내용으로 Write (프로젝트 MOC):

   ```markdown
   ---
   name: index
   title: <project-name> Project Wiki (MOC)
   description: 프로젝트 위키 진입점. docs/ 하위 wiki/task/reports/raw/logs 카테고리별 한 줄 description 인덱스.
   tags: [moc, index]
   aliases: [README, home]
   created: <today>
   updated: <today>
   ---

   # <project-name> Project Wiki

   프로젝트 docs 의 진입점. 각 노트의 한 줄 description 만 모아 LLM 이 어떤 파일을 읽을지 토큰 거의 안 쓰고 결정할 수 있게 한다.

   **컨벤션은 `~/wiki/WIKI-CONVENTIONS.md` 를 따른다.** 프로젝트 한정 보강:
   - Frontmatter 필수 (`name` / `title` / `description ~80자` / `tags` / `aliases` / `created` / `updated`).
   - Wikilink 는 leading-slash + `docs/` (vault root) 기준 풀패스: `[[/wiki/...]]`. `/docs/` prefix 는 붙이지 않는다.
   - 작업/보고/로그는 `task/`, `reports/`, `logs/` 폴더로 분리.
   - LLM 초기 프롬프트(`AGENTS.md` 정본, `CLAUDE.md`/`GEMINI.md` 는 `@AGENTS.md` import) 와 MOC(`docs/index.md`) 는 분리.

   ## 바로가기
   - [[/todos]] — 프로젝트 장기·크로스컷팅 할일 backlog (그날치는 task/)
   - [[/ideas]] — 미분류 아이디어·키워드 캡처

   ## Wiki
   - (새 위키 노트가 생기면 한 줄씩 추가)

   ## 작업 산출물 — task / reports (시간순, 영속 아님)

   `task/` 와 `reports/` 는 시간순으로 쌓이는 저durability 산출물이라 (홈 위키의 journal 과 동일) 메인 MOC 에 개별 날짜 파일을 나열하지 않는다 — 폴더/`grep` 으로 탐색.

   - `docs/task/` — 날짜별 작업 명세 `YYYYMMDD.md`. 생성은 `/llm-wiki:project-new-task`.
   - `docs/reports/` — 일일/주간 보고. `/llm-wiki:project-daily-report`.
   ```

   `<project-name>` 은 git 루트 디렉토리명, `<today>` 는 `currentDate` 로 치환.

5. **`docs/todos.md` / `docs/ideas.md`**: 각각 존재하지 않을 때만 Write (홈의 root 단일파일 `todos-*`/`journal/inbox` 의 프로젝트판 — 단일 맥락이라 work/home 분리 없음).

   - `docs/todos.md`: frontmatter (`name: todos`, `tags: [todos]`, `created`/`updated` = `<today>`) + 본문에 `## Active` / `## Backlog` / `## Done (최근만)` 빈 섹션. "그날치는 task/, 진행 경과는 reports/, 여기엔 장기 항목만" 안내 한 줄.
   - `docs/ideas.md`: frontmatter (`name: ideas`, `tags: [ideas]`, `aliases: [inbox]`, `created`/`updated` = `<today>`) + 본문에 "분류 전 아이디어·키워드 캡처. 무르익으면 wiki/ 또는 task/ 로 승격, 원본은 비운다" + `## 메모` 빈 섹션. (긴 원본 dump 는 `raw/`, 짧은 착상은 여기.)

6. **`.gitignore` 패치**: 파일이 있으면 `docs/logs/` 라인이 이미 있는지 확인. 없으면 끝에 추가. 파일 자체가 없으면 새로 만들고 `docs/logs/` 한 줄만 작성.

7. **AGENTS.md 부트스트랩 (정본 + 얇은 import) 안내**: 홈 위키 §12 의 agent-agnostic 패턴을 프로젝트에도 적용한다 — **`AGENTS.md` 가 정본**, `CLAUDE.md`/`GEMINI.md` 는 `@AGENTS.md` 한 줄 import. 자동 로드되는 부트스트랩은 작게 유지 (라우팅 요약 + MOC·컨벤션 포인터; 본 스펙은 "필요할 때만 read").

   - **신규 프로젝트** (AGENTS.md/CLAUDE.md 모두 없음): `AGENTS.md` 를 아래 스니펫으로 Write 하고, `CLAUDE.md`·`GEMINI.md` 는 각각 `@AGENTS.md` 한 줄 import 파일로 Write.

     ~~~markdown
     # <project-name> — Claude Code 부트스트랩

     이 프로젝트의 규칙은 `AGENTS.md` 가 정본이다. 아래 import 를 따른다.

     @AGENTS.md
     ~~~

   - **기존 CLAUDE.md 가 이미 있는 프로젝트**: 덮어쓰지 말고, "CLAUDE.md 내용을 `AGENTS.md` 로 옮기고 CLAUDE.md/GEMINI.md 는 `@AGENTS.md` import 로 바꾸면 Gemini CLI/Codex 등에서도 동일 부트스트랩이 로드된다" 고 **안내만** 한다 (사용자가 직접 이관).
   - **`@` carve-out**: AGENTS.md 본문의 "문서 링크 컨벤션" 에 "`@` prefix 금지, 단 루트 `CLAUDE.md`/`GEMINI.md` → `AGENTS.md` 부트스트랩 import 한 줄만 예외" 를 명시. (git repo 라 symlink `ln -sf AGENTS.md CLAUDE.md` 도 가능 — `@` 충돌을 피하고 싶으면 symlink, 동기화 호환·명시성을 원하면 import.)

   AGENTS.md 본문에 넣을 워크플로우 스니펫 (사용자가 위치 조정):

   ~~~markdown
   ## 작업 워크플로우

   - 오늘의 작업 명세: `docs/task/YYYYMMDD.md` (없으면 `/llm-wiki:project-new-task` 로 템플릿 생성)
   - 항목 마커: `[ ]` ready, `[WIP]` 사용자 작성 중 — **실행 금지, 사용자가 `[ ]` 로 바꿀 때까지 기다림**, `[DONE]` 완료
   - 중간 산출물은 `docs/logs/<date>/<slug>/` 에 자유 형식으로 기록 (gitignored). 사용자가 진행 경과를 검토하는 용도
   - 완료 시 spec 항목을 `[DONE] → docs/logs/<date>/<slug>/` 형태로 갱신 (로그 경로 첨부)
   - 자동 commit / push 절대 금지 — 모든 커밋은 사용자가 명시적으로 호출

   ### Frontmatter 규약 (task / reports / logs)

   - `docs/wiki/` 외에도 일자별 산출물은 모두 frontmatter 를 갖는다.
   - task: `name: YYYYMMDD`, `tags: [task]`.
   - reports: `name` = 파일명, `tags: [report, daily]` 또는 `[report, weekly]`.
   - logs: gitignored 라 필수는 아니지만, wiki 승격 가능성이 있으면 작성 시점에 부여 권장.
   - 공통 필드: `name` / `title` / `description (~80자)` / `tags` / `aliases` / `created` / `updated`.

   ## 디렉토리 (LLM 워크플로우)

   - `docs/index.md` 프로젝트 MOC — vault 라우팅 진입점
   - `docs/wiki/` LLM 위키 — 영속 레퍼런스 (frontmatter 필수)
   - `docs/raw/` 위키 source raw 데이터
   - `docs/task/` 날짜별 작업 명세 `YYYYMMDD.md` (git tracked)
   - `docs/logs/` 세션 중간 산출물 (gitignored)
   - `docs/reports/` 일일/주간 보고 (git tracked)

   ## 문서 링크 컨벤션

   - 위키 컨벤션 본체는 `~/wiki/WIKI-CONVENTIONS.md` 를 따른다 (홈 + 프로젝트 공통).
   - 부트스트랩(`AGENTS.md` 및 이를 import 하는 `CLAUDE.md`/`GEMINI.md`) → 다른 문서: 마크다운 링크 `[t](path)` (GitHub 웹뷰 호환).
   - docs/ ↔ docs/: leading-slash 풀패스 위키링크 `[[/path/name]]`. 확장자 생략. **vault root = `<repo>/docs/`** — `docs/index.md` 의 존재로 wiki.vim 의 `wiki_root_local` 이 `docs/` 를 자동탐지. Obsidian 도 `docs/` 를 vault 로 열면 동일. 따라서 wikilink 에 `/docs/` prefix 없음. vault 밖 (`<repo>/AGENTS.md` 등) 참조는 마크다운 링크.
   - `@` prefix 절대 금지 (docs/ 내부 링크 + 부트스트랩 본문). **단 하나의 예외**: 루트 `CLAUDE.md`/`GEMINI.md` → `AGENTS.md` 부트스트랩 import 한 줄(`@AGENTS.md`)만 허용.

   ## 위키 트리거 규칙

   다음 상황에서는 작업 전 해당 wiki 를 먼저 읽는다.

   | 상황 | 위키 |
   |------|------|
   | <상황 1> | [docs/wiki/<file>.md](docs/wiki/<file>.md) |

   ## 슬래시 커맨드

   | 커맨드 | 설명 |
   |--------|------|
   | `/llm-wiki:project-new-task` | 오늘 날짜의 작업 명세 파일 생성 (미완료 carry-over) |
   | `/llm-wiki:project-daily-report` | 오늘의 task spec + git log + logs 종합 보고 |
   | `/llm-wiki:project-weekly-report` | 이번 ISO 주의 일일 보고들을 통합한 주간 보고 |
   | `/llm-wiki:project-wiki-sync` | 최근 작업 결과 → `docs/wiki/` 업데이트 diff 제안 |
   | `/llm-wiki:moc-sync` | docs/index.md (또는 ~/wiki/index.md) frontmatter 기반 재생성 |
   | `/llm-wiki:digest` | raw/ 의 원본 → 정제 노트 + index 한 줄 추가 |
   ~~~

8. **보고**: 다음을 사용자에게 출력.
   - 생성한 디렉토리 / 파일 목록 (이미 있어서 건너뛴 것도 명시)
   - AGENTS.md 부트스트랩: 신규면 생성한 `AGENTS.md`/`CLAUDE.md`/`GEMINI.md` 를, 기존 CLAUDE.md 가 있었으면 위 워크플로우 스니펫 + AGENTS.md 이관 안내를 출력
   - 다음 단계: `/llm-wiki:project-new-task` 로 첫 번째 task spec 만들기

## 제약

- 기존 파일 덮어쓰기 금지 (특히 `_TEMPLATE.md`, `docs/index.md`, `docs/todos.md`, `docs/ideas.md`, `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `.gitignore` 의 기존 내용)
- `.gitignore` 는 덧붙이기만; 기존 라인 재정렬·삭제 금지
- 자동 commit 금지 — 사용자가 직접
