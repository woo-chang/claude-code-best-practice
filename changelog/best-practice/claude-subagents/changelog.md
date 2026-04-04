# Subagents Report — 체인지로그 히스토리

## 상태 범례

| 상태 | 의미 |
|--------|---------|
| ✅ `COMPLETE (reason)` | 조치가 취해지고 성공적으로 해결됨 |
| ❌ `INVALID (reason)` | 발견 사항이 잘못되었거나, 적용되지 않거나, 의도적인 것임 |
| ✋ `ON HOLD (reason)` | 조치 보류 — 외부 의존성 또는 사용자 결정 대기 중 |

---

## [2026-02-28 03:22 PM PKT] Claude Code v2.1.63

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Agents Table | Add `workflow-changelog-claude-agents-frontmatter-agent` to Agents in This Repository table | ✅ COMPLETE (added with model: opus, inherits all tools, no skills/memory) |
| 2 | HIGH | Agents Table | Fix presentation-curator skills column — add `presentation/` prefix to skill names | ✅ COMPLETE (updated to presentation/vibe-to-agentic-framework etc.) |
| 3 | MED | Field Documentation | Add note to `color` field that it is functional but absent from official frontmatter table | ✅ COMPLETE (added note about unofficial status in description column) |
| 4 | MED | Invocation Section | Expand invocation section with --agents CLI flag, /agents command, claude agents CLI, agent resumption | ✅ COMPLETE (added invocation methods table with 5 methods) |

---

## [2026-03-07 08:35 AM PKT] Claude Code v2.1.71

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Broken Link | Fix agent memory link to `reports/claude-agent-memory.md` | ✅ COMPLETE |
| 2 | HIGH | Changed Behavior | Update `tools` field description: `Task(agent_type)` → `Agent(agent_type)` (v2.1.63 rename) | ✅ COMPLETE |
| 3 | HIGH | Changed Behavior | Update invocation section: Task tool → Agent tool (v2.1.63 rename) | ✅ COMPLETE (updated heading, code example, and added rename note) |
| 4 | HIGH | Example Update | Update full-featured example: `Task(monitor, rollback)` → `Agent(monitor, rollback)` | ✅ COMPLETE |
| 5 | HIGH | Built-in Agent | Add `Bash` agent to Official Claude Agents table (model: inherit, purpose: terminal commands in separate context) | ✅ COMPLETE (added to table) |
| 6 | HIGH | Agents Table | Add `workflow-concepts-agent` to Agents in This Repository table (model: opus, color: green) | ✅ COMPLETE |
| 7 | HIGH | Agents Table | Add `workflow-claude-settings-agent` to Agents in This Repository table (model: opus, color: yellow) | ✅ COMPLETE |
| 8 | MED | Built-in Agent | Fix `statusline-setup` model: `inherit` → `Sonnet` | ✅ COMPLETE |
| 9 | MED | Built-in Agent | Fix `claude-code-guide` model: `inherit` → `Haiku` | ❌ NOT APPLICABLE (removed from table) |
| 10 | MED | Agents Table | Fix `weather-agent` color: `teal` → `green` | ✅ COMPLETE |
| 11 | MED | Invocation | Add `--agent <name>` CLI flag to invocation methods table | ✅ COMPLETE (added as first row in invocation methods table) |
| 12 | MED | Changed Behavior | Update line 147 text: "Task tool" → "Agent tool" in Official Claude Agents table header | ✅ COMPLETE (user rewrote header text) |
| 13 | MED | Cross-File | Update CLAUDE.md: `Task(...)` → `Agent(...)` references (lines 50-53, 61) | ✅ COMPLETE (updated orchestration section and tools field description) |

---

## [2026-03-12 12:17 PM PKT] Claude Code v2.1.74

드리프트 없음 — 보고서가 공식 문서와 완전히 동기화됨. 프론트매터 필드 13개와 내장 에이전트 6개 모두 일치.

---

## [2026-03-13 04:21 PM PKT] Claude Code v2.1.74

드리프트 없음 — 보고서가 공식 문서와 완전히 동기화됨. 프론트매터 필드 13개와 내장 에이전트 6개 모두 일치.

---

## [2026-03-15 12:50 PM PKT] Claude Code v2.1.76

드리프트 없음 — 보고서가 공식 문서와 완전히 동기화됨. 프론트매터 필드 13개와 내장 에이전트 6개 모두 일치.

