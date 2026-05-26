---
description: 오늘의 작업 명세 진행 상황 + git log + 로그를 종합해 docs/reports/YYYY-MM-DD.md 작성
---

# /llm-wiki:project-daily-report

오늘 한 일을 정리해 `docs/reports/YYYY-MM-DD.md` 를 생성한다.

## 동작

1. **날짜**: 환경 컨텍스트의 `currentDate` (예: 2026-05-21). 파일명용 `YYYYMMDD` (예: 20260521) 도 준비.
2. **수집**:
   - `docs/task/YYYYMMDD.md` Read — 항목별 마커 (`[ ]` / `[WIP]` / `[DONE]`) 카운트와 항목 제목
   - `git log --since=midnight --oneline --no-merges` — 오늘 커밋 목록
   - `docs/logs/YYYYMMDD/` 가 존재하면 하위 디렉토리/파일 목록 (내용 전체 아닌 인덱스만)
3. **작성**: `docs/reports/YYYY-MM-DD.md` 에 다음 구조로 Write:

   ```markdown
   ---
   name: YYYY-MM-DD
   title: YYYY-MM-DD 일일 보고
   description: <요약 섹션의 핵심을 한 줄 ~80자로 압축>
   tags: [report, daily]
   aliases: []
   created: YYYY-MM-DD
   updated: YYYY-MM-DD
   ---

   # YYYY-MM-DD 일일 보고

   ## 요약

   팀 보고 컨벤션 — 2-level 들여쓰기 bullet, 최대로 압축. 부서/팀 보고 시 그대로 복사해 쓸 수 있는 톤.
   - 상위 항목: 큰 테마/이니셔티브
     - 하위 항목: 그날 다룬 구체 작업
   - 상위가 하나뿐이거나 하위가 없어도 OK; 무리하게 채우지 말 것
   - 한 줄 ~30자 이내가 이상적

   ## 오늘 한 일
   - [DONE] 항목들의 제목 (task spec 기준)
   - 오늘 커밋 요약 (1-2줄)

   ## 미완료
   - [ ] / [WIP] 항목들의 제목 + 현재 상태

   ## 내일 이어갈 것
   - 미완료 중 우선순위가 있는 항목 또는 자연스러운 후속 작업
   - 작업 명세에 명시되어 있지 않은 추측성 내용은 적지 않음

   ## 참조
   - task spec: [[/task/YYYYMMDD]]
   - logs: docs/logs/YYYYMMDD/ (있으면)
   - 커밋: <hash> <message> 리스트
   ```

   **Frontmatter 규약**:
   - `name` / `created` / `updated` 는 오늘 날짜.
   - `description` 은 "요약" 섹션을 한 줄 ~80자로 압축. 본문 작성 후 채움.
   - `tags: [report, daily]` 고정.

4. **존재 시**: 같은 날짜 보고가 이미 있으면 사용자에게 덮어쓸지 / append 할지 / 종료할지 물음. 기본은 덮어쓰지 않음.

## 제약

- 자동 commit / push 절대 금지 — 보고 파일 생성만
- "내일 이어갈 것" 은 task spec 에서 추론 가능한 것만. 추측성 작업 만들지 않음
- 보고 톤: 사실 위주, 짧고 평이하게
- 참조 링크는 leading-slash 풀패스 wikilink (`[[/wiki/...]]`, `[[/task/...]]`, `[[/logs/...]]` — vault root = `<repo>/docs/` 기준) 사용
