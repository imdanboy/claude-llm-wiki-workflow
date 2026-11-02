---
description: vault 의 index.md (MOC) 를 각 노트의 frontmatter description 만 읽어 카테고리별 인덱스로 재생성. 프로젝트/홈 vault 자동감지.
---

# /llm-wiki:moc-sync

vault 의 `index.md` 를 frontmatter 기반으로 재생성한다. 각 노트의 description 한 줄만 모아 카테고리별로 갱신하므로, 본문은 읽지 않아 토큰 효율적.

## Vault 자동감지

1. `git rev-parse --show-toplevel` 가 성공하고 `<root>/docs/index.md` 가 존재 → **project mode** (vault root = `<git root>/docs`, MOC = `<root>/docs/index.md`).
2. cwd 가 `~/wiki/` 하위거나 `~/wiki/index.md` 가 존재 → **home mode** (vault root = `~/wiki`, MOC = `~/wiki/index.md`).
3. 둘 다 매칭 안 되거나 두 조건이 모두 매칭되면 사용자에게 명시 요청 (`--vault project|home`).

## 동작

1. **vault 결정**: 위 자동감지. mode 와 vault root 를 사용자에게 알림.
2. **카테고리 폴더 스캔**: vault root 하위 폴더 중 frontmatter 갖춘 `.md` 파일을 가진 것들.
   - **home mode**: `topics/`, `projects/`, `experiments/`, `raw/`. root 의 `todos-work.md`/`todos-home.md`, `refs-work.md`/`refs-home.md`, `WIKI-CONVENTIONS.md` 등 단일 파일도 별도 처리. `journal/` 은 시간순 폭증 카테고리라 **pointer-only** (메인 MOC 엔 `[[/journal/index]]` + `[[/journal/inbox]]` 두 줄만; 일자별 파일 나열 X — WIKI-CONVENTIONS §5.1).
   - **project mode** (vault root = `<git root>/docs`): per-file 나열은 `wiki/`, `raw/` 만. root 단일 파일 `todos.md`, `ideas.md` 는 별도 처리 (`## 바로가기` 섹션에 노출). `task/`, `reports/` 는 **pointer-only** — 시간순으로 쌓이는 저durability 산출물(홈 `journal/` 과 동일, WIKI-CONVENTIONS §5.1·§11)이라 개별 날짜 파일을 나열하지 않고 작업 산출물 섹션의 폴더 진입점 줄만 유지·보존한다. `logs/` 는 gitignored 라 제외.
3. **frontmatter 추출**: 각 `.md` 파일의 frontmatter 만 Read (본문 미사용). `name` 과 `description` 추출. frontmatter 가 없는 파일은 경고 목록에 모아 마지막에 보고.
4. **현재 index.md Read**: 기존 구조 (사용 규칙, Meta, 카테고리 헤더, etc.) 를 보존. 카테고리별 bullet 리스트만 갱신 대상.
   - **카테고리 안의 하위 헤더(`###` 이하)는 사용자가 만든 논리 구분이다 — 그대로 보존한다.** 헤더 문구·순서와
     각 하위 그룹 안의 항목 순서를 유지하고, 기존 항목은 원래 있던 하위 그룹에 남긴다 (description 만 갱신).
   - **새 항목**은 하위 구분이 있는 카테고리라면 어느 하위 그룹에 넣을지 frontmatter(`tags`·`description`)로
     추정해 diff 에 제안하고, 판단이 애매하면 사용자에게 묻는다. 하위 그룹 끝에 추가한다.
   - **삭제 항목**은 해당 하위 그룹에서만 뺀다. 그 결과 비게 된 하위 헤더는 지우지 말고 diff 에 표시해 사용자가 정한다.
5. **diff 제안**:
   - 카테고리별로 현재 vs 새 항목 비교.
   - 추가 / 삭제 / description 변경된 항목을 표시.
   - 변경이 없으면 "MOC 가 이미 최신입니다" 출력 후 종료.
6. **승인 대기**: 사용자 "적용" 전까지 Write 금지.
7. **적용**: 승인 후 카테고리 bullet 리스트만 갱신 (다른 섹션 손대지 않음). `updated` frontmatter 도 오늘 날짜로 갱신.

## 출력 포맷 (각 bullet)

```
- [[/path/to/note]] — <description from frontmatter>
```

home mode 예: `- [[/topics/tts]] — TTS 모델·G2P·MFA·neural audio codec·LLM TTS·데이터셋 정리.`
project mode 예: `- [[/wiki/normalization-rules]] — N2gkPlus.__call__ 의 11단계 파이프라인...` (vault root = `<repo>/docs/` 라 wikilink 에 `/docs/` prefix 없음)

## 경고 보고

- **frontmatter 누락 파일**: 파일 경로 목록. 사용자가 frontmatter 추가하길 권장.
- **description 비어있음 또는 placeholder (`<...>`)**: 사용자가 채우길 권장.
- **wikilink 깨짐 의심**: index.md 의 항목 중 실제 파일이 없는 것.

## 제약

- 본문 절대 안 읽음 — frontmatter 만으로 동작 (토큰 절약 핵심).
- index.md 의 카테고리 bullet 리스트 외 다른 섹션 손대지 않음.
- 카테고리 내 `###` 하위 헤더를 평탄화하거나 재배열·이름변경하지 않음 (단순 정렬로 재생성 금지).
- 자동 commit / push 금지.
- 사용자 승인 없이 Write 금지.
