# Claude Code: 사용량, 속도 제한 및 추가 사용

Claude Code의 사용량 제한 작동 방식과 한도에 도달했을 때 계속 작업하는 방법을 이해합니다.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 개요

구독 플랜 (Pro, Max 5x, Max 20x)의 Claude Code는 롤링 윈도우로 초기화되는 사용량 제한이 있습니다. 세 가지 내장 슬래시 커맨드로 사용량을 모니터링하고 관리할 수 있습니다:

| 커맨드 | 설명 | 사용 가능 대상 |
|---------|-------------|--------------|
| `/usage` | 플랜 제한 및 속도 제한 상태 확인 | Pro, Max 5x, Max 20x |
| `/extra-usage` | 제한 도달 시 종량제 초과 구성 | Pro, Max 5x, Max 20x |
| `/cost` | 현재 세션의 토큰 사용량 및 지출 표시 | API 키 사용자 |

---

## `/usage` — 제한 확인

현재 플랜의 사용량 제한 및 속도 제한 상태를 표시합니다. 제한에 도달하기 전에 남은 용량을 확인하는 데 유용합니다.

---

## `/extra-usage` — 제한을 넘어 계속 작업

`/extra-usage` 커맨드는 **종량제 초과 청구**를 구성하여 플랜의 속도 제한에 도달했을 때 차단되는 대신 Claude Code가 원활하게 계속 작동하도록 합니다.

### 작동 방식

1. 플랜의 속도 제한에 도달합니다 (제한은 5시간마다 초기화)
2. 추가 사용이 활성화되고 가용 자금이 있으면 Claude Code가 중단 없이 계속됩니다
3. 초과 토큰은 구독 요금과 별도로 **표준 API 요금**으로 청구됩니다

### 설정 방법

CLI의 `/extra-usage` 커맨드가 구성 과정을 안내합니다. claude.ai의 **설정 > 사용량**에서 웹으로도 구성할 수 있습니다:

1. 추가 사용 활성화
2. 결제 수단 추가
3. **월별 지출 한도** 설정 (또는 무제한 선택)
4. 선택적으로 **선불 자금** 추가 및 잔액이 임계값 이하로 떨어질 때 자동 재충전 설정

### 주요 세부 사항

| 세부 사항 | 값 |
|--------|-------|
| 일일 사용 한도 | $2,000/일 |
| 청구 | 구독과 별도, 표준 API 요금으로 |
| 제한 초기화 윈도우 | 5시간마다 |

### 알려진 문제

2026년 2월 기준, `/extra-usage` CLI 커맨드는 [문서화되지 않은 상태](https://github.com/anthropics/claude-code/issues/12396)이며 명확한 구성 옵션 없이 로그인 창을 열 수 있습니다. 현재로서는 **claude.ai 웹 인터페이스**를 통한 구성이 더 안정적인 방법입니다.

---

## `/cost` — 세션 지출 (API 사용자)

API 키로 인증하는 사용자 (구독 플랜이 아닌)의 경우, `/cost`는 다음을 표시합니다:

- 현재 세션의 총 비용
- API 지속 시간 및 실제 경과 시간
- 토큰 사용량 분석
- 변경된 코드

이 커맨드는 Pro/Max 구독 사용자에게는 관련이 없습니다.

---

## 고속 모드와 추가 사용

고속 모드 (`/fast`)는 Claude Opus 4.6을 더 빠른 출력으로 사용합니다. 추가 사용과 특별한 청구 관계가 있습니다:

- 고속 모드 사용은 **첫 번째 토큰부터 추가 사용으로 청구됩니다**
- 구독 플랜에 남은 사용량이 있더라도 마찬가지입니다
- 고속 모드는 플랜의 포함된 속도 제한을 소모하지 않습니다

따라서 `/fast`를 사용하려면 추가 사용이 활성화되고 자금이 있어야 합니다.

---

## CLI 시작 플래그

사용량 예산과 관련된 두 가지 시작 플래그 (API 키 사용자 전용, 프린트 모드):

| 플래그 | 설명 |
|------|-------------|
| `--max-budget-usd <AMOUNT>` | 중지하기 전 API 호출의 최대 달러 금액 |
| `--max-turns <NUMBER>` | 에이전틱 턴 수 제한 |

전체 목록은 [CLI 시작 플래그 참조](claude-cli-startup-flags.md)를 참조하세요.

---

## 소스

- [유료 Claude 플랜의 추가 사용 — Claude 헬프 센터](https://support.claude.com/en/articles/12429409-extra-usage-for-paid-claude-plans)
- [Pro 또는 Max 플랜으로 Claude Code 사용 — Claude 헬프 센터](https://support.claude.com/en/articles/11145838-using-claude-code-with-your-pro-or-max-plan)
- [/extra-usage 슬래시 커맨드 미문서 — GitHub 이슈 #12396](https://github.com/anthropics/claude-code/issues/12396)
- [Claude Code CLI 참조](https://code.claude.com/docs/en/cli-reference)
