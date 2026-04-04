# 서브에이전트 모범 사례

![Last Updated](https://img.shields.io/badge/Last_Updated-Apr%2002%2C%202026%209%3A13%20PM%20PKT-white?style=flat&labelColor=555)<br>
[![Implemented](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../implementation/claude-subagents-implementation.md)

Claude Code 서브에이전트 — 프론트매터 필드와 공식 내장 에이전트 유형.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 프론트매터 필드 (16)

| 필드 | 유형 | 필수 여부 | 설명 |
|-------|------|----------|-------------|
| `name` | string | 예 | 소문자와 하이픈을 사용하는 고유 식별자 |
| `description` | string | 예 | 호출 시점. Claude의 자동 호출을 위해 `"PROACTIVELY"` 사용 |
| `tools` | string/list | 아니오 | 도구의 쉼표로 구분된 허용 목록 (예: `Read, Write, Edit, Bash`). 생략 시 모든 도구 상속. 생성 가능한 서브에이전트를 제한하는 `Agent(agent_type)` 문법 지원; 이전 `Task(agent_type)` 별칭도 여전히 작동합니다 |
| `disallowedTools` | string/list | 아니오 | 거부할 도구, 상속되거나 지정된 목록에서 제거 |
| `model` | string | 아니오 | 모델 별칭: `haiku`, `sonnet`, `opus`, 또는 `inherit` (기본값: `inherit`) |
| `permissionMode` | string | 아니오 | 권한 모드: `default`, `acceptEdits`, `dontAsk`, `bypassPermissions`, 또는 `plan` |
| `maxTurns` | integer | 아니오 | 서브에이전트가 중지하기 전까지 최대 에이전틱 턴 수 |
| `skills` | list | 아니오 | 시작 시 에이전트 컨텍스트에 사전 로드할 스킬 이름 (전체 내용이 주입됨, 단순히 사용 가능하게 되는 것이 아님) |
| `mcpServers` | list | 아니오 | 이 서브에이전트를 위한 MCP 서버 — 서버 이름 문자열 또는 인라인 `{name: config}` 객체 |
| `hooks` | object | 아니오 | 이 서브에이전트에 범위 지정된 라이프사이클 훅. 모든 훅 이벤트가 지원됩니다. `PreToolUse`, `PostToolUse`, `Stop`이 가장 일반적입니다 |
| `memory` | string | 아니오 | 영속적 메모리 범위: `user`, `project`, 또는 `local` |
| `background` | boolean | 아니오 | 항상 백그라운드 작업으로 실행하려면 `true`로 설정 (기본값: `false`) |
| `effort` | string | 아니오 | 이 서브에이전트가 활성화될 때 노력 수준 재정의: `low`, `medium`, `high`, `max`. 기본값: 세션에서 상속 |
| `isolation` | string | 아니오 | 임시 git 워크트리에서 실행하려면 `"worktree"`로 설정 (변경 사항이 없으면 자동으로 정리됨) |
| `initialPrompt` | string | 아니오 | 이 에이전트가 메인 세션 에이전트로 실행될 때 (`--agent` 또는 `agent` 설정을 통해) 첫 번째 사용자 턴으로 자동 제출됩니다. 커맨드와 스킬이 처리됩니다. 사용자 제공 프롬프트 앞에 추가됩니다 |
| `color` | string | 아니오 | 시각적 구분을 위한 CLI 출력 색상 (예: `green`, `magenta`) |

---

## ![Official](../!/tags/official.svg) **(5)**

| # | 에이전트 | 모델 | 도구 | 설명 |
|---|-------|-------|-------|-------------|
| 1 | `general-purpose` | inherit | 모두 | 복잡한 다단계 작업 — 리서치, 코드 검색, 자율 작업을 위한 기본 에이전트 유형 |
| 2 | `Explore` | haiku | 읽기 전용 (Write, Edit 없음) | 빠른 코드베이스 검색 및 탐색 — 파일 찾기, 코드 검색, 코드베이스 질문 답변에 최적화 |
| 3 | `Plan` | inherit | 읽기 전용 (Write, Edit 없음) | 플랜 모드에서 사전 계획 리서치 — 코드 작성 전에 코드베이스를 탐색하고 구현 접근 방식을 설계 |
| 4 | `statusline-setup` | sonnet | Read, Edit | 사용자의 Claude Code 상태 줄 설정 구성 |
| 5 | `claude-code-guide` | haiku | Glob, Grep, Read, WebFetch, WebSearch | Claude Code 기능, Agent SDK, Claude API에 대한 질문 답변 |

---

## 소스

- [사용자 정의 서브에이전트 생성 — Claude Code 문서](https://code.claude.com/docs/en/sub-agents)
- [CLI 참조 — Claude Code 문서](https://code.claude.com/docs/en/cli-reference)
- [Claude Code 변경로그](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
