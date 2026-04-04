# HOOKS-README
훅에 대한 모든 세부 사항, 스크립트, 지시사항을 포함합니다

## 훅 이벤트 개요 - [공식 22개 훅](https://code.claude.com/docs/en/hooks)
Claude Code는 워크플로우의 다양한 시점에서 실행되는 여러 훅 이벤트를 제공합니다:

| # | 훅 | 설명 | 옵션 |
|:-:|------|-------------|---------|
| 1 | `PreToolUse` | 도구 호출 이전에 실행 (차단 가능) | `async`, `timeout: 5000`, `tool_use_id` |
| 2 | `PermissionRequest` | Claude Code가 사용자에게 권한을 요청할 때 실행 | `async`, `timeout: 5000`, `permission_suggestions` |
| 3 | `PostToolUse` | 도구 호출이 성공적으로 완료된 후 실행 | `async`, `timeout: 5000`, `tool_response`, `tool_use_id` |
| 4 | `PostToolUseFailure` | 도구 호출이 실패한 후 실행 | `async`, `timeout: 5000`, `error`, `is_interrupt`, `tool_use_id` |
| 5 | `UserPromptSubmit` | 사용자가 프롬프트를 제출할 때, Claude가 처리하기 전에 실행 | `async`, `timeout: 5000`, `prompt` |
| 6 | `Notification` | Claude Code가 알림을 보낼 때 실행 | `async`, `timeout: 5000`, `notification_type`, `message`, `title` |
| 7 | `Stop` | Claude Code가 응답을 완료할 때 실행 | `async`, `timeout: 5000`, `last_assistant_message`, `stop_hook_active` |
| 8 | `SubagentStart` | 서브에이전트 태스크가 시작할 때 실행 | `async`, `timeout: 5000`, `agent_id`, `agent_type` |
| 9 | `SubagentStop` | 서브에이전트 태스크가 완료될 때 실행 | `async`, `timeout: 5000`, `agent_id`, `agent_type`, `last_assistant_message`, `agent_transcript_path`, `stop_hook_active` |
| 10 | `PreCompact` | Claude Code가 compact 작업을 실행하기 직전에 실행 | `async`, `timeout: 5000`, `once`, `trigger`, `custom_instructions` |
| 11 | `PostCompact` | Claude Code가 compact 작업을 완료한 후 실행 | `async`, `timeout: 5000`, `trigger`, `compact_summary` |
| 12 | `SessionStart` | Claude Code가 새 세션을 시작하거나 기존 세션을 재개할 때 실행 | `async`, `timeout: 5000`, `once`, `agent_type`, `model`, `source` |
| 13 | `SessionEnd` | Claude Code 세션이 종료될 때 실행 | `async`, `timeout: 5000`, `once`, `reason` |
| 14 | `Setup` | Claude Code가 프로젝트 초기화를 위해 /setup 커맨드를 실행할 때 실행 | `async`, `timeout: 30000` |
| 15 | `TeammateIdle` | 팀원 에이전트가 유휴 상태가 될 때 실행 (실험적 에이전트 팀) | `async`, `timeout: 5000`, `teammate_name`, `team_name` |
| 16 | `TaskCompleted` | 백그라운드 태스크가 완료될 때 실행 (실험적 에이전트 팀) | `async`, `timeout: 5000`, `task_id`, `task_subject`, `task_description`, `teammate_name`, `team_name` |
| 17 | `ConfigChange` | 세션 중에 구성 파일이 변경될 때 실행 | `async`, `timeout: 5000`, `file_path`, `source` |
| 18 | `WorktreeCreate` | 에이전트 worktree 격리가 커스텀 VCS 설정을 위해 worktree를 생성할 때 실행 | `async`, `timeout: 5000`, `name` |
| 19 | `WorktreeRemove` | 에이전트 worktree 격리가 커스텀 VCS 정리를 위해 worktree를 제거할 때 실행 | `async`, `timeout: 5000`, `worktree_path` |
| 20 | `InstructionsLoaded` | CLAUDE.md 또는 `.claude/rules/*.md` 파일이 컨텍스트에 로드될 때 실행 | `async`, `timeout: 5000`, `file_path`, `memory_type`, `load_reason`, `globs`, `trigger_file_path`, `parent_file_path` |
| 21 | `Elicitation` | MCP 서버가 도구 호출 중에 사용자 입력을 요청할 때 실행 | `async`, `timeout: 5000`, `mcp_server_name`, `message`, `mode`, `url`, `elicitation_id`, `requested_schema` |
| 22 | `ElicitationResult` | 사용자가 MCP 엘리시테이션에 응답한 후, 서버로 응답이 전송되기 전에 실행 | `async`, `timeout: 5000`, `mcp_server_name`, `action`, `content`, `mode`, `elicitation_id` |

