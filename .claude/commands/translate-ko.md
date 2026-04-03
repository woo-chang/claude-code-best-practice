---
description: 리포지토리 전체 콘텐츠를 구조적 무결성을 유지하면서 영어에서 한국어로 번역합니다
model: sonnet
---

# 한국어 번역 오케스트레이터 커맨드

리포지토리 전체를 번역 하네스를 사용하여 영어에서 한국어로 번역합니다.

## 워크플로우

### 1단계: 번역 범위 확인

AskUserQuestion 도구를 사용하여 사용자에게 물어봅니다:
- 전체 파일을 번역할지, 특정 디렉토리만 번역할지?
- 선택지: "전체 번역 (all)", "하네스 파일만 (.claude/)", "문서만 (best-practice, reports, tips)", "특정 디렉토리 지정"

### 2단계: 번역 오케스트레이터 호출

Agent 도구를 사용하여 번역 오케스트레이터를 호출합니다:
- subagent_type: translation-orchestrator
- description: 리포지토리 파일의 한국어 번역 오케스트레이션
- prompt: 다음 범위를 번역하세요: [1단계에서 사용자가 선택한 범위]. translation-rules 스킬을 정확히 따르세요. 디렉토리 배치 순서: A) .claude/ 하네스 파일, B) best-practice/ + implementation/, C) reports/, D) tips/ + videos/ + tutorial/, E) changelog/ + orchestration-workflow/ + 기타, F) CLAUDE.md + README.md. 각 배치 완료 후 진행 상황을 보고하세요.
- model: sonnet

오케스트레이션이 완료될 때까지 대기합니다.

### 3단계: 번역 검증기 호출

Agent 도구를 사용하여 검증기를 호출합니다:
- subagent_type: translation-validator
- description: 번역된 파일의 구조적 무결성 검증
- prompt: validation-rules 스킬에 정의된 모든 검증 항목을 실행하세요. 결과를 reports/translation-validation-report.md에 작성하세요.
- model: sonnet

### 4단계: 요약 제시

사용자에게 다음을 포함한 명확한 요약을 제시합니다:
- 번역된 총 파일 수
- 건너뛴 파일 (이유 포함)
- 검증 결과 (오류, 경고, 통과)
- 다음 단계 (수동 검토 항목, 스크린샷 처리)

## 핵심 요구사항

1. **Agent 도구 사용**: 에이전트를 호출할 때 bash 커맨드를 사용하지 마세요. 반드시 Agent 도구를 사용하세요.
2. **순차적 진행**: 각 단계를 완료한 후 다음 단계로 진행하세요.
3. **translation-rules 스킬**: 모든 번역 에이전트에 이 스킬이 프리로드되어 있습니다. 규칙을 재정의하지 마세요.
4. **진행 상황 추적**: 오케스트레이터가 각 배치 완료 후 `reports/translation-progress.md`를 업데이트합니다.
5. **검증 필수**: 번역 완료 후 반드시 검증기를 실행하세요.
