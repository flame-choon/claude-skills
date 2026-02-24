# FMP Chart Endpoints

Base: `https://financialmodelingprep.com/stable`

---

## EOD (End-of-Day) 차트

### Stock Chart Light (날짜+종가+거래량만)
```
GET /historical-price-eod/light
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 (예: AAPL) |
| from | string | ❌ | 시작일 YYYY-MM-DD |
| to | string | ❌ | 종료일 YYYY-MM-DD |
| apikey | string | ✅ | API 키 |

```python
# 예시
params = {"symbol": "AAPL", "from": "2024-01-01", "to": "2024-12-31"}
# GET /stable/historical-price-eod/light?symbol=AAPL&from=2024-01-01&to=2024-12-31
```
응답: `[{ "date": "2024-01-02", "price": 185.20, "volume": 79000000 }]`

---

### Stock Price and Volume (OHLCV 전체)
```
GET /historical-price-eod/full
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| from | string | ❌ | 시작일 YYYY-MM-DD |
| to | string | ❌ | 종료일 YYYY-MM-DD |
| apikey | string | ✅ | API 키 |

응답 필드: `date, open, high, low, close, volume, change, changePercent, vwap`

---

### Unadjusted Stock Price (액면분할 미조정)
```
GET /historical-price-eod/non-split-adjusted
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| from | string | ❌ | 시작일 YYYY-MM-DD |
| to | string | ❌ | 종료일 YYYY-MM-DD |
| apikey | string | ✅ | API 키 |

응답 필드: `date, adjOpen, adjHigh, adjLow, adjClose, volume`

---

### Dividend Adjusted Price (배당 조정)
```
GET /historical-price-eod/dividend-adjusted
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| from | string | ❌ | 시작일 YYYY-MM-DD |
| to | string | ❌ | 종료일 YYYY-MM-DD |
| apikey | string | ✅ | API 키 |

응답 필드: `date, adjOpen, adjHigh, adjLow, adjClose, volume`

---

## Intraday 차트

> ⚠️ 인트라데이는 endpoint path에 interval을 포함하는 방식. 파라미터로 interval을 넣는 것이 아님!

### 1분봉
```
GET /historical-chart/1min
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| from | string | ❌ | 시작 YYYY-MM-DD 또는 YYYY-MM-DD HH:MM:SS |
| to | string | ❌ | 종료 YYYY-MM-DD 또는 YYYY-MM-DD HH:MM:SS |
| nonadjusted | boolean | ❌ | 조정주가(false) 또는 비조정주가 선택(true) |
| apikey | string | ✅ | API 키 |

### 5분봉
```
GET /historical-chart/5min
```
파라미터 동일

### 15분봉
```
GET /historical-chart/15min
```
파라미터 동일

### 30분봉
```
GET /historical-chart/30min
```
파라미터 동일

### 1시간봉
```
GET /historical-chart/1hour
```
파라미터 동일

### 4시간봉
```
GET /historical-chart/4hour
```
파라미터 동일

---

## Intraday 예시

```python
# 오늘 AAPL 5분봉
# GET /stable/historical-chart/5min?symbol=AAPL
params = {
    "symbol": "AAPL"
}

# 특정 시간대
# GET /stable/historical-chart/5min?symbol=AAPL&from=2026-02-24&to=2026-02-26
params = {
    "symbol": "AAPL",
    "from": "2026-02-24",
    "to": "2026-02-26"
}

# 비조정주가 데이터로 조회
# GET /stable/historical-chart/5min?symbol=AAPL&nonadjusted=true
params = {
    "symbol": "AAPL",
    "nonadjusted": true
}


```

응답 필드: `date, open, high, low, close, volume`

---

## 같은 endpoint로 Index/Forex/Crypto/Commodity 차트도 가능

symbol만 바꾸면 동일 endpoint 사용:
- Index: `symbol=^GSPC` (S&P500), `symbol=^IXIC` (NASDAQ)
- Forex: `symbol=EURUSD`
- Crypto: `symbol=BTCUSD`
- Commodity: `symbol=GCUSD` (금), `symbol=CLUSD` (원유)