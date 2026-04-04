# Glob: **/*.md

## 문서화 표준

- 파일을 집중적이고 간결하게 유지하세요 — 파일당 하나의 주제
- 문서 간에는 절대 GitHub URL이 아닌 상대 링크를 사용하세요 (예: `../best-practice/claude-memory.md`)
- best-practice 및 report 문서 상단에 뒤로 가기 탐색 링크를 포함하세요 (기존 파일의 패턴 참조)
- 새 개념이나 리포트를 추가할 때 README.md의 해당 테이블 (CONCEPTS 또는 REPORTS)을 업데이트하세요

## 구조 컨벤션

- Best practice 문서는 `best-practice/`에 저장하세요
- 구현 문서는 `implementation/`에 저장하세요
- 리포트는 `reports/`에 저장하세요
- 팁은 `tips/`에 저장하세요
- 체인지로그 추적은 `changelog/<category>/`에 저장하세요

## 형식

- 구조화된 비교에는 테이블을 사용하세요 (참조: README CONCEPTS 테이블)
- best-practice 또는 구현 문서 링크 시 시각적 일관성을 위해 `!/tags/`의 배지 이미지를 사용하세요
- 제목을 계층적으로 유지하세요 — 레벨을 건너뛰지 마세요 (예: `##`에서 `####`으로 바로 점프하지 마세요)
