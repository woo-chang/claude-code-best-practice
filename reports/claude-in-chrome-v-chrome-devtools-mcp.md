# 브라우저 자동화 MCP 종합 비교 리포트

<table width="100%">
<tr>
<td><a href="../">← Claude Code 모범 사례로 돌아가기</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 요약

광범위한 조사를 바탕으로, 스크린샷에 나온 두 도구와 세 번째 주요 대안을 분석했습니다. 작업 자동화 테스트에 가장 적합한 옵션을 선택하는 데 도움이 되는 종합 비교를 정리했습니다.

---

## 1. 세 가지 후보

### **A. Chrome DevTools MCP** (스크린샷 #1)
- **출처:** 공식 Google Chrome 팀
- **출시:** 2025년 9월 공개 프리뷰
- **아키텍처:** Chrome DevTools Protocol (CDP) + Puppeteer 기반
- **토큰 사용량:** 약 19.0k 토큰 (컨텍스트의 9.5%)
- **도구:** 6개 카테고리에 걸쳐 26개 전문 도구

### **B. Claude in Chrome** (스크린샷 #2)
- **출처:** 공식 Anthropic 확장 프로그램
- **출시:** 베타, 모든 유료 플랜 순차 출시 (Pro, Max, Team, Enterprise)
- **아키텍처:** 컴퓨터 사용 기능을 갖춘 브라우저 확장 프로그램
- **토큰 사용량:** 약 15.4k 토큰 (컨텍스트의 7.7%)
- **도구:** 컴퓨터 사용 기능 포함 16개 도구

### **C. Playwright MCP** (강력한 대안)
- **출처:** Microsoft (공식 + 커뮤니티 구현)
- **아키텍처:** 접근성 트리 기반 자동화
- **토큰 사용량:** 약 13.7k 토큰 (컨텍스트의 6.8%)
- **도구:** 21개 도구

---

## 2. 상세 기능 비교

| 기능 | Chrome DevTools MCP | Claude in Chrome | Playwright MCP |
|---------|---------------------|------------------|----------------|
| **주요 목적** | 디버깅 및 성능 분석 | 범용 브라우저 자동화 | UI 테스트 및 E2E |
| **브라우저 지원** | Chrome 전용 | Chrome 전용 | Chromium, Firefox, WebKit |
| **토큰 효율** | 19.0k (9.5%) | 15.4k (7.7%) | 13.7k (6.8%) |
| **요소 선택** | CSS/XPath 선택자 | 시각적 + DOM | 접근성 트리 (의미론적) |
| **성능 추적** | ✅ 우수 | ❌ 없음 | ⚠️ 제한적 |
| **네트워크 검사** | ✅ 심층 분석 | ⚠️ 기본 | ⚠️ 기본 |
| **콘솔 로그** | ✅ 완전 접근 | ✅ 완전 접근 | ⚠️ 제한적 |
| **크로스 브라우저** | ❌ 없음 | ❌ 없음 | ✅ 있음 |
| **CI/CD 통합** | ✅ 우수 | ❌ 미흡 (로그인 필요) | ✅ 우수 |
| **헤드리스 모드** | ✅ 있음 | ❌ 없음 | ✅ 있음 |
| **인증** | 별도 설정 필요 | 현재 세션 사용 | 별도 설정 필요 |
| **예약 작업** | ❌ 없음 | ✅ 있음 | ❌ 없음 |
| **비용** | 무료 | 유료 플랜 필요 | 무료 |
| **로컬 설정** | Node.js 필요 | 브라우저 확장 프로그램 | Node.js 필요 |

---

## 3. 도구 목록

### Chrome DevTools MCP (26개 도구)

```
입력 자동화 (8):     click, drag, fill, fill_form, handle_dialog,
                     hover, press_key, upload_file

탐색 (6):            close_page, list_pages, navigate_page,
                     new_page, select_page, wait_for

에뮬레이션 (2):      emulate, resize_page

성능 (3):            performance_analyze_insight,
                     performance_start_trace, performance_stop_trace

네트워크 (2):        get_network_request, list_network_requests

디버깅 (5):          evaluate_script, get_console_message,
                     list_console_messages, take_screenshot,
                     take_snapshot
```

### Claude in Chrome (16개 도구)

```
브라우저 제어:       navigate, read_page, find, computer
                     (click, type, scroll)

폼 인터랙션:         form_input, javascript_tool

미디어:              upload_image, get_page_text, gif_creator

탭 관리:             tabs_context_mcp, tabs_create_mcp

개발:                read_console_messages, read_network_requests

유틸리티:            shortcuts_list, shortcuts_execute,
                     resize_window, update_plan
```

### Playwright MCP (21개 도구)

