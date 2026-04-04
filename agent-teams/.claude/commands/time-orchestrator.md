---
model: haiku
---

# 시간 오케스트레이터 커맨드

두바이 (Asia/Dubai, UTC+4)의 현재 시간을 가져와 시각적 SVG 시간 카드를 생성합니다.

## 워크플로우

### 1단계: 현재 두바이 시간 가져오기

Agent 도구를 사용하여 time 에이전트를 호출합니다:
- subagent_type: time-agent
- description: 현재 두바이 시간 가져오기
- prompt: 두바이 (Asia/Dubai, UTC+4)의 현재 시간을 가져오세요. 정확히 세 개의 필드를 반환하세요: `time` (시간 부분, 예: "14:30:45"), `timezone` ("GST (UTC+4)"), `formatted` (전체 형식화된 문자열, 예: "2026-03-12 14:30:45 +04"). 에이전트에는 상세 지시사항을 제공하는 사전 로드된 스킬 (time-fetcher)이 있습니다.
- model: haiku

에이전트가 완료될 때까지 기다리고 반환된 시간 데이터를 캡처합니다.

### 데이터 계약

time-agent는 반드시 다음 세 개의 필드를 반환해야 합니다:
- **time**: 시간 부분 (예: "14:30:45")
- **timezone**: "GST (UTC+4)"
- **formatted**: 전체 형식화된 문자열 (예: "2026-03-12 14:30:45 +04")

### 2단계: SVG 시간 카드 생성

Skill 도구를 사용하여 time-svg-creator 스킬을 호출합니다:
- skill: time-svg-creator
- args: 1단계의 시간 데이터를 전달하세요 — `time`, `timezone`, `formatted` 값을 포함

스킬은 1단계의 시간 데이터 (현재 컨텍스트에서 사용 가능)를 사용하여 SVG 카드를 생성하고 출력 파일을 작성합니다.

## 핵심 요구사항

1. **time-agent에는 Agent 도구 사용**: 에이전트를 호출하기 위해 bash 커맨드를 사용하지 마세요. `subagent_type: "time-agent"`로 Agent 도구를 사용해야 합니다.
2. **SVG 크리에이터에는 Skill 도구 사용**: `skill: "time-svg-creator"`로 Skill 도구를 통해 SVG 크리에이터를 호출하세요. Agent 도구가 아닙니다.
3. **순차적 흐름**: 에이전트가 완료되고 시간 데이터를 반환한 후에 스킬을 호출해야 합니다. 병렬로 실행하지 마세요.
4. **데이터 전달**: 스킬을 호출할 때 에이전트 응답의 세 개 필드 (time, timezone, formatted) 모두 컨텍스트에서 사용 가능한지 확인하세요.

## 출력 요약

두 단계 완료 후, 다음을 표시하는 명확한 요약을 사용자에게 제공하세요:
- 가져온 현재 두바이 시간
- 타임존: GST (UTC+4)
- 전체 형식화된 타임스탬프
- `agent-teams/output/dubai-time.svg`에 생성된 SVG 카드
- `agent-teams/output/output.md`에 작성된 요약
