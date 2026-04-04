# Claude Agent SDK vs Claude CLI: 시스템 프롬프트와 출력 일관성

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

![SDK vs CLI 시스템 프롬프트 다이어그램](assets/sdk-vs-cli-diagram.svg)

---

## 요약

동일한 메시지 (예: "노르웨이의 수도는 어디입니까?")를 **Claude Agent SDK**와 **Claude CLI (Claude Code)**를 통해 전송할 때, 이 메시지와 함께 전달되는 시스템 프롬프트는 근본적으로 다릅니다. CLI는 **모듈식 시스템 프롬프트 아키텍처** (~269 기본 토큰, 기능에 따라 조건부로 로드되는 추가 컨텍스트)를 사용하는 반면, SDK는 기본적으로 최소 프롬프트를 사용합니다. **두 가지 사이의 동일한 출력은 보장되지 않습니다**, 시드 매개변수 부재와 Claude 아키텍처의 내재된 비결정론 때문입니다.

---

## 1. 시스템 프롬프트 비교

### Claude CLI (Claude Code)

Claude CLI는 **모듈식 시스템 프롬프트 아키텍처**를 사용하며 ~269 토큰의 기본 프롬프트와 조건부로 로드되는 추가 컨텍스트가 있습니다:

| 구성 요소 | 설명 | 로딩 |
|-----------|-------------|---------|
| **기본 시스템 프롬프트** | 핵심 지침 및 동작 | 항상 (~269 토큰) |
| **도구 지침** | 18개 이상의 내장 도구 (Write, Read, Edit, Bash, TodoWrite 등) | 항상 |
| **코딩 가이드라인** | 코드 스타일, 형식 규칙, 보안 관행 | 항상 |
| **안전 규칙** | 거부 규칙, 주입 방어, 피해 방지 | 항상 |
| **응답 스타일** | 톤, 상세도, 설명 깊이, 이모지 사용 | 항상 |
| **환경 컨텍스트** | 작업 디렉토리, git 상태, 플랫폼 정보 | 항상 |
| **프로젝트 컨텍스트** | CLAUDE.md 내용, 설정, 훅 구성 | 조건부 |
| **서브에이전트 프롬프트** | 플랜 모드, Explore 에이전트, Task 에이전트 | 조건부 |
| **보안 검토** | 확장된 보안 지침 (~2,610 토큰) | 조건부 |

**주요 특성:**
- 110개 이상의 시스템 프롬프트 문자열이 조건부로 로드되는 **모듈식 아키텍처**
- 기본 프롬프트는 작습니다 (~269 토큰), 활성화된 기능에 따라 총계 변동
- 광범위한 보안 및 주입 방어 레이어 포함
- 작업 디렉토리의 CLAUDE.md 파일 자동 로드
- 대화형 모드에서 세션 영속 컨텍스트

### Claude Agent SDK

Agent SDK는 기본적으로 **최소 시스템 프롬프트**를 사용하며 다음을 포함합니다:

| 구성 요소 | 설명 | 토큰 영향 |
|-----------|-------------|--------------|
| **필수 도구 지침** | 명시적으로 제공된 도구만 | 최소 |
| **기본 안전** | 최소한의 안전 지침 | 최소 |

**주요 특성:**
- 기본적으로 코딩 가이드라인이나 스타일 선호 없음
- 명시적으로 구성하지 않으면 프로젝트 컨텍스트 없음
- 광범위한 도구 설명 없음
- CLI 동작을 맞추려면 명시적 구성 필요

---

## 2. 각 인터페이스가 보내는 것

### 예시: "노르웨이의 수도는 어디입니까?"

#### Claude CLI를 통해

```
System Prompt: [modular, ~269+ base tokens]
├── Base system prompt (~269 tokens)
├── Tool instructions (Write, Read, Edit, Bash, Grep, Glob, etc.)
├── Git safety protocols
├── Code reference guidelines
├── Professional objectivity instructions
├── Security and injection defense rules
├── Environment context (OS, directory, date)
├── CLAUDE.md content (if present) [conditional]
├── MCP tool descriptions (if configured) [conditional]
├── Plan/Explore mode prompts [conditional]
└── Session/conversation context

User Message: "What is the capital of Norway?"
```

