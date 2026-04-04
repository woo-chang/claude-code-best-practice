# HOOKS-README
Codex CLI 훅에 대한 모든 세부 사항, 스크립트 및 지침이 포함되어 있습니다.

## 훅 이벤트 개요

Codex CLI는 두 가지 구성 시스템에 걸쳐 **3개의 훅**을 제공합니다:

| # | Hook | Event Type | Config File | Description |
|:-:|------|------------|-------------|-------------|
| 1 | `agent-turn-complete` | `agent-turn-complete` | `config.toml` | Runs when the Codex agent finishes responding |
| 2 | `SessionStart` | `SessionStart` | `hooks.json` | Runs once at session start — injects context + plays sound |
| 3 | `Stop` | `stop` | `hooks.json` | Runs when the session ends — plays sound |

> Hooks 2 and 3 require **Codex CLI v0.114.0+** with the hooks engine enabled:
> ```bash
> codex -c features.codex_hooks=true
> ```

### 훅 호출 방법

**agent-turn-complete 훅** (config.toml) — JSON을 CLI 인수로 전달:
```
python3 .codex/hooks/scripts/hooks.py '{"type":"agent-turn-complete"}'
```

**SessionStart / Stop 훅** (hooks.json) — `--hook` 플래그로 호출:
```
python3 .codex/hooks/scripts/hooks.py --hook SessionStart
python3 .codex/hooks/scripts/hooks.py --hook Stop
```

### SessionStart 컨텍스트 주입

SessionStart 훅은 **stdout**에 컨텍스트를 출력하며, 이는 모델의 컨텍스트 윈도우에 직접 주입됩니다. 포함 내용:
- 현재 날짜/시간
- Git 브랜치 이름
- 작업 트리 상태 (클린 또는 미커밋 변경사항)
- 작업 디렉토리 경로

> **Claude Code와의 주요 차이점:** Claude Code는 **stdin**을 통해 JSON을 전달하는 반면, Codex CLI는 **CLI 인수**로 전달합니다. 새 hooks.json 시스템은 `--hook` 플래그를 사용합니다.

## 사전 요구 사항

훅을 사용하기 전에 시스템에 **Python 3**가 설치되어 있는지 확인하세요.

### 필수 소프트웨어

#### 모든 플랫폼 (Windows, macOS, Linux)
- **Python 3**: 훅 스크립트 실행에 필요
- 설치 확인: `python3 --version`

**설치 안내:**
- **Windows**: [python.org](https://www.python.org/downloads/)에서 다운로드하거나 `winget install Python.Python.3`로 설치
- **macOS**: `brew install python3`으로 설치 ([Homebrew](https://brew.sh/) 필요)
- **Linux**: `sudo apt install python3` (Ubuntu/Debian) 또는 `sudo yum install python3` (RHEL/CentOS)로 설치

### 오디오 플레이어 (자동 감지)

훅 스크립트는 자동으로 플랫폼에 적합한 오디오 플레이어를 감지하여 사용합니다:

- **macOS**: `afplay` 사용 (내장, 별도 설치 불필요)
- **Linux**: `pulseaudio-utils`의 `paplay` 사용 — `sudo apt install pulseaudio-utils`로 설치
- **Windows**: 내장 `winsound` 모듈 사용 (Python에 포함됨)

### 구성 파일

**세 개**의 구성 파일이 있습니다:

1. **`.codex/config.toml`** — `agent-turn-complete` 훅 등록 (`notify` 경유)
2. **`.codex/hooks.json`** — `SessionStart` 및 `Stop` 훅 등록 (v0.114.0+)
3. **`.codex/hooks/config/hooks-config.json`** — 개별 훅 및 로깅 활성화/비활성화

#### config.toml (agent-turn-complete hook)

```toml
notify = ["python3", ".codex/hooks/scripts/hooks.py"]
```

#### hooks.json (SessionStart + Stop hooks)

```json
{
  "hooks": {
    "SessionStart": [
      {
        "type": "shell",
        "command": "python3 .codex/hooks/scripts/hooks.py --hook SessionStart",
        "statusMessage": "Initializing session hooks...",
        "timeout": 10
      }
    ],
    "Stop": [
      {
        "type": "shell",
        "command": "python3 .codex/hooks/scripts/hooks.py --hook Stop",
        "statusMessage": "Running session stop hook...",
        "timeout": 10
      }
    ]
  }
}
```

## 훅 구성 (활성화/비활성화)

### 개별 훅 비활성화

`.codex/hooks/config/hooks-config.json` 편집:
```json
{
  "disableAgentTurnCompleteHook": false,
  "disableSessionStartHook": false,
  "disableStopHook": false,
  "disableLogging": true
}
```

**구성 옵션:**
- `disableAgentTurnCompleteHook`: `true`로 설정하면 agent-turn-complete 사운드 비활성화
- `disableSessionStartHook`: `true`로 설정하면 세션 시작 컨텍스트 주입 및 사운드 비활성화
- `disableStopHook`: `true`로 설정하면 세션 종료 사운드 비활성화
- `disableLogging`: `true`로 설정하면 `.codex/hooks/logs/hooks-log.jsonl`에 훅 이벤트 로깅 비활성화

### 구성 폴백

두 개의 구성 파일이 있습니다:

1. **`.codex/hooks/config/hooks-config.json`** - git에 커밋된 공유/기본 구성
2. **`.codex/hooks/config/hooks-config.local.json`** - 개인 재정의 (git에서 무시됨)

로컬 구성 파일(`.local.json`)이 공유 구성보다 우선하므로, 각 개발자가 팀에 영향을 주지 않고 훅 동작을 사용자 정의할 수 있습니다.

#### 로컬 구성 (개인 재정의)

개인 설정을 위해 `.codex/hooks/config/hooks-config.local.json`을 생성하거나 편집하세요:

```json
{
  "disableAgentTurnCompleteHook": true,
  "disableSessionStartHook": false,
  "disableStopHook": true,
  "disableLogging": true
}
```

### 로깅

로깅이 활성화된 경우(`"disableLogging": false`), 훅 이벤트는 JSON Lines 형식으로 `.codex/hooks/logs/hooks-log.jsonl`에 기록됩니다. 각 항목에는 Codex CLI에서 수신한 전체 JSON 페이로드가 포함됩니다.

## 테스트

테스트 스위트 실행:
```bash
python3 -m unittest tests.test_hooks -v
```

## 향후 확장 가능성

이 프로젝트는 다음 방법으로 확장할 수 있습니다:

1. `hooks.py`의 `HOOK_SOUND_MAP`에 새 항목 추가
2. `.codex/hooks/sounds/`에 해당 사운드 파일 추가
3. `hooks-config.json`에 토글 키 추가
4. `hooks.json`에 새 훅 항목 추가
