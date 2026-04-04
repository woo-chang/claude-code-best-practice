현재 두바이 시간을 시각적 SVG 카드로 표시하는 시간 오케스트레이션 워크플로우를 구축하기 위해 에이전트 팀을 구성하세요. 워크플로우는 Command → Agent → Skill 아키텍처 패턴을 따릅니다:

- 커맨드가 흐름을 오케스트레이션하고 사용자 상호작용을 처리
- 에이전트가 사전 로드된 스킬을 사용하여 두바이의 현재 시간을 실시간으로 수집
- 스킬이 수집된 데이터로부터 시각적 SVG 시간 카드를 생성

**중요**: 모든 파일은 `agent-teams/.claude/` 내부에 생성되어야 합니다 —
리포지토리 루트의 `.claude/` 디렉토리가 아닙니다. 이렇게 하면 에이전트 팀의
출력이 독립적으로 유지되고 `cd agent-teams && claude`를 통해 실행 가능합니다.
기존 날씨 워크플로우를 참조하거나 복사하지 마세요 — 처음부터 모든 것을 구축하세요.

다음 팀원을 배정하세요:

1. **커맨드 아키텍트** — `agent-teams/.claude/commands/time-orchestrator.md`에
   `/time-orchestrator` 커맨드를 설계하고 구현하세요. 커맨드는:
   - Agent 도구(bash 아님)를 통해 time-agent를 호출하여
     두바이, UAE의 현재 시간(Asia/Dubai 타임존, UTC+4) 수집
   - Skill 도구를 통해 time-svg-creator 스킬을 호출하여
     수집된 시간 데이터로 SVG 카드 렌더링
   - 프론트매터에 model: haiku 사용
   - 핵심 요구사항 포함: 순차적 흐름, 올바른 도구 사용
     (에이전트에는 Agent 도구, 스킬에는 Skill 도구), 출력 요약
   공유 태스크 목록을 통해 다른 팀원과 조율하여 컴포넌트 간
   전달되는 데이터 계약({time, timezone, formatted})에 동의하세요.

2. **에이전트 엔지니어** — `agent-teams/.claude/agents/time-agent.md`에
   `time-agent`를 설계하고 구현하세요. 그리고 사전 로드되는 `time-fetcher`
   스킬을 `agent-teams/.claude/skills/time-fetcher/SKILL.md`에 구현하세요. 에이전트는:
   - Bash로 `TZ='Asia/Dubai' date '+%Y-%m-%d %H:%M:%S %Z'`를 사용하여
     두바이(Asia/Dubai, UTC+4)의 현재 시간 수집
   - 시간 값, 타임존 이름, 포맷된 문자열을 커맨드에 반환
   - 프론트매터: tools (Bash), model: haiku, color: blue, maxTurns: 3 사용
   - `skills:` 필드를 통해 time-fetcher 스킬을 사전 로드
   time-fetcher 스킬(`agent-teams/.claude/skills/time-fetcher/SKILL.md`)에는
   두바이 시간을 위한 bash 커맨드, 예상 출력 형식이 포함되어야 하며,
   에이전트 전용 도메인 지식이므로 user-invocable: false로 설정하세요.
   커맨드 아키텍트와 스킬 디자이너가 인터페이스에 맞출 수 있도록
   공유 태스크 목록에 합의된 데이터 계약을 게시하세요.

3. **스킬 디자이너** — `agent-teams/.claude/skills/time-svg-creator/SKILL.md`에
   `time-svg-creator` 스킬을 설계하고 구현하세요. 지원 파일로
   `reference.md`(SVG 템플릿 + 출력 템플릿)와 `examples.md`
   (예시 입력/출력 쌍)도 포함하세요. 스킬은:
   - 호출 컨텍스트에서 시간 값, 타임존, 포맷된 문자열을 수신
   - 현재 시간을 보여주는 두바이용 독립 SVG 시간 카드 생성
   - SVG를 `agent-teams/output/dubai-time.svg`에 저장
   - 마크다운 요약을 `agent-teams/output/output.md`에 저장
   - 제공된 정확한 시간 사용 — 재수집 금지
   - 템플릿은 reference.md(플레이스홀더가 있는 SVG 마크업, 마크다운
     출력 템플릿)에, 예시 쌍은 examples.md에 유지
   출력 파일을 위한 `agent-teams/output/` 디렉토리도 생성하세요.

세 팀원 모두 공유 태스크 목록에 태스크를 생성하여
데이터 계약을 조율해야 합니다: 에이전트가 {time, timezone, formatted}를 반환하고,
커맨드가 컨텍스트를 통해 전달하고, 스킬이 소비합니다.
컴포넌트가 독립적이므로 세 팀원 모두 병렬로 시작하세요 —
서로의 구현을 기다리지 않고 데이터 인터페이스에만 동의하면 됩니다.
