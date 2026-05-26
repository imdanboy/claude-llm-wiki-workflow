---
description: vault 의 raw/ 원본 자료를 읽고 정제된 노트 (topics/projects/wiki) 로 정리. frontmatter 갖춰 생성 + index.md 한 줄 추가 제안. vault 자동감지.
argument-hint: "<raw-file> [target-category]"
---

# /llm-wiki:digest

vault 의 `raw/` 디렉토리에 dump 된 원본 자료를 읽고, 영속 레퍼런스로 가치 있는 부분을 추려 카테고리 폴더의 노트로 정제한다. 원본은 보존, 정제본은 frontmatter 갖춤.

## Vault 자동감지

(`/llm-wiki:moc-sync` 와 동일 로직)

1. `git rev-parse --show-toplevel` + `<root>/docs/index.md` → **project mode** (raw = `<root>/docs/raw/`, target 후보 = `<root>/docs/wiki/`).
2. cwd 가 `~/wiki/` 하위 또는 `~/wiki/index.md` 존재 → **home mode** (raw = `~/wiki/raw/`, target 후보 = `~/wiki/topics/` `~/wiki/projects/` `~/wiki/experiments/`).

## 입력

- `$ARGUMENTS` 첫 번째 토큰: raw 파일 이름 (예: `gsheet_20260521.csv`). 경로는 vault 의 raw/ 기준 상대.
- `$ARGUMENTS` 두 번째 토큰 (옵션): target category 폴더명 (`topics`, `projects`, `wiki` 등). 생략 시 LLM 이 내용 분석 후 제안.

## 동작

1. **vault 결정**: 자동감지.
2. **raw 파일 Read**: `<vault>/raw/<file>`. 큰 파일이면 head/sample.
3. **내용 분석**: 어떤 종류의 정보인가 (table, log, doc, paper, etc.). 영속 가치 있는 부분 식별.
4. **target 결정**:
   - 사용자가 명시했으면 그대로.
   - 안 했으면 LLM 이 제안 + 사용자 승인. (예: "이 CSV 는 발음사전 후보 목록 → project mode 라면 `docs/wiki/pron-dic-candidates.md` 신규 또는 기존 위키 확장. 어느 쪽?")
5. **정제 노트 생성 제안**:
   - 새 파일이면: frontmatter (`name` / `title` / `description ~80자` / `tags` / `aliases` / `created` / `updated` 오늘) + 본문 (table/heading/리스트 적절히 구조화) 전체를 보여줌.
   - 기존 파일 확장이면: 추가될 섹션 + frontmatter `updated` 갱신을 diff 로 보여줌.
   - wikilink 는 leading-slash 풀패스. project mode 는 `[[/wiki/...]]` (vault root = `<repo>/docs/`), home mode 는 `[[/topics/...]]`.
6. **승인 대기**: 사용자 "적용" 전까지 Write 금지.
7. **적용**: 승인 후 Write/Edit. 새 파일이면 vault 의 `index.md` 의 해당 카테고리에 한 줄 추가도 함께 제안 (별도 승인).
8. **원본 보존**: raw/ 의 원본 파일은 절대 삭제하지 않음. 정제본 옆 어딘가에 "출처: raw/<file>" 표기.

## 권장 raw 파일 명명

`<source>-<topic>-<YYYYMMDD>.<ext>` 형태로 출처와 시점 추적 — 예: `gsheet-pron-dic-20260521.csv`, `paper-naturalspeech3-20260518.pdf`.

## 제약

- raw 원본 삭제 절대 금지 (근거/이력 보존).
- 추측성 내용 추가 금지 — raw 에 없는 내용은 만들지 않음.
- 정제본 frontmatter 의 `description` 은 raw 내용 요약, 사용자가 필요시 다듬도록 안내.
- 자동 commit / push 금지.
