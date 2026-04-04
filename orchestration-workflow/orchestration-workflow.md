# 오케스트레이션 워크플로우

이 문서는 날씨 데이터 수집 및 SVG 렌더링 시스템을 통해 시연되는 **Command → Agent (with skill) → Skill** 오케스트레이션 워크플로우를 설명합니다.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 시스템 개요

날씨 시스템은 단일 오케스트레이션 워크플로우 내에서 두 가지 스킬 패턴을 시연합니다:
- **에이전트 스킬** (사전 로드): `weather-fetcher`가 도메인 지식으로 시작 시 `weather-agent`에 주입됨
- **스킬** (독립형): `weather-svg-creator`가 Skill 도구를 통해 커맨드에서 직접 호출됨

이는 다음을 포함하는 **Command → Agent → Skill** 아키텍처 패턴을 보여줍니다:
- 커맨드가 워크플로우를 오케스트레이션하고 사용자 상호작용을 처리
- 에이전트가 사전 로드된 스킬을 사용하여 데이터를 수집
- 스킬이 독립적으로 시각적 출력을 생성

## 컴포넌트 요약

| 컴포넌트 | 역할 | 예시 |
|-----------|------|---------|
| **커맨드** | 진입점, 사용자 상호작용 | [`/weather-orchestrator`](../.claude/commands/weather-orchestrator.md) |
| **에이전트** | 사전 로드된 스킬로 데이터 수집 (에이전트 스킬) | [`weather-agent`](../.claude/agents/weather-agent.md) with [`weather-fetcher`](../.claude/skills/weather-fetcher/SKILL.md) |
| **스킬** | 독립적으로 출력 생성 (스킬) | [`weather-svg-creator`](../.claude/skills/weather-svg-creator/SKILL.md) |

## Flow Diagram

```
╔══════════════════════════════════════════════════════════════════╗
║              ORCHESTRATION WORKFLOW                              ║
║           Command  →  Agent  →  Skill                            ║
╚══════════════════════════════════════════════════════════════════╝

                         ┌───────────────────┐
                         │  User Interaction │
                         └─────────┬─────────┘
                                   │
                                   ▼
         ┌─────────────────────────────────────────────────────┐
         │  /weather-orchestrator — Command (Entry Point)      │
         └─────────────────────────┬───────────────────────────┘
                                   │
                              Step 1
                                   │
                                   ▼
                      ┌────────────────────────┐
                      │  AskUser — C° or F°?   │
                      └────────────┬───────────┘
                                   │
                         Step 2 — Agent tool
                                   │
                                   ▼
         ┌─────────────────────────────────────────────────────┐
         │  weather-agent — Agent ● skill: weather-fetcher     │
         └─────────────────────────┬───────────────────────────┘
                                   │
                          Returns: temp + unit
                                   │
                         Step 3 — Skill tool
                                   │
                                   ▼
         ┌─────────────────────────────────────────────────────┐
         │  weather-svg-creator — Skill ● SVG card + output    │
         └─────────────────────────┬───────────────────────────┘
                                   │
                          ┌────────┴────────┐
                          │                 │
                          ▼                 ▼
                   ┌────────────┐    ┌────────────┐
                   │weather.svg │    │ output.md  │
                   └────────────┘    └────────────┘
```

## 컴포넌트 상세

### 1. 커맨드

#### `/weather-orchestrator` (커맨드)
- **위치**: `.claude/commands/weather-orchestrator.md`
- **목적**: 진입점 — 워크플로우를 오케스트레이션하고 사용자 상호작용을 처리
- **동작**:
  1. 사용자에게 온도 단위 선호도(섭씨/화씨) 질문
  2. Agent 도구를 통해 weather-agent 호출
  3. Skill 도구를 통해 weather-svg-creator 호출
- **모델**: haiku

### 2. 사전 로드된 스킬이 있는 에이전트 (에이전트 스킬)

#### `weather-agent` (에이전트)
- **위치**: `.claude/agents/weather-agent.md`
- **목적**: 사전 로드된 스킬을 사용하여 날씨 데이터 수집
- **스킬**: `weather-fetcher` (도메인 지식으로 사전 로드됨)
- **사용 가능한 도구**: WebFetch, Read
- **모델**: sonnet
- **색상**: green
- **메모리**: project

에이전트는 시작 시 컨텍스트에 `weather-fetcher`가 사전 로드됩니다. 스킬의 지시사항에 따라 온도를 가져오고 커맨드에 값을 반환합니다.

### 3. 스킬

