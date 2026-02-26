# FMP Company Search Endpoints

Base: `https://financialmodelingprep.com/stable`

---

## 엔드포인트 선택 가이드

| 상황 | 사용할 엔드포인트 |
|------|----------------|
| 티커 심볼로 종목 검색 | `GET /search-symbol` |
| 회사명으로 종목 검색 | `GET /search-name` |
| CIK 번호로 SEC 등록 기업 조회 | `GET /search-cik` |
| CUSIP 번호로 증권 조회 | `GET /search-cusip` |
| ISIN 번호로 증권 조회 | `GET /search-isin` |
| 시가총액·섹터·가격 등 조건으로 종목 필터링 | `GET /company-screener` |
| 특정 심볼이 상장된 모든 거래소 조회 | `GET /search-exchange-variants` |

> ⚠️ `search-symbol`, `search-name`은 `query` 파라미터가 필수이며 부분 문자열 검색을 지원.
> `search-isin`, `search-cusip`, `search-cik`는 각 식별자를 정확하게 입력해야 함.

---

## 1. Stock Symbol Search — 심볼 검색

```
GET /search-symbol
```

**언제 사용:** 티커 심볼 또는 회사명 일부를 입력해 일치하는 종목을 검색할 때. 미국 주식, 해외 주식, ETF를 포함한 전 세계 거래소 종목을 검색할 때 가장 범용적인 검색 엔드포인트.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| query | string | ✅ | 검색어 (티커 심볼 또는 회사명 일부, 예: AAPL) |
| limit | number | ❌ | 반환할 최대 결과 수 (기본: 50) |
| exchange | string | ❌ | 거래소 필터 (예: NASDAQ, NYSE, AMEX, ETF, CRYPTO 등) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| name | string | 종목/회사명 |
| currency | string | 거래 통화 (예: USD) |
| exchangeFullName | string | 거래소 전체명 (예: NASDAQ Global Select) |
| exchange | string | 거래소 약칭 (예: NASDAQ) |

```python
# GET /stable/search-symbol?query=AAPL&limit=10&exchange=NASDAQ
params = {"query": "AAPL", "limit": 10, "exchange": "NASDAQ"}

# 응답 예시
[
  {
    "symbol": "AAPL",
    "name": "Apple Inc.",
    "currency": "USD",
    "exchangeFullName": "NASDAQ Global Select",
    "exchange": "NASDAQ"
  }
]
```

---

## 2. Company Name Search — 회사명 검색

```
GET /search-name
```

**언제 사용:** 티커 심볼을 모를 때 회사명이나 자산명 일부를 입력해 심볼을 찾을 때. 주식·ETF 모두 검색 가능하며, 자동완성 기능 구현에 적합.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| query | string | ✅ | 검색할 회사명 또는 자산명 일부 (예: AA) |
| limit | number | ❌ | 반환할 최대 결과 수 (기본: 50) |
| exchange | string | ❌ | 거래소 필터 (예: NASDAQ, NYSE 등) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| name | string | 종목/회사명 |
| currency | string | 거래 통화 (예: USD) |
| exchangeFullName | string | 거래소 전체명 (예: CCC) |
| exchange | string | 거래소 약칭 (예: CRYPTO) |

```python
# GET /stable/search-name?query=Apple&limit=10
params = {"query": "Apple", "limit": 10}

# 응답 예시
[
  {
    "symbol": "AAPL",
    "name": "Apple Inc.",
    "currency": "USD",
    "exchangeFullName": "NASDAQ Global Select",
    "exchange": "NASDAQ"
  }
]
```

---

## 3. CIK Search — CIK 번호로 SEC 등록 기업 조회

```
GET /search-cik
```

**언제 사용:** SEC(미국 증권거래위원회)의 CIK(Central Index Key) 번호로 기업의 심볼과 상장 정보를 조회할 때. SEC 공시 데이터 분석이나 규정 준수 워크플로우에서 CIK → 티커 매핑이 필요할 때.

> ⚠️ CIK는 특정 증권이 아닌 **법인(회사)** 을 식별하는 코드. 다중 주식 클래스가 있는 기업은 복수 결과를 반환할 수 있음.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| cik | string | ✅ | SEC CIK 번호 (예: 320193) |
| limit | number | ❌ | 반환할 최대 결과 수 (기본: 50) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| companyName | string | 기업명 |
| cik | string | SEC CIK 번호 |
| exchangeFullName | string | 거래소 전체명 (예: NASDAQ Global Select) |
| exchange | string | 거래소 약칭 (예: NASDAQ) |
| currency | string | 거래 통화 (예: USD) |