```
탐색:                navigate, goBack, goForward, reload

인터랙션:            click, fill, select, hover, press,
                     drag, uploadFile

요소 쿼리:           getElement, getElements, waitForSelector

어설션:              assertVisible, assertText, assertTitle

페이지 상태:         screenshot, getAccessibilityTree,
                     evaluateScript

브라우저 관리:       newPage, closePage
```

---

## 4. 자동화 테스트 사용 사례 분석

### **Chrome DevTools MCP가 최적인 경우:**

✅ **성능 테스트**
- Core Web Vitals를 포함한 성능 추적 기록
- 렌더링 병목 및 레이아웃 이동 식별
- 메모리 누수 감지 및 CPU 프로파일링

✅ **심층 디버깅**
- 네트워크 요청 검사 (헤더, 페이로드, 타이밍)
- 콘솔 오류 분석 및 스택 추적
- 실시간 DOM 검사

✅ **CI/CD 파이프라인**
- 헤드리스 실행 지원
- 안정적인 스크립트 기반 자동화
- 인증 상태 의존성 없음

**이상적인 워크플로우:** "이 페이지가 왜 느린지 찾기" 또는 "이 API 호출 디버깅"

---

### **Claude in Chrome이 최적인 경우:**

✅ **수동 테스트 지원**
- 계정에 로그인된 상태로 테스트
- 시각적 컨텍스트를 활용한 탐색적 테스트
- 반복 재생 가능한 워크플로우 기록

✅ **빠른 검증**
- 디자인 검증 (Figma와 결과물 비교)
- 새 기능 스팟 체크
- 개발 중 콘솔 오류 읽기

✅ **반복 브라우저 작업**
- 예약 자동 검사
- 멀티탭 워크플로우 관리
- 기록된 액션을 통한 학습

**이상적인 워크플로우:** "내 변경사항이 제대로 보이는지 확인" 또는 "내 로그인으로 이 폼 테스트"

---

### **Playwright MCP가 최적인 경우:**

✅ **E2E 테스트 자동화**
- 크로스 브라우저 테스트 (Chrome, Firefox, Safari)
- 재사용 가능한 테스트 스크립트 생성
- Page Object Model 생성

✅ **신뢰할 수 있는 UI 테스트**
- 접근성 트리 = 불안정한 선택자 없음
- 결정론적 인터랙션
- UI 변경에 덜 취약

✅ **CI/CD 통합**
- 파이프라인을 위한 헤드리스 모드
- 자연어로 Playwright 테스트 파일 생성
- 테스트 관리 도구와 통합

**이상적인 워크플로우:** "이 사용자 흐름에 대한 E2E 테스트 작성" 또는 "크로스 브라우저로 테스트"

---

## 5. 토큰 효율 분석

| 도구 | 토큰 사용량 | 컨텍스트 % | 효율 등급 |
|------|-------------|--------------|-------------------|
| Playwright MCP | 약 13.7k | 6.8% | ⭐⭐⭐⭐⭐ 최고 |
| Claude in Chrome | 약 15.4k | 7.7% | ⭐⭐⭐⭐ 양호 |
| Chrome DevTools MCP | 약 19.0k | 9.5% | ⭐⭐⭐ 허용 가능 |

**영향:** 200k 토큰 컨텍스트 기준:
- Playwright는 작업을 위해 186.3k 토큰 여유
- Claude in Chrome은 184.6k 토큰 여유
- Chrome DevTools는 181k 토큰 여유

Playwright와 Chrome DevTools 간 약 5.3k 토큰 차이는 코드 컨텍스트가 많은 복잡한 세션에서 중요할 수 있습니다.

---

## 6. 보안 고려사항

### Chrome DevTools MCP
- ✅ 기본적으로 격리된 브라우저 프로파일
- ✅ 클라우드 의존성 없음
- ✅ 완전한 로컬 제어
- ⚠️ 원격 디버깅 포트 보안 (격리된 프로파일 사용 권장)

### Claude in Chrome
- ⚠️ 완화 없이 **23.6% 공격 성공률** (방어 적용 시 11.2%로 감소)
- ⚠️ 실제 브라우저 세션 사용 (쿠키 노출 위험)
- ⚠️ 금융/성인/불법 복제 사이트 차단
- ⚠️ 알려진 취약점이 있는 베타 단계

### Playwright MCP
- ✅ 격리된 브라우저 컨텍스트
- ✅ 클라우드 의존성 없음
- ✅ 성숙한 보안 모델 (Microsoft 지원)
- ✅ 인증을 안전하게 처리 가능

---

## 7. 설치 커맨드

### Chrome DevTools MCP

```bash
claude mcp add chrome-devtools npx chrome-devtools-mcp@latest
```

### Claude in Chrome

```
Chrome 웹 스토어에서 설치 (Pro/Max/Team/Enterprise 플랜 필요)
```

### Playwright MCP (권장)

