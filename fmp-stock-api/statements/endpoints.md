# FMP Financial Statements Endpoints

Base: `https://financialmodelingprep.com/stable`

---

## 엔드포인트 선택 가이드

| 상황 | 사용할 엔드포인트 |
|------|----------------|
| 손익계산서 (매출, 영업이익, 순이익 등) | `GET /income-statement` |
| 대차대조표 (자산, 부채, 자본 등) | `GET /balance-sheet-statement` |
| 현금흐름표 (영업/투자/재무 현금흐름) | `GET /cash-flow-statement` |
| PER, ROE 등 핵심 재무 지표 | `GET /key-metrics` |
| 수익성/유동성/효율성 비율 지표 | `GET /ratios` |
| 핵심 지표 TTM | `GET /key-metrics-ttm` |
| 재무 비율 TTM | `GET /ratios-ttm` |
| Altman Z-Score, Piotroski Score | `GET /financial-scores` |
| 주주 관점 실질 수익 (Owner Earnings) | `GET /owner-earnings` |
| 기업가치 (EV = 시총 + 부채) | `GET /enterprise-values` |
| 손익계산서 항목별 성장률 | `GET /income-statement-growth` |
| 대차대조표 항목별 성장률 | `GET /balance-sheet-statement-growth` |
| 현금흐름표 항목별 성장률 | `GET /cash-flow-statement-growth` |
| 전체 재무제표 통합 성장률 | `GET /financial-growth` |
| 재무보고서 제출 가능 날짜 목록 | `GET /financial-reports-dates` |
| 10-K 연간보고서 JSON | `GET /financial-reports-json` |
| 제품/서비스별 매출 분류 | `GET /revenue-product-segmentation` |
| 지역별 매출 분류 | `GET /revenue-geographic-segmentation` |
| SEC 원문 기준 손익계산서 | `GET /income-statement-as-reported` |
| SEC 원문 기준 대차대조표 | `GET /balance-sheet-statement-as-reported` |
| SEC 원문 기준 현금흐름표 | `GET /cash-flow-statement-as-reported` |
| SEC 원문 기준 전체 재무제표 통합 | `GET /financial-statement-full-as-reported` |

> ⚠️ `period` 파라미터 미지정 시 기본값은 `annual`. 분기 데이터가 필요하면 반드시 `period=quarter` 명시.
> ⚠️ As-Reported 계열 엔드포인트는 FMP 표준화 없이 **SEC 원문 그대로** 반환하므로 필드명이 기업마다 다를 수 있음.

---

## 엔드포인트 분류 요약

| 구분 | 표준화 | TTM | 성장률 | SEC 원문 |
|------|--------|-----|--------|---------|
| 손익계산서 | `/income-statement` | `/income-statement-ttm` | `/income-statement-growth` | `/income-statement-as-reported` |
| 대차대조표 | `/balance-sheet-statement` | `/balance-sheet-statement-ttm` | `/balance-sheet-statement-growth` | `/balance-sheet-statement-as-reported` |
| 현금흐름표 | `/cash-flow-statement` | `/cash-flow-statement-ttm` | `/cash-flow-statement-growth` | `/cash-flow-statement-as-reported` |
| 핵심 지표 | `/key-metrics` | `/key-metrics-ttm` | - | - |
| 재무 비율 | `/ratios` | `/ratios-ttm` | - | - |
| 전체 통합 | - | - | `/financial-growth` | `/financial-statement-full-as-reported` |

---

## 기본 재무제표 (표준화)

### 1. Income Statement — 손익계산서

```
GET /income-statement
```

**언제 사용:** 매출, 영업이익, 순이익 등 수익성 분석이 필요할 때. FMP가 표준화한 형식으로 기업 간 비교에 용이.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 (예: AAPL) |
| period | string | ❌ | `annual`(기본) 또는 `quarter`,`Q1`,`Q2`,`Q3`,`Q4`,`FY` |
| limit | integer | ❌ | 반환할 기간 수 (기본: 5) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| date | string | 보고 기준일 (YYYY-MM-DD) |
| symbol | string | 티커 심볼 |
| reportedCurrency | string | 보고 통화 (예: USD) |
| cik | string | SEC CIK 번호 |
| filingDate | string | SEC 제출일 (YYYY-MM-DD) |
| acceptedDate | string | SEC 수락일시 (YYYY-MM-DD HH:MM:SS) |
| fiscalYear | string | 회계연도 (예: 2024) |
| period | string | 보고 기간 (FY, Q1~Q4) |
| cashAndCashEquivalents | integer | 현금 및 현금성 자산 |
| shortTermInvestments | integer | 단기 투자자산 |
| cashAndShortTermInvestments | integer | 현금 및 단기 투자자산 합계 |
| netReceivables | integer | 순 매출채권 |
| accountsReceivables | integer | 매출채권 |
| otherReceivables | integer | 기타 수취채권 |
| inventory | integer | 재고자산 |
| prepaids | integer | 선급비용 |
| otherCurrentAssets | integer | 기타 유동자산 |
| totalCurrentAssets | integer | 유동자산 합계 |
| propertyPlantEquipmentNet | integer | 유형자산 순액 (토지·건물·설비) |
| goodwill | integer | 영업권 |
| intangibleAssets | integer | 무형자산 |
| goodwillAndIntangibleAssets | integer | 영업권 및 무형자산 합계 |
| longTermInvestments | integer | 장기 투자자산 |
| taxAssets | integer | 이연법인세 자산 |
| otherNonCurrentAssets | integer | 기타 비유동자산 |
| totalNonCurrentAssets | integer | 비유동자산 합계 |
| otherAssets | integer | 기타 자산 |
| totalAssets | integer | 자산 총계 |
| totalPayables | integer | 매입채무 합계 |
| accountPayables | integer | 매입채무 |
| otherPayables | integer | 기타 지급채무 |
| accruedExpenses | integer | 미지급비용 |
| shortTermDebt | integer | 단기차입금 |
| capitalLeaseObligationsCurrent | integer | 유동 금융리스 부채 |
| taxPayables | integer | 미지급법인세 |
| deferredRevenue | integer | 이연수익 (유동) |
| otherCurrentLiabilities | integer | 기타 유동부채 |
| totalCurrentLiabilities | integer | 유동부채 합계 |
| longTermDebt | integer | 장기차입금 |
| deferredRevenueNonCurrent | integer | 이연수익 (비유동) |
| deferredTaxLiabilitiesNonCurrent | integer | 이연법인세 부채 (비유동) |
| otherNonCurrentLiabilities | integer | 기타 비유동부채 |
| totalNonCurrentLiabilities | integer | 비유동부채 합계 |
| otherLiabilities | integer | 기타 부채 |
| capitalLeaseObligations | integer | 금융리스 부채 (총계) |
| totalLiabilities | integer | 부채 총계 |
| treasuryStock | integer | 자기주식 |
| preferredStock | integer | 우선주 |
| commonStock | integer | 보통주 (자본금 + 주식발행초과금 포함) |
| retainedEarnings | integer | 이익잉여금 (결손금) |
| additionalPaidInCapital | integer | 주식발행초과금 |
| accumulatedOtherComprehensiveIncomeLoss | integer | 기타포괄손익누계액 |
| otherTotalStockholdersEquity | integer | 기타 자본 항목 |
| totalStockholdersEquity | integer | 주주지분 합계 |
| totalEquity | integer | 자본 총계 |
| minorityInterest | integer | 비지배주주지분 |
| totalLiabilitiesAndTotalEquity | integer | 부채 및 자본 총계 |
| totalInvestments | integer | 총 투자자산 (단기 + 장기) |
| totalDebt | integer | 총 차입금 (단기 + 장기) |
| netDebt | integer | 순차입금 (총차입금 - 현금성자산) |

```python
# GET /stable/income-statement?symbol=AAPL&period=annual&limit=5
params = {"symbol": "AAPL", "period": "annual", "limit": 5}

# 응답 예시
[
	{
		"date": "2024-09-28",
		"symbol": "AAPL",
		"reportedCurrency": "USD",
		"cik": "0000320193",
		"filingDate": "2024-11-01",
		"acceptedDate": "2024-11-01 06:01:36",
		"fiscalYear": "2024",
		"period": "FY",
		"cashAndCashEquivalents": 29943000000,
		"shortTermInvestments": 35228000000,
		"cashAndShortTermInvestments": 65171000000,
		"netReceivables": 66243000000,
		"accountsReceivables": 33410000000,
		"otherReceivables": 32833000000,
		"inventory": 7286000000,
		"prepaids": 0,
		"otherCurrentAssets": 14287000000,
		"totalCurrentAssets": 152987000000,
		"propertyPlantEquipmentNet": 45680000000,
		"goodwill": 0,
		"intangibleAssets": 0,
		"goodwillAndIntangibleAssets": 0,
		"longTermInvestments": 91479000000,
		"taxAssets": 19499000000,
		"otherNonCurrentAssets": 55335000000,
		"totalNonCurrentAssets": 211993000000,
		"otherAssets": 0,
		"totalAssets": 364980000000,
		"totalPayables": 95561000000,
		"accountPayables": 68960000000,
		"otherPayables": 26601000000,
		"accruedExpenses": 0,
		"shortTermDebt": 20879000000,
		"capitalLeaseObligationsCurrent": 1632000000,
		"taxPayables": 26601000000,
		"deferredRevenue": 8249000000,
		"otherCurrentLiabilities": 50071000000,
		"totalCurrentLiabilities": 176392000000,
		"longTermDebt": 85750000000,
		"deferredRevenueNonCurrent": 10798000000,
		"deferredTaxLiabilitiesNonCurrent": 0,
		"otherNonCurrentLiabilities": 35090000000,
		"totalNonCurrentLiabilities": 131638000000,
		"otherLiabilities": 0,
		"capitalLeaseObligations": 12430000000,
		"totalLiabilities": 308030000000,
		"treasuryStock": 0,
		"preferredStock": 0,
		"commonStock": 83276000000,
		"retainedEarnings": -19154000000,
		"additionalPaidInCapital": 0,
		"accumulatedOtherComprehensiveIncomeLoss": -7172000000,
		"otherTotalStockholdersEquity": 0,
		"totalStockholdersEquity": 56950000000,
		"totalEquity": 56950000000,
		"minorityInterest": 0,
		"totalLiabilitiesAndTotalEquity": 364980000000,
		"totalInvestments": 126707000000,
		"totalDebt": 106629000000,
		"netDebt": 76686000000
	}
]
```

---

### 2. Balance Sheet Statement — 대차대조표

```
GET /balance-sheet-statement
```

**언제 사용:** 자산, 부채, 자본 구조를 분석할 때. 재무 건전성 및 레버리지 파악에 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter`,`Q1`,`Q2`,`Q3`,`Q4`,`FY` |
| limit | integer | ❌ | 반환할 기간 수 (기본: 5) |
| apikey | string | ✅ | API 키 |

**응답 필드:** `/income-statement`과 동일

```python
# GET /stable/balance-sheet-statement?symbol=AAPL&period=annual&limit=5
params = {"symbol": "AAPL", "period": "annual", "limit": 5}

# 응답 예시
[
	{
		"date": "2024-09-28",
		"symbol": "AAPL",
		"reportedCurrency": "USD",
		"cik": "0000320193",
		"filingDate": "2024-11-01",
		"acceptedDate": "2024-11-01 06:01:36",
		"fiscalYear": "2024",
		"period": "FY",
		"cashAndCashEquivalents": 29943000000,
		"shortTermInvestments": 35228000000,
		"cashAndShortTermInvestments": 65171000000,
		"netReceivables": 66243000000,
		"accountsReceivables": 33410000000,
		"otherReceivables": 32833000000,
		"inventory": 7286000000,
		"prepaids": 0,
		"otherCurrentAssets": 14287000000,
		"totalCurrentAssets": 152987000000,
		"propertyPlantEquipmentNet": 45680000000,
		"goodwill": 0,
		"intangibleAssets": 0,
		"goodwillAndIntangibleAssets": 0,
		"longTermInvestments": 91479000000,
		"taxAssets": 19499000000,
		"otherNonCurrentAssets": 55335000000,
		"totalNonCurrentAssets": 211993000000,
		"otherAssets": 0,
		"totalAssets": 364980000000,
		"totalPayables": 95561000000,
		"accountPayables": 68960000000,
		"otherPayables": 26601000000,
		"accruedExpenses": 0,
		"shortTermDebt": 20879000000,
		"capitalLeaseObligationsCurrent": 1632000000,
		"taxPayables": 26601000000,
		"deferredRevenue": 8249000000,
		"otherCurrentLiabilities": 50071000000,
		"totalCurrentLiabilities": 176392000000,
		"longTermDebt": 85750000000,
		"deferredRevenueNonCurrent": 10798000000,
		"deferredTaxLiabilitiesNonCurrent": 0,
		"otherNonCurrentLiabilities": 35090000000,
		"totalNonCurrentLiabilities": 131638000000,
		"otherLiabilities": 0,
		"capitalLeaseObligations": 12430000000,
		"totalLiabilities": 308030000000,
		"treasuryStock": 0,
		"preferredStock": 0,
		"commonStock": 83276000000,
		"retainedEarnings": -19154000000,
		"additionalPaidInCapital": 0,
		"accumulatedOtherComprehensiveIncomeLoss": -7172000000,
		"otherTotalStockholdersEquity": 0,
		"totalStockholdersEquity": 56950000000,
		"totalEquity": 56950000000,
		"minorityInterest": 0,
		"totalLiabilitiesAndTotalEquity": 364980000000,
		"totalInvestments": 126707000000,
		"totalDebt": 106629000000,
		"netDebt": 76686000000
	}
]
```

---

### 3. Cash Flow Statement — 현금흐름표

```
GET /cash-flow-statement
```

**언제 사용:** 영업활동, 투자활동, 재무활동별 현금흐름을 분석할 때. 실질적인 현금 창출 능력 평가에 핵심.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter`,`Q1`,`Q2`,`Q3`,`Q4`,`FY` |
| limit | integer | ❌ | 반환할 기간 수 (기본: 5) |
| apikey | string | ✅ | API 키 |

