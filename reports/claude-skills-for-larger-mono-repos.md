# 대형 모노레포에서의 Claude 스킬 탐색 이해

Claude Code를 모노레포에서 사용할 때, 스킬이 어떻게 탐색되고 컨텍스트에 로드되는지 이해하는 것은 프로젝트별 기능을 효과적으로 구성하는 데 매우 중요합니다.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## CLAUDE.md와의 중요한 차이점

**스킬은 CLAUDE.md 파일과 동일한 로드 방식을 사용하지 않습니다.** CLAUDE.md 파일은 디렉토리 트리를 위로 탐색 (상위 로드)하는 반면, 스킬은 프로젝트 내 중첩 디렉토리에 초점을 맞춘 다른 탐색 방식을 사용합니다.

## 스킬 탐색 방법

### 1. 표준 스킬 위치

스킬은 범위에 따라 다음 고정 위치에서 로드됩니다:

| 위치 | 경로 | 적용 대상 |
|----------|------|------------|
| Enterprise | 관리형 설정 | 조직의 모든 사용자 |
| 개인 | `~/.claude/skills/<skill-name>/SKILL.md` | 모든 프로젝트 |
| 프로젝트 | `.claude/skills/<skill-name>/SKILL.md` | 이 프로젝트에만 |
| 플러그인 | `<plugin>/skills/<skill-name>/SKILL.md` | 플러그인이 활성화된 곳 |

### 2. 중첩 디렉토리에서의 자동 탐색

하위 디렉토리의 파일을 작업할 때, Claude Code는 중첩된 `.claude/skills/` 디렉토리에서 스킬을 자동으로 탐색합니다. 예를 들어, `packages/frontend/`의 파일을 편집하고 있다면 Claude Code는 `packages/frontend/.claude/skills/`에서도 스킬을 찾습니다.

이를 통해 패키지별로 고유한 스킬을 가진 모노레포 설정을 지원합니다.

## 모노레포 구조 예시

별도 패키지가 있는 전형적인 모노레포를 생각해 보세요:

```
/mymonorepo/
├── .claude/
│   └── skills/
│       └── shared-conventions/SKILL.md    # 프로젝트 수준 스킬
├── packages/
│   ├── frontend/
│   │   ├── .claude/
│   │   │   └── skills/
│   │   │       └── react-patterns/SKILL.md  # 프론트엔드 전용 스킬
│   │   └── src/
│   │       └── App.tsx
│   ├── backend/
│   │   ├── .claude/
│   │   │   └── skills/
│   │   │       └── api-design/SKILL.md      # 백엔드 전용 스킬
│   │   └── src/
│   └── shared/
│       ├── .claude/
│       │   └── skills/
│       │       └── utils-patterns/SKILL.md  # 공유 유틸리티 스킬
│       └── src/
```

## 시나리오 1: 루트에서 Claude를 시작한 직후 (파일 편집 전)

`/mymonorepo/`에서 Claude Code를 실행하고 아직 파일을 편집하지 않은 경우:

```bash
cd /mymonorepo
claude
# 방금 시작 — 아직 파일 편집 없음
```

| 스킬 | 컨텍스트에 있나요? | 이유 |
|-------|-------------|--------|
| `shared-conventions` | **예** | 루트 `.claude/skills/`의 프로젝트 수준 스킬 |
| `react-patterns` | **아니오** | 탐색되지 않음 — `packages/frontend/`의 파일을 아직 작업하지 않음 |
| `api-design` | **아니오** | 탐색되지 않음 — `packages/backend/`의 파일을 아직 작업하지 않음 |
| `utils-patterns` | **아니오** | 탐색되지 않음 — `packages/shared/`의 파일을 아직 작업하지 않음 |

## 시나리오 2: 패키지의 파일 편집 후

Claude가 `packages/frontend/src/App.tsx`를 편집한 후:

| 스킬 | 컨텍스트에 있나요? | 이유 |
|-------|-------------|--------|
| `shared-conventions` | **예** | 루트 `.claude/skills/`의 프로젝트 수준 스킬 |
| `react-patterns` | **예** | `packages/frontend/`의 파일 편집 시 탐색됨 |
| `api-design` | **아니오** | 여전히 탐색되지 않음 — `packages/backend/`의 파일을 아직 작업하지 않음 |
| `utils-patterns` | **아니오** | 여전히 탐색되지 않음 — `packages/shared/`의 파일을 아직 작업하지 않음 |

**핵심 인사이트**: 중첩 스킬은 해당 디렉토리의 파일을 작업할 때 **온디맨드로** 탐색됩니다. 세션 시작 시 미리 로드되지 않습니다.

