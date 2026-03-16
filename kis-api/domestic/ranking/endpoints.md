# KIS [국내주식] 순위분석 Endpoints

실전 Base: `https://openapi.koreainvestment.com:9443`
모의 Base: 모의투자 미지원 (전 API 공통)

> **TR_ID 규칙:** 순위분석 API는 모두 GET 조회 전용이며, `tr_cont`를 이용한 연속(다음페이지) 조회가 불가하다.

---


## 엔드포인트 선택 가이드

Claude는 아래 기준으로 적절한 엔드포인트를 선택한다.

| 상황 | 사용할 엔드포인트 |
|------|----------------|
| 등락률(상승/하락/시가대비/변동율) 순위 조회 | `GET /uapi/domestic-stock/v1/ranking/fluctuation` |
| 호가잔량(순매수/순매도/매수비율/매도비율) 순위 조회 | `GET /uapi/domestic-stock/v1/ranking/quote-balance` |
| 수익·자산 지표(매출이익/영업이익/당기순이익/자산총계 등) 순위 조회 | `GET /uapi/domestic-stock/v1/ranking/profit-asset-index` |
| 시가총액 상위 종목 조회 | `GET /uapi/domestic-stock/v1/ranking/market-cap` |
| 재무비율(수익성/안정성/성장성/활동성) 순위 조회 | `GET /uapi/domestic-stock/v1/ranking/finance-ratio` |
| 시간외 잔량(장전/장후/매도/매수) 순위 조회 | `GET /uapi/domestic-stock/v1/ranking/after-hour-balance` |
| 우선주/괴리율 상위 종목 조회 | `GET /uapi/domestic-stock/v1/ranking/prefer-disparate-ratio` |

---


## 공통 요청 Header (모든 API 동일)

> 아래 헤더는 모든 API에서 공통으로 사용한다. 각 API 설명에서는 API별로 다른 `tr_id`와 API 고유 파라미터만 표기한다.

| 파라미터 | 한글명 | 타입 | 필수 | 길이 | 설명 |
|---------|--------|------|------|------|------|
| content-type | 컨텐츠타입 | String | ✅ | 40 | `application/json; charset=utf-8` |
| authorization | 접근토큰 | String | ✅ | 350 | `Bearer {access_token}` |
| appkey | 앱키 | String | ✅ | 36 | KIS 홈페이지 발급 앱키 (노출 금지) |
| appsecret | 앱시크릿키 | String | ✅ | 180 | KIS 홈페이지 발급 앱시크릿 (노출 금지) |
| personalseckey | 고객식별키 | String | ❌ | 180 | [법인 필수] 제휴사 회원 고객식별키 |
| tr_id | 거래ID | String | ✅ | 13 | API마다 다름 (아래 각 API 항목 참조) |
| tr_cont | 연속거래여부 | String | ❌ | 1 | 순위분석 API는 전부 연속조회 불가 |
| custtype | 고객타입 | String | ✅ | 1 | `P`: 개인, `B`: 법인 |
| seq_no | 일련번호 | String | ❌ | 2 | [법인 필수] `001` |
| mac_address | 맥주소 | String | ❌ | 12 | [법인] Mac address |
| phone_number | 핸드폰번호 | String | ❌ | 12 | [법인 필수] 핸드폰번호 (하이픈 제거) |
| ip_addr | 접속단말 공인IP | String | ❌ | 12 | [법인 필수] 사용자 IP |
| gt_uid | Global UID | String | ❌ | 32 | [법인 전용] 거래별 UNIQUE 고유번호 |

---


## 공통 Query Parameter 설명

> 순위분석 API들은 공통적으로 아래 FID 파라미터 패턴을 사용한다. 각 API마다 지원하는 파라미터와 값이 다르므로 개별 API 항목을 참조한다.

| 파라미터 | 한글명 | 공통 설명 |
|---------|--------|----------|
| fid_cond_mrkt_div_code | 조건 시장 분류 코드 | `J`:KRX, `NX`:NXT (API에 따라 `J` 고정인 경우 있음) |
| fid_cond_scr_div_code | 조건 화면 분류 코드 | API별 고유 Unique key (아래 각 API 참조) |
| fid_input_iscd | 입력 종목코드 | `0000`:전체, `0001`:거래소, `1001`:코스닥, `2001`:코스피200 |
| fid_div_cls_code | 분류 구분 코드 | `0`:전체 (API에 따라 보통주/우선주 구분 가능) |
| fid_trgt_cls_code | 대상 구분 코드 | `0`:전체 |
| fid_trgt_exls_cls_code | 대상 제외 구분 코드 | `0`:전체 |
| fid_input_price_1 | 입력 가격1 | 가격 범위 시작. 공란 시 전체 |
| fid_input_price_2 | 입력 가격2 | 가격 범위 종료. 공란 시 전체 |
| fid_vol_cnt | 거래량 수 | 거래량 기준. 공란 시 전체 |