**응답 필드** `/income-statement`과 동일

```python
# GET /stable/cash-flow-statement?symbol=AAPL&period=annual&limit=5
params = {"symbol": "AAPL", "period": "annual", "limit": 5}

# 응답 예시
[
	{
		"date": "2024-09-28",
		"symbol": "AAPL",
		"reportedCurrency": "USD",
		"cik": "0000320193",
		"filingDate": "2024-11-01",
		"acceptedDate": "2024-11-01 06:01:36",
		"fiscalYear": "2024",
		"period": "FY",
		"netIncome": 93736000000,
		"depreciationAndAmortization": 11445000000,
		"deferredIncomeTax": 0,
		"stockBasedCompensation": 11688000000,
		"changeInWorkingCapital": 3651000000,
		"accountsReceivables": -5144000000,
		"inventory": -1046000000,
		"accountsPayables": 6020000000,
		"otherWorkingCapital": 3821000000,
		"otherNonCashItems": -2266000000,
		"netCashProvidedByOperatingActivities": 118254000000,
		"investmentsInPropertyPlantAndEquipment": -9447000000,
		"acquisitionsNet": 0,
		"purchasesOfInvestments": -48656000000,
		"salesMaturitiesOfInvestments": 62346000000,
		"otherInvestingActivities": -1308000000,
		"netCashProvidedByInvestingActivities": 2935000000,
		"netDebtIssuance": -5998000000,
		"longTermNetDebtIssuance": -9958000000,
		"shortTermNetDebtIssuance": 3960000000,
		"netStockIssuance": -94949000000,
		"netCommonStockIssuance": -94949000000,
		"commonStockIssuance": 0,
		"commonStockRepurchased": -94949000000,
		"netPreferredStockIssuance": 0,
		"netDividendsPaid": -15234000000,
		"commonDividendsPaid": -15234000000,
		"preferredDividendsPaid": 0,
		"otherFinancingActivities": -5802000000,
		"netCashProvidedByFinancingActivities": -121983000000,
		"effectOfForexChangesOnCash": 0,
		"netChangeInCash": -794000000,
		"cashAtEndOfPeriod": 29943000000,
		"cashAtBeginningOfPeriod": 30737000000,
		"operatingCashFlow": 118254000000,
		"capitalExpenditure": -9447000000,
		"freeCashFlow": 108807000000,
		"incomeTaxesPaid": 26102000000,
		"interestPaid": 0
	}
]
```

---

## 재무 지표 및 비율

### 4. Key Metrics — 핵심 재무 지표

```
GET /key-metrics
```

**언제 사용:** PER, ROE, ROA, 부채비율 등 투자 판단에 자주 쓰이는 핵심 지표를 한 번에 조회할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter`,`Q1`,`Q2`,`Q3`,`Q4`,`FY` |
| limit | integer | ❌ | 반환할 기간 수 (기본: 5) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| date | string | 보고 기준일 (YYYY-MM-DD) |
| fiscalYear | string | 회계연도 (예: 2024) |
| period | string | 보고 기간 (FY, Q1~Q4) |
| reportedCurrency | string | 보고 통화 (예: USD) |
| marketCap | integer | 시가총액 |
| enterpriseValue | integer | 기업가치 (EV) |
| evToSales | float | EV / 매출액 |
| evToOperatingCashFlow | float | EV / 영업현금흐름 |
| evToFreeCashFlow | float | EV / 잉여현금흐름 |
| evToEBITDA | float | EV / EBITDA |
| netDebtToEBITDA | float | 순차입금 / EBITDA |
| currentRatio | float | 유동비율 (유동자산 / 유동부채) |
| incomeQuality | float | 이익의 질 (영업현금흐름 / 순이익) |
| grahamNumber | float | 그레이엄 넘버 (내재가치 추정값) |
| grahamNetNet | float | 그레이엄 순순자산가치 (NCAV 기반) |
| taxBurden | float | 세금 부담률 (순이익 / 세전이익) |
| interestBurden | float | 이자 부담률 (세전이익 / EBIT) |
| workingCapital | integer | 순운전자본 (유동자산 - 유동부채) |
| investedCapital | integer | 투하자본 |
| returnOnAssets | float | 총자산이익률 (ROA) |
| operatingReturnOnAssets | float | 영업기준 총자산이익률 |
| returnOnTangibleAssets | float | 유형자산이익률 |
| returnOnEquity | float | 자기자본이익률 (ROE) |
| returnOnInvestedCapital | float | 투하자본이익률 (ROIC) |
| returnOnCapitalEmployed | float | 사용자본이익률 (ROCE) |
| earningsYield | float | 이익수익률 (EPS / 주가)

```python
# GET /stable/key-metrics?symbol=AAPL&period=annual&limit=5
params = {"symbol": "AAPL", "period": "annual", "limit": 5}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"date": "2024-09-28",
		"fiscalYear": "2024",
		"period": "FY",
		"reportedCurrency": "USD",
		"marketCap": 3495160329570,
		"enterpriseValue": 3571846329570,
		"evToSales": 9.134339201273542,
		"evToOperatingCashFlow": 30.204866893043786,
		"evToFreeCashFlow": 32.82735788662494,
		"evToEBITDA": 26.524727497716487,
		"netDebtToEBITDA": 0.5694744580836323,
		"currentRatio": 0.8673125765340832,
		"incomeQuality": 1.2615643936161134,
		"grahamNumber": 22.587017267616833,
		"grahamNetNet": -12.352478525015636,
		"taxBurden": 0.7590881483581001,
		"interestBurden": 1.0021831580314244,
		"workingCapital": -23405000000,
		"investedCapital": 22275000000,
		"returnOnAssets": 0.25682503150857583,
		"operatingReturnOnAssets": 0.3434290787011036,
		"returnOnTangibleAssets": 0.25682503150857583,
		"returnOnEquity": 1.6459350307287095,
		"returnOnInvestedCapital": 0.4430708117427921,
		"returnOnCapitalEmployed": 0.6533607652660827,
		"earningsYield": 0.026818798327209237,
		"freeCashFlowYield": 0.03113076074921754,
		"capexToOperatingCashFlow": 0.07988736110406414,
		"capexToDepreciation": 0.8254259501965924,
		"capexToRevenue": 0.02415896275269477,
		"salesGeneralAndAdministrativeToRevenue": 0,
		"researchAndDevelopementToRevenue": 0.08022299794136074,
		"stockBasedCompensationToRevenue": 0.02988990755303234,
		"intangiblesToTotalAssets": 0,
		"averageReceivables": 63614000000,
		"averagePayables": 65785500000,
		"averageInventory": 6808500000,
		"daysOfSalesOutstanding": 61.83255974529134,
		"daysOfPayablesOutstanding": 119.65847721913745,
		"daysOfInventoryOutstanding": 12.642570548414087,
		"operatingCycle": 74.47513029370543,
		"cashConversionCycle": -45.18334692543202,
		"freeCashFlowToEquity": 32121000000,
		"freeCashFlowToFirm": 117192805288.09166,
		"tangibleAssetValue": 56950000000,
		"netCurrentAssetValue": -155043000000
	}
]
```

---

### 5. Financial Ratios — 재무 비율

```
GET /ratios
```

**언제 사용:** 수익성, 유동성, 효율성, 레버리지 등 세부 재무 비율을 심층 분석할 때. Key Metrics보다 더 많은 비율 지표를 제공.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter`,`Q1`,`Q2`,`Q3`,`Q4`,`FY` |
| limit | integer | ❌ | 반환할 기간 수 (기본: 5) |
| apikey | string | ✅ | API 키 |

**응답 필드 (주요 항목)**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| date | string | 보고 기준일 (YYYY-MM-DD) |
| fiscalYear | string | 회계연도 (예: 2024) |
| period | string | 보고 기간 (FY, Q1~Q4) |
| reportedCurrency | string | 보고 통화 (예: USD) |
| grossProfitMargin | float | 매출총이익률 (매출총이익 / 매출액) |
| ebitMargin | float | EBIT 마진 (이자·세전이익 / 매출액) |
| ebitdaMargin | float | EBITDA 마진 (EBITDA / 매출액) |
| operatingProfitMargin | float | 영업이익률 (영업이익 / 매출액) |
| pretaxProfitMargin | float | 세전이익률 (세전이익 / 매출액) |
| continuousOperationsProfitMargin | float | 계속사업이익률 |
| netProfitMargin | float | 순이익률 (순이익 / 매출액) |
| bottomLineProfitMargin | float | 최종 순이익률 |
| receivablesTurnover | float | 매출채권 회전율 |
| payablesTurnover | float | 매입채무 회전율 |
| inventoryTurnover | float | 재고자산 회전율 |
| fixedAssetTurnover | float | 고정자산 회전율 |
| assetTurnover | float | 총자산 회전율 |
| currentRatio | float | 유동비율 (유동자산 / 유동부채) |
| quickRatio | float | 당좌비율 ((유동자산 - 재고) / 유동부채) |
| solvencyRatio | float | 지급능력비율 (자기자본 / 총자산) |
| cashRatio | float | 현금비율 (현금성자산 / 유동부채) |
| priceToEarningsRatio | float | 주가수익비율 (PER) |
| priceToEarningsGrowthRatio | float | 주가이익성장비율 (PEG) |
| forwardPriceToEarningsGrowthRatio | float | 선행 PEG 비율 |
| priceToBookRatio | float | 주가순자산비율 (PBR) |
| priceToSalesRatio | float | 주가매출비율 (PSR) |
| priceToFreeCashFlowRatio | float | 주가잉여현금흐름비율 (P/FCF) |
| priceToOperatingCashFlowRatio | float | 주가영업현금흐름비율 (P/OCF) |
| debtToAssetsRatio | float | 부채비율 (총부채 / 총자산) |
| debtToEquityRatio | float | 부채자본비율 (총부채 / 자기자본) |
| debtToCapitalRatio | float | 부채자본화비율 (총부채 / 총자본) |
| longTermDebtToCapitalRatio | float | 장기부채자본화비율 |
| financialLeverageRatio | float | 재무레버리지비율 (총자산 / 자기자본) |
| workingCapitalTurnoverRatio | float | 순운전자본 회전율 |
| operatingCashFlowRatio | float | 영업현금흐름비율 (OCF / 유동부채) |
| operatingCashFlowSalesRatio | float | 영업현금흐름 / 매출액 |
| freeCashFlowOperatingCashFlowRatio | float | 잉여현금흐름 / 영업현금흐름 |
| debtServiceCoverageRatio | float | 부채상환능력비율 (DSCR) |
| interestCoverageRatio | float | 이자보상배율 (EBIT / 이자비용) |
| shortTermOperatingCashFlowCoverageRatio | float | 단기 영업현금흐름 커버리지 비율 |
| operatingCashFlowCoverageRatio | float | 영업현금흐름 커버리지 비율 |
| capitalExpenditureCoverageRatio | float | 설비투자 커버리지 비율 (OCF / CapEx) |
| dividendPaidAndCapexCoverageRatio | float | 배당 및 설비투자 커버리지 비율 |
| dividendPayoutRatio | float | 배당성향 (배당금 / 순이익) |
| dividendYield | float | 배당수익률 (소수점) |
| dividendYieldPercentage | float | 배당수익률 (%) |
| revenuePerShare | float | 주당 매출액 |
| netIncomePerShare | float | 주당순이익 (EPS) |
| interestDebtPerShare | float | 주당 이자부담 차입금 |
| cashPerShare | float | 주당 현금성자산 |
| bookValuePerShare | float | 주당 순자산가치 (BPS) |
| tangibleBookValuePerShare | float | 주당 유형 순자산가치 |
| shareholdersEquityPerShare | float | 주당 자기자본 |
| operatingCashFlowPerShare | float | 주당 영업현금흐름 |
| capexPerShare | float | 주당 설비투자액 |
| freeCashFlowPerShare | float | 주당 잉여현금흐름 |
| netIncomePerEBT | float | 세금 부담률 (순이익 / 세전이익) |
| ebtPerEbit | float | 이자 부담률 (세전이익 / EBIT) |
| priceToFairValue | float | 주가 / 공정가치 비율 |
| debtToMarketCap | float | 총부채 / 시가총액 |
| effectiveTaxRate | float | 실효세율 |
| enterpriseValueMultiple | float | EV / EBITDA 배수 |

