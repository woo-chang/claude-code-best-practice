---
description: 최신 Claude Code 기능과 개념으로 README CONCEPTS 섹션을 업데이트합니다
argument-hint: [number of changelog versions to check, default 10]
---

# 워크플로우 체인지로그 — README 개념

당신은 claude-code-best-practice 프로젝트의 코디네이터입니다. 두 개의 리서치 에이전트를 병렬로 실행하고, 결과를 기다리고, 결과를 합쳐서 **README CONCEPTS 섹션** (`README.md`)의 드리프트에 대한 통합 보고서를 제시하는 것이 당신의 임무입니다.

**확인할 버전:** `$ARGUMENTS` (비어 있거나 숫자가 아닌 경우 기본값: 10)

이것은 **읽기 후 보고** 워크플로우입니다. 에이전트를 실행하고, 결과를 합쳐서 보고서를 작성하세요. 사용자가 승인한 경우에만 조치를 취하세요.

---

## 0단계: 두 에이전트를 병렬로 실행

**즉시** Task 도구를 사용하여 **같은 메시지**에서 두 에이전트를 실행 (병렬 실행)하세요:

### 에이전트 1: workflow-concepts-agent

`subagent_type: "workflow-concepts-agent"`를 사용하여 실행하세요. 다음 프롬프트를 제공하세요:

> README CONCEPTS 섹션 드리프트에 대해 claude-code-best-practice 프로젝트를 조사하세요. 마지막 $ARGUMENTS 버전 (기본값: 10)을 확인하세요.
>
> 다음 3개의 외부 소스를 가져오세요:
> 1. Claude Code 문서 인덱스: https://code.claude.com/docs/en
> 2. Claude Code 체인지로그: https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md
> 3. Claude Code 기능 개요: https://code.claude.com/docs/en/overview
>
> 그런 다음 로컬 README.md (특히 CONCEPTS 테이블), CLAUDE.md, `reports/claude-global-vs-project-settings.md`를 읽으세요. 공식 문서가 Claude Code 개념/기능으로 나열하는 것과 README CONCEPTS 테이블이 문서화하는 것의 차이를 분석하세요. 누락된 개념, 변경된 개념, 더 이상 사용되지 않는 개념, URL 정확도, 설명 정확도, 배지 정확도를 다루는 구조화된 결과 보고서를 반환하세요.

### 에이전트 2: claude-code-guide

`subagent_type: "claude-code-guide"`를 사용하여 실행하세요. 다음 프롬프트를 제공하세요:

> 최신 Claude Code 기능과 개념을 조사하세요. 문서화되어야 하는 모든 Claude Code 개념/기능의 전체 목록을 찾아야 합니다. 각각에 대해 다음을 제공하세요:
> 1. 공식 기능 이름
> 2. 공식 문서 URL
> 3. 파일 시스템 위치 (예: `.claude/commands/`, `~/.claude/teams/`)
> 4. 간략한 설명 (한 줄)
> 5. 도입 시기 (알려진 경우 버전/날짜)
>
> 특히 다음과 같이 누락될 수 있는 개념을 확인하세요:
> - **Worktrees** — 병렬 개발을 위한 git 워트리 격리
> - **Agent Teams** — 다중 에이전트 조정
> - **Tasks** — 세션 간 지속적인 작업 목록
> - **Auto Memory** — Claude의 자기 작성 프로젝트 학습
> - **Keybindings** — 사용자 정의 키보드 단축키
> - **Remote Connections** — SSH, Docker, 클라우드 개발
> - **IDE Integration** — VS Code, JetBrains 확장
> - **Model Configuration** — 모델 선택 및 라우팅
> - **GitHub Integration** — PR 검토, 이슈 분류
> - 최근 Claude Code 버전의 다른 개념
>
> 철저하게 조사하세요 — 웹을 검색하고, 문서를 가져오고, 찾은 모든 것에 대한 구체적인 버전 번호와 세부 정보를 제공하세요.

