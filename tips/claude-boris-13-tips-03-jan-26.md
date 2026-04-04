# 내가 Claude Code를 사용하는 방법 — Boris Cherny의 13가지 팁

Claude Code 창시자 Boris Cherny ([@bcherny](https://x.com/bcherny))가 2026년 1월 3일에 공유한 설정 팁 요약.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 배경

Boris는 자신의 개인 Claude Code 설정을 공유하며, "놀라울 정도로 기본적"이라고 언급했습니다 — Claude Code는 기본 설정으로도 훌륭하게 작동하므로 그는 많이 커스터마이즈하지 않습니다. 올바른 사용 방법은 하나가 아닙니다: 팀은 의도적으로 원하는 방식으로 사용, 커스터마이즈, 해킹할 수 있게 구축합니다. Claude Code 팀의 각 사람은 매우 다르게 사용합니다.

<a href="https://x.com/bcherny/status/2007179832300581177"><img src="assets/boris-3-jan-26/0.png" alt="Boris Cherny 소개 트윗" width="50%" /></a>

---

## 1/ 5개의 Claude를 병렬로 실행

터미널에서 5개의 Claude를 병렬로 실행하세요. 탭에 1~5 번호를 매기고, Claude가 입력이 필요할 때 알 수 있도록 시스템 알림을 사용하세요.

참조: [터미널 설정 문서](https://code.claude.com/docs/en/terminal)

<a href="https://x.com/bcherny/status/2007179833990885678"><img src="assets/boris-3-jan-26/1.png" alt="5개의 Claude를 병렬로 실행" width="50%" /></a>

---

## 2/ 더 많은 병렬 처리를 위해 claude.ai/code 사용

로컬 Claude와 함께 claude.ai/code에서 5~10개의 Claude를 병렬로 실행하세요. `claude.ai/code`를 사용하여 로컬 세션을 웹 세션에 넘기고, Chrome에서 세션을 수동으로 시작하고, 앞뒤로 텔레포트하세요.

<a href="https://x.com/bcherny/status/2007179836704600237"><img src="assets/boris-3-jan-26/2.png" alt="claude.ai/code 병렬 처리" width="50%" /></a>

---

## 3/ 모든 것에 Thinking과 함께 Opus 사용

모든 것에 thinking을 활성화한 Opus 4.5를 사용하세요. Boris가 사용해본 최고의 코딩 모델입니다 — Sonnet보다 크고 느리지만, 덜 안내해도 되고 도구 사용에 더 뛰어나기 때문에 결국 더 작은 모델을 사용하는 것보다 거의 항상 더 빠릅니다.

<a href="https://x.com/bcherny/status/2007179838864666847"><img src="assets/boris-3-jan-26/3.png" alt="Thinking과 함께 Opus 사용" width="50%" /></a>

---

## 4/ 팀과 단일 CLAUDE.md 공유

리포지토리에 단일 `CLAUDE.md`를 공유하세요. git에 체크인하고 전체 팀이 주당 여러 번 기여하게 하세요. Claude가 뭔가를 잘못 할 때마다 `CLAUDE.md`에 추가하여 다음번에는 하지 않도록 하세요.

<a href="https://x.com/bcherny/status/2007179840848597422"><img src="assets/boris-3-jan-26/4.png" alt="공유 CLAUDE.md" width="50%" /></a>

---

## 5/ PR에서 @claude 태그하여 CLAUDE.md 업데이트

코드 검토 중 동료의 PR에 `@claude`를 태그하여 PR의 일부로 `CLAUDE.md`에 무언가를 추가하세요. 이를 위해 Claude Code GitHub 액션 ([install-@hub-action](https://github.com/apps/claude))을 사용하세요 — Boris의 복리 엔지니어링 버전입니다.

<a href="https://x.com/bcherny/status/2007179842928947333"><img src="assets/boris-3-jan-26/5.png" alt="PR에서 @claude 태그" width="50%" /></a>

---

## 6/ 대부분의 세션을 플랜 모드로 시작

대부분의 세션을 플랜 모드로 시작하세요 (shift+tab 두 번). 목표가 Pull Request를 작성하는 것이라면 플랜 모드를 사용하고 플랜이 마음에 들 때까지 Claude와 주고받으세요. 거기서부터 편집 자동 수락 모드로 전환하면 Claude가 일반적으로 원샷으로 완성할 수 있습니다. 좋은 플랜이 정말 중요합니다.

<a href="https://x.com/bcherny/status/2007179845336527000"><img src="assets/boris-3-jan-26/6.png" alt="플랜 모드" width="50%" /></a>

---

## 7/ 내부 루프 워크플로우에 슬래시 커맨드 사용

하루에 여러 번 하는 모든 "내부 루프" 워크플로우에 슬래시 커맨드를 사용하세요. 이렇게 하면 반복 프롬프팅에서 벗어나고 Claude도 이 워크플로우를 사용할 수 있습니다. 커맨드는 git에 체크인되고 `.claude/commands/`에 있습니다.

예시: `/commit-push-pr` — 커밋, 푸시, PR 열기.

<a href="https://x.com/bcherny/status/2007179847949500714"><img src="assets/boris-3-jan-26/7.png" alt="슬래시 커맨드" width="50%" /></a>

---

## 8/ 일반적인 워크플로우 자동화에 서브에이전트 사용

몇 가지 서브에이전트를 정기적으로 사용합니다: `code-simplifier`는 Claude가 작업을 완료한 후 코드를 단순화하고, `verify-app`은 Claude Code를 엔드투엔드로 테스트하는 상세한 지침이 있습니다. 서브에이전트를 가장 일반적인 워크플로우를 자동화하는 것으로 생각하세요 — 슬래시 커맨드와 유사합니다.

서브에이전트는 `.claude/agents/`에 있습니다.

<a href="https://x.com/bcherny/status/2007179850139000872"><img src="assets/boris-3-jan-26/8.png" alt="서브에이전트" width="50%" /></a>

---

## 9/ PostToolUse 훅으로 코드 자동 포맷

`PostToolUse` 훅을 사용하여 Claude의 코드를 포맷하세요. Claude는 일반적으로 기본적으로 잘 포맷된 코드를 생성하며, 훅은 나중에 CI에서 포맷 오류를 피하기 위해 마지막 10%를 처리합니다.

```json
"PostToolUse": [
  {
    "matcher": "Write|Edit",
    "hooks": [
      {
        "type": "command",
        "command": "bun run format || true"
      }
    ]
  }
]
```

<a href="https://x.com/bcherny/status/2007179852047335529"><img src="assets/boris-3-jan-26/9.png" alt="포맷팅을 위한 PostToolUse 훅" width="50%" /></a>

---

## 10/ --dangerously-skip-permissions 대신 권한 사전 허용

`--dangerously-skip-permissions`를 사용하지 마세요. 대신 `/permissions`를 사용하여 환경에서 안전하다고 알고 있는 일반 bash 커맨드를 사전 허용하여 불필요한 권한 프롬프트를 피하세요. 이 중 대부분은 `.claude/settings.json`에 체크인되어 팀과 공유됩니다.

<a href="https://x.com/bcherny/status/2007179854077407667"><img src="assets/boris-3-jan-26/10.png" alt="권한 사전 허용" width="50%" /></a>

---

## 11/ MCP를 통해 모든 도구에 Claude 접근 허용

Claude Code는 모든 도구를 사용합니다. (MCP 서버를 통해) Slack에서 검색하고 게시하고, (bq CLI를 사용하여) 분석 질문에 답하기 위해 BigQuery 쿼리를 실행하고, Sentry에서 오류 로그를 가져오는 등의 작업을 자주 합니다. Slack MCP 구성은 `.mcp.json`에 체크인되어 팀과 공유됩니다.

<a href="https://x.com/bcherny/status/2007179856266789204"><img src="assets/boris-3-jan-26/11.png" alt="MCP 도구" width="50%" /></a>

---

## 12/ 백그라운드 에이전트로 장시간 실행 작업 검증

매우 긴 장시간 실행 작업의 경우, (a) 완료 시 백그라운드 에이전트로 작업을 검증하도록 Claude에 프롬프트하거나, (b) 더 결정론적으로 하기 위해 에이전트 Stop 훅을 사용하거나, (c) ralph-wiggum 플러그인 (원래 @GeoffreyHuntley가 구상)을 사용하세요.

<a href="https://x.com/bcherny/status/2007179858435281082"><img src="assets/boris-3-jan-26/12.png" alt="장시간 실행 작업 검증" width="50%" /></a>

---

## 13/ Claude에게 작업 검증 방법 제공

Claude Code에서 훌륭한 결과를 얻기 위해 아마도 가장 중요한 것 — Claude에게 작업을 검증할 방법을 제공하세요. Claude에게 피드백 루프가 있으면 최종 결과의 품질이 2~3배 향상됩니다.

Claude는 Boris가 작성하는 모든 단일 변경을 테스트합니다.

<a href="https://x.com/bcherny/status/2007179861115511237"><img src="assets/boris-3-jan-26/13.png" alt="Claude에게 검증 방법 제공" width="50%" /></a>

---

## 소스

- [Boris Cherny (@bcherny) on X — 2026년 1월 3일](https://x.com/bcherny/status/2007179832300581177)
