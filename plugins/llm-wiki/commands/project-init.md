---
description: 현재 프로젝트에 LLM 위키 워크플로우 골격을 부트스트랩한다 (docs/{wiki,raw,task,logs,reports}/ + index.md + _TEMPLATE.md + CLAUDE.md 스니펫 안내)
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
   - Wikilink 는 leading-slash + 프로젝트 root 기준 풀패스: `[[/docs/wiki/...]]`.
   - 작업/보고/로그는 `task/`, `reports/`, `logs/` 폴더로 분리.
   - LLM 초기 프롬프트(`CLAUDE.md`) 와 MOC(`docs/index.md`) 는 분리.

   ## Wiki
   - (새 위키 노트가 생기면 한 줄씩 추가)

   ## Task
   - (`/llm-wiki:project-new-task` 로 생성)

   ## Reports
   - (`/llm-wiki:project-daily-report` 로 생성)
   ```

   `<project-name>` 은 git 루트 디렉토리명, `<today>` 는 `currentDate` 로 치환.

5. **`.gitignore` 패치**: 파일이 있으면 `docs/logs/` 라인이 이미 있는지 확인. 없으면 끝에 추가. 파일 자체가 없으면 새로 만들고 `docs/logs/` 한 줄만 작성.

6. **CLAUDE.md 안내**: 다음 스니펫을 사용자에게 출력만 한다 (자동 삽입 금지 — 사용자가 위치를 직접 정해서 붙여넣음).

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
   - CLAUDE.md → 다른 문서: 마크다운 링크 `[t](path)` (GitHub 웹뷰 호환).
   - docs/ ↔ docs/: leading-slash 풀패스 위키링크 `[[/docs/path/name]]`. 확장자 생략. vault root = repo root.
   - `@` prefix 절대 금지.

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

7. **보고**: 다음을 사용자에게 출력.
   - 생성한 디렉토리 / 파일 목록 (이미 있어서 건너뛴 것도 명시)
   - 위 CLAUDE.md 스니펫 — 사용자가 CLAUDE.md 의 적절한 위치에 직접 붙여넣어야 함을 안내
   - 다음 단계: `/llm-wiki:project-new-task` 로 첫 번째 task spec 만들기

## 제약

- 기존 파일 덮어쓰기 금지 (특히 `_TEMPLATE.md`, `docs/index.md`, `CLAUDE.md`, `.gitignore` 의 기존 내용)
- `.gitignore` 는 덧붙이기만; 기존 라인 재정렬·삭제 금지
- 자동 commit 금지 — 사용자가 직접
