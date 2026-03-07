# FMP News Endpoints

Base: `https://financialmodelingprep.com/stable`

---

## 엔드포인트 선택 가이드

| 상황 | 사용할 엔드포인트 |
|------|----------------|
| FMP 자체 분석 기사 조회 | `GET /fmp-articles` |
| 전체 일반 뉴스 최신 피드 조회 | `GET /news/general-latest` |
| 전체 주식 관련 뉴스 최신 피드 조회 | `GET /news/stock-latest` |
| 전체 크립토 뉴스 최신 피드 조회 | `GET /news/crypto-latest` |
| 전체 외환(Forex) 뉴스 최신 피드 조회 | `GET /news/forex-latest` |
| 특정 종목의 주식 뉴스 검색 | `GET /news/stock` |
| 특정 코인의 크립토 뉴스 검색 | `GET /news/crypto` |
| 특정 통화쌍의 외환 뉴스 검색 | `GET /news/forex` |

> ⚠️ 요청당 최대 250건, 페이지당 최대 100건으로 제한됨.
> 🇺🇸 `fmp-articles`, `press-releases-latest`, `press-releases`는 **미국 전용** 엔드포인트.
> `from` 날짜 필터는 모든 뉴스 엔드포인트에서 **Limited Access** (유료 플랜 필요).

---

## Articles — 전체 최신 피드

### 1. FMP Articles — FMP 자체 분석 기사 🇺🇸

```
GET /fmp-articles
```

**언제 사용:** Financial Modeling Prep이 직접 작성한 헤드라인, 스니펫, 기사 본문, 관련 티커 등을 포함한 자체 분석 기사를 조회할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| page | number | ❌ | 페이지 번호 (기본: 0) |
| limit | number | ❌ | 페이지당 결과 수 (기본: 20) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| title | string | 기사 제목 |
| date | string | 발행일 (YYYY-MM-DD HH:mm:ss) |
| content | string | 기사 본문 (HTML 포함 가능) |
| tickers | string | 관련 티커 심볼 (예: NYSE:MRK) |
| image | string | 기사 대표 이미지 URL |
| link | string | 기사 원문 URL |
| author | string | 작성자 이름 |
| site | string | 출처 사이트 (예: Financial Modeling Prep) |

```python
# GET /stable/fmp-articles?page=0&limit=20
params = {"page": 0, "limit": 20}

# 응답 예시
[
  {
    "title": "Merck Shares Plunge 8% as Weak Guidance Overshadows Strong Revenue Growth",
    "date": "2025-02-04 09:33:00",
    "content": "<p><a href='https://financialmodelingprep.com/financial-summary/MRK'>Merck & Co (NYSE:MRK)</a> saw its s...",
    "tickers": "NYSE:MRK",
    "image": "https://cdn.financialmodelingprep.com/images/fmp-1738679603793.jpg",
    "link": "https://financialmodelingprep.com/market-news/fmp-merck-shares-plunge-8-as-weak-guidance-overshadows-strong-revenue-growth",
    "author": "Davit Kirakosyan",
    "site": "Financial Modeling Prep"
  }
]
```

---

### 2. General News — 전체 일반 뉴스 🌍

```
GET /news/general-latest
```

**언제 사용:** 특정 종목에 국한되지 않는 최신 일반 금융·경제 뉴스를 다양한 언론사 소스에서 조회할 때. `symbol` 필드가 `null`로 반환됨.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| from | date | ❌ | 시작일 (YYYY-MM-DD) ⚠️ Limited Access |
| to | date | ❌ | 종료일 (YYYY-MM-DD) |
| page | number | ❌ | 페이지 번호 (기본: 0, 최대 100) |
| limit | number | ❌ | 페이지당 결과 수 (기본: 20, 최대 250) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string\|null | 관련 티커 심볼 (일반 뉴스는 null) |
| publishedDate | string | 발행일시 (YYYY-MM-DD HH:mm:ss) |
| publisher | string | 언론사명 (예: CNBC) |
| title | string | 뉴스 제목 |
| image | string | 뉴스 대표 이미지 URL |
| site | string | 출처 도메인 (예: cnbc.com) |
| text | string | 뉴스 본문 요약 |
| url | string | 원문 기사 URL |

