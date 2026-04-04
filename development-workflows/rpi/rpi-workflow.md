# RPI 워크플로우

**RPI** = **R**esearch(리서치) → **P**lan(플랜) → **I**mplement(구현)

각 단계에서 검증 게이트가 있는 체계적인 개발 워크플로우. 실현 불가능한 기능에 대한 낭비적인 노력을 방지하고 포괄적인 문서화를 보장합니다.

<table width="100%">
<tr>
<td><a href="../../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 개요

![RPI Workflow](rpi-workflow.svg)

---

## 설치

`.claude` 폴더(`agents/` 및 `commands/rpi/` 포함)를 리포지토리 루트에 복사한 다음 `rpi/plans` 디렉토리를 생성하세요.

---

## 예시 워크플로우

### 기능: 사용자 인증

**1단계: 설명**
```
User: "Add OAuth2 authentication with Google and GitHub providers"

1. Claude generates plan
   → Output: rpi/plans/oauth2-authentication.md
2. Create feature folder: rpi/oauth2-authentication/
3. Copy the plan into the feature folder
4. Rename the plan to REQUEST.md
   → Final: rpi/oauth2-authentication/REQUEST.md
```

**2단계: 리서치**
```bash
/rpi:research rpi/oauth2-authentication/REQUEST.md
```
출력:
- 분석이 담긴 `research/RESEARCH.md`
- 판정: **GO** (실현 가능, 전략과 부합)

**3단계: 플랜**
```bash
/rpi:plan oauth2-authentication
```
출력:
- `plan/pm.md` - 사용자 스토리 및 수용 기준
- `plan/ux.md` - 로그인 UI 흐름
- `plan/eng.md` - 기술 아키텍처
- `plan/PLAN.md` - 3단계, 15개 태스크

**4단계: 구현**
```bash
/rpi:implement oauth2-authentication
```
진행 상황:
- Phase 1: Backend Foundation → PASS
- Phase 2: Frontend Integration → PASS
- Phase 3: Testing & Polish → PASS

결과: 기능 완성, PR 준비 완료.

---

## 기능 폴더 구조

모든 기능 작업은 `rpi/{feature-slug}/`에 위치합니다:

```
rpi/{feature-slug}/
├── REQUEST.md              # Step 1: Initial feature description
├── research/
│   └── RESEARCH.md         # Step 2: GO/NO-GO analysis
├── plan/
│   ├── PLAN.md             # Step 3: Implementation roadmap
│   ├── pm.md               # Product requirements
│   ├── ux.md               # UX design
│   └── eng.md              # Technical specification
└── implement/
    └── IMPLEMENT.md        # Step 4: Implementation record
```

---

## 에이전트와 커맨드

| 커맨드 | 사용되는 에이전트 |
|---------|-------------|
| `/rpi:research` | requirement-parser, product-manager, Explore, senior-software-engineer, technical-cto-advisor, documentation-analyst-writer |
| `/rpi:plan` | senior-software-engineer, product-manager, ux-designer, documentation-analyst-writer |
| `/rpi:implement` | Explore, senior-software-engineer, code-reviewer |