```python
# GET /stable/ratios?symbol=AAPL&period=annual&limit=5
params = {"symbol": "AAPL", "period": "annual", "limit": 5}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"date": "2024-09-28",
		"fiscalYear": "2024",
		"period": "FY",
		"reportedCurrency": "USD",
		"grossProfitMargin": 0.4620634981523393,
		"ebitMargin": 0.31510222870075566,
		"ebitdaMargin": 0.3443707085043538,
		"operatingProfitMargin": 0.31510222870075566,
		"pretaxProfitMargin": 0.3157901466620635,
		"continuousOperationsProfitMargin": 0.23971255769943867,
		"netProfitMargin": 0.23971255769943867,
		"bottomLineProfitMargin": 0.23971255769943867,
		"receivablesTurnover": 5.903038811648023,
		"payablesTurnover": 3.0503480278422272,
		"inventoryTurnover": 28.870710952511665,
		"fixedAssetTurnover": 8.560310858143607,
		"assetTurnover": 1.0713874732862074,
		"currentRatio": 0.8673125765340832,
		"quickRatio": 0.8260068483831466,
		"solvencyRatio": 0.3414634938155374,
		"cashRatio": 0.16975259648963673,
		"priceToEarningsRatio": 37.287278415656736,
		"priceToEarningsGrowthRatio": -45.93792700808932,
		"forwardPriceToEarningsGrowthRatio": -45.93792700808932,
		"priceToBookRatio": 61.37243774486391,
		"priceToSalesRatio": 8.93822887866815,
		"priceToFreeCashFlowRatio": 32.12256867269569,
		"priceToOperatingCashFlowRatio": 29.55638142954995,
		"debtToAssetsRatio": 0.29215025480848267,
		"debtToEquityRatio": 1.872326602282704,
		"debtToCapitalRatio": 0.6518501763673821,
		"longTermDebtToCapitalRatio": 0.6009110021023125,
		"financialLeverageRatio": 6.408779631255487,
		"workingCapitalTurnoverRatio": -31.099932397502684,
		"operatingCashFlowRatio": 0.6704045534944896,
		"operatingCashFlowSalesRatio": 0.3024128274962599,
		"freeCashFlowOperatingCashFlowRatio": 0.9201126388959359,
		"debtServiceCoverageRatio": 5.024761722304708,
		"interestCoverageRatio": 0,
		"shortTermOperatingCashFlowCoverageRatio": 5.663777000814215,
		"operatingCashFlowCoverageRatio": 1.109022873702276,
		"capitalExpenditureCoverageRatio": 12.517624642743728,
		"dividendPaidAndCapexCoverageRatio": 4.7912969490701345,
		"dividendPayoutRatio": 0.16252026969360758,
		"dividendYield": 0.0043585983369965175,
		"dividendYieldPercentage": 0.43585983369965176,
		"revenuePerShare": 25.484914639368924,
		"netIncomePerShare": 6.109054070954992,
		"interestDebtPerShare": 6.949329249507765,
		"cashPerShare": 4.247388013764271,
		"bookValuePerShare": 3.711600978715614,
		"tangibleBookValuePerShare": 3.711600978715614,
		"shareholdersEquityPerShare": 3.711600978715614,
		"operatingCashFlowPerShare": 7.706965094592383,
		"capexPerShare": 0.6156891035281195,
		"freeCashFlowPerShare": 7.091275991064264,
		"netIncomePerEBT": 0.7590881483581001,
		"ebtPerEbit": 1.0021831580314244,
		"priceToFairValue": 61.37243774486391,
		"debtToMarketCap": 0.03050761336980449,
		"effectiveTaxRate": 0.24091185164189982,
		"enterpriseValueMultiple": 26.524727497716487
	}
]
```

---

### 6. Key Metrics TTM — 핵심 재무 지표 TTM

```
GET /key-metrics-ttm
```

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드:** `/key-metrics`와 동일 (단일 TTM 기간 반환, 필드명에 `TTM` 접미사 포함)

```python
# GET /stable/key-metrics-ttm?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"marketCap": 3149833928000,
		"enterpriseValueTTM": 3216333928000,
		"evToSalesTTM": 8.126980816656559,
		"evToOperatingCashFlowTTM": 29.70001965021146,
		"evToFreeCashFlowTTM": 32.71990486169747,
		"evToEBITDATTM": 23.41672438697653,
		"netDebtToEBITDATTM": 0.48415749315627005,
		"currentRatioTTM": 0.9229383853427077,
		"incomeQualityTTM": 1.1263026521060842,
		"grahamNumberTTM": 25.198029099282905,
		"grahamNetNetTTM": -11.64435843011051,
		"taxBurdenTTM": 0.7646366484818603,
		"interestBurdenTTM": 1.0005649492739208,
		"workingCapitalTTM": -11125000000,
		"investedCapitalTTM": 34944000000,
		"returnOnAssetsTTM": 0.27943676707790227,
		"operatingReturnOnAssetsTTM": 0.35448090090471257,
		"returnOnTangibleAssetsTTM": 0.27943676707790227,
		"returnOnEquityTTM": 1.4534598087751787,
		"returnOnInvestedCapitalTTM": 0.45208108089346594,
		"returnOnCapitalEmployedTTM": 0.6292559583416784,
		"earningsYieldTTM": 0.030404739849149914,
		"freeCashFlowYieldTTM": 0.03120767705439485,
		"capexToOperatingCashFlowTTM": 0.09229504866382256,
		"capexToDepreciationTTM": 0.855956153121521,
		"capexToRevenueTTM": 0.025255205174853447,
		"salesGeneralAndAdministrativeToRevenueTTM": 0,
		"researchAndDevelopementToRevenueTTM": 0.08071053163533455,
		"stockBasedCompensationToRevenueTTM": 0.030263290883363655,
		"intangiblesToTotalAssetsTTM": 0,
		"averageReceivablesTTM": 62774500000,
		"averagePayablesTTM": 65435000000,
		"averageInventoryTTM": 7098500000,
		"daysOfSalesOutstandingTTM": 54.69650798463715,
		"daysOfPayablesOutstandingTTM": 106.76306476988712,
		"daysOfInventoryOutstandingTTM": 11.917937984569374,
		"operatingCycleTTM": 66.61444596920653,
		"cashConversionCycleTTM": -40.148618800680595,
		"freeCashFlowToEquityTTM": 31799000000,
		"freeCashFlowToFirmTTM": 85497710797.9578,
		"tangibleAssetValueTTM": 66758000000,
		"netCurrentAssetValueTTM": -144087000000
	}
]

```

---

### 7. Financial Ratios TTM — 재무 비율 TTM

```
GET /ratios-ttm
```

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드:** `/ratios`와 동일 (단일 TTM 기간 반환, 필드명에 `TTM` 접미사 포함)

```python
# GET /stable/ratios-ttm?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"grossProfitMarginTTM": 0.46518849807964424,
		"ebitMarginTTM": 0.3175535678188801,
		"ebitdaMarginTTM": 0.34705882352941175,
		"operatingProfitMarginTTM": 0.3175535678188801,
		"pretaxProfitMarginTTM": 0.31773296947645036,
		"continuousOperationsProfitMarginTTM": 0.24295027289266222,
		"netProfitMarginTTM": 0.24295027289266222,
		"bottomLineProfitMarginTTM": 0.24295027289266222,
		"receivablesTurnoverTTM": 6.673186524129093,
		"payablesTurnoverTTM": 3.4187853335486995,
		"inventoryTurnoverTTM": 30.626103313558097,
		"fixedAssetTurnoverTTM": 8.590592372311098,
		"assetTurnoverTTM": 1.1501809145995903,
		"currentRatioTTM": 0.9229383853427077,
		"quickRatioTTM": 0.8750666712845911,
		"solvencyRatioTTM": 0.3888081578786054,
		"cashRatioTTM": 0.20987774044955496,
		"priceToEarningsRatioTTM": 32.889608822880916,
		"priceToEarningsGrowthRatioTTM": 9.104441715061135,
		"forwardPriceToEarningsGrowthRatioTTM": 9.104441715061135,
		"priceToBookRatioTTM": 47.370141231313106,
		"priceToSalesRatioTTM": 7.958949686678795,
		"priceToFreeCashFlowRatioTTM": 32.04339747098139,
		"priceToOperatingCashFlowRatioTTM": 29.201395167968677,
		"debtToAssetsRatioTTM": 0.28132292892744526,
		"debtToEquityRatioTTM": 1.4499985020521886,
		"debtToCapitalRatioTTM": 0.5918364851397372,
		"longTermDebtToCapitalRatioTTM": 0.557055084464615,
		"financialLeverageRatioTTM": 5.154213727193745,
		"workingCapitalTurnoverRatioTTM": -22.92267593397046,
		"operatingCashFlowRatioTTM": 0.7501402694558931,
		"operatingCashFlowSalesRatioTTM": 0.2736355366889024,
		"freeCashFlowOperatingCashFlowRatioTTM": 0.9077049513361775,
		"debtServiceCoverageRatioTTM": 8.390251498870981,
		"interestCoverageRatioTTM": 0,
		"shortTermOperatingCashFlowCoverageRatioTTM": 8.432142022891847,
		"operatingCashFlowCoverageRatioTTM": 1.1187512267688715,
		"capitalExpenditureCoverageRatioTTM": 10.834817408704351,
		"dividendPaidAndCapexCoverageRatioTTM": 4.287173396674584,
		"dividendPayoutRatioTTM": 0.15876235049401977,
		"dividendYieldTTM": 0.0047691720717283476,
		"enterpriseValueTTM": 3216333928000,
		"revenuePerShareTTM": 26.24103186081379,
		"netIncomePerShareTTM": 6.375265851569754,
		"interestDebtPerShareTTM": 6.418298067250137,
		"cashPerShareTTM": 3.565573803101025,
		"bookValuePerShareTTM": 4.426417032959892,
		"tangibleBookValuePerShareTTM": 4.426417032959892,
		"shareholdersEquityPerShareTTM": 4.426417032959892,
		"operatingCashFlowPerShareTTM": 7.180478836504368,
		"capexPerShareTTM": 0.6627226436447186,
		"freeCashFlowPerShareTTM": 6.5177561928596495,
		"netIncomePerEBTTTM": 0.7646366484818603,
		"ebtPerEbitTTM": 1.0005649492739208,
		"priceToFairValueTTM": 47.370141231313106,
		"debtToMarketCapTTM": 0.030731461471514124,
		"effectiveTaxRateTTM": 0.23536335151813975,
		"enterpriseValueMultipleTTM": 23.41672438697653
	}
]

```

---

### 8. Financial Scores — 재무 건전성 점수

```
GET /financial-scores
```

**언제 사용:** Altman Z-Score(부도 예측)와 Piotroski F-Score(재무 건전성 9점 척도)를 통해 기업 재무 안정성을 빠르게 평가할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| reportedCurrency | string | 보고 통화 (예: USD) |
| altmanZScore | float | Altman Z-Score (1.8 이하: 위험, 3.0 이상: 안전) |
| piotroskiScore | integer | Piotroski F-Score (0~9, 높을수록 건전) |
| workingCapital | integer | 운전자본 |
| totalAssets | integer | 자산 총계 |
| retainedEarnings | integer | 이익잉여금 |
| ebit | integer | 이자·세전이익 (EBIT) |
| marketCap | integer | 시가총액 |
| totalLiabilities | integer | 부채 총계 |
| revenue | integer | 매출액 |

```python
# GET /stable/financial-scores?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"reportedCurrency": "USD",
		"altmanZScore": 9.322985825443649,
		"piotroskiScore": 8,
		"workingCapital": -11125000000,
		"totalAssets": 344085000000,
		"retainedEarnings": -11221000000,
		"ebit": 125675000000,
		"marketCap": 3259495258000,
		"totalLiabilities": 277327000000,
		"revenue": 395760000000
	}
]

```

---

### 9. Owner Earnings — 주주 관점 실질 수익

```
GET /owner-earnings
```

**언제 사용:** 버핏식 Owner Earnings 지표가 필요할 때. 순이익보다 보수적인 주주 귀속 실질 현금을 파악하는 데 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| reportedCurrency | string | 보고 통화 (예: USD) |
| fiscalYear | string | 회계연도 (예: 2025) |
| period | string | 보고 기간 (FY, Q1~Q4) |
| date | string | 보고 기준일 (YYYY-MM-DD) |
| averagePPE | float | 평균 유형자산 비율 (감가상각 대비) |
| maintenanceCapex | integer | 유지보수 자본지출 (기존 자산 유지 목적) |
| ownersEarnings | integer | 오너스 어닝 (순이익 + 감가상각 - 유지보수 CapEx) |
| growthCapex | integer | 성장 자본지출 (사업 확장 목적) |
| ownersEarningsPerShare | float | 주당 오너스 어닝 |

