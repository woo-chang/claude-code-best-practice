# Day 0 — Claude Code 설치

이 가이드는 머신에 Claude Code를 설치하고 인증하여 사용을 시작하는 과정을 안내합니다.

## 1단계: Claude Code 설치

운영 체제를 선택하세요:

| OS | 가이드 |
|----|-------|
| Windows | [windows.md](windows.md) |
| Linux | [linux.md](linux.md) |
| macOS | [mac.md](mac.md) |

해당 OS 가이드를 따른 후, 인증을 위해 다시 이 페이지로 돌아오세요.

---

## 2단계: 설치 확인

OS별 가이드를 따른 후, 모든 것이 정상적으로 작동하는지 확인합니다:

```bash
node --version    # v18.x 이상이 표시되어야 함
claude --version  # 설치된 Claude Code 버전이 표시되어야 함
```

---

## 3단계: 로그인

<img src="assets/login.png" alt="Claude Code 로그인 화면" width="50%">

터미널에서 `claude`를 실행하세요. 첫 실행 시 로그인 방법을 선택하라는 메시지가 표시됩니다.

### 방법 1: 구독 (Claude Pro / Max)

- **Claude.ai 계정** 선택
- 브라우저가 열림 — 로그인 후 승인
- 터미널로 돌아오면 로그인 완료

### 방법 2a: API 키 (팀 초대)

팀 관리자가 Anthropic 대시보드에서 초대를 보냅니다.

- **초대 이메일**을 받으면 수락하고 Anthropic 계정을 생성하세요
- 터미널에서 `claude`를 실행하세요
- **Anthropic API Key** 선택
- 키는 대시보드에서 **자동 생성**됨 — 수동 설정 불필요
- Claude Code가 즉시 작동 시작

### 방법 2b: API 키 (키를 직접 보유한 경우)

Slack, 이메일 등으로 키를 공유받거나 직접 생성한 경우:

- 터미널에서 `claude`를 실행하세요
- **Anthropic API Key** 선택
- 키를 붙여넣기 (키는 `sk-ant-`로 시작)
- 키는 **영구 저장**됨 — 다시 묻지 않습니다

---
