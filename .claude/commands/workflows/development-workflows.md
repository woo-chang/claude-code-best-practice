---
description: 9개의 워크플로우 리포지토리를 병렬로 조사하여 DEVELOPMENT WORKFLOWS 테이블을 업데이트합니다
---

# 워크플로우 — 개발 워크플로우

9개의 리포지토리를 병렬로 조사하여 `README.md`의 DEVELOPMENT WORKFLOWS 테이블을 업데이트합니다. 에이전트를 실행하고, 결과를 합치고, 변경사항을 제시하고, 승인 시 테이블을 업데이트합니다.

---

## 9개 리포지토리

| # | Repo | Owner |
|---|------|-------|
| 1 | `github/spec-kit` | GitHub (John Lam / Den Delimarsky) |
| 2 | `Fission-AI/OpenSpec` | Fission-AI (@0xTab) |
| 3 | `humanlayer/humanlayer` | HumanLayer (Dex Horthy) |
| 4 | `affaan-m/everything-claude-code` | Affaan Mustafa |
| 5 | `gsd-build/get-shit-done` | Lex Christopherson |
| 6 | `obra/superpowers` | Jesse Vincent |
| 7 | `garrytan/gstack` | Garry Tan (YC CEO) |
| 8 | `bmad-code-org/BMAD-METHOD` | BMAD Code Org |
| 9 | `EveryInc/compound-engineering-plugin` | Every.to |

---

## 테이블 형식

README 테이블에는 다음 열이 있습니다:

```markdown
| Name | ★ | Uniqueness | Plan | <img src="!/tags/a.svg" height="14"> | <img src="!/tags/c.svg" height="14"> | <img src="!/tags/s.svg" height="14"> |
```

- **Name**: `[Short Name](github-url)` — 소유자/리포지토리가 아닌 프로젝트 이름 사용
- **★**: `k` 단위로 반올림한 스타 수 (예: 98k, 10k, 4.1k). 1000 미만이면 정확한 수치 표시
- **Uniqueness**: `![tag](https://img.shields.io/badge/TAG-ddf4ff)`를 사용하는 2-3개의 shields.io 배지 태그. 공백에는 밑줄, 하이픈에는 `--`, `+`에는 `%2B`, `/`에는 `%2F`
- **Plan**: 계획 구현의 아이콘 + 연결된 이름. 커맨드는 `<img src="!/tags/c.svg" height="14">`, 에이전트는 `<img src="!/tags/a.svg" height="14">`, 스킬은 `<img src="!/tags/s.svg" height="14">`. 이름은 리포지토리의 실제 파일에 연결
- **에이전트/커맨드/스킬 수**: 숫자만 (예: `25`, `0`, `108+`)

**정렬 순서**: 스타 내림차순 (가장 높은 것이 먼저). 계획 유형으로 그룹화하지 마세요.

---

## 0단계: 현재 상태 읽기

다음 파일들을 읽으세요:

1. `README.md` — `## ⚙️ DEVELOPMENT WORKFLOWS` 테이블 (현재 스타, 태그, 계획 링크, 수를 기록)
2. `changelog/development-workflows/changelog.md` — 이전 체인지로그 항목

---

## 1단계: 2개의 리서치 에이전트 실행

**즉시** 두 에이전트를 **단일 메시지** (병렬)로 실행하세요. 각각 `subagent_type: "development-workflows-research-agent"`를 사용합니다.

### 에이전트 1 (3개 리포지토리)

