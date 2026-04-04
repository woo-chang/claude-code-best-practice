---
name: time-fetcher
description: bash 커맨드를 통해 현재 두바이 시간을 가져오는 지시사항
user-invocable: false
---

## 두바이 시간 가져오기

### 커맨드

```bash
TZ='Asia/Dubai' date '+%Y-%m-%d %H:%M:%S %Z'
```

### 예상 출력 형식

`YYYY-MM-DD HH:MM:SS +04` (걸프 표준시)

### 타임존 상세

- 타임존: Asia/Dubai
- 오프셋: UTC+4
- 약어: GST (걸프 표준시)
- 두바이는 일광 절약 시간제를 준수하지 않음

### 반환 형식

다음 필드를 제공하세요:
- `time`: 시간 부분만 (HH:MM:SS)
- `timezone`: "GST (UTC+4)"
- `formatted`: 커맨드에서의 전체 출력 문자열