```python
# GET /stable/news/general-latest?page=0&limit=20
params = {"page": 0, "limit": 20}

# 응답 예시
[
  {
    "symbol": null,
    "publishedDate": "2025-02-03 23:51:37",
    "publisher": "CNBC",
    "title": "Asia tech stocks rise after Trump pauses tariffs on China and Mexico",
    "image": "https://images.financialmodelingprep.com/news/asia-tech-stocks-rise-after-trump-pauses-tariffs-on-20250203.jpg",
    "site": "cnbc.com",
    "text": "Gains in Asian tech companies were broad-based, with stocks in Japan, South Korea and Hong Kong advancing...",
    "url": "https://www.cnbc.com/2025/02/04/asia-tech-stocks-rise-after-trump-pauses-tariffs-on-china-and-mexico.html"
  }
]
```

---

### 3. Stock News — 전체 주식 뉴스 🌍

```
GET /news/stock-latest
```

**언제 사용:** 주식 시장 관련 최신 뉴스를 다양한 언론사 소스로부터 전체 조회할 때. 특정 종목이 아닌 전반적인 주식 시장 뉴스 피드를 구성할 때 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| from | date | ❌ | 시작일 (YYYY-MM-DD) ⚠️ Limited Access |
| to | date | ❌ | 종료일 (YYYY-MM-DD) |
| page | number | ❌ | 페이지 번호 (기본: 0, 최대 100) |
| limit | number | ❌ | 페이지당 결과 수 (기본: 20, 최대 250) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 관련 티커 심볼 (예: INSG) |
| publishedDate | string | 발행일시 (YYYY-MM-DD HH:mm:ss) |
| publisher | string | 언론사명 (예: Seeking Alpha) |
| title | string | 뉴스 제목 |
| image | string | 대표 이미지 URL |
| site | string | 출처 도메인 (예: seekingalpha.com) |
| text | string | 뉴스 본문 요약 |
| url | string | 원문 기사 URL |

```python
# GET /stable/news/stock-latest?page=0&limit=20
params = {"page": 0, "limit": 20}

# 응답 예시
[
  {
    "symbol": "INSG",
    "publishedDate": "2025-02-03 23:53:40",
    "publisher": "Seeking Alpha",
    "title": "Q4 Earnings Release Looms For Inseego, But Don't Expect Miracles",
    "image": "https://images.financialmodelingprep.com/news/q4-earnings-release-looms-for-inseego-but-dont-expect-20250203.jpg",
    "site": "seekingalpha.com",
    "text": "Inseego's Q3 beat was largely due to a one-time debt restructuring gain, not sustainable earnings growth...",
    "url": "https://seekingalpha.com/article/4754485-inseego-stock-q4-earnings-preview-monitor-growth-margins-closely"
  }
]
```

---

### 4. Crypto News — 전체 크립토 뉴스 🌍

```
GET /news/crypto-latest
```

**언제 사용:** 비트코인, 이더리움 등 암호화폐 관련 최신 뉴스를 전체 조회할 때. 다양한 크립토 전문 매체 소스를 포함.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| from | date | ❌ | 시작일 (YYYY-MM-DD) ⚠️ Limited Access |
| to | date | ❌ | 종료일 (YYYY-MM-DD) |
| page | number | ❌ | 페이지 번호 (기본: 0, 최대 100) |
| limit | number | ❌ | 페이지당 결과 수 (기본: 20, 최대 250) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 관련 크립토 심볼 (예: BTCUSD) |
| publishedDate | string | 발행일시 (YYYY-MM-DD HH:mm:ss) |
| publisher | string | 언론사명 (예: Coingape) |
| title | string | 뉴스 제목 |
| image | string | 대표 이미지 URL |
| site | string | 출처 도메인 (예: coingape.com) |
| text | string | 뉴스 본문 요약 |
| url | string | 원문 기사 URL |

```python
# GET /stable/news/crypto-latest?page=0&limit=20
params = {"page": 0, "limit": 20}

# 응답 예시
[
  {
    "symbol": "BTCUSD",
    "publishedDate": "2025-02-03 23:32:19",
    "publisher": "Coingape",
    "title": "Crypto Prices Today Feb 4: BTC & Altcoins Recover Amid Pause On Trump's Tariffs",
    "image": "https://images.financialmodelingprep.com/news/crypto-prices-today-feb-4-btc-altcoins-recover-amid-pause-on-trumps-tariffs.jpg",
    "site": "coingape.com",
    "text": "Bitcoin and altcoins are rebounding as markets react to a temporary pause on tariffs...",
    "url": "https://coingape.com/crypto-prices-today-feb-4-btc-altcoins-recover-amid-pause-on-trumps-tariffs/"
  }
]
```

