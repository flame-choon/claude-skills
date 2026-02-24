# FMP Chart Endpoints

Base: `https://financialmodelingprep.com/stable`

---


## 엔드포인트 선택 가이드

Claude는 아래 기준으로 적절한 엔드포인트를 선택한다.

| 상황 | 사용할 엔드포인트 |
|------|----------------|
| 종가/거래량만 필요 (경량, 빠른 조회) | `GET /historical-price-eod/light` |
| OHLCV 전체 + 등락률/vwap 필요 | `GET /historical-price-eod/full` |
| 액면분할 미조정 원본 데이터 필요 | `GET /historical-price-eod/non-split-adjusted` |
| 배당 조정 가격 필요 (총수익률 계산 등) | `GET /historical-price-eod/dividend-adjusted` |
| 분/시간 단위 인트라데이 필요 | `GET /historical-chart/{interval}` |

> ⚠️ 날짜(`from`/`to`) 미지정 시 전체 이력이 반환되어 대용량 응답 발생 가능 → **항상 날짜 범위 지정 권장**

---


## EOD (End-of-Day) 차트

### 1. Stock Chart Light - 종가 + 거래량 (경량)

```
GET /historical-price-eod/light
```

**언제 사용:** 종가와 거래량만 필요할 때. 응답이 가볍고 빠르다.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 (예: AAPL) |
| from | string | ❌ | 시작일 YYYY-MM-DD |
| to | string | ❌ | 종료일 YYYY-MM-DD |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| date | string | 거래일 |
| price | float | 종가 (split-adjusted) |
| volume | integer | 거래량 |

```python
# GET /stable/historical-price-eod/light?symbol=AAPL&from=2024-01-01&to=2024-12-31
params = {"symbol": "AAPL", "from": "2024-01-01", "to": "2024-12-31"}

# 응답 예시
[{ "date": "2024-01-02", "price": 185.20, "volume": 79000000 }]
```


---

### 2. Stock Price and Volume — OHLCV 전체

```
GET /historical-price-eod/full
```

**언제 사용:** 시가/고가/저가/종가/거래량 전체가 필요하거나 등락률, VWAP이 필요할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| from | string | ❌ | 시작일 YYYY-MM-DD |
| to | string | ❌ | 종료일 YYYY-MM-DD |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| date | string | 거래일 (YYYY-MM-DD) |
| open | float | 시가 |
| high | float | 고가 |
| low | float | 저가 |
| close | float | 종가 |
| volume | integer | 거래량 |
| change | float | 전일 대비 변동액 |
| changePercent | float | 전일 대비 변동률 (%) |
| vwap | float | 거래량 가중 평균가 |

```python
# GET /stable/historical-price-eod/full?symbol=AAPL&from=2024-01-01&to=2024-12-31
params = {"symbol": "AAPL", "from": "2024-01-01", "to": "2024-12-31"}

# 응답 예시
[
    {
        "symbol": "AAPL",
        "date": "2026-02-23",
        "open": 263.49,
        "high": 269.43,
        "low": 263.38,
        "close": 266.18,
        "volume": 37007342,
        "change": 2.69,
        "changePercent": 1.02,
        "vwap": 265.62
    }
]
```

---

### 3. Unadjusted Stock Price — 액면분할 미조정

```
GET /historical-price-eod/non-split-adjusted
```

**언제 사용:** 액면분할 이전 원본 가격 기반 분석이 필요할 때. 분할 조정이 적용되지 않은 실제 거래 가격을 반환한다.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| from | string | ❌ | 시작일 YYYY-MM-DD |
| to | string | ❌ | 종료일 YYYY-MM-DD |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| date | string | 거래일 (YYYY-MM-DD) |
| adjOpen | float | 조정 시가 |
| adjHigh | float | 조정 고가 |
| adjLow | float | 조정 저가 |
| adjClose | float | 조정 종가 |
| volume | integer | 거래량 |

```python
# GET /stable/historical-price-eod/non-split-adjusted?symbol=AAPL&from=2024-01-01&to=2024-12-31
params = {"symbol": "AAPL", "from": "2024-01-01", "to": "2024-12-31"}

# 응답 예시
[
    {
        "symbol": "AAPL",
        "date": "2024-12-30",
        "adjOpen": 252.23,
        "adjHigh": 253.5,
        "adjLow": 250.75,
        "adjClose": 252.2,
        "volume": 35557542
    }
]
```

---

### 4. Dividend Adjusted Price — 배당 조정

```
GET /historical-price-eod/dividend-adjusted
```

**언제 사용:** 배당 재투자를 포함한 총수익률(Total Return) 계산이 필요할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| from | string | ❌ | 시작일 YYYY-MM-DD |
| to | string | ❌ | 종료일 YYYY-MM-DD |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| date | string | 거래일 (YYYY-MM-DD) |
| adjOpen | float | 배당 조정 시가 |
| adjHigh | float | 배당 조정 고가 |
| adjLow | float | 배당 조정 저가 |
| adjClose | float | 배당 조정 종가 |
| volume | integer | 거래량 |

```python
# GET /stable/historical-price-eod/dividend-adjusted?symbol=AAPL&from=2025-01-01&to=2025-12-31
params = {"symbol": "AAPL", "from": "2024-01-01", "to": "2024-12-31"}

# 응답 예시
[
    {
        "symbol": "AAPL",
        "date": "2025-12-30",
        "adjOpen": 272.56,
        "adjHigh": 273.83,
        "adjLow": 272.03,
        "adjClose": 272.83,
        "volume": 22139617
    }
]
```

---

## Intraday 차트

> **중요:** interval은 파라미터가 아닌 **URL path에 포함**된다.
> 지원 interval: `1min`, `5min`, `15min`, `30min`, `1hour`, `4hour`

```
GET /historical-chart/{interval}
```

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| from | string | ❌ | 시작 (YYYY-MM-DD 또는 YYYY-MM-DD HH:MM:SS) |
| to | string | ❌ | 종료 (YYYY-MM-DD 또는 YYYY-MM-DD HH:MM:SS) |
| nonadjusted | boolean | ❌ | `true`이면 미조정 가격 반환 (기본: false, 조정 가격) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| date | string | 기준 시각 (YYYY-MM-DD HH:MM:SS) |
| open | float | 시가 |
| high | float | 고가 |
| low | float | 저가 |
| close | float | 종가 |
| volume | integer | 거래량 |


**사용 예시**
```python
# 오늘 AAPL 5분봉 (날짜 미지정 = 최근 데이터)
# GET /stable/historical-chart/5min?symbol=AAPL
params = {"symbol": "AAPL"}

# 특정 날짜 범위 5분봉
# GET /stable/historical-chart/5min?symbol=AAPL&from=2026-02-24&to=2026-02-26
params = {"symbol": "AAPL", "from": "2026-02-24", "to": "2026-02-26"}

# 비조정 가격으로 조회
# GET /stable/historical-chart/5min?symbol=AAPL&nonadjusted=true
params = {"symbol": "AAPL", "nonadjusted": True}

# 응답 예시
[{"date": "2026-02-24 10:00:00", "open": 225.00, "high": 226.50, "low": 224.80, "close": 226.10, "volume": 1500000}]
```
