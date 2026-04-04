# CLI 시작 플래그 모범 사례

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar%2002%2C%202026-white?style=flat&labelColor=555)

터미널에서 Claude Code를 실행할 때 사용하는 시작 플래그, 최상위 서브커맨드, 시작 환경 변수 참조 문서입니다.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 목차

1. [세션 관리](#session-management)
2. [모델 및 설정](#model--configuration)
3. [권한 및 보안](#permissions--security)
4. [출력 및 형식](#output--format)
5. [시스템 프롬프트](#system-prompt)
6. [에이전트 및 서브에이전트](#agent--subagent)
7. [MCP 및 플러그인](#mcp--plugins)
8. [디렉토리 및 작업 공간](#directory--workspace)
9. [예산 및 제한](#budget--limits)
10. [통합](#integration)
11. [초기화 및 유지보수](#initialization--maintenance)
12. [디버그 및 진단](#debug--diagnostics)
13. [설정 재정의](#settings-override)
14. [버전 및 도움말](#version--help)
15. [서브커맨드](#subcommands)
16. [환경 변수](#environment-variables)

---

## 세션 관리

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--continue` | `-c` | 현재 디렉토리에서 가장 최근 대화를 이어서 진행 |
| `--resume` | `-r` | ID 또는 이름으로 특정 세션을 재개하거나 대화형 선택기 표시 |
| `--from-pr <NUMBER\|URL>` | | 특정 GitHub PR에 연결된 세션을 재개 |
| `--fork-session` | | 재개 시 새 세션 ID 생성 (`--resume` 또는 `--continue`와 함께 사용) |
| `--session-id <UUID>` | | 특정 세션 ID 사용 (유효한 UUID여야 함) |
| `--no-session-persistence` | | 세션 지속성 비활성화 (print 모드 전용) |
| `--remote` | | claude.ai에서 새 웹 세션 생성 |
| `--teleport` | | 웹 세션을 로컬 터미널에서 재개 |

---

## 모델 및 설정

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--model <NAME>` | | 별칭(`sonnet`, `opus`, `haiku`) 또는 전체 모델 ID로 모델 설정 |
| `--fallback-model <NAME>` | | 기본 모델이 과부하 시 자동 폴백 모델 (print 모드 전용) |
| `--betas <LIST>` | | API 요청에 포함할 베타 헤더 (API 키 사용자 전용) |

---

## 권한 및 보안

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--dangerously-skip-permissions` | | 모든 권한 프롬프트 건너뜀. 극도로 주의해서 사용 |
| `--allow-dangerously-skip-permissions` | | 활성화 없이 권한 우회를 옵션으로 활성화 |
| `--permission-mode <MODE>` | | 지정된 권한 모드로 시작: `default`, `plan`, `acceptEdits`, `bypassPermissions` |
| `--allowedTools <TOOLS>` | | 프롬프트 없이 실행되는 도구 (권한 규칙 구문) |
| `--disallowedTools <TOOLS>` | | 모델 컨텍스트에서 완전히 제거된 도구 |
| `--tools <TOOLS>` | | Claude가 사용할 수 있는 내장 도구 제한 (모두 비활성화하려면 `""` 사용) |
| `--permission-prompt-tool <TOOL>` | | 비대화형 모드에서 권한 프롬프트를 처리할 MCP 도구 지정 |

---

## 출력 및 형식

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--print` | `-p` | 대화형 모드 없이 응답 출력 (헤드리스/SDK 모드) |
| `--output-format <FORMAT>` | | 출력 형식: `text`, `json`, `stream-json` |
| `--input-format <FORMAT>` | | 입력 형식: `text`, `stream-json` |
| `--json-schema <SCHEMA>` | | 스키마에 맞는 검증된 JSON 반환 (print 모드 전용) |
| `--include-partial-messages` | | 부분 스트리밍 이벤트 포함 (`--print` 및 `--output-format=stream-json` 필요) |
| `--verbose` | | 턴별 전체 출력을 포함한 상세 로깅 활성화 |

---

## 시스템 프롬프트

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--system-prompt <TEXT>` | | 전체 시스템 프롬프트를 커스텀 텍스트로 교체 |
| `--system-prompt-file <PATH>` | | 파일에서 시스템 프롬프트 로드, 기본값 교체 (print 모드 전용) |
| `--append-system-prompt <TEXT>` | | 기본 시스템 프롬프트에 커스텀 텍스트 추가 |
| `--append-system-prompt-file <PATH>` | | 기본 프롬프트에 파일 내용 추가 (print 모드 전용) |

---

## 에이전트 및 서브에이전트

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--agent <NAME>` | | 현재 세션의 에이전트 지정 |
| `--agents <JSON>` | | JSON으로 커스텀 서브에이전트 동적 정의 |
| `--teammate-mode <MODE>` | | 에이전트 팀 표시 설정: `auto`, `in-process`, `tmux` |

---

## MCP 및 플러그인

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--mcp-config <PATH\|JSON>` | | JSON 파일 또는 문자열에서 MCP 서버 로드 |
| `--strict-mcp-config` | | `--mcp-config`의 MCP 서버만 사용, 나머지 무시 |
| `--plugin-dir <PATH>` | | 이 세션에만 디렉토리에서 플러그인 로드 (반복 가능) |

---

## 디렉토리 및 작업 공간

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--add-dir <PATH>` | | Claude가 접근할 추가 작업 디렉토리 추가 |
| `--worktree` | `-w` | 격리된 git 워크트리에서 Claude 시작 (HEAD에서 분기) |

---

## 예산 및 제한

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--max-budget-usd <AMOUNT>` | | 중지 전 API 호출의 최대 달러 금액 (print 모드 전용) |
| `--max-turns <NUMBER>` | | 에이전틱 턴 수 제한 (print 모드 전용) |

---

## 통합

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--chrome` | | 웹 자동화를 위한 Chrome 브라우저 통합 활성화 |
| `--no-chrome` | | 이 세션의 Chrome 브라우저 통합 비활성화 |
| `--ide` | | 정확히 하나의 유효한 IDE가 사용 가능한 경우 시작 시 자동 연결 |

---

## 초기화 및 유지보수

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--init` | | 초기화 훅 실행 후 대화형 모드 시작 |
| `--init-only` | | 초기화 훅 실행 후 종료 (대화형 세션 없음) |
| `--maintenance` | | 유지보수 훅 실행 후 종료 |

---

## 디버그 및 진단

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--debug <CATEGORIES>` | | 선택적 카테고리 필터링으로 디버그 모드 활성화 (예: `"api,hooks"`) |

---

## 설정 재정의

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--settings <PATH\|JSON>` | | 로드할 설정 JSON 파일 경로 또는 JSON 문자열 |
| `--setting-sources <LIST>` | | 로드할 소스의 쉼표 구분 목록: `user`, `project`, `local` |
| `--disable-slash-commands` | | 이 세션의 모든 스킬 및 슬래시 커맨드 비활성화 |

---

## 버전 및 도움말

| 플래그 | 단축 | 설명 |
|------|-------|-------------|
| `--version` | `-v` | 버전 번호 출력 |
| `--help` | `-h` | 도움말 정보 표시 |

---

## 서브커맨드

`claude <subcommand>` 형식으로 실행하는 최상위 커맨드:

| 서브커맨드 | 설명 |
|------------|-------------|
| `claude` | 대화형 REPL 시작 |
| `claude "query"` | 초기 프롬프트와 함께 REPL 시작 |
| `claude agents` | 설정된 에이전트 목록 표시 |
| `claude auth` | Claude Code 인증 관리 |
| `claude doctor` | 커맨드 라인에서 진단 실행 |
| `claude install` | Claude Code 네이티브 빌드 설치 또는 전환 |
| `claude mcp` | MCP 서버 설정 (`add`, `remove`, `list`, `get`, `enable`) |
| `claude plugin` | Claude Code 플러그인 관리 |
| `claude remote-control` | 원격 제어 세션 관리 |
| `claude setup-token` | 구독 사용을 위한 장기 토큰 생성 |
| `claude update` / `claude upgrade` | 최신 버전으로 업데이트 |

---

## 환경 변수

이 시작 전용 환경 변수는 Claude Code를 실행하기 전에 쉘에서 설정합니다 (`settings.json`으로 설정 불가):

| 변수 | 설명 |
|----------|-------------|
| `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` | 실험적 에이전트 팀 활성화 |
| `CLAUDE_CODE_TMPDIR` | 내부 파일의 임시 디렉토리 재정의. `env` 키로도 설정 가능 — [설정 참조](./claude-settings.md#environment-variables-via-env) 참조 |
| `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1` | 추가 디렉토리 CLAUDE.md 로딩 활성화 |
| `DISABLE_AUTOUPDATER=1` | 자동 업데이트 비활성화 |
| `CLAUDE_CODE_EFFORT_LEVEL` | 사고 깊이 제어 — [설정 참조](./claude-settings.md#environment-variables-via-env) 참조 |
| `USE_BUILTIN_RIPGREP=0` | 내장 대신 시스템 ripgrep 사용 (Alpine Linux) |
| `CLAUDE_CODE_SIMPLE` | 간단 모드 활성화 (Bash + Edit 도구만). `env` 키로도 설정 가능 — [설정 참조](./claude-settings.md#environment-variables-via-env) 참조 |
| `CLAUDE_BASH_NO_LOGIN=1` | BashTool의 로그인 쉘 건너뜀 |

`settings.json`의 `"env"` 키로 설정 가능한 환경 변수 (`MAX_THINKING_TOKENS`, `CLAUDE_CODE_SHELL`, `CLAUDE_CODE_ENABLE_TASKS`, `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS`, `CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS` 등)는 [Claude 설정 참조](./claude-settings.md#environment-variables-via-env)를 참조하세요.

---

## 출처

- [Claude Code CLI 참조](https://code.claude.com/docs/en/cli-reference)
- [Claude Code 헤드리스 모드](https://code.claude.com/docs/en/headless)
- [Claude Code 설정](https://code.claude.com/docs/en/setup)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
- [Claude Code 일반 워크플로우](https://code.claude.com/docs/en/common-workflows)