---

### 5. Forex News — 전체 외환 뉴스 🌍

```
GET /news/forex-latest
```

**언제 사용:** EUR/USD, XAU/USD 등 외환(Forex) 및 원자재 관련 최신 뉴스를 전체 조회할 때.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| from | date | ❌ | 시작일 (YYYY-MM-DD) ⚠️ Limited Access |
| to | date | ❌ | 종료일 (YYYY-MM-DD) |
| page | number | ❌ | 페이지 번호 (기본: 0, 최대 100) |
| limit | number | ❌ | 페이지당 결과 수 (기본: 20, 최대 250) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 관련 통화쌍/원자재 심볼 (예: XAUUSD) |
| publishedDate | string | 발행일시 (YYYY-MM-DD HH:mm:ss) |
| publisher | string | 언론사명 (예: FX Street) |
| title | string | 뉴스 제목 |
| image | string | 대표 이미지 URL |
| site | string | 출처 도메인 (예: fxstreet.com) |
| text | string | 뉴스 본문 요약 |
| url | string | 원문 기사 URL |

```python
# GET /stable/news/forex-latest?page=0&limit=20
params = {"page": 0, "limit": 20}

# 응답 예시
[
  {
    "symbol": "XAUUSD",
    "publishedDate": "2025-02-03 23:55:44",
    "publisher": "FX Street",
    "title": "United Arab Emirates Gold price today: Gold steadies, according to FXStreet data",
    "image": "https://images.financialmodelingprep.com/news/uae-gold-price-today-20250203.jpg",
    "site": "fxstreet.com",
    "text": "Gold price in the United Arab Emirates is steady today, according to FXStreet data...",
    "url": "https://www.fxstreet.com/currencies/gold/uae-gold-price-today-20250203"
  }
]
```

---

## Symbol — 종목별 뉴스 검색

### 6. Search Stock News — 종목별 주식 뉴스 검색 🌍

```
GET /news/stock
```

**언제 사용:** 특정 종목(또는 복수 종목)에 대한 주식 뉴스를 검색할 때. 종목 모니터링, 뉴스 알림, 감성 분석 파이프라인 구축에 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbols | string | ✅ | 조회할 티커 심볼, 콤마로 복수 입력 가능 (예: AAPL,MSFT) |
| from | date | ❌ | 시작일 (YYYY-MM-DD) ⚠️ Limited Access |
| to | date | ❌ | 종료일 (YYYY-MM-DD) |
| page | number | ❌ | 페이지 번호 (기본: 0, 최대 100) |
| limit | number | ❌ | 페이지당 결과 수 (기본: 20, 최대 250) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 티커 심볼 (예: AAPL) |
| publishedDate | string | 발행일시 (YYYY-MM-DD HH:mm:ss) |
| publisher | string | 언론사명 (예: Zacks Investment Research) |
| title | string | 뉴스 제목 |
| image | string | 대표 이미지 URL |
| site | string | 출처 도메인 (예: zacks.com) |
| text | string | 뉴스 본문 요약 |
| url | string | 원문 기사 URL |

```python
# GET /stable/news/stock?symbols=AAPL&page=0&limit=20
params = {"symbols": "AAPL", "page": 0, "limit": 20}

# 응답 예시
[
  {
    "symbol": "AAPL",
    "publishedDate": "2025-02-03 21:05:14",
    "publisher": "Zacks Investment Research",
    "title": "Apple & China Tariffs: A Closer Look",
    "image": "https://images.financialmodelingprep.com/news/apple-china-tariffs-a-closer-look-20250203.jpg",
    "site": "zacks.com",
    "text": "Tariffs have been the talk of the town over recent weeks...",
    "url": "https://www.zacks.com/stock/news/2408814/apple-china-tariffs-a-closer-look?cid=CS-STOCKNEWSAPI-FT-stocks"
  }
]
```

---