---


### 1. 국내주식 등락률 순위 [v1_국내주식-088]

```
GET /uapi/domestic-stock/v1/ranking/fluctuation
```

**언제 사용:** 국내주식 등락률 순위를 조회할 때. 한국투자 HTS(eFriend Plus) > [0170] 등락률 순위 화면의 기능을 API로 개발한 것으로, 상승율/하락율/시가대비/변동율 기준으로 정렬할 수 있다.

**TR_ID:** `FHPST01700000`

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| fid_cond_mrkt_div_code | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT |
| fid_cond_scr_div_code | 조건 화면 분류 코드 | ✅ | `20170` (고정값) |
| fid_input_iscd | 입력 종목코드 | ✅ | `0000`:전체, `0001`:코스피, `1001`:코스닥, `2001`:코스피200 |
| fid_rank_sort_cls_code | 순위 정렬 구분 코드 | ✅ | `0`:상승율순, `1`:하락율순, `2`:시가대비상승율, `3`:시가대비하락율, `4`:변동율 |
| fid_input_cnt_1 | 입력 수1 | ✅ | `0`:전체, 또는 누적일수 입력 |
| fid_prc_cls_code | 가격 구분 코드 | ✅ | 상승율순일 때 `0`:저가대비/`1`:종가대비, 하락율순일 때 `0`:고가대비/`1`:종가대비 |
| fid_input_price_1 | 입력 가격1 | ✅ | 가격 범위 시작. 공란 시 전체 |
| fid_input_price_2 | 입력 가격2 | ✅ | 가격 범위 종료. 공란 시 전체 |
| fid_vol_cnt | 거래량 수 | ✅ | 거래량 기준. 공란 시 전체 |
| fid_trgt_cls_code | 대상 구분 코드 | ✅ | `0`:전체 |
| fid_trgt_exls_cls_code | 대상 제외 구분 코드 | ✅ | `0`:전체 |
| fid_div_cls_code | 분류 구분 코드 | ✅ | `0`:전체 |
| fid_rsfl_rate1 | 등락 비율1 | ✅ | 비율 범위 시작. 공란 시 전체 |
| fid_rsfl_rate2 | 등락 비율2 | ✅ | 비율 범위 종료. 공란 시 전체 |

**Response** (`output` — 배열)

| 필드 | 한글명 |
|------|--------|
| stck_shrn_iscd | 주식 단축 종목코드 |
| data_rank | 데이터 순위 |
| hts_kor_isnm | HTS 한글 종목명 |
| stck_prpr | 주식 현재가 |
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_ctrt | 전일 대비율 |
| acml_vol | 누적 거래량 |
| stck_hgpr | 주식 최고가 |
| hgpr_hour | 최고가 시간 |
| acml_hgpr_date | 누적 최고가 일자 |
| stck_lwpr | 주식 최저가 |
| lwpr_hour | 최저가 시간 |
| acml_lwpr_date | 누적 최저가 일자 |
| lwpr_vrss_prpr_rate | 최저가 대비 현재가 비율 |
| dsgt_date_clpr_vrss_prpr_rate | 지정 일자 종가 대비 현재가 비율 |
| cnnt_ascn_dynu | 연속 상승 일수 |
| hgpr_vrss_prpr_rate | 최고가 대비 현재가 비율 |
| cnnt_down_dynu | 연속 하락 일수 |
| oprc_vrss_prpr_sign | 시가 대비 현재가 부호 |
| oprc_vrss_prpr | 시가 대비 현재가 |
| oprc_vrss_prpr_rate | 시가 대비 현재가 비율 |
| prd_rsfl | 기간 등락 |
| prd_rsfl_rate | 기간 등락 비율 |