> **참고:** 훅 15-16 (`TeammateIdle` 및 `TaskCompleted`)는 실험적 에이전트 팀 기능이 필요합니다. 활성화하려면 Claude Code를 실행할 때 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`을 설정하세요.

### 공식 문서에 없는 항목

다음 항목들은 [Claude Code 체인지로그](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)에 존재하지만 [공식 훅 참조](https://code.claude.com/docs/en/hooks)에는 **나열되지 않습니다**:

| 항목 | 추가된 버전 | 체인지로그 참조 | 참고사항 |
|------|----------|-------------------|-------|
| `Setup` 훅 | [v2.1.10](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md#2110) | "저장소 설정 및 유지 관리 작업을 위해 `--init`, `--init-only`, 또는 `--maintenance` CLI 플래그를 통해 트리거될 수 있는 새 Setup 훅 이벤트 추가" | 공식 훅 참조 페이지에 나열되지 않음 (21개 훅 나열, Setup 제외) |
| 에이전트 프론트매터 훅 | [v2.1.0](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md#210) | "에이전트 프론트매터에 훅 지원 추가, 에이전트가 에이전트 라이프사이클에 범위 지정된 PreToolUse, PostToolUse, Stop 훅을 정의할 수 있게 됨" | 체인지로그는 3개 훅만 언급하지만 테스트 결과 에이전트 세션에서 **6개 훅**이 실제로 실행됨: PreToolUse, PostToolUse, PermissionRequest, PostToolUseFailure, Stop, SubagentStop. 16개 훅 전체가 지원되는 것은 아님. |

## 사전 요구사항

훅을 사용하기 전에 시스템에 **Python 3**가 설치되어 있는지 확인하세요.

### 필수 소프트웨어

#### 모든 플랫폼 (Windows, macOS, Linux)
- **Python 3**: 훅 스크립트 실행에 필요
- 설치 확인: `python3 --version`

**설치 지침:**
- **Windows**: [python.org](https://www.python.org/downloads/)에서 다운로드하거나 `winget install Python.Python.3`로 설치
- **macOS**: `brew install python3`으로 설치 ([Homebrew](https://brew.sh/) 필요)
- **Linux**: `sudo apt install python3` (Ubuntu/Debian) 또는 `sudo yum install python3` (RHEL/CentOS)로 설치

### 오디오 플레이어 (선택 사항 — 자동 감지)

훅 스크립트는 플랫폼에 맞는 오디오 플레이어를 자동으로 감지하여 사용합니다:

- **macOS**: 내장 `afplay` 사용 (설치 불필요)
- **Linux**: `pulseaudio-utils`의 `paplay` 사용 — `sudo apt install pulseaudio-utils`로 설치
- **Windows**: 내장 `winsound` 모듈 사용 (Python에 포함)

### 훅 실행 방법

훅은 Python 3로 직접 실행되도록 `.claude/settings.json`에 구성됩니다:

```json
{
  "type": "command",
  "command": "python3 .claude/hooks/scripts/hooks.py"
}
```

## 훅 구성 (활성화/비활성화)

훅은 전역 및 개별 수준 모두에서 쉽게 활성화 또는 비활성화할 수 있습니다.

### 모든 훅 한 번에 비활성화

`.claude/settings.local.json`을 편집하고 다음을 설정하세요:
```json
{
  "disableAllHooks": true
}
```

**참고:** `.claude/settings.local.json` 파일은 git에서 무시되므로, 각 사용자가 팀의 공유 설정인 `.claude/settings.json`에 영향을 주지 않고 자체 훅 기본 설정을 구성할 수 있습니다.

> **관리형 설정:** 관리자가 관리형 정책 설정을 통해 훅을 구성한 경우, 사용자, 프로젝트, 또는 로컬 설정에서 설정한 `disableAllHooks`로 해당 관리형 훅을 비활성화할 수 없습니다 (v2.1.49에서 수정됨).

### 개별 훅 비활성화

세밀한 제어를 위해 훅 구성 파일을 편집하여 특정 훅을 비활성화할 수 있습니다.

#### 구성 파일

개별 훅 관리를 위한 두 가지 구성 파일이 있습니다:

1. **`.claude/hooks/config/hooks-config.json`** — git에 커밋된 공유/기본 구성
2. **`.claude/hooks/config/hooks-config.local.json`** — 개인 재정의 (git에서 무시)

로컬 구성 파일 (`.local.json`)이 공유 구성보다 우선순위가 높으므로, 각 개발자가 팀에 영향을 주지 않고 훅 동작을 커스터마이즈할 수 있습니다.

#### 공유 구성

팀 전체 기본값을 위해 `.claude/hooks/config/hooks-config.json`을 편집하세요:

```json
{
  "disableLogging": false,
  "disablePreToolUseHook": false,
  "disablePermissionRequestHook": false,
  "disablePostToolUseHook": false,
  "disablePostToolUseFailureHook": false,
  "disableUserPromptSubmitHook": false,
  "disableNotificationHook": false,
  "disableStopHook": false,
  "disableSubagentStartHook": false,
  "disableSubagentStopHook": false,
  "disablePreCompactHook": false,
  "disablePostCompactHook": false,
  "disableElicitationHook": false,
  "disableElicitationResultHook": false,
  "disableSessionStartHook": false,
  "disableSessionEndHook": false,
  "disableSetupHook": false,
  "disableTeammateIdleHook": false,
  "disableTaskCompletedHook": false,
  "disableConfigChangeHook": false,
  "disableWorktreeCreateHook": false,
  "disableWorktreeRemoveHook": false,
  "disableInstructionsLoadedHook": false
}
```

**구성 옵션:**
- `disableLogging`: 훅 이벤트를 `.claude/hooks/logs/hooks-log.jsonl`에 로깅하지 않으려면 `true`로 설정 (로그 파일 증가 방지에 유용)

#### 로컬 구성 (개인 재정의)

개인 기본 설정을 위해 `.claude/hooks/config/hooks-config.local.json`을 만들거나 편집하세요:

```json
{
  "disableLogging": true,
  "disablePostToolUseHook": true,
  "disableSessionStartHook": true
}
```

이 예시에서는 로깅이 비활성화되고, PostToolUse 및 SessionStart 훅이 로컬에서 재정의됩니다. 다른 모든 훅은 공유 구성 값을 사용합니다.

**참고:** 개별 훅 토글은 훅 스크립트 (`.claude/hooks/scripts/hooks.py`)가 확인합니다. 로컬 설정이 공유 설정을 재정의하며, 훅이 비활성화된 경우 스크립트가 소리를 재생하거나 훅 로직을 실행하지 않고 자동으로 종료됩니다.

### 텍스트 음성 변환 (TTS)
사운드 생성에 사용된 웹사이트: https://elevenlabs.io/
사용된 음성: Samara X

## 에이전트 프론트매터 훅

Claude Code 2.1.0에서 에이전트 프론트매터 파일에 정의된 에이전트별 훅 지원이 도입되었습니다. 이 훅들은 에이전트의 라이프사이클 내에서만 실행되며 훅 이벤트의 일부만 지원합니다.

### 지원되는 에이전트 훅

에이전트 프론트매터 훅은 **6개 훅**을 지원합니다 (전체 16개가 아님). 체인지로그에서 원래 3개만 언급했지만, 테스트 결과 에이전트 세션에서 6개 훅이 실제로 실행됨이 확인되었습니다:
- `PreToolUse`: 에이전트가 도구를 사용하기 전에 실행
- `PostToolUse`: 에이전트가 도구 사용을 완료한 후 실행
- `PermissionRequest`: 도구에 사용자 권한이 필요할 때 실행
- `PostToolUseFailure`: 도구 호출이 실패한 후 실행
- `Stop`: 에이전트가 완료될 때 실행
- `SubagentStop`: 서브에이전트가 완료될 때 실행

> **참고:** [v2.1.0 체인지로그](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md#210)에는 3개 훅만 언급됩니다: *"에이전트 프론트매터에 훅 지원 추가, 에이전트가 에이전트 라이프사이클에 범위 지정된 PreToolUse, PostToolUse, Stop 훅을 정의할 수 있게 됨."* 그러나 `claude-code-voice-hook-agent`를 사용한 테스트 결과 에이전트 세션에서 6개 훅이 실제로 실행됨이 확인되었습니다. 나머지 10개 훅 (예: Notification, SessionStart, SessionEnd 등)은 에이전트 컨텍스트에서 실행되지 않습니다.
>
> **업데이트 (2026년 2월):** [공식 훅 참조](https://code.claude.com/docs/en/hooks)에서 이제 스킬/에이전트 프론트매터 훅에 대해 *"모든 훅 이벤트가 지원됩니다"*라고 명시합니다. 이는 지원이 원래 테스트된 6개 훅을 넘어 확장되었을 수 있음을 의미합니다. 추가 훅이 이제 에이전트 세션에서 실행되는지 재테스트를 권장합니다.

### 에이전트 사운드 폴더

에이전트별 사운드는 별도 폴더에 저장됩니다:
- `.claude/hooks/sounds/agent_pretooluse/`
- `.claude/hooks/sounds/agent_posttooluse/`
- `.claude/hooks/sounds/agent_permissionrequest/`
- `.claude/hooks/sounds/agent_posttoolusefailure/`
- `.claude/hooks/sounds/agent_stop/`
- `.claude/hooks/sounds/agent_subagentstop/`

### 훅이 있는 에이전트 만들기

1. `.claude/agents/`에 에이전트 정의 파일을 만드세요:

```markdown
---
name: my-agent
description: 이 에이전트가 하는 일에 대한 설명
hooks:
  PreToolUse:
    - type: command
      command: python3 ${CLAUDE_PROJECT_DIR}/.claude/hooks/scripts/hooks.py --agent=my-agent
      timeout: 5000
      async: true
      statusMessage: PreToolUse
  PostToolUse:
    - type: command
      command: python3 ${CLAUDE_PROJECT_DIR}/.claude/hooks/scripts/hooks.py --agent=my-agent
      timeout: 5000
      async: true
      statusMessage: PostToolUse
  PermissionRequest:
    - type: command
      command: python3 ${CLAUDE_PROJECT_DIR}/.claude/hooks/scripts/hooks.py --agent=my-agent
      timeout: 5000
      async: true
      statusMessage: PermissionRequest
  PostToolUseFailure:
    - type: command
      command: python3 ${CLAUDE_PROJECT_DIR}/.claude/hooks/scripts/hooks.py --agent=my-agent
      timeout: 5000
      async: true
      statusMessage: PostToolUseFailure
  Stop:
    - type: command
      command: python3 ${CLAUDE_PROJECT_DIR}/.claude/hooks/scripts/hooks.py --agent=my-agent
      timeout: 5000
      async: true
      statusMessage: Stop
  SubagentStop:
    - type: command
      command: python3 ${CLAUDE_PROJECT_DIR}/.claude/hooks/scripts/hooks.py --agent=my-agent
      timeout: 5000
      async: true
      statusMessage: SubagentStop