### 7. Search Crypto News — 코인별 크립토 뉴스 검색 🌍

```
GET /news/crypto
```

**언제 사용:** 특정 암호화폐 심볼(또는 복수 심볼)에 대한 뉴스를 검색할 때. 코인 모니터링 대시보드나 알림 시스템 구축에 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbols | string | ✅ | 조회할 크립토 심볼, 콤마로 복수 입력 가능 (예: BTCUSD,ETHUSD) |
| from | date | ❌ | 시작일 (YYYY-MM-DD) ⚠️ Limited Access |
| to | date | ❌ | 종료일 (YYYY-MM-DD) |
| page | number | ❌ | 페이지 번호 (기본: 0, 최대 100) |
| limit | number | ❌ | 페이지당 결과 수 (기본: 20, 최대 250) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 크립토 심볼 (예: BTCUSD) |
| publishedDate | string | 발행일시 (YYYY-MM-DD HH:mm:ss) |
| publisher | string | 언론사명 (예: Coingape) |
| title | string | 뉴스 제목 |
| image | string | 대표 이미지 URL |
| site | string | 출처 도메인 (예: coingape.com) |
| text | string | 뉴스 본문 요약 |
| url | string | 원문 기사 URL |

```python
# GET /stable/news/crypto?symbols=BTCUSD&page=0&limit=20
params = {"symbols": "BTCUSD", "page": 0, "limit": 20}

# 응답 예시
[
  {
    "symbol": "BTCUSD",
    "publishedDate": "2025-02-03 23:32:19",
    "publisher": "Coingape",
    "title": "Crypto Prices Today Feb 4: BTC & Altcoins Recover Amid Pause On Trump's Tariffs",
    "image": "https://images.financialmodelingprep.com/news/crypto-prices-today-feb-4-btc-altcoins-recover.jpg",
    "site": "coingape.com",
    "text": "Bitcoin and altcoins are rebounding as markets react to a temporary pause on tariffs...",
    "url": "https://coingape.com/crypto-prices-today-feb-4-btc-altcoins-recover-amid-pause-on-trumps-tariffs/"
  }
]
```

---

### 8. Search Forex News — 통화쌍별 외환 뉴스 검색 🌍

```
GET /news/forex
```

**언제 사용:** 특정 통화쌍(또는 복수 심볼)에 대한 외환 뉴스를 검색할 때. EUR/USD, GBP/USD 등 특정 페어의 시장 동향 분석에 활용.

**파라미터**
| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| symbols | string | ✅ | 조회할 통화쌍 심볼, 콤마로 복수 입력 가능 (예: EURUSD,GBPUSD) |
| from | date | ❌ | 시작일 (YYYY-MM-DD) ⚠️ Limited Access |
| to | date | ❌ | 종료일 (YYYY-MM-DD) |
| page | number | ❌ | 페이지 번호 (기본: 0, 최대 100) |
| limit | number | ❌ | 페이지당 결과 수 (기본: 20, 최대 250) |
| apikey | string | ✅ | API 키 |

**응답 필드**
| 필드 | 타입 | 설명 |
|------|------|------|
| symbol | string | 통화쌍 심볼 (예: EURUSD) |
| publishedDate | string | 발행일시 (YYYY-MM-DD HH:mm:ss) |
| publisher | string | 언론사명 (예: FX Street) |
| title | string | 뉴스 제목 |
| image | string | 대표 이미지 URL |
| site | string | 출처 도메인 (예: fxstreet.com) |
| text | string | 뉴스 본문 요약 |
| url | string | 원문 기사 URL |

```python
# GET /stable/news/forex?symbols=EURUSD&page=0&limit=20
params = {"symbols": "EURUSD", "page": 0, "limit": 20}

# 응답 예시
[
  {
    "symbol": "EURUSD",
    "publishedDate": "2025-02-03 18:43:01",
    "publisher": "FX Street",
    "title": "EUR/USD Price Today: Euro struggles to recover amid renewed Dollar strength",
    "image": "https://images.financialmodelingprep.com/news/eurusd-price-today-20250203.jpg",
    "site": "fxstreet.com",
    "text": "The Euro faces renewed selling pressure as the US Dollar strengthens...",
    "url": "https://www.fxstreet.com/currencies/eurusd/eurusd-price-today-20250203"
  }
]
```
