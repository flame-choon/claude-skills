# FMP Company Information Endpoints

Base: `https://financialmodelingprep.com/stable`

---

## 엔드포인트 선택 가이드

| 상황 | 사용할 엔드포인트 |
|------|----------------|
| 기업 기본 정보 조회 (시총, 섹터, CEO 등) | `GET /profile` |
| CIK로 기업 정보 조회 | `GET /profile-cik` |
| 기업 발행 채권/노트 조회 | `GET /company-notes` |
| 동종 업종 경쟁사 목록 | `GET /stock-peers` |
| 상장폐지 기업 목록 | `GET /delisted-companies` |
| 현재 임직원 수 조회 | `GET /employee-count` |
| 임직원 수 추이 분석 | `GET /historical-employee-count` |
| 특정 종목 현재 시가총액 | `GET /market-capitalization` |
| 여러 종목 시가총액 한 번에 | `GET /market-capitalization-batch` |
| 시가총액 역사적 변화 추적 | `GET /historical-market-capitalization` |
| 유통주식수 및 유동성 분석 | `GET /shares-float` |
| 전체 종목 유통주식 데이터 | `GET /shares-float-all` |
| 최근 M&A 동향 파악 | `GET /mergers-acquisitions-latest` |
| 특정 기업 M&A 검색 | `GET /mergers-acquisitions-search` |
| 주요 임원 정보 조회 | `GET /key-executives` |
| 임원 보수 상세 내역 | `GET /governance-executive-compensation` |
| 업종별 임원 보수 벤치마크 | `GET /executive-compensation-benchmark` |

> ⚠️ `page`, `limit` 파라미터 미지정 시 대량 데이터 반환 가능 → **항상 페이지네이션 지정 권장**

---

## 1. Company Profile Data — 기업 기본 프로필

```
GET /profile
```

**언제 사용:** 기업의 핵심 정보(시총, 주가, 섹터, CEO, 설명 등)를 한 번에 조회할 때. 가장 범용적인 기업 정보 엔드포인트.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 (예: AAPL) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| price | float | 현재 주가 |
| marketCap | integer | 시가총액 |
| beta | float | 베타 (시장 대비 변동성) |
| lastDividend | float | 최근 주당 배당금 |
| range | string | 52주 주가 범위 (최저-최고) |
| change | float | 전일 대비 주가 변동액 |
| changePercentage | float | 전일 대비 주가 변동률 (%) |
| volume | integer | 당일 거래량 |
| averageVolume | integer | 평균 거래량 |
| companyName | string | 회사명 |
| currency | string | 거래 통화 (예: USD) |
| cik | string | SEC CIK 번호 |
| isin | string | 국제 증권 식별 번호 (ISIN) |
| cusip | string | 미국 증권 식별 번호 (CUSIP) |
| exchangeFullName | string | 상장 거래소 전체명 |
| exchange | string | 상장 거래소 코드 |
| industry | string | 산업 분류 |
| website | string | 공식 웹사이트 URL |
| description | string | 회사 사업 개요 |
| ceo | string | 최고경영자(CEO) 이름 |
| sector | string | 섹터 분류 |
| country | string | 본사 소재 국가 코드 |
| fullTimeEmployees | string | 정규직 임직원 수 |
| phone | string | 대표 전화번호 |
| address | string | 본사 주소 |
| city | string | 본사 도시 |
| state | string | 본사 주(State) |
| zip | string | 우편번호 |
| image | string | 회사 로고 이미지 URL |
| ipoDate | string | IPO 상장일 (YYYY-MM-DD) |
| defaultImage | boolean | 기본 이미지 사용 여부 |
| isEtf | boolean | ETF 여부 |
| isActivelyTrading | boolean | 현재 거래 활성 여부 |
| isAdr | boolean | ADR(미국 예탁 증서) 여부 |
| isFund | boolean | 펀드 여부 |


