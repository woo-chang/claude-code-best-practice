# Claude Code: 전역 vs 프로젝트 수준 기능

어떤 Claude Code 기능이 전역 전용 (`~/.claude/`)인지, 전역과 프로젝트 수준 (`.claude/`) 모두에 해당하는지에 대한 종합 비교.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 목차

1. [개요](#overview)
2. [전역 전용 기능](#global-only-features)
3. [이중 범위 기능](#dual-scope-features)
4. [설정 우선순위](#settings-precedence)
5. [디렉토리 구조 비교](#directory-structure-comparison)
6. [작업 시스템](#tasks-system)
7. [에이전트 팀](#agent-teams)
8. [설계 원칙](#design-principles)
9. [소스](#sources)

---

<a id="overview"></a>
## 개요

Claude Code는 **범위 계층구조**를 사용하며, 일부 기능은 전역 (`~/.claude/`)과 프로젝트 (`.claude/`) 수준 모두에 존재하고, 일부는 전역 전용입니다. 설계 원칙: *개인 상태* 또는 *프로젝트 간 조율*에 해당하는 것은 전역에 있습니다. *팀이 공유 가능한 프로젝트 구성*에 해당하는 것은 프로젝트 수준에 있을 수 있습니다.

- `~/.claude/`는 **사용자 수준 홈** (전역, 모든 프로젝트)
- 리포지토리 내 `.claude/`는 **프로젝트 수준 홈** (해당 프로젝트에 범위 지정)

---

<a id="global-only-features"></a>
## 전역 전용 기능

다음은 **오직** `~/.claude/` 아래에 있으며 프로젝트에 범위 지정할 수 없습니다:

| 기능 | 위치 | 목적 |
|---------|----------|---------|
| **작업** | `~/.claude/tasks/` | 세션과 에이전트 전반의 영속적 작업 목록 |
| **에이전트 팀** | `~/.claude/teams/` | 다중 에이전트 조율 구성 (실험적, 2026년 2월) |
| **자동 메모리** | `~/.claude/projects/<hash>/memory/` | 프로젝트별 Claude의 자체 작성 학습 (개인, 절대 공유되지 않음) |
| **자격 증명 및 OAuth** | 시스템 키체인 + `~/.claude.json` | API 키, OAuth 토큰 (절대 프로젝트 파일에 없음) |
| **키 바인딩** | `~/.claude/keybindings.json` | 사용자 정의 키보드 단축키 |
| **MCP 사용자 서버** | `~/.claude.json` (`mcpServers` 키) | 모든 프로젝트 전반의 개인 MCP 서버 |
| **기본 설정/캐시** | `~/.claude.json` | 테마, 모델, 출력 스타일, 세션 상태 |

---

<a id="dual-scope-features"></a>
## 이중 범위 기능

다음은 두 수준 모두에 존재하며, **프로젝트 수준이 전역보다 우선**합니다:

| 기능 | 전역 (`~/.claude/`) | 프로젝트 (`.claude/`) | 우선순위 |
|---------|----------------------|---------------------|------------|
| **CLAUDE.md** | `~/.claude/CLAUDE.md` | `./CLAUDE.md` 또는 `.claude/CLAUDE.md` | 프로젝트가 전역 재정의 |
| **설정** | `~/.claude/settings.json` | `.claude/settings.json` + `.claude/settings.local.json` | 프로젝트 > 전역 |
| **규칙** | `~/.claude/rules/*.md` | `.claude/rules/*.md` | 프로젝트 재정의 |
| **에이전트/서브에이전트** | `~/.claude/agents/*.md` | `.claude/agents/*.md` | 프로젝트 재정의 |
| **커맨드** | `~/.claude/commands/*.md` | `.claude/commands/*.md` | 둘 다 사용 가능 |
| **스킬** | `~/.claude/skills/` | `.claude/skills/` | 둘 다 사용 가능 |
| **훅** | `~/.claude/hooks/` | `.claude/hooks/` | 둘 다 실행 |
| **MCP 서버** | `~/.claude.json` (사용자 범위) | `.mcp.json` (프로젝트 범위) | 세 가지 범위: 로컬 > 프로젝트 > 사용자 |

---

<a id="settings-precedence"></a>
## 설정 우선순위

사용자 쓰기 가능 설정은 이 재정의 순서로 적용됩니다 (높음에서 낮음):

| 우선순위 | 위치 | 범위 | 버전 관리 | 목적 |
|----------|----------|-------|-----------------|---------|
| 1 | 커맨드 라인 플래그 | 세션 | 해당 없음 | 단일 세션 재정의 |
| 2 | `.claude/settings.local.json` | 프로젝트 | 아니오 (git 제외) | 개인 프로젝트별 |
| 3 | `.claude/settings.json` | 프로젝트 | 예 (커밋됨) | 팀 공유 설정 |
| 4 | `~/.claude/settings.local.json` | 사용자 | 해당 없음 | 개인 전역 재정의 |
| 5 | `~/.claude/settings.json` | 사용자 | 해당 없음 | 전역 개인 설정 |

정책 레이어: `managed-settings.json`은 조직이 강제하며 로컬 파일로 재정의할 수 없습니다.

**중요**: `deny` 규칙은 최고 안전 우선순위를 가지며 낮은 우선순위의 allow/ask 규칙으로 재정의할 수 없습니다.

---

<a id="directory-structure-comparison"></a>
## 디렉토리 구조 비교

### 전역 범위 (`~/.claude/`)

```
~/.claude/
├── settings.json              # 사용자 수준 설정 (모든 프로젝트)
├── settings.local.json        # 개인 재정의
├── CLAUDE.md                  # 사용자 메모리 (모든 프로젝트)
├── agents/                    # 사용자 서브에이전트 (모든 프로젝트에서 사용 가능)
│   └── *.md
├── rules/                     # 사용자 수준 모듈식 규칙
│   └── *.md
├── commands/                  # 사용자 수준 커맨드
│   └── *.md
├── skills/                    # 사용자 수준 스킬
│   └── */SKILL.md
├── tasks/                     # 전역 전용: 작업 목록
│   └── {task-list-id}/
├── teams/                     # 전역 전용: 에이전트 팀 구성
│   └── {team-name}/
│       └── config.json
├── projects/                  # 전역 전용: 프로젝트별 자동 메모리
│   └── {project-hash}/
│       └── memory/
│           ├── MEMORY.md
│           └── *.md
├── keybindings.json           # 전역 전용: 키보드 단축키
└── hooks/                     # 사용자 수준 훅
    ├── scripts/
    └── config/

~/.claude.json                 # 전역 전용: MCP 서버, OAuth, 기본 설정, 캐시
```

### 프로젝트 범위 (`.claude/`)

```
.claude/
├── settings.json              # 팀 공유 설정
├── settings.local.json        # 개인 프로젝트 재정의 (git 제외)
├── CLAUDE.md                  # 프로젝트 메모리 (./CLAUDE.md의 대안)
├── agents/                    # 프로젝트 서브에이전트
│   └── *.md
├── rules/                     # 프로젝트 수준 모듈식 규칙
│   └── *.md
├── commands/                  # 사용자 정의 슬래시 커맨드
│   └── *.md
├── skills/                    # 사용자 정의 스킬
│   └── {skill-name}/
│       ├── SKILL.md
│       └── supporting-files/
├── hooks/                     # 프로젝트 수준 훅
│   ├── scripts/
│   └── config/
└── plugins/                   # 설치된 플러그인

.mcp.json                      # 프로젝트 범위 MCP 서버 (리포지토리 루트)
```

---

<a id="tasks-system"></a>
## 작업 시스템

더 이상 사용되지 않는 TodoWrite 시스템을 대체하며 **Claude Code v2.1.16** (2026년 1월 22일)에서 도입되었습니다.

### 저장소

작업은 로컬 파일 시스템의 `~/.claude/tasks/`에 저장됩니다 (클라우드 데이터베이스가 아님). 이로 인해 작업 상태를 감사, 버전 관리, 충돌 복구할 수 있습니다.

### 도구

| 도구 | 목적 |
|------|---------|
| **TaskCreate** | `subject`, `description`, `activeForm`으로 새 작업 생성 |
| **TaskGet** | ID로 특정 작업의 전체 세부 정보 검색 |
| **TaskUpdate** | 상태 변경, 소유자 설정, 의존성 추가, 또는 삭제 |
| **TaskList** | 현재 상태와 함께 모든 작업 목록 |

### 작업 수명 주기

```
pending  →  in_progress  →  completed
```

### 의존성 관리

작업은 `addBlockedBy`/`addBlocks`를 통해 다른 작업을 차단할 수 있어, 조기 실행을 방지하는 의존성 그래프를 만듭니다.

### 다중 세션 협업

```bash
CLAUDE_CODE_TASK_LIST_ID=my-project-tasks claude
```

동일한 ID를 공유하는 모든 세션이 작업 업데이트를 실시간으로 볼 수 있어 병렬 작업 흐름과 세션 재개를 가능하게 합니다.

### 이전 할 일과의 주요 차이점

| 기능 | 이전 할 일 | 새 작업 |
|---------|-----------|-----------|
| 범위 | 단일 세션 | 세션 간, 에이전트 간 |
| 의존성 | 없음 | 전체 의존성 그래프 |
| 저장소 | 인메모리만 | 파일 시스템 (`~/.claude/tasks/`) |
| 영속성 | 세션 종료 시 손실 | 재시작 및 충돌 후에도 생존 |
| 다중 세션 | 불가능 | `CLAUDE_CODE_TASK_LIST_ID`를 통해 |

---

<a id="agent-teams"></a>
## 에이전트 팀

실험적 기능으로 **2026년 2월 5일** 발표되었습니다. 에이전트 팀은 여러 Claude Code 세션이 공유 작업을 조율할 수 있게 합니다.

### 활성화

```json
// ~/.claude/settings.json에서
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

### 구성

팀 구성은 `~/.claude/teams/{team-name}/`에 있으며 모드를 지원합니다:

| 모드 | 설명 | 요구 사항 |
|------|-------------|--------------|
| **인프로세스** (기본값) | 모든 팀원이 터미널 내부에서 실행 | 없음 |
| **분할 패인** | 각 팀원이 자체 패인을 가짐 | tmux 또는 iTerm2 (VS Code 터미널 아님) |

---

<a id="design-principles"></a>
## 설계 원칙

전역 전용 vs 이중 범위 분할은 명확한 패턴을 따릅니다:

| 범주 | 범위 | 근거 |
|----------|-------|-----------|
| **조율 상태** (작업, 팀) | 전역 전용 | 단일 프로젝트를 넘어 영속해야 함 |
| **보안 상태** (자격 증명, OAuth) | 전역 전용 | 버전 관리에 실수로 커밋되는 것 방지 |
| **개인 학습** (자동 메모리) | 전역 전용 | 사용자별, 팀 공유 불가 |
| **입력 선호도** (키 바인딩) | 전역 전용 | 사용자 근육 기억, 프로젝트별 아님 |
| **구성** (설정, 규칙, 에이전트) | 두 수준 | 팀이 프로젝트별 동작을 공유해야 함 |
| **워크플로우 정의** (커맨드, 스킬) | 두 수준 | 개인적이거나 팀 공유 가능 |

자동 메모리 (`~/.claude/projects/<hash>/memory/`)는 주목할 만한 하이브리드입니다: 특정 프로젝트*에 관한* 것이지만 팀 공유 가능한 구성보다는 개인 학습을 나타내기 때문에 *전역적으로* 저장됩니다.

---

<a id="sources"></a>
## 소스

- [Claude Code 설정 문서](https://code.claude.com/docs/en/settings)
- [Claude Code 세션 팀 오케스트레이션](https://code.claude.com/docs/en/agent-teams)
- [Claude Code의 작업이란 무엇인가 - ClaudeLog](https://claudelog.com/faqs/what-are-tasks-in-claude-code/)
- [Claude Code 작업 관리 - ClaudeFast](https://claudefa.st/blog/guide/development/task-management)
- [Claude Code 작업 업데이트 - VentureBeat](https://venturebeat.com/orchestration/claude-codes-tasks-update-lets-agents-work-longer-and-coordinate-across)
- [Claude Code 전역 설정은 어디에 있나 - ClaudeLog](https://claudelog.com/faqs/where-are-claude-code-global-settings/)
- [Claude Opus 4.6 에이전트 팀 - VentureBeat](https://venturebeat.com/technology/anthropics-claude-opus-4-6-brings-1m-token-context-and-agent-teams-to-take)
- [Claude Code 에이전트 팀 설정 방법 (전체 안내) - r/ClaudeCode](https://www.reddit.com/r/ClaudeCode/comments/1qz8tyy/how_to_set_up_claude_code_agent_teams_full/)
- [Anthropic이 Claude Code의 이전 '할 일'을 작업으로 대체 - r/ClaudeAI](https://www.reddit.com/r/ClaudeAI/comments/1qkjznp/anthropic_replaced_claude_codes_old_todos_with/)