```python
# GET /uapi/domestic-stock/v1/ranking/fluctuation — 상승률 순위 조회
import requests

url = "https://openapi.koreainvestment.com:9443/uapi/domestic-stock/v1/ranking/fluctuation"
headers = {
    "content-type": "application/json; charset=utf-8",
    "authorization": "Bearer {access_token}",
    "appkey": "{appkey}",
    "appsecret": "{appsecret}",
    "tr_id": "FHPST01700000",
    "custtype": "P"
}
params = {
    "fid_cond_mrkt_div_code": "J",
    "fid_cond_scr_div_code": "20170",
    "fid_input_iscd": "0000",
    "fid_rank_sort_cls_code": "0",
    "fid_input_cnt_1": "0",
    "fid_prc_cls_code": "0",
    "fid_input_price_1": "",
    "fid_input_price_2": "",
    "fid_vol_cnt": "",
    "fid_trgt_cls_code": "0",
    "fid_trgt_exls_cls_code": "0",
    "fid_div_cls_code": "0",
    "fid_rsfl_rate1": "",
    "fid_rsfl_rate2": ""
}
res = requests.get(url, headers=headers, params=params)
```

---


### 2. 국내주식 호가잔량 순위 [국내주식-089]

```
GET /uapi/domestic-stock/v1/ranking/quote-balance
```

**언제 사용:** 국내주식 호가잔량 순위를 조회할 때. 한국투자 HTS(eFriend Plus) > [0172] 호가잔량 순위 화면의 기능을 API로 개발한 것으로, 순매수잔량/순매도잔량/매수비율/매도비율 기준으로 정렬할 수 있다.

**TR_ID:** `FHPST01720000`

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| fid_cond_mrkt_div_code | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT |
| fid_cond_scr_div_code | 조건 화면 분류 코드 | ✅ | `20172` (고정값) |
| fid_input_iscd | 입력 종목코드 | ✅ | `0000`:전체, `0001`:코스피, `1001`:코스닥, `2001`:코스피200 |
| fid_rank_sort_cls_code | 순위 정렬 구분 코드 | ✅ | `0`:순매수잔량순, `1`:순매도잔량순, `2`:매수비율순, `3`:매도비율순 |
| fid_div_cls_code | 분류 구분 코드 | ✅ | `0`:전체 |
| fid_trgt_cls_code | 대상 구분 코드 | ✅ | `0`:전체 |
| fid_trgt_exls_cls_code | 대상 제외 구분 코드 | ✅ | `0`:전체 |
| fid_input_price_1 | 입력 가격1 | ✅ | 가격 범위 시작. 공란 시 전체 |
| fid_input_price_2 | 입력 가격2 | ✅ | 가격 범위 종료. 공란 시 전체 |
| fid_vol_cnt | 거래량 수 | ✅ | 거래량 기준. 공란 시 전체 |

**Response** (`output` — 배열)

| 필드 | 한글명 |
|------|--------|
| mksc_shrn_iscd | 유가증권 단축 종목코드 |
| data_rank | 데이터 순위 |
| hts_kor_isnm | HTS 한글 종목명 |
| stck_prpr | 주식 현재가 |
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_ctrt | 전일 대비율 |
| acml_vol | 누적 거래량 |
| total_askp_rsqn | 총 매도호가 잔량 |
| total_bidp_rsqn | 총 매수호가 잔량 |
| total_ntsl_bidp_rsqn | 총 순 매수호가 잔량 |
| shnu_rsqn_rate | 매수 잔량 비율 |
| seln_rsqn_rate | 매도 잔량 비율 |

```python
# GET /uapi/domestic-stock/v1/ranking/quote-balance — 순매수잔량 순위
headers["tr_id"] = "FHPST01720000"
params = {
    "fid_cond_mrkt_div_code": "J",
    "fid_cond_scr_div_code": "20172",
    "fid_input_iscd": "0000",
    "fid_rank_sort_cls_code": "0",
    "fid_div_cls_code": "0",
    "fid_trgt_cls_code": "0",
    "fid_trgt_exls_cls_code": "0",
    "fid_input_price_1": "",
    "fid_input_price_2": "",
    "fid_vol_cnt": ""
}
res = requests.get(url.replace("fluctuation", "quote-balance"), headers=headers, params=params)
```

---


### 3. 국내주식 수익자산지표 순위 [v1_국내주식-090]

```
GET /uapi/domestic-stock/v1/ranking/profit-asset-index
```

