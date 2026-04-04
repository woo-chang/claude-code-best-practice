# Glob: presentation/**

## 위임 규칙

프레젠테이션 (`presentation/index.html`)을 업데이트, 수정, 또는 교정하는 모든 요청은 `presentation-curator` 에이전트가 처리해야 합니다. 항상 Task 도구를 통해 이 에이전트에 프레젠테이션 작업을 위임하세요 — 프레젠테이션을 직접 편집하지 마세요.

```
Task(subagent_type="presentation-curator", description="...", prompt="...")
```

## 이유

presentation-curator 에이전트에는 프레젠테이션의 구조, 스타일링, 개념적 프레임워크와 동기화를 유지하는 세 가지 사전 로드된 스킬이 있습니다. 또한 모든 실행 후 자체 진화하여, 자체 스킬을 업데이트하여 지식 드리프트를 방지합니다. 에이전트를 우회하면 슬라이드 번호 매기기, 레벨 전환, 또는 스타일 일관성이 손상될 위험이 있습니다.
