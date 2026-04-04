---
name: sync-upstream
description: upstream(원본 repo)의 변경사항을 origin/main에 반영하고, ko 브랜치에도 동기화합니다. 2-브랜치 전략(main=영어 원본, ko=한글 번역)을 유지합니다.
model: sonnet
---

# Upstream 동기화

upstream(원본 repo)의 최신 변경사항을 fork의 main에 반영하고, ko 브랜치에도 동기화합니다.

## 브랜치 구조

```
upstream/main (원본, shanraisshan) ──→ origin/main (fork, woo-chang) ──→ origin/ko (한글 번역)
```

- `origin/main`: upstream과 항상 동일하게 유지 (영어 원본)
- `origin/ko`: 한글 번역 작업 브랜치

---

## 워크플로우

### 1단계: upstream 최신 상태 가져오기

```bash
git fetch upstream
```

### 2단계: 변경사항 확인

다음을 **병렬로** 실행합니다:
1. `git log --oneline origin/main..upstream/main` — upstream의 새 커밋 확인
2. `git branch --show-current` — 현재 브랜치 확인
3. `git status` — working tree 상태 확인

- 새 커밋이 없으면: "upstream에 변경사항이 없습니다. 이미 최신 상태입니다." 보고 후 종료
- working tree가 깨끗하지 않으면: 🚫 차단 — "커밋되지 않은 변경사항이 있습니다. 먼저 커밋하거나 stash하세요."
- 새 커밋이 있으면: 커밋 목록을 표시하고 진행

### 3단계: 사용자 확인

AskUserQuestion으로 새 커밋 목록을 보여주고 확인합니다:
- "upstream에 N개의 새 커밋이 있습니다. 동기화할까요?"
- 선택지: "확인", "취소"

### 4단계: origin/main에 반영

```bash
git checkout main
git merge upstream/main --ff-only
git push origin main
```

- fast-forward 실패 시: 🚫 차단 — "origin/main에 독자적 커밋이 있습니다. 수동 확인이 필요합니다."

### 5단계: ko 브랜치에 반영

```bash
git checkout ko
git merge main
```

#### 충돌이 없는 경우
- 자동 병합 완료 → 6단계로 진행

#### 충돌이 발생한 경우
1. `git diff --name-only --diff-filter=U`로 충돌 파일 목록을 확인합니다
2. 사용자에게 충돌 파일 목록을 표시합니다
3. 각 충돌 파일에 대해:
   - 파일을 Read로 읽어 충돌 마커(`<<<<<<<`, `=======`, `>>>>>>>`)를 확인합니다
   - upstream 변경 내용(영어)을 수용합니다 — 번역은 이후 재번역으로 처리
   - `git add <파일>`로 해결을 표시합니다
4. 모든 충돌 해결 후 `git commit`으로 병합 커밋을 생성합니다
   - 커밋 메시지: `update: upstream 변경사항 동기화 — 충돌 N개 해결`

### 6단계: 재번역 필요 파일 식별

upstream에서 변경된 파일 중 번역 대상 화이트리스트에 있는 파일을 식별합니다:

```bash
git diff HEAD~1..HEAD --name-only
```

해당 파일 목록을 `reports/retranslation-needed.md`에 기록합니다:

```markdown
# 재번역 필요 파일

upstream 동기화 후 재번역이 필요한 파일 목록입니다.

## 동기화 정보
- 동기화 일시: [날짜]
- upstream 커밋: [커밋 해시]

## 재번역 필요 파일
| 파일 | 변경 유형 |
|------|----------|
| [파일명] | [추가/수정/삭제] |
```

### 7단계: push 및 요약

```bash
git push origin ko
```

사용자에게 요약을 제시합니다:
- 동기화된 upstream 커밋 수
- 충돌 발생 여부 및 해결 내용
- 재번역 필요 파일 수 및 목록
- 다음 단계: `/translate-ko`로 해당 파일만 재번역 권고

## 핵심 요구사항

1. **working tree 확인**: 커밋되지 않은 변경이 있으면 동기화를 시작하지 마세요
2. **fast-forward only**: origin/main은 반드시 `--ff-only`로만 병합하세요
3. **origin/main 즉시 push**: main을 fast-forward한 후 반드시 `origin/main`에 push하세요. 로컬 기준점으로만 두지 않고, fork의 원문 기준 브랜치를 항상 최신 상태로 유지해야 다음 유지보수 루프에서 변경 감지 기준이 흔들리지 않습니다.
4. **충돌 시 upstream 우선**: 충돌 발생 시 upstream(영어) 내용을 수용하고, 재번역 목록에 추가하세요
5. **사용자 확인**: 동기화 전 반드시 확인을 받으세요
6. **push 확인**: 모든 변경(main + ko)이 origin에 push되었는지 확인하세요
