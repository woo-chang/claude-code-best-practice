# Claude Code 구축에서 배운 교훈: 스킬 활용 방법 — Thariq

Thariq ([@trq212](https://x.com/trq212))이 2026년 3월 17일에 공유한 Anthropic의 내부 스킬 활용 방법에 대한 종합 가이드.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 배경

스킬은 Claude Code에서 가장 많이 사용되는 확장 포인트 중 하나가 되었습니다. 유연하고, 만들기 쉬우며, 배포하기 간단합니다. 하지만 이 유연성 때문에 무엇이 가장 잘 작동하는지 알기 어렵습니다. Thariq는 수백 개의 스킬을 적극적으로 사용하는 Anthropic에서 스킬을 광범위하게 사용한 경험에서 배운 교훈을 공유합니다.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/1.png" alt="Thariq 소개 트윗" width="50%" /></a>

---

## 스킬이란 무엇인가?

일반적인 오해는 스킬이 "단순한 마크다운 파일"이라는 것이지만, 가장 흥미로운 부분은 스킬이 에이전트가 탐색, 탐구, 조작할 수 있는 스크립트, 에셋, 데이터 등을 포함할 수 있는 **폴더**라는 것입니다. 스킬에는 또한 동적 훅 등록을 포함한 다양한 구성 옵션이 있습니다.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/2.png" alt="스킬이란?" width="50%" /></a>

---

## 스킬의 유형

모든 스킬을 목록화한 후, 팀은 9가지 반복되는 카테고리로 분류된다는 것을 알아챘습니다. 가장 좋은 스킬은 하나에 딱 맞고, 더 혼란스러운 것들은 여러 카테고리에 걸쳐 있습니다.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/3.png" alt="스킬 유형 그리드" width="50%" /></a>

---

### 1/ 라이브러리 및 API 참조

라이브러리, CLI, 또는 SDK를 올바르게 사용하는 방법을 설명하는 스킬. 내부 라이브러리나 Claude Code가 가끔 어려움을 겪는 일반적인 라이브러리에 해당할 수 있습니다. 종종 참조 코드 스니펫 폴더와 스크립트 작성 시 피해야 할 주의사항 목록을 포함합니다.

**예시:** billing-lib, internal-platform-cli, frontend-design

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/4.png" alt="라이브러리 및 API 참조" width="50%" /></a>

---

### 2/ 제품 검증

코드가 작동하는지 테스트하거나 검증하는 방법을 설명하는 스킬. 이것들은 Playwright, tmux 등과 같은 외부 도구와 짝을 이루는 경우가 많습니다. 검증 스킬은 Claude의 출력이 올바른지 확인하는 데 매우 유용합니다. 엔지니어가 검증 스킬을 훌륭하게 만드는 데 일주일을 투자할 가치가 있을 수 있습니다.

**예시:** signup-flow-driver, checkout-verifier, tmux-cli-driver

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/5.png" alt="제품 검증" width="50%" /></a>

---

### 3/ 데이터 페칭 및 분석

데이터 및 모니터링 스택에 연결하는 스킬. 자격 증명이 있는 데이터를 가져오는 라이브러리, 특정 대시보드 ID 등, 그리고 데이터를 가져오는 일반적인 워크플로우나 방법에 대한 지침을 포함할 수 있습니다.

**예시:** funnel-query, cohort-compare, grafana

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/6.png" alt="데이터 페칭 및 분석" width="50%" /></a>

---

### 4/ 비즈니스 프로세스 및 팀 자동화

반복적인 워크플로우를 하나의 커맨드로 자동화하는 스킬. 이것들은 일반적으로 상당히 단순한 지침이지만 다른 스킬이나 MCP에 더 복잡한 의존성을 가질 수 있습니다. 이전 결과를 로그 파일에 저장하면 모델이 일관성을 유지하고 이전 워크플로우 실행을 반성하는 데 도움이 됩니다.

**예시:** standup-post, create-\<ticket-system\>-ticket, weekly-recap

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/7.png" alt="비즈니스 프로세스 및 팀 자동화" width="50%" /></a>

---

### 5/ 코드 스캐폴딩 및 템플릿

코드베이스의 특정 기능에 대한 프레임워크 보일러플레이트를 생성하는 스킬. 이러한 스킬을 구성할 수 있는 스크립트와 결합할 수 있습니다. 스캐폴딩이 코드만으로는 완전히 처리할 수 없는 자연어 요구사항을 가질 때 특히 유용합니다.

**예시:** new-\<framework\>-workflow, new-migration, create-app

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/8.png" alt="코드 스캐폴딩 및 템플릿" width="50%" /></a>

---

### 6/ 코드 품질 및 검토

조직 내 코드 품질을 강화하고 코드 검토를 돕는 스킬. 최대한의 견고성을 위해 결정론적 스크립트나 도구를 포함할 수 있습니다. 훅의 일부로 또는 GitHub Action 내에서 이 스킬들을 자동으로 실행할 수 있습니다.

**예시:** adversarial-review, code-style, testing-practices

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/10.png" alt="코드 품질 및 검토" width="50%" /></a>

---

### 7/ CI/CD 및 배포

코드베이스 내에서 코드를 가져오고, 푸시하고, 배포하는 데 도움을 주는 스킬. 이 스킬들은 데이터를 수집하기 위해 다른 스킬을 참조할 수 있습니다.

**예시:** babysit-pr, deploy-\<service\>, cherry-pick-prod

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/11.png" alt="CI/CD 및 배포" width="50%" /></a>

---

### 8/ 런북

증상 (Slack 스레드, 알림, 오류 서명 등)을 받아 멀티 도구 조사를 진행하고 구조화된 리포트를 생성하는 스킬.

**예시:** \<service\>-debugging, oncall-runner, log-correlator

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/12.png" alt="런북" width="50%" /></a>

---

### 9/ 인프라 운영

일상적인 유지 관리 및 운영 절차를 수행하는 스킬 — 일부는 가드레일이 유익한 파괴적인 작업을 포함합니다. 이것들은 엔지니어가 중요한 운영에서 모범 사례를 따르기 쉽게 만들어 줍니다.

**예시:** \<resource\>-orphans, dependency-management, cost-investigation

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/13.png" alt="인프라 운영" width="50%" /></a>

---

## 스킬 만들기 팁

효과적인 스킬을 작성하기 위한 9가지 모범 사례와 배포 및 측정 지침.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/14.png" alt="스킬 만들기 팁 그리드" width="50%" /></a>

---

### 팁 1: 당연한 것을 말하지 마세요

Claude Code는 코드베이스에 대해 많이 알고 있으며, Claude는 기본 의견을 포함한 코딩에 대해 많이 알고 있습니다. 주로 지식에 관한 스킬을 게시하는 경우, Claude를 일반적인 사고 방식에서 벗어나게 하는 정보에 집중하려고 노력하세요. frontend design 스킬이 좋은 예입니다 — Inter 폰트와 보라색 그라디언트와 같은 클래식 패턴을 피하고 Claude의 디자인 취향을 개선하기 위해 고객과 반복적으로 개발되었습니다.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/15.png" alt="당연한 것을 말하지 마세요" width="50%" /></a>

---

### 팁 2: 주의사항(Gotchas) 섹션 만들기

모든 스킬에서 가장 높은 신호를 가진 내용은 주의사항 섹션입니다. 이 섹션들은 Claude가 스킬을 사용할 때 자주 실패하는 지점에서 구축되어야 합니다. 이상적으로는 이러한 주의사항을 포착하기 위해 시간이 지남에 따라 스킬을 업데이트할 것입니다.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/16.png" alt="주의사항 섹션 만들기" width="50%" /></a>

---

### 팁 3: 파일 시스템 및 점진적 공개 활용

스킬은 단순한 마크다운 파일이 아니라 폴더입니다. 전체 파일 시스템을 컨텍스트 엔지니어링과 점진적 공개의 한 형태로 생각해야 합니다. Claude에게 스킬에 있는 파일이 무엇인지 알려주면 적절한 시점에 읽을 것입니다. 가장 단순한 형태는 다른 마크다운 파일을 가리키는 것입니다 — 예: 상세한 함수 서명과 사용 예를 `references/api.md`로 분리. 참조, 스크립트, 예시 등의 폴더를 가질 수 있습니다.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/17.png" alt="점진적 공개" width="50%" /></a>

---

### 팁 4: Claude를 억압하지 마세요

Claude는 일반적으로 지침을 따르려 하며, 스킬은 매우 재사용 가능하기 때문에 너무 구체적인 것에 주의해야 합니다. Claude에게 필요한 정보를 제공하되, 상황에 적응할 유연성을 주세요. 규정적인 단계별 지침 대신 목표와 제약을 제공하세요.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/18.png" alt="Claude를 억압하지 마세요" width="50%" /></a>

---

### 팁 5: 설정을 고려하세요

일부 스킬은 사용자의 컨텍스트로 설정이 필요할 수 있습니다. 좋은 패턴은 이 설정 정보를 스킬 디렉토리의 `config.json` 파일에 저장하는 것입니다. 구성이 설정되지 않은 경우 에이전트가 사용자에게 정보를 요청할 수 있습니다. Claude에게 구조화된 다중 선택 질문에 AskUserQuestion 도구를 사용하도록 지시할 수 있습니다.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/19.png" alt="설정 고려" width="50%" /></a>

---

### 팁 6: description 필드는 모델을 위한 것

Claude Code가 세션을 시작할 때 각 사용 가능한 스킬의 설명과 함께 목록을 작성합니다. 이 목록이 Claude가 "이 요청에 스킬이 있나?"를 결정하기 위해 스캔하는 것입니다. 즉, description 필드는 요약이 아닙니다 — 이 스킬을 **언제 트리거할지**에 대한 설명입니다. 모델을 위해 작성하세요.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/20.png" alt="설명 = 트리거" width="50%" /></a>

---

### 팁 7: 메모리 및 데이터 저장

일부 스킬은 그 안에 데이터를 저장하는 형태의 메모리를 포함할 수 있습니다. 추가 전용 텍스트 로그 파일이나 JSON 파일처럼 간단하거나 SQLite 데이터베이스처럼 복잡한 형태로 데이터를 저장할 수 있습니다. 스킬 디렉토리에 저장된 데이터는 스킬 업그레이드 시 삭제될 수 있으므로, 데이터를 저장하기 위한 안정적인 폴더로 플러그인당 `${CLAUDE_PLUGIN_DATA}`를 사용하세요.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/21.png" alt="메모리 및 데이터 저장" width="50%" /></a>

---

### 팁 8: 스크립트 저장 및 코드 생성

Claude에게 줄 수 있는 가장 강력한 도구 중 하나는 코드입니다. Claude에게 스크립트와 라이브러리를 주면 Claude가 보일러플레이트를 재구성하는 것보다 다음에 무엇을 할지 결정하는 구성에 턴을 집중할 수 있습니다. 그러면 Claude는 즉석에서 스크립트를 생성하여 더 고급 분석을 위해 이 기능을 구성할 수 있습니다.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/22.png" alt="스크립트 저장 및 코드 생성" width="50%" /></a>

---

### 팁 9: 온디맨드 훅

스킬은 스킬이 호출될 때만 활성화되고 세션 기간 동안 지속되는 훅을 포함할 수 있습니다. 항상 실행하고 싶지는 않지만 가끔 매우 유용한 보다 의견이 강한 훅에 이것을 사용하세요.

**예시:**
- `/careful` — PreToolUse 매처를 통해 Bash에서 rm -rf, DROP TABLE, force-push, kubectl delete 차단
- `/freeze` — 특정 디렉토리에 없는 Edit/Write 차단

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/23.png" alt="온디맨드 훅" width="50%" /></a>

---

## 스킬 배포

팀과 스킬을 공유하는 두 가지 방법:
- **리포지토리에 체크인** (`.claude/skills` 아래) — 비교적 적은 리포지토리에서 작업하는 소규모 팀에 최적
- **플러그인 만들기** 및 사용자가 플러그인을 업로드하고 설치할 수 있는 Claude Code 플러그인 마켓플레이스 만들기

체크인된 모든 스킬은 모델의 컨텍스트에 조금씩 추가됩니다. 규모가 커짐에 따라 내부 플러그인 마켓플레이스를 통해 스킬을 배포하고 팀이 설치할 것을 결정하게 할 수 있습니다.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/24.png" alt="스킬 배포" width="50%" /></a>

---

## 마켓플레이스 관리

마켓플레이스에 어떤 스킬이 들어가는지 결정하는 중앙화된 팀은 없습니다. 대신 유기적으로 가장 유용한 스킬을 찾으려고 노력하세요. GitHub의 샌드박스 폴더에 업로드하고 Slack이나 다른 포럼에서 사람들에게 알려주세요. 스킬이 인기를 끌면 (어느 정도는 스킬 소유자가 결정), 마켓플레이스로 이동하기 위한 PR을 제출할 수 있습니다. 중복 스킬을 피하기 위해 출시 전 큐레이션이 중요합니다.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/25.png" alt="마켓플레이스 관리" width="50%" /></a>

---

## 스킬 구성

서로 의존하는 스킬을 가질 수 있습니다. 예를 들어, 파일을 업로드하는 파일 업로드 스킬과 CSV를 만들고 업로드하는 CSV 생성 스킬. 이러한 종류의 의존성 관리는 마켓플레이스나 스킬에 기본적으로 내장되어 있지 않지만, 단순히 이름으로 다른 스킬을 참조할 수 있으며 설치된 경우 모델이 호출합니다.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/26.png" alt="스킬 구성" width="50%" /></a>

---

## 스킬 측정

스킬이 어떻게 작동하는지 이해하려면 회사 내에서 스킬 사용을 로그할 수 있는 PreToolUse 훅을 사용하세요. 이것은 인기 있는 스킬이나 기대에 비해 충분히 트리거되지 않는 스킬을 찾을 수 있음을 의미합니다.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/27.png" alt="스킬 측정" width="50%" /></a>

---

## 결론

스킬은 에이전트를 위한 믿을 수 없을 정도로 강력하고 유연한 도구이지만 아직 초기 단계이며 우리 모두 이것을 최선으로 사용하는 방법을 파악하고 있습니다. 이것을 결정적인 가이드보다는 우리가 작동하는 것을 본 유용한 팁 모음으로 생각하세요. 스킬을 이해하는 가장 좋은 방법은 시작하고, 실험하고, 자신에게 맞는 것을 보는 것입니다. 대부분은 몇 줄과 하나의 주의사항으로 시작했으며, Claude가 새로운 엣지 케이스에 부딪힐 때마다 사람들이 계속 추가하여 더 좋아졌습니다.

<a href="https://x.com/trq212/status/2033949937936085378"><img src="assets/thariq-17-mar-26/28.png" alt="결론" width="50%" /></a>

---

## 소스

- [Thariq (@trq212) on X — 2026년 3월 17일](https://x.com/trq212/status/2033949937936085378)
- [Skilljar — Agent Skills 코스](https://code.claude.com/docs/en/skills)
- [스킬 크리에이터](https://code.claude.com/docs/en/skills)
