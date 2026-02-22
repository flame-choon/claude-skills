# FMP Quote Endpoints

Base: `https://financialmodelingprep.com/stable`

---

## 단일 종목 Quote

### Stock Quote (전체 정보)
```
GET /quote
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 (예: AAPL) |
| apikey | string | ✅ | API 키 |

응답 필드: `symbol, name, price, changesPercentage, change, dayLow, dayHigh, yearHigh, yearLow, marketCap, priceAvg50, priceAvg200, exchange, volume, avgVolume, open, previousClose, eps, pe, earningsAnnouncement, sharesOutstanding, timestamp`

```python
# GET /stable/quote?symbol=AAPL
```

---

### Stock Quote Short (가격+변동+거래량만)
```
GET /quote-short
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

응답: `[{ "symbol": "AAPL", "price": 185.20, "volume": 50000000, "change": 1.5, "changesPercentage": 0.82 }]`

---

### Stock Price Change (기간별 변동률)
```
GET /stock-price-change
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

응답 필드: `symbol, 1D, 5D, 1M, 3M, 6M, ytd, 1Y, 3Y, 5Y, 10Y`

---

## After Market

### Aftermarket Trade
```
GET /aftermarket-trade
```
파라미터: `symbol` (✅), `apikey` (✅)

응답: `price, size, timestamp` (장외 체결 데이터)

---

### Aftermarket Quote
```
GET /aftermarket-quote
```
파라미터: `symbol` (✅), `apikey` (✅)

응답: `bidPrice, bidSize, askPrice, askSize, volume, timestamp`

---

## Batch Quote (복수 종목 동시 조회)

> ⚠️ Batch endpoint는 파라미터가 `symbols` (복수형) 임을 주의!

### Batch Quote
```
GET /batch-quote
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbols | string | ✅ | 콤마 구분 심볼 (예: AAPL,MSFT,GOOGL) |
| apikey | string | ✅ | API 키 |

```python
# GET /stable/batch-quote?symbols=AAPL,MSFT,GOOGL
```

---

### Batch Quote Short
```
GET /batch-quote-short
```
파라미터: `symbols` (✅, 콤마 구분), `apikey` (✅)

---

### Batch Aftermarket Trade
```
GET /batch-aftermarket-trade
```
파라미터: `symbols` (✅, 콤마 구분), `apikey` (✅)

---

### Batch Aftermarket Quote
```
GET /batch-aftermarket-quote
```
파라미터: `symbols` (✅, 콤마 구분), `apikey` (✅)

---

## Exchange 전체 Quote

### Exchange Stock Quotes (거래소 전종목)
```
GET /batch-exchange-quote
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| exchange | string | ✅ | 거래소명 (예: NASDAQ, NYSE, AMEX) |
| apikey | string | ✅ | API 키 |

```python
# GET /stable/batch-exchange-quote?exchange=NASDAQ
```

---

## 자산 유형별 전체 Quote

파라미터 없이 apikey만 필요:

| Endpoint | 설명 |
|---------|------|
| `GET /batch-etf-quotes` | ETF 전체 시세 |
| `GET /batch-mutualfund-quotes` | 뮤추얼펀드 전체 시세 |
| `GET /batch-commodity-quotes` | 원자재 전체 시세 |
| `GET /batch-crypto-quotes` | 암호화폐 전체 시세 |
| `GET /batch-forex-quotes` | 외환 전체 시세 |
| `GET /batch-index-quotes` | 지수 전체 시세 |

---

## Index/Forex/Crypto/Commodity - 같은 endpoint 사용

단일 quote는 `/quote`, `/quote-short` 동일하게 사용하고 symbol만 변경:
```python
# S&P 500 지수
params = {"symbol": "^GSPC"}

# 금 현물
params = {"symbol": "GCUSD"}

# 비트코인
params = {"symbol": "BTCUSD"}

# 유로/달러 환율
params = {"symbol": "EURUSD"}
```