두 에이전트는 독립적으로 실행되어 결과를 반환합니다.

---

## 0.5단계: 검증 체크리스트 읽기

**에이전트가 실행되는 동안**, `changelog/best-practice/concepts/verification-checklist.md`가 존재하면 읽으세요. 이 파일에는 누적된 검증 규칙이 포함되어 있습니다. 존재하지 않으면 이 단계를 건너뛰세요 — 2단계에서 생성될 것입니다.

---

## 1단계: 이전 체인지로그 항목 읽기

**결과를 합치기 전에**, `changelog/best-practice/concepts/changelog.md` 파일이 존재하면 읽어 이전 체인지로그 항목을 가져오세요. 각 항목은 `---`로 구분됩니다. 이전 항목에서 우선순위 조치를 파싱하여 현재 결과와 비교하세요. 이를 통해 다음을 파악할 수 있습니다:
- **반복 항목** — 이전에 나타났고 여전히 미해결인 문제
- **새로 해결된 항목** — 이전 실행의 문제가 이제 수정된 것
- **새 항목** — 이번 실행에서 처음 나타나는 문제

파일이 아직 없으면 모든 항목은 `NEW`입니다.

---

## 2단계: 결과 합치기 및 보고서 생성

**두 에이전트가 완료될 때까지 기다리세요.** 다음이 준비되면:
- **workflow-concepts-agent 결과** — 로컬 파일 읽기, 외부 문서 가져오기, 드리프트 감지가 포함된 상세 분석
- **claude-code-guide 결과** — 최신 Claude Code 기능 및 개념에 대한 독립적인 조사

두 가지를 교차 참조하세요. 전용 에이전트는 CONCEPTS 특화 드리프트 분석을 제공하고, claude-code-guide 에이전트는 누락된 것을 발견할 수 있습니다 (예: 매우 최근 변경사항, 미문서화된 기능, 웹 검색의 컨텍스트). 사용자가 해결해야 할 두 에이전트 간의 모순을 표시하세요.

**검증 체크리스트 실행 (존재하는 경우):** `changelog/best-practice/concepts/verification-checklist.md`의 모든 규칙에 대해 확인을 수행하세요. 보고서에 **검증 로그** 섹션을 포함하세요.

**필요한 경우 체크리스트 업데이트:** 결과가 기존 체크리스트 규칙이 다루지 않는 새로운 유형의 드리프트를 드러내면 `changelog/best-practice/concepts/verification-checklist.md`에 새 규칙을 추가하세요. 파일이 없으면 생성하세요. 규칙에는 카테고리, 확인할 항목, 깊이 레벨, 비교할 소스, 추가 날짜, 출처가 포함되어야 합니다.

현재 결과를 이전 체인지로그 항목 (1단계에서)과 비교하세요. 각 우선순위 조치에 대해 표시하세요:
- `NEW` — 이 문제가 처음 나타나는 경우
- `RECURRING` — 이전 실행에서 나타났고 여전히 미해결인 경우 (처음 나타난 실행 날짜 포함)
- `RESOLVED` — 이전 실행에서 나타났지만 이제 수정된 경우 (해결 날짜 포함)

다음 섹션으로 구조화된 보고서를 작성하세요:

1. **누락된 개념** — 공식 문서에는 있지만 CONCEPTS 테이블에 없는 기능/개념 (포함):
   - 공식 이름과 문서 URL
   - 권장 Location 열 값
   - 권장 Description 열 값
   - 붙여넣기할 준비가 된 정확한 마크다운 테이블 행
   - 도입 버전 (알려진 경우)
2. **변경된 개념** — 이름, URL, 위치, 설명이 변경된 개념
3. **더 이상 사용되지 않는/제거된 개념** — CONCEPTS 테이블에는 있지만 공식 문서에 없는 개념
4. **URL 정확도** — 개념별 URL 검증
5. **설명 정확도** — 개념별 설명/위치 검증
6. **배지 정확도** — 배지 링크 검증 및 누락된 배지 권장사항
7. **claude-code-guide 에이전트 결과** — 전용 에이전트가 캡처하지 못한 에이전트의 고유한 인사이트. 새 정보를 추가하는 결과만 포함하세요. 모순을 표시하세요.

