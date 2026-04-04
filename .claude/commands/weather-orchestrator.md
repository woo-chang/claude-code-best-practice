---
description: 두바이 날씨 데이터를 가져와 SVG 날씨 카드를 생성합니다
model: haiku
---

# Weather Orchestrator Command

두바이(UAE)의 현재 온도를 가져와 시각적 SVG 날씨 카드를 생성합니다.

## 워크플로우

### 1단계: 사용자 선호도 묻기

AskUserQuestion 도구를 사용하여 사용자가 섭씨 또는 화씨 중 어떤 단위를 원하는지 물어보세요.

### 2단계: 날씨 데이터 가져오기

Task 도구를 사용하여 날씨 에이전트를 호출하세요:
- subagent_type: weather-agent
- description: 두바이 날씨 데이터 가져오기
- prompt: 두바이(UAE)의 현재 온도를 [사용자가 요청한 단위]로 가져오세요. 숫자로 된 온도 값과 단위를 반환하세요. 에이전트에는 상세 지시사항을 제공하는 사전 로드된 스킬 (weather-fetcher)이 있습니다.
- model: haiku

에이전트가 완료될 때까지 기다리고 반환된 온도 값과 단위를 캡처하세요.

### 3단계: SVG 날씨 카드 생성

Skill 도구를 사용하여 weather-svg-creator 스킬을 호출하세요:
- skill: weather-svg-creator

스킬은 2단계의 온도 값과 단위 (현재 컨텍스트에서 사용 가능)를 사용하여 SVG 카드를 생성하고 출력 파일을 작성합니다.

## 핵심 요구사항

1. **에이전트에 Task 도구 사용**: 에이전트를 호출하기 위해 bash 커맨드를 사용하지 마세요. Task 도구를 사용해야 합니다.
2. **SVG 생성기에 Skill 도구 사용**: SVG 생성기는 Task 도구가 아닌 Skill 도구로 호출하세요.
3. **사용자 선호도 전달**: 에이전트를 호출할 때 사용자의 온도 단위 선호도를 포함하세요.
4. **순차적 흐름**: 다음 단계로 넘어가기 전에 각 단계를 완료하세요.

## 출력 요약

다음을 보여주는 명확한 요약을 사용자에게 제공하세요:
- 요청한 온도 단위
- 두바이에서 가져온 온도
- `orchestration-workflow/weather.svg`에 생성된 SVG 카드
- `orchestration-workflow/output.md`에 작성된 요약
