# Claude Code 커스터마이즈 12가지 방법 — Boris Cherny의 팁

Claude Code 창시자 Boris Cherny ([@bcherny](https://x.com/bcherny))가 2026년 2월 12일에 공유한 커스터마이즈 팁 요약.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 배경

Boris Cherny는 커스터마이즈 가능성이 엔지니어들이 Claude Code에서 가장 좋아하는 것 중 하나라고 강조했습니다 — 훅, 플러그인, LSP, MCP, 스킬, 노력 수준, 커스텀 에이전트, 상태 표시줄, 출력 스타일 등. 그는 개발자와 팀이 설정을 커스터마이즈하는 12가지 실용적인 방법을 공유했습니다.

<a href="https://x.com/bcherny/status/2021699851499798911"><img src="assets/boris-12-feb-26/0.webp" alt="Boris Cherny 소개 트윗" width="50%" /></a>

---

## 1/ 터미널 구성

최상의 Claude Code 경험을 위해 터미널을 설정하세요:

- **테마**: `/config`를 실행하여 라이트/다크 모드 설정
- **알림**: iTerm2용 알림 활성화, 또는 커스텀 알림 훅 사용
- **개행**: IDE 터미널, Apple Terminal, Warp, 또는 Alacritty에서 Claude Code를 사용하는 경우 `/terminal-setup`을 실행하여 개행에 shift+enter를 활성화 (`\`를 입력할 필요 없음)
- **Vim 모드**: `/vim` 실행

<a href="https://x.com/bcherny/status/2021699859359883608"><img src="assets/boris-12-feb-26/1.webp" alt="터미널 구성" width="50%" /></a>

---

## 2/ 노력 수준 조정

`/model`을 실행하여 선호하는 노력 수준을 선택하세요:

- **Low** — 적은 토큰, 빠른 응답
- **Medium** — 균형 잡힌 동작
- **High** — 더 많은 토큰, 더 높은 지능

Boris의 선호: 모든 것에 High.

<a href="https://x.com/bcherny/status/2021699860869902424"><img src="assets/boris-12-feb-26/2.webp" alt="노력 수준 조정" width="50%" /></a>

---

## 3/ 플러그인, MCP, 스킬 설치

플러그인으로 LSP (모든 주요 언어 지원), MCP, 스킬, 에이전트, 커스텀 훅을 설치할 수 있습니다.

공식 Anthropic 플러그인 마켓플레이스에서 설치하거나, 회사만의 마켓플레이스를 만드세요. `settings.json`을 코드베이스에 체크인하여 팀용 마켓플레이스를 자동으로 추가하세요.

`/plugin`을 실행하여 시작하세요.

<a href="https://x.com/bcherny/status/2021699862522364149"><img src="assets/boris-12-feb-26/3.webp" alt="플러그인, MCP, 스킬 설치" width="50%" /></a>

---

## 4/ 커스텀 에이전트 만들기

`.claude/agents`에 `.md` 파일을 넣어 커스텀 에이전트를 만드세요. 각 에이전트는 커스텀 이름, 색상, 도구 세트, 사전 허용 및 사전 거부 도구, 권한 모드, 모델을 가질 수 있습니다.

`settings.json`의 `"agent"` 필드 또는 `--agent` 플래그를 사용하여 메인 대화의 기본 에이전트를 설정할 수도 있습니다.

`/agents`를 실행하여 시작하세요.

<a href="https://x.com/bcherny/status/2021700144039903699"><img src="assets/boris-12-feb-26/4.webp" alt="커스텀 에이전트 만들기" width="50%" /></a>

---

## 5/ 일반 권한 사전 승인

Claude Code는 프롬프트 인젝션 감지, 정적 분석, 샌드박싱, 인간 감독을 결합한 권한 시스템을 사용합니다.

기본적으로 소수의 안전한 커맨드가 사전 승인됩니다. 더 많이 사전 승인하려면 `/permissions`를 실행하고 허용 및 차단 목록에 추가하세요. 이를 팀의 `settings.json`에 체크인하세요.

전체 와일드카드 구문이 지원됩니다 — 예: `Bash(bun run *)` 또는 `Edit(/docs/**)`.

<a href="https://x.com/bcherny/status/2021700332292911228"><img src="assets/boris-12-feb-26/5.webp" alt="일반 권한 사전 승인" width="50%" /></a>

---

## 6/ 샌드박싱 활성화

Claude Code의 오픈 소스 샌드박스 런타임을 사용하여 권한 프롬프트를 줄이면서 안전성을 향상시키세요.

`/sandbox`를 실행하여 활성화하세요. 샌드박싱은 머신에서 실행되며 파일 및 네트워크 격리를 모두 지원합니다.

<a href="https://x.com/bcherny/status/2021700506465579443"><img src="assets/boris-12-feb-26/6.webp" alt="샌드박싱 활성화" width="50%" /></a>

---

## 7/ 상태 표시줄 추가

커스텀 상태 표시줄은 작성기 바로 아래에 표시되어 작업하는 동안 보고 싶은 모델, 디렉토리, 남은 컨텍스트, 비용 등을 표시합니다.

모든 팀원이 다른 상태 표시줄을 가질 수 있습니다. `/statusline`을 사용하여 Claude가 `.bashrc`/`.zshrc`를 기반으로 생성하게 하세요.

<a href="https://x.com/bcherny/status/2021700784019452195"><img src="assets/boris-12-feb-26/7.webp" alt="상태 표시줄 추가" width="50%" /></a>

---

## 8/ 키 바인딩 커스터마이즈

Claude Code의 모든 키 바인딩은 커스터마이즈 가능합니다. `/keybindings`를 실행하여 키를 다시 매핑하세요. 설정이 즉시 다시 로드되므로 즉시 어떻게 느껴지는지 확인할 수 있습니다.

<a href="https://x.com/bcherny/status/2021700883873165435"><img src="assets/boris-12-feb-26/8.webp" alt="키 바인딩 커스터마이즈" width="50%" /></a>

---

## 9/ 훅 설정

훅으로 Claude의 라이프사이클에 결정론적으로 연결하세요:

- 권한 요청을 Slack이나 Opus로 자동 라우팅
- Claude가 턴 끝에 도달했을 때 계속하도록 유도 (계속할지 여부를 결정하기 위해 에이전트를 시작하거나 프롬프트를 사용할 수도 있음)
- 도구 호출 전후 처리, 예: 자체 로깅 추가

Claude에게 훅을 추가하도록 요청하여 시작하세요.

<a href="https://x.com/bcherny/status/2021701059253874861"><img src="assets/boris-12-feb-26/9.webp" alt="훅 설정" width="50%" /></a>

---

## 10/ 스피너 동사 커스터마이즈

스피너 동사를 커스터마이즈하여 기본 목록에 추가하거나 자신의 동사로 교체하세요. `settings.json`을 소스 컨트롤에 체크인하여 팀과 동사를 공유하세요.

<a href="https://x.com/bcherny/status/2021701145023197516"><img src="assets/boris-12-feb-26/10.webp" alt="스피너 동사 커스터마이즈" width="50%" /></a>

---

## 11/ 출력 스타일 사용

`/config`를 실행하고 출력 스타일을 설정하여 Claude가 다른 톤이나 형식으로 응답하게 하세요.

- **Explanatory** — 새 코드베이스를 익힐 때 권장, Claude가 작업하면서 프레임워크와 코드 패턴을 설명하게 합니다
- **Learning** — Claude가 코드 변경을 코치하게 합니다
- **Custom** — Claude의 목소리를 조정하기 위한 커스텀 출력 스타일 만들기

<a href="https://x.com/bcherny/status/2021701379409273093"><img src="assets/boris-12-feb-26/11.webp" alt="출력 스타일 사용" width="50%" /></a>

---

## 12/ 모든 것을 커스터마이즈!

Claude Code는 기본 설정으로도 훌륭하게 작동하지만, 커스터마이즈할 때는 `settings.json`을 git에 체크인하여 팀도 혜택을 받을 수 있게 하세요. 구성은 여러 수준에서 지원됩니다:

- 코드베이스 전체용
- 하위 폴더용
- 자신만을 위한
- 엔터프라이즈 전체 정책을 통한

37개의 설정과 84개의 환경 변수 (래퍼 스크립트를 피하기 위해 `settings.json`의 `"env"` 필드 사용)로, 원하는 동작이 구성 가능할 가능성이 높습니다.

<a href="https://x.com/bcherny/status/2021701636075458648"><img src="assets/boris-12-feb-26/12.webp" alt="모든 것을 커스터마이즈" width="50%" /></a>

---

## 소스

- [Boris Cherny (@bcherny) on X — 2026년 2월 12일](https://x.com/bcherny)
- [Claude Code 터미널 설정 문서](https://code.claude.com/docs/en/terminal)
- [Claude Code 플러그인 및 탐색 문서](https://code.claude.com/docs/en/discover-plugins)
- [Claude Code 서브에이전트 문서](https://code.claude.com/docs/en/sub-agents)
- [Claude Code 권한 문서](https://code.claude.com/docs/en/permissions)
- [Claude Code 샌드박스 문서](https://code.claude.com/docs/en/sandbox)
- [Claude Code 상태 표시줄 문서](https://code.claude.com/docs/en/statusline)
- [Claude Code 키보드 단축키 문서](https://code.claude.com/docs/en/keybindings)
- [Claude Code 훅 참조](https://code.claude.com/docs/en/hooks)
- [Claude Code 출력 스타일 문서](https://code.claude.com/docs/en/output-styles)
- [Claude Code 설정 문서](https://code.claude.com/docs/en/settings)