우선순위 **실행 항목** 요약 테이블로 마무리하세요:

```
Priority Actions:
#  | Type                | Action                                     | Status
1  | Missing Concept     | Add <concept> row to CONCEPTS table         | NEW
2  | Changed URL         | Update <concept> docs link                  | NEW
3  | Changed Description | Update <concept> description                | RECURRING (first seen: <date>)
4  | Deprecated Concept  | Remove <concept> row from CONCEPTS table    | NEW
5  | Broken Badge        | Fix badge link for <concept>                | NEW
```

이전 실행에서 더 이상 문제가 아닌 항목을 나열하는 **마지막 실행 이후 해결됨** 섹션도 포함하세요.

---

## 2.5단계: 체인지로그에 요약 추가

**이 단계는 필수입니다 — 사용자에게 보고서를 제시하기 전에 항상 실행하세요.**

기존 `changelog/best-practice/concepts/changelog.md` 파일을 읽은 다음 끝에 새 항목을 **추가** (덮어쓰지 마세요) 하세요. 파일이 없으면 상태 범례 테이블을 포함하여 생성한 후 첫 번째 항목을 추가하세요. 항목 형식은 정확히 다음과 같아야 합니다:

```markdown
---

## [<YYYY-MM-DD HH:MM AM/PM PKT>] Claude Code v<VERSION>

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH/MED/LOW | <type> | <action description> | <status> |
| ... | ... | ... | ... | ... |
```

**상태 형식 — 다음 세 가지 형식 중 하나를 반드시 사용하세요:**
- `COMPLETE (reason)` — 조치가 취해지고 성공적으로 해결된 경우
- `INVALID (reason)` — 결과가 잘못되었거나, 적용되지 않거나, 의도적인 경우
- `ON HOLD (reason)` — 조치 연기, 외부 의존성 또는 사용자 결정 대기 중

`(reason)`은 필수이며 수행된 작업 또는 이유를 간략하게 설명해야 합니다.

**추가 규칙:**
- 항상 추가하세요 — 이전 항목을 덮어쓰거나 교체하지 마세요
- 날짜와 시간은 커맨드가 파키스탄 표준시 (PKT, UTC+5)로 실행될 때이며, `TZ=Asia/Karachi date "+%Y-%m-%d %I:%M %p PKT"`를 실행하여 가져오세요. 버전은 에이전트 결과에서 가져옵니다
- 각 항목은 `---`로 구분됩니다
- **높음, 중간, 낮음 우선순위 항목만 포함** — 없음 우선순위 항목은 생략하세요

---

## 2.6단계: 마지막 업데이트 배지 업데이트

**이 단계는 필수입니다 — 2.5단계 직후, 보고서를 제시하기 전에 항상 실행하세요.**

`README.md` 상단 (3번째 줄)의 "Last Updated" 배지를 업데이트하세요. `TZ=Asia/Karachi date "+%b %d, %Y %-I:%M %p PKT"`를 실행하여 시간을 가져오고, URL 인코딩 (공백을 `%20`, 쉼표를 `%2C`)하여 배지의 날짜 부분을 교체하세요.

**배지 업데이트를 체인지로그나 보고서의 실행 항목으로 기록하지 마세요.**

---

## 2.7단계: 모든 CONCEPTS URL 검증

**이 단계는 필수입니다 — 2.6단계 후, 보고서를 제시하기 전에 항상 실행하세요.**

CONCEPTS 테이블의 각 개념에 대해:

