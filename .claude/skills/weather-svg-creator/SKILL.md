---
name: weather-svg-creator
description: 두바이의 현재 온도를 표시하는 SVG 날씨 카드를 생성합니다. SVG를 orchestration-workflow/weather.svg에 저장하고 orchestration-workflow/output.md를 업데이트합니다.
---

# 날씨 SVG 생성 스킬

두바이, UAE의 시각적 SVG 날씨 카드를 만들고 출력 파일을 저장합니다.

## 태스크

호출 컨텍스트에서 온도 값과 단위 (섭씨 또는 화씨)를 받습니다. SVG 날씨 카드를 만들고 SVG와 마크다운 요약 모두 저장합니다.

## 지시사항

1. **SVG 생성** — [reference.md](reference.md)의 SVG 템플릿을 사용하여 플레이스홀더를 실제 값으로 교체하세요
2. **SVG 파일 저장** — `orchestration-workflow/weather.svg`를 읽은 후 저장하세요
3. **요약 저장** — [reference.md](reference.md)의 마크다운 템플릿을 사용하여 `orchestration-workflow/output.md`를 읽은 후 저장하세요

## 규칙

- 제공된 정확한 온도 값과 단위를 사용하세요 — 다시 가져오거나 수정하지 마세요
- SVG는 독립적이고 유효해야 합니다
- 두 출력 파일 모두 `orchestration-workflow/` 디렉토리에 저장하세요

## 추가 리소스

- SVG 템플릿, 출력 템플릿, 디자인 사양은 [reference.md](reference.md)를 참조하세요
- 입력/출력 예시는 [examples.md](examples.md)를 참조하세요
