# Claude 고급 도구 사용 패턴

토큰 소비, 지연 시간을 줄이고 도구 정확도를 향상시키는 API 수준 기능 (현재 GA). Opus/Sonnet 4.6과 함께 출시.

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 목차

1. [개요](#overview)
2. [프로그래밍 방식 도구 호출 (PTC)](#programmatic-tool-calling-ptc)
3. [웹 검색/가져오기에 대한 동적 필터링](#dynamic-filtering-for-web-searchfetch)
4. [도구 검색 도구](#tool-search-tool)
5. [도구 사용 예시](#tool-use-examples)
6. [Claude Code 관련성](#claude-code-relevance)

---

<a id="overview"></a>
## 개요

| 기능 | 해결하는 문제 | 토큰 절감 | 가용성 |
|---------|---------------|---------------|--------------|
| 프로그래밍 방식 도구 호출 | 다단계 에이전트 루프가 라운드 트립에서 토큰을 소모 | ~37% 감소 | API, Foundry (GA) |
| 동적 필터링 | 웹 검색/가져오기 결과가 관련 없는 콘텐츠로 컨텍스트 팽창 | ~24% 입력 토큰 감소 | API, Foundry (GA) |
| 도구 검색 도구 | 너무 많은 도구 정의가 컨텍스트 팽창 | ~85% 감소 | API, Foundry (GA) |
| 도구 사용 예시 | 스키마만으로는 사용 패턴 표현 불가 | 72% → 90% 정확도 | API, Foundry (GA) |

모든 기능은 2026년 2월 18일부터 **일반 공개**됩니다.

**전략적 레이어링** — 가장 큰 병목 지점부터 시작하세요:
- 도구 정의로 인한 컨텍스트 팽창 → 도구 검색 도구
- 대용량 중간 결과 → 프로그래밍 방식 도구 호출
- 웹 검색 노이즈 → 동적 필터링
- 매개변수 오류 → 도구 사용 예시

---

<a id="programmatic-tool-calling-ptc"></a>
## 프로그래밍 방식 도구 호출 (PTC)

<img src="assets/programmatic-tool-calling-diagram.svg" alt="PTC 다이어그램 — 전통적 vs 프로그래밍 방식 도구 호출" width="100%" />

### 패러다임 전환

**이전 (전통적 도구 호출):**
```
사용자 프롬프트 → Claude → 도구 호출 1 → 응답 1 → Claude → 도구 호출 2 → 응답 2 → Claude → 도구 호출 3 → 응답 3 → Claude → 최종 답변
```
각 도구 호출은 전체 모델 라운드 트립이 필요합니다. 3개 도구 = 3번의 추론 패스.

**이후 (프로그래밍 방식 도구 호출):**
```
사용자 프롬프트 → Claude → Python 스크립트 작성 → 스크립트가 내부적으로 도구 1, 2, 3 호출 → stdout → Claude → 최종 답변
```
Claude가 모든 도구를 오케스트레이션하는 코드를 작성합니다. 최종 `stdout`만 컨텍스트 윈도우에 들어갑니다. 3개 도구 = 1번의 추론 패스.

### 작동 방식

1. `allowed_callers: ["code_execution_20250825"]`로 도구 정의
2. Claude가 샌드박스 내에서 비동기 함수로 해당 도구를 호출하는 Python 작성
3. 도구 함수가 호출되면 샌드박스가 일시 중지되고 API가 `tool_use` 블록 반환
4. 도구 결과 제공 — Claude의 컨텍스트가 아닌 **실행 중인 코드**로 전달
5. 코드가 재개되어 결과를 처리하고 필요하면 더 많은 도구 호출
6. 최종 실행의 `stdout`만 Claude에 도달

### 주요 구성

```json
{
  "tools": [
    {
      "type": "code_execution_20250825",
      "name": "code_execution"
    },
    {
      "name": "query_database",
      "description": "Execute a SQL query. Returns rows as JSON objects with fields: id (str), name (str), revenue (float).",
      "input_schema": {
        "type": "object",
        "properties": {
          "sql": { "type": "string", "description": "SQL query to execute" }
        },
        "required": ["sql"]
      },
      "allowed_callers": ["code_execution_20250825"]
    }
  ]
}
```

### `allowed_callers` 필드

| 값 | 동작 |
|-------|----------|
| `["direct"]` | 전통적 도구 호출만 (생략 시 기본값) |
| `["code_execution_20250825"]` | Python 샌드박스에서만 호출 가능 |
| `["direct", "code_execution_20250825"]` | 두 모드 모두 사용 가능 |

**권장 사항:** 도구당 하나의 모드를 선택하세요, 둘 다 아닙니다. 이것이 Claude에게 더 명확한 지침을 제공합니다.

### 응답의 `caller` 필드

모든 도구 사용 블록에는 어떻게 호출되었는지 알 수 있는 `caller` 필드가 포함됩니다:

```json
// 직접 (전통적)
{ "caller": { "type": "direct" } }

// 프로그래밍 방식 (코드 실행에서)
{ "caller": { "type": "code_execution_20250825", "tool_id": "srvtoolu_abc123" } }
```

### 고급 패턴

**일괄 처리** — N개 항목을 1번의 추론 패스로 처리:
```python
regions = ["West", "East", "Central", "North", "South"]
results = {}
for region in regions:
    data = await query_database(f"SELECT SUM(revenue) FROM sales WHERE region='{region}'")
    results[region] = data[0]["revenue"]

top = max(results.items(), key=lambda x: x[1])
print(f"Top region: {top[0]} with ${top[1]:,}")
```

**조기 종료** — 성공 기준이 충족되면 즉시 중지:
```python
endpoints = ["us-east", "eu-west", "apac"]
for endpoint in endpoints:
    status = await check_health(endpoint)
    if status == "healthy":
        print(f"Found healthy endpoint: {endpoint}")
        break
```

**조건부 도구 선택:**
```python
file_info = await get_file_info(path)
if file_info["size"] < 10000:
    content = await read_full_file(path)
else:
    content = await read_file_summary(path)
print(content)
```

**데이터 필터링** — Claude가 보는 것을 줄이기:
```python
logs = await fetch_logs(server_id)
errors = [log for log in logs if "ERROR" in log]
print(f"Found {len(errors)} errors")
for error in errors[-10:]:
    print(error)
```

### 모델 호환성

| 모델 | 지원 여부 |
|-------|-----------|
| Claude Opus 4.6 | 예 |
| Claude Sonnet 4.6 | 예 |
| Claude Sonnet 4.5 | 예 |
| Claude Opus 4.5 | 예 |

### 제약 사항

| 제약 | 세부 사항 |
|-----------|--------|
| **Bedrock/Vertex 미지원** | API와 Foundry만 |
| **MCP 도구 미지원** | MCP 커넥터 도구는 프로그래밍 방식으로 호출 불가 |
| **웹 검색/가져오기 미지원** | PTC에서 웹 도구 미지원 |
| **구조화된 출력 미지원** | `strict: true` 도구 비호환 |
| **강제 도구 선택 미지원** | `tool_choice`가 PTC를 강제할 수 없음 |
| **컨테이너 수명** | 만료 전 약 4.5분 |
| **ZDR** | 제로 데이터 보존 미적용 |
| **문자열로서의 도구 결과** | 코드 주입 위험을 위해 외부 결과 검증 필요 |

### PTC 사용 시기

| 좋은 사용 사례 | 덜 이상적인 경우 |
|----------------|------------|
| 집계가 필요한 대규모 데이터셋 처리 | 간단한 응답의 단일 도구 호출 |
| 순차적인 3개 이상의 종속 도구 호출 | 즉각적인 사용자 피드백이 필요한 도구 |
| Claude가 보기 전에 결과 필터링/변환 | 매우 빠른 작업 (오버헤드 > 이점) |
| 많은 항목에 걸친 병렬 작업 | |
| 중간 결과에 기반한 조건부 로직 | |

### 토큰 효율성

- 프로그래밍 방식 호출의 도구 결과는 **Claude의 컨텍스트에 추가되지 않음** — 최종 `stdout`만
- 중간 처리는 모델 토큰이 아닌 코드에서 발생
- 10개 도구를 프로그래밍 방식으로 ≈ 10개 직접 호출의 1/10 토큰

---

<a id="dynamic-filtering-for-web-searchfetch"></a>
## 웹 검색/가져오기에 대한 동적 필터링

### 문제점

웹 검색 및 가져오기 도구가 전체 HTML 페이지를 Claude의 컨텍스트 윈도우에 덤프합니다. 대부분의 내용은 관련 없는 — 네비게이션, 광고, 보일러플레이트입니다. 그러면 Claude가 모든 것을 추론하면서 토큰을 낭비하고 정확도가 저하됩니다.

### 해결책

Claude가 이제 **컨텍스트 윈도우에 들어오기 전에 웹 결과를 필터링하기 위해 Python 코드를 작성하고 실행합니다**. 원시 HTML을 추론하는 대신 Claude가 샌드박스에서 관련 내용만 필터링, 파싱, 추출합니다.

### 작동 방식

**이전:**
```
쿼리 → 검색 결과 → 전체 HTML 가져오기 × N 페이지 → 모든 내용이 컨텍스트에 진입 → Claude가 모든 것을 추론
```

**이후:**
```
쿼리 → 검색 결과 → Claude가 필터링 코드 작성 → 코드가 관련 내용만 추출 → 필터링된 결과가 컨텍스트에 진입
```

### API 구성

베타 헤더와 함께 업데이트된 도구 유형 버전 사용:

```json
{
  "model": "claude-opus-4-6",
  "max_tokens": 4096,
  "tools": [
    {
      "type": "web_search_20260209",
      "name": "web_search"
    },
    {
      "type": "web_fetch_20260209",
      "name": "web_fetch"
    }
  ]
}
```

**필요한 헤더:** `anthropic-beta: code-execution-web-tools-2026-02-09`

Sonnet 4.6 및 Opus 4.6과 함께 새 도구 유형 버전 사용 시 **기본으로 활성화**.

### 벤치마크 결과

**BrowseComp** (웹사이트에서 특정 정보 찾기):

| 모델 | 필터링 없이 | 필터링 사용 | 개선 |
|-------|-------------------|----------------|-------------|
| Sonnet 4.6 | 33.3% | **46.6%** | +13.3 pp |
| Opus 4.6 | 45.3% | **61.6%** | +16.3 pp |

**DeepsearchQA** (다단계 리서치, F1 점수):

| 모델 | 필터링 없이 | 필터링 사용 | 개선 |
|-------|-------------------|----------------|-------------|
| Sonnet 4.6 | 52.6% | **59.4%** | +6.8 pp |
| Opus 4.6 | 69.8% | **77.3%** | +7.5 pp |

**토큰 효율성:** 평균 24% 입력 토큰 감소. Sonnet 4.6은 비용 감소 혜택을 봅니다. Opus 4.6은 더 복잡한 필터링 코드로 인해 약간 증가할 수 있습니다.

### 사용 사례

- 기술 문서 필터링
- 여러 소스에 걸친 인용 검증
- 검색 결과 교차 참조
- 다단계 리서치 쿼리
- 대용량 페이지에 묻혀 있는 특정 데이터 포인트 찾기

---

<a id="tool-search-tool"></a>
## 도구 검색 도구

### 문제점

모든 도구 정의를 미리 로드하면 컨텍스트가 낭비됩니다. 50개의 MCP 도구가 각각 ~1.5K 토큰이면 사용자가 질문하기도 전에 75K 토큰이 됩니다.

### 해결책

가끔 사용되는 도구를 `defer_loading: true`로 표시합니다. 초기 컨텍스트에서 제외됩니다. Claude는 도구 검색 도구를 통해 필요 시 이를 검색합니다.

### 구성

```json
{
  "tools": [
    {
      "type": "mcp_toolset",
      "mcp_server_name": "google-drive",
      "default_config": { "defer_loading": true },
      "configs": {
        "search_files": { "defer_loading": false }
      }
    }
  ]
}
```

### 모범 사례

- 가장 많이 사용되는 3-5개의 도구는 항상 로드하고 나머지는 지연
- 명확하고 설명적인 도구 이름과 설명 작성 (검색이 이에 의존)
- 시스템 프롬프트에 사용 가능한 기능 문서화

### 사용 시기

- 도구 정의가 10K 토큰 초과 소비
- 10개 이상의 도구 사용 가능
- 여러 MCP 서버
- 너무 많은 옵션으로 인한 도구 선택 정확도 문제

### 토큰 절감

도구 정의 토큰에서 ~85% 감소 (Anthropic 벤치마크에서 77K → 8.7K).

### Claude Code 동급 기능

Claude Code에는 **MCP 도구 검색 자동 모드**가 있습니다 (v2.1.7부터 기본으로 활성화). MCP 도구 설명이 컨텍스트의 10%를 초과하면 지연되고 `MCPSearch`를 통해 검색됩니다. `ENABLE_TOOL_SEARCH=auto:N`으로 임계값을 구성하세요 (N은 컨텍스트 비율 0-100).

---

<a id="tool-use-examples"></a>
## 도구 사용 예시

### 문제점

JSON 스키마는 구조를 정의하지만 다음을 표현할 수 없습니다:
- 선택적 매개변수를 언제 포함할지
- 어떤 매개변수 조합이 의미가 있는지
- 형식 규칙 (날짜 형식, ID 패턴)
- 중첩 구조 사용

### 해결책

도구 정의에 `input_examples` 추가 — 스키마를 넘어서는 구체적인 사용 패턴.

### 구성

```json
{
  "name": "create_ticket",
  "description": "Create a support ticket",
  "input_schema": {
    "type": "object",
    "properties": {
      "title": { "type": "string" },
      "priority": { "type": "string", "enum": ["low", "medium", "high", "critical"] },
      "assignee": { "type": "string" },
      "labels": { "type": "array", "items": { "type": "string" } }
    },
    "required": ["title"]
  },
  "input_examples": [
    {
      "title": "Login page returns 500 error",
      "priority": "critical",
      "assignee": "oncall-team",
      "labels": ["bug", "auth", "production"]
    },
    {
      "title": "Add dark mode support",
      "priority": "low",
      "labels": ["feature-request", "ui"]
    },
    {
      "title": "Update API docs for v2 endpoints"
    }
  ]
}
```

### 모범 사례

- "example_value" 같은 자리 표시자 문자열이 아닌 **실제 데이터** 사용
- **다양성 표시**: 최소, 부분, 전체 사양
- 간결하게 유지: **도구당 1-5개 예시**
- 모호함 해소에 집중 — 스키마 완전성보다 동작 명확성 목표
- 매개변수 상관관계 표시 (예: `priority: "critical"`은 `assignee`를 가지는 경향이 있음)

### 결과

Anthropic 벤치마크에서 복잡한 매개변수 처리 시 72% → 90% 정확도.

---

<a id="claude-code-relevance"></a>
## Claude Code 관련성

### Claude Code 사용자에게 직접 적용되는 것

| 기능 | Claude Code 상태 | 조치 |
|---------|-------------------|--------|
| 도구 검색 | v2.1.7부터 MCPSearch 자동 모드로 기본 제공 | MCP 도구가 많으면 `ENABLE_TOOL_SEARCH=auto:N` 조정 |
| 동적 필터링 | CLI에서 사용 불가 (API 수준 웹 도구) | 웹 리서치를 하는 Agent SDK 사용자에게 관련 |
| PTC | CLI에서 사용 불가 | 사용자 정의 에이전트를 구축하는 Agent SDK 사용자에게 관련 |
| 도구 사용 예시 | CLI에서 구성 불가 | 사용자 정의 MCP 서버 제작자에게 관련 |

### Agent SDK 개발자를 위해

`@anthropic-ai/claude-agent-sdk`로 에이전트를 구축하는 경우 PTC를 즉시 활용할 수 있습니다:

1. 도구 배열에 `code_execution_20250825` 추가
2. 일괄 처리/필터링의 이점이 있는 도구에 `allowed_callers` 설정
3. 도구 결과 루프 구현 (일시 중지 → 결과 제공 → 재개)
4. 더 쉬운 프로그래밍 방식 파싱을 위해 도구에서 구조화된 데이터 (JSON) 반환

### MCP 서버 제작자를 위해

사용자 정의 MCP 서버를 구축하는 경우 도구 사용 예시가 Claude가 도구를 사용하는 방법을 개선할 수 있습니다:
- 도구 스키마에 `input_examples` 추가
- 설명에 반환 형식 명확하게 문서화 (PTC가 이를 파싱해야 함)

---

## 소스

- [Anthropic 엔지니어링: 고급 도구 사용](https://www.anthropic.com/engineering/advanced-tool-use)
- [프로그래밍 방식 도구 호출 문서](https://platform.claude.com/docs/en/agents-and-tools/tool-use/programmatic-tool-calling)
- [코드 실행 도구 문서](https://platform.claude.com/docs/en/agents-and-tools/tool-use/code-execution-tool)
- [동적 필터링으로 개선된 웹 검색](https://claude.com/blog/improved-web-search-with-dynamic-filtering)