```python
# GET /stable/search-cik?cik=320193
params = {"cik": "320193"}

# 응답 예시
[
  {
    "symbol": "AAPL",
    "companyName": "Apple Inc.",
    "cik": "0000320193",
    "exchangeFullName": "NASDAQ Global Select",
    "exchange": "NASDAQ",
    "currency": "USD"
  }
]
```

---

## 4. CUSIP Search — CUSIP 번호로 증권 조회

```
GET /search-cusip
```

**언제 사용:** CUSIP(미국 증권 식별 코드) 9자리 번호로 해당 증권의 심볼과 시가총액을 조회할 때. 주식·채권·펀드 등 다양한 미국 증권 유형 식별에 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| cusip | string | ✅ | CUSIP 번호 9자리 (예: 037833100) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| companyName | string | 회사명 |
| cusip | string | CUSIP 번호 |
| marketCap | number | 시가총액 (USD) |

```python
# GET /stable/search-cusip?cusip=037833100
params = {"cusip": "037833100"}

# 응답 예시
[
  {
    "symbol": "AAPL",
    "companyName": "Apple Inc.",
    "cusip": "037833100",
    "marketCap": 3542555295744
  }
]
```

---

## 5. ISIN Search — ISIN 번호로 증권 조회

```
GET /search-isin
```

**언제 사용:** ISIN(국제 증권 식별 번호)으로 특정 증권의 티커 심볼과 시가총액을 조회할 때. 국제 데이터셋 통합이나 글로벌 증권 식별에 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| isin | string | ✅ | ISIN 번호 (예: US0378331005) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| name | string | 종목/회사명 |
| isin | string | ISIN 번호 |
| marketCap | number | 시가총액 (USD) |

```python
# GET /stable/search-isin?isin=US0378331005
params = {"isin": "US0378331005"}

# 응답 예시
[
  {
    "symbol": "AAPL",
    "name": "Apple Inc.",
    "isin": "US0378331005",
    "marketCap": 3427916386000
  }
]
```

---

## 6. Stock Screener — 조건 기반 종목 필터링

```
GET /company-screener
```

**언제 사용:** 시가총액·주가·베타·배당·거래량·섹터·국가 등 다양한 조건을 조합해 투자 전략에 맞는 종목을 필터링할 때. 모든 파라미터가 선택사항이며 원하는 조건만 조합해 사용 가능.

> ⚠️ `exchange` 파라미터는 **Limited Access** — 유료 플랜 필요.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| marketCapMoreThan | number | ❌ | 시가총액 하한 (예: 1000000) |
| marketCapLowerThan | number | ❌ | 시가총액 상한 (예: 1000000000) |
| sector | string | ❌ | 섹터 필터 (예: Technology) |
| industry | string | ❌ | 산업 필터 (예: Consumer Electronics) |
| betaMoreThan | number | ❌ | 베타 하한 (예: 0.5) |
| betaLowerThan | number | ❌ | 베타 상한 (예: 1.5) |
| priceMoreThan | number | ❌ | 주가 하한 (예: 10) |
| priceLowerThan | number | ❌ | 주가 상한 (예: 200) |
| dividendMoreThan | number | ❌ | 연간 배당 하한 (예: 0.5) |
| dividendLowerThan | number | ❌ | 연간 배당 상한 (예: 2) |
| volumeMoreThan | number | ❌ | 거래량 하한 (예: 1000) |
| volumeLowerThan | number | ❌ | 거래량 상한 (예: 1000000) |
| exchange | string | ❌ | 거래소 필터 (예: NASDAQ) ⚠️ Limited Access |
| country | string | ❌ | 국가 코드 (예: US) |
| isEtf | boolean | ❌ | ETF 여부 필터 (예: false) |
| isFund | boolean | ❌ | 펀드 여부 필터 (예: false) |
| isActivelyTrading | boolean | ❌ | 현재 거래 중인 종목만 (예: true) |
| limit | number | ❌ | 반환할 최대 결과 수 (기본: 1000) |
| includeAllShareClasses | boolean | ❌ | 다중 주식 클래스 모두 포함 여부 (기본: false) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| companyName | string | 회사명 |
| marketCap | number | 시가총액 (USD) |
| sector | string | 섹터 (예: Technology) |
| industry | string | 산업 (예: Consumer Electronics) |
| beta | number | 베타 값 |
| price | number | 현재 주가 |
| lastAnnualDividend | number | 최근 연간 배당금 |
| volume | number | 거래량 |
| exchange | string | 거래소 전체명 (예: NASDAQ Global Select) |
| exchangeShortName | string | 거래소 약칭 (예: NASDAQ) |
| country | string | 국가 코드 (예: US) |
| isEtf | boolean | ETF 여부 |
| isFund | boolean | 펀드 여부 |
| isActivelyTrading | boolean | 현재 거래 중 여부 |