> 다음 3개의 Claude Code 워크플로우 리포지토리를 조사하세요:
>
> **리포지토리 1: github/spec-kit** (https://github.com/github/spec-kit)
> **리포지토리 2: affaan-m/everything-claude-code** (https://github.com/affaan-m/everything-claude-code)
> **리포지토리 3: obra/superpowers** (https://github.com/obra/superpowers)
>
> 각 리포지토리에 대해 다음을 반환하세요:
>
> 1. **스타** — GitHub API `https://api.github.com/repos/{owner}/{repo}`를 사용하고, `stargazers_count`를 읽으세요. `k`로 반올림.
> 2. **에이전트 수** — `agents/` 또는 `.claude/agents/`의 `.md` 파일을 세세요. obra의 경우 스킬에 의해 디스패치된 암묵적 서브에이전트도 세세요.
> 3. **스킬 수** — `skills/` 또는 `.claude/skills/`의 폴더를 세세요.
> 4. **커맨드 수** — `commands/` 또는 `.claude/commands/`의 `.md` 파일을 세세요. spec-kit의 경우 `templates/commands/`의 파일을 세세요.
> 5. **계획 구현** — 계획/계획 에이전트, 스킬, 또는 커맨드를 찾으세요. 이름, 유형 (에이전트/스킬/커맨드), 파일 경로를 반환하세요.
> 6. **독창성 태그** — 이 워크플로우를 독특하게 만드는 것을 담은 2-3개의 짧은 태그 (각 2-3 단어).
> 7. **주목할 만한 변경사항** — 최근 중요한 변경사항이 있나요? 새 에이전트/스킬/커맨드, 주요 버전?
>
> 리포지토리별 구조화된 보고서 반환:
> ```
> REPO: github/spec-kit
> STARS: <number>k
> AGENTS: <count>
> COMMANDS: <count>
> SKILLS: <count>
> PLAN: <name> (<type>) — <file-path>
> TAGS: <tag1>, <tag2>, <tag3>
> CHANGES: <changes or "No significant changes">
> ```

### 에이전트 2 (6개 리포지토리)

> 다음 6개의 Claude Code 워크플로우 리포지토리를 조사하세요:
>
> **리포지토리 1: Fission-AI/OpenSpec** (https://github.com/Fission-AI/OpenSpec)
> **리포지토리 2: humanlayer/humanlayer** (https://github.com/humanlayer/humanlayer)
> **리포지토리 3: gsd-build/get-shit-done** (https://github.com/gsd-build/get-shit-done)
> **리포지토리 4: garrytan/gstack** (https://github.com/garrytan/gstack)
> **리포지토리 5: bmad-code-org/BMAD-METHOD** (https://github.com/bmad-code-org/BMAD-METHOD)
> **리포지토리 6: EveryInc/compound-engineering-plugin** (https://github.com/EveryInc/compound-engineering-plugin)
>
> 각 리포지토리에 대해 다음을 반환하세요:
>
> 1. **스타** — GitHub API `https://api.github.com/repos/{owner}/{repo}`를 사용하고, `stargazers_count`를 읽으세요. `k`로 반올림.
> 2. **에이전트 수** — `agents/` 또는 `.claude/agents/`의 `.md` 파일을 세세요. BMAD의 경우 `src/bmm-skills/`의 에이전트 페르소나 스킬을 세세요. compound-engineering-plugin의 경우 `plugins/compound-engineering/agents/`의 모든 하위 디렉토리의 `.md` 파일을 세세요.
> 3. **스킬 수** — `skills/` 또는 `.claude/skills/`의 폴더를 세세요. gstack의 경우 스킬은 SKILL.md가 있는 루트 레벨 디렉토리입니다. BMAD의 경우 `src/bmm-skills/` 및 `src/core-skills/`의 모든 스킬을 세세요. compound-engineering-plugin의 경우 `plugins/compound-engineering/skills/`와 `plugins/coding-tutor/skills/`의 폴더를 세세요.
> 4. **커맨드 수** — `commands/` 또는 `.claude/commands/`의 `.md` 파일을 세세요. GSD의 경우 `commands/gsd/`에서 세세요. OpenSpec의 경우 `/opsx:*` 커맨드를 세세요. BMAD의 경우 수는 0 (커맨드는 설치 시 생성됨). compound-engineering-plugin의 경우 `.claude/commands/`와 `plugins/coding-tutor/commands/`의 `.md` 파일을 세세요.
> 5. **계획 구현** — 계획/계획 에이전트, 스킬, 또는 커맨드를 찾으세요. 이름, 유형 (에이전트/스킬/커맨드), 파일 경로를 반환하세요.
> 6. **독창성 태그** — 이 워크플로우를 독특하게 만드는 것을 담은 2-3개의 짧은 태그 (각 2-3 단어).
> 7. **주목할 만한 변경사항** — 최근 중요한 변경사항이 있나요? 새 에이전트/스킬/커맨드, 주요 버전?
>
> 리포지토리별 구조화된 보고서 반환:
> ```
> REPO: Fission-AI/OpenSpec
> STARS: <number>k
> AGENTS: <count>
> COMMANDS: <count>
> SKILLS: <count>
> PLAN: <name> (<type>) — <file-path>
> TAGS: <tag1>, <tag2>, <tag3>
> CHANGES: <changes or "No significant changes">
> ```

---

## 2단계: 비교 및 보고서

**두 에이전트를 기다리세요.** 그런 다음 결과를 현재 테이블과 비교하고 다음을 제시하세요:

```
Development Workflows — Update Report
══════════════════════════════════════

Changes Found:
  <repo>: ★ <old>k → <new>k | agents <old>→<new> | commands <old>→<new> | skills <old>→<new>
  <repo>: tags updated: <old tags> → <new tags>
  <repo>: Plan link changed: <old> → <new>
  ...

No Changes:
  <repo>: ✓ (all values match)
  ...

Action Items:
#  | Type        | Action                              | Status
1  | Star        | Update <repo> ★ from Xk to Yk       | NEW/RECURRING
2  | Count       | Update <repo> agents from X to Y     | NEW/RECURRING
3  | Tags        | Update <repo> tags                   | NEW/RECURRING
4  | Plan        | Update <repo> Plan link              | NEW/RECURRING
5  | Sort        | Move <repo> (Plan type changed)      | NEW/RECURRING
```

이전 체인지로그 항목과 비교하고 항목을 `NEW`, `RECURRING`, 또는 `RESOLVED`로 표시하세요.

---

## 2.5단계: 체인지로그에 추가

**필수** — 사용자에게 제시하기 전에 항상 실행하세요.

`changelog/development-workflows/changelog.md`를 읽은 다음 새 항목을 **추가**하세요. 파일이 없으면 상태 범례와 함께 생성한 후 첫 번째 항목을 추가하세요.

```markdown
---

## [<YYYY-MM-DD HH:MM AM/PM PKT>] Development Workflows Update

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH/MED/LOW | <type> | <action> | <status> |
```

`TZ=Asia/Karachi date "+%Y-%m-%d %I:%M %p PKT"`를 통해 시간을 가져오세요. 상태는 다음 중 하나여야 합니다:
- `COMPLETE (reason)` | `INVALID (reason)` | `ON HOLD (reason)`

항상 추가하고, 절대 덮어쓰지 마세요.

---

## 2.6단계: 마지막 업데이트 배지 업데이트

**필수** — 2.5단계 후 실행하세요.

`README.md` 4번째 줄의 배지를 업데이트하세요. `TZ=Asia/Karachi date "+%b %d, %Y %-I:%M %p PKT"`를 통해 시간을 가져오고, URL 인코딩하여 배지의 날짜를 교체하세요. 이것을 실행 항목으로 기록하지 마세요.

---

## 3단계: 실행

사용자에게 묻습니다: **(1) 모두 실행** | **(2) 특정 실행** | **(3) 건너뜀**

실행 시 `README.md`의 `## ⚙️ DEVELOPMENT WORKFLOWS` 테이블을 편집하세요:
- 행별 스타, 태그, 계획 링크, 수를 업데이트하세요
- 정렬 순서 유지: 스타 내림차순 (가장 높은 것이 먼저). 계획 유형으로 그룹화하지 마세요
- 기존 형식과 정확히 일치하세요 (아이콘, 배지 URL, 링크 스타일)

---

## 규칙

1. **두 에이전트를 병렬로 실행** — 단일 메시지, 순차적으로 절대 실행하지 마세요
2. **추측하지 마세요** — 에이전트 데이터만 사용
3. **자동 실행하지 마세요** — 먼저 보고서를 제시하고, 승인 기다리기
4. **항상 체인지로그에 추가** 및 **항상 배지 업데이트** — 필수
5. **스타 내림차순 정렬** — 가장 높은 스타가 먼저, 계획 유형으로 그룹화하지 마세요
6. **태그는 shields.io 사용** — `![tag](https://img.shields.io/badge/TAG-ddf4ff)` (공백에는 `_`, 하이픈에는 `--`)
7. **계획 링크는 실제 파일을 가리켜야 합니다** — 리포지토리 루트가 아님
8. **에이전트, 커맨드, 스킬은 다릅니다** — 각각의 디렉토리에서 세세요, 혼동하지 마세요
9. **스타는 일관되게 반올림** — `k` 접미사 (98k, 10k, 4.1k). 1000 미만이면 정확한 수치
10. **이전 체인지로그와 비교** — 항목을 NEW, RECURRING, 또는 RESOLVED로 표시하세요
