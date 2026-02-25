# FMP Stock Directory Endpoints

Base: `https://financialmodelingprep.com/stable`

---

## 엔드포인트 선택 가이드

| 상황 | 사용할 엔드포인트 |
|------|----------------|
| 전체 거래 가능 종목 목록 조회 | `GET /stock-list` |
| 재무제표 데이터가 있는 종목만 필터링 | `GET /financial-statement-symbol-list` |
| SEC CIK 번호 전체 목록 조회 | `GET /cik-list` |
| 심볼 변경(합병/분할/이름변경) 이력 추적 | `GET /symbol-change` |
| ETF 종목 목록만 조회 | `GET /etf-list` |
| 현재 거래 중인 종목만 필터링 | `GET /actively-trading-list` |
| 어닝콜 트랜스크립트 보유 종목 목록 | `GET /earnings-transcript-list` |
| 지원 거래소 목록 확인 | `GET /available-exchanges` |
| 지원 섹터 목록 확인 | `GET /available-sectors` |
| 지원 산업 분류 목록 확인 | `GET /available-industries` |
| 지원 국가 목록 확인 | `GET /available-countries` |

> ⚠️ 대부분의 디렉토리 엔드포인트는 파라미터 없이 **전체 목록을 반환**하므로 응답 크기가 클 수 있음. 종목 스크리닝 목적이라면 `/company-screener` 사용 권장.

---

## 엔드포인트 활용 패턴

디렉토리 엔드포인트는 단독 사용보다 다른 엔드포인트와 조합해 사용하는 경우가 많다.

| 디렉토리 엔드포인트 | 주로 연계되는 엔드포인트 |
|--------------------|------------------------|
| `/available-exchanges` | `/company-screener?exchange=` |
| `/available-sectors` | `/company-screener?sector=`, `/sector-performance-snapshot` |
| `/available-industries` | `/company-screener?industry=`, `/industry-performance-snapshot` |
| `/available-countries` | `/company-screener?country=` |
| `/financial-statement-symbol-list` | `/income-statement`, `/balance-sheet-statement` |
| `/symbol-change` | 데이터 파이프라인에서 구심볼 → 신심볼 매핑 |
| `/etf-list` | `/etf/holdings`, `/etf/info` |

---

## 1. Company Symbols List — 전체 종목 목록

```
GET /stock-list
```

**언제 사용:** 전 세계 거래소에 상장된 모든 종목(주식, ETF, 펀드 등)의 기본 정보를 한 번에 조회할 때. 심볼 유효성 검증이나 전체 유니버스 탐색에 유용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| companyName | string | 종목/회사명 |

```python
# GET /stable/stock-list
params = {}

# 응답 예시
[
	{
		"symbol": "6898.HK",
		"companyName": "China Aluminum Cans Holdings Limited"
	}
]
```

---

## 2. Financial Statement Symbols List — 재무제표 보유 종목 목록

```
GET /financial-statement-symbol-list
```

**언제 사용:** 재무제표(손익계산서, 대차대조표, 현금흐름표) 데이터가 실제로 존재하는 종목만 필터링할 때. 기본적 분석 대상 종목 선별에 유용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| companyName | string | 종목/회사명 |
| tradingCurrency | string | 거래시 사용되는 통화 |
| reportingCurrency | string | 재무재표에서 사용되는 통화 |

```python
# GET /stable/financial-statement-symbol-list
params = {}

# 응답 예시
[
	{
		"symbol": "6898.HK",
		"companyName": "China Aluminum Cans Holdings Limited",
		"tradingCurrency": "HKD",
		"reportingCurrency": "HKD"
	}
]

```

---

## 3. CIK List — SEC CIK 전체 목록

```
GET /cik-list
```

**언제 사용:** SEC에 등록된 기업의 CIK(Central Index Key) 번호 전체 목록을 조회할 때. SEC 공시 데이터 분석이나 규정 준수 워크플로우에 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| page | integer | ❌ | 페이지 번호 (기본: 0) |
| limit | integer | ❌ | 페이지당 결과 수 (기본: 1000) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| cik | string | SEC CIK 번호 |
| companyName | string | 기업/법인명 |

```python
# GET /stable/cik-list?page=0&limit=1000
params = {"page": 0, "limit": 1000}

# 응답 예시
[
  {"cik": "0000320193", "name": "Apple Inc."},
  {"cik": "0000789019", "name": "Microsoft Corp"}
]
```

---

## 4. Symbol Changes List — 심볼 변경 이력

```
GET /symbol-change
```

**언제 사용:** 합병, 인수, 액면분할, 사명 변경 등으로 인해 변경된 티커 심볼 이력을 추적할 때. 데이터 파이프라인에서 구심볼 → 신심볼 매핑이 필요한 경우.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| invalid | string | ❌ | 유효하지 않은 변경 건 포함 여부 |
| limit | integer | ❌ | 페이지당 결과 수 (기본: 1000) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| date | string | 변경일 (YYYY-MM-DD) |
| companyName | string | 회사명 |
| oldSymbol | string | 변경 전 심볼 |
| newSymbol | string | 변경 후 심볼 |

