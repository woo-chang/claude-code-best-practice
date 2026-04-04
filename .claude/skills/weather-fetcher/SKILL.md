---
name: weather-fetcher
description: Open-Meteo API에서 두바이, UAE의 현재 날씨 온도 데이터를 가져오는 지시사항
user-invocable: false
---

# 날씨 가져오기 스킬

이 스킬은 현재 날씨 데이터를 가져오는 지시사항을 제공합니다.

## 태스크

요청된 단위 (섭씨 또는 화씨)로 두바이, UAE의 현재 온도를 가져옵니다.

## 지시사항

1. **날씨 데이터 가져오기**: WebFetch 도구를 사용하여 Open-Meteo API에서 두바이의 현재 날씨 데이터를 가져오세요.

   **섭씨**의 경우:
   - URL: `https://api.open-meteo.com/v1/forecast?latitude=25.2048&longitude=55.2708&current=temperature_2m&temperature_unit=celsius`

   **화씨**의 경우:
   - URL: `https://api.open-meteo.com/v1/forecast?latitude=25.2048&longitude=55.2708&current=temperature_2m&temperature_unit=fahrenheit`

2. **온도 추출**: JSON 응답에서 현재 온도를 추출하세요:
   - 필드: `current.temperature_2m`
   - 단위 레이블: `current_units.temperature_2m`

3. **결과 반환**: 온도 값과 단위를 명확하게 반환하세요.

## 예상 출력

이 스킬의 지시사항을 완료한 후:
```
Current Dubai Temperature: [X]°[C/F]
Unit: [Celsius/Fahrenheit]
```

## 참고사항

- 온도만 가져오세요, 변환을 수행하거나 파일을 작성하지 마세요
- Open-Meteo는 무료이며, API 키가 필요 없고, 안정성을 위해 좌표 기반 조회를 사용합니다
- 두바이 좌표: 위도 25.2048, 경도 55.2708
- 숫자 온도 값과 단위를 명확하게 반환하세요
- 호출자의 요청에 따라 섭씨와 화씨 모두 지원하세요