```python
# GET /stable/profile?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"price": 232.8,
		"marketCap": 3500823120000,
		"beta": 1.24,
		"lastDividend": 0.99,
		"range": "164.08-260.1",
		"change": 4.79,
		"changePercentage": 2.1008,
		"volume": 0,
		"averageVolume": 50542058,
		"companyName": "Apple Inc.",
		"currency": "USD",
		"cik": "0000320193",
		"isin": "US0378331005",
		"cusip": "037833100",
		"exchangeFullName": "NASDAQ Global Select",
		"exchange": "NASDAQ",
		"industry": "Consumer Electronics",
		"website": "https://www.apple.com",
		"description": "Apple Inc. designs, manufactures, and markets smartphones, personal computers, tablets, wearables, and accessories worldwide. The company offers iPhone, a line of smartphones; Mac, a line of personal computers; iPad, a line of multi-purpose tablets; and wearables, home, and accessories comprising AirPods, Apple TV, Apple Watch, Beats products, and HomePod. It also provides AppleCare support and cloud services; and operates various platforms, including the App Store that allow customers to discov...",
		"ceo": "Mr. Timothy D. Cook",
		"sector": "Technology",
		"country": "US",
		"fullTimeEmployees": "164000",
		"phone": "(408) 996-1010",
		"address": "One Apple Park Way",
		"city": "Cupertino",
		"state": "CA",
		"zip": "95014",
		"image": "https://images.financialmodelingprep.com/symbol/AAPL.png",
		"ipoDate": "1980-12-12",
		"defaultImage": false,
		"isEtf": false,
		"isActivelyTrading": true,
		"isAdr": false,
		"isFund": false
	}
]

```

---

## 2. Company Profile by CIK — CIK로 기업 조회

```
GET /profile-cik
```

**언제 사용:** 티커 심볼 대신 SEC CIK 번호로 기업을 조회할 때. SEC 문서 분석 워크플로우에 유용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| cik | string | ✅ | CIK 번호 (예: 320193) |
| apikey | string | ✅ | API 키 |

**응답 필드:** `/profile`과 동일

```python
# GET /stable/profile-cik?cik=320193
params = {"cik": "320193"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"price": 232.8,
		"marketCap": 3500823120000,
		"beta": 1.24,
		"lastDividend": 0.99,
		"range": "164.08-260.1",
		"change": 4.79,
		"changePercentage": 2.1008,
		"volume": 0,
		"averageVolume": 50542058,
		"companyName": "Apple Inc.",
		"currency": "USD",
		"cik": "0000320193",
		"isin": "US0378331005",
		"cusip": "037833100",
		"exchangeFullName": "NASDAQ Global Select",
		"exchange": "NASDAQ",
		"industry": "Consumer Electronics",
		"website": "https://www.apple.com",
		"description": "Apple Inc. designs, manufactures, and markets smartphones, personal computers, tablets, wearables, and accessories worldwide. The company offers iPhone, a line of smartphones; Mac, a line of personal computers; iPad, a line of multi-purpose tablets; and wearables, home, and accessories comprising AirPods, Apple TV, Apple Watch, Beats products, and HomePod. It also provides AppleCare support and cloud services; and operates various platforms, including the App Store that allow customers to discov...",
		"ceo": "Mr. Timothy D. Cook",
		"sector": "Technology",
		"country": "US",
		"fullTimeEmployees": "164000",
		"phone": "(408) 996-1010",
		"address": "One Apple Park Way",
		"city": "Cupertino",
		"state": "CA",
		"zip": "95014",
		"image": "https://images.financialmodelingprep.com/symbol/AAPL.png",
		"ipoDate": "1980-12-12",
		"defaultImage": false,
		"isEtf": false,
		"isActivelyTrading": true,
		"isAdr": false,
		"isFund": false
	}
]

```

---

## 3. Company Notes — 기업 발행 채권/노트

```
GET /company-notes
```

**언제 사용:** 기업이 발행한 채권(Notes)의 목록과 상장 거래소를 파악할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| cik | string | SEC CIK 번호 |
| symbol | string | 티커 심볼 |
| title | string | 채권/노트 명칭 |
| exchange | string | 상장 거래소 |

```python
# GET /stable/company-notes?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"cik": "0000320193",
		"symbol": "AAPL",
		"title": "1.000% Notes due 2022",
		"exchange": "NASDAQ"
	}
]
```

---

## 4. Stock Peer Comparison — 동종 업종 경쟁사

```
GET /stock-peers
```

**언제 사용:** 특정 종목의 경쟁사/동종 업종 기업 목록을 조회할 때. 동일 거래소 + 동일 섹터 기준.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| companyName | string | 회사명 |
| price | float | 현재 주가 |
| mktCap | integer | 시가총액 |