```python
# GET /stable/company-screener?sector=Technology&priceMoreThan=100&priceLowerThan=300&limit=10
params = {
    "sector": "Technology",
    "priceMoreThan": 100,
    "priceLowerThan": 300,
    "limit": 10
}

# 응답 예시
[
  {
    "symbol": "AAPL",
    "companyName": "Apple Inc.",
    "marketCap": 3435062313000,
    "sector": "Technology",
    "industry": "Consumer Electronics",
    "beta": 1.24,
    "price": 225.93,
    "lastAnnualDividend": 1,
    "volume": 43010091,
    "exchange": "NASDAQ Global Select",
    "exchangeShortName": "NASDAQ",
    "country": "US",
    "isEtf": false,
    "isFund": false,
    "isActivelyTrading": true
  }
]
```

---

## 7. Exchange Variants Search — 다중 거래소 상장 정보 조회

```
GET /search-exchange-variants
```

**언제 사용:** 특정 종목이 상장된 모든 거래소의 목록과 거래소별 상세 프로파일 정보를 조회할 때. 동일 기업이 복수의 거래소에 중복 상장된 경우 전체 거래 현황 및 기업 프로파일 파악에 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbol | string | ✅ | 기준 티커 심볼 (예: AAPL) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 해당 거래소에서의 티커 심볼 |
| price | number | 현재 주가 |
| beta | number | 베타 값 |
| volAvg | number | 평균 거래량 |
| mktCap | number | 시가총액 (USD) |
| lastDiv | number | 최근 배당금 |
| range | string | 52주 주가 범위 (예: "164.08-237.23") |
| changes | number | 전일 대비 가격 변동 |
| companyName | string | 회사명 |
| currency | string | 거래 통화 (예: USD) |
| cik | string | SEC CIK 번호 |
| isin | string | ISIN 번호 |
| cusip | string | CUSIP 번호 |
| exchange | string | 거래소 전체명 (예: NASDAQ Global Select) |
| exchangeShortName | string | 거래소 약칭 (예: NASDAQ) |
| industry | string | 산업 분류 |
| website | string | 회사 웹사이트 |
| description | string | 회사 설명 |
| ceo | string | 대표자 이름 |
| sector | string | 섹터 |
| country | string | 국가 코드 |
| fullTimeEmployees | string | 정규직 직원 수 |
| phone | string | 대표 전화번호 |
| address | string | 본사 주소 |
| city | string | 도시 |
| state | string | 주/지역 |
| zip | string | 우편번호 |
| dcfDiff | number | DCF 차이 (현재가 - DCF 가치) |
| dcf | number | DCF 내재 가치 |
| image | string | 회사 로고 이미지 URL |
| ipoDate | string | IPO 날짜 (YYYY-MM-DD) |
| defaultImage | boolean | 기본 이미지 사용 여부 |
| isEtf | boolean | ETF 여부 |
| isActivelyTrading | boolean | 현재 거래 중 여부 |
| isAdr | boolean | ADR(미국 예탁증권) 여부 |
| isFund | boolean | 펀드 여부 |

```python
# GET /stable/search-exchange-variants?symbol=AAPL
params = {"symbol": "AAPL"}

# 응답 예시
[
	{
		"symbol": "AAPL",
		"price": 225.46,
		"beta": 1.24,
		"volAvg": 54722288,
		"mktCap": 3427916386000,
		"lastDiv": 1,
		"range": "164.08-237.23",
		"changes": -7.54,
		"companyName": "Apple Inc.",
		"currency": "USD",
		"cik": "0000320193",
		"isin": "US0378331005",
		"cusip": "037833100",
		"exchange": "NASDAQ Global Select",
		"exchangeShortName": "NASDAQ",
		"industry": "Consumer Electronics",
		"website": "https://www.apple.com",
		"description": "Apple Inc. designs, manufactures, and markets smartphones, personal computers, tablets, wearables, and accessories worldwide. The company offers iPhone, a line of smartphones; Mac, a line of personal computers; iPad, a line of multi-purpose tablets; and wearables, home, and accessories comprising AirPods, Apple TV, Apple Watch, Beats products, and HomePod. It also provides AppleCare support and cloud services; and operates various platforms, including the App Store that allow customers to discov...",
		"ceo": "Mr. Timothy D. Cook",
		"sector": "Technology",
		"country": "US",
		"fullTimeEmployees": "161000",
		"phone": "408 996 1010",
		"address": "One Apple Park Way",
		"city": "Cupertino",
		"state": "CA",
		"zip": "95014",
		"dcfDiff": 62.45842,
		"dcf": 161.68157666868984,
		"image": "https://financialmodelingprep.com/image-stock/AAPL.png",
		"ipoDate": "1980-12-12",
		"defaultImage": false,
		"isEtf": false,
		"isActivelyTrading": true,
		"isAdr": false,
		"isFund": false
	}
]

```
