# FMP Financial Statements Endpoints

Base: `https://financialmodelingprep.com/stable`

---

## 공통 파라미터 (재무제표 계열 공통)

| 파라미터 | 타입 | 필수 | 값 | 설명 |
|---------|------|------|----|------|
| symbol | string | ✅ | - | 티커 심볼 |
| period | string | ❌ | `annual` \| `quarter` | 기간 (기본값: annual) |
| limit | integer | ❌ | 1~120 | 가져올 기간 수 (기본값: 5) |
| page | integer | ❌ | - | 페이지 번호 (기본값: 0) |
| apikey | string | ✅ | - | API 키 |

---

## 핵심 재무제표 3종

### Income Statement (손익계산서)
```
GET /income-statement
```
```python
params = {"symbol": "AAPL", "period": "annual", "limit": 5}
# GET /stable/income-statement?symbol=AAPL&period=annual&limit=5
```
주요 응답 필드: `revenue, costOfRevenue, grossProfit, grossProfitRatio, operatingExpenses, operatingIncome, ebitda, netIncome, eps, epsDiluted`

---

### Balance Sheet Statement (대차대조표)
```
GET /balance-sheet-statement
```
주요 응답 필드: `totalAssets, totalCurrentAssets, cashAndCashEquivalents, totalLiabilities, totalCurrentLiabilities, totalDebt, netDebt, totalStockholdersEquity`

---

### Cash Flow Statement (현금흐름표)
```
GET /cash-flow-statement
```
주요 응답 필드: `operatingCashFlow, capitalExpenditure, freeCashFlow, dividendsPaid, commonStockRepurchased, netCashUsedForInvestingActivities, netCashUsedProvidedByFinancingActivities`

---

## TTM (Trailing Twelve Months)

연간/분기 구분 없이 최근 12개월 합산:

| Endpoint | 설명 |
|---------|------|
| `GET /income-statement-ttm` | TTM 손익계산서 |
| `GET /balance-sheet-statement-ttm` | TTM 대차대조표 |
| `GET /cash-flow-statement-ttm` | TTM 현금흐름표 |

파라미터: `symbol` (✅), `apikey` (✅) - period, limit 불필요

---

## 최신 재무제표

### Latest Financial Statements
```
GET /latest-financial-statements
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| page | integer | ❌ | 페이지 번호 (기본값: 0) |
| limit | integer | ❌ | 결과 수 (기본값: 250) |
| apikey | string | ✅ | API 키 |

---

## 핵심 지표 & 비율

### Key Metrics (핵심 재무지표)
```
GET /key-metrics
```
파라미터: `symbol` (✅), `period` (annual|quarter), `limit`, `apikey` (✅)

주요 응답 필드: `revenuePerShare, netIncomePerShare, operatingCashFlowPerShare, freeCashFlowPerShare, cashPerShare, bookValuePerShare, peRatio, pbRatio, evToSales, evToOperatingCashFlow, earningsYield, freeCashFlowYield, debtToEquity, roe, roa, roic`

---

### Key Metrics TTM
```
GET /key-metrics-ttm
```
파라미터: `symbol` (✅), `apikey` (✅)

---

### Financial Ratios (재무비율)
```
GET /ratios
```
파라미터: `symbol` (✅), `period` (annual|quarter), `limit`, `apikey` (✅)

주요 응답 필드: `grossProfitMargin, operatingProfitMargin, netProfitMargin, returnOnEquity, returnOnAssets, currentRatio, quickRatio, debtEquityRatio, priceToBookRatio, priceEarningsRatio, priceToSalesRatio, dividendYield, payoutRatio`

---

### Financial Ratios TTM
```
GET /ratios-ttm
```
파라미터: `symbol` (✅), `apikey` (✅)

---

## 기업가치 지표

### Enterprise Values
```
GET /enterprise-values
```
파라미터: `symbol` (✅), `period` (annual|quarter), `limit`, `apikey` (✅)

응답 필드: `enterpriseValue, addTotalDebt, minusCashAndCashEquivalents, numberOfShares, marketCapitalization, stockPrice`

---

### Financial Scores (Altman Z-Score, Piotroski)
```
GET /financial-scores
```
파라미터: `symbol` (✅), `apikey` (✅)

응답 필드: `symbol, altmanZScore, piotroskiScore, workingCapital, totalAssets, retainedEarnings, ebit, marketCap, totalLiabilities, revenue`

---

### Owner Earnings
```
GET /owner-earnings
```
파라미터: `symbol` (✅), `apikey` (✅)

---

## 성장률

### Income Statement Growth
```
GET /income-statement-growth
```
파라미터: `symbol` (✅), `period` (annual|quarter), `limit`, `apikey` (✅)

---

### Balance Sheet Statement Growth
```
GET /balance-sheet-statement-growth
```
파라미터 동일

---

### Cashflow Statement Growth
```
GET /cash-flow-statement-growth
```
파라미터 동일

---

### Financial Statement Growth (통합)
```
GET /financial-growth
```
파라미터 동일

---

## 10-K 보고서

### Financial Reports Dates
```
GET /financial-reports-dates
```
파라미터: `symbol` (✅), `apikey` (✅)

---

### Financial Reports Form 10-K (JSON)
```
GET /financial-reports-json
```
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| year | integer | ✅ | 연도 (예: 2022) |
| period | string | ✅ | `FY` \| `Q1` \| `Q2` \| `Q3` \| `Q4` |
| apikey | string | ✅ | API 키 |

---

### Financial Reports Form 10-K (XLSX)
```
GET /financial-reports-xlsx
```
파라미터 동일 (다운로드 파일 반환)

---

## 매출 세분화

### Revenue Product Segmentation
```
GET /revenue-product-segmentation
```
파라미터: `symbol` (✅), `apikey` (✅)

---

### Revenue Geographic Segmentation
```
GET /revenue-geographic-segmentation
```
파라미터: `symbol` (✅), `apikey` (✅)

---

## As-Reported (원본 공시 기준)

실제 SEC 공시 숫자 그대로 (FMP 표준화 적용 전):

| Endpoint | 설명 |
|---------|------|
| `GET /income-statement-as-reported` | 원본 손익계산서 |
| `GET /balance-sheet-statement-as-reported` | 원본 대차대조표 |
| `GET /cash-flow-statement-as-reported` | 원본 현금흐름표 |
| `GET /financial-statement-full-as-reported` | 원본 통합 재무제표 |

파라미터: `symbol` (✅), `period` (annual|quarter), `limit`, `apikey` (✅)