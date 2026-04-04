# claude-code-best-practice
practice makes claude perfect

![updated with Claude Code](https://img.shields.io/badge/updated_with_Claude_Code-v2.1.90%20(Apr%2002%2C%202026%209%3A43%20PM%20PKT)-white?style=flat&labelColor=555) <a href="https://github.com/shanraisshan/claude-code-best-practice/stargazers"><img src="https://img.shields.io/github/stars/shanraisshan/claude-code-best-practice?style=flat&label=%E2%98%85&labelColor=555&color=white" alt="GitHub Stars"></a><br>

[![Best Practice](!/tags/best-practice.svg)](best-practice/) [![Implemented](!/tags/implemented.svg)](implementation/) [![Orchestration Workflow](!/tags/orchestration-workflow.svg)](orchestration-workflow/orchestration-workflow.md) [![Boris](!/tags/boris-cherny.svg)](#-tips-and-tricks) ![Click on these badges below to see the actual sources](!/tags/click-badges.svg)<br>
<img src="!/tags/a.svg" height="14"> = 에이전트 · <img src="!/tags/c.svg" height="14"> = 커맨드 · <img src="!/tags/s.svg" height="14"> = 스킬

<p align="center">
  <img src="!/claude-jumping.svg" alt="Claude Code mascot jumping" width="120" height="100"><br>
  <a href="https://github.com/trending"><img src="!/root/github-trending-day.svg" alt="GitHub Trending #1 Repository Of The Day"></a>
</p>

<p align="center">
  <img src="!/root/boris-slider.gif" alt="Boris Cherny on Claude Code" width="600"><br>
  Boris Cherny on X (<a href="https://x.com/bcherny/status/2007179832300581177">tweet 1</a> · <a href="https://x.com/bcherny/status/2017742741636321619">tweet 2</a> · <a href="https://x.com/bcherny/status/2021699851499798911">tweet 3</a>)
</p>


## 🧠 개념 (CONCEPTS)

| 기능 | 위치 | 설명 |
|---------|----------|-------------|
| <img src="!/tags/a.svg" height="14"> [**서브에이전트**](https://code.claude.com/docs/en/sub-agents) | `.claude/agents/<name>.md` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-subagents.md) [![Implemented](!/tags/implemented.svg)](implementation/claude-subagents-implementation.md) 새로운 격리된 컨텍스트에서 동작하는 자율 행위자 — 커스텀 도구, 권한, 모델, 메모리, 지속적 정체성 |
| <img src="!/tags/c.svg" height="14"> [**커맨드**](https://code.claude.com/docs/en/slash-commands) | `.claude/commands/<name>.md` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-commands.md) [![Implemented](!/tags/implemented.svg)](implementation/claude-commands-implementation.md) 기존 컨텍스트에 주입되는 지식 — 워크플로우 오케스트레이션을 위한 사용자 호출 프롬프트 템플릿 |
| <img src="!/tags/s.svg" height="14"> [**스킬**](https://code.claude.com/docs/en/skills) | `.claude/skills/<name>/SKILL.md` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-skills.md) [![Implemented](!/tags/implemented.svg)](implementation/claude-skills-implementation.md) 기존 컨텍스트에 주입되는 지식 — 구성 가능하고, 사전 로드 가능하며, 자동 발견이 가능하고, 컨텍스트 포킹 및 점진적 공개 지원 · [공식 스킬](https://github.com/anthropics/skills/tree/main/skills) |
| [**워크플로우**](https://code.claude.com/docs/en/common-workflows) | [`.claude/commands/weather-orchestrator.md`](.claude/commands/weather-orchestrator.md) | [![Orchestration Workflow](!/tags/orchestration-workflow.svg)](orchestration-workflow/orchestration-workflow.md) |
| [**훅**](https://code.claude.com/docs/en/hooks) | `.claude/hooks/` | [![Best Practice](!/tags/best-practice.svg)](https://github.com/shanraisshan/claude-code-hooks) [![Implemented](!/tags/implemented.svg)](https://github.com/shanraisshan/claude-code-hooks) 특정 이벤트에서 에이전틱 루프 외부에서 실행되는 사용자 정의 핸들러 (스크립트, HTTP, 프롬프트, 에이전트) · [가이드](https://code.claude.com/docs/en/hooks-guide) |
| [**MCP 서버**](https://code.claude.com/docs/en/mcp) | `.claude/settings.json`, `.mcp.json` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-mcp.md) [![Implemented](!/tags/implemented.svg)](.mcp.json) 외부 도구, 데이터베이스, API에 대한 Model Context Protocol 연결 |
| [**플러그인**](https://code.claude.com/docs/en/plugins) | 배포 가능한 패키지 | 스킬, 서브에이전트, 훅, MCP 서버, LSP 서버의 번들 · [마켓플레이스](https://code.claude.com/docs/en/discover-plugins) · [마켓플레이스 만들기](https://code.claude.com/docs/en/plugin-marketplaces) |
| [**설정**](https://code.claude.com/docs/en/settings) | `.claude/settings.json` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-settings.md) [![Implemented](!/tags/implemented.svg)](.claude/settings.json) 계층적 구성 시스템 · [권한](https://code.claude.com/docs/en/permissions) · [모델 구성](https://code.claude.com/docs/en/model-config) · [출력 스타일](https://code.claude.com/docs/en/output-styles) · [샌드박싱](https://code.claude.com/docs/en/sandboxing) · [키 바인딩](https://code.claude.com/docs/en/keybindings) · [Fast Mode](https://code.claude.com/docs/en/fast-mode) |
| [**상태 표시줄**](https://code.claude.com/docs/en/statusline) | `.claude/settings.json` | [![Best Practice](!/tags/best-practice.svg)](https://github.com/shanraisshan/claude-code-status-line) [![Implemented](!/tags/implemented.svg)](.claude/settings.json) 컨텍스트 사용량, 모델, 비용, 세션 정보를 표시하는 커스터마이즈 가능한 상태 표시줄 |
| [**메모리**](https://code.claude.com/docs/en/memory) | `CLAUDE.md`, `.claude/rules/`, `~/.claude/rules/`, `~/.claude/projects/<project>/memory/` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-memory.md) [![Implemented](!/tags/implemented.svg)](CLAUDE.md) CLAUDE.md 파일과 `@path` 임포트를 통한 영속적 컨텍스트 · [자동 메모리](https://code.claude.com/docs/en/memory) · [Rules](https://code.claude.com/docs/en/memory#organize-rules-with-clauderules) |
| [**체크포인팅**](https://code.claude.com/docs/en/checkpointing) | 자동 (git 기반) | 되감기 (`Esc Esc` 또는 `/rewind`)와 타겟 요약을 통한 파일 편집 자동 추적 |
| [**CLI 시작 플래그**](https://code.claude.com/docs/en/cli-reference) | `claude [flags]` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-cli-startup-flags.md) Claude Code 실행을 위한 커맨드 라인 플래그, 서브커맨드, 환경 변수 · [인터랙티브 모드](https://code.claude.com/docs/en/interactive-mode) |
| **AI 용어** | | [![Best Practice](!/tags/best-practice.svg)](https://github.com/shanraisshan/claude-code-codex-cursor-gemini/blob/main/reports/ai-terms.md) 에이전틱 엔지니어링 · 컨텍스트 엔지니어링 · Vibe Coding |
| [**모범 사례**](https://code.claude.com/docs/en/best-practices) | | 공식 모범 사례 · [프롬프트 엔지니어링](https://github.com/anthropics/prompt-eng-interactive-tutorial) · [Claude Code 확장](https://code.claude.com/docs/en/features-overview) |

### 🔥 핫 (Hot)

| 기능 | 위치 | 설명 |
|---------|----------|-------------|
| [**Power-ups**](best-practice/claude-power-ups.md) | `/powerup` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-power-ups.md) 애니메이션 데모와 함께 Claude Code 기능을 가르치는 인터랙티브 레슨 (v2.1.90) |
| [**No Flicker Mode**](https://code.claude.com/docs/en/fullscreen) ![beta](!/tags/beta.svg) | `CLAUDE_CODE_NO_FLICKER=1` | [![Best Practice](!/tags/best-practice.svg)](https://x.com/bcherny/status/2039421575422980329) 마우스 지원, 안정적인 메모리, 앱 내 스크롤이 있는 플리커 없는 alt-screen 렌더링 — 옵트인 연구 미리 보기 |
| [**Computer Use**](https://code.claude.com/docs/en/computer-use) ![beta](!/tags/beta.svg) | `computer-use` MCP server | Claude가 화면을 제어하도록 하세요 — macOS에서 앱을 열고, 클릭하고, 타이핑하고, 디스플레이 스크린샷 찍기 · [데스크탑](https://code.claude.com/docs/en/desktop#let-claude-use-your-computer) |
| [**Auto Mode**](https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode) ![beta](!/tags/beta.svg) | `claude --enable-auto-mode` | [![Best Practice](!/tags/best-practice.svg)](https://x.com/claudeai/status/2036503582166393240) 백그라운드 안전 분류기가 수동 권한 프롬프트를 대체 — Claude가 프롬프트 주입과 위험한 에스컬레이션을 차단하면서 무엇이 안전한지 결정 · `claude --enable-auto-mode` (또는 `--permission-mode auto`)로 시작하거나, 세션 중 `Shift+Tab`으로 전환 · [블로그](https://claude.com/blog/auto-mode) |
| [**Channels**](https://code.claude.com/docs/en/channels) ![beta](!/tags/beta.svg) | `--channels`, plugin-based | Telegram, Discord, 또는 웹훅에서 실행 중인 세션으로 이벤트를 푸시 — 당신이 자리를 비운 동안 Claude가 반응 · [참조](https://code.claude.com/docs/en/channels-reference) |
| [**Slack**](https://code.claude.com/docs/en/slack) | `@Claude` in Slack | 팀 채팅에서 코딩 태스크와 함께 @Claude를 언급 — 버그 수정, 코드 리뷰, 병렬 태스크 실행을 위한 Claude Code 웹 세션으로 라우팅 |
| [**코드 리뷰**](https://code.claude.com/docs/en/code-review) ![beta](!/tags/beta.svg) | GitHub App (관리형) | [![Best Practice](!/tags/best-practice.svg)](https://x.com/claudeai/status/2031088171262554195) 버그, 보안 취약점, 회귀를 잡는 멀티 에이전트 PR 분석 · [블로그](https://claude.com/blog/code-review) |
| [**GitHub Actions**](https://code.claude.com/docs/en/github-actions) | `.github/workflows/` | CI/CD 파이프라인에서 PR 리뷰, 이슈 트리아지, 코드 생성 자동화 · [GitLab CI/CD](https://code.claude.com/docs/en/gitlab-ci-cd) |
| [**Chrome**](https://code.claude.com/docs/en/chrome) ![beta](!/tags/beta.svg) | `--chrome`, extension | [![Best Practice](!/tags/best-practice.svg)](reports/claude-in-chrome-v-chrome-devtools-mcp.md) Claude in Chrome을 통한 브라우저 자동화 — 웹 앱 테스트, 콘솔 디버깅, 양식 자동화, 페이지에서 데이터 추출 |
| [**스케줄된 태스크**](https://code.claude.com/docs/en/scheduled-tasks) | `/loop`, `/schedule`, cron tools | [![Best Practice](!/tags/best-practice.svg)](https://x.com/bcherny/status/2030193932404150413) [![Implemented](!/tags/implemented.svg)](implementation/claude-scheduled-tasks-implementation.md) `/loop`는 반복 일정으로 로컬에서 프롬프트 실행 (최대 3일) · [`/schedule`](https://code.claude.com/docs/en/web-scheduled-tasks)는 Anthropic 인프라에서 클라우드로 실행 — 머신이 꺼져 있어도 작동 · [발표](https://x.com/noahzweben/status/2036129220959805859) |
| [**음성 받아쓰기**](https://code.claude.com/docs/en/voice-dictation) ![beta](!/tags/beta.svg) | `/voice` | [![Best Practice](!/tags/best-practice.svg)](https://x.com/trq212/status/2028628570692890800) 20개 언어 지원과 재바인딩 가능한 활성화 키가 있는 프롬프트용 Push-to-talk 음성 입력 |
| [**Simplify & Batch**](https://code.claude.com/docs/en/skills#bundled-skills) | `/simplify`, `/batch` | [![Best Practice](!/tags/best-practice.svg)](https://x.com/bcherny/status/2027534984534544489) 코드 품질과 대량 작업을 위한 내장 스킬 — simplify는 재사용과 효율성을 위해 리팩토링하고, batch는 파일 전반에 커맨드를 실행 |
| [**에이전트 팀**](https://code.claude.com/docs/en/agent-teams) ![beta](!/tags/beta.svg) | 내장 (환경 변수) | [![Best Practice](!/tags/best-practice.svg)](https://x.com/bcherny/status/2019472394696683904) [![Implemented](!/tags/implemented.svg)](implementation/claude-agent-teams-implementation.md) 공유 태스크 조율과 함께 동일한 코드베이스에서 병렬로 작업하는 여러 에이전트 |
| [**원격 제어**](https://code.claude.com/docs/en/remote-control) | `/remote-control`, `/rc` | [![Best Practice](!/tags/best-practice.svg)](https://x.com/noahzweben/status/2032533699116355819) 모든 기기에서 로컬 세션 계속하기 — 폰, 태블릿, 또는 브라우저 · [헤드리스 모드](https://code.claude.com/docs/en/headless) |
| [**Git Worktrees**](https://code.claude.com/docs/en/common-workflows#run-parallel-claude-code-sessions-with-git-worktrees) | 내장 | [![Best Practice](!/tags/best-practice.svg)](https://x.com/bcherny/status/2025007393290272904) 병렬 개발을 위한 격리된 git 브랜치 — 각 에이전트가 자체 작업 복사본을 가짐 |
| [**Ralph Wiggum Loop**](https://github.com/anthropics/claude-code/tree/main/plugins/ralph-wiggum) | plugin | [![Best Practice](!/tags/best-practice.svg)](https://github.com/ghuntley/how-to-ralph-wiggum) [![Implemented](!/tags/implemented.svg)](https://github.com/shanraisshan/novel-llm-26) 장시간 실행 태스크를 위한 자율 개발 루프 — 완료될 때까지 반복 |

<p align="center">
  <img src="!/claude-jumping.svg" alt="section divider" width="60" height="50">
</p>

<a id="orchestration-workflow"></a>

## <a href="orchestration-workflow/orchestration-workflow.md"><img src="!/tags/orchestration-workflow-hd.svg" alt="Orchestration Workflow"></a>

<img src="!/tags/c.svg" height="14"> **커맨드** → <img src="!/tags/a.svg" height="14"> **에이전트** → <img src="!/tags/s.svg" height="14"> **스킬** 패턴의 구현 세부사항은 [orchestration-workflow](orchestration-workflow/orchestration-workflow.md)를 참조하세요.


<p align="center">
  <img src="orchestration-workflow/orchestration-workflow.svg" alt="Command Skill Agent Architecture Flow" width="100%">
</p>

<p align="center">
  <img src="orchestration-workflow/orchestration-workflow.gif" alt="Orchestration Workflow Demo" width="600">
</p>

![How to Use](!/tags/how-to-use.svg)

```bash
claude
/weather-orchestrator
```

<p align="center">
  <img src="!/claude-jumping.svg" alt="section divider" width="60" height="50">
</p>

## ⚙️ 개발 워크플로우 (DEVELOPMENT WORKFLOWS)

모든 주요 워크플로우는 동일한 아키텍처 패턴으로 수렴합니다: **리서치 → 계획 → 실행 → 리뷰 → 배포**

| Name | ★ | Uniqueness | Plan | <img src="!/tags/a.svg" height="14"> | <img src="!/tags/c.svg" height="14"> | <img src="!/tags/s.svg" height="14"> |
|------|---|------------|------|---|---|---|
| [Everything Claude Code](https://github.com/affaan-m/everything-claude-code) | 133k | ![instinct scoring](https://img.shields.io/badge/instinct_scoring-ddf4ff) ![AgentShield](https://img.shields.io/badge/AgentShield-ddf4ff) ![multi-lang rules](https://img.shields.io/badge/multi--lang_rules-ddf4ff) | <img src="!/tags/a.svg" height="14"> [planner](https://github.com/affaan-m/everything-claude-code/blob/main/agents/planner.md) | 36 | 68 | 152 |
| [Superpowers](https://github.com/obra/superpowers) | 132k | ![TDD-first](https://img.shields.io/badge/TDD--first-ddf4ff) ![Iron Laws](https://img.shields.io/badge/Iron_Laws-ddf4ff) ![whole-plan review](https://img.shields.io/badge/whole--plan_review-ddf4ff) | <img src="!/tags/s.svg" height="14"> [writing-plans](https://github.com/obra/superpowers/tree/main/skills/writing-plans) | 5 | 3 | 14 |
| [Spec Kit](https://github.com/github/spec-kit) | 85k | ![spec-driven](https://img.shields.io/badge/spec--driven-ddf4ff) ![constitution](https://img.shields.io/badge/constitution-ddf4ff) ![22+ tools](https://img.shields.io/badge/22%2B_tools-ddf4ff) | <img src="!/tags/c.svg" height="14"> [speckit.plan](https://github.com/github/spec-kit/blob/main/templates/commands/plan.md) | 0 | 9+ | 0 |
| [gstack](https://github.com/garrytan/gstack) | 62k | ![role personas](https://img.shields.io/badge/role_personas-ddf4ff) ![/codex review](https://img.shields.io/badge/%2Fcodex_review-ddf4ff) ![parallel sprints](https://img.shields.io/badge/parallel_sprints-ddf4ff) | <img src="!/tags/s.svg" height="14"> [autoplan](https://github.com/garrytan/gstack/tree/main/autoplan) | 0 | 0 | 33 |
| [Get Shit Done](https://github.com/gsd-build/get-shit-done) | 47k | ![fresh 200K contexts](https://img.shields.io/badge/fresh_200K_contexts-ddf4ff) ![wave execution](https://img.shields.io/badge/wave_execution-ddf4ff) ![XML plans](https://img.shields.io/badge/XML_plans-ddf4ff) | <img src="!/tags/a.svg" height="14"> [gsd-planner](https://github.com/gsd-build/get-shit-done/blob/main/agents/gsd-planner.md) | 21 | 59 | 0 |
| [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) | 43k | ![full SDLC](https://img.shields.io/badge/full_SDLC-ddf4ff) ![agent personas](https://img.shields.io/badge/agent_personas-ddf4ff) ![22+ platforms](https://img.shields.io/badge/22%2B_platforms-ddf4ff) | <img src="!/tags/s.svg" height="14"> [bmad-create-prd](https://github.com/bmad-code-org/BMAD-METHOD/tree/main/src/bmm-skills/2-plan-workflows/bmad-create-prd) | 0 | 0 | 40 |
| [OpenSpec](https://github.com/Fission-AI/OpenSpec) | 37k | ![delta specs](https://img.shields.io/badge/delta_specs-ddf4ff) ![brownfield](https://img.shields.io/badge/brownfield-ddf4ff) ![artifact DAG](https://img.shields.io/badge/artifact_DAG-ddf4ff) | <img src="!/tags/c.svg" height="14"> [opsx:propose](https://github.com/Fission-AI/OpenSpec/blob/main/src/commands/workflow/new-change.ts) | 0 | 11 | 0 |
| [Compound Engineering](https://github.com/EveryInc/compound-engineering-plugin) | 13k | ![Compound Learning](https://img.shields.io/badge/Compound_Learning-ddf4ff) ![Multi-Platform CLI](https://img.shields.io/badge/Multi--Platform_CLI-ddf4ff) ![Plugin Marketplace](https://img.shields.io/badge/Plugin_Marketplace-ddf4ff) | <img src="!/tags/s.svg" height="14"> [ce-plan](https://github.com/EveryInc/compound-engineering-plugin/tree/main/plugins/compound-engineering/skills/ce-plan) | 49 | 4 | 42 |
| [HumanLayer](https://github.com/humanlayer/humanlayer) | 10k | ![RPI](https://img.shields.io/badge/RPI-ddf4ff) ![context engineering](https://img.shields.io/badge/context_engineering-ddf4ff) ![300k+ LOC](https://img.shields.io/badge/300k%2B_LOC-ddf4ff) | <img src="!/tags/c.svg" height="14"> [create_plan](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/create_plan.md) | 6 | 27 | 0 |

### 기타
- [크로스 모델 (Claude Code + Codex) 워크플로우](development-workflows/cross-model-workflow/cross-model-workflow.md) [![Implemented](!/tags/implemented.svg)](development-workflows/cross-model-workflow/cross-model-workflow.md)
- [RPI](development-workflows/rpi/rpi-workflow.md) [![Implemented](!/tags/implemented.svg)](development-workflows/rpi/rpi-workflow.md)
- [Ralph Wiggum Loop](https://www.youtube.com/watch?v=eAtvoGlpeRU) [![Implemented](!/tags/implemented.svg)](https://github.com/shanraisshan/novel-llm-26)
- [Andrej Karpathy (OpenAI 창립 멤버) 워크플로우](https://x.com/karpathy/status/2015883857489522876)
- [Peter Steinberger (OpenClaw 제작자) 워크플로우](https://youtu.be/8lF7HmQ_RgY?t=2582)
- Boris Cherny (Claude Code 제작자) 워크플로우 — [13 Tips](tips/claude-boris-13-tips-03-jan-26.md) · [10 Tips](tips/claude-boris-10-tips-01-feb-26.md) · [12 Tips](tips/claude-boris-12-tips-12-feb-26.md) · [2 Tips](tips/claude-boris-2-tips-25-mar-26.md) · [15 Tips](tips/claude-boris-15-tips-30-mar-26.md) [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny)

<p align="center">
  <img src="!/claude-jumping.svg" alt="section divider" width="60" height="50">
</p>

## 💡 팁 및 트릭 (87개)

🚫👶 = 과잉 개입하지 마세요

[프롬프팅](#tips-prompting) · [계획](#tips-planning) · [CLAUDE.md](#tips-claudemd) · [에이전트](#tips-agents) · [커맨드](#tips-commands) · [스킬](#tips-skills) · [훅](#tips-hooks) · [워크플로우](#tips-workflows) · [고급](#tips-workflows-advanced) · [Git / PR](#tips-git-pr) · [디버깅](#tips-debugging) · [유틸리티](#tips-utilities) · [일상](#tips-daily)

![Community](!/tags/community.svg)

<a id="tips-prompting"></a>■ **프롬프팅 (3)**

| 팁 | 출처 |
|-----|--------|
| Claude에게 도전하세요 — "이 변경사항들을 철저히 검토하고 내가 테스트를 통과할 때까지 PR을 만들지 마세요." 또는 "이것이 작동한다는 것을 증명하세요"라고 하고 Claude로 하여금 main과 브랜치의 diff를 비교하도록 하세요 🚫👶 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742752566632544) |
| 평범한 수정 후에는 — "지금 알고 있는 모든 것을 바탕으로, 이것을 버리고 우아한 해결책을 구현하세요" 🚫👶 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742752566632544) |
| Claude는 대부분의 버그를 스스로 고칩니다 — 버그를 붙여넣고 "수정하세요"라고 말하세요, 방법을 과도하게 지시하지 마세요 🚫👶 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742750473720121) |

<a id="tips-planning"></a>■ **계획/스펙 (6)**

| 팁 | 출처 |
|-----|--------|
| 항상 [플랜 모드](https://code.claude.com/docs/en/common-workflows)로 시작하세요 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179845336527000) |
| 최소한의 스펙이나 프롬프트로 시작하고 Claude에게 [AskUserQuestion](https://code.claude.com/docs/en/cli-reference) 도구를 사용하여 인터뷰하도록 요청한 후, 새 세션을 만들어 스펙을 실행하세요 | [![Thariq](!/tags/thariq.svg)](https://x.com/trq212/status/2005315275026260309) |
| 항상 단계별 게이트 계획을 만들고, 각 단계에 여러 테스트 (단위, 자동화, 통합)를 포함하세요 | |
| 스태프 엔지니어로서 계획을 검토하기 위해 두 번째 Claude를 실행하거나, 리뷰를 위해 [크로스 모델](development-workflows/cross-model-workflow/cross-model-workflow.md)을 사용하세요 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742745365057733) |
| 작업을 넘기기 전에 상세한 스펙을 작성하고 모호성을 줄이세요 — 구체적일수록 출력이 좋아집니다 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742752566632544) |
| 프로토타입 > PRD — 스펙 작성 대신 20-30개 버전을 만드세요, 구축 비용이 낮으므로 많은 시도를 하세요 | [![Boris](!/tags/boris-cherny.svg)](https://youtu.be/julbw1JuAz0?t=3630) [![Video](!/tags/video.svg)](https://youtu.be/julbw1JuAz0?t=3630) |

<a id="tips-claudemd"></a>■ **CLAUDE.md (7)**

| 팁 | 출처 |
|-----|--------|
| [CLAUDE.md](https://code.claude.com/docs/en/memory)는 파일당 [200줄](https://code.claude.com/docs/en/memory#write-effective-instructions) 이하를 목표로 하세요. [humanlayer에서는 60줄](https://www.humanlayer.dev/blog/writing-a-good-claude-md) ([여전히 100% 보장되지는 않음](https://www.reddit.com/r/ClaudeCode/comments/1qn9pb9/claudemd_says_must_use_agent_claude_ignores_it_80/)) | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179840848597422) [![Dex](!/tags/dex.svg)](https://www.humanlayer.dev/blog/writing-a-good-claude-md) |
| 파일이 길어질수록 Claude가 무시하지 않도록 도메인별 CLAUDE.md 규칙을 [\<important if="..."\> 태그](https://www.hlyr.dev/blog/stop-claude-from-ignoring-your-claude-md)로 감싸세요 | [![Dex](!/tags/dex.svg)](https://www.hlyr.dev/blog/stop-claude-from-ignoring-your-claude-md) |
| 모노레포에는 [여러 CLAUDE.md](best-practice/claude-memory.md)를 사용하세요 — 조상 + 하위 파일 로딩 | |
| 큰 지시사항을 분할하려면 [.claude/rules/](https://code.claude.com/docs/en/memory#organize-rules-with-clauderules)를 사용하세요 | |
| [memory.md](https://code.claude.com/docs/en/memory), constitution.md는 아무것도 보장하지 않습니다 | |
| 어떤 개발자라도 Claude를 실행하고 "테스트를 실행하세요"라고 말하면 첫 번째 시도에 작동해야 합니다 — 작동하지 않으면 CLAUDE.md에 필수 설정/빌드/테스트 커맨드가 누락된 것입니다 | [![Dex](!/tags/dex.svg)](https://x.com/dexhorthy/status/2034713765401551053) |
| 코드베이스를 깔끔하게 유지하고 마이그레이션을 완료하세요 — 부분적으로 마이그레이션된 프레임워크는 모델이 잘못된 패턴을 선택할 수 있어 혼란을 줍니다 | [![Boris](!/tags/boris-cherny.svg)](https://youtu.be/julbw1JuAz0?t=1112) [![Video](!/tags/video.svg)](https://youtu.be/julbw1JuAz0?t=1112) |
| 하네스 강제 동작 (어트리뷰션, 권한, 모델)에는 [settings.json](best-practice/claude-settings.md)을 사용하세요 — CLAUDE.md에 "절대 Co-Authored-By를 추가하지 마세요"를 넣는 것보다 `attribution.commit: ""`이 결정론적입니다 | [![davila7](!/tags/davila7.svg)](https://x.com/dani_avila7/status/2036182734310195550) |

<a id="tips-agents"></a><img src="!/tags/a.svg" height="14"> **에이전트 (4)**

| 팁 | 출처 |
|-----|--------|
| 범용 qa, 백엔드 엔지니어 대신 [스킬](https://code.claude.com/docs/en/skills) (점진적 공개)을 가진 기능별 [서브에이전트](https://code.claude.com/docs/en/sub-agents) (추가 컨텍스트)를 사용하세요 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179850139000872) |
| 문제에 더 많은 컴퓨팅을 투입하려면 "서브에이전트를 사용하세요"라고 말하세요 — 태스크를 오프로드하여 메인 컨텍스트를 깨끗하고 집중된 상태로 유지하세요 🚫👶 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742755737555434) |
| 병렬 개발을 위해 [tmux를 사용한 에이전트 팀](https://code.claude.com/docs/en/agent-teams)과 [git worktrees](https://x.com/bcherny/status/2025007393290272904)를 사용하세요 | |
| [테스트 시간 컴퓨팅](https://code.claude.com/docs/en/sub-agents)을 사용하세요 — 별도의 컨텍스트 창이 결과를 향상시킵니다; 한 에이전트가 버그를 만들고 다른 에이전트 (같은 모델)가 찾을 수 있습니다 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2031151689219321886) |

<a id="tips-commands"></a><img src="!/tags/c.svg" height="14"> **커맨드 (3)**

| 팁 | 출처 |
|-----|--------|
| [서브에이전트](https://code.claude.com/docs/en/sub-agents) 대신 워크플로우에는 [커맨드](https://code.claude.com/docs/en/slash-commands)를 사용하세요 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179847949500714) |
| 하루에 여러 번 수행하는 모든 "내부 루프" 워크플로우에는 [슬래시 커맨드](https://code.claude.com/docs/en/slash-commands)를 사용하세요 — 반복적인 프롬프팅을 절약하고, 커맨드는 `.claude/commands/`에 있으며 git에 체크인됩니다 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179847949500714) |
| 하루에 두 번 이상 하는 일이 있으면 [스킬](https://code.claude.com/docs/en/skills)이나 [커맨드](https://code.claude.com/docs/en/slash-commands)로 만드세요 — `/techdebt`, 컨텍스트 덤프, 또는 분석 커맨드를 구축하세요 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742748984742078) |

<a id="tips-skills"></a><img src="!/tags/s.svg" height="14"> **스킬 (9)**

| 팁 | 출처 |
|-----|--------|
| [context: fork](https://code.claude.com/docs/en/skills)를 사용하여 격리된 서브에이전트에서 스킬을 실행하세요 — 메인 컨텍스트는 중간 도구 호출이 아닌 최종 결과만 봅니다. agent 필드로 서브에이전트 유형을 설정할 수 있습니다 | [![Lydia](!/tags/lydia.svg)](https://x.com/lydiahallie/status/2033603164398883042) |
| 모노레포를 위해 [서브폴더의 스킬](reports/claude-skills-for-larger-mono-repos.md)을 사용하세요 | |
| 스킬은 파일이 아닌 폴더입니다 — [점진적 공개](https://code.claude.com/docs/en/skills)를 위해 references/, scripts/, examples/ 서브디렉토리를 사용하세요 | [![Thariq](!/tags/thariq.svg)](https://x.com/trq212/status/2033949937936085378) |
| 모든 스킬에 Gotchas 섹션을 만드세요 — 가장 신호가 높은 콘텐츠, 시간이 지남에 따라 Claude의 실패 지점을 추가하세요 | [![Thariq](!/tags/thariq.svg)](https://x.com/trq212/status/2033949937936085378) |
| 스킬 description 필드는 트리거이지 요약이 아닙니다 — 모델을 위해 작성하세요 ("언제 실행해야 하는가?") | [![Thariq](!/tags/thariq.svg)](https://x.com/trq212/status/2033949937936085378) |
| 스킬에서 자명한 것을 명시하지 마세요 — Claude의 기본 동작에서 벗어나게 하는 것에 집중하세요 🚫👶 | [![Thariq](!/tags/thariq.svg)](https://x.com/trq212/status/2033949937936085378) |
| 스킬에서 Claude를 과도하게 제한하지 마세요 — 목표와 제약 조건을 제공하고, 규범적인 단계별 지시사항은 피하세요 🚫👶 | [![Thariq](!/tags/thariq.svg)](https://x.com/trq212/status/2033949937936085378) |
| Claude가 보일러플레이트를 재구성하지 않고 구성할 수 있도록 스킬에 스크립트와 라이브러리를 포함하세요 | [![Thariq](!/tags/thariq.svg)](https://x.com/trq212/status/2033949937936085378) |
| 동적 셸 출력을 프롬프트에 주입하려면 SKILL.md에 `` !`command` ``를 삽입하세요 — Claude가 호출 시 실행하고 모델은 결과만 봅니다 | [![Lydia](!/tags/lydia.svg)](https://x.com/lydiahallie/status/2034337963820327017) |

<a id="tips-hooks"></a>■ **훅 (5)**

| 팁 | 출처 |
|-----|--------|
| 스킬에서 [온디맨드 훅](https://code.claude.com/docs/en/skills)을 사용하세요 — /careful은 파괴적인 커맨드를 차단하고, /freeze는 디렉토리 외부의 편집을 차단합니다 | [![Thariq](!/tags/thariq.svg)](https://x.com/trq212/status/2033949937936085378) |
| 인기 있거나 트리거가 부족한 스킬을 찾기 위해 PreToolUse 훅으로 [스킬 사용량을 측정](https://code.claude.com/docs/en/skills)하세요 | [![Thariq](!/tags/thariq.svg)](https://x.com/trq212/status/2033949937936085378) |
| 코드를 자동 포맷하기 위해 [PostToolUse 훅](https://code.claude.com/docs/en/hooks)을 사용하세요 — Claude는 잘 포맷된 코드를 생성하고, 훅이 CI 실패를 방지하기 위해 마지막 10%를 처리합니다 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179852047335529) |
| 훅을 통해 [권한 요청](https://code.claude.com/docs/en/hooks)을 Opus로 라우팅하세요 — 공격을 스캔하고 안전한 것을 자동 승인하도록 하세요 🚫👶 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742755737555434) |
| 턴이 끝날 때 Claude가 계속 진행하거나 작업을 확인하도록 하기 위해 [Stop 훅](https://code.claude.com/docs/en/hooks)을 사용하세요 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2021701059253874861) |

<a id="tips-workflows"></a>■ **워크플로우 (7)**

| 팁 | 출처 |
|-----|--------|
| 에이전트 둔화 구간을 피하고, 최대 50%에서 수동 [/compact](https://code.claude.com/docs/en/interactive-mode)를 수행하세요. 새 태스크로 전환할 때 컨텍스트를 재설정하려면 [/clear](https://code.claude.com/docs/en/cli-reference)를 사용하세요 | |
| 작은 태스크에는 바닐라 cc가 어떤 워크플로우보다 낫습니다 | |
| 모델과 추론을 선택하려면 [/model](https://code.claude.com/docs/en/model-config), 컨텍스트 사용량을 보려면 [/context](https://code.claude.com/docs/en/interactive-mode), 플랜 한도를 확인하려면 [/usage](https://code.claude.com/docs/en/costs), 오버플로우 청구를 구성하려면 [/extra-usage](https://code.claude.com/docs/en/interactive-mode), 설정을 구성하려면 [/config](https://code.claude.com/docs/en/settings)를 사용하세요 — 최고의 결과를 얻으려면 계획 모드에는 Opus를, 코드에는 Sonnet을 사용하세요 | [![Cat](!/tags/cat-wu.svg)](https://x.com/_catwu/status/1955694117264261609) |
| Claude의 결정을 더 잘 이해하기 위해 [/config](https://code.claude.com/docs/en/settings)에서 [thinking mode](https://code.claude.com/docs/en/model-config)를 true (추론 보기)로 설정하고 [Output Style](https://code.claude.com/docs/en/output-styles)을 Explanatory (★ Insight 박스가 있는 상세 출력 보기)로 설정하세요 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179838864666847) |
| [높은 노력 추론](https://docs.anthropic.com/en/docs/build-with-claude/extended-thinking#tips-and-best-practices)을 위해 프롬프트에 ultrathink 키워드를 사용하세요 | |
| 중요한 세션에 [/rename](https://code.claude.com/docs/en/cli-reference)을 사용하고 (예: [TODO - 리팩토링 태스크]) 나중에 [/resume](https://code.claude.com/docs/en/cli-reference)하세요 — 여러 Claude를 동시에 실행할 때 각 인스턴스에 레이블을 붙이세요 | [![Cat](!/tags/cat-wu.svg)](https://every.to/podcast/how-to-use-claude-code-like-the-people-who-built-it) |
| Claude가 잘못된 방향으로 갈 때 같은 컨텍스트에서 고치려 하지 말고 [Esc Esc 또는 /rewind](https://code.claude.com/docs/en/checkpointing)를 사용하여 되돌리세요 | |

<a id="tips-workflows-advanced"></a>■ **워크플로우 고급 (6)**

| 팁 | 출처 |
|-----|--------|
| 아키텍처를 이해하기 위해 ASCII 다이어그램을 많이 사용하세요 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742759218794768) |
| 로컬 반복 모니터링 (최대 3일)에는 [/loop](https://code.claude.com/docs/en/scheduled-tasks)를 사용하고, 머신이 꺼져 있어도 실행되는 클라우드 기반 반복 태스크에는 [/schedule](https://code.claude.com/docs/en/web-scheduled-tasks)을 사용하세요 | |
| 장시간 실행되는 자율 태스크에는 [Ralph Wiggum 플러그인](https://github.com/shanraisshan/novel-llm-26)을 사용하세요 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179858435281082) |
| dangerously-skip-permissions 대신 와일드카드 구문 (Bash(npm run *), Edit(/docs/**))을 사용하여 [/permissions](https://code.claude.com/docs/en/permissions)를 설정하세요 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179854077407667) |
| 파일 및 네트워크 격리로 권한 프롬프트를 줄이기 위해 [/sandbox](https://code.claude.com/docs/en/sandboxing)를 사용하세요 — 내부적으로 84% 감소 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2021700506465579443) [![Cat](!/tags/cat-wu.svg)](https://creatoreconomy.so/p/inside-claude-code-how-an-ai-native-actually-works-cat-wu) |
| [제품 검증](https://code.claude.com/docs/en/skills) 스킬 (signup-flow-driver, checkout-verifier)에 투자하세요 — 완벽하게 만드는 데 일주일을 쓸 가치가 있습니다 | [![Thariq](!/tags/thariq.svg)](https://x.com/trq212/status/2033949937936085378) |

<a id="tips-git-pr"></a>■ **Git / PR (5)**

| 팁 | 출처 |
|-----|--------|
| PR을 작고 집중적으로 유지하세요 — [p50은 118줄](tips/claude-boris-2-tips-25-mar-26.md) (141개 PR, 하루에 45K 줄 변경), 기능당 하나의 PR, 리뷰와 되돌리기가 더 쉽습니다 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2038552880018538749) |
| 항상 PR을 [squash merge](tips/claude-boris-2-tips-25-mar-26.md)하세요 — 깔끔한 선형 히스토리, 기능당 하나의 커밋, 쉬운 git revert와 git bisect | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2038552880018538749) |
| 자주 커밋하세요 — 최소 한 시간에 한 번, 태스크가 완료되는 즉시 커밋하세요 | |
| 반복되는 리뷰 피드백에 대한 린트 규칙을 자동 생성하기 위해 동료의 PR에 [@claude](https://github.com/apps/claude)를 태그하세요 — 코드 리뷰에서 자신을 자동화하세요 🚫👶 | [![Boris](!/tags/boris-cherny.svg)](https://youtu.be/julbw1JuAz0?t=2715) [![Video](!/tags/video.svg)](https://youtu.be/julbw1JuAz0?t=2715) |
| 멀티 에이전트 PR 분석을 위해 [/code-review](https://code.claude.com/docs/en/code-review)를 사용하세요 — 병합 전에 버그, 보안 취약점, 회귀를 잡습니다 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2031089411820228645) |

<a id="tips-debugging"></a>■ **디버깅 (7)**

| 팁 | 출처 |
|-----|--------|
| 어떤 문제에 막혔을 때마다 스크린샷을 찍고 Claude와 공유하는 습관을 만드세요 | |
| mcp ([Claude in Chrome](https://code.claude.com/docs/en/chrome), [Playwright](https://github.com/microsoft/playwright-mcp), [Chrome DevTools](https://developer.chrome.com/blog/chrome-devtools-mcp))를 사용하여 Claude가 스스로 Chrome 콘솔 로그를 볼 수 있도록 하세요 | |
| 더 나은 디버깅을 위해 항상 Claude에게 터미널 (로그를 보고 싶은)을 백그라운드 태스크로 실행하도록 요청하세요 | |
| 설치, 인증, 구성 문제를 진단하려면 [/doctor](https://code.claude.com/docs/en/cli-reference)를 사용하세요 | |
| 압축 중 오류는 [/model](https://code.claude.com/docs/en/model-config)을 사용하여 1M 토큰 모델을 선택한 후 [/compact](https://code.claude.com/docs/en/interactive-mode)를 실행하면 해결할 수 있습니다 | |
| QA를 위해 [크로스 모델](development-workflows/cross-model-workflow/cross-model-workflow.md)을 사용하세요 — 예: 계획 및 구현 리뷰를 위해 [Codex](https://github.com/shanraisshan/codex-cli-best-practice) 사용 | |
| 에이전틱 검색 (glob + grep)이 RAG를 능가합니다 — Claude Code는 코드가 동기화에서 벗어나고 권한이 복잡하기 때문에 벡터 데이터베이스를 시도하고 폐기했습니다 | [![Boris](!/tags/boris-cherny.svg)](https://youtu.be/julbw1JuAz0?t=3095) [![Video](!/tags/video.svg)](https://youtu.be/julbw1JuAz0?t=3095) |

<a id="tips-utilities"></a>■ **유틸리티 (5)**

| 팁 | 출처 |
|-----|--------|
| IDE ([VS Code](https://code.visualstudio.com/)/[Cursor](https://www.cursor.com/)) 대신 [iTerm](https://iterm2.com/)/[Ghostty](https://ghostty.org/)/[tmux](https://github.com/tmux/tmux) 터미널을 사용하세요 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742753971769626) |
| 음성 프롬프팅 (10배 생산성)을 위해 [Wispr Flow](https://wisprflow.ai)를 사용하세요 | |
| Claude 피드백을 위해 [claude-code-hooks](https://github.com/shanraisshan/claude-code-hooks)를 사용하세요 | |
| 컨텍스트 인식과 빠른 압축을 위해 [status line](https://github.com/shanraisshan/claude-code-status-line)을 사용하세요 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2021700784019452195) |
| 개인화된 경험을 위해 [Plans Directory](best-practice/claude-settings.md#plans-directory), [Spinner Verbs](best-practice/claude-settings.md#display--ux)와 같은 [settings.json](best-practice/claude-settings.md) 기능을 탐색하세요 | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2021701145023197516) |

<a id="tips-daily"></a>■ **일상 (4)**

| 팁 | 출처 |
|-----|--------|
| Claude Code를 매일 [업데이트](https://code.claude.com/docs/en/setup)하고 [체인지로그](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)를 읽으며 하루를 시작하세요 | |
| [r/ClaudeAI](https://www.reddit.com/r/ClaudeAI/), [r/ClaudeCode](https://www.reddit.com/r/ClaudeCode/), [r/Anthropic](https://www.reddit.com/r/Anthropic/)을 팔로우하세요 | ![Reddit](https://img.shields.io/badge/-FF4500?style=flat&logo=reddit&logoColor=white) |
| [Claude](https://x.com/claudeai), [Anthropic](https://x.com/AnthropicAI), [Boris](https://x.com/bcherny), [Thariq](https://x.com/trq212), [Cat](https://x.com/_catwu), [Lydia](https://x.com/lydiahallie), [Noah](https://x.com/noahzweben), [Anthony](https://x.com/amorriscode), [Alex](https://x.com/alexalbert__), [Kenneth](https://x.com/neilhtennek)을 팔로우하세요 | ![X](https://img.shields.io/badge/-000?style=flat&logo=x&logoColor=white) [![Boris + Team](!/tags/boris-team.svg)](https://x.com/bcherny) |
| [Jesse Kriss](https://x.com/obra) ([Superpowers](https://github.com/obra/superpowers)), [Affaan Mustafa](https://x.com/affaanmustafa) ([ECC](https://github.com/affaan-m/everything-claude-code)), [Garry Tan](https://x.com/garrytan) ([gstack](https://github.com/garrytan/gstack)), [Dex Horthy](https://x.com/dexhorthy) ([HumanLayer](https://github.com/humanlayer/humanlayer)), [Kieran Klaassen](https://x.com/kieranklaassen) ([Compound Eng](https://github.com/EveryInc/compound-engineering-plugin)), [Tabish Gilani](https://x.com/0xTab) ([OpenSpec](https://github.com/Fission-AI/OpenSpec)), [Brian McAdams](https://x.com/BMadCode) ([BMAD](https://github.com/bmad-code-org/BMAD-METHOD)), [Lex Christopherson](https://x.com/official_taches) ([GSD](https://github.com/gsd-build/get-shit-done)), [Dani Avila](https://x.com/dani_avila7) ([CC Templates](https://github.com/davila7/claude-code-templates)), [Dan Shipper](https://x.com/danshipper) ([Every](https://every.to/)), [Andrej Karpathy](https://x.com/karpathy) ([AutoResearch](https://x.com/karpathy/status/2015883857489522876)), [Peter Steinberger](https://x.com/steipete) ([OpenClaw](https://x.com/openclaw)), [Sigrid Jin](https://x.com/realsigridjin) ([claw-code](https://github.com/ultraworkers/claw-code))를 팔로우하세요 | ![X](https://img.shields.io/badge/-000?style=flat&logo=x&logoColor=white) ![Community](!/tags/community.svg) |

![Boris Cherny + Team](!/tags/boris-team.svg)

| 아티클 / 트윗 | 출처 |
|-----------------|--------|
| [15 Hidden & Under-Utilized Features in Claude Code (Boris) \| 30/Mar/26](tips/claude-boris-15-tips-30-mar-26.md) | [Tweet](https://x.com/bcherny/status/2038454336355999749) |
| [Squash Merging & PR Size Distribution (Boris) \| 25/Mar/26](tips/claude-boris-2-tips-25-mar-26.md) | [Tweet](https://x.com/bcherny/status/2038552880018538749) |
| [Lessons from Building Claude Code: How We Use Skills (Thariq) \| 17/Mar/26](tips/claude-thariq-tips-17-mar-26.md) | [Article](https://x.com/trq212/status/2033949937936085378) |
| [Code Review & Test Time Compute (Boris) \| 10/Mar/26](tips/claude-boris-2-tips-10-mar-26.md) | [Tweet](https://x.com/bcherny/status/2031089411820228645) |
| /loop — 최대 3일 동안 반복 태스크 스케줄링 (Boris) \| 07 Mar 2026 | [Tweet](https://x.com/bcherny/status/2030193932404150413) |
| AskUserQuestion + ASCII Markdowns (Thariq) \| 28 Feb 2026 | [Tweet](https://x.com/trq212/status/2027543858289250472) |
| Seeing like an Agent - lessons from building Claude Code (Thariq) \| 28 Feb 2026 | [Article](https://x.com/trq212/status/2027463795355095314) |
| Git Worktrees - Boris가 사용하는 5가지 방법 \| 21 Feb 2026 | [Tweet](https://x.com/bcherny/status/2025007393290272904) |
| Lessons from Building Claude Code: Prompt Caching Is Everything (Thariq) \| 20 Feb 2026 | [Article](https://x.com/trq212/status/2024574133011673516) |
| [12 ways how people are customizing their claudes (Boris) \| 12/Feb/26](tips/claude-boris-12-tips-12-feb-26.md) | [Tweet](https://x.com/bcherny/status/2021699851499798911) |
| [10 tips for using Claude Code from the team (Boris) \| 01/Feb/26](tips/claude-boris-10-tips-01-feb-26.md) | [Tweet](https://x.com/bcherny/status/2017742741636321619) |
| [How I use Claude Code — 13 tips from my surprisingly vanilla setup (Boris) \| 03/Jan/26](tips/claude-boris-13-tips-03-jan-26.md) | [Tweet](https://x.com/bcherny/status/2007179832300581177) |
| AskUserQuestion 도구를 사용하여 Claude로 하여금 인터뷰하도록 요청 (Thariq) \| 28/Dec/25 | [Tweet](https://x.com/trq212/status/2005315275026260309) |
| 항상 플랜 모드를 사용하고, Claude에게 검증 방법을 제공하고, /code-review 사용 (Boris) \| 27/Dec/25 | [Tweet](https://x.com/bcherny/status/2004711722926616680) |

![Videos / Podcasts](!/tags/videos-podcasts.svg)

| 영상 / 팟캐스트 | 출처 | YouTube |
|-----------------|--------|---------|
| Everything We Got Wrong About Research-Plan-Implement (Dex) \| 24 Mar 2026 \| MLOps Community | [![Dex](!/tags/dex.svg)](https://x.com/daborhyde) | [YouTube](https://youtu.be/YwZR6tc7qYg) |
| Building Claude Code with Boris Cherny (Boris) \| 04 Mar 2026 \| The Pragmatic Engineer | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny) | [YouTube](https://youtu.be/julbw1JuAz0) |
| Head of Claude Code: What happens after coding is solved (Boris) \| 19 Feb 2026 \| Lenny's Podcast | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny) | [YouTube](https://youtu.be/We7BZVKbCVw) |
| Inside Claude Code With Its Creator Boris Cherny (Boris) \| 17 Feb 2026 \| Y Combinator | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny) | [YouTube](https://youtu.be/PQU9o_5rHC4) |
| Boris Cherny (Creator of Claude Code) On What Grew His Career (Boris) \| 15 Dec 2025 \| Ryan Peterman | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny) | [YouTube](https://youtu.be/AmdLVWMdjOk) |
| The Secrets of Claude Code From the Engineers Who Built It (Cat) \| 29 Oct 2025 \| Every | [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny) | [YouTube](https://youtu.be/IDSAMqip6ms) |

<p align="center">
  <img src="!/claude-jumping.svg" alt="section divider" width="60" height="50">
</p>

## ☠️ 스타트업 / 비즈니스

| Claude | 대체된 것 |
|-|-|
|[**코드 리뷰**](https://code.claude.com/docs/en/code-review)|[Greptile](https://greptile.com), [CodeRabbit](https://coderabbit.ai), [Devin Review](https://devin.ai), [OpenDiff](https://opendiff.com), [Cursor BugBot](https://bugbot.dev)|
|[**음성 받아쓰기**](https://code.claude.com/docs/en/voice-dictation)|[Wispr Flow](https://wisprflow.ai), [SuperWhisper](https://superwhisper.com/)|
|[**원격 제어**](https://code.claude.com/docs/en/remote-control)|[OpenClaw](https://openclaw.ai/)
|[**Cowork**](https://claude.com/blog/cowork-research-preview)|[OpenAI Operator](https://openai.com/operator), [AgentShadow](https://agentshadow.ai)
|[**Tasks**](https://x.com/trq212/status/2014480496013803643)|[Beads](https://github.com/steveyegge/beads)
|[**플랜 모드**](https://code.claude.com/docs/en/common-workflows)|[Agent OS](https://github.com/buildermethods/agent-os)|
|[**Skills / Plugins**](https://code.claude.com/docs/en/plugins)|YC AI 래퍼 스타트업 ([reddit](https://reddit.com/r/ClaudeAI/comments/1r6bh4d/claude_code_skills_are_basically_yc_ai_startup/))|

<p align="center">
  <img src="!/claude-jumping.svg" alt="section divider" width="60" height="50">
</p>

<a id="billion-dollar-questions"></a>
![Billion-Dollar Questions](!/tags/billion-dollar-questions.svg)

*답이 있다면 shanraisshan@gmail.com으로 알려주세요*

**메모리 & 지시사항 (4)**

1. CLAUDE.md에 정확히 무엇을 넣어야 하고 무엇을 빼야 하는가?
2. 이미 CLAUDE.md가 있다면, 별도의 constitution.md나 rules.md가 실제로 필요한가?
3. CLAUDE.md를 얼마나 자주 업데이트해야 하고, 언제 오래된 것이 되었는지 어떻게 알 수 있는가?
4. CLAUDE.md 지시사항에 모두 대문자로 MUST라고 쓰여 있어도 Claude가 여전히 무시하는 이유는 무엇인가? ([reddit](https://reddit.com/r/ClaudeCode/comments/1qn9pb9/claudemd_says_must_use_agent_claude_ignores_it_80/))

**에이전트, 스킬 & 워크플로우 (6)**

1. 커맨드 vs 에이전트 vs 스킬을 언제 사용해야 하고 — 언제 바닐라 Claude Code가 더 나은가?
2. 모델이 향상됨에 따라 에이전트, 커맨드, 워크플로우를 얼마나 자주 업데이트해야 하는가?
3. 서브에이전트에게 상세한 페르소나를 부여하면 품질이 향상되는가? 리서치/QA 서브에이전트의 "완벽한 페르소나/프롬프트"는 어떻게 생겼는가?
4. Claude Code의 내장 플랜 모드에 의존해야 하는가 — 아니면 팀의 워크플로우를 강제하는 자체 계획 커맨드/에이전트를 만들어야 하는가?
5. 개인 스킬 (예: 코딩 스타일을 가진 /implement)이 있을 때 충돌 없이 커뮤니티 스킬 (예: /simplify)을 통합하려면 어떻게 해야 하는가 — 그리고 의견이 다를 때 누가 이기는가?
6. 아직인가? 기존 코드베이스를 스펙으로 변환하고, 코드를 삭제하고, AI가 그 스펙만으로 정확히 같은 코드를 재생성할 수 있는가?

**스펙 & 문서화 (3)**

1. 리포지토리의 모든 기능에 마크다운 파일로 스펙이 있어야 하는가?
2. 새 기능이 구현될 때 스펙이 구식이 되지 않도록 얼마나 자주 업데이트해야 하는가?
3. 새 기능을 구현할 때 다른 기능의 스펙에 대한 연쇄 효과를 어떻게 처리하는가?

<p align="center">
  <img src="!/claude-jumping.svg" alt="section divider" width="60" height="50">
</p>

## 리포트 (REPORTS)

<p align="center">
  <a href="reports/claude-agent-sdk-vs-cli-system-prompts.md"><img src="https://img.shields.io/badge/Agent_SDK_vs_CLI-555?style=for-the-badge" alt="Agent SDK vs CLI"></a>
  <a href="reports/claude-in-chrome-v-chrome-devtools-mcp.md"><img src="https://img.shields.io/badge/Browser_Automation_MCP-555?style=for-the-badge" alt="Browser Automation MCP"></a>
  <a href="reports/claude-global-vs-project-settings.md"><img src="https://img.shields.io/badge/Global_vs_Project_Settings-555?style=for-the-badge" alt="Global vs Project Settings"></a>
  <a href="reports/claude-skills-for-larger-mono-repos.md"><img src="https://img.shields.io/badge/Skills_in_Monorepos-555?style=for-the-badge" alt="Skills in Monorepos"></a>
  <br>
  <a href="reports/claude-agent-memory.md"><img src="https://img.shields.io/badge/Agent_Memory-555?style=for-the-badge" alt="Agent Memory"></a>
  <a href="reports/claude-advanced-tool-use.md"><img src="https://img.shields.io/badge/Advanced_Tool_Use-555?style=for-the-badge" alt="Advanced Tool Use"></a>
  <a href="reports/claude-usage-and-rate-limits.md"><img src="https://img.shields.io/badge/Usage_&_Rate_Limits-555?style=for-the-badge" alt="Usage & Rate Limits"></a>
  <a href="reports/claude-agent-command-skill.md"><img src="https://img.shields.io/badge/Agents_vs_Commands_vs_Skills-555?style=for-the-badge" alt="Agents vs Commands vs Skills"></a>
  <br>
  <a href="reports/llm-day-to-day-degradation.md"><img src="https://img.shields.io/badge/LLM_Degradation-555?style=for-the-badge" alt="LLM Degradation"></a>
</p>

<p align="center">
  <img src="!/claude-jumping.svg" alt="section divider" width="60" height="50">
</p>

![How to Use](!/tags/how-to-use.svg)

```
1. 리포지토리를 코스처럼 읽고, 커맨드, 에이전트, 스킬, 훅이 무엇인지 사용하기 전에 배우세요.
2. 이 리포지토리를 클론하고 예제를 가지고 놀아보세요, /weather-orchestrator를 시도하고, 훅 사운드를 들어보고, 에이전트 팀을 실행해서 실제로 어떻게 작동하는지 볼 수 있습니다.
3. 자신의 프로젝트로 가서 Claude에게 이 리포지토리에서 어떤 모범 사례를 추가해야 할지 제안해 달라고 요청하고, 가능한 것이 무엇인지 알 수 있도록 이 리포지토리를 참조로 제공하세요.
```

<p align="center">
  <img src="!/claude-jumping.svg" alt="section divider" width="60" height="50">
</p>

<p align="center">
  <a href="https://github.com/trending?since=monthly"><img src="!/root/github-trending.png" alt="GitHub Trending" width="1200"></a><br>
  ✨2026년 3월 Github 트렌딩✨
</p>

## 기타 리포지토리

<a href="https://github.com/shanraisshan/claude-code-hooks"><img src="!/claude-speaking.svg" alt="Claude Code Hooks" width="40" height="40" align="center"></a> <a href="https://github.com/shanraisshan/claude-code-hooks"><strong>claude-code-hooks</strong></a> · <a href="https://github.com/shanraisshan/codex-cli-best-practice"><img src="!/codex-jumping.svg" alt="Codex CLI" width="40" height="40" align="center"></a> <a href="https://github.com/shanraisshan/codex-cli-best-practice"><strong>codex-cli-best-practice</strong></a> · <a href="https://github.com/shanraisshan/codex-cli-hooks"><img src="!/codex-speaking.svg" alt="Codex CLI Hooks" width="40" height="40" align="center"></a> <a href="https://github.com/shanraisshan/codex-cli-hooks"><strong>codex-cli-hooks</strong></a>

## 개발자

![Developed by](!/tags/developed-by.svg)

> | 워크플로우 | 설명 |
> |----------|-------------|
> | /workflows:development-workflows | DEVELOPMENT WORKFLOWS 테이블과 크로스 워크플로우 분석 리포트를 8개의 워크플로우 리포지토리를 병렬로 조사하여 업데이트 |
> | /workflows:best-practice:workflow-concepts | 최신 Claude Code 기능과 개념으로 README CONCEPTS 섹션을 업데이트 |
> | /workflows:best-practice:workflow-claude-settings | Claude Code 설정 리포트 변경사항을 추적하고 업데이트가 필요한 항목을 찾음 |
> | /workflows:best-practice:workflow-claude-subagents | Claude Code 서브에이전트 리포트 변경사항을 추적하고 업데이트가 필요한 항목을 찾음 |
> | /workflows:best-practice:workflow-claude-commands | Claude Code 커맨드 리포트 변경사항을 추적하고 업데이트가 필요한 항목을 찾음 |
> | /workflows:best-practice:workflow-claude-skills | Claude Code 스킬 리포트 변경사항을 추적하고 업데이트가 필요한 항목을 찾음 |

[![Claude for OSS](!/tags/claude-for-oss.svg)](https://claude.com/contact-sales/claude-for-oss)
[![Claude Community Ambassador](!/tags/claude-community-ambassador.svg)](https://claude.com/community/ambassadors)
[![Claude Certified Architect](!/tags/claude-certified-architect.svg)](https://anthropic.skilljar.com/claude-certified-architect-foundations-access-request)
[![Anthropic Academy](!/tags/anthropic-academy.svg)](https://anthropic.skilljar.com/)

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=shanraisshan/claude-code-best-practice&type=Date)](https://star-history.com/#shanraisshan/claude-code-best-practice&Date)
