# FMP Quote Endpoints

Base: `https://financialmodelingprep.com/stable`

---

## 엔드포인트 선택 가이드

| 상황 | 사용할 엔드포인트 |
|------|----------------|
| 단일 종목 실시간 상세 시세 | `GET /quote` |
| 단일 종목 실시간 가격/거래량만 (경량) | `GET /quote-short` |
| 장 마감 후 시간외 체결 내역 조회 | `GET /aftermarket-trade` |
| 장 마감 후 시간외 호가(bid/ask) 조회 | `GET /aftermarket-quote` |
| 기간별 등락률 조회 (1일~10년) | `GET /stock-price-change` |

> ⚠️ Aftermarket 관련 엔드포인트는 **미국 거래소(USA) 전용**.

---

## 단일 종목 시세

### 1. Stock Quote — 실시간 상세 시세

```
GET /quote
```

**언제 사용:** 단일 종목의 현재가, 등락, 거래량, 시가총액, PER 등 종합 시세 정보가 필요할 때. 가장 많은 정보를 담은 기본 시세 엔드포인트.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 (예: AAPL) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| name | string | 종목명 |
| price | float | 현재가 |
| changesPercentage | float | 전일 대비 등락률 (%) |
| change | float | 전일 대비 등락액 |
| dayLow | float | 당일 저가 |
| dayHigh | float | 당일 고가 |
| yearHigh | float | 52주 최고가 |
| yearLow | float | 52주 최저가 |
| marketCap | integer | 시가총액 (USD) |
| priceAvg50 | float | 50일 이동평균가 |
| priceAvg200 | float | 200일 이동평균가 |
| exchange | string | 거래소명 |
| volume | integer | 당일 거래량 |
| avgVolume | integer | 평균 거래량 |
| open | float | 당일 시가 |
| previousClose | float | 전일 종가 |
| timestamp | integer | 데이터 기준 시각 (Unix timestamp) |

```python
# GET /stable/quote?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[{
  "symbol": "AAPL",
  "name": "Apple Inc.",
  "price": 227.52,
  "changesPercentage": 1.23,
  "change": 2.75,
  "dayLow": 224.80,
  "dayHigh": 228.10,
  "yearHigh": 260.10,
  "yearLow": 164.08,
  "marketCap": 3412000000000,
  "priceAvg50": 230.15,
  "priceAvg200": 218.43,
  "volume": 52300000,
  "avgVolume": 61000000,
  "open": 225.00,
  "previousClose": 224.77,
  "timestamp": 1708900000
}]
```

---

### 2. Stock Quote Short — 실시간 경량 시세

```
GET /quote-short
```

**언제 사용:** 현재가와 거래량만 필요할 때. 응답이 매우 가볍고 빠르므로 대량 폴링이나 대시보드 실시간 업데이트에 적합.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| change | float | 전일 대비 등락액 |
| price | float | 현재가 |
| volume | integer | 당일 거래량 |

```python
# GET /stable/quote-short?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"price": 232.8,
		"change": 4.79,
		"volume": 44489128
	}
]
```

---

### 3. Aftermarket Trade — 시간외 체결 내역 🇺🇸

```
GET /aftermarket-trade
```

**언제 사용:** 정규 장 마감 후 시간외 거래에서 실제 체결된 가격, 수량, 시각을 조회할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| price | float | 시간외 체결가 |
| tradeSize | integer | 체결 수량 |
| timestamp | integer | 체결 시각 (Unix timestamp) |

```python
# GET /stable/aftermarket-trade?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[{"symbol": "AAPL", "price": 228.10, "tradeSize": 1500, "timestamp": 1708920000}]
```

---

### 4. Aftermarket Quote — 시간외 호가 🇺🇸

```
GET /aftermarket-quote
```

**언제 사용:** 시간외 거래의 매수호가(bid)와 매도호가(ask)를 조회할 때. 스프레드 파악이나 시간외 유동성 분석에 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| askPrice | float | 매도 최우선 호가 |
| bidPrice | float | 매수 최우선 호가 |
| askSize | integer | 매도 호가 수량 |
| bidSize | integer | 매수 호가 수량 |
| timestamp | integer | 호가 기준 시각 (Unix timestamp) |

```python
# GET /stable/aftermarket-quote?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"bidSize": 1,
		"bidPrice": 232.45,
		"askSize": 3,
		"askPrice": 232.64,
		"volume": 41647042,
		"timestamp": 1738715334311
	}
]

```

---

### 5. Stock Price Change — 기간별 등락률

```
GET /stock-price-change
```

**언제 사용:** 1일부터 10년까지 다양한 기간의 누적 등락률을 한 번에 조회할 때. 모멘텀 분석이나 장기 성과 비교에 유용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| 1D | float | 1일 등락률 (%) |
| 5D | float | 5일 등락률 (%) |
| 1M | float | 1개월 등락률 (%) |
| 3M | float | 3개월 등락률 (%) |
| 6M | float | 6개월 등락률 (%) |
| ytd | float | 연초 대비 등락률 (%) |
| 1Y | float | 1년 등락률 (%) |
| 3Y | float | 3년 등락률 (%) |
| 5Y | float | 5년 등락률 (%) |
| 10Y | float | 10년 등락률 (%) |
| max | float | 상장 이후 전체 등락률 (%) |

```python
# GET /stable/stock-price-change?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"1D": 2.1008,
		"5D": -2.45946,
		"1M": -4.33925,
		"3M": 4.86014,
		"6M": 5.88556,
		"ytd": -4.53147,
		"1Y": 24.04092,
		"3Y": 35.04264,
		"5Y": 192.05871,
		"10Y": 678.8558,
		"max": 181279.04168
	}
]

```
