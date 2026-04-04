---
name: time-skill
description: 파키스탄 표준시 (PKT, UTC+5)로 현재 시간을 표시합니다. 사용자가 현재 시간, 파키스탄 시간, 또는 PKT를 물어볼 때 사용하세요.
user-invocable: true
---

# 타임 스킬

이 스킬은 파키스탄 표준시 (PKT)로 현재 날짜와 시간을 표시합니다.

## 태스크

파키스탄 표준시 (UTC+5)로 현재 날짜와 시간을 표시합니다.

## 지시사항

1. **현재 시간 가져오기**: 다음 bash 커맨드를 실행하세요:
   ```
   TZ='Asia/Karachi' date '+%Y-%m-%d %H:%M:%S %Z'
   ```

2. **결과 표시**: 다음 형식으로 시간을 표시하세요:
   ```
   Current Time in Pakistan (PKT): YYYY-MM-DD HH:MM:SS PKT
   ```

## 요구사항

- 항상 `Asia/Karachi` 타임존 (UTC+5)을 사용하세요
- 24시간 형식을 사용하세요
- 시간과 함께 날짜를 포함하세요
- 출력을 간결하게 유지하세요 — 추가 주석 없음
