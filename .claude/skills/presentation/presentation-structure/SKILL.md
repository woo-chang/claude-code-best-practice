---
name: presentation-structure
description: 프레젠테이션 슬라이드 형식, 가중치 시스템, 탐색, 섹션 구조에 대한 지식
---

# 프레젠테이션 구조 스킬

`presentation/index.html` 프레젠테이션의 구조에 대한 지식입니다.

## 파일 위치

`presentation/index.html` — 인라인 CSS와 JS가 포함된 단일 파일 HTML 프레젠테이션.

## 슬라이드 형식

각 슬라이드는 `data-slide` (순차 번호)와 선택적 `data-level` (전환 지점의 여정 레벨)이 포함된 div입니다:

```html
<!-- 일반 슬라이드 — 이전 data-level 슬라이드에서 레벨 상속 -->
<div class="slide" data-slide="12">
    <h1>슬라이드 제목</h1>
    <!-- 내용 -->
</div>

<!-- 레벨 전환 슬라이드 — 이 슬라이드와 이후 모든 슬라이드에 새 레벨 설정 -->
<div class="slide section-slide" data-slide="10" data-level="low">
    <h1>섹션 이름</h1>
    <p class="section-desc">Level: Low — 이 섹션 설명</p>
</div>

<!-- 제목 슬라이드 (중앙 정렬) -->
<div class="slide title-slide" data-slide="1">
    <h1>프레젠테이션 제목</h1>
    <p class="subtitle">부제목 텍스트</p>
</div>
```

## 여정 바 레벨 시스템

프레젠테이션은 누적 퍼센트 대신 4레벨 시스템을 사용합니다:

- 레벨은 주요 전환 슬라이드 (섹션 구분자)의 `data-level` 속성으로 설정됩니다
- `data-level` 슬라이드 이후의 모든 슬라이드는 다음 전환까지 해당 레벨을 상속합니다
- 여정 바는 Low / Medium / High / Pro에 대해 각각 25% / 50% / 75% / 100%로 채워집니다
- 바는 슬라이드 1 (제목 슬라이드)에서 숨겨지고 슬라이드 2부터 표시됩니다
- 첫 번째 `data-level` 이전 슬라이드 (슬라이드 2-9)는 빈 바를 표시합니다 (아직 레벨 미설정)
- `data-level`을 가진 슬라이드의 `<h1>`에 `.level-badge`가 JS로 주입됩니다 — HTML에 하드코딩하지 마세요

### 섹션별 레벨 전환

| 섹션 | 슬라이드 범위 | data-level | 바 높이 |
|---------|-------------|------------|------------|
| Part 0: 소개 | 슬라이드 1-4 | (없음) | 숨김 / 비어 있음 |
| Part 1: 사전 조건 | 슬라이드 5-9 | (없음) | 비어 있음 |
| Part 2: 더 나은 프롬프팅 | 슬라이드 10-17 | `low` | 25% |
| Part 3: 프로젝트 메모리 | 슬라이드 18-24 | `medium` | 50% |
| Part 4: 구조화된 워크플로우 | 슬라이드 25-28 | (medium 상속) | 50% |
| Part 5: 도메인 지식 | 슬라이드 29-33 | `high` | 75% |
| Part 6: 에이전틱 엔지니어링 | 슬라이드 34-46 | `high` | 75% |
| 부록 | 슬라이드 47+ | (high 상속) | 75% |

## 탐색 시스템

- `goToSlide(n)` — TOC 링크에 사용되며, 실제 `data-slide` 번호와 일치해야 합니다
- `totalSlides`는 DOM에서 자동 계산됩니다 (`document.querySelectorAll('[data-slide]').length`)
- 방향키, 스페이스, 터치 스와이프로 탐색
- 슬라이드 카운터는 왼쪽 하단에 `현재 / 전체`를 표시합니다

## 재번호 매기기 규칙

슬라이드 추가, 제거, 순서 변경 후:
1. 모든 `data-slide` 속성을 1부터 순차적으로 재번호 매기기
2. TOC/여정 맵 슬라이드의 모든 `goToSlide()` 호출 업데이트
3. JS `totalSlides`는 자동 계산 — 수동 업데이트 불필요
4. 간격이나 중복이 없는지 확인

## 섹션 구분자 형식

섹션 구분자는 `section-slide` 클래스를 사용합니다. 레벨 전환 섹션 구분자는 `data-level`을 가지며 설명에 레벨 이름을 표시합니다:

```html
<div class="slide section-slide" data-slide="10" data-level="low">
    <p class="section-number">Part 2</p>
    <h1>더 나은 프롬프팅</h1>
    <p class="section-desc">Level: Low — 실제 결과를 위한 효과적인 프롬프팅.</p>
</div>
```

JS는 레벨이 전환될 때 런타임에 `<h1>`에 `.level-badge` (예: "→ Low")를 주입합니다 — HTML에 수동으로 추가하지 마세요.
