---
name: commit-rules
description: 한글 커밋 메시지 작성 규칙과 커밋 전 검증 체크리스트를 정의합니다. 커밋 유형 분류, 메시지 형식, attribution, 번역 프로젝트 특화 검증을 포함합니다.
user-invocable: false
---

# 커밋 규칙 스킬

이 프로젝트의 커밋 메시지는 **한글**로 작성합니다. 이 스킬은 커밋 메시지 형식과 **커밋 전 검증 규칙**을 정의합니다.

---

## 파트 1: 커밋 메시지 형식

```
<영어 접두사>: <한글 변경 요약> — <세부 컨텍스트>

<본문 (선택)>

Co-Authored-By: Claude <noreply@anthropic.com>
```

### 유형 접두사

접두사는 **영어**로 작성하고, 요약과 본문은 **한글**로 작성합니다.

| 접두사 | 용도 | 예시 |
|--------|------|------|
| `feat` | 새 파일/기능 생성 | feat: 번역 하네스 스킬 3개 추가 |
| `translate` | 한글 번역 작업 | translate: best-practice 8개 파일 한글화 |
| `update` | 기존 내용 변경/개선 | update: 오케스트레이터 화이트리스트 업데이트 |
| `remove` | 파일/내용 제거 | remove: 사용하지 않는 레거시 설정 제거 |
| `fix` | 버그/오류 수정 | fix: 프론트매터 YAML 구문 오류 수정 |
| `config` | 설정/구성 변경 | config: .gitignore에 .idea/ 추가 |
| `verify` | 검증/테스트 관련 | verify: 번역 후 링크 유효성 확인 |

### 변경 요약 규칙
- 한 줄로 **무엇을 했는지** 간결하게 서술 (70자 이내 권장)
- 파일 수나 범위가 있으면 포함: "best-practice 8개 파일"
- 여러 작업이 있으면 `—` (em dash)로 구분

### 본문 규칙 (선택)
- 변경 파일이 3개 이상이면 본문에 파일 목록 또는 주요 변경 내용을 불릿으로 나열
- 본문은 빈 줄로 요약과 구분
- 기술적 식별자 (파일명, 도구명, 모델명)는 영어 그대로 사용

## 예시

### 단순 변경 (본문 없음)
```
config: .gitignore에 .idea/ 추가

Co-Authored-By: Claude <noreply@anthropic.com>
```

### 다중 파일 변경 (본문 포함)
```
feat: 한글 번역 하네스 구축 — 스킬 3개, 에이전트 5개, 커맨드 2개, 규칙 1개

- translation-rules 스킬: 번역 규칙 중앙 관리 (용어 사전 30개 항목)
- validation-rules 스킬: 번역 후 8가지 검증 체크리스트
- commit-rules 스킬: 한글 커밋 규칙 + 커밋 전 7가지 검증
- translate-ko 커맨드: /translate-ko 진입점
- commit-ko 커맨드: /commit-ko 한글 커밋
- translation-orchestrator 에이전트: 103개 파일 화이트리스트 기반 배치 총괄
- markdown-translator, html-translator, json-translator: 파일 유형별 번역 워커
- translation-validator 에이전트: 구조적 무결성 검증 (읽기 전용)
- translation.md 규칙: 번역 작업 시 안전망

Co-Authored-By: Claude <noreply@anthropic.com>
```

### 번역 배치 커밋
```
translate: best-practice 8개 파일 한글화

- claude-subagents.md
- claude-commands.md
- claude-skills.md
- claude-settings.md
- claude-memory.md
- claude-mcp.md
- claude-power-ups.md
- claude-cli-startup-flags.md

Co-Authored-By: Claude <noreply@anthropic.com>
```

## Attribution 규칙

- 모든 커밋에 `Co-Authored-By: Claude <noreply@anthropic.com>`을 마지막 줄에 포함합니다
- 본문과 attribution 사이에 빈 줄을 넣습니다
- HEREDOC 방식으로 커밋하여 포매팅을 보존합니다:

```bash
git commit -m "$(cat <<'EOF'
<커밋 메시지>

Co-Authored-By: Claude <noreply@anthropic.com>
EOF
)"
```

## 커밋 단위 가이드

| 상황 | 커밋 단위 |
|------|-----------|
| 하네스 파일 생성/수정 | 관련 파일을 한번에 커밋 |
| 번역 배치 완료 | 배치 단위로 커밋 (배치 A, B, C... 각각) |
| 설정 변경 | 개별 커밋 |
| 검증 후 수정 | 수정 내용별로 커밋 |

---

## 파트 2: 커밋 전 검증 체크리스트

