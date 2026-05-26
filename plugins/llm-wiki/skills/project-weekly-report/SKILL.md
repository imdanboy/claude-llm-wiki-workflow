---
description: 이번 ISO 주의 일일 보고들을 통합해 docs/reports/weekly-YYYY-Www.md 작성
argument-hint: "[YYYY-Www, 기본=이번주]"
---

# /llm-wiki:project-weekly-report

이번 ISO 주 (월–일) 의 일일 보고들을 통합해 주간 보고를 작성한다.

## 입력

- `$ARGUMENTS` 가 `YYYY-Www` 형식이면 그 주. 비어있으면 오늘이 속한 주.

## 동작

1. **주차 결정**:
   - 오늘 날짜 (`currentDate`) 의 ISO 주차를 계산 — `date +%G-W%V` (예: 2026-W21)
   - 주의 시작 = 월요일, 끝 = 일요일
2. **수집**:
   - 그 주의 7일 각각에 대해 `docs/reports/YYYY-MM-DD.md` 가 있는지 확인
   - 있는 파일들을 Read — "오늘 한 일", "미완료", "내일 이어갈 것" 섹션 파싱
3. **작성**: `docs/reports/weekly-YYYY-Www.md` 를 다음 구조로 Write:

   ```markdown
   ---
   name: weekly-YYYY-Www
   title: YYYY-Www 주간 보고
   description: <이번 주 한 일 섹션의 핵심을 한 줄 ~80자로 압축>
   tags: [report, weekly]
   aliases: []
   created: YYYY-MM-DD
   updated: YYYY-MM-DD
   ---

   # YYYY-Www 주간 보고 (YYYY-MM-DD ~ YYYY-MM-DD)

   ## 이번 주 한 일
   - 일별 한 일을 통합. 중복 제거. 카테고리로 묶을 수 있으면 묶음 (예: 모듈명 / 기능 영역 / 문서)
   - 굵직한 커밋이 있으면 함께 표기

   ## 미완료 carry-over
   - 마지막 일일 보고의 "미완료" / "내일 이어갈 것" 항목들

   ## 일별 인덱스
   - YYYY-MM-DD (월): <한 줄 요약>
   - YYYY-MM-DD (화): <한 줄 요약>
   - ... 보고가 없는 날은 "(보고 없음)" 표기
   ```

   **Frontmatter 규약**:
   - `name` 은 파일명과 동일 (`weekly-YYYY-Www`).
   - `created` 는 주간 보고 작성일 (보통 주 마지막 날), `updated` 도 동일 초기값.
   - `tags: [report, weekly]` 고정.

4. **존재 시**: 같은 주차 보고가 이미 있으면 사용자에게 덮어쓸지 물음. 기본은 덮어쓰지 않음.
5. **부족 시**: 그 주의 일일 보고가 하나도 없으면 작성하지 않고 "일일 보고가 없습니다 — /llm-wiki:project-daily-report 를 먼저 실행하세요" 안내.

## 제약

- 자동 commit / push 절대 금지
- 일일 보고에 없는 내용은 만들지 않음 (요약만 함)
