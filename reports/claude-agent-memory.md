# Claude Code: 에이전트 메모리 프론트매터

서브에이전트를 위한 영속적 메모리 — 에이전트가 세션 전반에서 학습하고, 기억하고, 지식을 쌓을 수 있게 합니다.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 개요

**Claude Code v2.1.33** (2026년 2월)에서 도입된 `memory` 프론트매터 필드는 각 서브에이전트에 자체 영속적 마크다운 기반 지식 저장소를 제공합니다. 이전에는 모든 에이전트 호출이 처음부터 시작되었습니다.

```yaml
---
name: code-reviewer
description: Reviews code for quality and best practices
tools: Read, Write, Edit, Bash
model: sonnet
memory: user
---

You are a code reviewer. As you review code, update your agent memory with
patterns, conventions, and recurring issues you discover.
```

---

## 메모리 범위

| 범위 | 저장 위치 | 버전 관리 | 공유 | 적합한 용도 |
|-------|-----------------|-------------------|--------|----------|
| `user` | `~/.claude/agent-memory/<agent-name>/` | 아니오 | 아니오 | 프로젝트 간 지식 (권장 기본값) |
| `project` | `.claude/agent-memory/<agent-name>/` | 예 | 예 | 팀이 공유해야 하는 프로젝트별 지식 |
| `local` | `.claude/agent-memory-local/<agent-name>/` | 아니오 (git 제외) | 아니오 | 개인적인 프로젝트별 지식 |

이 범위들은 설정 계층구조를 반영합니다 (`~/.claude/settings.json` → `.claude/settings.json` → `.claude/settings.local.json`).

---

## 작동 방식

1. **시작 시**: `MEMORY.md`의 처음 200줄이 에이전트의 시스템 프롬프트에 주입됩니다
2. **도구 접근**: `Read`, `Write`, `Edit`가 자동으로 활성화되어 에이전트가 메모리를 관리할 수 있습니다
3. **실행 중**: 에이전트는 메모리 디렉토리를 자유롭게 읽고/씁니다
4. **큐레이션**: `MEMORY.md`가 200줄을 초과하면 에이전트는 세부 내용을 주제별 파일로 이동합니다

```
~/.claude/agent-memory/code-reviewer/     # user 범위 예시
├── MEMORY.md                              # 기본 파일 (처음 200줄 로드)
├── react-patterns.md                      # 주제별 파일
└── security-checklist.md                  # 주제별 파일
```

---

## 에이전트 메모리 vs 다른 메모리 시스템

| 시스템 | 작성자 | 읽는 주체 | 범위 |
|--------|-----------|-----------|-------|
| **CLAUDE.md** | 직접 (수동) | 메인 Claude + 모든 에이전트 | 프로젝트 |
| **자동 메모리** | 메인 Claude (자동) | 메인 Claude만 | 프로젝트별 사용자별 |
| **`/memory` 커맨드** | 직접 (편집기를 통해) | 메인 Claude만 | 프로젝트별 사용자별 |
| **에이전트 메모리** | 에이전트 자체 | 해당 특정 에이전트만 | 구성 가능 (user/project/local) |

이 시스템들은 **상호 보완적**입니다 — 에이전트는 CLAUDE.md (프로젝트 컨텍스트)와 자체 메모리 (에이전트별 지식) 모두를 읽습니다.

---

## 실용적인 예시

```yaml
---
name: api-developer
description: Implement API endpoints following team conventions
tools: Read, Write, Edit, Bash
model: sonnet
memory: project
skills:
  - api-conventions
  - error-handling-patterns
---

Implement API endpoints. Follow the conventions from your preloaded skills.
As you work, save architectural decisions and patterns to your memory.
```

이것은 **스킬** (시작 시 정적 지식)과 **메모리** (시간이 지남에 따라 구축되는 동적 지식)를 결합합니다.

---

## 팁

- **메모리 사용 프롬프트** — 명시적인 지침을 포함하세요: `"시작하기 전에 메모리를 검토하세요. 완료 후 학습한 내용으로 메모리를 업데이트하세요."`
- 에이전트를 호출할 때 **메모리 확인 요청**: `"이 PR을 검토하고, 이전에 본 패턴을 메모리에서 확인하세요."`
- **적절한 범위 선택** — 프로젝트 간에는 `user`, 팀 공유에는 `project`, 개인적인 것에는 `local`

---

## 소스

- [사용자 정의 서브에이전트 생성 — Claude Code 문서](https://code.claude.com/docs/en/sub-agents)
- [Claude의 메모리 관리 — Claude Code 문서](https://code.claude.com/docs/en/memory)
- [Claude Code v2.1.33 릴리스 노트](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