```python
# GET /stable/owner-earnings?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"reportedCurrency": "USD",
		"fiscalYear": "2025",
		"period": "Q1",
		"date": "2024-12-28",
		"averagePPE": 0.13969,
		"maintenanceCapex": -2279964750,
		"ownersEarnings": 27655035250,
		"growthCapex": -660035250,
		"ownersEarningsPerShare": 1.83
	}
]

```

---

### 10. Enterprise Values — 기업가치 (EV)

```
GET /enterprise-values
```

**언제 사용:** 시가총액 + 순차입금으로 산출되는 기업가치(EV)를 시계열로 조회할 때. EV/EBITDA 등 밸류에이션 멀티플 계산에 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter`,`Q1`,`Q2`,`Q3`,`Q4`,`FY` |
| limit | integer | ❌ | 반환할 기간 수 (기본: 5) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| date | string | 기준일 (YYYY-MM-DD) |
| stockPrice | float | 기준일 주가 |
| numberOfShares | integer | 발행 주식 수 |
| marketCapitalization | integer | 시가총액 |
| minusCashAndCashEquivalents | integer | 차감: 현금 및 현금성 자산 |
| addTotalDebt | integer | 가산: 총 차입금 |
| enterpriseValue | integer | 기업가치 (EV) |

```python
# GET /stable/enterprise-values?symbol=AAPL&period=annual&limit=5
params = {"symbol": "AAPL", "period": "annual", "limit": 5}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"date": "2024-09-28",
		"stockPrice": 227.79,
		"numberOfShares": 15343783000,
		"marketCapitalization": 3495160329570,
		"minusCashAndCashEquivalents": 29943000000,
		"addTotalDebt": 106629000000,
		"enterpriseValue": 3571846329570
	}
]
```

---

## 성장률 분석

### 11. Income Statement Growth — 손익계산서 성장률

```
GET /income-statement-growth
```

**언제 사용:** 매출, 영업이익, 순이익 등 손익 항목의 YoY 성장률을 시계열로 파악할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter`,`Q1`,`Q2`,`Q3`,`Q4`,`FY` |
| limit | integer | ❌ | 반환할 기간 수 (기본: 5) |
| apikey | string | ✅ | API 키 |

**응답 필드 (주요 항목)**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| date | string | 보고 기준일 (YYYY-MM-DD) |
| fiscalYear | string | 회계연도 (예: 2024) |
| period | string | 보고 기간 (FY, Q1~Q4) |
| reportedCurrency | string | 보고 통화 (예: USD) |
| growthRevenue | float | 매출액 증가율 (YoY) |
| growthCostOfRevenue | float | 매출원가 증가율 (YoY) |
| growthGrossProfit | float | 매출총이익 증가율 (YoY) |
| growthGrossProfitRatio | float | 매출총이익률 증가율 (YoY) |
| growthResearchAndDevelopmentExpenses | float | 연구개발비 증가율 (YoY) |
| growthGeneralAndAdministrativeExpenses | float | 일반관리비 증가율 (YoY) |
| growthSellingAndMarketingExpenses | float | 판매·마케팅비 증가율 (YoY) |
| growthOtherExpenses | float | 기타비용 증가율 (YoY) |
| growthOperatingExpenses | float | 영업비용 증가율 (YoY) |
| growthCostAndExpenses | float | 총비용 증가율 (YoY) |
| growthInterestIncome | float | 이자수익 증가율 (YoY) |
| growthInterestExpense | float | 이자비용 증가율 (YoY) |
| growthDepreciationAndAmortization | float | 감가상각비 증가율 (YoY) |
| growthEBITDA | float | EBITDA 증가율 (YoY) |
| growthOperatingIncome | float | 영업이익 증가율 (YoY) |
| growthIncomeBeforeTax | float | 세전이익 증가율 (YoY) |
| growthIncomeTaxExpense | float | 법인세비용 증가율 (YoY) |
| growthNetIncome | float | 순이익 증가율 (YoY) |
| growthEPS | float | 주당순이익(EPS) 증가율 (YoY) |
| growthEPSDiluted | float | 희석 EPS 증가율 (YoY) |
| growthWeightedAverageShsOut | float | 기본 가중평균 주식수 증가율 (YoY) |
| growthWeightedAverageShsOutDil | float | 희석 가중평균 주식수 증가율 (YoY) |
| growthEBIT | float | EBIT 증가율 (YoY) |
| growthNonOperatingIncomeExcludingInterest | float | 이자 제외 영업외수익 증가율 (YoY) |
| growthNetInterestIncome | float | 순이자수익 증가율 (YoY) |
| growthTotalOtherIncomeExpensesNet | float | 기타 수익·비용 합계 증가율 (YoY) |
| growthNetIncomeFromContinuingOperations | float | 계속사업 순이익 증가율 (YoY) |
| growthOtherAdjustmentsToNetIncome | float | 순이익 기타 조정항목 증가율 (YoY) |
| growthNetIncomeDeductions | float | 순이익 차감항목 증가율 (YoY) |

```python
# GET /stable/income-statement-growth?symbol=AAPL&period=annual&limit=5
params = {"symbol": "AAPL", "period": "annual", "limit": 5}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"date": "2024-09-28",
		"fiscalYear": "2024",
		"period": "FY",
		"reportedCurrency": "USD",
		"growthRevenue": 0.020219940775141214,
		"growthCostOfRevenue": -0.017675600199872046,
		"growthGrossProfit": 0.06819471705252206,
		"growthGrossProfitRatio": 0.04776303446712012,
		"growthResearchAndDevelopmentExpenses": 0.04863780712017383,
		"growthGeneralAndAdministrativeExpenses": 0,
		"growthSellingAndMarketingExpenses": 0,
		"growthOtherExpenses": -1,
		"growthOperatingExpenses": 0.04776924900176856,
		"growthCostAndExpenses": -0.004331112631234571,
		"growthInterestIncome": -1,
		"growthInterestExpense": -1,
		"growthDepreciationAndAmortization": -0.006424168764649709,
		"growthEBITDA": 0.07026704816404387,
		"growthOperatingIncome": 0.07799581805933456,
		"growthIncomeBeforeTax": 0.08571604417246959,
		"growthIncomeTaxExpense": 0.7770145152619318,
		"growthNetIncome": -0.033599670086086914,
		"growthEPS": -0.008116883116883088,
		"growthEPSDiluted": -0.008156606851549727,
		"growthWeightedAverageShsOut": -0.02543458616683152,
		"growthWeightedAverageShsOutDil": -0.02557791606880283,
		"growthEBIT": 0.0471407082579099,
		"growthNonOperatingIncomeExcludingInterest": 1,
		"growthNetInterestIncome": 1,
		"growthTotalOtherIncomeExpensesNet": 1.4761061946902654,
		"growthNetIncomeFromContinuingOperations": -0.033599670086086914,
		"growthOtherAdjustmentsToNetIncome": 0,
		"growthNetIncomeDeductions": 0
	}
]

```

---

### 12. Balance Sheet Statement Growth — 대차대조표 성장률

```
GET /balance-sheet-statement-growth
```

**언제 사용:** 자산, 부채, 자본 항목의 성장률 추이를 파악할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter`,`Q1`,`Q2`,`Q3`,`Q4`,`FY` |
| limit | integer | ❌ | 반환할 기간 수 (기본: 5) |
| apikey | string | ✅ | API 키 |

**응답 필드 (주요 항목)**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| date | string | 보고 기준일 (YYYY-MM-DD) |
| fiscalYear | string | 회계연도 (예: 2024) |
| period | string | 보고 기간 (FY, Q1~Q4) |
| reportedCurrency | string | 보고 통화 (예: USD) |
| growthCashAndCashEquivalents | float | 현금 및 현금성 자산 증가율 (YoY) |
| growthShortTermInvestments | float | 단기 투자자산 증가율 (YoY) |
| growthCashAndShortTermInvestments | float | 현금 및 단기 투자자산 합계 증가율 (YoY) |
| growthNetReceivables | float | 순 매출채권 증가율 (YoY) |
| growthInventory | float | 재고자산 증가율 (YoY) |
| growthOtherCurrentAssets | float | 기타 유동자산 증가율 (YoY) |
| growthTotalCurrentAssets | float | 유동자산 합계 증가율 (YoY) |
| growthPropertyPlantEquipmentNet | float | 유형자산 순액 증가율 (YoY) |
| growthGoodwill | float | 영업권 증가율 (YoY) |
| growthIntangibleAssets | float | 무형자산 증가율 (YoY) |
| growthGoodwillAndIntangibleAssets | float | 영업권 및 무형자산 합계 증가율 (YoY) |
| growthLongTermInvestments | float | 장기 투자자산 증가율 (YoY) |
| growthTaxAssets | float | 이연법인세 자산 증가율 (YoY) |
| growthOtherNonCurrentAssets | float | 기타 비유동자산 증가율 (YoY) |
| growthTotalNonCurrentAssets | float | 비유동자산 합계 증가율 (YoY) |
| growthOtherAssets | float | 기타 자산 증가율 (YoY) |
| growthTotalAssets | float | 자산 총계 증가율 (YoY) |
| growthAccountPayables | float | 매입채무 증가율 (YoY) |
| growthShortTermDebt | float | 단기차입금 증가율 (YoY) |
| growthTaxPayables | float | 미지급법인세 증가율 (YoY) |
| growthDeferredRevenue | float | 이연수익 (유동) 증가율 (YoY) |
| growthOtherCurrentLiabilities | float | 기타 유동부채 증가율 (YoY) |
| growthTotalCurrentLiabilities | float | 유동부채 합계 증가율 (YoY) |
| growthLongTermDebt | float | 장기차입금 증가율 (YoY) |
| growthDeferredRevenueNonCurrent | float | 이연수익 (비유동) 증가율 (YoY) |
| growthDeferredTaxLiabilitiesNonCurrent | float | 이연법인세 부채 (비유동) 증가율 (YoY) |
| growthOtherNonCurrentLiabilities | float | 기타 비유동부채 증가율 (YoY) |
| growthTotalNonCurrentLiabilities | float | 비유동부채 합계 증가율 (YoY) |
| growthOtherLiabilities | float | 기타 부채 증가율 (YoY) |
| growthTotalLiabilities | float | 부채 총계 증가율 (YoY) |
| growthPreferredStock | float | 우선주 증가율 (YoY) |
| growthCommonStock | float | 보통주 증가율 (YoY) |
| growthRetainedEarnings | float | 이익잉여금 증가율 (YoY) |
| growthAccumulatedOtherComprehensiveIncomeLoss | float | 기타포괄손익누계액 증가율 (YoY) |
| growthOthertotalStockholdersEquity | float | 기타 자본항목 증가율 (YoY) |
| growthTotalStockholdersEquity | float | 주주지분 합계 증가율 (YoY) |
| growthMinorityInterest | float | 비지배주주지분 증가율 (YoY) |
| growthTotalEquity | float | 자본 총계 증가율 (YoY) |
| growthTotalLiabilitiesAndStockholdersEquity | float | 부채 및 자본 총계 증가율 (YoY) |
| growthTotalInvestments | float | 총 투자자산 증가율 (YoY) |
| growthTotalDebt | float | 총 차입금 증가율 (YoY) |
| growthNetDebt | float | 순차입금 증가율 (YoY) |
| growthAccountsReceivables | float | 매출채권 증가율 (YoY) |
| growthOtherReceivables | float | 기타 수취채권 증가율 (YoY) |
| growthPrepaids | float | 선급비용 증가율 (YoY) |
| growthTotalPayables | float | 매입채무 합계 증가율 (YoY) |
| growthOtherPayables | float | 기타 지급채무 증가율 (YoY) |
| growthAccruedExpenses | float | 미지급비용 증가율 (YoY) |
| growthCapitalLeaseObligationsCurrent | float | 유동 금융리스 부채 증가율 (YoY) |
| growthAdditionalPaidInCapital | float | 주식발행초과금 증가율 (YoY) |
| growthTreasuryStock | float | 자기주식 증가율 (YoY) |

```python
# GET /stable/balance-sheet-statement-growth?symbol=AAPL&period=annual&limit=5
params = {"symbol": "AAPL", "period": "annual", "limit": 5}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"date": "2024-09-28",
		"fiscalYear": "2024",
		"period": "FY",
		"reportedCurrency": "USD",
		"growthCashAndCashEquivalents": -0.0007341898882029034,
		"growthShortTermInvestments": 0.11516302627413738,
		"growthCashAndShortTermInvestments": 0.058744212492892536,
		"growthNetReceivables": 0.08621792243994425,
		"growthInventory": 0.15084504817564365,
		"growthOtherCurrentAssets": -0.02776454576386526,
		"growthTotalCurrentAssets": 0.06562138667929733,
		"growthPropertyPlantEquipmentNet": -0.15992349565984992,
		"growthGoodwill": 0,
		"growthIntangibleAssets": 0,
		"growthGoodwillAndIntangibleAssets": 0,
		"growthLongTermInvestments": -0.09015953214513049,
		"growthTaxAssets": 0.09225857046829487,
		"growthOtherNonCurrentAssets": 0.5266933370120016,
		"growthTotalNonCurrentAssets": 0.014238076328719674,
		"growthOtherAssets": 0,
		"growthTotalAssets": 0.035160515396374756,
		"growthAccountPayables": 0.1014039066617687,
		"growthShortTermDebt": 0.32087050041121024,
		"growthTaxPayables": 2.01632838190271,
		"growthDeferredRevenue": 0.023322168465450935,
		"growthOtherCurrentLiabilities": -0.1254584832500786,
		"growthTotalCurrentLiabilities": 0.21391802240757563,
		"growthLongTermDebt": -0.10003043628845205,
		"growthDeferredRevenueNonCurrent": 0,
		"growthDeferredTaxLiabilitiesNonCurrent": 0,
		"growthOtherNonCurrentLiabilities": -0.09048495373370312,
		"growthTotalNonCurrentLiabilities": -0.09295867814151548,
		"growthOtherLiabilities": 0,
		"growthTotalLiabilities": 0.060574238130816666,
		"growthPreferredStock": 0,
		"growthCommonStock": 0.12821763398905328,
		"growthRetainedEarnings": -88.50467289719626,
		"growthAccumulatedOtherComprehensiveIncomeLoss": 0.3737338456164862,
		"growthOthertotalStockholdersEquity": 0,
		"growthTotalStockholdersEquity": -0.0836095645737457,
		"growthMinorityInterest": 0,
		"growthTotalEquity": -0.0836095645737457,
		"growthTotalLiabilitiesAndStockholdersEquity": 0.035160515396374756,
		"growthTotalInvestments": -0.04107194211936368,
		"growthTotalDebt": -0.0401393489845888,
		"growthNetDebt": -0.05469472282829777,
		"growthAccountsReceivables": 0.13223532601328453,
		"growthOtherReceivables": 0.04307907360930203,
		"growthPrepaids": 0,
		"growthTotalPayables": 0.5262653527335452,
		"growthOtherPayables": 0,
		"growthAccruedExpenses": 0,
		"growthCapitalLeaseObligationsCurrent": 0.03619047619047619,
		"growthAdditionalPaidInCapital": 0,
		"growthTreasuryStock": 0
	}
]