#### Claude Agent SDK (기본값)를 통해

```
System Prompt: [minimal]
├── Essential tool instructions (if any tools provided)
└── Basic operational context

User Message: "What is the capital of Norway?"
```

#### Agent SDK (`claude_code` 프리셋 사용)를 통해

```typescript
const response = await query({
  prompt: "What is the capital of Norway?",
  options: {
    systemPrompt: {
      type: "preset",
      preset: "claude_code"
    }
  }
});
```

```
System Prompt: [modular, matches CLI]
├── Full Claude Code system prompt
├── Tool instructions
├── Coding guidelines
└── Safety rules

// NOTE: Still does NOT include CLAUDE.md unless settingSources is configured
```

---

## 3. 사용자 정의 방법

### Claude CLI 사용자 정의

| 방법 | 커맨드 | 효과 |
|--------|---------|--------|
| **프롬프트에 추가** | `claude -p "..." --append-system-prompt "..."` | 기본값을 보존하면서 지침 추가 |
| **프롬프트 대체** | `claude -p "..." --system-prompt "..."` | 시스템 프롬프트 완전 대체 |
| **프로젝트 컨텍스트** | CLAUDE.md 파일 | 자동 로드, 영속적 |
| **출력 스타일** | `/output-style [name]` | 미리 정의된 응답 스타일 적용 |

### Agent SDK 사용자 정의

| 방법 | 구성 | 효과 |
|--------|---------------|--------|
| **사용자 정의 프롬프트** | `systemPrompt: "..."` | 기본값 완전 대체 (도구 손실) |
| **추가가 있는 프리셋** | `systemPrompt: { type: "preset", preset: "claude_code", append: "..." }` | CLI 기능 + 사용자 정의 지침 보존 |
| **CLAUDE.md 로딩** | `settingSources: ["project"]` | 프로젝트 수준 지침 로드 |
| **출력 스타일** | `settingSources: ["user"]` 또는 `settingSources: ["project"]` | 저장된 출력 스타일 로드 |

### 구성 비교 표

| 기능 | CLI 기본값 | SDK 기본값 | 프리셋 사용 SDK |
|---------|-------------|-------------|-----------------|
| 도구 지침 | ✅ 전체 | ❌ 최소 | ✅ 전체 |
| 코딩 가이드라인 | ✅ 예 | ❌ 아니오 | ✅ 예 |
| 안전 규칙 | ✅ 예 | ❌ 기본 | ✅ 예 |
| CLAUDE.md 자동 로드 | ✅ 예 | ❌ 아니오 | ❌ 아니오* |
| 프로젝트 컨텍스트 | ✅ 자동 | ❌ 아니오 | ❌ 아니오* |

*명시적인 `settingSources: ["project"]` 구성 필요

---

## 4. 출력 일관성 보장

### 중요 발견: 결정론 보장 없음

**Claude Messages API는 재현성을 위한 시드 매개변수를 제공하지 않습니다.** 이것은 근본적인 아키텍처 제한입니다.

### 동일한 출력을 방지하는 요인

| 요인 | 설명 | 제어 가능? |
|--------|-------------|---------------|
| **다른 시스템 프롬프트** | CLI vs SDK는 다른 기본값을 가짐 | ✅ 예 (구성으로) |
| **부동 소수점 연산** | 병렬 하드웨어 특성 | ❌ 아니오 |
| **MoE 라우팅** | 전문가 혼합 아키텍처 변동 | ❌ 아니오 |
| **배치/스케줄링** | 클라우드 인프라 차이 | ❌ 아니오 |
| **수치 정밀도** | 추론 엔진 변동 | ❌ 아니오 |
| **모델 스냅샷** | 버전 업데이트/변경 | ❌ 아니오 |