```python
# GET /stable/stock-peers?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
  {
    "symbol": "GOOGL",
    "companyName": "Alphabet Inc.",
    "price": 311.49,
    "mktCap": 3768094838064
  }
]
```

---

# 5. Delisted Companies — 상장폐지 기업 목록

```
GET /delisted-companies
```

**언제 사용:** 미국 거래소에서 상장폐지된 기업 목록을 조회할 때. 리스크 스크리닝 용도.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| page | integer | ❌ | 페이지 번호 (기본: 0) |
| limit | integer | ❌ | 페이지당 결과 수 (기본: 100) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| companyName | string | 회사명 |
| exchange | string | 거래소 |
| ipoDate | string | IPO 날짜 (YYYY-MM-DD) |
| delistedDate | string | 상장폐지 날짜 (YYYY-MM-DD) |

```python
# GET /stable/delisted-companies?page=0&limit=100
params = {"page": 0, "limit": 100}

# 응답 예시
[
	{
		"symbol": "BRQSF",
		"companyName": "Borqs Technologies, Inc.",
		"exchange": "PNK",
		"ipoDate": "2017-08-24",
		"delistedDate": "2025-02-03"
	}
]
```

---

## 6. Company Employee Count — 현재 임직원 수

```
GET /employee-count
```

**언제 사용:** 기업의 최신 임직원 수와 SEC 제출 기준일을 조회할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| limit | integer | ❌ | 페이지당 결과 수 (기본: 100) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| cik | string | CIK 번호 |
| acceptanceTime | string | SEC 제출 수락 시각 |
| periodOfReport | string | 보고 기준일 (YYYY-MM-DD) |
| companyName | string | 회사명 |
| employeeCount | integer | 임직원 수 |
| formType | string | 제출 양식 유형 (예: 10-K) |
| filingDate | string | 제출일 (YYYY-MM-DD) |
| source | string | SEC 원문 링크 URL |

```python
# GET /stable/employee-count?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"cik": "0000320193",
		"acceptanceTime": "2024-11-01 06:01:36",
		"periodOfReport": "2024-09-28",
		"companyName": "Apple Inc.",
		"formType": "10-K",
		"filingDate": "2024-11-01",
		"employeeCount": 164000,
		"source": "https://www.sec.gov/Archives/edgar/data/320193/000032019324000123/0000320193-24-000123-index.htm"
	}
]

```

---

## 7. Historical Employee Count — 임직원 수 추이

```
GET /historical-employee-count
```

**언제 사용:** 기업의 임직원 수 변화를 시계열로 분석할 때. 성장세 또는 구조조정 트렌드 파악.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| limit | integer | ❌ | 페이지당 결과 수 (기본: 100) |
| apikey | string | ✅ | API 키 |

**응답 필드** `/employee-count`와 동일 (복수의 기간 데이터 반환)

```python
# GET /stable/historical-employee-count?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시 (기간별 리스트)
[
	{
		"symbol": "AAPL",
		"cik": "0000320193",
		"acceptanceTime": "2024-11-01 06:01:36",
		"periodOfReport": "2024-09-28",
		"companyName": "Apple Inc.",
		"formType": "10-K",
		"filingDate": "2024-11-01",
		"employeeCount": 164000,
		"source": "https://www.sec.gov/Archives/edgar/data/320193/000032019324000123/0000320193-24-000123-index.htm"
	}
]
```

---

## 8. Company Market Cap — 현재 시가총액

```
GET /market-capitalization
```

**언제 사용:** 특정 날짜 기준 시가총액을 조회할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| date | string | 기준일 (YYYY-MM-DD) |
| marketCap | integer | 시가총액 (USD) |

```python
# GET /stable/market-capitalization?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[{"symbol": "AAPL", "date": "2024-01-02", "marketCap": 3412000000000}]
```

---

## 9. Batch Market Cap — 여러 종목 시가총액 일괄 조회

```
GET /market-capitalization-batch
```

**언제 사용:** 여러 종목의 시가총액을 한 번의 요청으로 비교할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbols | string | ✅ | 쉼표로 구분된 티커 목록 (예: AAPL,MSFT,GOOG) |
| apikey | string | ✅ | API 키 |

**응답 필드** `/market-capitalization`과 동일 (복수 종목 배열)