**언제 사용:** 국내주식 수익·자산 지표 순위를 조회할 때. 한국투자 HTS(eFriend Plus) > [0173] 수익자산지표 순위 화면의 기능을 API로 개발한 것으로, 매출이익/영업이익/경상이익/당기순이익/자산총계/부채총계/자본총계 기준으로 정렬할 수 있다. 회계연도와 분기를 지정하여 조회한다.

**TR_ID:** `FHPST01730000`

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| fid_cond_mrkt_div_code | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT |
| fid_cond_scr_div_code | 조건 화면 분류 코드 | ✅ | `20173` (고정값) |
| fid_input_iscd | 입력 종목코드 | ✅ | `0000`:전체, `0001`:거래소, `1001`:코스닥, `2001`:코스피200 |
| fid_rank_sort_cls_code | 순위 정렬 구분 코드 | ✅ | `0`:매출이익, `1`:영업이익, `2`:경상이익, `3`:당기순이익, `4`:자산총계, `5`:부채총계, `6`:자본총계 |
| fid_input_option_1 | 입력 옵션1 | ✅ | 회계연도 (ex. `2023`) |
| fid_input_option_2 | 입력 옵션2 | ✅ | `0`:1/4분기, `1`:반기, `2`:3/4분기, `3`:결산 |
| fid_div_cls_code | 분류 구분 코드 | ✅ | `0`:전체 |
| fid_trgt_cls_code | 대상 구분 코드 | ✅ | `0`:전체 |
| fid_trgt_exls_cls_code | 대상 제외 구분 코드 | ✅ | `0`:전체 |
| fid_blng_cls_code | 소속 구분 코드 | ✅ | `0`:전체 |
| fid_input_price_1 | 입력 가격1 | ✅ | 가격 범위 시작. 공란 시 전체 |
| fid_input_price_2 | 입력 가격2 | ✅ | 가격 범위 종료. 공란 시 전체 |
| fid_vol_cnt | 거래량 수 | ✅ | 거래량 기준. 공란 시 전체 |

**Response** (`output` — 배열)

| 필드 | 한글명 |
|------|--------|
| data_rank | 데이터 순위 |
| hts_kor_isnm | HTS 한글 종목명 |
| mksc_shrn_iscd | 유가증권 단축 종목코드 |
| stck_prpr | 주식 현재가 |
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_ctrt | 전일 대비율 |
| acml_vol | 누적 거래량 |
| sale_totl_prfi | 매출 총 이익 |
| bsop_prti | 영업 이익 |
| op_prfi | 경상 이익 |
| thtr_ntin | 당기순이익 |
| total_aset | 자산총계 |
| total_lblt | 부채총계 |
| total_cptl | 자본총계 |
| stac_month | 결산 월 |
| stac_month_cls_code | 결산 월 구분 코드 |
| iqry_csnu | 조회 건수 |

```python
# GET /uapi/domestic-stock/v1/ranking/profit-asset-index — 당기순이익 기준 순위 (2024년 결산)
headers["tr_id"] = "FHPST01730000"
params = {
    "fid_cond_mrkt_div_code": "J",
    "fid_cond_scr_div_code": "20173",
    "fid_input_iscd": "0000",
    "fid_rank_sort_cls_code": "3",
    "fid_input_option_1": "2024",
    "fid_input_option_2": "3",
    "fid_div_cls_code": "0",
    "fid_trgt_cls_code": "0",
    "fid_trgt_exls_cls_code": "0",
    "fid_blng_cls_code": "0",
    "fid_input_price_1": "",
    "fid_input_price_2": "",
    "fid_vol_cnt": ""
}
res = requests.get("https://openapi.koreainvestment.com:9443/uapi/domestic-stock/v1/ranking/profit-asset-index",
                    headers=headers, params=params)
```

---


### 4. 국내주식 시가총액 상위 [v1_국내주식-091]

```
GET /uapi/domestic-stock/v1/ranking/market-cap
```

**언제 사용:** 국내주식 시가총액 상위 종목을 조회할 때. 한국투자 HTS(eFriend Plus) > [0174] 시가총액 상위 화면의 기능을 API로 개발한 것이다.