```

---

### 13. Cash Flow Statement Growth — 현금흐름표 성장률

```
GET /cash-flow-statement-growth
```

**언제 사용:** 영업현금흐름, FCF 등 현금흐름 항목의 성장 추이를 파악할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter`,`Q1`,`Q2`,`Q3`,`Q4`,`FY` |
| limit | integer | ❌ | 반환할 기간 수 (기본: 5) |
| apikey | string | ✅ | API 키 |

**응답 필드 (주요 항목)**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| date | string | 보고 기준일 (YYYY-MM-DD) |
| fiscalYear | string | 회계연도 (예: 2024) |
| period | string | 보고 기간 (FY, Q1~Q4) |
| reportedCurrency | string | 보고 통화 (예: USD) |
| growthNetIncome | float | 순이익 증가율 (YoY) |
| growthDepreciationAndAmortization | float | 감가상각비 증가율 (YoY) |
| growthDeferredIncomeTax | float | 이연법인세 증가율 (YoY) |
| growthStockBasedCompensation | float | 주식보상비용 증가율 (YoY) |
| growthChangeInWorkingCapital | float | 운전자본 변동 증가율 (YoY) |
| growthAccountsReceivables | float | 매출채권 변동 증가율 (YoY) |
| growthInventory | float | 재고자산 변동 증가율 (YoY) |
| growthAccountsPayables | float | 매입채무 변동 증가율 (YoY) |
| growthOtherWorkingCapital | float | 기타 운전자본 변동 증가율 (YoY) |
| growthOtherNonCashItems | float | 기타 비현금 항목 증가율 (YoY) |
| growthNetCashProvidedByOperatingActivites | float | 영업활동 현금흐름 증가율 (YoY) |
| growthInvestmentsInPropertyPlantAndEquipment | float | 유형자산 투자 증가율 (YoY) |
| growthAcquisitionsNet | float | 순 인수합병 금액 증가율 (YoY) |
| growthPurchasesOfInvestments | float | 투자자산 매입 증가율 (YoY) |
| growthSalesMaturitiesOfInvestments | float | 투자자산 매각·만기 증가율 (YoY) |
| growthOtherInvestingActivites | float | 기타 투자활동 증가율 (YoY) |
| growthNetCashUsedForInvestingActivites | float | 투자활동 순현금 증가율 (YoY) |
| growthDebtRepayment | float | 차입금 상환 증가율 (YoY) |
| growthCommonStockIssued | float | 보통주 발행 증가율 (YoY) |
| growthCommonStockRepurchased | float | 자사주 매입 증가율 (YoY) |
| growthDividendsPaid | float | 배당금 지급 증가율 (YoY) |
| growthOtherFinancingActivites | float | 기타 재무활동 증가율 (YoY) |
| growthNetCashUsedProvidedByFinancingActivities | float | 재무활동 순현금 증가율 (YoY) |
| growthEffectOfForexChangesOnCash | float | 환율 변동 현금 영향 증가율 (YoY) |
| growthNetChangeInCash | float | 순 현금 변동 증가율 (YoY) |
| growthCashAtEndOfPeriod | float | 기말 현금 증가율 (YoY) |
| growthCashAtBeginningOfPeriod | float | 기초 현금 증가율 (YoY) |
| growthOperatingCashFlow | float | 영업현금흐름 증가율 (YoY) |
| growthCapitalExpenditure | float | 자본지출(CapEx) 증가율 (YoY) |
| growthFreeCashFlow | float | 잉여현금흐름(FCF) 증가율 (YoY) |
| growthNetDebtIssuance | float | 순 차입금 발행 증가율 (YoY) |
| growthLongTermNetDebtIssuance | float | 장기 순 차입금 발행 증가율 (YoY) |
| growthShortTermNetDebtIssuance | float | 단기 순 차입금 발행 증가율 (YoY) |
| growthNetStockIssuance | float | 순 주식 발행 증가율 (YoY) |
| growthPreferredDividendsPaid | float | 우선주 배당금 지급 증가율 (YoY) |
| growthIncomeTaxesPaid | float | 법인세 납부 증가율 (YoY) |
| growthInterestPaid | float | 이자 지급 증가율 (YoY) |

```python
# GET /stable/cash-flow-statement-growth?symbol=AAPL&period=annual&limit=5
params = {"symbol": "AAPL", "period": "annual", "limit": 5}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"date": "2024-09-28",
		"fiscalYear": "2024",
		"period": "FY",
		"reportedCurrency": "USD",
		"growthNetIncome": -0.033599670086086914,
		"growthDepreciationAndAmortization": -0.006424168764649709,
		"growthDeferredIncomeTax": 0,
		"growthStockBasedCompensation": 0.07892550540016616,
		"growthChangeInWorkingCapital": 1.555116314429071,
		"growthAccountsReceivables": -2.0473933649289098,
		"growthInventory": 0.3535228677379481,
		"growthAccountsPayables": 4.1868713605082055,
		"growthOtherWorkingCapital": 2.4402563136072373,
		"growthOtherNonCashItems": -0.017512348450830714,
		"growthNetCashProvidedByOperatingActivites": 0.06975566069312394,
		"growthInvestmentsInPropertyPlantAndEquipment": 0.13796879277306323,
		"growthAcquisitionsNet": 0,
		"growthPurchasesOfInvestments": -0.6486294175448107,
		"growthSalesMaturitiesOfInvestments": 0.3698202750801951,
		"growthOtherInvestingActivites": 0.02169035153328347,
		"growthNetCashUsedForInvestingActivites": -0.2078272604588394,
		"growthDebtRepayment": -0.012662502110417018,
		"growthCommonStockIssued": 0,
		"growthCommonStockRepurchased": -0.2243584784010316,
		"growthDividendsPaid": -0.013910149750415973,
		"growthOtherFinancingActivites": 0.03493013972055888,
		"growthNetCashUsedProvidedByFinancingActivities": -0.12439163778482412,
		"growthEffectOfForexChangesOnCash": 0,
		"growthNetChangeInCash": -1.1378472222222222,
		"growthCashAtEndOfPeriod": -0.02583205908188828,
		"growthCashAtBeginningOfPeriod": 0.23061216319013492,
		"growthOperatingCashFlow": 0.06975566069312394,
		"growthCapitalExpenditure": 0.13796879277306323,
		"growthFreeCashFlow": 0.092615279562982,
		"growthNetDebtIssuance": 0.3942026057973942,
		"growthLongTermNetDebtIssuance": -0.6812426135404356,
		"growthShortTermNetDebtIssuance": 1.995475113122172,
		"growthNetStockIssuance": -0.2243584784010316,
		"growthPreferredDividendsPaid": -0.013910149750415973,
		"growthIncomeTaxesPaid": 0.3973981476524439,
		"growthInterestPaid": -1
	}
]

```

---

### 14. Financial Statement Growth — 통합 재무제표 성장률

```
GET /financial-growth
```

**언제 사용:** 손익/대차/현금흐름 전체 주요 항목의 성장률을 단일 엔드포인트로 한 번에 조회할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter`,`Q1`,`Q2`,`Q3`,`Q4`,`FY` |
| limit | integer | ❌ | 반환할 기간 수 (기본: 5) |
| apikey | string | ✅ | API 키 |

**응답 필드:** 
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| date | string | 보고 기준일 (YYYY-MM-DD) |
| fiscalYear | string | 회계연도 (예: 2024) |
| period | string | 보고 기간 (FY, Q1~Q4) |
| reportedCurrency | string | 보고 통화 (예: USD) |
| revenueGrowth | float | 매출액 증가율 (YoY) |
| grossProfitGrowth | float | 매출총이익 증가율 (YoY) |
| ebitgrowth | float | EBIT 증가율 (YoY) |
| operatingIncomeGrowth | float | 영업이익 증가율 (YoY) |
| netIncomeGrowth | float | 순이익 증가율 (YoY) |
| epsgrowth | float | 주당순이익(EPS) 증가율 (YoY) |
| epsdilutedGrowth | float | 희석 EPS 증가율 (YoY) |
| weightedAverageSharesGrowth | float | 기본 가중평균 주식수 증가율 (YoY) |
| weightedAverageSharesDilutedGrowth | float | 희석 가중평균 주식수 증가율 (YoY) |
| dividendsPerShareGrowth | float | 주당 배당금 증가율 (YoY) |
| operatingCashFlowGrowth | float | 영업현금흐름 증가율 (YoY) |
| receivablesGrowth | float | 매출채권 증가율 (YoY) |
| inventoryGrowth | float | 재고자산 증가율 (YoY) |
| assetGrowth | float | 총자산 증가율 (YoY) |
| bookValueperShareGrowth | float | 주당 순자산가치(BPS) 증가율 (YoY) |
| debtGrowth | float | 총 차입금 증가율 (YoY) |
| rdexpenseGrowth | float | 연구개발비 증가율 (YoY) |
| sgaexpensesGrowth | float | 판관비(SG&A) 증가율 (YoY) |
| freeCashFlowGrowth | float | 잉여현금흐름(FCF) 증가율 (YoY) |
| tenYRevenueGrowthPerShare | float | 주당 매출액 10년 누적 증가율 |
| fiveYRevenueGrowthPerShare | float | 주당 매출액 5년 누적 증가율 |
| threeYRevenueGrowthPerShare | float | 주당 매출액 3년 누적 증가율 |
| tenYOperatingCFGrowthPerShare | float | 주당 영업현금흐름 10년 누적 증가율 |
| fiveYOperatingCFGrowthPerShare | float | 주당 영업현금흐름 5년 누적 증가율 |
| threeYOperatingCFGrowthPerShare | float | 주당 영업현금흐름 3년 누적 증가율 |
| tenYNetIncomeGrowthPerShare | float | 주당 순이익 10년 누적 증가율 |
| fiveYNetIncomeGrowthPerShare | float | 주당 순이익 5년 누적 증가율 |
| threeYNetIncomeGrowthPerShare | float | 주당 순이익 3년 누적 증가율 |
| tenYShareholdersEquityGrowthPerShare | float | 주당 자기자본 10년 누적 증가율 |
| fiveYShareholdersEquityGrowthPerShare | float | 주당 자기자본 5년 누적 증가율 |
| threeYShareholdersEquityGrowthPerShare | float | 주당 자기자본 3년 누적 증가율 |
| tenYDividendperShareGrowthPerShare | float | 주당 배당금 10년 누적 증가율 |
| fiveYDividendperShareGrowthPerShare | float | 주당 배당금 5년 누적 증가율 |
| threeYDividendperShareGrowthPerShare | float | 주당 배당금 3년 누적 증가율 |
| ebitdaGrowth | float | EBITDA 증가율 (YoY) |
| growthCapitalExpenditure | float | 자본지출(CapEx) 증가율 (YoY) |
| tenYBottomLineNetIncomeGrowthPerShare | float | 주당 최종 순이익 10년 누적 증가율 |
| fiveYBottomLineNetIncomeGrowthPerShare | float | 주당 최종 순이익 5년 누적 증가율 |
| threeYBottomLineNetIncomeGrowthPerShare | float | 주당 최종 순이익 3년 누적 증가율 |

```python
# GET /stable/financial-growth?symbol=AAPL&period=annual&limit=5
params = {"symbol": "AAPL", "period": "annual", "limit": 5}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"date": "2024-09-28",
		"fiscalYear": "2024",
		"period": "FY",
		"reportedCurrency": "USD",
		"revenueGrowth": 0.020219940775141214,
		"grossProfitGrowth": 0.06819471705252206,
		"ebitgrowth": 0.07799581805933456,
		"operatingIncomeGrowth": 0.07799581805933456,
		"netIncomeGrowth": -0.033599670086086914,
		"epsgrowth": -0.008116883116883088,
		"epsdilutedGrowth": -0.008156606851549727,
		"weightedAverageSharesGrowth": -0.02543458616683152,
		"weightedAverageSharesDilutedGrowth": -0.02557791606880283,
		"dividendsPerShareGrowth": 0.040371570095532654,
		"operatingCashFlowGrowth": 0.06975566069312394,
		"receivablesGrowth": 0.08621792243994425,
		"inventoryGrowth": 0.15084504817564365,
		"assetGrowth": 0.035160515396374756,
		"bookValueperShareGrowth": -0.059693251557224776,
		"debtGrowth": -0.0401393489845888,
		"rdexpenseGrowth": 0.04863780712017383,
		"sgaexpensesGrowth": 0.04672709770575967,
		"freeCashFlowGrowth": 0.092615279562982,
		"tenYRevenueGrowthPerShare": 2.3937532854122625,
		"fiveYRevenueGrowthPerShare": 0.8093292228858464,
		"threeYRevenueGrowthPerShare": 0.163506592883552,
		"tenYOperatingCFGrowthPerShare": 2.1417809176982403,
		"fiveYOperatingCFGrowthPerShare": 1.051533221923415,
		"threeYOperatingCFGrowthPerShare": 0.23720294833900227,
		"tenYNetIncomeGrowthPerShare": 2.76381558093543,
		"fiveYNetIncomeGrowthPerShare": 1.0421744314966246,
		"threeYNetIncomeGrowthPerShare": 0.07761907162786884,
		"tenYShareholdersEquityGrowthPerShare": -0.19003774225234785,
		"fiveYShareholdersEquityGrowthPerShare": -0.24235004889283715,
		"threeYShareholdersEquityGrowthPerShare": -0.017459858915902907,
		"tenYDividendperShareGrowthPerShare": 1.1722201809466772,
		"fiveYDividendperShareGrowthPerShare": 0.29890046876764864,
		"threeYDividendperShareGrowthPerShare": 0.14617932692103452,
		"ebitdaGrowth": null,
		"growthCapitalExpenditure": null,
		"tenYBottomLineNetIncomeGrowthPerShare": null,
		"fiveYBottomLineNetIncomeGrowthPerShare": null,
		"threeYBottomLineNetIncomeGrowthPerShare": null
	}
]

