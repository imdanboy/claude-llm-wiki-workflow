---
description: 최근 작업 결과를 바탕으로 docs/wiki/ 업데이트 diff 를 제안한다 (적용 전 사용자 승인 필요)
argument-hint: "[days=1]"
---

# /llm-wiki:project-wiki-sync

최근 N일 (기본 1일) 의 완료된 작업과 raw 데이터를 바탕으로 `docs/wiki/` 의 업데이트 diff 를 제안한다.

## 입력

- `$ARGUMENTS` 가 숫자면 그 일수만큼 거슬러 올라간다. 비어있으면 1.

## 동작

1. **수집**:
   - `docs/task/*.md` 중 최근 N일치 파일 목록 — `ls docs/task/ | sort | tail` 또는 파일명 날짜 비교
   - 각 파일에서 `[DONE]` 마커가 있는 항목들과 그 본문 (`## <번호>.` 섹션) 을 추출
   - `[DONE] → docs/logs/<date>/<slug>/` 링크가 있으면 해당 로그 디렉토리도 Read
   - `docs/raw/` 가 비어있지 않으면 그 안의 파일들도 함께 Read
2. **분석**:
   - 수집한 내용 중 영속 레퍼런스로 가치 있는 것을 추린다 — 재사용 가능한 결정, 규칙, 데이터 정의, 함정/주의사항
   - 이번 작업에만 의미 있는 일회성 결과는 제외
   - `docs/wiki/` 기존 파일과 매칭 — 어디에 추가/수정할지 매핑. 새 위키 글이 필요하면 신규 파일 제안
3. **제안**:
   - 변경 대상별로 diff 형식으로 출력
   - 각 변경마다 "왜 이 위치에 / 왜 이 내용을" 1-2 줄 근거
   - **새 위키 파일 제안 시**: frontmatter (`name` / `title` / `description ~80자` / `tags` / `aliases` / `created` / `updated`) 포함한 완성된 형태로 제안. wikilink 는 leading-slash (`[[/docs/wiki/...]]`)
   - **기존 파일 수정 시**: 해당 파일의 frontmatter `updated` 필드도 오늘 날짜로 갱신
4. **승인 대기**: 사용자가 명시적으로 "적용" 이라고 답하기 전까지 Edit / Write 실행 금지
5. **적용**: 승인 후 Edit / Write 로 반영. 새 파일이 생긴 경우 `docs/index.md` 의 "Wiki" 섹션에 한 줄 추가 제안. git add / commit 자동 수행 금지 — 사용자가 직접

## 제약

- 위키 글의 톤은 기존 `docs/wiki/*.md` 의 구조 (H2/H3 헤더 + 예제 + 표) 를 따른다
- Frontmatter 컨벤션은 `~/wiki/WIKI-CONVENTIONS.md` §2 를 따른다 (single source-of-truth)
- 짧은 한글 키워드의 부분문자열 매칭 위험 (예: flashtext 류 키워드 매칭기 사용 시) 은 항상 고려
- 자동 commit / push 자동 수행 금지
