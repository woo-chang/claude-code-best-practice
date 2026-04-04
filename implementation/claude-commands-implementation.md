# 커맨드 구현

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar_02%2C_2026-white?style=flat&labelColor=555)

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

<a href="#weather-orchestrator"><img src="../!/tags/implemented-hd.svg" alt="Implemented"></a>

날씨 오케스트레이터 커맨드는 이 리포지토리에서 **커맨드 → 에이전트 → 스킬** 아키텍처 패턴의 진입점으로 구현되어 있으며, 커맨드가 다단계 워크플로우를 오케스트레이션하는 방법을 시연합니다.

---

## 날씨 오케스트레이터

**파일**: [`.claude/commands/weather-orchestrator.md`](../.claude/commands/weather-orchestrator.md)

```yaml
---
description: Fetch weather data for Dubai and create an SVG weather card
model: haiku
---

# Weather Orchestrator Command

Fetch the current temperature for Dubai, UAE and create a visual SVG weather card.

## Workflow

### Step 1: Ask User Preference
Use the AskUserQuestion tool to ask the user whether they want the temperature
in Celsius or Fahrenheit.

### Step 2: Fetch Weather Data
Use the Agent tool to invoke the weather agent:
- subagent_type: weather-agent
- prompt: Fetch the current temperature for Dubai, UAE in [unit]...

### Step 3: Create SVG Weather Card
Use the Skill tool to invoke the weather-svg-creator skill:
- skill: weather-svg-creator

...
```

커맨드는 전체 워크플로우를 오케스트레이션합니다: 사용자에게 온도 단위 선호도를 묻고, Agent 도구를 통해 `weather-agent`를 호출한 다음, Skill 도구를 통해 `weather-svg-creator` 스킬을 호출합니다.

---

## ![How to Use](../!/tags/how-to-use.svg)

```bash
$ claude
> /weather-orchestrator
```

---

## ![How to Implement](../!/tags/how-to-implement.svg)

Claude에게 만들어 달라고 요청하면 — YAML 프론트매터와 본문이 있는 마크다운 파일을 `.claude/commands/<name>.md`에 생성합니다

---

<a href="https://github.com/shanraisshan/claude-code-best-practice#orchestration-workflow"><img src="../!/tags/orchestration-workflow-hd.svg" alt="Orchestration Workflow"></a>

날씨 오케스트레이터는 커맨드 → 에이전트 → 스킬 오케스트레이션 패턴의 **커맨드**입니다. 진입점 역할을 합니다 — 사용자 상호작용 처리 (온도 단위 선호도), `weather-agent`에 데이터 가져오기 위임, 시각적 출력을 위한 `weather-svg-creator` 스킬 호출.

<p align="center">
  <img src="../orchestration-workflow/orchestration-workflow.svg" alt="커맨드 스킬 에이전트 아키텍처 흐름" width="100%">
</p>

| 구성 요소 | 역할 | 이 리포지토리 |
|-----------|------|-----------|
| **커맨드** | 진입점, 사용자 상호작용 | [`/weather-orchestrator`](../.claude/commands/weather-orchestrator.md) |
| **에이전트** | 사전 로드된 스킬로 데이터 가져오기 (에이전트 스킬) | [`weather-agent`](../.claude/agents/weather-agent.md)와 [`weather-fetcher`](../.claude/skills/weather-fetcher/SKILL.md) |
| **스킬** | 독립적으로 출력 생성 (스킬) | [`weather-svg-creator`](../.claude/skills/weather-svg-creator/SKILL.md) |
