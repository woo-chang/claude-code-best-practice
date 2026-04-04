# 스킬 모범 사례

![Last Updated](https://img.shields.io/badge/Last_Updated-Apr%2002%2C%202026%209%3A11%20PM%20PKT-white?style=flat&labelColor=555)<br>
[![Implemented](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../implementation/claude-skills-implementation.md)

Claude Code 스킬 — 프론트매터 필드와 공식 번들 스킬.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 프론트매터 필드 (13)

| 필드 | 유형 | 필수 여부 | 설명 |
|-------|------|----------|-------------|
| `name` | string | 아니오 | 표시 이름 및 `/slash-command` 식별자. 생략하면 디렉토리 이름이 기본값 |
| `description` | string | 권장 | 스킬이 하는 일. 자동완성에 표시되며 Claude의 자동 검색에 사용됨 |
| `argument-hint` | string | 아니오 | 자동완성 중 표시되는 힌트 (예: `[issue-number]`, `[filename]`) |
| `disable-model-invocation` | boolean | 아니오 | Claude가 이 스킬을 자동으로 호출하지 않도록 하려면 `true`로 설정 |
| `user-invocable` | boolean | 아니오 | `/` 메뉴에서 숨기려면 `false`로 설정 — 스킬이 에이전트 사전 로딩을 위한 배경 지식이 됨 |
| `allowed-tools` | string | 아니오 | 이 스킬이 활성화될 때 권한 프롬프트 없이 허용되는 도구 |
| `model` | string | 아니오 | 이 스킬 실행 시 사용할 모델 (예: `haiku`, `sonnet`, `opus`) |
| `effort` | string | 아니오 | 호출 시 모델 노력 수준 재정의 (`low`, `medium`, `high`, `max`) |
| `context` | string | 아니오 | 격리된 서브에이전트 컨텍스트에서 스킬을 실행하려면 `fork`로 설정 |
| `agent` | string | 아니오 | `context: fork`가 설정되었을 때 서브에이전트 유형 (기본값: `general-purpose`) |
| `hooks` | object | 아니오 | 이 스킬에 범위 지정된 라이프사이클 훅 |
| `paths` | string/list | 아니오 | 스킬이 자동 활성화되는 시기를 제한하는 Glob 패턴. 쉼표로 구분된 문자열 또는 YAML 목록 허용 — Claude는 일치하는 파일을 작업할 때만 스킬을 로드합니다 |
| `shell` | string | 아니오 | `` !`command` `` 블록의 셸 — `bash` (기본값) 또는 `powershell`. `CLAUDE_CODE_USE_POWERSHELL_TOOL=1` 필요 |

---

## ![Official](../!/tags/official.svg) **(5)**

| # | 스킬 | 설명 |
|---|-------|-------------|
| 1 | `simplify` | 변경된 코드를 재사용성, 품질, 효율성 측면에서 검토 — 중복 제거를 위해 리팩토링 |
| 2 | `batch` | 여러 파일에 걸쳐 커맨드를 일괄 실행 |
| 3 | `debug` | 실패하는 커맨드나 코드 문제 디버깅 |
| 4 | `loop` | 반복 간격으로 프롬프트 또는 슬래시 커맨드 실행 (최대 3일) |
| 5 | `claude-api` | Claude API 또는 Anthropic SDK로 앱 구축 — `anthropic` / `@anthropic-ai/sdk` import에서 트리거 |

참조: 커뮤니티가 유지 관리하는 설치 가능한 스킬은 [공식 스킬 리포지토리](https://github.com/anthropics/skills/tree/main/skills)를 참조하세요.

---

## 소스

- [Claude Code 스킬 — 문서](https://code.claude.com/docs/en/skills)
- [모노레포에서 스킬 검색](../reports/claude-skills-for-larger-mono-repos.md)
- [Claude Code 변경로그](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