---

여기에 에이전트 지시사항을 작성하세요...
```

2. 에이전트 사운드 폴더에 사운드 파일을 추가하세요:
   - `agent_pretooluse/agent_pretooluse.wav`
   - `agent_posttooluse/agent_posttooluse.wav`
   - `agent_permissionrequest/agent_permissionrequest.wav`
   - `agent_posttoolusefailure/agent_posttoolusefailure.wav`
   - `agent_stop/agent_stop.wav`
   - `agent_subagentstop/agent_subagentstop.wav`

### 예시: 날씨 가져오기 에이전트

훅이 구성된 에이전트의 전체 예시는 `.claude/agents/claude-code-voice-hook-agent.md`를 참조하세요.

### 훅 옵션: `once: true`

`once: true` 옵션은 훅이 세션당 한 번만 실행되도록 보장합니다:

```json
{
  "type": "command",
  "command": "python3 .claude/hooks/scripts/hooks.py",
  "timeout": 5000,
  "once": true
}
```

이는 한 번만 트리거되어야 하는 `SessionStart`, `SessionEnd`, `PreCompact`와 같은 훅에 유용합니다.

> **참고:** `once` 옵션은 **에이전트가 아닌 스킬 전용**입니다. 설정 기반 훅과 스킬 프론트매터에서 작동하지만, 에이전트 프론트매터 훅에서는 지원되지 않습니다.

### 훅 옵션: `async: true`

`"async": true`를 추가하여 Claude Code의 실행을 차단하지 않고 백그라운드에서 훅을 실행할 수 있습니다:

```json
{
  "type": "command",
  "command": "python3 .claude/hooks/scripts/hooks.py",
  "timeout": 5000,
  "async": true
}
```

**async 훅을 사용해야 할 때:**
- 로깅 및 분석
- 알림 및 사운드 효과
- Claude Code를 느리게 해서는 안 되는 모든 부작용

이 프로젝트는 음성 알림이 실행을 차단할 필요 없는 부작용이므로 모든 훅에 `async: true`를 사용합니다. `timeout`은 async 훅이 종료되기 전까지 실행될 수 있는 시간을 지정합니다.

### 훅 옵션: `statusMessage`

`statusMessage` 필드는 훅이 실행되는 동안 사용자에게 표시되는 커스텀 스피너 메시지를 설정합니다:

```json
{
  "type": "command",
  "command": "python3 .claude/hooks/scripts/hooks.py",
  "timeout": 5000,
  "async": true,
  "statusMessage": "PreToolUse"
}
```

이 프로젝트는 모든 훅에서 `statusMessage`를 훅 이벤트 이름으로 설정하여, 스피너가 어떤 훅이 실행 중인지 간략하게 표시합니다 (예: "PreToolUse", "SessionStart", "Stop"). 이는 동기 훅에서 가장 잘 보이며, async 훅의 경우 훅이 백그라운드에서 실행되기 전에 메시지가 잠시 표시됩니다.

## 훅 유형

Claude Code는 네 가지 훅 핸들러 유형을 지원합니다. 이 프로젝트는 모든 사운드 재생에 `command` 훅을 사용합니다.

### `type: "command"` (이 프로젝트에서 사용)

셸 커맨드를 실행합니다. stdin을 통해 JSON 입력을 받고, 종료 코드와 stdout을 통해 결과를 전달합니다.

```json
{
  "type": "command",
  "command": "python3 .claude/hooks/scripts/hooks.py",
  "timeout": 5000,
  "async": true
}
```

### `type: "prompt"`

단일 턴 평가를 위해 Claude 모델에 프롬프트를 보냅니다. 모델은 JSON (`{"ok": true/false, "reason": "..."}`)으로 예/아니오 결정을 반환합니다. 결정론적 규칙보다 판단이 필요한 결정에 유용합니다.

```json
{
  "type": "prompt",
  "prompt": "Check if all tasks are complete. $ARGUMENTS",
  "timeout": 30
}
```

**지원되는 이벤트:** PreToolUse, PostToolUse, PostToolUseFailure, PermissionRequest, UserPromptSubmit, Stop, SubagentStop, TaskCompleted. **커맨드 전용 이벤트 (prompt/agent 유형 미지원):** ConfigChange, Elicitation, ElicitationResult, InstructionsLoaded, Notification, PostCompact, PreCompact, SessionEnd, SessionStart, Setup, SubagentStart, TeammateIdle, WorktreeCreate, WorktreeRemove.

### `type: "agent"`

조건을 확인하기 위해 멀티 턴 도구 접근 (Read, Grep, Glob)이 있는 서브에이전트를 생성한 후 결정을 반환합니다. prompt 훅과 동일한 응답 형식. 검증에 실제 파일이나 테스트 출력 검사가 필요할 때 유용합니다.

```json
{
  "type": "agent",
  "prompt": "Verify that all unit tests pass. $ARGUMENTS",
  "timeout": 120
}
```

### `type: "http"` (v2.1.63부터)

셸 커맨드를 실행하는 대신 URL에 JSON을 POST하고 JSON 응답을 받습니다. 외부 서비스나 웹훅과 통합하는 데 유용합니다. HTTP 훅은 샌드박싱이 활성화된 경우 샌드박스 네트워크 프록시를 통해 라우팅됩니다.

```json
{
  "type": "http",
  "url": "http://localhost:8080/hooks/pre-tool-use",
  "timeout": 30,
  "headers": {
    "Authorization": "Bearer $MY_TOKEN"
  },
  "allowedEnvVars": ["MY_TOKEN"]
}
```

**미지원 이벤트:** ConfigChange, Elicitation, ElicitationResult, InstructionsLoaded, Notification, PostCompact, PreCompact, SessionEnd, SessionStart, Setup, SubagentStart, TeammateIdle, WorktreeCreate, WorktreeRemove (커맨드 전용 이벤트). 헤더는 `$VAR_NAME`으로 환경 변수 보간을 지원하지만, `allowedEnvVars`에 명시적으로 나열된 변수에 한해서만 가능합니다.

## 환경 변수

Claude Code는 훅 스크립트에 다음 환경 변수를 제공합니다:

| 변수 | 사용 가능 범위 | 설명 |
|----------|-------------|-------------|
| `$CLAUDE_PROJECT_DIR` | 모든 훅 | 프로젝트 루트 디렉토리. 공백이 있는 경로는 따옴표로 묶으세요 |
| `$CLAUDE_ENV_FILE` | SessionStart만 | 이후 Bash 커맨드를 위한 환경 변수 저장 파일 경로. 다른 훅의 변수를 보존하려면 추가 (`>>`) 사용 |
| `${CLAUDE_PLUGIN_ROOT}` | 플러그인 훅 | 플러그인과 번들된 스크립트를 위한 플러그인 루트 디렉토리 |
| `$CLAUDE_CODE_REMOTE` | 모든 훅 | 원격 웹 환경에서는 `"true"`로 설정되며, 로컬 CLI에서는 설정되지 않음 |
| `${CLAUDE_SKILL_DIR}` | 스킬 훅 | 스킬과 번들된 스크립트를 위한 스킬 자체 디렉토리 (v2.1.69부터) |
| `CLAUDE_CODE_SESSIONEND_HOOKS_TIMEOUT_MS` | SessionEnd 훅 | SessionEnd 훅 타임아웃을 밀리초 단위로 재정의. v2.1.74 이전에는 구성된 `timeout` 무관하게 1.5초 후 종료. 이제 훅의 `timeout` 값을 존중하거나, 설정된 경우 이 환경 변수 사용 (v2.1.74부터) |
| `session_id` (stdin JSON을 통해) | 모든 훅 | 현재 세션 ID, stdin의 JSON 입력의 일부로 수신 (환경 변수가 아님) |

### 공통 입력 필드 (stdin JSON)

모든 훅은 위의 Options 열에 나열된 훅별 필드 외에도, 다음 공통 필드가 포함된 JSON 객체를 stdin으로 수신합니다:

| 필드 | 유형 | 설명 |
|-------|------|-------------|
| `hook_event_name` | string | 발생한 훅 이벤트 이름 (예: `"PreToolUse"`, `"Stop"`) |
| `session_id` | string | 현재 세션 식별자 |
| `transcript_path` | string | 대화 트랜스크립트 JSON 파일 경로 |
| `cwd` | string | 현재 작업 디렉토리 |
| `permission_mode` | string | 현재 권한 모드: `default`, `plan`, `acceptEdits`, `dontAsk`, 또는 `bypassPermissions` |
| `agent_id` | string | 고유 서브에이전트 식별자. 서브에이전트 컨텍스트 내에서 훅이 실행될 때 존재 (v2.1.69부터) |
| `agent_type` | string | 에이전트 유형 이름 (예: `Bash`, `Explore`, `Plan`, 또는 커스텀). `--agent <name>` 플래그 사용 시 또는 서브에이전트 내부에서 존재 (v2.1.69부터) |

> **참고:** 훅별 필드 (예: PreToolUse의 `tool_name`, Stop의 `last_assistant_message`)는 위의 [훅 이벤트 개요](#훅-이벤트-개요---공식-19개-훅) 테이블의 Options 열에 나열되어 있습니다.

## 훅 관리 커맨드

Claude Code는 훅 관리를 위한 내장 커맨드를 제공합니다:

- **`/hooks`** — 인터랙티브 훅 관리 UI. JSON 파일을 편집하지 않고 훅을 보고, 추가하고, 삭제합니다. 훅은 소스별로 레이블이 표시됩니다: `[User]`, `[Project]`, `[Local]`, `[Plugin]`. 이 메뉴에서 `disableAllHooks`도 토글할 수 있습니다.
- **`claude hooks reload`** — 세션을 재시작하지 않고 훅 구성을 다시 로드합니다. 설정 파일 편집 후 유용합니다 (v2.0.47부터).

## MCP 도구 매처

`PreToolUse`, `PostToolUse`, `PermissionRequest` 훅에서 `mcp__<server>__<tool>` 패턴을 사용하여 MCP (Model Context Protocol) 도구를 매칭할 수 있습니다:

```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "mcp__memory__.*",
      "hooks": [{ "type": "command", "command": "echo 'MCP memory tool used'" }]
    }]
  }
}
```

전체 정규식 지원: `mcp__memory__.*` (메모리 서버의 모든 도구), `mcp__.*__write.*` (모든 서버의 모든 쓰기 도구).

### 훅별 매처 참조

매처는 어떤 이벤트가 훅을 트리거할지 필터링합니다. 모든 훅이 매처를 지원하는 것은 아닙니다 — 매처 지원이 없는 훅은 항상 실행됩니다.

| 훅 | 매처 필드 | 가능한 값 | 예시 |
|------|--------------|-----------------|---------|
| `PreToolUse` | `tool_name` | 모든 도구 이름: `Bash`, `Read`, `Edit`, `Write`, `Glob`, `Grep`, `mcp__*` | `"matcher": "Bash"` |
| `PermissionRequest` | `tool_name` | PreToolUse와 동일 | `"matcher": "mcp__memory__.*"` |
| `PostToolUse` | `tool_name` | PreToolUse와 동일 | `"matcher": "Write"` |
| `PostToolUseFailure` | `tool_name` | PreToolUse와 동일 | `"matcher": "Bash"` |
| `Notification` | `notification_type` | `permission_prompt`, `idle_prompt`, `auth_success`, `elicitation_dialog` | `"matcher": "permission_prompt"` |
| `SubagentStart` | `agent_type` | `Bash`, `Explore`, `Plan`, 또는 커스텀 에이전트 이름 | `"matcher": "Bash"` |
| `SubagentStop` | `agent_type` | `Bash`, `Explore`, `Plan`, 또는 커스텀 에이전트 이름 | `"matcher": "Bash"` |
| `SessionStart` | `source` | `startup`, `resume`, `clear`, `compact` | `"matcher": "startup"` |
| `SessionEnd` | `reason` | `clear`, `logout`, `prompt_input_exit`, `bypass_permissions_disabled`, `other` | `"matcher": "logout"` |
| `PreCompact` | `trigger` | `manual`, `auto` | `"matcher": "auto"` |
| `PostCompact` | `trigger` | `manual`, `auto` | `"matcher": "manual"` |
| `Elicitation` | `mcp_server_name` | MCP 서버 이름 | `"matcher": "my-mcp-server"` |
| `ElicitationResult` | `mcp_server_name` | MCP 서버 이름 | `"matcher": "my-mcp-server"` |
| `ConfigChange` | `source` | `user_settings`, `project_settings`, `local_settings`, `policy_settings`, `skills` | `"matcher": "project_settings"` |
| `UserPromptSubmit` | — | 매처 지원 없음 | 항상 실행 |
| `Stop` | — | 매처 지원 없음 | 항상 실행 |
| `TeammateIdle` | — | 매처 지원 없음 | 항상 실행 |
| `TaskCompleted` | — | 매처 지원 없음 | 항상 실행 |
| `WorktreeCreate` | — | 매처 지원 없음 | 항상 실행 |
| `WorktreeRemove` | — | 매처 지원 없음 | 항상 실행 |
| `InstructionsLoaded` | — | 매처 지원 없음 | 항상 실행 |
| `Setup` | — | 매처 지원 없음 | 항상 실행 |

## 알려진 문제 및 해결 방법

### 에이전트 Stop 훅 버그 (SubagentStop vs Stop)

**버그 리포트:** [GitHub Issue #19220](https://github.com/anthropics/claude-code/issues/19220)

**문제:** 에이전트 프론트매터에서 `Stop` 훅을 정의할 때, 훅 스크립트에 전달되는 `hook_event_name`이 `"Stop"` 대신 `"SubagentStop"`입니다. 이는 공식 문서와 모순되며, 구성된 이름을 올바르게 전달하는 다른 에이전트 훅 (`PreToolUse`와 `PostToolUse`)과의 일관성을 깨뜨립니다.

| 훅 | 정의된 방식 | 수신된 방식 | 상태 |
|------|------------|-------------|--------|
| PreToolUse | `PreToolUse:` | `"PreToolUse"` | ✅ 올바름 |
| PostToolUse | `PostToolUse:` | `"PostToolUse"` | ✅ 올바름 |
| Stop | `Stop:` | `"SubagentStop"` | ❌ 불일치 |

**상태:** [공식 훅 참조](https://code.claude.com/docs/en/hooks#hooks-in-skills-and-agents)는 이제 이를 예상된 동작으로 문서화합니다: *"서브에이전트의 경우, Stop 훅은 서브에이전트가 완료될 때 발생하는 이벤트가 SubagentStop이므로 자동으로 SubagentStop으로 변환됩니다."* 이 프로젝트는 `hooks.py`의 `AGENT_HOOK_SOUND_MAP`을 통해 처리하며, `agent_subagentstop` 사운드 폴더를 매핑하는 별도의 `SubagentStop` 항목이 있습니다.

### PreToolUse 결정 제어 지원 중단

`PreToolUse` 훅은 이전에 도구 호출을 차단하기 위해 최상위 `decision` 및 `reason` 필드를 사용했습니다. 이들은 이제 **지원 중단**되었습니다. 대신 `hookSpecificOutput.permissionDecision` 및 `hookSpecificOutput.permissionDecisionReason`을 사용하세요:

| 지원 중단 | 현재 |
|-----------|---------|
| `"decision": "approve"` | `"hookSpecificOutput": { "permissionDecision": "allow" }` |
| `"decision": "block"` | `"hookSpecificOutput": { "permissionDecision": "deny" }` |

`hooks.py`는 async 사운드 재생을 사용하고 결정 제어를 사용하지 않으므로 이 프로젝트에는 영향을 주지 않습니다.

## 결정 제어 패턴

다른 훅은 실행을 차단하거나 제어하기 위해 다른 출력 스키마를 사용합니다. 이 프로젝트는 결정 제어를 사용하지 않지만 (모든 훅은 async 사운드 재생), 참조를 위해:

| 훅 | 제어 방법 | 값 |
|---------|---------------|--------|
| PreToolUse | `hookSpecificOutput.permissionDecision` | `allow`, `deny`, `ask` |
| PreToolUse | `hookSpecificOutput.autoAllow` | `true` — 이 도구의 향후 사용을 자동 승인 (v2.0.76부터) |
| PermissionRequest | `hookSpecificOutput.decision.behavior` | `allow`, `deny` |
| PostToolUse, PostToolUseFailure, Stop, SubagentStop, ConfigChange | 최상위 `decision` | `block` |
| TeammateIdle, TaskCompleted | `continue` + exit code 2 | `{"continue": false, "stopReason": "..."}` — v2.1.70에서 JSON 결정 제어 추가 |
| UserPromptSubmit | `prompt` 필드 수정 가능 | stdout을 통해 수정된 프롬프트 반환 |
| WorktreeCreate | 비제로 종료 + stdout 경로 | 비제로 종료는 생성 실패; stdout은 worktree 경로 제공 |
| Elicitation | `hookSpecificOutput.action` + `hookSpecificOutput.content` | `accept`, `decline`, `cancel` — MCP 엘리시테이션 응답 제어 |
| ElicitationResult | `hookSpecificOutput.action` + `hookSpecificOutput.content` | `accept`, `decline`, `cancel` — 서버로 전송 전 사용자 응답 재정의 |

### 범용 JSON 출력 필드

모든 훅은 stdout JSON을 통해 다음 필드를 반환할 수 있습니다:

| 필드 | 유형 | 설명 |
|-------|------|-------------|
| `continue` | bool | `false`이면 Claude를 완전히 중지 |
| `stopReason` | string | `continue`가 false일 때 표시되는 메시지 |
| `suppressOutput` | bool | verbose 모드에서 stdout 숨김 |
| `systemMessage` | string | 사용자에게 표시되는 경고 메시지 |
| `additionalContext` | string | Claude의 대화에 추가되는 컨텍스트 |

## 훅 중복 제거 및 외부 변경

- **훅 중복 제거:** 여러 설정 위치에 정의된 동일한 훅 핸들러는 병렬로 한 번만 실행되어 중복 실행을 방지합니다.
- **외부 변경 감지:** 활성 세션 중에 훅이 외부에서 수정될 때 (예: 다른 프로세스가 설정 파일 편집) Claude Code가 경고합니다.
