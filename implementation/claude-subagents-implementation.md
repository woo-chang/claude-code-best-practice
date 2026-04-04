# 서브에이전트 구현

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar_02%2C_2026_07%3A59_PM_PKT-white?style=flat&labelColor=555)

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

<a href="#weather-agent"><img src="../!/tags/implemented-hd.svg" alt="Implemented"></a>

날씨 에이전트는 이 리포지토리에서 **커맨드 → 에이전트 → 스킬** 아키텍처 패턴의 예시로 구현되어 있으며, 두 가지 별개의 스킬 패턴을 시연합니다.

---

## 날씨 에이전트

**파일**: [`.claude/agents/weather-agent.md`](../.claude/agents/weather-agent.md)

```yaml
---
name: weather-agent
description: Use this agent PROACTIVELY when you need to fetch weather data for
  Dubai, UAE. This agent fetches real-time temperature from Open-Meteo
  using its preloaded weather-fetcher skill.
tools: WebFetch, Read, Write, Edit
model: sonnet
color: green
maxTurns: 5
permissionMode: acceptEdits
memory: project
skills:
  - weather-fetcher
---

# Weather Agent

You are a specialized weather agent that fetches weather data for Dubai,
UAE.

## Your Task

Execute the weather workflow by following the instructions from your preloaded
skill:

1. **Fetch**: Follow the `weather-fetcher` skill instructions to fetch the
   current temperature
2. **Report**: Return the temperature value and unit to the caller
3. **Memory**: Update your agent memory with the reading details for
   historical tracking

...
```

에이전트는 Open-Meteo에서 가져오는 지침을 제공하는 사전 로드된 스킬(`weather-fetcher`) 하나를 가지고 있습니다. 온도 값과 단위를 호출 커맨드에 반환합니다.

---

## ![How to Use](../!/tags/how-to-use.svg)

```bash
$ claude
> what is the weather in dubai?
```

---

## ![How to Implement](../!/tags/how-to-implement.svg)

`/agents` 커맨드를 사용하여 에이전트를 생성할 수 있습니다,
```bash
$ claude
> /agents
```

또는 Claude에게 만들어 달라고 요청하면 — YAML 프론트매터와 본문이 있는 마크다운 파일을 `.claude/agents/<name>.md`에 생성합니다

---

<a href="https://github.com/shanraisshan/claude-code-best-practice#orchestration-workflow"><img src="../!/tags/orchestration-workflow-hd.svg" alt="Orchestration Workflow"></a>

날씨 에이전트는 커맨드 → 에이전트 → 스킬 오케스트레이션 패턴의 **에이전트**입니다. `/weather-orchestrator` 커맨드에서 워크플로우를 받아 사전 로드된 스킬(`weather-fetcher`)을 사용하여 온도를 가져옵니다. 그런 다음 커맨드는 독립형 `weather-svg-creator` 스킬을 호출하여 시각적 출력을 생성합니다.

<p align="center">
  <img src="../orchestration-workflow/orchestration-workflow.svg" alt="커맨드 스킬 에이전트 아키텍처 흐름" width="100%">
</p>

| 구성 요소 | 역할 | 이 리포지토리 |
|-----------|------|-----------|
| **커맨드** | 진입점, 사용자 상호작용 | [`/weather-orchestrator`](../.claude/commands/weather-orchestrator.md) |
| **에이전트** | 사전 로드된 스킬로 데이터 가져오기 (에이전트 스킬) | [`weather-agent`](../.claude/agents/weather-agent.md)와 [`weather-fetcher`](../.claude/skills/weather-fetcher/SKILL.md) |
| **스킬** | 독립적으로 출력 생성 (스킬) | [`weather-svg-creator`](../.claude/skills/weather-svg-creator/SKILL.md) |