**TR_ID:** `FHPST01740000`

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| fid_cond_mrkt_div_code | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT |
| fid_cond_scr_div_code | 조건 화면 분류 코드 | ✅ | `20174` (고정값) |
| fid_input_iscd | 입력 종목코드 | ✅ | `0000`:전체, `0001`:거래소, `1001`:코스닥, `2001`:코스피200 |
| fid_div_cls_code | 분류 구분 코드 | ✅ | `0`:전체, `1`:보통주, `2`:우선주 |
| fid_trgt_cls_code | 대상 구분 코드 | ✅ | `0`:전체 |
| fid_trgt_exls_cls_code | 대상 제외 구분 코드 | ✅ | `0`:전체 |
| fid_input_price_1 | 입력 가격1 | ✅ | 가격 범위 시작. 공란 시 전체 |
| fid_input_price_2 | 입력 가격2 | ✅ | 가격 범위 종료. 공란 시 전체 |
| fid_vol_cnt | 거래량 수 | ✅ | 거래량 기준. 공란 시 전체 |

**Response** (`output` — 배열)

| 필드 | 한글명 |
|------|--------|
| mksc_shrn_iscd | 유가증권 단축 종목코드 |
| data_rank | 데이터 순위 |
| hts_kor_isnm | HTS 한글 종목명 |
| stck_prpr | 주식 현재가 |
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_ctrt | 전일 대비율 |
| acml_vol | 누적 거래량 |
| lstn_stcn | 상장 주수 |
| stck_avls | 시가 총액 |
| mrkt_whol_avls_rlim | 시장 전체 시가총액 비중 |

```python
# GET /uapi/domestic-stock/v1/ranking/market-cap — 시가총액 상위 전체 조회
headers["tr_id"] = "FHPST01740000"
params = {
    "fid_cond_mrkt_div_code": "J",
    "fid_cond_scr_div_code": "20174",
    "fid_input_iscd": "0000",
    "fid_div_cls_code": "0",
    "fid_trgt_cls_code": "0",
    "fid_trgt_exls_cls_code": "0",
    "fid_input_price_1": "",
    "fid_input_price_2": "",
    "fid_vol_cnt": ""
}
res = requests.get("https://openapi.koreainvestment.com:9443/uapi/domestic-stock/v1/ranking/market-cap",
                    headers=headers, params=params)
```

---


### 5. 국내주식 재무비율 순위 [v1_국내주식-092]

```
GET /uapi/domestic-stock/v1/ranking/finance-ratio
```

**언제 사용:** 국내주식 재무비율 순위를 조회할 때. 한국투자 HTS(eFriend Plus) > [0175] 재무비율 순위 화면의 기능을 API로 개발한 것으로, 수익성/안정성/성장성/활동성 분석 기준으로 정렬할 수 있다. 회계연도와 분기를 지정하여 조회한다.

**TR_ID:** `FHPST01750000`

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| fid_cond_mrkt_div_code | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT |
| fid_cond_scr_div_code | 조건 화면 분류 코드 | ✅ | `20175` (고정값) |
| fid_input_iscd | 입력 종목코드 | ✅ | `0000`:전체, `0001`:거래소, `1001`:코스닥, `2001`:코스피200 |
| fid_rank_sort_cls_code | 순위 정렬 구분 코드 | ✅ | `7`:수익성 분석, `11`:안정성 분석, `15`:성장성 분석, `20`:활동성 분석 |
| fid_input_option_1 | 입력 옵션1 | ✅ | 회계연도 (ex. `2023`) |
| fid_input_option_2 | 입력 옵션2 | ✅ | `0`:1/4분기, `1`:반기, `2`:3/4분기, `3`:결산 |
| fid_div_cls_code | 분류 구분 코드 | ✅ | `0`:전체 |
| fid_trgt_cls_code | 대상 구분 코드 | ✅ | `0`:전체 |
| fid_trgt_exls_cls_code | 대상 제외 구분 코드 | ✅ | `0`:전체 |
| fid_blng_cls_code | 소속 구분 코드 | ✅ | (공란 가능) |
| fid_input_price_1 | 입력 가격1 | ✅ | 가격 범위 시작. 공란 시 전체 |
| fid_input_price_2 | 입력 가격2 | ✅ | 가격 범위 종료. 공란 시 전체 |
| fid_vol_cnt | 거래량 수 | ✅ | 거래량 기준. 공란 시 전체 |

**Response** (`output` — 배열)