#### `weather-svg-creator` (스킬)
- **위치**: `.claude/skills/weather-svg-creator/SKILL.md`
- **목적**: 시각적 SVG 날씨 카드 생성 및 출력 파일 저장
- **호출**: 커맨드에서 Skill 도구를 통해 (어떤 에이전트에도 사전 로드되지 않음)
- **출력**:
  - `orchestration-workflow/weather.svg` — SVG 날씨 카드
  - `orchestration-workflow/output.md` — 날씨 요약

### 4. 사전 로드된 스킬

#### `weather-fetcher` (스킬)
- **위치**: `.claude/skills/weather-fetcher/SKILL.md`
- **목적**: 실시간 온도 데이터 수집 지시사항
- **데이터 소스**: 두바이, UAE의 Open-Meteo API
- **출력**: 온도 값 및 단위 (섭씨 또는 화씨)
- **참고**: 이것은 에이전트 스킬 — `weather-agent`에 사전 로드되며 직접 호출되지 않음

## 실행 흐름

1. **사용자 호출**: 사용자가 `/weather-orchestrator` 커맨드 실행
2. **사용자 프롬프트**: 커맨드가 사용자에게 선호 온도 단위(섭씨/화씨) 질문
3. **에이전트 호출**: 커맨드가 Agent 도구를 통해 `weather-agent` 호출
4. **스킬 실행** (에이전트 컨텍스트 내):
   - 에이전트가 `weather-fetcher` 스킬 지시사항에 따라 Open-Meteo에서 온도 수집
   - 에이전트가 온도 값과 단위를 커맨드에 반환
5. **SVG 생성**: 커맨드가 Skill 도구를 통해 `weather-svg-creator` 호출
   - 스킬이 `orchestration-workflow/weather.svg`에 SVG 날씨 카드 생성
   - 스킬이 `orchestration-workflow/output.md`에 요약 저장
6. **결과 표시**: 사용자에게 다음 정보와 함께 요약 표시:
   - 요청된 온도 단위
   - 수집된 온도
   - SVG 카드 위치
   - 출력 파일 위치

## Example Execution

```
Input: /weather-orchestrator
├─ Step 1: Asks: Celsius or Fahrenheit?
│  └─ User: Celsius
├─ Step 2: Agent tool → weather-agent
│  ├─ Preloaded Skill:
│  │  └─ weather-fetcher (domain knowledge)
│  ├─ Fetches from Open-Meteo → 26°C
│  └─ Returns: temperature=26, unit=Celsius
├─ Step 3: Skill tool → /weather-svg-creator
│  ├─ Creates: orchestration-workflow/weather.svg
│  └─ Writes: orchestration-workflow/output.md
└─ Output:
   ├─ Unit: Celsius
   ├─ Temperature: 26°C
   ├─ SVG: orchestration-workflow/weather.svg
   └─ Summary: orchestration-workflow/output.md
```

## 핵심 설계 원칙

1. **두 가지 스킬 패턴**: 에이전트 스킬(사전 로드)과 스킬(직접 호출) 모두 시연
2. **오케스트레이터로서의 커맨드**: 커맨드가 사용자 상호작용을 처리하고 워크플로우를 조율
3. **데이터 수집을 위한 에이전트**: 에이전트가 사전 로드된 스킬을 사용하여 데이터를 수집한 후 반환
4. **출력을 위한 스킬**: SVG 크리에이터가 커맨드 컨텍스트의 데이터를 받아 독립적으로 실행
5. **명확한 분리**: 수집(에이전트) → 렌더링(스킬) — 각 컴포넌트는 단일 책임을 가짐

## 아키텍처 패턴

### 에이전트 스킬 (사전 로드)

```yaml
# In agent definition (.claude/agents/weather-agent.md)
---
name: weather-agent
skills:
  - weather-fetcher    # Preloaded into agent context at startup
---
```

- **스킬이 사전 로드됨**: 시작 시 전체 스킬 콘텐츠가 에이전트의 컨텍스트에 주입됨
- **에이전트가 스킬 지식 활용**: 에이전트가 사전 로드된 스킬의 지시사항을 따름
- **동적 호출 없음**: 스킬은 참조 자료로, 별도로 호출되지 않음

### 스킬 (직접 호출)

```yaml
# In skill definition (.claude/skills/weather-svg-creator/SKILL.md)
---
name: weather-svg-creator
description: Creates an SVG weather card...
---
```

- **Skill 도구를 통해 호출**: 커맨드가 `Skill(skill: "weather-svg-creator")`를 호출
- **독립적 실행**: 에이전트 내부가 아닌 커맨드의 컨텍스트에서 실행
- **컨텍스트에서 데이터 수신**: 대화에서 이미 사용 가능한 온도 데이터를 사용
