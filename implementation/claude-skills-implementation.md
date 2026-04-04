# 스킬 구현

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar_02%2C_2026-white?style=flat&labelColor=555)

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

<a href="#weather-svg-creator"><img src="../!/tags/implemented-hd.svg" alt="Implemented"></a>

두 가지 스킬이 **커맨드 → 에이전트 → 스킬** 아키텍처 패턴의 일부로 이 리포지토리에 구현되어 있으며, 두 가지 별개의 스킬 호출 패턴을 시연합니다: **에이전트 스킬** (사전 로드) 및 **스킬** (직접 호출).

---

## 날씨 SVG 생성기 (스킬)

**파일**: [`.claude/skills/weather-svg-creator/SKILL.md`](../.claude/skills/weather-svg-creator/SKILL.md)

```yaml
---
name: weather-svg-creator
description: Creates an SVG weather card showing the current temperature for
  Dubai. Writes the SVG to orchestration-workflow/weather.svg and updates
  orchestration-workflow/output.md.
---

# Weather SVG Creator Skill

This skill creates a visual SVG weather card and writes the output files.

## Task
Create an SVG weather card displaying the temperature for Dubai, UAE,
and write it along with a summary to output files.

## Instructions
You will receive the temperature value and unit (Celsius or Fahrenheit)
from the calling context.

### 1. Create SVG Weather Card
Generate a clean SVG weather card...

### 2. Write SVG File
Write the SVG content to `orchestration-workflow/weather.svg`.

### 3. Write Output Summary
Write to `orchestration-workflow/output.md`...

...
```

이것은 **스킬**입니다 — Skill 도구를 통해 커맨드에 의해 직접 호출됩니다. 대화 컨텍스트에서 온도 데이터를 받아 SVG 날씨 카드와 출력 요약을 생성합니다.

---

## 날씨 가져오기 (에이전트 스킬)

**파일**: [`.claude/skills/weather-fetcher/SKILL.md`](../.claude/skills/weather-fetcher/SKILL.md)

```yaml
---
name: weather-fetcher
description: Instructions for fetching current weather temperature data
  for Dubai, UAE from Open-Meteo API
user-invocable: false
---

# Weather Fetcher Skill

This skill provides instructions for fetching current weather data.

## Task
Fetch the current temperature for Dubai, UAE in the requested unit
(Celsius or Fahrenheit).

## Instructions
1. Fetch Weather Data: Use the WebFetch tool to get current weather data
   - Celsius URL: https://api.open-meteo.com/v1/forecast?latitude=25.2048&longitude=55.2708&current=temperature_2m&temperature_unit=celsius
   - Fahrenheit URL: https://api.open-meteo.com/v1/forecast?latitude=25.2048&longitude=55.2708&current=temperature_2m&temperature_unit=fahrenheit
2. Extract Temperature: From the JSON response, extract `current.temperature_2m`
3. Return Result: Return the temperature value and unit clearly.

...
```

이것은 **에이전트 스킬**입니다 — `skills:` 프론트매터 필드를 통해 시작 시 `weather-agent`에 사전 로드됩니다. 직접 호출되지 않으며, 대신 에이전트의 컨텍스트에 주입된 도메인 지식으로 사용됩니다. `/` 커맨드 메뉴에서 숨기는 `user-invocable: false`를 참고하세요.

---

## 두 가지 스킬 패턴

| 패턴 | 호출 방법 | 예시 | 핵심 차이점 |
|---------|-----------|---------|----------------|
| **스킬** | `Skill(skill: "name")` | `weather-svg-creator` | Skill 도구를 통해 직접 호출 |
| **에이전트 스킬** | `skills:` 필드를 통해 사전 로드 | `weather-fetcher` | 시작 시 에이전트 컨텍스트에 주입 |

---

## ![How to Use](../!/tags/how-to-use.svg)

**스킬** — 슬래시 커맨드로 직접 호출:
```bash
$ claude
> /weather-svg-creator
```

---

## ![How to Implement](../!/tags/how-to-implement.svg)

Claude에게 만들어 달라고 요청하면 — YAML 프론트매터와 본문이 있는 마크다운 파일을 `.claude/skills/my-skill/SKILL.md`에 생성합니다

# My Skill

Instructions for what the skill does.
```