```

---

## 보고서 원문 접근

### 15. Financial Reports Dates — 보고서 제출 가능 날짜 목록

```
GET /financial-reports-dates
```

**언제 사용:** 특정 기업의 10-K/10-Q 보고서가 어떤 연도/분기로 제출되어 있는지 확인할 때. JSON 원문 조회 전 유효한 파라미터 값 확인용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| fiscalYear | integer | 회계연도 (예: 2025) |
| period | string | 보고 기간 (FY, Q1~Q4) |
| fiscalYear | string | 회계연도 (예: 2025) |

```python
# GET /stable/financial-reports-dates?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"fiscalYear": 2025,
		"period": "Q1",
		"linkJson": "https://financialmodelingprep.com/stable/financial-reports-xlsx?symbol=AAPL&year=2025&period=Q1&apikey=YOUR_API_KEY"
	}
]

```

---

### 16. Financial Reports Form 10-K JSON — 연간보고서 JSON

```
GET /financial-reports-json
```

**언제 사용:** SEC에 제출된 10-K 연간보고서를 구조화된 JSON으로 조회할 때. 원문 데이터 파싱이나 심층 분석에 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| year | integer | ✅ | 회계연도 (예: 2022) |
| period | string | ✅ | 보고 기간 (`FY`, `Q1`, `Q2`, `Q3`, `Q4`) |
| apikey | string | ✅ | API 키 |

**응답:** 10-K 보고서 전체를 섹션별로 구조화한 JSON 객체

```python
# GET /stable/financial-reports-json?symbol=AAPL&year=2022&period=FY
params = {"symbol": "AAPL", "year": 2022, "period": "FY"}
```

---

## 매출 세분화

### 17. Revenue Product Segmentation — 제품별 매출 분류

```
GET /revenue-product-segmentation
```

**언제 사용:** 제품 라인별 상세 매출 내역을 확인할 수 있습니다. 어떤 제품이 기업의 수익을 이끌고 있는지 파악하고, 개별 제품 세그먼트의 성과에 대한 인사이트를 얻을 수 있습니다.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter` |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| fiscalYear | integer | 회계연도 (예: 2024) |
| period | string | 보고 기간 (FY, Q1~Q4) |
| reportedCurrency | string | 보고 통화 (예: USD), 없을 경우 null |
| date | string | 보고 기준일 (YYYY-MM-DD) |
| data | object | 제품 세그먼트별 매출 데이터 (기업 마다 유동적 포멧) |


```python
# GET /stable/revenue-product-segmentation?symbol=AAPL&period=annual
params = {"symbol": "AAPL", "period": "annual"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"fiscalYear": 2024,
		"period": "FY",
		"reportedCurrency": null,
		"date": "2024-09-28",
		"data": {
			"Mac": 29984000000,
			"Service": 96169000000,
			"Wearables, Home and Accessories": 37005000000,
			"iPad": 26694000000,
			"iPhone": 201183000000
		}
	}
]

```

---

### 18. Revenue Geographic Segments — 지역별 매출 분류

```
GET /revenue-geographic-segmentation
```

**언제 사용:** 미주, 유럽, 중화권 등 지역별 매출 비중과 규모를 파악할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter` |
| apikey | string | ✅ | API 키 |

**응답:** 
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| fiscalYear | integer | 회계연도 (예: 2024) |
| period | string | 보고 기간 (FY, Q1~Q4) |
| reportedCurrency | string | 보고 통화 (예: USD), 없을 경우 null |
| date | string | 보고 기준일 (YYYY-MM-DD) |
| data | object | 지역별 매출 데이터 (기업 마다 유동적 포멧) |


```python
# GET /stable/revenue-geographic-segmentation?symbol=AAPL&period=annual
params = {"symbol": "AAPL", "period": "annual"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"fiscalYear": 2024,
		"period": "FY",
		"reportedCurrency": null,
		"date": "2024-09-28",
		"data": {
			"Americas Segment": 167045000000,
			"Europe Segment": 101328000000,
			"Greater China Segment": 66952000000,
			"Japan Segment": 25052000000,
			"Rest of Asia Pacific": 30658000000
		}
	}
]

```

---

## As-Reported (SEC 원문 기준)

> SEC에 제출된 **원문 데이터를 표준화 없이** 그대로 반환. 필드명과 구조가 기업마다 다를 수 있으므로 주의.

### 19. As Reported Income Statement — SEC 원문 손익계산서

```
GET /income-statement-as-reported
```

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter` |
| limit | integer | ❌ | 반환할 기간 수 |
| apikey | string | ✅ | API 키 |

**응답 필드** 
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| fiscalYear | integer | 회계연도 (예: 2024) |
| period | string | 보고 기간 (FY, Q1~Q4) |
| reportedCurrency | string | 보고 통화 (예: USD), 없을 경우 null |
| date | string | 보고 기준일 (YYYY-MM-DD) |
| data | object | SEC 원문 항목명 그대로 반환 (기업마다 필드명 상이) |


```python
# GET /stable/income-statement-as-reported?symbol=AAPL&period=annual
params = {"symbol": "AAPL", "period": "annual"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"fiscalYear": 2024,
		"period": "FY",
		"reportedCurrency": null,
		"date": "2024-09-27",
		"data": {
			"revenuefromcontractwithcustomerexcludingassessedtax": 391035000000,
			"costofgoodsandservicessold": 210352000000,
			"grossprofit": 180683000000,
			"researchanddevelopmentexpense": 31370000000,
			"sellinggeneralandadministrativeexpense": 26097000000,
			"operatingexpenses": 57467000000,
			"operatingincomeloss": 123216000000,
			"nonoperatingincomeexpense": 269000000,
			"incomelossfromcontinuingoperationsbeforeincometaxesextraordinaryitemsnoncontrollinginterest": 123485000000,
			"incometaxexpensebenefit": 29749000000,
			"netincomeloss": 93736000000,
			"earningspersharebasic": 6.11,
			"earningspersharediluted": 6.08,
			"weightedaveragenumberofsharesoutstandingbasic": 15343783000,
			"weightedaveragenumberofdilutedsharesoutstanding": 15408095000,
			"othercomprehensiveincomelossforeigncurrencytransactionandtranslationadjustmentnetoftax": 395000000,
			"othercomprehensiveincomelossderivativeinstrumentgainlossbeforereclassificationaftertax": -832000000,
			"othercomprehensiveincomelossderivativeinstrumentgainlossreclassificationaftertax": 1337000000,
			"othercomprehensiveincomelossderivativeinstrumentgainlossafterreclassificationandtax": -2169000000,
			"othercomprehensiveincomeunrealizedholdinggainlossonsecuritiesarisingduringperiodnetoftax": 5850000000,
			"othercomprehensiveincomelossreclassificationadjustmentfromaociforsaleofsecuritiesnetoftax": -204000000,
			"othercomprehensiveincomelossavailableforsalesecuritiesadjustmentnetoftax": 6054000000,
			"othercomprehensiveincomelossnetoftaxportionattributabletoparent": 4280000000,
			"comprehensiveincomenetoftax": 98016000000
		}
	}
]

```

---

### 20. As Reported Balance Sheet — SEC 원문 대차대조표

```
GET /balance-sheet-statement-as-reported
```

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter` |
| limit | integer | ❌ | 반환할 기간 수 |
| apikey | string | ✅ | API 키 |

**응답 필드** `/income-statement-as-reported`과 동일

```python
# GET /stable/balance-sheet-statement-as-reported?symbol=AAPL&period=annual
params = {"symbol": "AAPL", "period": "annual"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"fiscalYear": 2024,
		"period": "FY",
		"reportedCurrency": null,
		"date": "2024-09-27",
		"data": {
			"cashandcashequivalentsatcarryingvalue": 29943000000,
			"marketablesecuritiescurrent": 35228000000,
			"accountsreceivablenetcurrent": 33410000000,
			"nontradereceivablescurrent": 32833000000,
			"inventorynet": 7286000000,
			"otherassetscurrent": 14287000000,
			"assetscurrent": 152987000000,
			"marketablesecuritiesnoncurrent": 91479000000,
			"propertyplantandequipmentnet": 45680000000,
			"otherassetsnoncurrent": 74834000000,
			"assetsnoncurrent": 211993000000,
			"assets": 364980000000,
			"accountspayablecurrent": 68960000000,
			"otherliabilitiescurrent": 78304000000,
			"contractwithcustomerliabilitycurrent": 8249000000,
			"commercialpaper": 10000000000,
			"longtermdebtcurrent": 10912000000,
			"liabilitiescurrent": 176392000000,
			"longtermdebtnoncurrent": 85750000000,
			"otherliabilitiesnoncurrent": 45888000000,
			"liabilitiesnoncurrent": 131638000000,
			"liabilities": 308030000000,
			"commonstocksharesoutstanding": 15116786000,
			"commonstocksharesissued": 15116786000,
			"commonstocksincludingadditionalpaidincapital": 83276000000,
			"retainedearningsaccumulateddeficit": -19154000000,
			"accumulatedothercomprehensiveincomelossnetoftax": -7172000000,
			"stockholdersequity": 56950000000,
			"liabilitiesandstockholdersequity": 364980000000,
			"commonstockparorstatedvaluepershare": 0.00001,
			"commonstocksharesauthorized": 50400000000
		}
	}
]

```

---

### 21. As Reported Cash Flow Statement — SEC 원문 현금흐름표

```
GET /cash-flow-statement-as-reported
```

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter` |
| limit | integer | ❌ | 반환할 기간 수 |
| apikey | string | ✅ | API 키 |

**응답 필드** `/income-statement-as-reported`과 동일

