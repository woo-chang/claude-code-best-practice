---
name: translation-rules
description: 영어에서 한국어로의 번역 규칙을 정의합니다. 영어 유지 항목, 한글 번역 항목, 포매팅 규칙, 용어 사전을 포함합니다.
user-invocable: false
---

# 번역 규칙 스킬

이 스킬은 리포지토리를 영어에서 한국어로 번역할 때 적용하는 중앙 규칙집입니다. 모든 번역 에이전트는 이 규칙을 **반드시** 따라야 합니다.

## 섹션 1: 절대 번역 금지 (영어 유지)

### YAML/JSON 키 및 프론트매터 필드명
모든 프론트매터 키는 영어로 유지합니다:
`name`, `description`, `tools`, `model`, `allowedTools`, `disallowedTools`, `permissionMode`, `maxTurns`, `skills`, `mcpServers`, `hooks`, `memory`, `background`, `effort`, `isolation`, `color`, `context`, `agent`, `argument-hint`, `disable-model-invocation`, `user-invocable`, `allowed-tools`

### 도구 이름
`Bash`, `Edit`, `Read`, `Write`, `Glob`, `Grep`, `Agent`, `Skill`, `WebFetch`, `WebSearch`, `NotebookEdit`, `AskUserQuestion`, `TaskCreate`, `TaskUpdate`, `TaskList`

### 파일 및 디렉토리 경로
모든 파일 경로와 디렉토리 이름은 그대로 유지합니다:
`.claude/`, `best-practice/`, `reports/`, `tips/`, `implementation/`, `videos/`, `tutorial/`, `changelog/`, `orchestration-workflow/`, `development-workflows/`, `agent-teams/`, `presentation/`, `!/`, `CLAUDE.md`, `README.md`, `SKILL.md`, `MEMORY.md`, `.mcp.json`, `settings.json`, `hooks-config.json`

### CLI 커맨드 및 슬래시 커맨드
`/compact`, `/memory`, `/weather-orchestrator`, `/translate-ko`, `/doctor`, `/help`, `/batch`, `claude`, `gh`, `git`, `npm`, `python3`

### 모델 별칭
`haiku`, `sonnet`, `opus`, `inherit`

### 코드 펜스 및 인라인 코드
` ``` ` 블록 내부와 인라인 `` ` `` 백틱 내부의 모든 내용은 영어로 유지합니다. 코드 펜스나 인라인 코드 내부의 내용은 **절대 번역하지 않습니다.**

### 훅 이벤트 이름
`PreToolUse`, `PostToolUse`, `Stop`, `SessionStart`, `SessionEnd`, `Setup`, `Notification`, `SubagentStart`, `SubagentStop`, `PreCompact`, `UserPromptSubmit`, `PermissionRequest`, `TeammateIdle`, `TaskCompleted`, `ConfigChange`

### 권한 모드 값
`acceptEdits`, `plan`, `bypassPermissions`, `dontAsk`

### 배지 및 Shield.io URL
`![...](https://img.shields.io/...)` 구문 전체를 변경하지 않습니다. URL 인코딩된 텍스트를 수정하지 마세요.

### SVG 및 이미지 참조
모든 `!/tags/*.svg`, 이미지 경로, 에셋 참조는 변경하지 않습니다.

### GitHub 및 외부 URL
모든 URL은 변경하지 않습니다.

### HTML 속성
`class`, `id`, `data-*`, `style`, `href`, `src` 속성명은 영어 유지. 속성값도 영어 유지 (단, 사용자에게 보이는 `alt` 텍스트는 번역 가능).

### 프론트매터 `name` 필드 값
시스템 식별자이므로 절대 번역하지 않습니다: `weather-agent`, `translation-rules`, `markdown-translator` 등.

### 기능 키워드
에이전트 description의 `PROACTIVELY` — Claude Code 자동 호출 트리거 단어입니다. 영어로 유지하세요.

### `argument-hint` 값
CLI 자동완성에 표시되므로 영어로 유지합니다.

## 섹션 2: 한글로 번역

### 프론트매터 `description` 필드 값
사용자에게 표시되는 설명 텍스트를 번역합니다. `PROACTIVELY`가 포함된 경우 해당 단어만 영어로 유지하고 나머지를 번역합니다.

