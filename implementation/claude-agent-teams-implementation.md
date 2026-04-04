# 에이전트 팀 구현

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar_12%2C_2026-white?style=flat&labelColor=555)

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

<a href="#time-orchestration"><img src="../!/tags/implemented-hd.svg" alt="Implemented"></a>

<p align="center">
  <img src="assets/impl-agent-teams.png" alt="에이전트 팀 실제 동작 — tmux 분할 패인 모드" width="100%">
</p>

에이전트 팀은 공유 작업 목록을 통해 조율하는 **여러 독립적인 Claude Code 세션**을 생성합니다. 하나의 세션 내에서 격리된 컨텍스트 포크인 서브에이전트와 달리, 각 팀원은 CLAUDE.md, MCP 서버, 스킬이 자동으로 로드된 자체 전체 컨텍스트 윈도우를 갖습니다.

---

## ![How to Use](../!/tags/how-to-use.svg)

시간 오케스트레이션 워크플로우는 에이전트 팀에 의해 완전히 구축되었습니다. 완성된 결과물을 실행하려면:

```bash
cd agent-teams
claude
/time-orchestrator
```

이것은 **커맨드 → 에이전트 → 스킬** 파이프라인을 호출합니다: 에이전트는 두바이의 현재 시간을 가져오고, 스킬은 SVG 시간 카드를 `agent-teams/output/dubai-time.svg`에 렌더링합니다.

---

## ![How to Implement](../!/tags/how-to-implement.svg)

에이전트 팀을 사용하여 날씨 오케스트레이션 워크플로우의 복제본을 만들 수 있습니다 — 이 예시에서 시간 오케스트레이션 워크플로우는 에이전트 팀에 의해 완전히 구축되었습니다.

### 1. [iTerm2](https://iterm2.com/)와 tmux 설치

```bash
brew install --cask iterm2
brew install tmux
```

### 2. iTerm2 → tmux → Claude 시작

```bash
tmux new -s dev
CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1 claude
```

### 3. 팀 구조로 프롬프트 입력

<a id="time-orchestration"></a>

에이전트 팀을 사용하여 완전한 시간 오케스트레이터 워크플로우를 구성하려면 이 프롬프트를 Claude에 붙여넣으세요:

메인 프롬프트: **[agent-teams-prompt.md](../agent-teams/agent-teams-prompt.md)**

### 팀 조율 흐름

```
┌──────────────────────────────────────────────────────────────┐
│                         LEAD (You)                           │
│       "Create an agent team to build time orchestration"     │
└──────────────────────────┬───────────────────────────────────┘
                           │ spawns team (all parallel)
              ┌────────────┼────────────┐
              ▼            ▼            ▼
   ┌────────────────┐ ┌──────────┐ ┌──────────────┐
   │ Command        │ │ Agent    │ │ Skill        │
   │ Architect      │ │ Engineer │ │ Designer     │
   │                │ │          │ │              │
   │ agent-teams/   │ │ agent-   │ │ agent-teams/ │
   │ .claude/       │ │ teams/   │ │ .claude/     │
   │ commands/      │ │ .claude/ │ │ skills/      │
   │ time-          │ │ agents/  │ │ time-svg-    │
   │ orchestrator.md│ │ time-    │ │ creator/     │
   │                │ │ agent.md │ │              │
   └───────┬────────┘ └────┬─────┘ └──────┬───────┘
           │               │              │
           ▼               ▼              ▼
   ┌──────────────────────────────────────────────────┐
   │            Shared Task List                      │
   │  ☐ Agree on data contract: {time, tz, formatted} │
   │  ☐ Command uses Agent tool (not bash)            │
   │  ☐ Agent preloads time-fetcher skill             │
   │  ☐ Skill reads time from context (no re-fetch)   │
   │  ☐ All files inside agent-teams/.claude/         │
   └──────────────────────────────────────────────────┘
                       │
                       ▼
          ┌──────────────────────────────┐
          │  cd agent-teams && claude    │
          │    /time-orchestrator        │
          │   Command → Agent → Skill    │
          └──────────────────────────────┘
```
