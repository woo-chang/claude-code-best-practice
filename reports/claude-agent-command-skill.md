# 에이전트 vs 커맨드 vs 스킬 — 언제 무엇을 사용할까

Claude Code의 세 가지 확장 메커니즘 비교: 서브에이전트, 커맨드, 스킬.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

![슬래시 메뉴에 time-skill, time-command, time-agent가 표시된 화면](assets/agent-command-skill-1.jpg)

---

## 한눈에 보기

| | 에이전트 | 커맨드 | 스킬 |
|---|---|---|---|
| **위치** | `.claude/agents/<name>.md` | `.claude/commands/<name>.md` | `.claude/skills/<name>/SKILL.md` |
| **컨텍스트** | 별도의 서브에이전트 프로세스 | 인라인 (메인 대화) | 인라인 (메인 대화) |
| **사용자 호출 가능** | `/` 메뉴 없음 — Claude 또는 Agent 도구를 통해 호출 | 예 — `/command-name` | 예 — `/skill-name` (`user-invocable: false`가 아니면) |
| **Claude가 자동 호출** | 예 — `description` 필드를 통해 | 아니오 | 예 — `description` 필드를 통해 (`disable-model-invocation: true`가 아니면) |
| **인수 허용** | `prompt` 매개변수를 통해 | `$ARGUMENTS`, `$0`, `$1` | `$ARGUMENTS`, `$0`, `$1` |
| **동적 컨텍스트 주입** | 아니오 | 예 — `` !`command` `` | 예 — `` !`command` `` |
| **자체 컨텍스트 윈도우** | 예 — 격리됨 | 아니오 — 메인 공유 | 아니오 — 메인 공유 (`context: fork`가 아니면) |
| **모델 재정의** | `model:` 프론트매터 | `model:` 프론트매터 | `model:` 프론트매터 |
| **도구 제한** | `tools:` / `disallowedTools:` | `allowed-tools:` | `allowed-tools:` |
| **훅** | `hooks:` 프론트매터 | — | `hooks:` 프론트매터 |
| **메모리** | `memory:` 프론트매터 (user/project/local) | — | — |
| **스킬 사전 로드 가능** | 예 — `skills:` 프론트매터 | — | — |
| **MCP 서버** | `mcpServers:` 프론트매터 | — | — |

---

## 각각의 사용 시기

### 에이전트 사용 시기:

- 작업이 **자율적이고 다단계** — 에이전트가 지속적인 안내 없이 탐색, 결정, 행동해야 하는 경우
- **컨텍스트 격리** 필요 — 작업이 메인 대화 윈도우를 오염시켜서는 안 되는 경우
- 에이전트가 세션 전반에 걸쳐 **영속적 메모리** 필요 (예: 패턴을 학습하는 코드 리뷰어)
- 메인 컨텍스트를 혼잡하게 만들지 않고 스킬을 통해 **도메인 지식을 사전 로드**하려는 경우
- 작업이 **백그라운드 실행** 또는 **git 워크트리**에서 실행되는 것이 이점인 경우
- **도구 제한** 또는 **다른 권한 모드** 필요 (예: `acceptEdits`, `plan`)

**예시**: `weather-agent` — 사전 로드된 `weather-fetcher` 스킬을 사용하여 자율적으로 날씨 데이터를 가져오고, 제한된 도구로 별도의 컨텍스트에서 실행됩니다.

### 커맨드 사용 시기:

- **사용자가 시작하는 진입점** 필요 — 사용자가 명시적으로 트리거하는 워크플로우
- 워크플로우가 다른 에이전트나 스킬을 **오케스트레이션**하는 경우
- **컨텍스트를 간결하게 유지**하려는 경우 — 커맨드 내용은 사용자가 트리거할 때까지 세션 컨텍스트에 주입되지 않음

**예시**: `weather-orchestrator` — 사용자가 트리거하고, C/F 선호도를 묻고, 에이전트를 호출한 다음 SVG 스킬을 호출합니다.

### 스킬 사용 시기:

- 사용자 의도에 따라 **Claude가 자동 호출**하기를 원하는 경우 — 스킬 설명이 의미론적 매칭을 위해 세션 컨텍스트에 주입됨
- 작업이 여러 곳에서 호출할 수 있는 **재사용 가능한 절차** (커맨드, 에이전트, 또는 Claude 자체)
- **에이전트 사전 로드** 필요 — 시작 시 특정 에이전트에 도메인 지식 주입

**예시**: `weather-svg-creator` — 사용자가 날씨 카드를 요청할 때 Claude가 자동으로 호출. 커맨드에서도 호출 가능합니다.

---

## 커맨드 → 에이전트 → 스킬 아키텍처

이 리포지토리는 레이어드 오케스트레이션 패턴을 시연합니다:

```
사용자가 /command 트리거
    ↓
커맨드가 워크플로우 오케스트레이션
    ↓
커맨드가 에이전트 호출 (별도 컨텍스트, 자율)
    ↓
에이전트가 사전 로드된 스킬 사용 (도메인 지식)
    ↓
커맨드가 스킬 호출 (인라인, 출력 생성용)
```

**구체적인 예시** — 날씨 시스템:

```
/weather-orchestrator (커맨드 — 진입점, C/F 질문)
    ↓
weather-agent (에이전트 — 자율적으로 온도 가져오기)
    ├── weather-fetcher (에이전트 스킬 — 사전 로드된 API 지침)
    ↓
weather-svg-creator (스킬 — 인라인으로 SVG 생성)
```

---

## 프론트매터 비교

### 에이전트 프론트매터