예시:
- 변경 전: `description: Use this agent PROACTIVELY when you need to fetch weather data`
- 변경 후: `description: 날씨 데이터를 가져와야 할 때 PROACTIVELY 이 에이전트를 사용하세요`

### 마크다운 본문 산문
- 제목 (`#`, `##`, `###` 등)
- 단락 및 설명 텍스트
- 불릿 포인트 및 번호 리스트
- 표 셀 텍스트 (설명, 해설)
- 참고사항, 팁, 주의사항

### 규칙 설명
`.claude/rules/*.md` 본문의 산문

### 설정의 사용자 대면 텍스트
`settings.json`의 `spinnerVerbs` 및 `spinnerTipsOverride` 값

### 프레젠테이션 슬라이드 텍스트
`<h1>`, `<h2>`, `<h3>`, `<p>`, `<li>`, `<td>`, `<span>`, `<div>` 요소 내부의 HTML 텍스트 노드. 속성, CSS, JavaScript는 번역하지 않습니다.

### CLAUDE.md 및 README.md 산문
기술 식별자, 코드 블록, 파일 경로를 보존하면서 설명 단락을 번역합니다.

## 섹션 3: 포매팅 규칙

### 링크 텍스트 vs 링크 대상
링크 텍스트만 번역하고, href는 변경하지 않습니다:
- 변경 전: `[Back to Claude Code Best Practice](../)`
- 변경 후: `[Claude Code 모범 사례로 돌아가기](../)`

### 앵커 보존
링크 대상이 되는 제목 앞에 명시적 앵커를 삽입합니다:
```markdown
<a id="original-english-heading"></a>
## 번역된 제목
```
이렇게 하면 모든 내부 프래그먼트 링크가 보존됩니다.

### 산문과 코드 혼합
인라인 코드 주변의 산문만 번역하고, 코드는 변경하지 않습니다:
- 변경 전: `Use the `Edit` tool to modify files`
- 변경 후: `` `Edit` 도구를 사용하여 파일을 수정하세요``

### 구조 보존
- 모든 마크다운 포매팅 보존 (굵게, 기울임, 취소선)
- 모든 줄바꿈, 빈 줄, 들여쓰기 보존
- 표 정렬 보존 (`|---|---|`)
- 이미지 참조 보존 `![alt](path)` — 사용자에게 보이는 alt 텍스트만 번역
- 배지 참조는 완전히 변경하지 않음

### 뒤로가기 네비게이션 링크
보이는 텍스트만 번역합니다:
- 변경 전: `<a href="../">← Back to Claude Code Best Practice</a>`
- 변경 후: `<a href="../">← Claude Code 모범 사례로 돌아가기</a>`

## 섹션 4: 용어 통일 사전

모든 파일에서 다음 한국어 용어를 일관되게 사용합니다:

| English | Korean |
|---------|--------|
| Best Practice | 모범 사례 |
| Subagent | 서브에이전트 |
| Skill | 스킬 |
| Command | 커맨드 |
| Hook | 훅 |
| Workflow | 워크플로우 |
| Orchestration | 오케스트레이션 |
| Context | 컨텍스트 |
| Frontmatter | 프론트매터 |
| Agent | 에이전트 |
| Plugin | 플러그인 |
| Memory | 메모리 |
| Model | 모델 |
| Permission | 권한 |
| Settings | 설정 |
| Sandbox | 샌드박스 |
| Configuration | 구성 |
| Repository | 리포지토리 |
| Tool | 도구 |
| Prompt | 프롬프트 |
| Token | 토큰 |
| Context Window | 컨텍스트 윈도우 |
| Rate Limit | 레이트 리밋 |
| Deployment | 배포 |
| Pipeline | 파이프라인 |
| Middleware | 미들웨어 |
| Endpoint | 엔드포인트 |
| Codebase | 코드베이스 |
| Refactoring | 리팩토링 |
| Debugging | 디버깅 |

## 섹션 5: 핵심 안전 규칙

**확실하지 않으면 영어로 유지하세요.** 기술 식별자를 번역해서 깨뜨리는 것이 산문 구절을 영어로 남겨두는 것보다 훨씬 심각합니다. 검증 에이전트가 미번역 산문을 검토 대상으로 표시할 것입니다 — 하지만 깨진 식별자는 시스템 장애를 일으킵니다.
