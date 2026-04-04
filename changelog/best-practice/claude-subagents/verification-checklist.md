# 검증 체크리스트 — Subagents Report

규칙은 시간이 지남에 따라 축적됩니다. 각 workflow-changelog 실행은 지정된 깊이에서 모든 규칙을 실행해야 합니다. 기존 규칙이 잡았어야 하는(그러나 존재하지 않았거나 너무 얕은) 새로운 유형의 드리프트가 발견되면, 여기에 새 규칙을 추가하세요.

## 깊이 수준

| 깊이 | 의미 | 예시 |
|-------|---------|---------|
| `exists` | 섹션/표/파일이 존재하는지 확인 | "보고서에 Memory Scopes 표가 있는가?" |
| `presence-check` | 특정 항목이 있는지 없는지 확인 | "`color` 필드가 Frontmatter Fields 표에 있는가?" |
| `content-match` | 소스와 단어 단위로 실제 값 비교 | "`model` 필드 설명이 공식 문서와 일치하는가?" |
| `field-level` | 모든 개별 필드가 반영되었는지 확인 | "공식 문서의 각 프론트매터 필드가 표에 나타나는가?" |
| `cross-file` | 동일한 값이 여러 파일에서 일치하는지 확인 | "CLAUDE.md 에이전트 섹션이 보고서의 필드 목록과 일치하는가?" |

---

## 1. 프론트매터 필드 표

공식 문서에 대한 Frontmatter Fields 표를 검증하는 규칙.

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 1A | Field Completeness | For each agent frontmatter field in official docs, verify it appears in the report's Frontmatter Fields table | field-level | sub-agents reference page | 2026-02-28 | Initial checklist — ensures no new fields are missed |
| 1B | Field Types | For each field in the table, verify the Type column matches official docs | content-match | sub-agents reference page | 2026-02-28 | Initial checklist — type mismatches cause user confusion |
| 1C | Required Status | For each field, verify the Required column matches official docs | content-match | sub-agents reference page | 2026-02-28 | Initial checklist — wrong required status causes broken agents |
| 1D | Field Descriptions | For each field, verify the Description column accurately reflects official docs behavior | content-match | sub-agents reference page | 2026-02-28 | Initial checklist — stale descriptions mislead users |

---

## 2. 메모리 범위

Memory Scopes 표를 검증하는 규칙.

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 2A | Scope Completeness | Verify all memory scopes from official docs appear in the Memory Scopes table | field-level | sub-agents reference page | 2026-02-28 | Initial checklist — new scopes could be added |
| 2B | Storage Locations | For each scope, verify the Storage Location column matches official docs | content-match | sub-agents reference page | 2026-02-28 | Initial checklist — wrong paths cause data loss |

---

## 3. 예시

예시 정확성을 검증하는 규칙.

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 3A | Minimal Example | Verify the minimal example uses only required fields with valid syntax | content-match | sub-agents reference page | 2026-02-28 | Initial checklist — minimal example should stay minimal |
| 3B | Full-Featured Example | Verify the full-featured example demonstrates ALL available frontmatter fields | field-level | sub-agents reference page | 2026-02-28 | Initial checklist — full example must show every field |

---

## 4. 범위 및 우선순위

범위 및 우선순위 정보를 검증하는 규칙.

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 4A | Priority Order | Verify the Scope and Priority table lists all agent locations in correct priority order | content-match | sub-agents reference page + CLI reference page | 2026-02-28 | Initial checklist — wrong priority order causes resolution bugs |
| 4B | Invocation Methods | Verify the invocation methods table lists ALL invocation methods from CLI reference and sub-agents docs, including `--agent` (singular), `--agents` (plural), `/agents`, `claude agents`, Agent tool, and agent resumption | field-level | CLI reference page + sub-agents reference page | 2026-03-07 | `--agent` CLI flag was missing from the invocation table — it's a distinct invocation method for running Claude as a specific agent |

---

## 5. 파일 간 일관성

보고서와 다른 리포지토리 파일 간의 일관성을 검증하는 규칙.

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 5A | CLAUDE.md Sync | Verify CLAUDE.md's Subagent Definition Structure section lists the same fields as the report's Frontmatter Fields table | cross-file | CLAUDE.md vs report | 2026-02-28 | Initial checklist — CLAUDE.md could drift from report |

---

## 6. 프로세스

워크플로우 검증 프로세스 자체에 대한 메타 규칙.

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 6A | Source Credibility Guard | Only flag items as drift if confirmed by official sources (sub-agents reference page, CLI reference page, GitHub changelog). Third-party blog sources may be outdated or wrong — use them for leads only, verify against official docs before flagging | content-match | official docs only | 2026-02-28 | Adopted from hooks workflow — prevents false positives from blog sources |

---

## 7. 에이전트 표

Official Claude Agents 표와 이 리포지토리의 에이전트 표를 검증하는 규칙.

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 7A | Built-in Agent Completeness | Verify the "Official Claude Agents" table lists all built-in agent types with correct model, tools, and description | field-level | sub-agents reference page + changelog | 2026-02-28 | Report only had 3 of 5 built-in agents — `claude-code-guide` and `statusline-setup` were missing |
| 7B | Repository Agent Completeness | Scan `.claude/agents/**/*.md` and verify every agent file appears in the "Agents in This Repository" table with correct model, color, tools, skills, and memory columns | field-level | `.claude/agents/**/*.md` file frontmatter | 2026-02-28 | Repo agents were manually maintained — new agents added to the repo were not reflected in the report |
| 7C | Repository Agent Links | Verify each agent name in the "Agents in This Repository" table has a clickable link that resolves to the correct `.md` file | exists | resolved file path from `best-practice/` | 2026-02-28 | Agent names were made clickable — links must stay valid after file moves |

---

## 8. 하이퍼링크

보고서의 모든 하이퍼링크가 유효한지 검증하는 규칙.

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 8A | Local File Links | Verify all relative file links (e.g. `../.claude/agents/weather-agent.md`) resolve to existing files | exists | local filesystem | 2026-02-28 | File moves (reports/ → best-practice/) broke relative links — must catch future breakage |
| 8B | External URL Links | Verify all external URLs (e.g. `https://code.claude.com/docs/en/sub-agents`) return valid pages | exists | HTTP response | 2026-02-28 | External docs pages can be restructured or removed — must validate on each run |
| 8C | Cross-File Reference Links | Verify links to other report files (e.g. `../reports/claude-agent-memory.md`) resolve to existing files | exists | local filesystem | 2026-02-28 | Reports can be moved or renamed — cross-references must stay in sync |