```python
# GET /stable/symbol-change?invalid=true&limit=1000
params = { "invalid": true, "limit": 1000 }

# 응답 예시
[
  {"date": "2022-10-28", "companyName": "Twitter, Inc.", "oldSymbol": "TWTR", "newSymbol": ""},
  {"date": "2023-02-01", "companyName": "Meta Platforms", "oldSymbol": "FB", "newSymbol": "META"}
]
```

---

## 5. ETF Symbol List — ETF 종목 목록

```
GET /etf-list
```

**언제 사용:** ETF 종목만 별도로 조회할 때. ETF 유니버스 탐색이나 ETF 관련 데이터 처리 전 심볼 목록 확인에 유용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| name | string | ETF명 |

```python
# GET /stable/etf-list
params = {}

# 응답 예시
[
  {"symbol": "SPY", "name": "SPDR S&P 500 ETF Trust"},
  {"symbol": "QQQ", "name": "Invesco QQQ Trust"}
]
```

---

## 6. Actively Trading List — 현재 거래 중인 종목 목록

```
GET /actively-trading-list
```

**언제 사용:** 현재 거래소에서 실제로 거래 중인 종목만 필터링할 때. 상장폐지되거나 거래 정지된 종목을 제외하고 유효한 종목만 다룰 때 유용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| name | string | 종목/회사명 |

```python
# GET /stable/actively-trading-list
params = {}

# 응답 예시
[
  {"symbol": "AAPL", "name": "Apple Inc."}
]
```

---

## 7. Earnings Transcript List — 어닝콜 트랜스크립트 보유 종목 목록

```
GET /earnings-transcript-list
```

**언제 사용:** 어닝콜 트랜스크립트 데이터를 보유한 종목 목록과 각 종목별 보유 트랜스크립트 수를 조회할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 |
| companyName | string | 회사명 |
| noOfTranscripts | string | 보유 트랜스크립트 수 |

```python
# GET /stable/earnings-transcript-list
params = {}

# 응답 예시
[
  {"symbol": "AAPL", "companyName": "Apple Inc.", "transcriptCount": "48"}
]
```

---

## 8. Available Exchanges — 지원 거래소 목록

```
GET /available-exchanges
```

**언제 사용:** FMP가 지원하는 전 세계 거래소 목록을 조회할 때. 다른 엔드포인트의 `exchange` 파라미터에 입력할 유효한 거래소 값을 확인할 때 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| exchange | string | 거래소 약칭 (예: AMEX, NASDAQ) |
| name | string | 거래소 전체명 (예: New York Stock Exchange Arca) |
| countryName | string | 소속 국가명 (예: United States of America) |
| countryCode | string | 국가 코드 ISO 2자리 (예: US) |
| symbolSuffix | string | 해당 거래소 심볼 접미사 (미국은 N/A, 해외 거래소는 .KS, .T 등) |
| delay | string | 데이터 지연 유형 (예: Real-time, 15 min delay) |

```python
# GET /stable/available-exchanges
params = {}

# 응답 예시
[
	{
		"exchange": "AMEX",
		"name": "New York Stock Exchange Arca",
		"countryName": "United States of America",
		"countryCode": "US",
		"symbolSuffix": "N/A",
		"delay": "Real-time"
	}
]

```

---

## 9. Available Sectors — 지원 섹터 목록

```
GET /available-sectors
```

**언제 사용:** FMP에서 사용하는 섹터 분류 목록을 조회할 때. 스크리너나 섹터 필터링 시 유효한 `sector` 값을 확인할 때 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| sector | string | 섹터명 (예: Technology, Healthcare) |

```python
# GET /stable/available-sectors
params = {}

# 응답 예시
[
	{
		"sector": "Basic Materials"
	}
]
```

---

## 10. Available Industries — 지원 산업 분류 목록

```
GET /available-industries
```

**언제 사용:** FMP에서 사용하는 세부 산업 분류 목록을 조회할 때. 섹터보다 세분화된 `industry` 파라미터 값을 확인할 때 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| industry | string | 산업명 (예: Consumer Electronics, Biotechnology) |

```python
# GET /stable/available-industries
params = {}

# 응답 예시
[
	{
		"industry": "Steel"
	}
]
```

---

## 11. Available Countries — 지원 국가 목록

```
GET /available-countries
```

**언제 사용:** FMP에서 지원하는 국가 코드 목록을 조회할 때. 스크리너나 국가별 필터링 시 유효한 `country` 파라미터 값을 확인할 때 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| country | string | 국가 코드 (ISO 2자리, 예: US, KR, GB) |

```python
# GET /stable/available-countries
params = {}

# 응답 예시
[
	{
		"country": "FK"
	}
]

```