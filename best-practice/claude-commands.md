# 커맨드 모범 사례

![Last Updated](https://img.shields.io/badge/Last_Updated-Apr%2002%2C%202026%209%3A14%20PM%20PKT-white?style=flat&labelColor=555)<br>
[![Implemented](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../implementation/claude-commands-implementation.md)

Claude Code 커맨드 — 프론트매터 필드 및 공식 내장 슬래시 커맨드.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 프론트매터 필드 (13개)

| 필드 | 타입 | 필수 여부 | 설명 |
|-------|------|----------|-------------|
| `name` | string | 아니오 | 표시 이름 및 `/slash-command` 식별자. 생략하면 디렉토리 이름이 기본값 |
| `description` | string | 권장 | 커맨드가 하는 일. 자동 완성에 표시되고 Claude의 자동 검색에 사용됨 |
| `argument-hint` | string | 아니오 | 자동 완성 시 표시되는 힌트 (예: `[issue-number]`, `[filename]`) |
| `disable-model-invocation` | boolean | 아니오 | Claude가 이 커맨드를 자동으로 호출하지 못하도록 `true`로 설정 |
| `user-invocable` | boolean | 아니오 | `/` 메뉴에서 숨기려면 `false`로 설정 — 커맨드는 백그라운드 지식으로만 사용됨 |
| `paths` | string/list | 아니오 | 이 스킬이 활성화되는 경우를 제한하는 글로브 패턴. 쉼표로 구분된 문자열 또는 YAML 목록 허용. 설정하면 Claude는 패턴과 일치하는 파일 작업 시에만 스킬을 자동 로드 |
| `allowed-tools` | string | 아니오 | 이 커맨드가 활성화될 때 권한 프롬프트 없이 허용되는 도구 |
| `model` | string | 아니오 | 이 커맨드 실행 시 사용할 모델 (예: `haiku`, `sonnet`, `opus`) |
| `effort` | string | 아니오 | 호출 시 모델 effort 레벨 재정의 (`low`, `medium`, `high`, `max`) |
| `context` | string | 아니오 | 격리된 서브에이전트 컨텍스트에서 커맨드 실행하려면 `fork`로 설정 |
| `agent` | string | 아니오 | `context: fork` 설정 시 서브에이전트 타입 (기본값: `general-purpose`) |
| `shell` | string | 아니오 | `` !`command` `` 블록의 쉘 — `bash` (기본값) 또는 `powershell` 허용. `CLAUDE_CODE_USE_POWERSHELL_TOOL=1` 필요 |
| `hooks` | object | 아니오 | 이 커맨드에 스코프된 라이프사이클 훅 |

---

## ![Official](../!/tags/official.svg) **(64개)**

| # | 커맨드 | 태그 | 설명 |
|---|---------|-----|-------------|
| 1 | `/login` | ![Auth](https://img.shields.io/badge/Auth-2980B9?style=flat) | Anthropic 계정에 로그인 |
| 2 | `/logout` | ![Auth](https://img.shields.io/badge/Auth-2980B9?style=flat) | Anthropic 계정에서 로그아웃 |
| 3 | `/upgrade` | ![Auth](https://img.shields.io/badge/Auth-2980B9?style=flat) | 더 높은 플랜 티어로 전환하는 업그레이드 페이지 열기 |
| 4 | `/color [color\|default]` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 현재 세션의 프롬프트 바 색상 설정. 사용 가능한 색상: `red`, `blue`, `green`, `yellow`, `purple`, `orange`, `pink`, `cyan`. 초기화하려면 `default` 사용 |
| 5 | `/config` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 테마, 모델, 출력 스타일 등 환경설정을 조정하는 Settings 인터페이스 열기. 별칭: `/settings` |
| 6 | `/keybindings` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 키 바인딩 설정 파일 열기 또는 생성 |
| 7 | `/permissions` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 도구 권한의 허용, 요청, 거부 규칙 관리. 스코프별 규칙 조회, 규칙 추가/제거, 작업 디렉토리 관리, 최근 자동 모드 거부 검토 가능한 대화형 다이얼로그 열기. 별칭: `/allowed-tools` |
| 8 | `/privacy-settings` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 개인정보 설정 조회 및 업데이트. Pro 및 Max 플랜 구독자에게만 제공 |
| 9 | `/sandbox` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 샌드박스 모드 전환. 지원되는 플랫폼에서만 사용 가능 |
| 10 | `/statusline` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | Claude Code의 상태 표시줄 설정. 원하는 내용을 설명하거나 인수 없이 실행하여 쉘 프롬프트에서 자동 설정 |
| 11 | `/stickers` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | Claude Code 스티커 주문 |
| 12 | `/terminal-setup` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | Shift+Enter 및 기타 단축키에 대한 터미널 키 바인딩 설정. VS Code, Alacritty, Warp 같은 필요한 터미널에서만 표시 |
| 13 | `/theme` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 색상 테마 변경. 라이트 및 다크 변형, 색맹 접근성 (달토나이즈) 테마, 터미널의 색상 팔레트를 사용하는 ANSI 테마 포함 |
| 14 | `/vim` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | Vim과 Normal 편집 모드 간 전환 |
| 15 | `/voice` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 푸시-투-토크 음성 받아쓰기 전환. Claude.ai 계정 필요 |
| 16 | `/context` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 현재 컨텍스트 사용량을 색상 그리드로 시각화. 컨텍스트가 많은 도구, 메모리 팽창, 용량 경고에 대한 최적화 제안 표시 |
| 17 | `/cost` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 토큰 사용 통계 표시. 구독별 비용 추적 안내는 비용 추적 가이드 참조 |
| 18 | `/extra-usage` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 속도 제한 도달 시 계속 작업할 추가 사용량 설정 |
| 19 | `/insights` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 프로젝트 영역, 상호 작용 패턴, 마찰 지점을 포함한 Claude Code 세션 분석 보고서 생성 |
| 20 | `/stats` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 일일 사용량, 세션 기록, 연속 기록, 모델 환경설정 시각화 |
| 21 | `/status` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 버전, 모델, 계정, 연결 상태를 표시하는 Settings 인터페이스 (Status 탭) 열기. 현재 응답을 기다리지 않고 Claude가 응답하는 동안에도 작동 |
| 22 | `/usage` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 플랜 사용 한도 및 속도 제한 상태 표시 |
| 23 | `/doctor` | ![Debug](https://img.shields.io/badge/Debug-E74C3C?style=flat) | Claude Code 설치 및 설정 진단 및 확인 |
| 24 | `/feedback [report]` | ![Debug](https://img.shields.io/badge/Debug-E74C3C?style=flat) | Claude Code에 대한 피드백 제출. 별칭: `/bug` |
| 25 | `/help` | ![Debug](https://img.shields.io/badge/Debug-E74C3C?style=flat) | 도움말 및 사용 가능한 커맨드 표시 |
| 26 | `/release-notes` | ![Debug](https://img.shields.io/badge/Debug-E74C3C?style=flat) | 전체 변경 로그 표시, 가장 최근 버전이 프롬프트에 가장 가깝게 표시 |
| 27 | `/tasks` | ![Debug](https://img.shields.io/badge/Debug-E74C3C?style=flat) | 백그라운드 태스크 목록 조회 및 관리. 별칭: `/bashes` |
| 28 | `/copy [N]` | ![Export](https://img.shields.io/badge/Export-7F8C8D?style=flat) | 마지막 어시스턴트 응답을 클립보드에 복사. 숫자 `N`을 전달하여 N번째 최신 응답 복사: `/copy 2`는 두 번째 최신 응답 복사. 코드 블록이 있으면 개별 블록 또는 전체 응답을 선택하는 대화형 선택기 표시. 선택기에서 `w`를 눌러 클립보드 대신 파일에 저장 — SSH 사용 시 유용 |
| 29 | `/export [filename]` | ![Export](https://img.shields.io/badge/Export-7F8C8D?style=flat) | 현재 대화를 일반 텍스트로 내보내기. 파일명을 지정하면 해당 파일에 직접 저장. 없으면 클립보드 복사 또는 파일 저장 다이얼로그 열기 |
| 30 | `/agents` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | 에이전트 설정 관리 |
| 31 | `/chrome` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | Claude in Chrome 설정 구성 |
| 32 | `/hooks` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | 도구 이벤트에 대한 훅 설정 조회 |
| 33 | `/ide` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | IDE 통합 관리 및 상태 표시 |
| 34 | `/mcp` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | MCP 서버 연결 및 OAuth 인증 관리 |
| 35 | `/plugin` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | Claude Code 플러그인 관리 |
| 36 | `/reload-plugins` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | 재시작 없이 대기 중인 변경사항 적용을 위해 모든 활성 플러그인 재로드. 재로드된 각 구성 요소 수를 보고하고 로드 오류 표시 |
| 37 | `/skills` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | 사용 가능한 스킬 목록 표시 |
| 38 | `/memory` | ![Memory](https://img.shields.io/badge/Memory-3498DB?style=flat) | `CLAUDE.md` 메모리 파일 편집, 자동 메모리 활성화/비활성화, 자동 메모리 항목 조회 |
| 39 | `/effort [low\|medium\|high\|max\|auto]` | ![Model](https://img.shields.io/badge/Model-E67E22?style=flat) | 모델 effort 레벨 설정. `low`, `medium`, `high`는 세션 간 유지됨. `max`는 현재 세션에만 적용되며 Opus 4.6 필요. `auto`는 모델 기본값으로 초기화. 인수 없이 실행하면 현재 레벨 표시. 현재 응답을 기다리지 않고 즉시 적용 |
| 40 | `/fast [on\|off]` | ![Model](https://img.shields.io/badge/Model-E67E22?style=flat) | 빠른 모드 켜기/끄기 |
| 41 | `/model [model]` | ![Model](https://img.shields.io/badge/Model-E67E22?style=flat) | AI 모델 선택 또는 변경. 지원하는 모델의 경우 ← → 화살표로 effort 레벨 조정. 현재 응답을 기다리지 않고 즉시 적용 |
| 42 | `/passes` | ![Model](https://img.shields.io/badge/Model-E67E22?style=flat) | 친구에게 Claude Code 무료 1주일 공유. 계정이 적격한 경우에만 표시 |
| 43 | `/plan [description]` | ![Model](https://img.shields.io/badge/Model-E67E22?style=flat) | 프롬프트에서 직접 플랜 모드 진입. 선택적 설명을 전달하여 해당 태스크로 즉시 시작 (예: `/plan fix the auth bug`) |
| 44 | `/add-dir <path>` | ![Project](https://img.shields.io/badge/Project-27AE60?style=flat) | 현재 세션에 새 작업 디렉토리 추가 |
| 45 | `/diff` | ![Project](https://img.shields.io/badge/Project-27AE60?style=flat) | 커밋되지 않은 변경사항 및 턴별 diff를 표시하는 대화형 diff 뷰어 열기. ← → 화살표로 현재 git diff와 개별 Claude 턴 간 전환, ↑ ↓로 파일 탐색 |
| 46 | `/init` | ![Project](https://img.shields.io/badge/Project-27AE60?style=flat) | `CLAUDE.md` 가이드로 프로젝트 초기화. 스킬, 훅, 개인 메모리 파일도 안내하는 대화형 플로우를 위해 `CLAUDE_CODE_NEW_INIT=1` 설정 |
| 47 | `/pr-comments [PR]` | ![Project](https://img.shields.io/badge/Project-27AE60?style=flat) | GitHub 풀 리퀘스트 댓글 가져와 표시. 현재 브랜치의 PR 자동 감지, 또는 PR URL이나 번호 전달. `gh` CLI 필요 |
| 48 | `/review` | ![Project](https://img.shields.io/badge/Project-27AE60?style=flat) | 더 이상 사용되지 않음. 대신 `code-review` 플러그인 설치: `claude plugin install code-review@claude-plugins-official` |
| 49 | `/security-review` | ![Project](https://img.shields.io/badge/Project-27AE60?style=flat) | 현재 브랜치의 대기 중인 변경사항에서 보안 취약점 분석. git diff를 검토하여 인젝션, 인증 문제, 데이터 노출 같은 위험 식별 |
| 50 | `/desktop` | ![Remote](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | Claude Code Desktop 앱에서 현재 세션 계속하기. macOS 및 Windows 전용. 별칭: `/app` |
| 51 | `/install-github-app` | ![Remote](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 리포지토리에 Claude GitHub Actions 앱 설치. 리포지토리 선택 및 통합 설정 안내 |
| 52 | `/install-slack-app` | ![Remote](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | Claude Slack 앱 설치. OAuth 플로우 완료를 위해 브라우저 열기 |
| 53 | `/mobile` | ![Remote](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | Claude 모바일 앱 다운로드 QR 코드 표시. 별칭: `/ios`, `/android` |
| 54 | `/remote-control` | ![Remote](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | claude.ai에서 원격 제어를 위해 이 세션 공개. 별칭: `/rc` |
| 55 | `/remote-env` | ![Remote](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | `--remote`로 시작된 웹 세션의 기본 원격 환경 설정 |
| 56 | `/schedule [description]` | ![Remote](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 클라우드 예약 태스크 생성, 업데이트, 목록, 실행. Claude가 대화 방식으로 설정 안내 |
| 57 | `/branch [name]` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 이 시점에서 현재 대화의 브랜치 생성. 별칭: `/fork` |
| 58 | `/btw <question>` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 대화에 추가하지 않고 빠른 부가 질문 |
| 59 | `/clear` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 대화 기록 지우기 및 컨텍스트 확보. 별칭: `/reset`, `/new` |
| 60 | `/compact [instructions]` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 선택적 포커스 지시사항으로 대화 압축 |
| 61 | `/exit` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | CLI 종료. 별칭: `/quit` |
| 62 | `/rename [name]` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 현재 세션 이름 변경 및 프롬프트 바에 이름 표시. 이름 없이 실행하면 대화 기록에서 자동 생성 |
| 63 | `/resume [session]` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | ID 또는 이름으로 대화 재개, 또는 세션 선택기 열기. 별칭: `/continue` |
| 64 | `/rewind` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 대화 및/또는 코드를 이전 시점으로 되돌리거나 선택한 메시지부터 요약. 체크포인팅 참조. 별칭: `/checkpoint` |

`/debug` 같은 번들 스킬도 슬래시 커맨드 메뉴에 나타날 수 있지만 내장 커맨드는 아닙니다.

---

## 출처

- [Claude Code 슬래시 커맨드](https://code.claude.com/docs/en/slash-commands)
- [Claude Code 대화형 모드](https://code.claude.com/docs/en/interactive-mode)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