커밋 실행 전 다음 7가지 검증을 **반드시** 통과해야 합니다. 하나라도 실패하면 커밋을 중단하고 사용자에게 알립니다.

### 검증 1: 민감 파일 감지
staging된 파일 중 다음 패턴이 있으면 **차단**합니다:
- `.env`, `.env.*` 파일
- `credentials`, `secret`, `token`이 포함된 파일명
- `*.pem`, `*.key`, `*.p12` 인증서 파일
- `*password*`, `*apikey*` 파일

**검증 방법**: `git diff --staged --name-only`로 파일 목록을 확인하고 위 패턴과 대조합니다.

### 검증 2: 브랜치 보호
현재 브랜치가 다음인 경우 **차단**합니다:
- `main`
- `master`

**검증 방법**: `git branch --show-current`로 확인합니다.
**메시지**: "main 브랜치에 직접 커밋할 수 없습니다. `ko` 브랜치에서 작업하세요."

### 검증 3: 프론트매터 정합성
staging된 `.md` 파일의 YAML 프론트매터를 검사합니다:
- `---` 마커 사이의 키가 모두 영어인지 확인
- 키 목록: `name`, `description`, `tools`, `model`, `allowedTools`, `disallowedTools`, `permissionMode`, `maxTurns`, `skills`, `mcpServers`, `hooks`, `memory`, `background`, `effort`, `isolation`, `color`, `context`, `agent`, `argument-hint`, `disable-model-invocation`, `user-invocable`, `allowed-tools`
- 한글이 포함된 키가 발견되면 **경고** (오류는 아님, 사용자에게 확인)

**검증 방법**: staging된 `.md` 파일을 Read로 읽어 프론트매터 영역의 키를 파싱합니다.

### 검증 4: 코드 펜스 내 한글 감지
staging된 파일의 코드 펜스(` ``` ` 블록) 내부에 한글 문자(가-힣)가 있는지 검사합니다:
- 발견되면 **경고** — 해당 줄 번호와 내용을 표시
- 예외: 주석에 의도적으로 한글을 넣은 경우 (사용자 확인 필요)

**검증 방법**: staging된 파일을 Read로 읽어 코드 펜스 영역을 파싱하고 한글 유니코드 범위를 검색합니다.

### 검증 5: 내부 링크 유효성
staging된 `.md` 파일 내의 상대 링크 `[...](상대경로)`의 대상 파일이 존재하는지 확인합니다:
- 존재하지 않는 링크 대상이 있으면 **경고**
- 프래그먼트 앵커(`#anchor`)는 이 단계에서는 건너뜁니다 (전체 검증은 `translation-validator`가 담당)

**검증 방법**: Grep으로 `](` 패턴을 찾아 상대 경로를 추출하고, 파일 존재 여부를 확인합니다.

### 검증 6: JSON 유효성
staging된 `.json` 파일이 있으면 구문 오류가 없는지 확인합니다:
- 파싱 실패 시 **차단**

**검증 방법**: `python3 -m json.tool <파일> > /dev/null 2>&1`로 확인합니다.

### 검증 7: 화이트리스트 외 파일 수정 감지
staging된 파일이 `translation-orchestrator.md`의 번역 대상 화이트리스트 또는 새로 생성한 하네스 파일에 해당하는지 확인합니다:
- 다음 파일 유형이 수정되었으면 **경고**:
  - `.py` 파일 (코드)
  - `.svg`, `.png`, `.jpg`, `.gif`, `.webp` 파일 (이미지)
  - `.wav`, `.mp3` 파일 (오디오)
  - `output.md` (자동 생성)
  - `MEMORY.md` (자동 생성)
- 이 파일들은 번역 대상이 아니므로, 실수로 수정된 것일 수 있습니다

**검증 방법**: `git diff --staged --name-only`로 파일 목록을 확인하고 확장자/경로를 대조합니다.

### 검증 결과 처리

| 결과 | 동작 |
|------|------|
| 모든 검증 통과 | 커밋 진행 (사용자 확인 후) |
| **차단** 항목 발견 | 커밋 중단, 문제 내용 표시, 수정 요청 |
| **경고** 항목만 발견 | 경고 내용을 표시하고 사용자에게 계속 진행할지 확인 |

---

## 파트 3: 금지 사항

- `git commit --amend`로 이전 커밋 수정 금지 (새 커밋 생성)
- `git push --force` 금지
- `--no-verify`로 훅 건너뛰기 금지
- `main`/`master` 브랜치 직접 커밋 금지
- 민감 파일 (.env, credentials) 커밋 금지
- 번역 대상이 아닌 파일 (.py, .svg, 이미지, 오디오) 수정 커밋 금지
