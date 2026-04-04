# Claude 메모리

CLAUDE.md 파일을 통한 영속적 컨텍스트 — 작성 방법과 모노레포에서의 로딩 방식.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 1. 좋은 CLAUDE.md 작성하기

잘 구조화된 CLAUDE.md는 프로젝트에서 Claude Code의 출력을 개선하는 가장 효과적인 방법입니다. Humanlayer에 무엇을 포함할지, 어떻게 구조화할지, 흔한 함정에 관한 훌륭한 가이드가 있습니다.

- [Humanlayer - Writing a good Claude.md](https://www.humanlayer.dev/blog/writing-a-good-claude-md)

---

## 2. 대규모 모노레포의 CLAUDE.md

모노레포에서 Claude Code를 사용할 때, CLAUDE.md 파일이 컨텍스트에 어떻게 로드되는지 이해하는 것은 프로젝트 지시사항을 효과적으로 구성하는 데 중요합니다.

<p align="center">
  <a href="https://x.com/bcherny/status/2016339448863355206"><img src="assets/claude-memory/claude-memory-monorepo.jpg" alt="모노레포에서의 CLAUDE.md 로딩" width="600"></a>
</p>

### 두 가지 로딩 메커니즘

Claude Code는 CLAUDE.md 파일을 로드하는 두 가지 고유한 메커니즘을 사용합니다:

#### 상위 디렉토리 로딩 (디렉토리 트리 위쪽)

Claude Code를 시작하면 현재 작업 디렉토리에서 파일시스템 루트 방향으로 **위쪽**을 탐색하며 발견한 모든 CLAUDE.md 파일을 로드합니다. 이 파일들은 **시작 시 즉시 로드**됩니다.

#### 하위 디렉토리 로딩 (디렉토리 트리 아래쪽)

현재 작업 디렉토리 아래 하위 디렉토리의 CLAUDE.md 파일은 **시작 시 로드되지 않습니다**. 세션 중 해당 하위 디렉토리의 파일을 읽을 때만 포함됩니다. 이를 **지연 로딩**이라고 합니다.

### 모노레포 구조 예시

서로 다른 컴포넌트를 위한 별도 디렉토리가 있는 일반적인 모노레포를 고려해보세요:

```
/mymonorepo/
├── CLAUDE.md          # 루트 수준 지시사항 (모든 컴포넌트에 공유)
├── frontend/
│   └── CLAUDE.md      # 프론트엔드별 지시사항
├── backend/
│   └── CLAUDE.md      # 백엔드별 지시사항
└── api/
    └── CLAUDE.md      # API별 지시사항
```

### 시나리오 1: 루트 디렉토리에서 Claude Code 실행

`/mymonorepo/`에서 Claude Code를 실행할 때:

```bash
cd /mymonorepo
claude
```

| 파일 | 시작 시 로드? | 이유 |
|------|-------------------|--------|
| `/mymonorepo/CLAUDE.md` | 예 | 현재 작업 디렉토리 |
| `/mymonorepo/frontend/CLAUDE.md` | 아니오 | `frontend/`의 파일 읽기/편집 시에만 로드 |
| `/mymonorepo/backend/CLAUDE.md` | 아니오 | `backend/`의 파일 읽기/편집 시에만 로드 |
| `/mymonorepo/api/CLAUDE.md` | 아니오 | `api/`의 파일 읽기/편집 시에만 로드 |

### 시나리오 2: 컴포넌트 디렉토리에서 Claude Code 실행

`/mymonorepo/frontend/`에서 Claude Code를 실행할 때:

```bash
cd /mymonorepo/frontend
claude
```

| 파일 | 시작 시 로드? | 이유 |
|------|-------------------|--------|
| `/mymonorepo/CLAUDE.md` | 예 | 상위 디렉토리 |
| `/mymonorepo/frontend/CLAUDE.md` | 예 | 현재 작업 디렉토리 |
| `/mymonorepo/backend/CLAUDE.md` | 아니오 | 디렉토리 트리의 다른 브랜치 |
| `/mymonorepo/api/CLAUDE.md` | 아니오 | 디렉토리 트리의 다른 브랜치 |

### 핵심 요약

1. **상위 디렉토리는 항상 시작 시 로드됨** — Claude는 디렉토리 트리를 위쪽으로 탐색하며 발견한 모든 CLAUDE.md 파일을 로드합니다. 루트 수준, 리포지토리 전체 지시사항에 항상 접근할 수 있습니다.

2. **하위 디렉토리는 지연 로딩됨** — 하위 디렉토리 CLAUDE.md 파일은 해당 하위 디렉토리의 파일과 상호 작용할 때만 로드됩니다. 관련 없는 컨텍스트로 세션이 부풀려지는 것을 방지합니다.

3. **형제 디렉토리는 로드되지 않음** — `frontend/`에서 작업 중이면 `backend/CLAUDE.md` 또는 `api/CLAUDE.md`는 컨텍스트에 로드되지 않습니다.

4. **전역 CLAUDE.md** — 홈 폴더의 `~/.claude/CLAUDE.md`에 배치할 수 있으며, 프로젝트와 관계없이 모든 Claude Code 세션에 적용됩니다.

### 이 설계가 모노레포에 적합한 이유

- **공유 지시사항이 아래로 전파됨** — 루트 수준 CLAUDE.md에는 어디에나 적용되는 리포지토리 전체 규칙, 코딩 표준, 일반 패턴이 포함됩니다.

- **컴포넌트별 지시사항이 격리됨** — 프론트엔드 개발자는 컨텍스트를 어지럽히는 백엔드별 지시사항이 필요 없고, 그 반대도 마찬가지입니다.

- **컨텍스트가 최적화됨** — 하위 CLAUDE.md 파일을 지연 로딩함으로써 Claude Code는 시작 시 잠재적으로 수백 킬로바이트의 관련 없는 지시사항을 로드하는 것을 방지합니다.

### 모범 사례

1. **공유 규칙은 루트 CLAUDE.md에** — 코딩 표준, 커밋 메시지 형식, PR 템플릿, 기타 리포지토리 전체 가이드라인.

2. **컴포넌트별 지시사항은 컴포넌트 CLAUDE.md에** — 프레임워크별 패턴, 컴포넌트 아키텍처, 해당 컴포넌트만의 테스트 규칙.

3. **개인 환경설정에는 CLAUDE.local.md 사용** — 팀과 공유하면 안 되는 지시사항을 위해 `.gitignore`에 추가.

---

## 출처

- [Claude Code 문서 - 메모리 조회 방법](https://code.claude.com/docs/en/memory#how-claude-looks-up-memories)
- [Boris Cherny on X - CLAUDE.md 로딩 설명](https://x.com/bcherny/status/2016339448863355206)
- [Humanlayer - Writing a good Claude.md](https://www.humanlayer.dev/blog/writing-a-good-claude-md)
