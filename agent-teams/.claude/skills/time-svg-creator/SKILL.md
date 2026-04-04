---
name: time-svg-creator
description: 두바이의 현재 시간을 보여주는 SVG 시간 카드를 생성합니다. SVG를 agent-teams/output/dubai-time.svg에 작성하고 agent-teams/output/output.md를 업데이트합니다.
allowed-tools: Write, Read
---

# 시간 SVG 크리에이터 스킬

두바이, UAE를 위한 시각적 SVG 시간 카드를 생성하고 출력 파일을 작성합니다.

## 작업

호출 컨텍스트에서 세 개의 필드를 받습니다: `time`, `timezone`, `formatted`. SVG 시간 카드를 생성하고 SVG와 마크다운 요약을 모두 작성합니다.

## 지시사항

1. **SVG 생성** — [reference.md](reference.md)의 SVG 템플릿을 사용하여 플레이스홀더를 실제 값으로 교체
2. **SVG 파일 작성** — `agent-teams/output/dubai-time.svg`에 작성
3. **요약 작성** — [reference.md](reference.md)의 마크다운 템플릿을 사용하여 `agent-teams/output/output.md`에 작성

## 규칙

- 제공된 정확한 시간 값 사용 — 절대 재가져오기 또는 재계산하지 않음
- SVG가 자체 포함되고 유효해야 함
- 두 출력 파일 모두 `agent-teams/output/` 디렉토리에 저장

## 추가 리소스

- SVG 템플릿, 출력 템플릿, 디자인 명세는 [reference.md](reference.md) 참조
- 예시 입력/출력 쌍은 [examples.md](examples.md) 참조