```python
# GET /stable/market-capitalization-batch?symbols=AAPL,MSFT,GOOG
params = {"symbols": "AAPL,MSFT,GOOG"}

# 응답 예시
[
  {"symbol": "AAPL", "date": "2024-01-02", "marketCap": 3412000000000},
  {"symbol": "MSFT", "date": "2024-01-02", "marketCap": 2893000000000},
  {"symbol": "GOOG", "date": "2024-01-02", "marketCap": 1751000000000}
]
```

---

## 10. Historical Market Cap — 시가총액 역사적 추이

```
GET /historical-market-capitalization
```

**언제 사용:** 시가총액 변화 추이를 장기간 분석할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| from | string | ❌ | 시작일 YYYY-MM-DD |
| to | string | ❌ | 종료일 YYYY-MM-DD |
| limit | integer | ❌ | 반환할 최대 건수 |
| apikey | string | ✅ | API 키 |

**응답 필드:** `/market-capitalization`과 동일 (시계열 배열)

```python
# GET /stable/historical-market-capitalization?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
  {
    "symbol": "AAPL",
    "date": "2026-02-23",
    "marketCap": 3925664696440
  },
  {
    "symbol": "AAPL",
    "date": "2026-02-20",
    "marketCap": 3902067643640
  }
]

```

---

## 11. Shares Float — 유통주식수 및 유동성

```
GET /shares-float
```

**언제 사용:** 특정 종목의 유통주식수, 공개유통비율을 조회해 유동성을 평가할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| date | string | 기준일 (YYYY-MM-DD) |
| freeFloat | float | 공개 유통 비율 (%) |
| floatShares | integer | 유통 주식 수 |
| outstandingShares | integer | 발행 주식 총수 |

```python
# GET /stable/shares-float?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"date": "2025-02-04 17:01:35",
		"freeFloat": 99.9095,
		"floatShares": 15024290700,
		"outstandingShares": 15037900000
	}
]

```

---

# 12. All Shares Float — 전체 종목 유통주식 데이터

```
GET /shares-float-all
```

**언제 사용:** 전 종목의 유통주식 데이터를 일괄 조회할 때. 스크리너 구축 용도.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| page | integer | ❌ | 페이지 번호 (기본: 0) |
| limit | integer | ❌ | 페이지당 결과 수 (기본: 1000) |
| apikey | string | ✅ | API 키 |

**응답 필드** `/shares-float`과 동일

> ⚠️ 전체 데이터 크기가 매우 크므로 반드시 `page` + `limit`으로 페이지네이션 사용


---

## 13. Latest Mergers & Acquisitions — 최근 M&A 목록

```
GET /mergers-acquisitions-latest
```

**언제 사용:** 최근 발생한 M&A 거래 전체 목록을 조회할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| page | integer | ❌ | 페이지 번호 (기본: 0) |
| limit | integer | ❌ | 페이지당 결과 수 (기본: 100) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 인수 대상 기업 티커 |
| cik | string | 인수 대상 회사의 CIK 번호 |
| companyName | string | 인수 대상 회사명 |
| targetedCompanyName | string | 피인수 기업명 |
| targetedCik | string | 피인수 기업의 CIK 번호 |
| targetedSymbol | string | 피인수 기업 티커 |
| transactionDate | string | 거래일 (YYYY-MM-DD) |
| acceptedDate | string | SEC 수락 날짜 |
| link | string | SEC 원문 링크 URL |

> ⚠️ 전체 데이터 크기가 매우 크므로 반드시 `page` + `limit`으로 페이지네이션 사용

```python
# GET /stable/mergers-acquisitions-latest?page=0&limit=20
params = {"page": 0, "limit": 20}

# 응답 예시
[
	{
		"symbol": "NLOK",
		"companyName": "NortonLifeLock Inc.",
		"cik": "0000849399",
		"targetedCompanyName": "MoneyLion Inc.",
		"targetedCik": "0001807846",
		"targetedSymbol": "ML",
		"transactionDate": "2025-02-03",
		"acceptedDate": "2025-02-03 06:01:10",
		"link": "https://www.sec.gov/Archives/edgar/data/849399/000114036125002752/ny20039778x6_s4.htm"
	}
]

```

---

## 14. Search Mergers & Acquisitions — M&A 검색

```
GET /mergers-acquisitions-search
```