```yaml
---
name: my-agent
description: Use this agent PROACTIVELY when...
tools: Read, Write, Edit, Bash
model: sonnet
maxTurns: 10
permissionMode: acceptEdits
memory: user
skills:
  - my-skill
---
```

### 커맨드 프론트매터

```yaml
---
description: Do something useful
argument-hint: [issue-number]
allowed-tools: Read, Edit, Bash(gh *)
model: sonnet
---
```

### 스킬 프론트매터

```yaml
---
name: my-skill
description: Do something when the user asks for...
argument-hint: [file-path]
disable-model-invocation: false
user-invocable: true
allowed-tools: Read, Grep, Glob
model: sonnet
context: fork
agent: general-purpose
---
```

---

## 주요 차이점

### 자동 호출

| 메커니즘 | Claude가 자동 호출 가능? | 방지 방법 |
|-----------|------------------------|----------------|
| 에이전트 | 예 — `description`을 통해 ("PROACTIVELY"를 사용하여 권장) | 설명 제거 또는 완화 |
| 커맨드 | 아니오 — 항상 사용자가 `/`를 통해 시작 | 해당 없음 |
| 스킬 | 예 — `description`을 통해 | `disable-model-invocation: true` 설정 |

### `/` 메뉴 가시성

| 메커니즘 | `/` 메뉴에 표시? | 숨기는 방법 |
|-----------|---------------------|-------------|
| 에이전트 | 아니오 | 해당 없음 |
| 커맨드 | 예 — 항상 | 숨길 수 없음 |
| 스킬 | 예 — 기본값 | `user-invocable: false` 설정 |

### 컨텍스트 격리

| 메커니즘 | 자체 컨텍스트에서 실행? | 구성 방법 |
|-----------|---------------------|-----------------|
| 에이전트 | 항상 | 기본 동작 |
| 커맨드 | 절대 아님 | 해당 없음 |
| 스킬 | 선택적 | `context: fork` 설정 |

---

## 실제 예시: "현재 시간이 몇 시입니까?"

이 리포지토리에는 동일한 작업 — PKT에서 현재 시간 표시 — 에 대해 세 가지 메커니즘이 모두 정의되어 있습니다. 사용자가 **"현재 시간이 몇 시입니까?"** 라고 타이핑했을 때 어떻게 되는지:

| 메커니즘 | 실행 여부 | 이유 |
|-----------|--------------|---------------|
| `time-command` | 아니오 | 커맨드는 **절대 자동 호출되지 않습니다**. 사용자가 실행하려면 명시적으로 `/time-command`를 타이핑해야 합니다. 커맨드에는 자동 검색 경로가 없습니다 — 엄격하게 사용자가 시작합니다. |
| `time-agent` | **예** (가능) | 에이전트의 `description`은 *"파키스탄 표준시로 현재 시간을 표시하려면 이 에이전트를 사용하세요"*라고 합니다. Claude가 이것을 사용자의 의도와 일치시켜 Agent 도구를 통해 생성할 수 있습니다. 하지만 에이전트는 **별도의 컨텍스트 윈도우**에서 실행되어 이 단순한 작업에 불필요하게 무거워집니다. |
| `time-skill` | **예** (가장 가능성 높음) | 스킬의 `description`은 *"파키스탄 표준시 (PKT, UTC+5)로 현재 시간을 표시합니다. 사용자가 현재 시간, 파키스탄 시간, 또는 PKT를 요청할 때 사용하세요."*라고 합니다. Claude가 이를 일치시켜 Skill 도구를 통해 호출합니다. **인라인**으로 실행되어 컨텍스트 오버헤드가 없어 가장 효율적인 매칭입니다. |

### 우선순위 해석

여러 메커니즘이 동일한 의도와 일치할 때 Claude는 요청을 충족하는 **가장 가벼운 옵션**을 선호합니다:

```
1. 스킬 (인라인, 컨텍스트 오버헤드 없음)     ← 선호
2. 에이전트 (별도 컨텍스트, 자율)    ← 스킬이 없거나 작업이 복잡할 경우 사용
3. 커맨드 (절대 아님 — 명시적 / 필요)   ← 사용자가 /time-command를 타이핑할 경우만
```

### 스킬에 `disable-model-invocation: true`가 설정된 경우?

그러면 Claude가 스킬을 자동 호출할 **수 없습니다**. 에이전트가 유일한 자동 호출 가능 옵션이 되므로, Claude가 대신 `time-agent`를 생성하게 됩니다 — 한 줄짜리 bash 커맨드를 위한 별도의 컨텍스트 윈도우 비용을 감수하고.

### 스킬과 에이전트 모두 자동 호출이 비활성화된 경우?

그러면 **아무것도 자동으로 실행되지 않습니다**. Claude는 자체 일반 지식으로 폴백하여 `TZ='Asia/Karachi' date`를 직접 실행할 것입니다 — 확장 메커니즘 없이. 사용자가 하나를 사용하려면 명시적으로 `/time-command` 또는 `/time-skill`을 타이핑해야 합니다.

![사용자가 "현재 시간이 몇 시입니까?"라고 물었을 때 Claude가 time-skill을 자동 호출](assets/agent-command-skill-2.png)

---

## 소스

- [Claude Code 스킬 — 문서](https://code.claude.com/docs/en/skills)
- [Claude Code 서브에이전트 — 문서](https://code.claude.com/docs/en/sub-agents)
- [Claude Code 슬래시 커맨드 — 문서](https://code.claude.com/docs/en/slash-commands)
- [스킬 모범 사례](../best-practice/claude-skills.md)
- [커맨드 모범 사례](../best-practice/claude-commands.md)
- [서브에이전트 모범 사례](../best-practice/claude-subagents.md)
