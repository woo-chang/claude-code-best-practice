# Claude Code 활용 10가지 팁 — Claude Code 팀으로부터

Claude Code 창시자 Boris Cherny ([@bcherny](https://x.com/bcherny))가 2026년 2월 1일에 공유한 팀 팁 요약.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 배경

Boris는 Claude Code 팀에서 직접 가져온 Claude Code 활용 팁을 공유했습니다. 팀이 Claude를 사용하는 방식은 Boris 개인이 사용하는 방식과 다릅니다. 기억하세요: Claude Code를 사용하는 데 유일한 정답은 없습니다 — 모든 사람의 설정은 다릅니다. 자신에게 맞는 것을 찾기 위해 실험해 보세요!

<a href="https://x.com/bcherny/status/2017742741636321619"><img src="assets/boris-1-feb-26/0.png" alt="Boris Cherny 소개 트윗" width="50%" /></a>

---

## 1/ 병렬로 더 많이 작업하기

한 번에 3~5개의 git 워크트리를 실행하고, 각각 병렬로 자체 Claude 세션을 실행하세요. 생산성을 가장 크게 높이는 단일 방법이며, 팀의 최고 팁입니다. 개인적으로 Boris는 여러 git 체크아웃을 사용하지만, 대부분의 Claude Code 팀은 워크트리를 선호합니다 — 이것이 `@amorisscode`가 Claude Desktop 앱에 네이티브 지원을 구축한 이유입니다!

일부 사람들은 워크트리에 이름을 붙이고 셸 별칭 (`2a`, `2b`, `2c`)을 설정하여 한 번의 키 입력으로 전환할 수 있게 합니다. 다른 사람들은 로그를 읽고 BigQuery를 실행하는 전용 "분석" 워크트리를 운용합니다.

참조: [워크트리 문서](https://code.claude.com/docs/en/common...)

<a href="https://x.com/bcherny/status/2017742743125299476"><img src="assets/boris-1-feb-26/1.png" alt="병렬로 더 많이 작업하기" width="50%" /></a>

---

## 2/ 복잡한 작업은 항상 플랜 모드로 시작

Claude가 구현을 원샷으로 완성할 수 있도록 플랜에 에너지를 쏟으세요.

한 사람은 Claude 하나가 플랜을 작성하게 한 다음, 두 번째 Claude를 시작하여 수석 엔지니어로서 검토하게 합니다.

또 다른 사람은 뭔가 잘못되는 순간 플랜 모드로 돌아가 다시 계획합니다. 계속 밀어붙이지 마세요. 그들은 또한 빌드 단계뿐만 아니라 검증 단계에서도 플랜 모드로 들어가도록 Claude에 명시적으로 지시합니다.

<a href="https://x.com/bcherny/status/2017742745365057733"><img src="assets/boris-1-feb-26/2.png" alt="복잡한 작업은 플랜 모드로 시작" width="50%" /></a>

---

## 3/ CLAUDE.md에 투자하기

모든 수정 후에 이렇게 끝맺으세요: "같은 실수를 반복하지 않도록 CLAUDE.md를 업데이트하세요." Claude는 자신을 위한 규칙을 작성하는 데 놀라울 정도로 능숙합니다.

시간이 지남에 따라 `CLAUDE.md`를 가차 없이 편집하세요. Claude의 실수율이 측정 가능하게 떨어질 때까지 계속 반복하세요.

한 엔지니어는 Claude가 모든 작업/프로젝트에 대한 메모 디렉토리를 유지하고 모든 PR 후에 업데이트하게 합니다. 그 다음 `CLAUDE.md`에서 그것을 가리킵니다.

<a href="https://x.com/bcherny/status/2017742747067945390"><img src="assets/boris-1-feb-26/3.png" alt="CLAUDE.md에 투자하기" width="50%" /></a>

---

## 4/ 자신만의 스킬을 만들고 Git에 커밋하기

모든 프로젝트에서 재사용하세요. 팀의 팁:

- 하루에 두 번 이상 하는 일이 있다면 스킬이나 커맨드로 만드세요
- `/techdebt` 슬래시 커맨드를 만들고 모든 세션 끝에 실행하여 중복 코드를 찾아 제거하세요
- Slack, GDrive, Asana, GitHub의 7일 치 내용을 하나의 컨텍스트 덤프로 동기화하는 슬래시 커맨드를 설정하세요
- dbt 모델을 작성하고, 코드를 검토하고, 개발에서 변경 사항을 테스트하는 분석 엔지니어 스타일의 에이전트를 구축하세요

참조: [스킬로 Claude 확장 — Claude Code 문서](https://code.claude.com/docs/en/skills)

<a href="https://x.com/bcherny/status/2017742748984742078"><img src="assets/boris-1-feb-26/4.png" alt="자신만의 스킬 만들기" width="50%" /></a>

---

## 5/ Claude는 대부분의 버그를 스스로 수정합니다

팀이 하는 방법:

Slack MCP를 활성화하고 Slack 버그 스레드를 Claude에 붙여넣고 "수정"이라고만 말하세요. 컨텍스트 전환이 전혀 필요 없습니다.

또는 "실패하는 CI 테스트를 수정하세요"라고만 말하세요. 방법을 세세하게 관리하지 마세요.

분산 시스템 문제 해결을 위해 Claude가 docker 로그를 보게 하세요 — 놀라울 정도로 능숙합니다.

<a href="https://x.com/bcherny/status/2017742750473720121"><img src="assets/boris-1-feb-26/5.png" alt="Claude는 대부분의 버그를 스스로 수정" width="50%" /></a>

---

## 6/ 프롬프팅 실력 향상

a. **Claude에 도전하세요.** "이 변경사항에 대해 나를 철저히 검토하고 내가 테스트를 통과할 때까지 PR을 만들지 마세요"라고 하세요. Claude를 검토자로 만드세요. 또는 "이것이 작동한다는 것을 증명하세요"라고 말하고 Claude가 main 브랜치와 피처 브랜치 사이의 동작 차이를 보여주게 하세요.

b. **평범한 수정 후에는** 이렇게 말하세요: "지금 알고 있는 모든 것을 감안할 때, 이것을 버리고 우아한 해결책을 구현하세요."

c. **상세한 사양서를 작성하고** 작업을 넘기기 전에 모호성을 줄이세요. 구체적일수록 출력이 더 좋습니다.

<a href="https://x.com/bcherny/status/2017742752566632544"><img src="assets/boris-1-feb-26/6.png" alt="프롬프팅 실력 향상" width="50%" /></a>

---

## 7/ 터미널 및 환경 설정

팀은 Ghostty를 좋아합니다! 여러 사람들이 동기화된 렌더링, 24비트 색상, 적절한 유니코드 지원을 좋아합니다.

Claude 전환을 더 쉽게 하려면 `/statusline`을 사용하여 상태 바에 항상 컨텍스트 사용량과 현재 git 브랜치를 표시하도록 커스터마이즈하세요. 많은 사람들이 터미널 탭에 색상 코드를 지정하고 이름을 붙입니다. 때로는 tmux를 사용하며, 작업/워크트리당 하나의 탭을 사용합니다.

음성 받아쓰기를 사용하세요. 타이핑보다 3배 빠르게 말할 수 있으며, 그 결과 프롬프트가 훨씬 더 상세해집니다. (macOS에서 fn 두 번 누르기)

참조: [터미널 설정 문서](https://code.claude.com/docs/en/termin...)

<a href="https://x.com/bcherny/status/2017742753971769626"><img src="assets/boris-1-feb-26/7.png" alt="터미널 및 환경 설정" width="50%" /></a>

---

## 8/ 서브에이전트 사용

a. 문제에 더 많은 컴퓨팅을 투입하고 싶을 때 모든 요청에 "서브에이전트를 사용하세요"를 추가하세요.

b. 메인 에이전트의 컨텍스트 윈도우를 깔끔하고 집중된 상태로 유지하기 위해 개별 작업을 서브에이전트에 위임하세요.

c. 훅을 통해 권한 요청을 Opus 4.5로 라우팅하세요 — 공격을 스캔하고 안전한 것을 자동 승인하게 합니다. 참조: [훅 문서](https://code.claude.com/docs/en/hooks#...)

<a href="https://x.com/bcherny/status/2017742755737555434"><img src="assets/boris-1-feb-26/8.png" alt="서브에이전트 사용" width="50%" /></a>

---

## 9/ 데이터 및 분석에 Claude 사용

Claude Code에 "bq" CLI를 사용하여 즉석에서 메트릭을 가져오고 분석하도록 요청하세요. 팀에는 코드베이스에 체크인된 BigQuery 스킬이 있으며, 모두가 Claude Code에서 직접 분석 쿼리에 이것을 사용합니다. 개인적으로 Boris는 6개월 넘게 SQL 코드를 한 줄도 작성하지 않았습니다.

이것은 CLI, MCP, 또는 API가 있는 모든 데이터베이스에 작동합니다.

<a href="https://x.com/bcherny/status/2017742757666902374"><img src="assets/boris-1-feb-26/9.png" alt="데이터 및 분석에 Claude 사용" width="50%" /></a>

---

## 10/ Claude로 학습하기

팀에서 Claude Code를 학습에 사용하는 몇 가지 팁:

a. `/config`에서 "Explanatory" 또는 "Learning" 출력 스타일을 활성화하여 Claude가 변경 사항 뒤의 "이유"를 설명하게 하세요.

b. Claude가 익숙하지 않은 코드를 설명하는 시각적 HTML 프레젠테이션을 생성하게 하세요. 놀랍도록 좋은 슬라이드를 만듭니다!

c. Claude에게 새로운 프로토콜과 코드베이스를 이해하는 데 도움이 되도록 ASCII 다이어그램을 그리게 하세요.

d. 간격 반복 학습 스킬을 구축하세요: 당신이 이해한 것을 설명하면 Claude가 후속 질문을 하여 공백을 채우고 결과를 저장합니다.

<a href="https://x.com/bcherny/status/2017742759218794768"><img src="assets/boris-1-feb-26/10.png" alt="Claude로 학습하기" width="50%" /></a>

---

## 소스

- [Boris Cherny (@bcherny) on X — 2026년 2월 1일](https://x.com/bcherny/status/2017742741636321619)