1. **외부 문서 URL** (예: `https://code.claude.com/docs/en/skills`): WebFetch를 사용하여 각 URL을 가져와 유효한 페이지를 반환하는지 확인하세요. 죽은 링크나 이동된 링크를 표시하세요.
2. **로컬 배지 링크** (예: `best-practice/claude-commands.md`): Read 도구를 사용하여 파일이 존재하는지 확인하세요. 끊어진 링크를 표시하세요.
3. **구현 배지 링크** (예: `.claude/commands/`): 경로가 존재하는지 확인하세요.

보고서에 **URL 검증 로그**를 포함하세요:

```
URL Validation Log:
#  | Concept     | URL Type  | URL                                           | Status | Notes
1  | Commands    | External  | https://code.claude.com/docs/en/skills         | OK     |
2  | Commands    | Badge     | best-practice/claude-commands.md               | OK     |
3  | Sub-Agents  | External  | https://code.claude.com/docs/en/sub-agents     | OK     |
...
```

**URL이 끊어진 경우**, 높은 우선순위 실행 항목으로 추가하세요.

---

## 3단계: 조치 제안

보고서를 제시한 후 (체인지로그가 업데이트되었음을 확인한 후), 사용자에게 묻습니다:

1. **모든 조치 실행** — 누락된 개념 추가, 변경된 것 업데이트, 더 이상 사용되지 않는 것 제거
2. **특정 조치 실행** — 사용자가 실행할 번호 선택
3. **보고서만 저장** — 변경사항 없음

실행 시:
- **누락된 개념**: 기존 형식에 따라 `README.md`의 CONCEPTS 테이블에 새 행을 추가하세요:
  ```
  | [**Name**](docs-url) | `location` | Description |
  ```
  해당 파일이 존재하는 경우에만 배지 (모범 사례, 구현됨)를 추가하세요.
- **변경된 개념**: 변경된 특정 열을 업데이트하세요
- **더 이상 사용되지 않는 개념**: 행을 제거하기 전 사용자 확인
- **끊어진 URL**: URL을 현재 유효한 것으로 수정하세요
- **배지 수정**: 배지 링크를 올바른 파일 경로로 업데이트하세요
- 기존 테이블과 일관성 있는 알파벳순 또는 논리적 순서 유지
- 모든 조치 후 CONCEPTS 테이블을 다시 검증하여 일관성 확인

---

## 핵심 규칙

1. **두 에이전트를 병렬로 실행** 단일 메시지에서 — 순차적으로 절대 실행하지 마세요
2. **보고서 생성 전 두 에이전트 모두 기다리세요**
3. **버전, URL, 날짜를 추측하지 마세요** — 에이전트 데이터 사용
4. **누락된 개념은 높은 우선순위** — CONCEPTS 테이블은 개발자들이 가장 먼저 보는 것입니다
5. **모든 URL 확인** — 끊어진 링크는 전체 프로젝트에 대한 신뢰를 저하시킵니다
6. **자동 실행하지 마세요** — 항상 먼저 보고서를 제시하세요
7. **항상 체인지로그에 추가하세요** — 2.5단계는 필수입니다. 절대 건너뛰지 마세요. 이전 항목을 절대 덮어쓰지 마세요.
8. **이전 실행과 비교하세요** — 체인지로그의 이전 항목을 읽고 각 실행 항목을 NEW, RECURRING, 또는 RESOLVED로 표시하세요.
9. **검증 체크리스트 실행 (존재하는 경우)** — verification-checklist.md를 읽고 모든 규칙을 실행하세요. 결과가 새 규칙을 보장하는 경우 파일이 없으면 생성하세요.
10. **항상 마지막 업데이트 배지를 업데이트하세요** — 2.6단계는 필수입니다.
11. **항상 모든 CONCEPTS URL을 검증하세요** — 2.7단계는 필수입니다. 끊어진 URL은 높은 우선순위입니다.
12. **붙여넣기할 준비가 된 행 제공** — 누락된 개념에 대해 실행이 복사-붙여넣기가 되도록 정확한 마크다운 테이블 행을 포함하세요.
13. **기존 테이블 형식 준수** — 기존 행의 열 구조, 배지 패턴, 링크 스타일을 일치시키세요.
