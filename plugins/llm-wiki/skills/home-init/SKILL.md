---
description: ~/wiki/ 에 홈 위키 골격을 부트스트랩한다 (topics/projects/journal/experiments/raw + _templates + index/todos-work/todos-home/refs-work/refs-home/journal-index + AGENTS.md 부트스트랩). WIKI-CONVENTIONS §1 구조 따름.
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
4. **secrets 디렉토리**: vault 안의 `~/wiki/secrets/` 가 없으면 `mkdir -p ~/wiki/secrets && chmod 700 ~/wiki/secrets`. (§1·§9 — 평문은 vault 에 두지 않고 age 암호문 `*.age` 만. 암호문째 동기화되므로 vault 내부에 둔다. 설계는 `topics/secret-notes`.)
5. **root 파일 생성** (각각 존재하지 않을 때만, WIKI-CONVENTIONS §2 frontmatter 갖춰):

   - `~/wiki/index.md` — MOC. `tags: [moc, index]`, `aliases: [README, home]`. 본문에 §5 사용 규칙 + `## 바로가기` (journal/inbox·todos-work·todos-home·journal/index·refs-work·refs-home) + 빈 카테고리 섹션 (Topics/Projects/Experiments/Journal/Raw). **Journal 섹션은 pointer-only** — `[[/journal/index]]` + `[[/journal/inbox]]` 두 줄만 (§5.1, 일자별 파일은 나열 X).
   - `~/wiki/todos-work.md` / `~/wiki/todos-home.md` — 장기/크로스컷팅 할일, 업무/개인 **맥락 분리** (§6). 각 `tags: [todos]`. 본문에 `## Active` / `## Backlog` / `## Done (최근만)` 빈 섹션.
   - `~/wiki/refs-work.md` / `~/wiki/refs-home.md` — 사용 맥락별 링크·참고 정보, 업무/개인 분리 (§7). 각 `tags: [refs]`. refs-work 은 사용 맥락별 섹션 + when-to-use 한 줄 예시, refs-home 은 개인 링크·연락처 (진짜 비밀은 `secrets/*.age`).
   - `~/wiki/journal/index.md` — Journal 전용 sub-MOC (§5.1). `tags: [moc, journal]`, `aliases: [journal]`. 최근 ≈1년치 일자 노트만 wikilink + description, 그 이전은 `archive-YYYY.md` 로 묶어 한 줄 참조. 빈 골격으로 생성.
   - `~/wiki/journal/inbox.md` — 분류 전 잡메모·아이디어. `tags: [journal, inbox]`.
   - `~/wiki/raw/README.md` — raw/ 사용 규칙. `tags: [raw, meta]`. 본문에 §8 의 흐름 요약.

6. **AGENTS.md 부트스트랩** (§12, agent-agnostic, 각각 없을 때만):

   - `~/wiki/AGENTS.md` — 세션 부트스트랩 **정본** (~30줄). 진입점=`index.md` MOC, 노트 규칙 요약(frontmatter·kebab-case·leading-slash wikilink), 더 깊은 규칙은 `WIKI-CONVENTIONS.md` "필요할 때만 read", `/llm-wiki:*` 자동화, secrets `*.age` 금지 read. 본 스펙(13KB)을 직접 symlink/복붙하지 말 것 — L0 부트스트랩은 작게.
   - `~/wiki/CLAUDE.md`, `~/wiki/GEMINI.md` — 각각 `@AGENTS.md` 한 줄 import 파일. (Obsidian Sync 가 symlink 를 전송 안 하므로 symlink 대신 import; §12.)

7. **템플릿 파일** (없을 때만):

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

8. **보고**:
   - 생성한 디렉토리 / 파일 목록 (이미 있어서 건너뛴 것도 명시).
   - 다음 단계 안내: `/llm-wiki:home-journal-today` 로 오늘 메모 시작, raw/ 에 자료 dump 후 `/llm-wiki:digest` 로 정제, 새 노트 추가 후 `/llm-wiki:moc-sync` 로 index 갱신.

## 제약

- 기존 파일 덮어쓰기 절대 금지.
- WIKI-CONVENTIONS.md 가 없으면 진행하지 않음 — 컨벤션이 single source-of-truth.
- **평문** 비밀정보를 `~/wiki/` 안에 만들지 않음 (§9) — secrets 는 `secrets/*.age` 암호문만, 평문은 nvim 버퍼에서만.
- 자동 commit 금지 — 사용자가 직접.
