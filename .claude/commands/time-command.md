---
description: 파키스탄 표준시 (PKT, UTC+5)로 현재 시간을 표시합니다
---

# Time Command

파키스탄 표준시 (PKT, UTC+5)로 현재 날짜와 시간을 표시합니다.

## 지시사항

1. 다음 bash 커맨드를 실행하여 PKT 현재 시간을 가져오세요:
   ```
   TZ='Asia/Karachi' date '+%Y-%m-%d %H:%M:%S %Z'
   ```

2. 다음 형식으로 사용자에게 결과를 표시하세요:
   ```
   Current Time in Pakistan (PKT): YYYY-MM-DD HH:MM:SS PKT
   ```

## 요구사항

- 항상 `Asia/Karachi` 타임존 (UTC+5) 사용
- 24시간 형식 사용
- 시간과 함께 날짜 포함
- 출력을 간결하게 유지
