---
description: 홈 위키 (~/wiki/journal/) 에 오늘 날짜의 일자별 메모 파일을 생성. 이미 있으면 안내만.
---

# /llm-wiki:home-journal-today

`~/wiki/journal/YYYY-MM-DD.md` 를 frontmatter 갖춰 생성한다. 일자별 잡메모용 — todos/aha/링크/짧은 메모 등. 작업 명세가 아닌 일기 성격이므로 carry-over 없음.

## 동작

1. **날짜 확인**: 환경 컨텍스트의 `currentDate` (예: 2026-05-24).
2. **vault 위치**: `~/wiki/` 고정. 없으면 "`/llm-wiki:home-init` 을 먼저 실행하라" 안내.
3. **`~/wiki/_templates/journal.md` Read**: 템플릿 있으면 그대로 사용. 없으면 아래 기본 템플릿 사용.
4. **오늘 파일 존재 확인**: `~/wiki/journal/<YYYY-MM-DD>.md` 가 있으면 "이미 있습니다 — 경로" 출력 후 종료. 덮어쓰기 금지.
5. **파일 작성**:

   ```markdown
   ---
   name: YYYY-MM-DD
   title: YYYY-MM-DD journal
   description: <오늘 메모의 한 줄 요약 — 작성 후 갱신>
   tags: [journal]
   aliases: []
   created: YYYY-MM-DD
   updated: YYYY-MM-DD
   ---

   # YYYY-MM-DD

   ## Todos
   - [ ]

   ## Notes

   ## Links
   ```

   `YYYY-MM-DD` 자리는 모두 오늘 날짜로 치환.

6. **보고**: 생성된 경로 출력. "메모 작성 후 frontmatter `description` 을 한 줄 요약으로 갱신하라" 안내.

## 제약

- 기존 파일 덮어쓰기 금지.
- carry-over 없음 — 홈 journal 은 일기 성격, 항상 빈 템플릿에서 시작.
- 자동 commit 금지.
- `description` 은 placeholder 유지, 사용자가 그날 메모 정리 후 채움.
