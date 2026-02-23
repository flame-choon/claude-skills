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

## 의존성 설정

FMP API 관련 코드 작성 시 아래 패키지들이 필요하다. **패키지 설치는 하지 않으며**, 프로젝트 방식에 따라 의존성 정의만 추가한다.

### Poetry 기반 프로젝트 (`pyproject.toml` 존재)

`pyproject.toml`의 `[tool.poetry.dependencies]` 섹션에 추가:

```toml
[tool.poetry.dependencies]
python = "^3.9"
requests = "^2.31.0"
pandas = "^2.0.0"
numpy = "^1.24.0"
matplotlib = "^3.7.0"
plotly = "^5.18.0"
```

### 일반 프로젝트 (`requirements.txt` 존재)

`requirements.txt`에 추가:

```
requests>=2.31.0
pandas>=2.0.0
numpy>=1.24.0
matplotlib>=3.7.0
plotly>=5.18.0
```

> 💡 **판단 기준**: `pyproject.toml` 파일이 프로젝트 루트에 있으면 Poetry 기반. 없고 `requirements.txt`가 있으면 해당 파일에 추가. 둘 다 없으면 `requirements.txt`를 새로 생성하여 추가.

---

## 환경변수 설정 (.env)

### .env.example 생성/수정

프로젝트 루트에 `.env.example` 파일에 아래 내용을 포함한다:

```dotenv
# Financial Modeling Prep API
# https://site.financialmodelingprep.com/developer/docs 에서 발급
FMP_API_KEY=your_fmp_api_key_here
```

### 안내 사항

1. **`.env.example`은 Git에 커밋**한다 — 팀원들이 어떤 환경변수가 필요한지 알 수 있도록
2. **`.env`는 절대 Git에 커밋하지 않는다** — `.gitignore`에 `.env` 가 포함되어 있는지 확인
3. 실제 사용 시 `.env.example`을 복사해 `.env`로 만들고 실제 키 값을 입력:
   ```bash
   cp .env.example .env
   # .env 파일을 열어 FMP_API_KEY 값을 실제 키로 교체
   ```
4. 코드에서 환경변수 로딩은 `python-dotenv` 사용 권장:
   ```python
   from dotenv import load_dotenv
   import os

   load_dotenv()
   API_KEY = os.environ["FMP_API_KEY"]
   ```

> ⚠️ `python-dotenv` 사용 시 의존성에도 추가 필요: Poetry면 `python-dotenv = "^1.0.0"`, requirements.txt면 `python-dotenv>=1.0.0`

---

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