| 필드 | 한글명 |
|------|--------|
| data_rank | 데이터 순위 |
| hts_kor_isnm | HTS 한글 종목명 |
| mksc_shrn_iscd | 유가증권 단축 종목코드 |
| stck_prpr | 주식 현재가 |
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_ctrt | 전일 대비율 |
| acml_vol | 누적 거래량 |
| cptl_op_prfi | 총자본경상이익율 |
| cptl_ntin_rate | 총자본 순이익율 |
| sale_totl_rate | 매출액 총이익율 |
| sale_ntin_rate | 매출액 순이익율 |
| bis | 자기자본비율 |
| lblt_rate | 부채 비율 |
| bram_depn | 차입금 의존도 |
| rsrv_rate | 유보 비율 |
| grs | 매출액 증가율 |
| op_prfi_inrt | 경상 이익 증가율 |
| bsop_prfi_inrt | 영업 이익 증가율 |
| ntin_inrt | 순이익 증가율 |
| equt_inrt | 자기자본 증가율 |
| cptl_tnrt | 총자본회전율 |
| sale_bond_tnrt | 매출 채권 회전율 |
| totl_aset_inrt | 총자산 증가율 |
| stac_month | 결산 월 |
| stac_month_cls_code | 결산 월 구분 코드 |
| iqry_csnu | 조회 건수 |

```python
# GET /uapi/domestic-stock/v1/ranking/finance-ratio — 수익성 분석 순위 (2024년 결산)
headers["tr_id"] = "FHPST01750000"
params = {
    "fid_cond_mrkt_div_code": "J",
    "fid_cond_scr_div_code": "20175",
    "fid_input_iscd": "0000",
    "fid_rank_sort_cls_code": "7",
    "fid_input_option_1": "2024",
    "fid_input_option_2": "3",
    "fid_div_cls_code": "0",
    "fid_trgt_cls_code": "0",
    "fid_trgt_exls_cls_code": "0",
    "fid_blng_cls_code": "",
    "fid_input_price_1": "",
    "fid_input_price_2": "",
    "fid_vol_cnt": ""
}
res = requests.get("https://openapi.koreainvestment.com:9443/uapi/domestic-stock/v1/ranking/finance-ratio",
                    headers=headers, params=params)
```

---


### 6. 국내주식 시간외잔량 순위 [v1_국내주식-093]

```
GET /uapi/domestic-stock/v1/ranking/after-hour-balance
```

**언제 사용:** 국내주식 시간외 잔량 순위를 조회할 때. 한국투자 HTS(eFriend Plus) > [0176] 시간외잔량 순위 화면의 기능을 API로 개발한 것으로, 장전 시간외/장후 시간외/매도잔량/매수잔량 기준으로 정렬할 수 있다.

**TR_ID:** `FHPST01760000`

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| fid_cond_mrkt_div_code | 조건 시장 분류 코드 | ✅ | `J` (주식 고정) |
| fid_cond_scr_div_code | 조건 화면 분류 코드 | ✅ | `20176` (고정값) |
| fid_input_iscd | 입력 종목코드 | ✅ | `0000`:전체, `0001`:거래소, `1001`:코스닥, `2001`:코스피200 |
| fid_rank_sort_cls_code | 순위 정렬 구분 코드 | ✅ | `1`:장전 시간외, `2`:장후 시간외, `3`:매도잔량, `4`:매수잔량 |
| fid_div_cls_code | 분류 구분 코드 | ✅ | `0`:전체 |
| fid_trgt_cls_code | 대상 구분 코드 | ✅ | `0`:전체 |
| fid_trgt_exls_cls_code | 대상 제외 구분 코드 | ✅ | `0`:전체 |
| fid_input_price_1 | 입력 가격1 | ✅ | 가격 범위 시작. 공란 시 전체 |
| fid_input_price_2 | 입력 가격2 | ✅ | 가격 범위 종료. 공란 시 전체 |
| fid_vol_cnt | 거래량 수 | ✅ | 거래량 기준. 공란 시 전체 |

**Response** (`output` — 배열)

| 필드 | 한글명 |
|------|--------|
| stck_shrn_iscd | 주식 단축 종목코드 |
| data_rank | 데이터 순위 |
| hts_kor_isnm | HTS 한글 종목명 |
| stck_prpr | 주식 현재가 |
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_ctrt | 전일 대비율 |
| ovtm_total_askp_rsqn | 시간외 총 매도호가 잔량 |
| ovtm_total_bidp_rsqn | 시간외 총 매수호가 잔량 |
| mkob_otcp_vol | 장개시전 시간외종가 거래량 |
| mkfa_otcp_vol | 장종료후 시간외종가 거래량 |

