# Claude Code의 숨겨진 기능 15가지 & 잘 활용되지 않는 기능 — Boris Cherny

Claude Code 창시자 Boris Cherny ([@bcherny](https://x.com/bcherny))가 2026년 3월 30일에 공유한 팁 요약.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 배경

Boris는 Claude Code에서 가장 좋아하는 숨겨진 기능과 잘 활용되지 않는 기능들을 자신이 가장 많이 사용하는 것들에 초점을 맞춰 공유했습니다.

<a href="https://x.com/bcherny/status/2038454336355999749"><img src="assets/boris-30-mar-26/0.png" alt="Boris Cherny 소개 트윗" width="50%" /></a>

---

## 1/ Claude Code에 모바일 앱이 있습니다

Claude Code에 모바일 앱이 있다는 것을 알고 계셨나요? Boris는 iOS 앱에서 많은 코드를 작성합니다 — 노트북을 열지 않고도 변경을 가할 수 있는 편리한 방법입니다.

- iOS/Android용 Claude 앱 다운로드
- 왼쪽의 **코드** 탭으로 이동
- 변경 사항 검토, PR 승인, 폰에서 직접 코드 작성 가능

<a href="https://x.com/bcherny/status/2038454337811386436"><img src="assets/boris-30-mar-26/1.png" alt="Claude Code 모바일 앱" width="50%" /></a>

---

## 2/ 모바일/웹/데스크톱과 터미널 간 세션 이동

`claude --teleport` 또는 `/teleport`를 실행하여 클라우드 세션을 머신에서 계속하세요. 또는 `/remote-control`을 실행하여 폰/웹에서 로컬로 실행 중인 세션을 제어하세요.

- **텔레포트**: 클라우드 세션을 로컬 터미널로 가져옴
- **원격 제어**: 모든 기기에서 로컬 세션을 제어할 수 있음
- Boris는 `/config`에서 **"모든 세션에 원격 제어 활성화"**를 설정했습니다

<a href="https://x.com/bcherny/status/2038454339933548804"><img src="assets/boris-30-mar-26/2.png" alt="텔레포트와 원격 제어" width="50%" /></a>

---

## 3/ /loop와 /schedule — 가장 강력한 두 기능

이것들을 사용하여 Claude가 최대 일주일 동안 설정된 간격으로 자동으로 실행되도록 예약하세요. Boris는 로컬에서 여러 루프를 실행합니다:

- `/loop 5m /babysit` — 코드 검토 자동 처리, 자동 리베이스, PR을 프로덕션으로 안내
- `/loop 30m /slack-feedback` — 30분마다 Slack 피드백을 위한 PR 자동 작성
- `/loop /post-merge-sweeper` — 놓친 코드 검토 의견을 처리하기 위한 PR 작성
- `/loop 1h /pr-pruner` — 오래된 불필요한 PR 종료
- ...그 외에도 많습니다!

워크플로우를 스킬 + 루프로 전환해보세요. 강력합니다.

<a href="https://x.com/bcherny/status/2038454341884154269"><img src="assets/boris-30-mar-26/3.png" alt="/loop와 /schedule" width="50%" /></a>

---

## 4/ 훅으로 결정론적 로직 실행

에이전트 라이프사이클의 일부로 로직을 실행하기 위해 훅을 사용하세요. 예:

- Claude를 시작할 때마다 컨텍스트를 **동적으로 로드** (`SessionStart`)
- 모델이 실행하는 **모든 bash 커맨드 로그** (`PreToolUse`)
- 승인/거부를 위해 **권한 프롬프트를 WhatsApp으로 라우팅** (`PermissionRequest`)
- 중지할 때마다 Claude를 **계속하도록 유도** (`Stop`)

<a href="https://x.com/bcherny/status/2038454343519932844"><img src="assets/boris-30-mar-26/4.png" alt="훅 사용" width="50%" /></a>

---

## 5/ Cowork Dispatch

Boris는 Dispatch를 매일 사용하여 Slack과 이메일을 확인하고, 파일을 관리하며, 컴퓨터 앞에 없을 때 노트북에서 작업을 처리합니다. 코딩하지 않을 때는 디스패칭합니다.

- Dispatch는 Claude Desktop 앱을 위한 **안전한 원격 제어**입니다
- 사용자의 허락 하에 MCP, 브라우저, 컴퓨터를 사용할 수 있습니다
- 어디서나 비코딩 작업을 Claude에 위임하는 방법으로 생각하세요

<a href="https://x.com/bcherny/status/2038454345419936040"><img src="assets/boris-30-mar-26/5.png" alt="Cowork Dispatch" width="50%" /></a>

---

## 6/ 프론트엔드 작업에 Chrome 확장 프로그램 사용

Claude Code 사용을 위한 가장 중요한 팁: **Claude에게 출력을 검증할 방법을 제공하세요.** 그러면 Claude는 결과가 훌륭해질 때까지 반복합니다.

- 브라우저를 사용할 수 없는 상태에서 웹사이트를 만들라고 하는 것과 같습니다 — 결과가 좋지 않을 것입니다
- Claude에게 브라우저를 주면 좋아 보일 때까지 코드를 작성하고 반복합니다
- Boris는 웹 코드 작업 시 항상 Chrome 확장 프로그램을 사용합니다 — 유사한 다른 MCP보다 더 안정적으로 작동하는 경향이 있습니다

<a href="https://x.com/bcherny/status/2038454347156398333"><img src="assets/boris-30-mar-26/6.png" alt="프론트엔드용 Chrome 확장 프로그램" width="50%" /></a>

---

## 7/ Claude Desktop 앱으로 웹 서버 자동 시작 및 테스트

같은 맥락에서 Desktop 앱은 Claude가 **웹 서버를 자동으로 실행하고 내장 브라우저에서 테스트**할 수 있는 기능을 제공합니다.

- CLI 또는 VSCode에서 Chrome 확장 프로그램을 사용하여 유사하게 설정할 수 있습니다
- 또는 통합 경험을 위해 Desktop 앱을 사용하세요

<a href="https://x.com/bcherny/status/2038454348804714642"><img src="assets/boris-30-mar-26/7.png" alt="Desktop 앱 웹 서버 테스트" width="50%" /></a>

---

## 8/ 세션 포크

기존 세션을 포크하는 방법을 묻는 사람들이 많습니다. 두 가지 방법:

1. 세션에서 `/branch` 실행
2. CLI에서 `claude --resume <session-id> --fork-session` 실행

`/branch`는 분기된 대화를 만듭니다 — 이제 분기 안에 있습니다. 원본으로 돌아가려면 `claude -r <original-session-id>`를 사용하세요.

<a href="https://x.com/bcherny/status/2038454350214041740"><img src="assets/boris-30-mar-26/8.png" alt="세션 포크" width="50%" /></a>

---

## 9/ 사이드 쿼리에 /btw 사용

Boris는 에이전트가 작업하는 동안 빠른 질문에 답하기 위해 이것을 항상 사용합니다. `/btw`를 사용하면 에이전트의 현재 작업을 방해하지 않고 사이드 질문을 할 수 있습니다.

예시:
```
/btw how do I spell dachshund?
> dachshund — German for "badger dog" (dachs + badger, hund + dog).
↑/↓ to scroll · Space, Enter, or Escape to dismiss
```

<a href="https://x.com/bcherny/status/2038454351849787485"><img src="assets/boris-30-mar-26/9.png" alt="사이드 쿼리에 /btw 사용" width="50%" /></a>

---

## 10/ Git 워크트리 사용

Claude Code는 git 워크트리에 대한 깊은 지원을 제공합니다. 워크트리는 동일한 리포지토리에서 많은 병렬 작업을 하는 데 필수적입니다. Boris는 **항상 수십 개의 Claude를 실행**하며, 이것이 그 방법입니다.

- `claude -w`를 사용하여 워크트리에서 새 세션 시작
- 또는 Claude Desktop 앱에서 **"워크트리" 체크박스** 클릭
- git 이외의 VCS 사용자는 `WorktreeCreate` 훅을 사용하여 워크트리 생성에 대한 자체 로직 추가

<a href="https://x.com/bcherny/status/2038454353787519164"><img src="assets/boris-30-mar-26/10.png" alt="Git 워크트리" width="50%" /></a>

---

## 11/ 대규모 변경에 /batch 사용

`/batch`는 인터뷰 후 Claude가 작업을 완료하는 데 필요한 만큼의 **워크트리 에이전트** (수십, 수백, 심지어 수천)에게 작업을 분배합니다.

- 대규모 코드 마이그레이션 및 기타 병렬화 가능한 작업에 사용
- 각 워크트리 에이전트는 코드베이스의 자체 복사본에서 독립적으로 작업

<a href="https://x.com/bcherny/status/2038454355469484142"><img src="assets/boris-30-mar-26/11.png" alt="대규모 변경에 /batch 사용" width="50%" /></a>

---

## 12/ SDK 시작 속도를 최대 10배 높이는 --bare 사용

기본적으로 `claude -p`를 실행할 때 (또는 TypeScript 또는 Python SDK), Claude는 로컬 CLAUDE.md, 설정, MCP를 검색합니다. 하지만 비대화형 사용의 경우, 대부분 `--system-prompt`, `--mcp-config`, `--settings` 등을 통해 로드할 것을 명시적으로 지정하고 싶을 것입니다.

- 이것은 SDK가 처음 구축되었을 때의 설계 상의 실수였습니다
- 미래 버전에서는 기본값을 `--bare`로 변경할 것입니다
- 지금은 플래그로 선택하여 **최대 10배 빠른 시작**을 얻으세요

```bash
claude -p "summarize this codebase" \
    --output-format=stream-json \
    --verbose \
    --bare
```

<a href="https://x.com/bcherny/status/2038454357088457168"><img src="assets/boris-30-mar-26/12.png" alt="SDK 시작을 위한 --bare 플래그" width="50%" /></a>

---

## 13/ 더 많은 폴더에 Claude 접근 권한을 주는 --add-dir 사용

여러 리포지토리에서 작업할 때 Boris는 일반적으로 한 리포지토리에서 Claude를 시작하고 `--add-dir` (또는 `/add-dir`)를 사용하여 Claude가 다른 리포지토리를 볼 수 있게 합니다.

- 이것은 Claude에게 리포지토리에 대해 알려줄 뿐만 아니라 리포지토리에서 작업할 **권한도 부여**합니다
- 또는 `"additionalDirectories"`를 팀의 `settings.json`에 추가하여 Claude Code 시작 시 항상 추가 폴더를 로드하세요

<a href="https://x.com/bcherny/status/2038454359047156203"><img src="assets/boris-30-mar-26/13.png" alt="다중 리포지토리를 위한 --add-dir" width="50%" /></a>

---

## 14/ Claude Code에 커스텀 시스템 프롬프트 및 도구를 주는 --agent 사용

커스텀 에이전트는 종종 간과되는 강력한 프리미티브입니다. 사용하려면 `.claude/agents/`에 새 에이전트를 정의한 다음 실행하세요:

```bash
claude --agent=<your agent's name>
```

- 에이전트는 제한된 도구, 커스텀 설명, 특정 모델을 가질 수 있습니다
- 읽기 전용 에이전트, 특화된 검토 에이전트, 또는 도메인별 도구를 만드는 데 탁월합니다

<a href="https://x.com/bcherny/status/2038454360418787764"><img src="assets/boris-30-mar-26/14.png" alt="커스텀 시스템 프롬프트를 위한 --agent" width="50%" /></a>

---

## 15/ 음성 입력을 위한 /voice 사용

재미있는 사실: Boris는 타이핑보다 Claude에게 말하는 방식으로 대부분의 코딩을 합니다.

- CLI에서 `/voice`를 실행하고 스페이스 바를 길게 눌러 말하기
- Desktop에서 음성 버튼 누르기
- 또는 iOS 설정에서 받아쓰기 활성화

<a href="https://x.com/bcherny/status/2038454362226467112"><img src="assets/boris-30-mar-26/15.png" alt="음성 입력을 위한 /voice" width="50%" /></a>

---

## 소스

- [Boris Cherny (@bcherny) on X — 2026년 3월 30일](https://x.com/bcherny/status/2038454336355999749)