```python
# GET /stable/cash-flow-statement-as-reported?symbol=AAPL&period=annual
params = {"symbol": "AAPL", "period": "annual"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"fiscalYear": 2024,
		"period": "FY",
		"reportedCurrency": null,
		"date": "2024-09-27",
		"data": {
			"cashcashequivalentsrestrictedcashandrestrictedcashequivalents": 29943000000,
			"netincomeloss": 93736000000,
			"depreciationdepletionandamortization": 11445000000,
			"sharebasedcompensation": 11688000000,
			"othernoncashincomeexpense": 2266000000,
			"increasedecreaseinaccountsreceivable": 3788000000,
			"increasedecreaseinotherreceivables": 1356000000,
			"increasedecreaseininventories": 1046000000,
			"increasedecreaseinotheroperatingassets": 11731000000,
			"increasedecreaseinaccountspayable": 6020000000,
			"increasedecreaseinotheroperatingliabilities": 15552000000,
			"netcashprovidedbyusedinoperatingactivities": 118254000000,
			"paymentstoacquireavailableforsalesecuritiesdebt": 48656000000,
			"proceedsfrommaturitiesprepaymentsandcallsofavailableforsalesecurities": 51211000000,
			"proceedsfromsaleofavailableforsalesecuritiesdebt": 11135000000,
			"paymentstoacquirepropertyplantandequipment": 9447000000,
			"paymentsforproceedsfromotherinvestingactivities": 1308000000,
			"netcashprovidedbyusedininvestingactivities": 2935000000,
			"paymentsrelatedtotaxwithholdingforsharebasedcompensation": 5600000000,
			"paymentsofdividends": 15234000000,
			"paymentsforrepurchaseofcommonstock": 94949000000,
			"repaymentsoflongtermdebt": 9958000000,
			"proceedsfromrepaymentsofcommercialpaper": 3960000000,
			"proceedsfrompaymentsforotherfinancingactivities": -361000000,
			"netcashprovidedbyusedinfinancingactivities": -121983000000,
			"cashcashequivalentsrestrictedcashandrestrictedcashequivalentsperiodincreasedecreaseincludingexchangerateeffect": -794000000,
			"incometaxespaidnet": 26102000000
		}
	}
]

```

---

### 22. As Reported Full Financial Statements — SEC 원문 전체 통합

```
GET /financial-statement-full-as-reported
```

**언제 사용:** 손익/대차/현금흐름 3개 재무제표를 SEC 원문 기준으로 한 번에 조회할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| period | string | ❌ | `annual`(기본) 또는 `quarter` |
| apikey | string | ✅ | API 키 |

**응답 필드** `/income-statement-as-reported`과 동일