```bash
# 먼저 브라우저 설치
npx playwright install

# 그 다음 Claude Code에 추가 (user 범위 = 모든 프로젝트)
claude mcp add playwright -s user -- npx @playwright/mcp@latest
```

---

## 8. 추천

### **자동화 테스트 워크플로우를 위한 추천:**

#### 1순위: **Playwright MCP**

**활용 용도:** 일상적인 E2E 테스트, 크로스 브라우저 검증, 테스트 스크립트 생성

**이유:**
- 가장 낮은 토큰 사용량 (코드에 더 많은 컨텍스트 할당)
- 크로스 브라우저 지원 (Chrome, Firefox, Safari)
- 접근성 트리 방식 = 더 안정적인 선택자
- 우수한 CI/CD 통합
- 실제 Playwright 테스트 파일 생성 가능
- 무료, 구독 불필요

#### 2순위: **Chrome DevTools MCP**

**활용 용도:** 성능 디버깅, 네트워크 분석, Core Web Vitals

**이유:**
- 성능 추적 및 디버깅에 비교할 수 없는 기능
- 심층 네트워크 요청 검사
- 장기 지원을 갖춘 공식 Google 도구
- "왜 이렇게 느린가?"라는 질문에 답할 때 필수

#### 3순위 (상황별): **Claude in Chrome**

**활용 용도:** 로그인된 상태에서 빠른 수동 검증, 탐색적 테스트, 디자인 검증

**이유:**
- 개발 중 빠른 시각적 확인에 유용
- 로그인 상태를 읽을 수 있음
- "제대로 보이나요?" 확인에 유용
- CI/CD 또는 본격적인 테스트 자동화에는 적합하지 않음

---

## 9. 권장 설정

```bash
# Playwright와 Chrome DevTools MCP 모두 설치
npx playwright install
claude mcp add playwright -s user -- npx @playwright/mcp@latest
claude mcp add chrome-devtools -s user -- npx chrome-devtools-mcp@latest
```

### 권장 워크플로우

```
1. 개발      → Claude Code (터미널)
2. 테스트    → Playwright MCP (E2E, 크로스 브라우저)
3. 디버그    → Chrome DevTools MCP (성능, 네트워크)
4. 검증      → Claude in Chrome (빠른 시각적 확인)
5. CI/CD     → Playwright MCP (헤드리스, 자동화)
```

---

## 10. 최종 판정

| 필요한 경우... | 사용할 도구 |
|----------------|----------|
| 크로스 브라우저 E2E 테스트 | **Playwright MCP** |
| 성능 분석 | **Chrome DevTools MCP** |
| 네트워크 디버깅 | **Chrome DevTools MCP** |
| 빠른 시각적 검증 | **Claude in Chrome** |
| CI/CD 자동화 | **Playwright MCP** |
| 테스트 스크립트 생성 | **Playwright MCP** |
| 최소 토큰 사용량 | **Playwright MCP** |
| 로그인 세션 테스트 | **Claude in Chrome** |
| 콘솔 로그 디버깅 | **Chrome DevTools MCP** |

### **요약 추천:**

**Playwright MCP와 Chrome DevTools MCP를 모두 설치하세요.** Playwright를 주요 테스트 도구로 사용하세요 (더 토큰 효율적이고, 크로스 브라우저 지원이 있으며, E2E에 더 적합합니다). 심층 성능 분석이나 네트워크 디버깅이 필요할 때 Chrome DevTools를 사용하세요. Claude in Chrome은 로그인 세션이 필요한 빠른 수동 검증에만 사용하세요.

---

## 소스

- [Chrome DevTools MCP - GitHub](https://github.com/ChromeDevTools/chrome-devtools-mcp)
- [Anthropic - Piloting Claude in Chrome](https://claude.com/blog/claude-for-chrome)
- [Claude in Chrome Help Center](https://support.claude.com/en/articles/12012173-getting-started-with-claude-in-chrome)
- [Playwright MCP - GitHub](https://github.com/microsoft/playwright-mcp)
- [Simon Willison - Using Playwright MCP with Claude Code](https://til.simonwillison.net/claude-code/playwright-mcp-claude-code)
- [Testomat.io - Playwright MCP Claude Code](https://testomat.io/blog/playwright-mcp-claude-code/)
- [MCP Integration Guide - Scrapeless](https://www.scrapeless.com/en/blog/mcp-integration-guide)
- [Chrome DevTools MCP Guide - Vladimir Siedykh](https://vladimirsiedykh.com/blog/chrome-devtools-mcp-ai-browser-debugging-complete-guide-2025)
- [Addy Osmani - Give your AI eyes](https://addyosmani.com/blog/devtools-mcp/)

---

*이 리포트는 Claude Code의 Opus 4.5 모델을 사용하여 2025년 12월 19일에 생성되었습니다.*