## 주요 동작: 설명 vs 전체 내용

스킬 설명은 사용 가능한 항목을 Claude가 알 수 있도록 컨텍스트에 로드되지만, **전체 스킬 내용은 호출될 때만 로드됩니다**. 이것은 중요한 최적화입니다:

- **설명**: 항상 컨텍스트에 있음 (문자 예산 내에서)
- **전체 내용**: 스킬이 호출될 때 온디맨드로 로드

> 참고: 스킬이 사전 로드된 서브에이전트는 다르게 동작합니다 — 전체 스킬 내용이 시작 시 주입됩니다.

## 우선순위 순서 (스킬 이름이 겹칠 경우)

스킬이 여러 수준에서 같은 이름을 공유할 경우, 우선순위가 높은 위치가 우선합니다:

| 우선순위 | 위치 | 범위 |
|----------|----------|-------|
| 1 (최고) | Enterprise | 조직 전체 |
| 2 | 개인 (`~/.claude/skills/`) | 모든 프로젝트 |
| 3 (최저) | 프로젝트 (`.claude/skills/`) | 이 프로젝트에만 |

플러그인 스킬은 `plugin-name:skill-name` 네임스페이스를 사용하므로 다른 수준과 충돌할 수 없습니다.

## 모노레포에 이 설계가 효과적인 이유

- **패키지별 스킬이 격리됨** — `packages/frontend/`에서 작업하는 프론트엔드 개발자는 백엔드 스킬이 컨텍스트를 어지럽히지 않고 프론트엔드 특화 스킬을 받습니다.

- **자동 탐색으로 설정 감소** — 패키지 수준 스킬을 명시적으로 등록할 필요 없이 해당 디렉토리의 파일을 작업할 때 자동으로 탐색됩니다.

- **컨텍스트 최적화** — 처음에는 스킬 설명만 로드되고 중첩 스킬은 온디맨드로 탐색됩니다.

- **팀이 자체 스킬을 유지할 수 있음** — 각 패키지 팀은 다른 팀과 조율 없이 자신의 도메인에 특화된 스킬을 정의할 수 있습니다.

## 문자 예산 고려사항

스킬 설명은 문자 예산 (기본값 15,000자)까지 컨텍스트에 로드됩니다. 많은 패키지와 스킬을 가진 대형 모노레포에서는 이 한도에 도달할 수 있습니다.

- `/context`를 실행하여 제외된 스킬에 대한 경고 확인
- `SLASH_COMMAND_TOOL_CHAR_BUDGET` 환경 변수를 설정하여 한도 늘리기

## 모범 사례

1. **공유 워크플로우는 루트 `.claude/skills/`에** — 리포지토리 전체 컨벤션, 커밋 워크플로우, 공유 패턴.

2. **패키지별 스킬은 패키지 `.claude/skills/`에** — 프레임워크 특화 패턴, 컴포넌트 컨벤션, 해당 패키지에 고유한 테스트 유틸리티.

3. **위험한 스킬에는 `disable-model-invocation: true` 사용** — 배포 또는 파괴적인 스킬은 명시적인 사용자 호출이 필요합니다.

4. **스킬 설명을 간결하게 유지** — 설명은 항상 컨텍스트에 있으므로 (문자 예산까지), 장황한 설명은 컨텍스트 공간을 낭비합니다.

5. **스킬 이름에 네임스페이스 사용** — 혼동을 피하기 위해 패키지 이름을 접두사로 사용하는 것을 고려하세요 (예: `frontend-review`, `backend-deploy`).

## 비교: 스킬 vs CLAUDE.md 로딩

| 동작 | CLAUDE.md | 스킬 |
|----------|-----------|--------|
| 상위 로드 (디렉토리 트리 위로) | 예 | 아니오 |
| 중첩/하위 탐색 (디렉토리 트리 아래로) | 예 (지연 로드) | 예 (자동 탐색) |
| 전역 위치 | `~/.claude/CLAUDE.md` | `~/.claude/skills/` |
| 프로젝트 위치 | `.claude/` 또는 리포지토리 루트 | `.claude/skills/` |
| 내용 로딩 | 전체 내용 | 설명만 (호출 시 전체) |

---

## 소스

- [Claude Code 문서 - 스킬로 Claude 확장](https://code.claude.com/docs/en/skills)
- [Claude Code 문서 - 중첩 디렉토리에서의 자동 탐색](https://code.claude.com/docs/en/skills#automatic-discovery-from-nested-directories)