**언제 사용:** 특정 기업 이름으로 관련 M&A 거래를 검색할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| name | string | ✅ | 검색할 기업명 (부분 일치) |
| apikey | string | ✅ | API 키 |

**응답 필드** `/mergers-acquisitions-latest`와 동일

```python
# GET /stable/mergers-acquisitions-search?name=Apple
params = {"name": "Apple"}

# 응답 예시
[
	{
		"symbol": "PEGY",
		"companyName": "Pineapple Energy Inc.",
		"cik": "0000022701",
		"targetedCompanyName": "Communications Systems, Inc.",
		"targetedCik": "0000022701",
		"targetedSymbol": "JCS",
		"transactionDate": "2021-11-12",
		"acceptedDate": "2021-11-12 09:54:22",
		"link": "https://www.sec.gov/Archives/edgar/data/22701/000089710121000932/a211292_s-4.htm"
	}
]

```

---

## 15. Company Executives — 주요 임원 정보

```
GET /key-executives
```

**언제 사용:** 기업의 C-레벨 임원 명단, 직책, 보수 정보를 조회할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| title | string | 직책 |
| name | string | 임원 이름 |
| pay | integer | 연간 보수 (USD) |
| currencyPay | string | 보수 통화 |
| gender | string | 성별 |
| yearBorn | integer | 출생연도 |
| active | string | 현 직책 유지 여부 |

```python
# GET /stable/key-executives?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
  {
    "title": "Chief Executive Officer & Director",
    "name": "Timothy D. Cook",
    "pay": 16520856,
    "currencyPay": "USD",
    "gender": "male",
    "yearBorn": 1961,
    "titleSince": null,
    "active": true
  }
]

```

---

# 16. Executive Compensation — 임원 보수 상세

```
GET /governance-executive-compensation
```

**언제 사용:** 임원별 급여, 주식 보상, 총 보수 내역을 SEC 제출 기준으로 상세 조회할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 티커 심볼 |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| cik | string | SEC CIK 번호 |
| symbol | string | 티커 심볼 |
| companyName | string | 회사명 |
| filingDate | string | SEC 제출일 (YYYY-MM-DD) |
| acceptedDate | string | SEC 수락일시 (YYYY-MM-DD HH:MM:SS) |
| nameAndPosition | string | 임원 이름 및 직책 |
| year | integer | 보상 기준 연도 |
| salary | integer | 기본급 |
| bonus | integer | 보너스 |
| stockAward | integer | 주식 보상 (Stock Award) |
| optionAward | integer | 스톡옵션 보상 (Option Award) |
| incentivePlanCompensation | integer | 인센티브 플랜 보상 |
| allOtherCompensation | integer | 기타 보상 합계 |
| total | integer | 총 보상액 |
| link | string | SEC 공시 원문 링크 URL |

```python
# GET /stable/governance-executive-compensation?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"cik": "0000320193",
		"symbol": "AAPL",
		"companyName": "Apple Inc.",
		"filingDate": "2025-01-10",
		"acceptedDate": "2025-01-10 16:31:18",
		"nameAndPosition": "Kate Adams Senior Vice President, General Counsel and Secretary",
		"year": 2023,
		"salary": 1000000,
		"bonus": 0,
		"stockAward": 22323641,
		"optionAward": 0,
		"incentivePlanCompensation": 3571150,
		"allOtherCompensation": 46914,
		"total": 26941705,
		"link": "https://www.sec.gov/Archives/edgar/data/320193/000130817925000008/0001308179-25-000008-index.htm"
	}
]

```

---     

## 17. Executive Compensation Benchmark — 업종별 임원 보수 벤치마크

```
GET /executive-compensation-benchmark
```

**언제 사용:** 특정 업종의 평균 임원 보수를 조회하여 특정 기업 보수 수준과 비교할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| year | integer | ❌ | 기준 연도 (미지정 시 최신) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| industryTitle | string | 업종명 |
| year | integer | 기준 연도 |
| averageCompensation | float | 평균 총 보수 (USD) |

```python
# GET /stable/executive-compensation-benchmark?year=2023
params = {"year": 2023}

# 응답 예시
[
	{
		"industryTitle": "ABRASIVE, ASBESTOS & MISC NONMETALLIC MINERAL PRODS",
		"year": 2023,
		"averageCompensation": 694313.1666666666
	}
]

```

