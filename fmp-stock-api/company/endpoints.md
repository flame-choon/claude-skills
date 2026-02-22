# FMP Company Information Endpoints

Base: `https://financialmodelingprep.com/stable`

---

## 기업 프로필

### Company Profile
```
GET /profile
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 (예: AAPL) |
| apikey | string | ✅ | API 키 |

응답 필드: `symbol, companyName, currency, cik, isin, cusip, exchange, exchangeShortName, industry, website, description, ceo, sector, country, fullTimeEmployees, phone, address, city, state, zip, dcfDiff, dcf, image, ipoDate, defaultImage, isEtf, isActivelyTrading, isAdr, isFund`

```python
# GET /stable/profile?symbol=AAPL
```

---

### Company Profile by CIK
```
GET /profile-cik
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| cik | string | ✅ | Central Index Key (예: 320193) |
| apikey | string | ✅ | API 키 |

---

### Company Notes
```
GET /company-notes
```
파라미터: `symbol` (✅), `apikey` (✅)

---

## 임원 정보

### Key Executives
```
GET /key-executives
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

응답 필드: `title, name, pay, currencyPay, gender, yearBorn, titleSince`

---

### Executive Compensation
```
GET /governance-executive-compensation
```
파라미터: `symbol` (✅), `apikey` (✅)

---

### Executive Compensation Benchmark
```
GET /executive-compensation-benchmark
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| year | integer | ❌ | 연도 (예: 2023) |
| apikey | string | ✅ | API 키 |

---

## 시가총액

### Company Market Cap (현재)
```
GET /market-capitalization
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

---

### Batch Market Cap (복수 종목)
```
GET /market-capitalization-batch
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbols | string | ✅ | 콤마 구분 심볼 (예: AAPL,MSFT,GOOG) |
| apikey | string | ✅ | API 키 |

---

### Historical Market Cap
```
GET /historical-market-capitalization
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| limit | integer | ❌ | 결과 수 제한 (기본값: 데이터 전체) |
| from | string | ❌ | 시작일 YYYY-MM-DD |
| to | string | ❌ | 종료일 YYYY-MM-DD |
| apikey | string | ✅ | API 키 |

---

## 직원 수

### Company Employee Count
```
GET /employee-count
```
파라미터: `symbol` (✅), `apikey` (✅)

---

### Historical Employee Count
```
GET /historical-employee-count
```
파라미터: `symbol` (✅), `apikey` (✅)

---

## 주식 유통량

### Shares Float
```
GET /shares-float
```
파라미터: `symbol` (✅), `apikey` (✅)

응답 필드: `symbol, date, freeFloat, floatShares, outstandingShares, source`

---

### All Shares Float
```
GET /shares-float-all
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| page | integer | ❌ | 페이지 번호 (기본값: 0) |
| limit | integer | ❌ | 페이지당 결과 수 (기본값: 1000) |
| apikey | string | ✅ | API 키 |

---

## 동종업계 비교

### Stock Peers
```
GET /stock-peers
```
파라미터: `symbol` (✅), `apikey` (✅)

응답: `[{ "symbol": "AAPL", "peersList": ["MSFT", "GOOGL", ...] }]`

---

## 상장폐지 기업

### Delisted Companies
```
GET /delisted-companies
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| page | integer | ❌ | 페이지 번호 (기본값: 0) |
| limit | integer | ❌ | 페이지당 결과 수 (기본값: 100) |
| apikey | string | ✅ | API 키 |

---

## M&A

### Latest Mergers & Acquisitions
```
GET /mergers-acquisitions-latest
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| page | integer | ❌ | 페이지 번호 |
| limit | integer | ❌ | 결과 수 |
| apikey | string | ✅ | API 키 |

---

### Search Mergers & Acquisitions
```
GET /mergers-acquisitions-search
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| name | string | ✅ | 기업명 검색어 |
| apikey | string | ✅ | API 키 |