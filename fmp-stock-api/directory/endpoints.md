# FMP Directory & Search Endpoints

Base: `https://financialmodelingprep.com/stable`

---

## 종목 검색

### Stock Symbol Search (티커로 검색)
```
GET /search-symbol
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| query | string | ✅ | 검색할 티커 심볼 (예: AAPL) |
| apikey | string | ✅ | API 키 |

```python
# GET /stable/search-symbol?query=AAPL
```

---

### Company Name Search (회사명으로 검색)
```
GET /search-name
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| query | string | ✅ | 검색할 회사명 (예: Apple) |
| apikey | string | ✅ | API 키 |

```python
# GET /stable/search-name?query=Apple
```

---

### CIK Search (CIK로 검색)
```
GET /search-cik
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| cik | string | ✅ | Central Index Key (예: 320193) |
| apikey | string | ✅ | API 키 |

---

### CUSIP Search
```
GET /search-cusip
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| cusip | string | ✅ | CUSIP 번호 (예: 037833100) |
| apikey | string | ✅ | API 키 |

---

### ISIN Search
```
GET /search-isin
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| isin | string | ✅ | ISIN 번호 (예: US0378331005) |
| apikey | string | ✅ | API 키 |

---

### Exchange Variants (상장 거래소 조회)
```
GET /search-exchange-variants
```
파라미터: `symbol` (✅), `apikey` (✅)

---

## 종목 스크리너

### Stock Screener
```
GET /company-screener
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| marketCapMoreThan | number | ❌ | 시총 이상 (USD) |
| marketCapLowerThan | number | ❌ | 시총 이하 (USD) |
| priceMoreThan | number | ❌ | 주가 이상 |
| priceLowerThan | number | ❌ | 주가 이하 |
| betaMoreThan | number | ❌ | 베타 이상 |
| betaLowerThan | number | ❌ | 베타 이하 |
| volumeMoreThan | number | ❌ | 거래량 이상 |
| volumeLowerThan | number | ❌ | 거래량 이하 |
| dividendMoreThan | number | ❌ | 배당수익률 이상 |
| dividendLowerThan | number | ❌ | 배당수익률 이하 |
| sector | string | ❌ | 섹터 (예: Technology, Healthcare) |
| industry | string | ❌ | 산업 |
| country | string | ❌ | 국가 코드 (예: US, KR) |
| exchange | string | ❌ | 거래소 (예: NASDAQ, NYSE) |
| limit | integer | ❌ | 결과 수 제한 |
| apikey | string | ✅ | API 키 |

```python
# 나스닥 상장 기술주 중 시총 100B 이상, 주가 100달러 이상
params = {
    "sector": "Technology",
    "exchange": "NASDAQ",
    "marketCapMoreThan": 100000000000,
    "priceMoreThan": 100
}
# GET /stable/company-screener?sector=Technology&exchange=NASDAQ&...
```

---

## 심볼 리스트 (Directory)

### Company Symbols List (전체 종목)
```
GET /stock-list
```
파라미터: `apikey` (✅)

응답: symbol, name, price, exchange, exchangeShortName, type 등 전종목 목록

---

### Financial Statement Symbols List (재무제표 있는 종목)
```
GET /financial-statement-symbol-list
```
파라미터: `apikey` (✅)

---

### ETF Symbol List
```
GET /etf-list
```
파라미터: `apikey` (✅)

---

### Actively Trading List (현재 거래 중인 종목)
```
GET /actively-trading-list
```
파라미터: `apikey` (✅)

---

### CIK List
```
GET /cik-list
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| page | integer | ❌ | 페이지 번호 (기본값: 0) |
| limit | integer | ❌ | 페이지당 결과 수 (기본값: 1000) |
| apikey | string | ✅ | API 키 |

---

### Symbol Changes List (심볼 변경 이력)
```
GET /symbol-change
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| date | string | ❌ | 특정 날짜 YYYY-MM-DD |
| apikey | string | ✅ | API 키 |

---

## 거래소 / 섹터 / 산업 목록

파라미터 없이 apikey만 필요:

| Endpoint | 설명 |
|---------|------|
| `GET /available-exchanges` | 지원 거래소 전체 목록 |
| `GET /available-sectors` | 섹터 목록 |
| `GET /available-industries` | 산업 목록 |
| `GET /available-countries` | 국가 목록 |

```python
# 사용 가능한 섹터 확인
# GET /stable/available-sectors?apikey=...
# 응답 예: ["Technology", "Healthcare", "Financial Services", ...]
```

---

## Earnings Transcript 목록

### Earnings Transcript List
```
GET /earnings-transcript-list
```
파라미터: `apikey` (✅)

응답: 실적발표 트랜스크립트가 있는 종목 목록과 트랜스크립트 수