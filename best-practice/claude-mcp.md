# MCP 서버 모범 사례

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar%2002%2C%202026%2012%3A30%20PM%20PKT-white?style=flat&labelColor=555)<br>
[![Implemented](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../.mcp.json)

MCP (Model Context Protocol) 서버는 외부 도구, 데이터베이스, API 연결로 Claude Code를 확장합니다. 이 가이드는 일상 사용에 권장되는 서버와 설정 모범 사례를 다룹니다.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 일상 사용을 위한 MCP 서버

> *"15개의 MCP 서버로 과도하게 설정했는데 더 많을수록 좋다고 생각했습니다. 결국 매일 4개만 사용하게 됐습니다."* — [r/mcp](https://reddit.com/r/mcp/comments/1mj0fxs/) (682 upvotes)

| MCP 서버 | 역할 | 리소스 |
|------------|-------------|-----------|
| [**Context7**](https://github.com/upstash/context7) | 최신 라이브러리 문서를 컨텍스트에 가져옴. 오래된 학습 데이터로 인한 잘못된 API 호출 방지 | [Reddit: "코딩에 가장 좋은 MCP"](https://reddit.com/r/mcp/comments/1qarjqm/) · [npm](https://www.npmjs.com/package/@upstash/context7-mcp) |
| [**Playwright**](https://github.com/microsoft/playwright-mcp) | 브라우저 자동화 — UI 기능을 자율적으로 구현, 테스트, 검증. 스크린샷, 탐색, 폼 테스트 | [Reddit: 프론트엔드에 필수](https://reddit.com/r/mcp/comments/1m59pk0/) · [Docs](https://playwright.dev/) |
| [**Claude in Chrome**](https://github.com/nicobailon/claude-code-in-chrome-mcp) | Claude를 실제 Chrome 브라우저에 연결 — 콘솔, 네트워크, DOM 검사. 사용자가 실제로 보는 것 디버그 | [Reddit: 디버깅의 "게임 체인저"](https://reddit.com/r/mcp/comments/1qarjqm/5_mcps_that_have_genuinely_made_me_10x_faster/nza0i7t/) · [비교 리포트](../reports/claude-in-chrome-v-chrome-devtools-mcp.md) |
| [**DeepWiki**](https://github.com/devanshusemwal/deepwiki-mcp) | GitHub 리포지토리의 아키텍처, API 표면, 관계에 대한 구조화된 위키 스타일 문서 가져오기 | [Reddit: "Context7 뒤에 게이트웨이로 설정"](https://reddit.com/r/mcp/comments/1qarjqm/) |
| [**Excalidraw**](https://github.com/antonpk1/excalidraw-mcp-app) | 프롬프트에서 손으로 그린 Excalidraw 스케치 형식의 아키텍처 다이어그램, 플로우차트, 시스템 디자인 생성 | [GitHub](https://github.com/antonpk1/excalidraw-mcp-app) |

조사 (Context7/DeepWiki) -> 디버그 (Playwright/Chrome) -> 문서화 (Excalidraw)

---

## 설정

MCP 서버는 프로젝트 루트의 `.mcp.json` (프로젝트 스코프) 또는 `~/.claude.json` (사용자 스코프)에 설정합니다.

### 서버 타입

| 타입 | 전송 | 예시 |
|------|-----------|---------|
| **stdio** | 로컬 프로세스 생성 | `npx`, `python`, 바이너리 |
| **http** | 원격 URL에 연결 | HTTP/SSE 엔드포인트 |

### 예시 `.mcp.json`

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp"]
    },
    "deepwiki": {
      "command": "npx",
      "args": ["-y", "deepwiki-mcp"]
    },
    "remote-api": {
      "type": "http",
      "url": "https://mcp.example.com/mcp"
    }
  }
}
```

`.mcp.json`에 API 키를 커밋하는 대신 시크릿에는 환경 변수 확장을 사용하세요:

```json
{
  "mcpServers": {
    "remote-api": {
      "type": "http",
      "url": "https://mcp.example.com/mcp?token=${MCP_API_TOKEN}"
    }
  }
}
```

### MCP 서버 설정

`.claude/settings.json`의 이 설정들은 MCP 서버 승인을 제어합니다:

| 키 | 타입 | 설명 |
|-----|------|-------------|
| `enableAllProjectMcpServers` | boolean | 프롬프트 없이 모든 `.mcp.json` 서버 자동 승인 |
| `enabledMcpjsonServers` | array | 자동 승인할 특정 서버 이름 허용 목록 |
| `disabledMcpjsonServers` | array | 거부할 특정 서버 이름 차단 목록 |

### MCP 도구의 권한 규칙

MCP 도구는 권한 규칙에서 `mcp__<server>__<tool>` 명명 규칙을 따릅니다:

```json
{
  "permissions": {
    "allow": [
      "mcp__*",
      "mcp__context7__*",
      "mcp__playwright__browser_snapshot"
    ],
    "deny": [
      "mcp__dangerous-server__*"
    ]
  }
}
```

---

## MCP 스코프

MCP 서버는 세 가지 레벨에서 정의할 수 있습니다:

| 스코프 | 위치 | 목적 |
|-------|----------|---------|
| **프로젝트** | `.mcp.json` (리포지토리 루트) | 팀 공유 서버, git에 커밋됨 |
| **사용자** | `~/.claude.json` (`mcpServers` 키) | 모든 프로젝트에 걸친 개인 서버 |
| **서브에이전트** | 에이전트 프론트매터 (`mcpServers` 필드) | 특정 서브에이전트에 스코프된 서버 |

우선순위: 서브에이전트 > 프로젝트 > 사용자

---

## 출처

- [MCP 서버 — Claude Code 문서](https://code.claude.com/docs/en/mcp)
- [Model Context Protocol 사양](https://modelcontextprotocol.io/)
- [나를 10배 빠르게 만든 5가지 MCP — r/mcp](https://reddit.com/r/mcp/comments/1qarjqm/)
- [MCP 서버 과부하 토론 — r/mcp](https://reddit.com/r/mcp/comments/1mj0fxs/)
