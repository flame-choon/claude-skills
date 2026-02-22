---
name: fmp-stock-api
description: Financial Modeling Prep (FMP) API reference. Use this skill when writing code that calls FMP API endpoints for stock quotes, historical price charts, company information, financial statements, or directory/search data. Prevents hallucinated endpoints and incorrect parameters.
---

# FMP API Reference

## Base URL (Stable API)
```
https://financialmodelingprep.com/stable
```

> ⚠️ **주의**: FMP는 `/api/v3/` 경로도 있지만 반드시 `/stable/` 경로를 사용할 것. 두 경로의 endpoint 명칭이 다름.

## Authentication

모든 요청에 API key 필수. 두 가지 방식 중 하나 사용:

```
# Header 방식
apikey: YOUR_API_KEY

# Query Parameter 방식 (다른 파라미터가 없을 때)
?apikey=YOUR_API_KEY

# Query Parameter 방식 (다른 파라미터가 있을 때)
&apikey=YOUR_API_KEY
```

환경변수 권장: `FMP_API_KEY`

## 카테고리별 Endpoint 문서

세부 endpoint 및 parameter는 각 카테고리 파일 참조:

| 카테고리 | 파일 | 주요 용도 |
|---------|------|---------|
| Charts | `chart/endpoints.md` | 주가 차트 (EOD, Intraday) |
| Quote | `quote/endpoints.md` | 실시간 시세, Batch 조회 |
| Company | `company/endpoints.md` | 기업 프로필, 임원, 시총 |
| Statements | `statements/endpoints.md` | 재무제표, 지표, 비율 |
| Directory | `directory/endpoints.md` | 종목 검색, 심볼 리스트 |

## 공통 파라미터 패턴

```python
# 단일 종목 조회
params = {
    "symbol": "AAPL",
    "apikey": os.environ["FMP_API_KEY"]
}

# 기간 필터 (날짜 포함 endpoint)
params = {
    "symbol": "AAPL",
    "from": "2024-01-01",   # YYYY-MM-DD
    "to": "2024-12-31",     # YYYY-MM-DD
    "apikey": os.environ["FMP_API_KEY"]
}

# 재무제표 기간 설정
params = {
    "symbol": "AAPL",
    "period": "annual",     # "annual" | "quarter"
    "limit": 5,
    "apikey": os.environ["FMP_API_KEY"]
}

# Batch 조회
params = {
    "symbols": "AAPL,MSFT,GOOGL",   # 복수형 "symbols" 주의
    "apikey": os.environ["FMP_API_KEY"]
}
```

## Python 호출 예시

```python
import os
import requests

BASE_URL = "https://financialmodelingprep.com/stable"
API_KEY = os.environ["FMP_API_KEY"]

def fmp_get(endpoint: str, params: dict) -> dict:
    params["apikey"] = API_KEY
    response = requests.get(f"{BASE_URL}/{endpoint}", params=params)
    response.raise_for_status()
    return response.json()
```

## 자주 발생하는 실수

| ❌ 잘못된 사용 | ✅ 올바른 사용 |
|------------|------------|
| `/api/v3/historical-price-full/AAPL` | `/stable/historical-price-eod/full?symbol=AAPL` |
| `/api/v3/quote/AAPL` | `/stable/quote?symbol=AAPL` |
| `symbol=AAPL,MSFT` (단수) | `symbols=AAPL,MSFT` (복수 - batch endpoint) |
| `/api/v3/income-statement/AAPL` | `/stable/income-statement?symbol=AAPL` |
| `interval=daily` | EOD endpoint 자체가 daily. intraday는 `/historical-chart/1min` 등 경로로 구분 |