```python
# GET /stable/financial-statement-full-as-reported?symbol=AAPL&period=annual
params = {"symbol": "AAPL", "period": "annual"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"fiscalYear": 2024,
		"period": "FY",
		"reportedCurrency": null,
		"date": "2024-09-27",
		"data": {
			"documenttype": "10-K",
			"documentannualreport": "true",
			"currentfiscalyearenddate": "--09-28",
			"documentperiodenddate": "2024-09-28",
			"documenttransitionreport": "false",
			"entityfilenumber": "001-36743",
			"entityregistrantname": "Apple Inc.",
			"entityincorporationstatecountrycode": "CA",
			"entitytaxidentificationnumber": "94-2404110",
			"entityaddressaddressline1": "One Apple Park Way",
			"entityaddresscityortown": "Cupertino",
			"entityaddressstateorprovince": "CA",
			"entityaddresspostalzipcode": 95014,
			"cityareacode": 408,
			"localphonenumber": "996-1010",
			"security12btitle": "3.600% Notes due 2042",
			"tradingsymbol": "AAPL",
			"notradingsymbolflag": "true",
			"securityexchangename": "NASDAQ",
			"entitywellknownseasonedissuer": "Yes",
			"entityvoluntaryfilers": "No",
			"entitycurrentreportingstatus": "Yes",
			"entityinteractivedatacurrent": "Yes",
			"entityfilercategory": "Large Accelerated Filer",
			"entitysmallbusiness": "false",
			"entityemerginggrowthcompany": "false",
			"icfrauditorattestationflag": "true",
			"documentfinstmterrorcorrectionflag": "false",
			"entityshellcompany": "false",
			"amendmentflag": "false",
			"documentfiscalyearfocus": 2024,
			"documentfiscalperiodfocus": "FY",
			"entitycentralindexkey": 320193,
			"auditorname": "Ernst & Young LLP",
			"auditorlocation": "San Jose, California",
			"auditorfirmid": 42,
			"revenuefromcontractwithcustomerexcludingassessedtax": 391035000000,
			"costofgoodsandservicessold": 210352000000,
			"grossprofit": 180683000000,
			"researchanddevelopmentexpense": 31370000000,
			"sellinggeneralandadministrativeexpense": 26097000000,
			"operatingexpenses": 57467000000,
			"operatingincomeloss": 123216000000,
			"nonoperatingincomeexpense": 269000000,
			"incomelossfromcontinuingoperationsbeforeincometaxesextraordinaryitemsnoncontrollinginterest": 123485000000,
			"incometaxexpensebenefit": 29749000000,
			"netincomeloss": 93736000000,
			"earningspersharebasic": 6.11,
			"earningspersharediluted": 6.08,
			"weightedaveragenumberofsharesoutstandingbasic": 15343783000,
			"weightedaveragenumberofdilutedsharesoutstanding": 15408095000,
			"othercomprehensiveincomelossforeigncurrencytransactionandtranslationadjustmentnetoftax": 395000000,
			"othercomprehensiveincomelossderivativeinstrumentgainlossbeforereclassificationaftertax": -832000000,
			"othercomprehensiveincomelossderivativeinstrumentgainlossreclassificationaftertax": 1337000000,
			"othercomprehensiveincomelossderivativeinstrumentgainlossafterreclassificationandtax": -2169000000,
			"othercomprehensiveincomeunrealizedholdinggainlossonsecuritiesarisingduringperiodnetoftax": 5850000000,
			"othercomprehensiveincomelossreclassificationadjustmentfromaociforsaleofsecuritiesnetoftax": -204000000,
			"othercomprehensiveincomelossavailableforsalesecuritiesadjustmentnetoftax": 6054000000,
			"othercomprehensiveincomelossnetoftaxportionattributabletoparent": 4280000000,
			"comprehensiveincomenetoftax": 98016000000,
			"cashandcashequivalentsatcarryingvalue": 29943000000,
			"marketablesecuritiescurrent": 35228000000,
			"accountsreceivablenetcurrent": 33410000000,
			"nontradereceivablescurrent": 32833000000,
			"inventorynet": 7286000000,
			"otherassetscurrent": 14287000000,
			"assetscurrent": 152987000000,
			"marketablesecuritiesnoncurrent": 91479000000,
			"propertyplantandequipmentnet": 45680000000,
			"otherassetsnoncurrent": 74834000000,
			"assetsnoncurrent": 211993000000,
			"assets": 364980000000,
			"accountspayablecurrent": 68960000000,
			"otherliabilitiescurrent": 78304000000,
			"contractwithcustomerliabilitycurrent": 8249000000,
			"commercialpaper": 10000000000,
			"longtermdebtcurrent": 10912000000,
			"liabilitiescurrent": 176392000000,
			"longtermdebtnoncurrent": 85750000000,
			"otherliabilitiesnoncurrent": 45888000000,
			"liabilitiesnoncurrent": 131638000000,
			"liabilities": 308030000000,
			"commonstocksharesoutstanding": 15116786000,
			"commonstocksharesissued": 15116786000,
			"commonstocksincludingadditionalpaidincapital": 83276000000,
			"retainedearningsaccumulateddeficit": -19154000000,
			"accumulatedothercomprehensiveincomelossnetoftax": -7172000000,
			"stockholdersequity": 56950000000,
			"liabilitiesandstockholdersequity": 364980000000,
			"commonstockparorstatedvaluepershare": 0.00001,
			"commonstocksharesauthorized": 50400000000,
			"stockissuedduringperiodvaluenewissues": 1423000000,
			"adjustmentsrelatedtotaxwithholdingforsharebasedcompensation": 1612000000,
			"adjustmentstoadditionalpaidincapitalsharebasedcompensationrequisiteserviceperiodrecognitionvalue": 12034000000,
			"dividends": 15218000000,
			"stockrepurchasedandretiredduringperiodvalue": 95000000000,
			"commonstockdividendspersharedeclared": 0.98,
			"cashcashequivalentsrestrictedcashandrestrictedcashequivalents": 29943000000,
			"depreciationdepletionandamortization": 11445000000,
			"sharebasedcompensation": 11688000000,
			"othernoncashincomeexpense": 2266000000,
			"increasedecreaseinaccountsreceivable": 3788000000,
			"increasedecreaseinotherreceivables": 1356000000,
			"increasedecreaseininventories": 1046000000,
			"increasedecreaseinotheroperatingassets": 11731000000,
			"increasedecreaseinaccountspayable": 6020000000,
			"increasedecreaseinotheroperatingliabilities": 15552000000,
			"netcashprovidedbyusedinoperatingactivities": 118254000000,
			"paymentstoacquireavailableforsalesecuritiesdebt": 48656000000,
			"proceedsfrommaturitiesprepaymentsandcallsofavailableforsalesecurities": 51211000000,
			"proceedsfromsaleofavailableforsalesecuritiesdebt": 11135000000,
			"paymentstoacquirepropertyplantandequipment": 9447000000,
			"paymentsforproceedsfromotherinvestingactivities": 1308000000,
			"netcashprovidedbyusedininvestingactivities": 2935000000,
			"paymentsrelatedtotaxwithholdingforsharebasedcompensation": 5600000000,
			"paymentsofdividends": 15234000000,
			"paymentsforrepurchaseofcommonstock": 94949000000,
			"repaymentsoflongtermdebt": 9958000000,
			"proceedsfromrepaymentsofcommercialpaper": 3960000000,
			"proceedsfrompaymentsforotherfinancingactivities": -361000000,
			"netcashprovidedbyusedinfinancingactivities": -121983000000,
			"cashcashequivalentsrestrictedcashandrestrictedcashequivalentsperiodincreasedecreaseincludingexchangerateeffect": -794000000,
			"incometaxespaidnet": 26102000000,
			"commercialpapercashflowsummarytabletextblock": "The following table provides a summary of cash flows associated with the issuance and maturities of commercial paper for 2024, 2023 and 2022 (in millions):",
			"contractwithcustomerliabilityrevenuerecognized": 7700000000,
			"contractwithcustomerliability": 12800000000,
			"revenueremainingperformanceobligationpercentage": 0.02,
			"revenueremainingperformanceobligationexpectedtimingofsatisfactionperiod1": "P1Y",
			"incrementalcommonsharesattributabletosharebasedpaymentarrangements": 64312000,
			"cash": 27199000000,
			"equitysecuritiesfvnicost": 1293000000,
			"equitysecuritiesfvniaccumulatedgrossunrealizedgainbeforetax": 105000000,
			"equitysecuritiesfvniaccumulatedgrossunrealizedlossbeforetax": 3000000,
			"equitysecuritiesfvnicurrentandnoncurrent": 1395000000,
			"availableforsaledebtsecuritiesamortizedcostbasis": 132108000000,
			"availableforsaledebtsecuritiesaccumulatedgrossunrealizedgainbeforetax": 583000000,
			"availableforsaledebtsecuritiesaccumulatedgrossunrealizedlossbeforetax": 4635000000,
			"availableforsalesecuritiesdebtsecurities": 128056000000,
			"cashcashequivalentsandmarketablesecuritiescost": 160600000000,
			"cashequivalentsandmarketablesecuritiesaccumulatedgrossunrealizedgainbeforetax": 688000000,
			"cashequivalentsandmarketablesecuritiesaccumulatedgrossunrealizedlossbeforetax": 4638000000,
			"cashcashequivalentsandmarketablesecurities": 156650000000,
			"restrictedcashandcashequivalents": 2600000000,
			"debtsecuritiesavailableforsalerestricted": 13200000000,
			"debtsecuritiesavailableforsalematurityallocatedandsinglematuritydaterollingafteronethroughfiveyearspercentage": 0.14,
			"debtsecuritiesavailableforsalematurityallocatedandsinglematuritydaterollingafterfivethroughtenyearspercentage": 0.09,
			"debtsecuritiesavailableforsalematurityallocatedandsinglematuritydaterollingaftertenyearspercentage": 0.77,
			"maximumlengthoftimeforeigncurrencycashflowhedge": "P18Y",
			"concentrationriskpercentage1": 0.23,
			"numberofsignificantvendors": 2,
			"derivativenotionalamount": 91493000000,
			"hedgedassetstatementoffinancialpositionextensibleenumeration": "http://fasb.org/us-gaap/2024#MarketableSecuritiesCurrent http://fasb.org/us-gaap/2024#MarketableSecuritiesNoncurrent",
			"hedgedliabilityfairvaluehedge": 13505000000,
			"hedgedliabilitystatementoffinancialpositionextensibleenumeration": "http://fasb.org/us-gaap/2024#LongTermDebtCurrent http://fasb.org/us-gaap/2024#LongTermDebtNoncurrent",
			"propertyplantandequipmentgross": 119128000000,
			"accumulateddepreciationdepletionandamortizationpropertyplantandequipment": 73448000000,
			"depreciation": 8200000000,
			"deferredincometaxassetsnet": 19499000000,
			"otherassetsmiscellaneousnoncurrent": 55335000000,
			"accruedincometaxescurrent": 1200000000,
			"otheraccruedliabilitiescurrent": 51703000000,
			"accruedincometaxesnoncurrent": 9254000000,
			"otheraccruedliabilitiesnoncurrent": 36634000000,
			"totalrestrictedcashcashequivalentsandavailableforsaledebtsecurities": 15800000000,
			"currentforeigntaxexpensebenefit": 25483000000,
			"currentfederaltaxexpensebenefit": 5571000000,
			"unrecognizedtaxbenefitsdecreasesresultingfromsettlementswithtaxingauthorities": 1070000000,
			"incomelossfromcontinuingoperationsbeforeincometaxesforeign": 77300000000,
			"effectiveincometaxratereconciliationatfederalstatutoryincometaxrate": 0.21,
			"deferredtaxassetstaxcreditcarryforwardsforeign": 5100000000,
			"deferredtaxassetstaxcreditcarryforwardsresearch": 3600000000,
			"unrecognizedtaxbenefits": 22038000000,
			"unrecognizedtaxbenefitsthatwouldimpacteffectivetaxrate": 10800000000,
			"decreaseinunrecognizedtaxbenefitsisreasonablypossible": 13000000000,
			"deferredfederalincometaxexpensebenefit": -3080000000,
			"federalincometaxexpensebenefitcontinuingoperations": 2491000000,
			"currentstateandlocaltaxexpensebenefit": 1726000000,
			"deferredstateandlocalincometaxexpensebenefit": -298000000,
			"stateandlocalincometaxexpensebenefitcontinuingoperations": 1428000000,
			"deferredforeignincometaxexpensebenefit": 347000000,
			"foreignincometaxexpensebenefitcontinuingoperations": 25830000000,
			"incometaxreconciliationincometaxexpensebenefitatfederalstatutoryincometaxrate": 25932000000,
			"incometaxreconciliationstateandlocalincometaxes": 1162000000,
			"effectiveincometaxratereconciliationimpactofthestateaiddecisionamount": 10246000000,
			"incometaxreconciliationforeignincometaxratedifferential": -5311000000,
			"incometaxreconciliationtaxcreditsresearch": 1397000000,
			"effectiveincometaxratereconciliationsharebasedcompensationexcesstaxbenefitamount": -893000000,
			"incometaxreconciliationotheradjustments": 10000000,
			"effectiveincometaxratecontinuingoperations": 0.241,
			"deferredtaxassetscapitalizedresearchanddevelopment": 10739000000,
			"deferredtaxassetstaxcreditcarryforwards": 8856000000,
			"deferredtaxassetstaxdeferredexpensereservesandaccruals": 6114000000,
			"deferredtaxassetsdeferredincome": 3413000000,
			"deferredtaxassetsleaseliabilities": 2410000000,
			"deferredtaxassetsothercomprehensiveloss": 1173000000,
			"deferredtaxassetsother": 2168000000,
			"deferredtaxassetsgross": 34873000000,
			"deferredtaxassetsvaluationallowance": 8866000000,
			"deferredtaxassetsnet": 26007000000,
			"deferredtaxliabilitiespropertyplantandequipment": 2551000000,
			"deferredtaxliabilitiesleasingarrangements": 2125000000,
			"deferredtaxliabilitiesminimumtaxonforeignearnings": 1674000000,
			"deferredtaxliabilitiesother": 455000000,
			"deferredincometaxliabilities": 6805000000,
			"deferredtaxassetsliabilitiesnet": 19202000000,
			"unrecognizedtaxbenefitsincreasesresultingfrompriorperiodtaxpositions": 1727000000,
			"unrecognizedtaxbenefitsdecreasesresultingfrompriorperiodtaxpositions": 386000000,
			"unrecognizedtaxbenefitsincreasesresultingfromcurrentperiodtaxpositions": 2542000000,
			"unrecognizedtaxbenefitsreductionsresultingfromlapseofapplicablestatuteoflimitations": 229000000,
			"lesseeoperatingandfinanceleasetermofcontract": "P10Y",
			"operatingleasecost": 2000000000,
			"variableleasecost": 13800000000,
			"operatingleasepayments": 1900000000,
			"rightofuseassetsobtainedinexchangeforoperatingandfinanceleaseliabilities": 1000000000,
			"operatingandfinanceleaseweightedaverageremainingleaseterm": "P10Y3M18D",
			"operatingandfinanceleaseweightedaveragediscountratepercent": 0.031,
			"unrecordedunconditionalpurchaseobligationbalancesheetamount": 11226000000,
			"lesseeoperatingandfinanceleaseleasenotyetcommencedtermofcontract": "P21Y",
			"operatingleaserightofuseasset": 10234000000,
			"operatingleaserightofuseassetstatementoffinancialpositionextensiblelist": "http://fasb.org/us-gaap/2024#OtherAssetsNoncurrent",
			"financeleaserightofuseasset": 1069000000,
			"financeleaserightofuseassetstatementoffinancialpositionextensiblelist": "http://fasb.org/us-gaap/2024#PropertyPlantAndEquipmentNet",
			"operatingandfinanceleaserightofuseasset": 11303000000,
			"operatingleaseliabilitycurrent": 1488000000,
			"operatingleaseliabilitycurrentstatementoffinancialpositionextensiblelist": "http://fasb.org/us-gaap/2024#OtherLiabilitiesCurrent",
			"operatingleaseliabilitynoncurrent": 10046000000,
			"operatingleaseliabilitynoncurrentstatementoffinancialpositionextensiblelist": "http://fasb.org/us-gaap/2024#OtherLiabilitiesNoncurrent",
			"financeleaseliabilitycurrent": 144000000,
			"financeleaseliabilitycurrentstatementoffinancialpositionextensiblelist": "http://fasb.org/us-gaap/2024#OtherLiabilitiesCurrent",
			"financeleaseliabilitynoncurrent": 752000000,
			"financeleaseliabilitynoncurrentstatementoffinancialpositionextensiblelist": "http://fasb.org/us-gaap/2024#OtherLiabilitiesNoncurrent",
			"operatingandfinanceleaseliability": 12430000000,
			"lesseeoperatingleaseliabilitypaymentsduenexttwelvemonths": 1820000000,
			"lesseeoperatingleaseliabilitypaymentsdueyeartwo": 1914000000,
			"lesseeoperatingleaseliabilitypaymentsdueyearthree": 1674000000,
			"lesseeoperatingleaseliabilitypaymentsdueyearfour": 1360000000,
			"lesseeoperatingleaseliabilitypaymentsdueyearfive": 1187000000,
			"lesseeoperatingleaseliabilitypaymentsdueafteryearfive": 5563000000,
			"lesseeoperatingleaseliabilitypaymentsdue": 13518000000,
			"lesseeoperatingleaseliabilityundiscountedexcessamount": 1984000000,
			"operatingleaseliability": 11534000000,
			"financeleaseliabilitypaymentsduenexttwelvemonths": 171000000,
			"financeleaseliabilitypaymentsdueyeartwo": 131000000,
			"financeleaseliabilitypaymentsdueyearthree": 59000000,
			"financeleaseliabilitypaymentsdueyearfour": 38000000,
			"financeleaseliabilitypaymentsdueyearfive": 36000000,
			"financeleaseliabilitypaymentsdueafteryearfive": 837000000,
			"financeleaseliabilitypaymentsdue": 1272000000,
			"financeleaseliabilityundiscountedexcessamount": 376000000,
			"financeleaseliability": 896000000,
			"lesseeoperatingandfinanceleaseliabilitytobepaidyearone": 1991000000,
			"lesseeoperatingandfinanceleaseliabilitytobepaidyeartwo": 2045000000,
			"lesseeoperatingandfinanceleaseliabilitytobepaidyearthree": 1733000000,
			"lesseeoperatingandfinanceleaseliabilitytobepaidyearfour": 1398000000,
			"lesseeoperatingandfinanceleaseliabilitytobepaidyearfive": 1223000000,
			"lesseeoperatingandfinanceleaseliabilitytobepaidafteryearfive": 6400000000,
			"lesseeoperatingandfinanceleaseliabilitytobepaid": 14790000000,
			"lesseeoperatingandfinanceleaseliabilityundiscountedexcessamount": 2360000000,
			"debtinstrumentterm": "P9M",
			"shorttermdebtweightedaverageinterestrate": 0.05,
			"longtermdebtfairvalue": 88400000000,
			"proceedsfromrepaymentsofshorttermdebtmaturinginthreemonthsorless": 3960000000,
			"debtinstrumentcarryingamount": 97341000000,
			"debtinstrumentunamortizeddiscountpremiumanddebtissuancecostsnet": 321000000,
			"hedgeaccountingadjustmentsrelatedtolongtermdebt": 358000000,
			"longtermdebt": 96662000000,
			"debtinstrumentmaturityyearrangestart": 2024,
			"debtinstrumentmaturityyearrangeend": 2062,
			"debtinstrumentinterestratestatedpercentage": 0.0485,
			"debtinstrumentinterestrateeffectivepercentage": 0.0665,
			"longtermdebtmaturitiesrepaymentsofprincipalinnexttwelvemonths": 10930000000,
			"longtermdebtmaturitiesrepaymentsofprincipalinyeartwo": 12342000000,
			"longtermdebtmaturitiesrepaymentsofprincipalinyearthree": 9936000000,
			"longtermdebtmaturitiesrepaymentsofprincipalinyearfour": 7800000000,
			"longtermdebtmaturitiesrepaymentsofprincipalinyearfive": 5153000000,
			"longtermdebtmaturitiesrepaymentsofprincipalafteryearfive": 51180000000,
			"stockrepurchasedandretiredduringperiodshares": 499372000,
			"stockissuedduringperiodsharessharebasedpaymentarrangementnetofshareswithheldfortaxes": 66097000,
			"sharebasedcompensationarrangementbysharebasedpaymentawardawardvestingperiod1": "P4Y",
			"sharebasedcompensationarrangementbysharebasedpaymentawardequityinstrumentsotherthanoptionsnumberofsharesofcommonstockissuedperunituponvesting": 1,
			"sharebasedcompensationarrangementbysharebasedpaymentawardequityinstrumentsotherthanoptionsvestedinperiodtotalfairvalue": 15800000000,
			"sharespaidfortaxwithholdingforsharebasedcompensation": 31000000,
			"employeeservicesharebasedcompensationnonvestedawardstotalcompensationcostnotyetrecognized": 19400000000,
			"employeeservicesharebasedcompensationnonvestedawardstotalcompensationcostnotyetrecognizedperiodforrecognition1": "P2Y4M24D",
			"sharebasedcompensationarrangementbysharebasedpaymentawardequityinstrumentsotherthanoptionsnonvestednumber": 163326000,
			"sharebasedcompensationarrangementbysharebasedpaymentawardequityinstrumentsotherthanoptionsgrantsinperiod": 80456000,
			"sharebasedcompensationarrangementbysharebasedpaymentawardequityinstrumentsotherthanoptionsvestedinperiod": 87633000,
			"sharebasedcompensationarrangementbysharebasedpaymentawardequityinstrumentsotherthanoptionsforfeitedinperiod": 9744000,
			"sharebasedcompensationarrangementbysharebasedpaymentawardequityinstrumentsotherthanoptionsnonvestedweightedaveragegrantdatefairvalue": 158.73,
			"sharebasedcompensationarrangementbysharebasedpaymentawardequityinstrumentsotherthanoptionsgrantsinperiodweightedaveragegrantdatefairvalue": 173.78,
			"sharebasedcompensationarrangementbysharebasedpaymentawardequityinstrumentsotherthanoptionsvestedinperiodweightedaveragegrantdatefairvalue": 127.59,
			"sharebasedcompensationarrangementbysharebasedpaymentawardequityinstrumentsotherthanoptionsforfeituresweightedaveragegrantdatefairvalue": 140.8,
			"sharebasedcompensationarrangementbysharebasedpaymentawardequityinstrumentsotherthanoptionsaggregateintrinsicvaluenonvested": 37204000000,
			"allocatedsharebasedcompensationexpense": 11688000000,
			"employeeservicesharebasedcompensationtaxbenefitfromcompensationexpense": 3350000000,
			"unrecordedunconditionalpurchaseobligationbalanceonfirstanniversary": 3206000000,
			"unrecordedunconditionalpurchaseobligationbalanceonsecondanniversary": 2440000000,
			"unrecordedunconditionalpurchaseobligationbalanceonthirdanniversary": 1156000000,
			"unrecordedunconditionalpurchaseobligationbalanceonfourthanniversary": 3121000000,
			"unrecordedunconditionalpurchaseobligationbalanceonfifthanniversary": 633000000,
			"unrecordedunconditionalpurchaseobligationdueafterfiveyears": 670000000,
			"othergeneralandadministrativeexpense": 7458000000,
			"noncurrentassets": 45680000000,
			"trdarrsecuritiesaggavailamt": 100000,
			"insidertrdpoliciesprocadoptedflag": "true"
		}
	}
]

```