---

## [2026-03-17 12:42 PM PKT] Claude Code v2.1.77

드리프트 없음 — 보고서가 공식 문서와 완전히 동기화됨. 프론트매터 필드 13개와 내장 에이전트 6개 모두 일치.

---

## [2026-03-18 11:41 PM PKT] Claude Code v2.1.78

드리프트 없음 — 보고서가 공식 문서와 완전히 동기화됨. 프론트매터 필드 13개와 내장 에이전트 6개 모두 일치.

---

## [2026-03-19 11:56 AM PKT] Claude Code v2.1.79

드리프트 없음 — 보고서가 공식 문서와 완전히 동기화됨. 프론트매터 필드 13개와 내장 에이전트 6개 모두 일치.

---

## [2026-03-20 08:35 AM PKT] Claude Code v2.1.80

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | New Field | Add `effort` field to Frontmatter Fields table (string, optional — effort level override: `low`, `medium`, `high`, `max`) | ✅ COMPLETE (added between `background` and `isolation`, count updated 14→15) |

---

## [2026-03-21 09:07 PM PKT] Claude Code v2.1.81

드리프트 없음 — 보고서가 공식 문서와 완전히 동기화됨. 프론트매터 필드 15개와 내장 에이전트 6개 모두 일치.

---

## [2026-03-23 09:49 PM PKT] Claude Code v2.1.81

드리프트 없음 — 보고서가 공식 문서와 완전히 동기화됨. 프론트매터 필드 15개 (공식 14개 + 비공식 `color` 1개)와 내장 에이전트 6개 모두 일치.

---

## [2026-03-25 08:07 PM PKT] Claude Code v2.1.83

드리프트 없음 — 보고서가 공식 문서와 완전히 동기화됨. 프론트매터 필드 15개 (공식 14개 + 비공식 `color` 1개)와 내장 에이전트 6개 모두 일치.

**주시 항목:** `initialPrompt`가 v2.1.83 체인지로그에 추가되었지만 공식 문서의 지원 프론트매터 필드 표에는 아직 나타나지 않음. 반영되면 보고서 업데이트 필요.

---

## [2026-03-26 01:01 PM PKT] Claude Code v2.1.84

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | New Field | Add `initialPrompt` to Frontmatter Fields table (string, optional — auto-submitted as first user turn when agent runs as main session agent via `--agent` or `agent` setting) | ✅ COMPLETE (added between `isolation` and `color`, count updated 15→16) |

---

## [2026-03-27 06:28 PM PKT] Claude Code v2.1.85

드리프트 없음 — 보고서가 공식 문서와 완전히 동기화됨. 프론트매터 필드 16개 (공식 15개 + 비공식 `color` 1개)와 내장 에이전트 6개 모두 일치.

---

## [2026-03-28 06:00 PM PKT] Claude Code v2.1.86

드리프트 없음 — 보고서가 공식 문서와 완전히 동기화됨. 프론트매터 필드 16개 (공식 15개 + 비공식 `color` 1개)와 내장 에이전트 6개 모두 일치.

---

## [2026-04-01 12:26 PM PKT] Claude Code v2.1.89

드리프트 없음 — 보고서가 공식 문서와 완전히 동기화됨. 프론트매터 필드 16개 (공식 15개 + 비공식 `color` 1개)와 내장 에이전트 6개 모두 일치.

---

## [2026-04-02 09:11 PM PKT] Claude Code v2.1.90

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Removed Agent | Remove `Bash` from Official Claude Agents table — official docs list 5 built-in agents, `Bash` is not among them | ✅ COMPLETE (removed Bash row, renumbered 6→5 agents) |
| 2 | LOW | Field Docs | Update `color` field description — remove "absent from official frontmatter table" note; `color` now appears in official supported frontmatter fields table | ✅ COMPLETE (removed unofficial note from color field description) |

---

## [2026-04-03 08:30 PM PKT] Claude Code v2.1.91

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | LOW | Field Docs | Update `permissionMode` field description — add `auto` as a valid value (official docs now list: `default`, `acceptEdits`, `auto`, `dontAsk`, `bypassPermissions`, `plan`) | ✅ COMPLETE (added `auto` between `acceptEdits` and `dontAsk` in permissionMode description) |