### 온도 및 샘플링

`temperature=0.0` (탐욕적 디코딩)에서도:
- 완전한 결정론은 **보장되지 않습니다**
- 인프라 요인으로 인해 경미한 변동이 여전히 발생할 수 있습니다
- 알려진 버그: [Claude CLI가 동일한 입력에 대해 비결정론적 출력 생성](https://github.com/anthropics/claude-code/issues/3370)

---

## 5. 최대 일관성 달성

SDK와 CLI 사이에서 **가능한 한 동일한** 출력을 얻으려면:

### Agent SDK 구성

```typescript
import Anthropic from "@anthropic-ai/sdk";

const client = new Anthropic();

// 옵션 1: claude_code 프리셋 사용
const response = await client.messages.create({
  model: "claude-sonnet-4-20250514",
  max_tokens: 1024,
  // CLI 시스템 프롬프트와 최대한 맞추기
  system: "Your exact system prompt matching CLI",
  messages: [
    { role: "user", content: "What is the capital of Norway?" }
  ],
  // 최대 일관성을 위한 탐욕적 디코딩
  temperature: 0
});

// 옵션 2: Agent SDK query 함수 사용
import { query } from "@anthropic-ai/agent-sdk";

for await (const message of query({
  prompt: "What is the capital of Norway?",
  options: {
    systemPrompt: {
      type: "preset",
      preset: "claude_code"
    },
    temperature: 0,
    model: "claude-sonnet-4-20250514",
    // CLI처럼 프로젝트 컨텍스트 로드
    settingSources: ["project"]
  }
})) {
  // 응답 처리
}
```

### CLI 구성

```bash
# SDK 구성과 최대한 맞추기
claude -p "What is the capital of Norway?" \
  --model claude-sonnet-4-20250514 \
  --temperature 0
```

### 여전히 보장되지 않음

완벽하게 일치하는 구성으로도:
- 실행 간에 출력이 다를 수 있음
- SDK와 CLI 간에 출력이 다를 수 있음
- 재현성을 강제하는 시드 매개변수 없음

---

## 6. 실용적인 시사점

### 각 인터페이스 사용 시기

| 사용 사례 | 권장 인터페이스 | 이유 |
|----------|----------------------|--------|
| 대화형 개발 | Claude CLI | 전체 도구 세트, 프로젝트 컨텍스트 |
| 프로그래밍 방식 통합 | Agent SDK | 세밀한 제어, 임베딩 |
| 일관된 API 응답 | Agent SDK + 사용자 정의 프롬프트 | 시스템 프롬프트에 대한 더 많은 제어 |
| 일괄 처리 | Agent SDK | 자동화 파이프라인에 더 적합 |
| 일회성 작업 | Claude CLI | 더 빠른 설정, 즉각적인 컨텍스트 |

### 설계 권장 사항

1. **비트 완벽한 재현성에 의존하지 마세요**
   - 경미한 출력 변동에 강건한 애플리케이션 구축
   - 구조화된 출력 및 검증 사용

2. **일관성이 필요한 프로덕션 파이프라인의 경우:**
   - 가능하면 결과 캐시
   - JSON 스키마 검증이 있는 구조화된 출력 사용
   - 결정론적 로직 및 검증과 결합
   - 합의를 통한 여러 생성 고려

3. **SDK에서 CLI 동작 맞추기:**
   ```typescript
   systemPrompt: {
     type: "preset",
     preset: "claude_code",
     append: "Your additional instructions"
   },
   settingSources: ["project", "user"]
   ```

---

## 7. 시스템 프롬프트 토큰 영향

| 구성 | 아키텍처 | 참고 |
|---------------|-------------|-------|
| SDK (최소) | 최소 기본값 | 필수 도구 지침만 |
| SDK (claude_code 프리셋) | 모듈식 (~269+ 기본) | CLI와 일치, 기능에 따라 변동 |
| CLI (기본값) | 모듈식 (~269+ 기본) | 조건부로 로드된 추가 컨텍스트 |
| CLI (MCP 도구 포함) | 모듈식 + MCP | MCP 도구 설명이 상당한 토큰 추가 |

**참고:** Claude Code는 110개 이상의 시스템 프롬프트 문자열의 모듈식 아키텍처를 사용합니다. 기본 프롬프트는 ~269 토큰이며, 활성화된 기능에 따라 개별 구성 요소는 18에서 2,610 토큰 사이입니다.

**시사점:** SDK의 최소 기본값은 실제 작업을 위한 더 많은 컨텍스트를 제공하지만, Claude Code의 전체 기능을 희생합니다.

---

## 8. 요약 표

| 측면 | Claude CLI | Agent SDK (기본값) | Agent SDK (프리셋) |
|--------|------------|--------------------|--------------------|
| **시스템 프롬프트** | 모듈식 (~269+ 기본) | 최소 | 모듈식 (CLI와 일치) |
| **포함된 도구** | 18개 이상 내장 | 제공된 경우만 | 18개 이상 내장 |
| **CLAUDE.md 자동 로드** | 예 | 아니오 | 아니오 (구성 필요) |
| **코딩 가이드라인** | 예 | 아니오 | 예 |
| **안전 규칙** | 전체 | 기본 | 전체 |
| **온도 제어** | 예 | 예 | 예 |
| **결정론 보장** | 아니오 | 아니오 | 아니오 |
| **동일한 출력?** | 해당 없음 | 아니오 (CLI 대비) | 더 가깝지만, 아니오 |

---

## 9. 결론

**Q: SDK vs CLI에서 동일한 메시지에 어떤 시스템 프롬프트가 동반됩니까?**

CLI는 ~269 토큰의 기본 프롬프트와 110개 이상의 조건부 로드 구성 요소 (도구 지침, 코딩 가이드라인, 안전 규칙, 프로젝트 컨텍스트)가 있는 **모듈식 시스템 프롬프트 아키텍처**를 사용합니다. SDK는 필수 도구 지침만 있는 **최소 기본값**을 사용하지만, `claude_code` 프리셋을 사용하여 CLI 동작을 맞추도록 구성할 수 있습니다.

**Q: 동일한 출력이 보장됩니까?**

**아니오.** 일치하는 시스템 프롬프트, 동일한 입력, `temperature=0`으로도 동일한 출력은 보장되지 않습니다:
- Claude API에 시드 매개변수 부재
- 부동 소수점 연산 변동
- 인프라 수준의 비결정론
- 모델 아키텍처 (전문가 혼합) 라우팅 변동

**권장 사항:** 결정론적 동작에 의존하기보다 출력 변동에 강건한 시스템을 설계하세요. 일관성이 중요한 애플리케이션의 경우 구조화된 출력, 캐싱, 검증 레이어를 사용하세요.

---

## 소스

- [시스템 프롬프트 수정 - Agent SDK](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/sdk#modifying-system-prompts)
- [Claude Code CLI 참조](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/cli)
- [Claude Code 헤드리스 모드](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/headless)
- [Claude Code 모범 사례 - Anthropic 엔지니어링](https://www.anthropic.com/engineering/claude-code-best-practices)
- [Claude Messages API 참조](https://docs.anthropic.com/en/api/messages)
- [GitHub 이슈 #3370: 비결정론적 출력](https://github.com/anthropics/claude-code/issues/3370)
- [Claude Code 시스템 프롬프트 리포지토리](https://github.com/Piebald-AI/claude-code-system-prompts) - 모듈식 프롬프트 아키텍처 분석
- [LLM에서 결정론적 출력이 거의 불가능한 이유](https://unstract.com/blog/understanding-why-deterministic-output-from-llms-is-nearly-impossible/)

---

*이 리포트는 2026년 2월 3일에 Opus 4.5 모델을 사용하여 Claude Code에 의해 생성되었습니다.*
