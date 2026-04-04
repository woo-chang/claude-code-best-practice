# Claude Code 설정 참조

![Last Updated](https://img.shields.io/badge/Last_Updated-Apr%2002%2C%202026%209%3A24%20PM%20PKT-white?style=flat&labelColor=555) ![Version](https://img.shields.io/badge/Claude_Code-v2.1.90-blue?style=flat&labelColor=555)

Claude Code의 `settings.json` 파일에서 사용 가능한 모든 구성 옵션에 대한 종합 가이드입니다. v2.1.90 기준으로, Claude Code는 **60개 이상의 설정**과 **170개 이상의 환경 변수**를 제공합니다 (래퍼 스크립트를 피하려면 `settings.json`의 `"env"` 필드를 사용하세요).

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 목차

1. [설정 계층구조](#settings-hierarchy)
2. [핵심 구성](#core-configuration)
3. [권한](#permissions)
4. [훅](#hooks)
5. [MCP 서버](#mcp-servers)
6. [샌드박스](#sandbox)
7. [플러그인](#plugins)
8. [모델 구성](#model-configuration)
9. [디스플레이 및 UX](#display--ux)
10. [AWS 및 클라우드 자격 증명](#aws--cloud-credentials)
11. [환경 변수](#environment-variables-via-env)
12. [유용한 커맨드](#useful-commands)

---

<a id="settings-hierarchy"></a>
## 설정 계층구조

설정은 우선순위 순서대로 적용됩니다 (높음에서 낮음):

| 우선순위 | 위치 | 범위 | 공유? | 목적 |
|----------|----------|-------|---------|---------|
| 1 | 관리형 설정 | 조직 | 예 (IT 배포) | 재정의 불가능한 보안 정책 |
| 2 | 커맨드 라인 인수 | 세션 | 해당 없음 | 임시 단일 세션 재정의 |
| 3 | `.claude/settings.local.json` | 프로젝트 | 아니오 (git 제외) | 개인 프로젝트별 설정 |
| 4 | `.claude/settings.json` | 프로젝트 | 예 (커밋됨) | 팀 공유 설정 |
| 5 | `~/.claude/settings.json` | 사용자 | 해당 없음 | 전역 개인 기본값 |

**관리형 설정**은 조직이 강제하며 커맨드 라인 인수를 포함한 다른 어떤 레벨로도 재정의할 수 없습니다. 전달 방법:
- **서버 관리** 설정 (원격 전달)
- **MDM 프로파일** — `com.anthropic.claudecode`의 macOS plist
- **레지스트리 정책** — Windows `HKLM\SOFTWARE\Policies\ClaudeCode` (관리자) 및 `HKCU\SOFTWARE\Policies\ClaudeCode` (사용자 수준, 최저 정책 우선순위)
- **파일** — `managed-settings.json`과 `managed-mcp.json` (macOS: `/Library/Application Support/ClaudeCode/`, Linux/WSL: `/etc/claude-code/`, Windows: `C:\Program Files\ClaudeCode\`)
- **드롭인 디렉토리** — 독립적인 정책 조각을 위한 `managed-settings.json` 옆의 `managed-settings.d/` (v2.1.83). systemd 규칙에 따라, `managed-settings.json`이 먼저 기반으로 병합된 다음, 드롭인 디렉토리의 모든 `*.json` 파일이 알파벳순으로 정렬되어 위에 병합됩니다. 나중 파일이 스칼라 값에 대해 이전 파일을 재정의하며, 배열은 연결 및 중복 제거됩니다. 객체는 깊게 병합됩니다. `.`으로 시작하는 숨김 파일은 무시됩니다. 병합 순서를 제어하려면 숫자 접두사를 사용하세요 (예: `10-telemetry.json`, `20-security.json`)

관리형 계층 내에서 우선순위는: 서버 관리 > MDM/OS 수준 정책 > 파일 기반 (`managed-settings.d/*.json` + `managed-settings.json`) > HKCU 레지스트리 (Windows만). 하나의 관리형 소스만 사용되며, 소스는 계층 간에 병합되지 않습니다. 파일 기반 계층 내에서는 드롭인 파일과 기반 파일이 함께 병합됩니다.

> **참고:** v2.1.75부터 더 이상 사용되지 않는 Windows 폴백 경로 `C:\ProgramData\ClaudeCode\managed-settings.json`이 제거되었습니다. 대신 `C:\Program Files\ClaudeCode\managed-settings.json`을 사용하세요.

**중요**:
- `deny` 규칙은 최고 안전 우선순위를 가지며 낮은 우선순위의 allow/ask 규칙으로 재정의할 수 없습니다.
- 관리형 설정은 로컬 파일이 다른 값을 지정하더라도 로컬 동작을 잠그거나 재정의할 수 있습니다.
- 배열 설정 (예: `permissions.allow`)은 범위 전반에서 **연결 및 중복 제거**됩니다 — 모든 레벨의 항목이 결합되며, 대체되지 않습니다.

---

<a id="core-configuration"></a>
## 핵심 구성

### 일반 설정

| 키 | 유형 | 기본값 | 설명 |
|-----|------|---------|-------------|
| `$schema` | string | - | IDE 검증 및 자동완성을 위한 JSON Schema URL (예: `"https://json.schemastore.org/claude-code-settings.json"`) |
| `model` | string | `"default"` | 기본 모델 재정의. 별칭 (`sonnet`, `opus`, `haiku`) 또는 전체 모델 ID 허용 |
| `agent` | string | - | 메인 대화의 기본 에이전트 설정. 값은 `.claude/agents/`의 에이전트 이름. `--agent` CLI 플래그로도 사용 가능 |
| `language` | string | `"english"` | Claude의 선호 응답 언어 |
| `cleanupPeriodDays` | number | `30` | 이 기간보다 오래 비활성 상태인 세션은 시작 시 삭제됩니다 (최소 1). `0`으로 설정하면 유효성 검사 오류가 발생합니다. 비대화형 모드(`-p`)에서 트랜스크립트 쓰기를 비활성화하려면 `--no-session-persistence` 또는 `persistSession: false` SDK 옵션을 사용하세요 |
| `autoUpdatesChannel` | string | `"latest"` | 릴리스 채널: `"stable"` 또는 `"latest"` |
| `alwaysThinkingEnabled` | boolean | `false` | 모든 세션에 대해 기본적으로 확장 사고 활성화 |
| `skipWebFetchPreflight` | boolean | `false` | URL 가져오기 전 WebFetch 차단 목록 확인 건너뛰기 *(JSON 스키마에 있으나 공식 설정 페이지에는 없음)* |
| `availableModels` | array | - | 사용자가 `/model`, `--model`, Config 도구, 또는 `ANTHROPIC_MODEL`을 통해 선택할 수 있는 모델 제한. 기본 옵션에는 영향을 미치지 않습니다. 예: `["sonnet", "haiku"]` |
| `fastModePerSessionOptIn` | boolean | `false` | 각 세션마다 사용자가 빠른 모드에 동의하도록 요구 |
| `defaultShell` | string | `"bash"` | 입력창 `!` 커맨드의 기본 셸. `"bash"` (기본값) 또는 `"powershell"` 허용. `"powershell"` 설정은 Windows에서 대화형 `!` 커맨드를 PowerShell을 통해 라우팅합니다. `CLAUDE_CODE_USE_POWERSHELL_TOOL=1` 필요 (v2.1.84) |
| `includeGitInstructions` | boolean | `true` | 시스템 프롬프트에 git 관련 지침 포함 |
| `voiceEnabled` | boolean | - | 푸시 투 토크 음성 받아쓰기 활성화. `/voice` 실행 시 자동으로 작성됩니다. Claude.ai 계정 필요 |
| `showClearContextOnPlanAccept` | boolean | `false` | 플랜 수락 화면에 "컨텍스트 지우기" 옵션 표시. v2.1.81부터 기본적으로 숨겨진 옵션을 복원하려면 `true`로 설정하세요 |
| `disableDeepLinkRegistration` | string | - | 시작 시 Claude Code가 운영 체제에 `claude-cli://` 프로토콜 핸들러를 등록하지 않도록 하려면 `"disable"`로 설정하세요. 딥 링크를 사용하면 외부 도구가 `claude-cli://open?q=...`를 통해 미리 채워진 프롬프트로 Claude Code 세션을 열 수 있습니다. 프로토콜 핸들러 등록이 제한되거나 별도로 관리되는 환경에서 유용합니다 |
| `showThinkingSummaries` | boolean | `false` | 대화형 세션에서 확장 사고 요약 표시. 설정하지 않거나 `false`(대화형 모드의 기본값)이면, 사고 블록이 API에 의해 수정되어 접힌 스텁으로 표시됩니다. 수정은 보이는 것만 변경하며 모델이 생성하는 것은 변경하지 않습니다 — 사고 비용을 줄이려면 예산을 낮추거나 사고를 비활성화하세요. 비대화형 모드(`-p`)와 SDK 호출자는 이 설정에 관계없이 항상 요약을 받습니다 |
| `feedbackSurveyRate` | number | - | 세션 품질 설문조사가 적격 시 나타날 확률 (0–1). 엔터프라이즈 관리자는 설문조사 표시 빈도를 제어할 수 있습니다. 예: `0.05` = 적격 세션의 5% |

**예시:**
```json
{
  "model": "opus",
  "agent": "code-reviewer",
  "language": "japanese",
  "cleanupPeriodDays": 60,
  "autoUpdatesChannel": "stable",
  "alwaysThinkingEnabled": true
}
```

### 플랜 및 메모리 디렉토리

플랜과 자동 메모리 파일을 사용자 정의 위치에 저장합니다.

| 키 | 유형 | 기본값 | 설명 |
|-----|------|---------|-------------|
| `plansDirectory` | string | `~/.claude/plans` | `/plan` 출력이 저장되는 디렉토리 |
| `autoMemoryDirectory` | string | - | 자동 메모리 저장을 위한 사용자 정의 디렉토리. `~/` 확장 경로 허용. 민감한 위치로의 메모리 쓰기 리디렉션 방지를 위해 프로젝트 설정(`.claude/settings.json`)에서는 허용되지 않습니다. 정책, 로컬, 사용자 설정에서는 허용됩니다 |

**예시:**
```json
{
  "plansDirectory": "./my-plans"
}
```

**사용 사례:** 계획 아티팩트를 Claude의 내부 파일과 별도로 구성하거나 공유 팀 위치에 플랜을 보관하는 데 유용합니다.

### 워크트리 설정

`--worktree`가 git 워크트리를 생성하고 관리하는 방법을 구성합니다. 대규모 모노레포에서 디스크 사용량과 시작 시간을 줄이는 데 유용합니다.

| 키 | 유형 | 기본값 | 설명 |
|-----|------|---------|-------------|
| `worktree.symlinkDirectories` | array | `[]` | 디스크에 대규모 디렉토리 복제를 피하기 위해 각 워크트리에 메인 리포지토리에서 심볼릭 링크로 연결할 디렉토리 |
| `worktree.sparsePaths` | array | `[]` | git sparse-checkout (콘 모드)을 통해 각 워크트리에서 체크아웃할 디렉토리. 나열된 경로만 디스크에 기록됩니다 |

**예시:**
```json
{
  "worktree": {
    "symlinkDirectories": ["node_modules", ".cache"],
    "sparsePaths": ["packages/my-app", "shared/utils"]
  }
}
```

### 어트리뷰션 설정

git 커밋과 풀 리퀘스트에 대한 어트리뷰션 메시지를 사용자 정의합니다.

| 키 | 유형 | 기본값 | 설명 |
|-----|------|---------|-------------|
| `attribution.commit` | string | Co-authored-by | Git 커밋 어트리뷰션 (트레일러 지원) |
| `attribution.pr` | string | 생성된 메시지 | 풀 리퀘스트 설명 어트리뷰션 |
| `includeCoAuthoredBy` | boolean | `true` | **DEPRECATED** - 대신 `attribution`을 사용하세요 |

**예시:**
```json
{
  "attribution": {
    "commit": "Generated with AI\n\nCo-Authored-By: Claude <noreply@anthropic.com>",
    "pr": "Generated with Claude Code"
  }
}
```

**참고:** 어트리뷰션을 완전히 숨기려면 빈 문자열(`""`)로 설정하세요.

### 인증 헬퍼

동적 인증 토큰 생성을 위한 스크립트입니다.

| 키 | 유형 | 설명 |
|-----|------|-------------|
| `apiKeyHelper` | string | 인증 토큰을 출력하는 셸 스크립트 경로 (`X-Api-Key` 헤더로 전송) |
| `forceLoginMethod` | string | 로그인을 `"claudeai"` 또는 `"console"` 계정으로 제한 |
| `forceLoginOrgUUID` | string \| array | 특정 조직에 속하는 로그인을 요구합니다. 단일 UUID 문자열 (로그인 중 해당 조직을 미리 선택하기도 함) 또는 나열된 조직이 사전 선택 없이 허용되는 UUID 배열을 허용합니다. 관리형 설정에서 설정되면, 인증된 계정이 나열된 조직에 속하지 않으면 로그인이 실패합니다. 빈 배열은 닫힌 상태로 실패하며 잘못된 구성 메시지로 로그인을 차단합니다 |

**예시:**
```json
{
  "apiKeyHelper": "/bin/generate_temp_api_key.sh",
  "forceLoginMethod": "console",
  "forceLoginOrgUUID": ["xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", "yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy"]
}
```

### 회사 공지

시작 시 사용자에게 사용자 정의 공지를 표시합니다 (무작위로 순환).

| 키 | 유형 | 설명 |
|-----|------|-------------|
| `companyAnnouncements` | array | 시작 시 표시되는 문자열 배열 |

**예시:**
```json
{
  "companyAnnouncements": [
    "Acme Corp에 오신 것을 환영합니다!",
    "커밋하기 전에 테스트를 실행하세요!",
    "코딩 표준은 위키를 확인하세요"
  ]
}
```

---

<a id="permissions"></a>
## 권한

Claude가 수행할 수 있는 도구 및 작업을 제어합니다.

### 권한 구조

```json
{
  "permissions": {
    "allow": [],
    "ask": [],
    "deny": [],
    "additionalDirectories": [],
    "defaultMode": "acceptEdits",
    "disableBypassPermissionsMode": "disable"
  }
}
```

### 권한 키

| 키 | 유형 | 설명 |
|-----|------|-------------|
| `permissions.allow` | array | 프롬프트 없이 도구 사용을 허용하는 규칙 |
| `permissions.ask` | array | 사용자 확인이 필요한 규칙 |
| `permissions.deny` | array | 도구 사용을 차단하는 규칙 (최고 우선순위) |
| `permissions.additionalDirectories` | array | Claude가 접근할 수 있는 추가 디렉토리 |
| `permissions.defaultMode` | string | 기본 권한 모드. 원격 환경에서는 `acceptEdits`와 `plan`만 적용됩니다 (v2.1.70+) |
| `permissions.disableBypassPermissionsMode` | string | 바이패스 모드 활성화 방지 |
| `permissions.skipDangerousModePermissionPrompt` | boolean | `--dangerously-skip-permissions` 또는 `defaultMode: "bypassPermissions"`를 통해 바이패스 권한 모드에 진입하기 전에 표시되는 확인 프롬프트 건너뛰기. 신뢰할 수 없는 리포지토리가 프롬프트를 자동으로 바이패스하지 못하도록 프로젝트 설정(`.claude/settings.json`)에서 설정하면 무시됩니다 |
| `allowManagedPermissionRulesOnly` | boolean | **(관리형만)** 관리형 권한 규칙만 적용됩니다. 사용자/프로젝트의 `allow`, `ask`, `deny` 규칙은 무시됩니다 |
| `allow_remote_sessions` | boolean | **(관리형만)** 사용자가 원격 제어 및 웹 세션을 시작할 수 있도록 허용합니다. 기본값: `true`. 원격 세션 접근을 방지하려면 `false`로 설정하세요 *(공식 문서에 없음 — 공식 권한 페이지에는 "원격 제어 및 웹 세션 접근은 관리형 설정 키로 제어되지 않습니다."라고 명시되어 있습니다. Team 및 Enterprise 플랜에서는 관리자가 [Claude Code 관리자 설정](https://claude.ai/admin-settings/claude-code)을 통해 활성화/비활성화합니다)* |
| `autoMode` | object | [자동 모드](/en/permission-modes#eliminate-prompts-with-auto-mode) 분류기가 차단하고 허용하는 것을 사용자 정의합니다. `environment` (신뢰할 수 있는 인프라 설명), `allow` (차단 규칙의 예외), `soft_deny` (차단 규칙) — 모두 산문 문자열 배열. **공유 프로젝트 설정**에서 읽지 않습니다 (`.claude/settings.json`) (리포지토리 주입 방지). 사용자, 로컬, 관리형 설정에서 사용 가능. `allow` 또는 `soft_deny` 설정은 해당 섹션의 전체 기본 목록을 **대체**합니다. 사용자 정의 전에 기본 제공 규칙을 보려면 `claude auto-mode defaults`를 실행하세요 |
| `disableAutoMode` | string | [자동 모드](/en/permission-modes#eliminate-prompts-with-auto-mode) 활성화를 방지하려면 `"disable"`로 설정하세요. `Shift+Tab` 사이클에서 `auto`를 제거하고 시작 시 `--permission-mode auto`를 거부합니다. 모든 설정 레벨에서 설정할 수 있습니다. 사용자가 재정의할 수 없는 관리형 설정에서 가장 유용합니다 |
| `useAutoModeDuringPlan` | boolean | 자동 모드를 사용할 수 있을 때 플랜 모드가 자동 모드 의미 체계를 사용하는지 여부. 기본값: `true`. 공유 프로젝트 설정(`.claude/settings.json`)에서 읽지 않습니다. `/config`에 "플랜 중 자동 모드 사용"으로 표시됩니다 |

### 권한 모드

| 모드 | 동작 |
|------|----------|
| `"default"` | 프롬프트가 있는 표준 권한 확인 |
| `"acceptEdits"` | 묻지 않고 파일 편집 자동 수락 |
| `"askEdits"` | 모든 작업 전에 확인 *(공식 문서에 없음 — 미확인)* |
| `"dontAsk"` | `/permissions` 또는 `permissions.allow` 규칙을 통해 사전 승인되지 않으면 도구를 자동 거부 |
| `"viewOnly"` | 읽기 전용 모드, 수정 없음 *(공식 문서에 없음 — 미확인)* |
| `"bypassPermissions"` | 모든 권한 확인 건너뛰기 (위험) |
| `"auto"` | 백그라운드 분류기가 수동 프롬프트를 대체합니다 (`--enable-auto-mode`). 리서치 미리보기 — Team 플랜 + Sonnet/Opus 4.6 필요. 분류기는 읽기 전용 및 파일 편집을 자동 승인합니다. 그 외는 안전 확인을 통과시킵니다. 연속 3회 또는 총 20회 차단 후 프롬프트로 폴백합니다. `autoMode` 설정으로 구성하세요 |
| `"plan"` | 읽기 전용 탐색 모드 |

### 도구 권한 문법

| 도구 | 문법 | 예시 |
|------|--------|----------|
| `Bash` | `Bash(command pattern)` | `Bash(npm run *)`, `Bash(* install)`, `Bash(git * main)` |
| `Read` | `Read(path pattern)` | `Read(.env)`, `Read(./secrets/**)` |
| `Edit` | `Edit(path pattern)` | `Edit(src/**)`, `Edit(*.ts)` |
| `Write` | `Write(path pattern)` | `Write(*.md)`, `Write(./docs/**)` |
| `NotebookEdit` | `NotebookEdit(pattern)` | `NotebookEdit(*)` |
| `WebFetch` | `WebFetch(domain:pattern)` | `WebFetch(domain:example.com)` |
| `WebSearch` | `WebSearch` | 전역 웹 검색 |
| `Task` | `Task(agent-name)` | `Task(Explore)`, `Task(my-agent)` |
| `Agent` | `Agent(name)` | `Agent(researcher)`, `Agent(*)` — 서브에이전트 생성에 범위 지정된 권한 |
| `Skill` | `Skill(skill-name)` | `Skill(weather-fetcher)` |
| `MCP` | `mcp__server__tool` 또는 `MCP(server:tool)` | `mcp__memory__*`, `MCP(github:*)` |

**평가 순서:** 규칙은 순서대로 평가됩니다: deny 규칙 먼저, 그 다음 ask, 그 다음 allow. 첫 번째로 일치하는 규칙이 우선합니다.

**Read/Edit 경로 패턴:** `Read`, `Edit`, `Write`에 대한 권한 규칙은 네 가지 접두사 유형의 gitignore 스타일 패턴을 지원합니다:

| 접두사 | 의미 | 예시 |
|--------|---------|---------|
| `//` | 파일시스템 루트에서 절대 경로 | `Read(//Users/alice/file)` |
| `~/` | 홈 디렉토리에 상대적 | `Read(~/.zshrc)` |
| `/` | 프로젝트 루트에 상대적 | `Edit(/src/**)` |
| `./` 또는 없음 | 상대 경로 (현재 디렉토리) | `Read(.env)`, `Read(*.ts)` |

**Bash 와일드카드 참고:**
- `*`는 **어느 위치에나** 올 수 있습니다: 접두사 (`Bash(* install)`), 접미사 (`Bash(npm *)`), 또는 중간 (`Bash(git * main)`)
- **단어 경계:** `Bash(ls *)` (`*` 앞에 공백)는 `ls -la`와 일치하지만 `lsof`와는 일치하지 않습니다. `Bash(ls*)` (공백 없음)는 둘 다 일치합니다
- `Bash(*)`는 `Bash`와 동등하게 취급됩니다 (모든 bash 커맨드와 일치)
- 권한 규칙은 출력 리디렉션을 지원합니다: `Bash(python:*)`는 `python script.py > output.txt`와 일치합니다
- 레거시 `:*` 접미사 문법 (예: `Bash(npm:*)`)은 ` *`와 동등하지만 더 이상 사용되지 않습니다

**예시:**
```json
{
  "permissions": {
    "allow": [
      "Edit(*)",
      "Write(*)",
      "Bash(npm run *)",
      "Bash(git *)",
      "WebFetch(domain:*)",
      "mcp__*"
    ],
    "ask": [
      "Bash(rm *)",
      "Bash(git push *)"
    ],
    "deny": [
      "Read(.env)",
      "Read(./secrets/**)",
      "Bash(curl *)"
    ],
    "additionalDirectories": ["../shared-libs/"]
  }
}
```

---

<a id="hooks"></a>
## 훅

훅 구성 (이벤트, 속성, 매처, 종료 코드, 환경 변수, HTTP 훅)은 전용 리포지토리에서 관리됩니다:

> **[claude-code-hooks](https://github.com/shanraisshan/claude-code-hooks)** — 사운드 알림 시스템, 25개의 훅 이벤트 전체, HTTP 훅, 매처 패턴, 종료 코드, 환경 변수가 포함된 완전한 훅 참조.

훅 관련 설정 키 (`hooks`, `disableAllHooks`, `allowManagedHooksOnly`, `allowedHttpHookUrls`, `httpHookAllowedEnvVars`)는 그곳에 문서화되어 있습니다.

공식 훅 참조는 [Claude Code 훅 문서](https://code.claude.com/docs/en/hooks)를 참조하세요.

---

<a id="mcp-servers"></a>
## MCP 서버

확장된 기능을 위한 Model Context Protocol 서버를 구성합니다.

### MCP 설정

| 키 | 유형 | 범위 | 설명 |
|-----|------|-------|-------------|
| `enableAllProjectMcpServers` | boolean | 모두 | 모든 `.mcp.json` 서버 자동 승인 |
| `enabledMcpjsonServers` | array | 모두 | 특정 서버 이름 허용 목록 |
| `disabledMcpjsonServers` | array | 모두 | 특정 서버 이름 차단 목록 |
| `allowedMcpServers` | array | 관리형만 | 이름/커맨드/URL 매칭이 있는 허용 목록 |
| `deniedMcpServers` | array | 관리형만 | 매칭이 있는 차단 목록 |
| `allowManagedMcpServersOnly` | boolean | 관리형만 | 관리형 허용 목록에 명시적으로 나열된 MCP 서버만 허용 |
| `channelsEnabled` | boolean | 관리형만 | Team 및 Enterprise 사용자를 위한 [채널](https://code.claude.com/docs/en/channels) 허용. 설정하지 않거나 `false`이면, `--channels` 플래그에 관계없이 채널 메시지 전달이 차단됩니다 |
| `allowedChannelPlugins` | array | 관리형만 | 메시지를 푸시할 수 있는 채널 플러그인 허용 목록. 설정 시 기본 Anthropic 허용 목록을 대체합니다. 미정의 = 기본값으로 폴백, 빈 배열 = 모든 채널 플러그인 차단. `channelsEnabled: true` 필요. 각 항목은 `marketplace`와 `plugin` 필드를 가진 객체입니다 (v2.1.84) |

### MCP 서버 매칭 (관리형 설정)

```json
{
  "allowedMcpServers": [
    { "serverName": "github" },
    { "serverCommand": "npx @modelcontextprotocol/*" },
    { "serverUrl": "https://mcp.company.com/*" }
  ],
  "deniedMcpServers": [
    { "serverName": "dangerous-server" }
  ]
}
```

**예시:**
```json
{
  "enableAllProjectMcpServers": true,
  "enabledMcpjsonServers": ["memory", "github", "filesystem"],
  "disabledMcpjsonServers": ["experimental-server"]
}
```

---

<a id="sandbox"></a>
## 샌드박스

보안을 위한 bash 커맨드 샌드박싱을 구성합니다.

### 샌드박스 설정

| 키 | 유형 | 기본값 | 설명 |
|-----|------|---------|-------------|
| `sandbox.enabled` | boolean | `false` | bash 샌드박싱 활성화 |
| `sandbox.failIfUnavailable` | boolean | `false` | 샌드박스가 활성화되었지만 시작할 수 없을 때 샌드박스 없이 실행하는 대신 오류로 종료합니다. 엄격한 샌드박싱이 필요한 엔터프라이즈 정책에 유용합니다 (v2.1.83) |
| `sandbox.autoAllowBashIfSandboxed` | boolean | `true` | 샌드박스 시 bash 자동 승인 |
| `sandbox.excludedCommands` | array | `[]` | 샌드박스 외부에서 실행할 커맨드 |
| `sandbox.allowUnsandboxedCommands` | boolean | `true` | `dangerouslyDisableSandbox` 허용 |
| `sandbox.ignoreViolations` | object | `{}` | 경로 배열에 대한 커맨드 패턴 맵 — 위반 경고 억제 *(JSON 스키마에 있으나 공식 설정 페이지에는 없음)* |
| `sandbox.enableWeakerNestedSandbox` | boolean | `false` | Docker를 위한 더 약한 샌드박스 (보안 감소) |
| `sandbox.network.allowUnixSockets` | array | `[]` | 샌드박스에서 접근 가능한 특정 Unix 소켓 경로 |
| `sandbox.network.allowAllUnixSockets` | boolean | `false` | 모든 Unix 소켓 허용 (allowUnixSockets 재정의) |
| `sandbox.network.allowLocalBinding` | boolean | `false` | localhost 포트 바인딩 허용 (macOS) |
| `sandbox.network.allowedDomains` | array | `[]` | 샌드박스 네트워크 도메인 허용 목록 |
| `sandbox.network.deniedDomains` | array | `[]` | 샌드박스 네트워크 도메인 차단 목록 *(공식 문서에 없음 — 미확인)* |
| `sandbox.network.httpProxyPort` | number | - | HTTP 프록시 포트 1-65535 (사용자 정의 프록시) |
| `sandbox.network.socksProxyPort` | number | - | SOCKS5 프록시 포트 1-65535 (사용자 정의 프록시) |
| `sandbox.network.allowManagedDomainsOnly` | boolean | `false` | 관리형 허용 목록의 도메인만 허용 (관리형 설정) |
| `sandbox.filesystem.allowWrite` | array | `[]` | 샌드박스 커맨드가 쓸 수 있는 추가 경로. 배열은 모든 설정 범위에서 병합됩니다. `Edit(...)` 허용 권한 규칙의 경로와도 병합됩니다. 접두사: `/` (절대), `~/` (홈), `./` 또는 없음 (프로젝트 설정의 경우 프로젝트 상대, 사용자 설정의 경우 `~/.claude` 상대). 이전 `//` 절대 경로 접두사도 작동합니다. **참고:** 이것은 `//`를 절대 경로에, `/`를 프로젝트 상대 경로에 사용하는 [Read/Edit 권한 규칙](#tool-permission-syntax)과 다릅니다 |
| `sandbox.filesystem.denyWrite` | array | `[]` | 샌드박스 커맨드가 쓸 수 없는 경로. 배열은 모든 설정 범위에서 병합됩니다. `Edit(...)` deny 권한 규칙의 경로와도 병합됩니다. `allowWrite`와 동일한 경로 접두사 규칙 |
| `sandbox.filesystem.denyRead` | array | `[]` | 샌드박스 커맨드가 읽을 수 없는 경로. 배열은 모든 설정 범위에서 병합됩니다. `Read(...)` deny 권한 규칙의 경로와도 병합됩니다. `allowWrite`와 동일한 경로 접두사 규칙 |
| `sandbox.filesystem.allowRead` | array | `[]` | `denyRead` 영역 내에서 읽기 접근을 다시 허용할 경로. `denyRead`보다 우선합니다. 배열은 모든 설정 범위에서 병합됩니다. `allowWrite`와 동일한 경로 접두사 규칙 |
| `sandbox.filesystem.allowManagedReadPathsOnly` | boolean | `false` | **(관리형만)** 관리형 설정의 `allowRead` 경로만 적용됩니다. 사용자, 프로젝트, 로컬 설정의 `allowRead` 항목은 무시됩니다 |
| `sandbox.enableWeakerNetworkIsolation` | boolean | `false` | (macOS만) 시스템 TLS 신뢰(`com.apple.trustd.agent`) 접근 허용. 보안 감소 |

**예시:**
```json
{
  "sandbox": {
    "enabled": true,
    "autoAllowBashIfSandboxed": true,
    "excludedCommands": ["git", "docker", "gh"],
    "allowUnsandboxedCommands": false,
    "network": {
      "allowUnixSockets": ["/var/run/docker.sock"],
      "allowLocalBinding": true
    }
  }
}
```

---

<a id="plugins"></a>
## 플러그인

Claude Code 플러그인과 마켓플레이스를 구성합니다.

### 플러그인 설정

| 키 | 유형 | 범위 | 설명 |
|-----|------|-------|-------------|
| `enabledPlugins` | object | 모두 | 특정 플러그인 활성화/비활성화 |
| `extraKnownMarketplaces` | object | 프로젝트 | 사용자 정의 플러그인 마켓플레이스 추가 (`.claude/settings.json`을 통한 팀 공유) |
| `strictKnownMarketplaces` | array | 관리형만 | 허용된 마켓플레이스 허용 목록 |
| `skippedMarketplaces` | array | 모두 | 사용자가 설치를 거부한 마켓플레이스 *(JSON 스키마에 있으나 공식 설정 페이지에는 없음)* |
| `skippedPlugins` | array | 모두 | 사용자가 설치를 거부한 플러그인 *(JSON 스키마에 있으나 공식 설정 페이지에는 없음)* |
| `pluginConfigs` | object | 모두 | 플러그인별 MCP 서버 구성 (`plugin@marketplace`로 키가 지정됨) *(JSON 스키마에 있으나 공식 설정 페이지에는 없음)* |
| `blockedMarketplaces` | array | 관리형만 | 특정 플러그인 마켓플레이스 차단 |
| `pluginTrustMessage` | string | 관리형만 | 사용자에게 플러그인 신뢰를 요청할 때 표시되는 사용자 정의 메시지 |

**마켓플레이스 소스 유형:** `github`, `git`, `directory`, `hostPattern`, `settings`, `url` *(공식 문서에 없음 — 미확인)*, `npm` *(공식 문서에 없음 — 미확인)*, `file` *(공식 문서에 없음 — 미확인)*. 호스팅된 마켓플레이스 리포지토리 설정 없이 소수의 플러그인을 인라인으로 선언하려면 `source: 'settings'`를 사용하세요.

**예시:**
```json
{
  "enabledPlugins": {
    "formatter@acme-tools": true,
    "deployer@acme-tools": true,
    "experimental@acme-tools": false
  },
  "extraKnownMarketplaces": {
    "acme-tools": {
      "source": {
        "source": "github",
        "repo": "acme-corp/claude-plugins"
      }
    },
    "inline-tools": {
      "source": {
        "source": "settings",
        "name": "inline-tools",
        "plugins": [
          {
            "name": "code-formatter",
            "source": { "source": "github", "repo": "acme-corp/code-formatter" }
          }
        ]
      }
    }
  }
}
```

---

<a id="model-configuration"></a>
## 모델 구성

### 모델 별칭

| 별칭 | 설명 |
|-------|-------------|
| `"default"` | 계정 유형에 권장 |
| `"sonnet"` | 최신 Sonnet 모델 (Claude Sonnet 4.6) |
| `"opus"` | 최신 Opus 모델 (Claude Opus 4.6) |
| `"haiku"` | 빠른 Haiku 모델 |
| `"sonnet[1m]"` | 1M 토큰 컨텍스트의 Sonnet |
| `"opus[1m]"` | 1M 토큰 컨텍스트의 Opus (v2.1.75부터 Max, Team, Enterprise의 기본값) |
| `"opusplan"` | 플랜에는 Opus, 실행에는 Sonnet |

**예시:**
```json
{
  "model": "opus"
}
```

### 모델 재정의

Bedrock, Vertex, Foundry 배포를 위해 Anthropic 모델 ID를 공급자별 모델 ID에 매핑합니다.

| 키 | 유형 | 기본값 | 설명 |
|-----|------|---------|-------------|
| `effortLevel` | string | - | 세션 간에 노력 수준 유지. `"low"`, `"medium"`, `"high"` 허용. `/effort low`, `/effort medium`, 또는 `/effort high` 실행 시 자동으로 작성됩니다. Opus 4.6 및 Sonnet 4.6에서 지원됩니다 |
| `modelOverrides` | object | - | 모델 피커 항목을 공급자별 ID에 매핑합니다 (예: Bedrock 추론 프로파일 ARN). 각 키는 모델 피커 항목 이름이고, 각 값은 공급자 모델 ID입니다 |

**예시:**
```json
{
  "modelOverrides": {
    "claude-opus-4-6": "arn:aws:bedrock:us-east-1:123456789:inference-profile/anthropic.claude-opus-4-6-v1:0",
    "claude-sonnet-4-6": "arn:aws:bedrock:us-east-1:123456789:inference-profile/anthropic.claude-sonnet-4-6-v1:0"
  }
}
```

### 노력 수준

`/model` 커맨드는 모델이 응답당 적용하는 추론 양을 조정하는 **노력 수준** 컨트롤을 제공합니다. `/model` UI에서 ← → 화살표 키를 사용하여 노력 수준을 순환합니다.

| 노력 수준 | 설명 |
|-------------|-------------|
| High | 전체 추론 깊이, 복잡한 작업에 최적 |
| Medium (기본값) | 균형 잡힌 추론, 일상적인 작업에 적합 |
| Low | 최소 추론, 가장 빠른 응답 |

**사용 방법:**
1. `/effort low`, `/effort medium`, 또는 `/effort high`를 실행하여 직접 설정 (v2.1.76+)
2. 또는 `/model` → 모델 선택 → **← →** 화살표 키로 조정
3. 설정은 `settings.json`의 `effortLevel` 키를 통해 지속됩니다

**참고:** 노력 수준은 Max 및 Team 플랜에서 Opus 4.6 및 Sonnet 4.6에서 사용할 수 있습니다. 기본값은 v2.1.68에서 High에서 Medium으로 변경되었습니다. v2.1.75부터, Opus 4.6의 1M 컨텍스트 윈도우가 Max, Team, Enterprise 플랜에서 기본적으로 사용 가능합니다.

### 모델 환경 변수

`env` 키를 통해 구성합니다:

```json
{
  "env": {
    "ANTHROPIC_MODEL": "sonnet",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "custom-haiku-model",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "custom-sonnet-model",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "custom-opus-model",
    "CLAUDE_CODE_SUBAGENT_MODEL": "haiku",
    "MAX_THINKING_TOKENS": "10000"
  }
}
```

---

<a id="display--ux"></a>
## 디스플레이 및 UX

### 디스플레이 설정

| 키 | 유형 | 기본값 | 설명 |
|-----|------|---------|-------------|
| `statusLine` | object | - | 사용자 정의 상태 줄 구성 |
| `outputStyle` | string | `"default"` | 출력 스타일 (예: `"Explanatory"`) |
| `spinnerTipsEnabled` | boolean | `true` | 대기 중 팁 표시 |
| `spinnerVerbs` | object | - | `mode` ("append" 또는 "replace")와 `verbs` 배열이 있는 사용자 정의 스피너 동사 |
| `spinnerTipsOverride` | object | - | `tips` (문자열 배열)와 선택적 `excludeDefault` (boolean)가 있는 사용자 정의 스피너 팁 |
| `respectGitignore` | boolean | `true` | 파일 피커에서 .gitignore 준수 |
| `prefersReducedMotion` | boolean | `false` | UI의 애니메이션과 모션 효과 감소 |
| `fileSuggestion` | object | - | 사용자 정의 파일 제안 커맨드 (아래 파일 제안 구성 참조) |

### 전역 구성 설정 (`~/.claude.json`)

이 디스플레이 설정은 `settings.json`이 아닌 `~/.claude.json`에 저장됩니다. `settings.json`에 추가하면 스키마 유효성 검사 오류가 발생합니다.

| 키 | 유형 | 기본값 | 설명 |
|-----|------|---------|-------------|
| `autoConnectIde` | boolean | `false` | 외부 터미널에서 Claude Code 시작 시 실행 중인 IDE에 자동 연결. VS Code 또는 JetBrains 터미널 외부에서 실행할 때 `/config`에 **외부 터미널에서 IDE 자동 연결**로 표시됩니다 |
| `autoInstallIdeExtension` | boolean | `true` | VS Code 터미널에서 실행할 때 Claude Code IDE 확장 자동 설치. `/config`에 **IDE 확장 자동 설치**로 표시됩니다. `CLAUDE_CODE_IDE_SKIP_AUTO_INSTALL` 환경 변수를 통해서도 비활성화할 수 있습니다 |
| `editorMode` | string | `"normal"` | 입력 프롬프트의 키 바인딩 모드: `"normal"` 또는 `"vim"`. `/vim` 실행 시 자동으로 작성됩니다. `/config`에 **키 바인딩 모드**로 표시됩니다 |
| `showTurnDuration` | boolean | `true` | 응답 후 턴 지속 시간 메시지 표시 (예: "1분 6초 동안 처리"). 변경하려면 `~/.claude.json`을 직접 편집하세요 |
| `terminalProgressBarEnabled` | boolean | `true` | 지원되는 터미널(ConEmu, Ghostty 1.2.0+, iTerm2 3.6.6+)에서 터미널 진행 바 표시. `/config`에 **터미널 진행 바**로 표시됩니다 |
| `teammateMode` | string | `"auto"` | [에이전트 팀](https://code.claude.com/docs/en/agent-teams) 팀원이 표시되는 방식: `"auto"` (tmux 또는 iTerm2에서 분할 패널 선택, 그렇지 않으면 인프로세스), `"in-process"`, 또는 `"tmux"`. [디스플레이 모드 선택](https://code.claude.com/docs/en/agent-teams#choose-a-display-mode) 참조 |

### 상태 줄 구성

```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh",
    "padding": 0
  }
}
```

**상태 줄 입력 필드:**

상태 줄 커맨드는 표준 입력에서 다음의 주요 필드가 있는 JSON 객체를 받습니다:

| 필드 | 설명 |
|-------|-------------|
| `workspace.added_dirs` | `/add-dir`을 통해 추가된 디렉토리 |
| `context_window.used_percentage` | 컨텍스트 윈도우 사용 비율 |
| `context_window.remaining_percentage` | 컨텍스트 윈도우 남은 비율 |
| `current_usage` | 현재 컨텍스트 윈도우 토큰 수 |
| `exceeds_200k_tokens` | 컨텍스트가 200k 토큰을 초과하는지 여부 |
| `rate_limits.five_hour.used_percentage` | 5시간 레이트 리밋 사용 비율 (v2.1.80+) |
| `rate_limits.five_hour.resets_at` | 5시간 레이트 리밋 리셋 타임스탬프 |
| `rate_limits.seven_day.used_percentage` | 7일 레이트 리밋 사용 비율 |
| `rate_limits.seven_day.resets_at` | 7일 레이트 리밋 리셋 타임스탬프 |

### 파일 제안 구성

파일 제안 스크립트는 표준 입력에서 JSON 객체를 받고 (예: `{"query": "src/comp"}`) 최대 15개의 파일 경로 (줄당 하나)를 출력해야 합니다.

```json
{
  "fileSuggestion": {
    "type": "command",
    "command": "~/.claude/file-suggestion.sh"
  },
  "respectGitignore": true
}
```

**예시:**
```json
{
  "statusLine": {
    "type": "command",
    "command": "git branch --show-current 2>/dev/null || echo 'no-branch'"
  },
  "spinnerTipsEnabled": true,
  "spinnerVerbs": {
    "mode": "replace",
    "verbs": ["Cooking", "Brewing", "Crafting", "Conjuring"]
  },
  "spinnerTipsOverride": {
    "tips": ["~50% 컨텍스트에서 /compact 사용", "복잡한 작업에는 플랜 모드로 시작"],
    "excludeDefault": true
  }
}
```

---

<a id="aws--cloud-credentials"></a>
## AWS 및 클라우드 자격 증명

### AWS 설정

| 키 | 유형 | 설명 |
|-----|------|-------------|
| `awsAuthRefresh` | string | AWS 인증을 갱신하는 스크립트 (`.aws` 디렉토리 수정) |
| `awsCredentialExport` | string | AWS 자격 증명이 포함된 JSON을 출력하는 스크립트 |

**예시:**
```json
{
  "awsAuthRefresh": "aws sso login --profile myprofile",
  "awsCredentialExport": "/bin/generate_aws_grant.sh"
}
```

### OpenTelemetry

| 키 | 유형 | 설명 |
|-----|------|-------------|
| `otelHeadersHelper` | string | 동적 OpenTelemetry 헤더를 생성하는 스크립트 |

**예시:**
```json
{
  "otelHeadersHelper": "/bin/generate_otel_headers.sh"
}
```

---

<a id="environment-variables-via-env"></a>
## 환경 변수 (`env`를 통해)

모든 Claude Code 세션에 대한 환경 변수를 설정합니다.

```json
{
  "env": {
    "ANTHROPIC_API_KEY": "...",
    "NODE_ENV": "development",
    "DEBUG": "true"
  }
}
```

### 공통 환경 변수

| 변수 | 설명 |
|----------|-------------|
| `ANTHROPIC_API_KEY` | 인증을 위한 API 키 |
| `ANTHROPIC_AUTH_TOKEN` | OAuth 토큰 |
| `CLAUDE_CODE_OAUTH_TOKEN` | Claude.ai 인증을 위한 OAuth 접근 토큰. SDK 및 자동화 환경을 위한 `/login`의 대안. 키체인에 저장된 자격 증명보다 우선합니다 |
| `CLAUDE_CODE_OAUTH_REFRESH_TOKEN` | Claude.ai 인증을 위한 OAuth 갱신 토큰. 설정되면, `claude auth login`이 브라우저 열기 대신 이 토큰을 직접 교환합니다. `CLAUDE_CODE_OAUTH_SCOPES` 필요 |
| `CLAUDE_CODE_OAUTH_SCOPES` | 갱신 토큰이 발급된 공백으로 구분된 OAuth 범위 (예: `"user:profile user:inference user:sessions:claude_code"`). `CLAUDE_CODE_OAUTH_REFRESH_TOKEN`이 설정된 경우 필요 |
| `ANTHROPIC_BASE_URL` | 사용자 정의 API 엔드포인트 |
| `ANTHROPIC_BEDROCK_BASE_URL` | Bedrock 엔드포인트 URL 재정의 |
| `ANTHROPIC_VERTEX_BASE_URL` | Vertex AI 엔드포인트 URL 재정의 |
| `ANTHROPIC_BETAS` | 쉼표로 구분된 Anthropic 베타 헤더 값 |
| `ANTHROPIC_VERTEX_PROJECT_ID` | Vertex AI를 위한 GCP 프로젝트 ID |
| `ANTHROPIC_CUSTOM_MODEL_OPTION` | `/model` 피커에 사용자 정의 항목으로 추가할 모델 ID. 기본 제공 별칭을 대체하지 않고 비표준 또는 게이트웨이별 모델을 선택 가능하게 합니다 |
| `ANTHROPIC_CUSTOM_MODEL_OPTION_NAME` | `/model` 피커의 사용자 정의 모델 항목 표시 이름. 설정하지 않으면 모델 ID가 기본값 |
| `ANTHROPIC_CUSTOM_MODEL_OPTION_DESCRIPTION` | `/model` 피커의 사용자 정의 모델 항목 표시 설명. 설정하지 않으면 `Custom model (<model-id>)`가 기본값 |
| `ANTHROPIC_MODEL` | 사용할 모델 이름. 별칭(`sonnet`, `opus`, `haiku`) 또는 전체 모델 ID 허용. `model` 설정을 재정의 |
| `ANTHROPIC_DEFAULT_HAIKU_MODEL` | Haiku 모델 별칭을 사용자 정의 모델 ID로 재정의 (예: 서드파티 배포) |
| `ANTHROPIC_DEFAULT_HAIKU_MODEL_NAME` | Bedrock/Vertex/Foundry에서 고정 모델을 사용할 때 `/model` 피커의 Haiku 항목 레이블 사용자 정의. 기본값은 모델 ID |
| `ANTHROPIC_DEFAULT_HAIKU_MODEL_DESCRIPTION` | `/model` 피커의 Haiku 항목 설명 사용자 정의. 기본값은 `Custom model (<model-id>)` |
| `ANTHROPIC_DEFAULT_HAIKU_MODEL_SUPPORTED_CAPABILITIES` | 고정된 Haiku 모델의 기능 감지 재정의. 쉼표로 구분된 값 (예: `effort,thinking`). 고정된 모델이 자동 감지로 확인할 수 없는 기능을 지원할 때 필요 |
| `CLAUDECODE` | Claude Code가 생성하는 셸 환경 (Bash 도구, tmux 세션)에서 `1`로 설정됩니다. 훅이나 상태 줄 커맨드에서는 설정되지 않습니다. 스크립트가 Claude Code 셸 내에서 실행 중인지 감지하는 데 사용합니다 |
| `CLAUDE_CODE_SKIP_FAST_MODE_NETWORK_ERRORS` | 조직 상태 확인이 네트워크 오류로 실패할 때 빠른 모드를 허용하려면 `1`로 설정하세요. 회사 프록시가 상태 엔드포인트를 차단할 때 유용합니다 |
| `CLAUDE_CODE_USE_BEDROCK` | AWS Bedrock 사용 (`1`로 활성화) |
| `CLAUDE_CODE_USE_VERTEX` | Google Vertex AI 사용 (`1`로 활성화) |
| `CLAUDE_CODE_USE_FOUNDRY` | Microsoft Foundry 사용 (`1`로 활성화) |
| `CLAUDE_CODE_USE_POWERSHELL_TOOL` | Windows에서 PowerShell 도구를 활성화하려면 `1`로 설정합니다 (옵트인 미리보기). 활성화되면 Claude가 Git Bash를 통한 라우팅 대신 네이티브로 PowerShell 커맨드를 실행할 수 있습니다. WSL이 아닌 네이티브 Windows만 지원합니다 (v2.1.84) |
| `CLAUDE_CODE_ENABLE_TELEMETRY` | 원격 측정 활성화/비활성화 (`0` 또는 `1`) |
| `DISABLE_ERROR_REPORTING` | 오류 보고 비활성화 (`1`로 비활성화) |
| `DISABLE_TELEMETRY` | 원격 측정 비활성화 (`1`로 비활성화) |
| `MCP_TIMEOUT` | MCP 시작 타임아웃 (밀리초) |
| `MAX_MCP_OUTPUT_TOKENS` | 최대 MCP 출력 토큰 (기본값: 25000). 출력이 10,000 토큰을 초과하면 경고 표시 |
| `API_TIMEOUT_MS` | API 요청 타임아웃 (밀리초, 기본값: 600000) |
| `BASH_MAX_TIMEOUT_MS` | Bash 커맨드 타임아웃 |
| `BASH_MAX_OUTPUT_LENGTH` | 최대 bash 출력 길이 |
| `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` | 자동 컴팩트 임계값 비율 (1-100). 기본값은 약 95%. 조기에 압축을 트리거하려면 낮게 설정하세요 (예: `50`). 95% 이상의 값은 효과가 없습니다. 현재 사용량을 모니터링하려면 `/context`를 사용하세요. 예: `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=50 claude` |
| `CLAUDE_BASH_MAINTAIN_PROJECT_WORKING_DIR` | bash 호출 간 cwd 유지 (`1`로 활성화) |
| `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS` | 백그라운드 작업 비활성화 (`1`로 비활성화) |
| `ENABLE_TOOL_SEARCH` | MCP 도구 검색 임계값 (예: `auto:5`) |
| `DISABLE_PROMPT_CACHING` | 모든 프롬프트 캐싱 비활성화 (`1`로 비활성화) |
| `DISABLE_PROMPT_CACHING_HAIKU` | Haiku 프롬프트 캐싱 비활성화 |
| `DISABLE_PROMPT_CACHING_SONNET` | Sonnet 프롬프트 캐싱 비활성화 |
| `DISABLE_PROMPT_CACHING_OPUS` | Opus 프롬프트 캐싱 비활성화 |
| `ENABLE_PROMPT_CACHING_1H_BEDROCK` | Bedrock에서 1시간 캐시 TTL 요청 (`1`로 활성화) |
| `CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS` | 실험적 베타 기능 비활성화 (`1`로 비활성화) |
| `CLAUDE_CODE_SHELL` | 자동 셸 감지 재정의 |
| `CLAUDE_CODE_FILE_READ_MAX_OUTPUT_TOKENS` | 기본 파일 읽기 토큰 제한 재정의 |
| `CLAUDE_CODE_GLOB_HIDDEN` | Glob 결과에 숨김 파일 포함 (`1`로 활성화) |
| `CLAUDE_CODE_GLOB_NO_IGNORE` | Glob이 `.gitignore` 패턴을 무시하도록 설정 (`1`로 활성화) |
| `CLAUDE_CODE_GLOB_TIMEOUT_SECONDS` | Glob 파일 검색 타임아웃 (초) |
| `CLAUDE_CODE_ENABLE_TASKS` | 비대화형 모드(`-p` 플래그)에서 작업 추적을 활성화하려면 `true`로 설정. 작업은 대화형 모드에서 기본적으로 활성화됩니다 |
| `CLAUDE_CODE_SIMPLE` | 최소한의 시스템 프롬프트와 Bash, 파일 읽기, 파일 편집 도구만으로 실행하려면 `1`로 설정 |
| `CLAUDE_CODE_EXIT_AFTER_STOP_DELAY` | 유휴 기간 후 SDK 모드 자동 종료 (밀리초) |
| `CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING` | 적응형 사고 비활성화 (`1`로 비활성화) |
| `CLAUDE_CODE_DISABLE_THINKING` | 확장 사고 강제 비활성화 (`1`로 비활성화) |
| `DISABLE_INTERLEAVED_THINKING` | 인터리브드 사고 베타 헤더 전송 방지 (`1`로 비활성화) |
| `CLAUDE_CODE_DISABLE_1M_CONTEXT` | 1M 토큰 컨텍스트 윈도우 비활성화 (`1`로 비활성화) |
| `CLAUDE_CODE_ACCOUNT_UUID` | 인증을 위한 계정 UUID 재정의 |
| `CLAUDE_CODE_DISABLE_GIT_INSTRUCTIONS` | git 관련 시스템 프롬프트 지침 비활성화 |
| `CLAUDE_CODE_NEW_INIT` | `/init`이 대화형 설정 흐름을 실행하도록 하려면 `true`로 설정. 코드베이스 탐색 전에 생성할 파일 (CLAUDE.md, 스킬, 훅)을 선택하도록 요청합니다. 없으면 `/init`이 CLAUDE.md를 자동으로 생성합니다 |
| `CLAUDE_CODE_PLUGIN_SEED_DIR` | Unix에서는 `:`로, Windows에서는 `;`로 구분된 하나 이상의 읽기 전용 플러그인 시드 디렉토리 경로. 미리 채워진 플러그인을 컨테이너 이미지에 번들화합니다. Claude Code는 시작 시 이 디렉토리의 마켓플레이스를 등록하고 다시 클론하지 않고 미리 캐시된 플러그인을 사용합니다 |
| `ENABLE_CLAUDEAI_MCP_SERVERS` | Claude.ai MCP 서버 활성화 |
| `CLAUDE_CODE_EFFORT_LEVEL` | 노력 수준 설정: `low`, `medium`, `high`, `max` (Opus 4.6만), 또는 `auto` (모델 기본값 사용). `/effort`와 `effortLevel` 설정보다 우선합니다 |
| `CLAUDE_CODE_MAX_TURNS` | 중지 전 최대 에이전틱 턴 *(공식 문서에 없음 — 미확인)* |
| `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` | `DISABLE_AUTOUPDATER`, `DISABLE_FEEDBACK_COMMAND`, `DISABLE_ERROR_REPORTING`, `DISABLE_TELEMETRY`를 설정하는 것과 동등 |
| `CLAUDE_CODE_SKIP_SETTINGS_SETUP` | 첫 실행 설정 흐름 건너뛰기 *(공식 문서에 없음 — 미확인)* |
| `CLAUDE_CODE_PROMPT_CACHING_ENABLED` | 프롬프트 캐싱 동작 재정의 *(공식 문서에 없음 — 미확인)* |
| `CLAUDE_CODE_DISABLE_TOOLS` | 비활성화할 도구의 쉼표로 구분된 목록 *(공식 문서에 없음 — 미확인)* |
| `CLAUDE_CODE_DISABLE_MCP` | 모든 MCP 서버 비활성화 (`1`로 비활성화) *(공식 문서에 없음 — 미확인)* |
| `CLAUDE_CODE_MAX_OUTPUT_TOKENS` | 응답당 최대 출력 토큰. 기본값: 32,000 (v2.1.77부터 Opus 4.6은 64,000). 상한: 64,000 (v2.1.77부터 Opus 4.6 및 Sonnet 4.6은 128,000) |
| `CLAUDE_CODE_DISABLE_FAST_MODE` | 빠른 모드 완전 비활성화 (`1`로 비활성화) |
| `CLAUDE_CODE_DISABLE_NONSTREAMING_FALLBACK` | 스트리밍 요청이 중간에 실패할 때 비스트리밍 폴백을 비활성화하려면 `1`로 설정. 스트리밍 오류가 재시도 레이어로 전파됩니다. 프록시나 게이트웨이가 폴백으로 인해 도구 실행이 중복될 때 유용합니다 (v2.1.83) |
| `CLAUDE_ENABLE_STREAM_WATCHDOG` | 멈춘 스트림 중단 (`1`로 활성화) |
| `CLAUDE_CODE_ENABLE_FINE_GRAINED_TOOL_STREAMING` | 세밀한 도구 스트리밍 활성화 (`1`로 활성화) |
| `CLAUDE_CODE_DISABLE_AUTO_MEMORY` | 자동 메모리 비활성화 (`1`로 비활성화) |
| `CLAUDE_CODE_DISABLE_FILE_CHECKPOINTING` | `/rewind`를 위한 파일 체크포인팅 비활성화 (`1`로 비활성화) |
| `CLAUDE_CODE_DISABLE_ATTACHMENTS` | 첨부 파일 처리 비활성화 (`1`로 비활성화) |
| `CLAUDE_CODE_DISABLE_CLAUDE_MDS` | CLAUDE.md 파일 로딩 방지 (`1`로 비활성화) |
| `CLAUDE_CODE_RESUME_INTERRUPTED_TURN` | 이전 세션이 턴 중간에 종료된 경우 자동 재개 (`1`로 활성화) |
| `CLAUDE_CODE_USER_EMAIL` | 인증을 위한 사용자 이메일 동기적 제공 |
| `CLAUDE_CODE_ORGANIZATION_UUID` | 인증을 위한 조직 UUID 동기적 제공 |
| `CLAUDE_CONFIG_DIR` | 사용자 정의 구성 디렉토리 (기본 `~/.claude` 재정의) |
| `CLAUDE_CODE_TMPDIR` | 내부 임시 파일에 사용되는 임시 디렉토리 재정의. Claude Code가 이 경로에 `/claude/`를 추가합니다. 기본값: Unix/macOS에서 `/tmp`, Windows에서 `os.tmpdir()` |
| `ANTHROPIC_CUSTOM_HEADERS` | API 요청을 위한 사용자 정의 헤더 (`Name: Value` 형식, 여러 헤더는 줄바꿈으로 구분) |
| `ANTHROPIC_FOUNDRY_API_KEY` | Microsoft Foundry 인증을 위한 API 키 |
| `ANTHROPIC_FOUNDRY_BASE_URL` | Foundry 리소스의 기본 URL |
| `ANTHROPIC_FOUNDRY_RESOURCE` | Foundry 리소스 이름 |
| `AWS_BEARER_TOKEN_BEDROCK` | 인증을 위한 Bedrock API 키 |
| `ANTHROPIC_SMALL_FAST_MODEL` | **DEPRECATED** — 대신 `ANTHROPIC_DEFAULT_HAIKU_MODEL`을 사용하세요 |
| `ANTHROPIC_SMALL_FAST_MODEL_AWS_REGION` | 더 이상 사용되지 않는 Haiku 클래스 모델 재정의를 위한 AWS 리전 |
| `CLAUDE_CODE_SHELL_PREFIX` | bash 커맨드 앞에 추가되는 커맨드 접두사 |
| `BASH_DEFAULT_TIMEOUT_MS` | 기본 bash 커맨드 타임아웃 (밀리초) |
| `CLAUDE_CODE_SKIP_BEDROCK_AUTH` | Bedrock의 AWS 인증 건너뛰기 (`1`로 건너뛰기) |
| `CLAUDE_CODE_SKIP_FOUNDRY_AUTH` | Foundry의 Azure 인증 건너뛰기 (`1`로 건너뛰기) |
| `CLAUDE_CODE_SKIP_VERTEX_AUTH` | Vertex의 Google 인증 건너뛰기 (`1`로 건너뛰기) |
| `CLAUDE_CODE_PROXY_RESOLVES_HOSTS` | 프록시가 DNS 해석을 수행하도록 허용 |
| `CLAUDE_CODE_API_KEY_HELPER_TTL_MS` | `apiKeyHelper`의 자격 증명 갱신 간격 (밀리초) |
| `CLAUDE_CODE_CLIENT_CERT` | mTLS를 위한 클라이언트 인증서 경로 |
| `CLAUDE_CODE_CLIENT_KEY` | mTLS를 위한 클라이언트 개인 키 경로 |
| `CLAUDE_CODE_CLIENT_KEY_PASSPHRASE` | 암호화된 mTLS 키의 암호 |
| `CLAUDE_CODE_PLUGIN_GIT_TIMEOUT_MS` | 플러그인 마켓플레이스 git 클론 타임아웃 (밀리초, 기본값: 120000) |
| `CLAUDE_CODE_PLUGIN_CACHE_DIR` | 플러그인 루트 디렉토리 재정의 |
| `CLAUDE_CODE_DISABLE_OFFICIAL_MARKETPLACE_AUTOINSTALL` | 공식 마켓플레이스 자동 추가 건너뛰기 (`1`로 비활성화) |
| `CLAUDE_CODE_SYNC_PLUGIN_INSTALL` | 첫 쿼리 전에 플러그인 설치 완료 대기 (`1`로 활성화) |
| `CLAUDE_CODE_SYNC_PLUGIN_INSTALL_TIMEOUT_MS` | 동기식 플러그인 설치 타임아웃 (밀리초) |
| `CLAUDE_CODE_PLUGIN_KEEP_MARKETPLACE_ON_FAILURE` | `git pull` 업데이트가 실패할 때 삭제하고 다시 클론하는 대신 마켓플레이스 캐시를 디스크에 유지하려면 `1`로 설정 *(v2.1.90 변경로그에 있으나 공식 환경 변수 페이지에는 아직 없음)* |
| `CLAUDE_CODE_HIDE_ACCOUNT_INFO` | UI에서 이메일/조직 정보 숨기기 *(공식 문서에 없음 — 미확인)* |
| `CLAUDE_CODE_DISABLE_CRON` | 예약된/크론 작업 비활성화 (`1`로 비활성화) |
| `DISABLE_INSTALLATION_CHECKS` | 설치 경고 비활성화 |
| `DISABLE_FEEDBACK_COMMAND` | `/feedback` 커맨드 비활성화. 이전 이름 `DISABLE_BUG_COMMAND`도 허용됩니다 |
| `DISABLE_DOCTOR_COMMAND` | `/doctor` 커맨드 숨기기 (`1`로 비활성화) |
| `DISABLE_LOGIN_COMMAND` | `/login` 커맨드 숨기기 (`1`로 비활성화) |
| `DISABLE_LOGOUT_COMMAND` | `/logout` 커맨드 숨기기 (`1`로 비활성화) |
| `DISABLE_UPGRADE_COMMAND` | `/upgrade` 커맨드 숨기기 (`1`로 비활성화) |
| `DISABLE_EXTRA_USAGE_COMMAND` | `/extra-usage` 커맨드 숨기기 (`1`로 비활성화) |
| `DISABLE_INSTALL_GITHUB_APP_COMMAND` | `/install-github-app` 커맨드 숨기기 (`1`로 비활성화) |
| `DISABLE_NON_ESSENTIAL_MODEL_CALLS` | 맛깔스러운 텍스트 및 비필수 모델 호출 비활성화 *(공식 문서에 없음 — 미확인)* |
| `CLAUDE_CODE_DEBUG_LOGS_DIR` | 디버그 로그 파일 디렉토리 경로 재정의 |
| `CLAUDE_CODE_DEBUG_LOG_LEVEL` | 최소 디버그 로그 레벨 |
| `CLAUDE_AUTO_BACKGROUND_TASKS` | 장시간 작업 자동 백그라운드 강제 활성화 (`1`로 활성화) |
| `CLAUDE_CODE_DISABLE_LEGACY_MODEL_REMAP` | Opus 4.0/4.1을 최신 모델로 리매핑 방지 (`1`로 비활성화) |
| `FALLBACK_FOR_ALL_PRIMARY_MODELS` | 기본 모델만이 아닌 모든 기본 모델에 대해 폴백 모델 트리거 (`1`로 활성화) |
| `CLAUDE_CODE_GIT_BASH_PATH` | Windows Git Bash 실행 파일 경로 (시작 시만) |
| `DISABLE_COST_WARNINGS` | 비용 경고 메시지 비활성화 |
| `CLAUDE_CODE_SUBAGENT_MODEL` | 서브에이전트에 대한 모델 재정의 (예: `haiku`, `sonnet`) |
| `CLAUDE_CODE_SUBPROCESS_ENV_SCRUB` | 서브프로세스 환경 (Bash 도구, 훅, MCP stdio 서버)에서 Anthropic 및 클라우드 공급자 자격 증명을 제거하려면 `1`로 설정합니다. 서브프로세스가 API 키를 상속해서는 안 되는 경우 심층 방어를 위해 사용합니다 (v2.1.83) |
| `CLAUDE_CODE_MAX_RETRIES` | API 요청 재시도 횟수 재정의 (기본값: 10) |
| `CLAUDE_CODE_MAX_TOOL_USE_CONCURRENCY` | 최대 병렬 읽기 전용 도구 (기본값: 10) |
| `CLAUDE_AGENT_SDK_DISABLE_BUILTIN_AGENTS` | SDK 모드에서 기본 제공 서브에이전트 유형 비활성화 (`1`로 비활성화) |
| `CLAUDE_AGENT_SDK_MCP_NO_PREFIX` | SDK 모드에서 MCP 도구의 `mcp__<server>__` 접두사 건너뛰기 (`1`로 활성화) |
| `MCP_CONNECTION_NONBLOCKING` | `-p` 모드에서 MCP 연결 대기를 완전히 건너뛰려면 `true`로 설정합니다. `--mcp-config` 서버 연결을 가장 느린 서버에서 차단하는 대신 5초로 제한합니다 *(v2.1.89 변경로그에 있으나 공식 환경 변수 페이지에는 아직 없음)* |
| `CLAUDE_CODE_SESSIONEND_HOOKS_TIMEOUT_MS` | SessionEnd 훅 타임아웃 (밀리초, 하드 1.5초 제한 대체) |
| `CLAUDE_CODE_DISABLE_FEEDBACK_SURVEY` | 피드백 설문조사 프롬프트 비활성화 (`1`로 비활성화) |
| `CLAUDE_CODE_DISABLE_TERMINAL_TITLE` | 터미널 제목 업데이트 비활성화 (`1`로 비활성화) |
| `CLAUDE_CODE_NO_FLICKER` | 깜빡임 없는 대체 화면 렌더링을 활성화하려면 `1`로 설정. 전체 화면 다시 그리기 중 시각적 깜빡임을 제거합니다 (v2.1.88) |
| `CLAUDE_CODE_SCROLL_SPEED` | 전체 화면 렌더링을 위한 마우스 휠 스크롤 배수. 더 빠른 스크롤을 위해 증가시키거나, 더 세밀한 제어를 위해 감소시키세요 |
| `CLAUDE_CODE_DISABLE_MOUSE` | 전체 화면 렌더링에서 마우스 추적을 비활성화하려면 `1`로 설정. 마우스 이벤트가 터미널 멀티플렉서나 접근성 도구를 방해할 때 유용합니다 |
| `CLAUDE_CODE_ACCESSIBILITY` | 스크린 리더 및 접근성 도구를 위해 네이티브 터미널 커서를 표시하려면 `1`로 설정 |
| `CLAUDE_CODE_SYNTAX_HIGHLIGHT` | diff 출력에서 구문 강조를 비활성화하려면 `0`으로 설정 |
| `CLAUDE_CODE_IDE_SKIP_AUTO_INSTALL` | 자동 IDE 확장 설치 건너뛰기 (`1`로 건너뛰기) |
| `CLAUDE_CODE_AUTO_CONNECT_IDE` | 자동 IDE 연결 동작 재정의 |
| `CLAUDE_CODE_IDE_HOST_OVERRIDE` | 연결을 위한 IDE 호스트 주소 재정의 |
| `CLAUDE_CODE_IDE_SKIP_VALID_CHECK` | IDE 잠금 파일 유효성 검사 건너뛰기 (`1`로 건너뛰기) |
| `CLAUDE_CODE_OTEL_HEADERS_HELPER_DEBOUNCE_MS` | OTel 헤더 헬퍼 스크립트의 디바운스 간격 (밀리초) |
| `CLAUDE_CODE_OTEL_FLUSH_TIMEOUT_MS` | OpenTelemetry 플러시 타임아웃 (밀리초) |
| `CLAUDE_CODE_OTEL_SHUTDOWN_TIMEOUT_MS` | OpenTelemetry 종료 타임아웃 (밀리초) |
| `CLAUDE_STREAM_IDLE_TIMEOUT_MS` | 스트리밍 유휴 감시기가 멈춘 연결을 닫기 전의 타임아웃 (밀리초). 기본값: `90000` (90초). 장시간 실행 도구나 느린 네트워크가 조기 타임아웃 오류를 유발하는 경우 증가시키세요 |
| `OTEL_LOG_TOOL_DETAILS` | OpenTelemetry 이벤트에 `tool_parameters`를 포함하려면 `1`로 설정. 프라이버시를 위해 기본적으로 생략됩니다 *(v2.1.85 변경로그에 있으나 공식 환경 변수 페이지에는 아직 없음)* |
| `CLAUDE_CODE_MCP_SERVER_NAME` | `headersHelper` 스크립트에 환경 변수로 전달되는 MCP 서버 이름으로, 서버별 인증 헤더를 생성할 수 있습니다 *(v2.1.85 변경로그에 있으나 공식 환경 변수 페이지에는 아직 없음)* |
| `CLAUDE_CODE_MCP_SERVER_URL` | `CLAUDE_CODE_MCP_SERVER_NAME`과 함께 `headersHelper` 스크립트에 환경 변수로 전달되는 MCP 서버 URL *(v2.1.85 변경로그에 있으나 공식 환경 변수 페이지에는 아직 없음)* |
| `ANTHROPIC_DEFAULT_OPUS_MODEL` | Opus 모델 별칭 재정의 (예: `claude-opus-4-6[1m]`) |
| `ANTHROPIC_DEFAULT_OPUS_MODEL_NAME` | Bedrock/Vertex/Foundry에서 고정 모델을 사용할 때 `/model` 피커의 Opus 항목 레이블 사용자 정의. 기본값은 모델 ID |
| `ANTHROPIC_DEFAULT_OPUS_MODEL_DESCRIPTION` | `/model` 피커의 Opus 항목 설명 사용자 정의. 기본값은 `Custom model (<model-id>)` |
| `ANTHROPIC_DEFAULT_OPUS_MODEL_SUPPORTED_CAPABILITIES` | 고정된 Opus 모델의 기능 감지 재정의. 쉼표로 구분된 값 (예: `effort,thinking`). 고정된 모델이 자동 감지로 확인할 수 없는 기능을 지원할 때 필요 |
| `ANTHROPIC_DEFAULT_SONNET_MODEL` | Sonnet 모델 별칭 재정의 (예: `claude-sonnet-4-6`) |
| `ANTHROPIC_DEFAULT_SONNET_MODEL_NAME` | Bedrock/Vertex/Foundry에서 고정 모델을 사용할 때 `/model` 피커의 Sonnet 항목 레이블 사용자 정의. 기본값은 모델 ID |
| `ANTHROPIC_DEFAULT_SONNET_MODEL_DESCRIPTION` | `/model` 피커의 Sonnet 항목 설명 사용자 정의. 기본값은 `Custom model (<model-id>)` |
| `ANTHROPIC_DEFAULT_SONNET_MODEL_SUPPORTED_CAPABILITIES` | 고정된 Sonnet 모델의 기능 감지 재정의. 쉼표로 구분된 값 (예: `effort,thinking`). 고정된 모델이 자동 감지로 확인할 수 없는 기능을 지원할 때 필요 |
| `MAX_THINKING_TOKENS` | 응답당 최대 확장 사고 토큰 |
| `CLAUDE_CODE_AUTO_COMPACT_WINDOW` | 자동 압축 계산에 사용되는 컨텍스트 용량 (토큰). 기본값은 모델의 컨텍스트 윈도우 (표준 200K, 확장 컨텍스트 모델은 1M). 1M 모델에서 500K로 처리하려면 낮은 값을 사용하세요 (예: `500000`). 실제 컨텍스트 윈도우로 제한됩니다. `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE`는 이 값의 비율로 적용됩니다. 설정하면 상태 줄의 `used_percentage`와 압축 임계값이 분리됩니다 |
| `DISABLE_AUTO_COMPACT` | 자동 컨텍스트 압축 비활성화 (`1`로 비활성화). 수동 `/compact`는 여전히 작동합니다 |
| `DISABLE_COMPACT` | 자동 및 수동 모두 압축 비활성화 (`1`로 비활성화) |
| `CLAUDE_CODE_ENABLE_PROMPT_SUGGESTION` | 프롬프트 제안 활성화 |
| `CLAUDE_CODE_PLAN_MODE_REQUIRED` | 세션에 플랜 모드 요구 |
| `CLAUDE_CODE_TEAM_NAME` | 에이전트 팀의 팀 이름 |
| `CLAUDE_CODE_TASK_LIST_ID` | 작업 통합을 위한 작업 목록 ID |
| `CLAUDE_ENV_FILE` | 사용자 정의 환경 파일 경로 |
| `FORCE_AUTOUPDATE_PLUGINS` | 플러그인 자동 업데이트 강제 (`1`로 활성화) |
| `HTTP_PROXY` | 네트워크 요청을 위한 HTTP 프록시 URL |
| `HTTPS_PROXY` | 네트워크 요청을 위한 HTTPS 프록시 URL |
| `NO_PROXY` | 프록시를 우회하는 쉼표로 구분된 호스트 목록 |
| `MCP_TOOL_TIMEOUT` | MCP 도구 실행 타임아웃 (밀리초) |
| `MCP_CLIENT_SECRET` | MCP OAuth 클라이언트 시크릿 |
| `MCP_OAUTH_CALLBACK_PORT` | MCP OAuth 콜백 포트 |
| `IS_DEMO` | 데모 모드 활성화 |
| `SLASH_COMMAND_TOOL_CHAR_BUDGET` | 슬래시 커맨드 도구 출력의 문자 예산 |
| `VERTEX_REGION_CLAUDE_3_5_HAIKU` | Claude 3.5 Haiku의 Vertex AI 리전 재정의 |
| `VERTEX_REGION_CLAUDE_3_7_SONNET` | Claude 3.7 Sonnet의 Vertex AI 리전 재정의 |
| `VERTEX_REGION_CLAUDE_4_0_OPUS` | Claude 4.0 Opus의 Vertex AI 리전 재정의 |
| `VERTEX_REGION_CLAUDE_4_0_SONNET` | Claude 4.0 Sonnet의 Vertex AI 리전 재정의 |
| `VERTEX_REGION_CLAUDE_4_1_OPUS` | Claude 4.1 Opus의 Vertex AI 리전 재정의 |

---

<a id="useful-commands"></a>
## 유용한 커맨드

| 커맨드 | 설명 |
|---------|-------------|
| `/model` | 모델 전환 및 Opus 4.6 노력 수준 조정 |
| `/effort` | 노력 수준 직접 설정: `low`, `medium`, `high` (v2.1.76+) |
| `/config` | 대화형 구성 UI |
| `/memory` | 모든 메모리 파일 보기/편집 |
| `/agents` | 서브에이전트 관리 |
| `/mcp` | MCP 서버 관리 |
| `/hooks` | 구성된 훅 보기 |
| `/plugin` | 플러그인 관리 |
| `/keybindings` | 사용자 정의 키보드 단축키 구성 |
| `/skills` | 스킬 보기 및 관리 |
| `/permissions` | 권한 규칙 보기 및 관리 |
| `--doctor` | 구성 문제 진단 |
| `--debug` | 훅 실행 세부 정보가 있는 디버그 모드 |

---

## 빠른 참조: 완전한 예시

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "model": "sonnet",
  "agent": "code-reviewer",
  "language": "english",
  "cleanupPeriodDays": 30,
  "autoUpdatesChannel": "stable",
  "alwaysThinkingEnabled": true,
  "showThinkingSummaries": true,
  "includeGitInstructions": true,
  "defaultShell": "bash",
  "plansDirectory": "./plans",
  "effortLevel": "medium",

  "worktree": {
    "symlinkDirectories": ["node_modules"],
    "sparsePaths": ["packages/my-app", "shared/utils"]
  },

  "modelOverrides": {
    "claude-opus-4-6": "arn:aws:bedrock:us-east-1:123456789:inference-profile/anthropic.claude-opus-4-6-v1:0"
  },

  "autoMode": {
    "environment": [
      "Source control: github.example.com/acme-corp and all repos under it",
      "Trusted internal domains: *.internal.example.com"
    ]
  },

  "permissions": {
    "allow": [
      "Edit(*)",
      "Write(*)",
      "Bash(npm run *)",
      "Bash(git *)",
      "WebFetch(domain:*)",
      "mcp__*",
      "Agent(*)"
    ],
    "deny": [
      "Read(.env)",
      "Read(./secrets/**)"
    ],
    "additionalDirectories": ["../shared/"],
    "defaultMode": "acceptEdits"
  },

  "enableAllProjectMcpServers": true,

  "sandbox": {
    "enabled": true,
    "excludedCommands": ["git", "docker"],
    "filesystem": {
      "denyRead": ["./secrets/"],
      "denyWrite": ["./.env"]
    }
  },

  "attribution": {
    "commit": "Generated with Claude Code",
    "pr": ""
  },

  "statusLine": {
    "type": "command",
    "command": "git branch --show-current"
  },

  "spinnerTipsEnabled": true,
  "spinnerTipsOverride": {
    "tips": ["Custom tip 1", "Custom tip 2"],
    "excludeDefault": false
  },
  "prefersReducedMotion": false,

  "env": {
    "NODE_ENV": "development",
    "CLAUDE_CODE_EFFORT_LEVEL": "medium"
  }
}
```

---

## 소스

- [Claude Code 설정 문서](https://code.claude.com/docs/en/settings)
- [Claude Code 설정 JSON 스키마](https://json.schemastore.org/claude-code-settings.json)
- [Claude Code 변경로그](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
- [Claude Code GitHub 설정 예시](https://github.com/feiskyer/claude-code-settings)
- [Shipyard - Claude Code CLI 치트시트](https://shipyard.build/blog/claude-code-cheat-sheet/)
- [Claude Code 환경 변수 참조](https://code.claude.com/docs/en/env-vars)
- [Claude Code 권한 참조](https://code.claude.com/docs/en/permissions)