```python
# GET /uapi/domestic-stock/v1/ranking/after-hour-balance — 장후 시간외 잔량 순위
headers["tr_id"] = "FHPST01760000"
params = {
    "fid_cond_mrkt_div_code": "J",
    "fid_cond_scr_div_code": "20176",
    "fid_input_iscd": "0000",
    "fid_rank_sort_cls_code": "2",
    "fid_div_cls_code": "0",
    "fid_trgt_cls_code": "0",
    "fid_trgt_exls_cls_code": "0",
    "fid_input_price_1": "",
    "fid_input_price_2": "",
    "fid_vol_cnt": ""
}
res = requests.get("https://openapi.koreainvestment.com:9443/uapi/domestic-stock/v1/ranking/after-hour-balance",
                    headers=headers, params=params)
```

---


### 7. 국내주식 우선주/괴리율 상위 [v1_국내주식-094]

```
GET /uapi/domestic-stock/v1/ranking/prefer-disparate-ratio
```

**언제 사용:** 국내주식 우선주/괴리율 상위 종목을 조회할 때. 한국투자 HTS(eFriend Plus) > [0177] 우선주/괴리율 상위 화면의 기능을 API로 개발한 것으로, 보통주와 우선주 간의 가격 차이(괴리율)를 확인할 수 있다.

**TR_ID:** `FHPST01770000`

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| fid_cond_mrkt_div_code | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT |
| fid_cond_scr_div_code | 조건 화면 분류 코드 | ✅ | `20177` (고정값) |
| fid_input_iscd | 입력 종목코드 | ✅ | `0000`:전체, `0001`:거래소, `1001`:코스닥, `2001`:코스피200 |
| fid_div_cls_code | 분류 구분 코드 | ✅ | `0`:전체 |
| fid_trgt_cls_code | 대상 구분 코드 | ✅ | `0`:전체 |
| fid_trgt_exls_cls_code | 대상 제외 구분 코드 | ✅ | `0`:전체 |
| fid_input_price_1 | 입력 가격1 | ✅ | 가격 범위 시작. 공란 시 전체 |
| fid_input_price_2 | 입력 가격2 | ✅ | 가격 범위 종료. 공란 시 전체 |
| fid_vol_cnt | 거래량 수 | ✅ | 거래량 기준. 공란 시 전체 |

**Response** (`output` — 배열)

| 필드 | 한글명 |
|------|--------|
| mksc_shrn_iscd | 유가증권 단축 종목코드 |
| data_rank | 데이터 순위 |
| hts_kor_isnm | HTS 한글 종목명 |
| stck_prpr | 주식 현재가 |
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_ctrt | 전일 대비율 |
| acml_vol | 누적 거래량 |
| prst_iscd | 우선주 종목코드 |
| prst_kor_isnm | 우선주 한글 종목명 |
| prst_prpr | 우선주 현재가 |
| prst_prdy_vrss | 우선주 전일대비 |
| prst_prdy_vrss_sign | 우선주 전일 대비 부호 |
| prst_acml_vol | 우선주 누적 거래량 |
| diff_prpr | 차이 현재가 |
| dprt | 괴리율 |
| prst_prdy_ctrt | 우선주 전일 대비율 |

```python
# GET /uapi/domestic-stock/v1/ranking/prefer-disparate-ratio — 우선주/괴리율 상위
headers["tr_id"] = "FHPST01770000"
params = {
    "fid_cond_mrkt_div_code": "J",
    "fid_cond_scr_div_code": "20177",
    "fid_input_iscd": "0000",
    "fid_div_cls_code": "0",
    "fid_trgt_cls_code": "0",
    "fid_trgt_exls_cls_code": "0",
    "fid_input_price_1": "",
    "fid_input_price_2": "",
    "fid_vol_cnt": ""
}
res = requests.get("https://openapi.koreainvestment.com:9443/uapi/domestic-stock/v1/ranking/prefer-disparate-ratio",
                    headers=headers, params=params)
```

---


## 공통 응답 구조

모든 API의 응답은 아래 공통 필드를 포함한다.

| 필드 | 타입 | 설명 |
|------|------|------|
| rt_cd | String | `0`: 성공, 그 외: 오류 |
| msg_cd | String | 응답코드 (ex: `MCA00000`) |
| msg1 | String | 응답메시지 |
| output | Array | API별 순위 데이터 배열 |

> **참고:** 순위분석 API는 전부 `tr_cont`를 이용한 연속조회(페이징)가 불가하므로, 한 번의 호출로 전체 결과를 수신한다.
