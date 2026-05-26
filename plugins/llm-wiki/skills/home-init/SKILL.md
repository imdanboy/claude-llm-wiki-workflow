---
description: ~/wiki/ 에 홈 위키 골격을 부트스트랩한다 (topics/projects/journal/experiments/raw + _templates + index/todos/links). WIKI-CONVENTIONS §1 구조 따름.
---

# /llm-wiki:home-init

홈 위키 (`~/wiki/`) 의 빈 골격을 부트스트랩한다. `~/wiki/WIKI-CONVENTIONS.md` §1 구조를 자동 생성 (해당 컨벤션의 셸 fallback 절을 자동화한 형태).
멱등 — 이미 존재하는 파일/디렉토리는 덮어쓰지 않는다.

## 동작

1. **사전 점검**: `~/wiki/` 가 존재하는지 확인. 없으면 사용자에게 "`~/wiki/` 를 생성할까요?" 물어본 뒤 mkdir.
2. **`~/wiki/WIKI-CONVENTIONS.md` Read**: 컨벤션이 있어야 함. 없으면 "WIKI-CONVENTIONS.md 가 먼저 있어야 한다 — 깃에서 클론하거나 새 머신이면 본 파일을 복사하라" 안내 후 종료.
3. **디렉토리 생성** (mkdir -p, 멱등):
   - `~/wiki/_templates/`
   - `~/wiki/topics/`
   - `~/wiki/projects/`
   - `~/wiki/journal/`
   - `~/wiki/experiments/`
   - `~/wiki/raw/`
4. **secrets 디렉토리**: `~/.wiki-secrets/` 가 없으면 `mkdir -p ~/.wiki-secrets && chmod 700 ~/.wiki-secrets`.
5. **root 파일 생성** (각각 존재하지 않을 때만, WIKI-CONVENTIONS §2 frontmatter 갖춰):

   - `~/wiki/index.md` — MOC. `tags: [moc, index]`, `aliases: [README, home]`. 본문에 §5 사용 규칙 + 빈 카테고리 섹션 (Topics/Projects/Experiments/Journal/Raw).
   - `~/wiki/todos.md` — 장기/크로스컷팅 할일. `tags: [todos]`. 본문에 §6 의 `## Active` / `## Backlog` / `## Done (최근만)` 빈 섹션.
   - `~/wiki/links.md` — 사용 맥락별 외부 링크. `tags: [links]`. 본문에 §7 의 빈 섹션 + 예시 한 줄.
   - `~/wiki/journal/inbox.md` — 분류 전 잡메모. `tags: [journal, inbox]`.
   - `~/wiki/raw/README.md` — raw/ 사용 규칙. `tags: [raw, meta]`. 본문에 §8 의 흐름 요약.

6. **템플릿 파일** (없을 때만):

   - `~/wiki/_templates/note.md` — 일반 노트 frontmatter + 빈 본문.

     ```markdown
     ---
     name: <slug>
     title: <Human Title>
     description: <~80자 한 줄>
     tags: []
     aliases: []
     created: YYYY-MM-DD
     updated: YYYY-MM-DD
     ---

     # <Human Title>
     ```

   - `~/wiki/_templates/journal.md` — `/llm-wiki:home-journal-today` 가 사용. 일자별 메모 frontmatter + Todos/Notes/Links 빈 섹션.

7. **보고**:
   - 생성한 디렉토리 / 파일 목록 (이미 있어서 건너뛴 것도 명시).
   - 다음 단계 안내: `/llm-wiki:home-journal-today` 로 오늘 메모 시작, raw/ 에 자료 dump 후 `/llm-wiki:digest` 로 정제, 새 노트 추가 후 `/llm-wiki:moc-sync` 로 index 갱신.

## 제약

- 기존 파일 덮어쓰기 절대 금지.
- WIKI-CONVENTIONS.md 가 없으면 진행하지 않음 — 컨벤션이 single source-of-truth.
- 비밀정보를 `~/wiki/` 안에 만들지 않음 (§9).
- 자동 commit 금지 — 사용자가 직접.
