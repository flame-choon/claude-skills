# KIS [국내주식] 기본시세 Endpoints

실전 Base: `https://openapi.koreainvestment.com:9443`
모의 Base: `https://openapivts.koreainvestment.com:29443`

---

## 엔드포인트 선택 가이드

| 상황 | 사용할 엔드포인트 |
|------|----------------|
| 주식 현재가 종합 정보 (시세, PER, EPS 등) | `GET /uapi/domestic-stock/v1/quotations/inquire-price` |
| 주식 현재가 간단 정보 (등락률, 상한가 등) | `GET /uapi/domestic-stock/v1/quotations/inquire-price-2` |
| 주식 현재가 체결 내역 | `GET /uapi/domestic-stock/v1/quotations/inquire-ccnl` |
| 주식 현재가 일자별 시세 (최근 30일/30주/30개월) | `GET /uapi/domestic-stock/v1/quotations/inquire-daily-price` |
| 주식 호가 및 예상체결가 | `GET /uapi/domestic-stock/v1/quotations/inquire-asking-price-exp-ccn` |
| 주식 투자자별 매매 동향 | `GET /uapi/domestic-stock/v1/quotations/inquire-investor` |
| 주식 회원사별 매매 동향 | `GET /uapi/domestic-stock/v1/quotations/inquire-member` |
| 국내주식 기간별 시세 (일/주/월/년봉) | `GET /uapi/domestic-stock/v1/quotations/inquire-daily-itemchartprice` |
| 주식 당일 분봉 데이터 (시간별) | `GET /uapi/domestic-stock/v1/quotations/inquire-time-itemchartprice` |
| 주식 일별 분봉 데이터 (날짜+시간 지정) | `GET /uapi/domestic-stock/v1/quotations/inquire-time-dailychartprice` |
| 주식 당일 시간대별 체결 내역 | `GET /uapi/domestic-stock/v1/quotations/inquire-time-itemconclusion` |
| 주식 시간외 일자별 주가 | `GET /uapi/domestic-stock/v1/quotations/inquire-daily-overtimeprice` |
| 주식 시간외 시간별 체결 | `GET /uapi/domestic-stock/v1/quotations/inquire-time-overtimeconclusion` |
| 주식 시간외 현재가 | `GET /uapi/domestic-stock/v1/quotations/inquire-overtime-price` |
| 주식 시간외 호가 | `GET /uapi/domestic-stock/v1/quotations/inquire-overtime-asking-price` |
| 장마감 예상체결가 순위 | `GET /uapi/domestic-stock/v1/quotations/exp-closing-price` |
| ETF/ETN 현재가 | `GET /uapi/etfetn/v1/quotations/inquire-price` |
| ETF 구성종목 시세 | `GET /uapi/etfetn/v1/quotations/inquire-component-stock-price` |
| ETF/ETN NAV 비교추이 (종목별 실시간) | `GET /uapi/etfetn/v1/quotations/nav-comparison-trend` |
| ETF/ETN NAV 비교추이 (일자별) | `GET /uapi/etfetn/v1/quotations/nav-comparison-daily-trend` |
| ETF/ETN NAV 비교추이 (분봉) | `GET /uapi/etfetn/v1/quotations/nav-comparison-time-trend` |

---

## 공통 요청 Header

모든 기본시세 API는 아래 공통 요청 헤더를 사용한다. (GET 요청이므로 `hashkey` 불필요)

| 파라미터 | 한글명 | 타입 | 필수 | 길이 | 설명 |
|---------|--------|------|------|------|------|
| content-type | 컨텐츠타입 | String | ✅ | 40 | `application/json; charset=utf-8` |
| authorization | 접근토큰 | String | ✅ | 350 | `Bearer {access_token}` |
| appkey | 앱키 | String | ✅ | 36 | KIS 홈페이지 발급 앱키 (노출 금지) |
| appsecret | 앱시크릿키 | String | ✅ | 180 | KIS 홈페이지 발급 앱시크릿 (노출 금지) |
| personalseckey | 고객식별키 | String | ❌ | 180 | [법인 필수] 제휴사 회원 고객식별키 |
| tr_id | 거래ID | String | ✅ | 13 | API마다 다름 (아래 각 API 항목 참조) |
| tr_cont | 연속거래여부 | String | ❌ | 1 | 첫 조회 시 공란, 연속 조회 시 `N` (응답 tr_cont가 `M`이면 다음 페이지 있음) |
| custtype | 고객타입 | String | ✅ | 1 | `P`: 개인, `B`: 법인 |
| seq_no | 일련번호 | String | ❌ | 2 | [법인 필수] `001` |
| mac_address | 맥주소 | String | ❌ | 12 | [법인] Mac address |
| phone_number | 핸드폰번호 | String | ❌ | 12 | [법인 필수] 핸드폰번호 (하이픈 제거) |
| ip_addr | 접속단말 공인IP | String | ❌ | 12 | [법인 필수] 사용자 IP |
| gt_uid | Global UID | String | ❌ | 32 | [법인 전용] 거래별 UNIQUE 고유번호 |

---

## [국내주식] 기본시세 API

### 1. 주식현재가 시세 [v1_국내주식-008]

```
GET /uapi/domestic-stock/v1/quotations/inquire-price
```

**언제 사용:** 주식 종목의 현재가 및 종합 시세 정보를 조회할 때. 현재가, 전일 대비, 누적 거래량, 52주 최고/최저, PER, PBR, EPS, 시가총액, 상/하한가 등 풍부한 시세 정보를 제공한다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHKST01010100` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT, `UN`:통합 |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리 (ex. `005930`). ETN은 앞에 `Q` 추가 |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| stck_prpr | 주식 현재가 |
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 (1:상한/2:상승/3:보합/4:하락/5:하한) |
| prdy_ctrt | 전일 대비율 |
| acml_vol | 누적 거래량 |
| acml_tr_pbmn | 누적 거래 대금 |
| stck_oprc | 주식 시가 |
| stck_hgpr | 주식 최고가 |
| stck_lwpr | 주식 최저가 |
| stck_mxpr | 주식 상한가 |
| stck_llam | 주식 하한가 |
| stck_sdpr | 주식 기준가 |
| stck_mxpr | 상한가 |
| w52_hgpr | 52주 최고가 |
| w52_lwpr | 52주 최저가 |
| hts_avls | HTS 시가총액 |
| per | PER |
| pbr | PBR |
| eps | EPS |
| bps | BPS |
| lstn_stcn | 상장 주수 |
| stck_fcam | 주식 액면가 |
| cpfn | 자본금 |
| vol_tnrt | 거래량 회전율 |
| bstp_kor_isnm | 업종 한글 종목명 |
| marg_rate | 증거금 비율 |
| hts_frgn_ehrt | HTS 외국인 소진율 |
| frgn_ntby_qty | 외국인 순매수 수량 |
| mrkt_warn_cls_code | 시장경고코드 |
| vi_cls_code | VI 적용구분코드 |
| ssts_yn | 공매도 가능 여부 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-price
import requests

url = "https://openapi.koreainvestment.com:9443/uapi/domestic-stock/v1/quotations/inquire-price"
headers = {
    "content-type": "application/json; charset=utf-8",
    "authorization": "Bearer {access_token}",
    "appkey": "PSxxxxxxxxxxxxxxxxxx",
    "appsecret": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...",
    "tr_id": "FHKST01010100",
    "custtype": "P"
}
params = {
    "FID_COND_MRKT_DIV_CODE": "J",
    "FID_INPUT_ISCD": "005930"
}
res = requests.get(url, headers=headers, params=params)

# 응답 예시
{
    "rt_cd": "0",
    "msg_cd": "MCA00000",
    "msg1": "정상처리 되었습니다.",
    "output": {
        "stck_prpr": "71400",
        "prdy_vrss": "800",
        "prdy_vrss_sign": "2",
        "prdy_ctrt": "1.13",
        "acml_vol": "12345678"
    }
}
```

---

### 2. 주식현재가 시세2 [v1_국내주식-009]

```
GET /uapi/domestic-stock/v1/quotations/inquire-price-2
```

**언제 사용:** 주식 현재가의 경량 버전으로, 등락 여부, 상한가/하한가 여부, 이상급등 여부 등 상태 플래그 위주의 정보가 필요할 때. inquire-price보다 응답 필드가 적어 경량 조회에 적합하다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전 | `FHPST01010000` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT, `UN`:통합 |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리 |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| stck_mxpr | 주식 상한가 |
| stck_llam | 주식 하한가 |
| stck_lwpr | 주식 최저가 |
| stck_hgpr | 주식 최고가 |
| mxpr_llam_cls_code | 상하한가 구분 코드 |
| crdt_able_yn | 신용 가능 여부 |
| vi_cls_code | VI 적용구분코드 |
| ssts_hot_yn | 공매도 과열 여부 |
| stange_runup_yn | 이상급등 여부 |
| short_over_yn | 단기과열 여부 |
| mrkt_warn_cls_code | 시장경고코드 |
| invt_caful_yn | 투자유의 여부 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-price-2
headers["tr_id"] = "FHPST01010000"
params = {"FID_COND_MRKT_DIV_CODE": "J", "FID_INPUT_ISCD": "000660"}
res = requests.get(url.replace("inquire-price", "inquire-price-2"), headers=headers, params=params)
```

---

### 3. 주식현재가 체결 [v1_국내주식-013]

```
GET /uapi/domestic-stock/v1/quotations/inquire-ccnl
```

**언제 사용:** 주식 종목의 현재가 기준 체결 내역(틱 데이터)을 조회할 때. 최근 체결 시간, 체결 가격, 체결량, 누적 거래량 등을 반환한다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHKST01010300` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT, `UN`:통합 |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리 |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| stck_cntg_hour | 주식 체결 시간 |
| stck_prpr | 주식 현재가 (체결가) |
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_ctrt | 전일 대비율 |
| askp | 매도호가 |
| bidp | 매수호가 |
| cntg_vol | 체결 거래량 |
| acml_vol | 누적 거래량 |
| acml_tr_pbmn | 누적 거래 대금 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-ccnl
headers["tr_id"] = "FHKST01010300"
params = {"FID_COND_MRKT_DIV_CODE": "J", "FID_INPUT_ISCD": "005930"}
res = requests.get(".../inquire-ccnl", headers=headers, params=params)
```

---

### 4. 주식현재가 일자별 [v1_국내주식-010]

```
GET /uapi/domestic-stock/v1/quotations/inquire-daily-price
```

**언제 사용:** 주식 종목의 일자별/주별/월별 시세를 조회할 때. 최근 30거래일(일), 30주(주), 30개월(월) 데이터를 반환한다. 수정주가 반영 여부를 선택할 수 있다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHKST01010400` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT, `UN`:통합 |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리 |
| FID_PERIOD_DIV_CODE | 기간 분류 코드 | ✅ | `D`:일 (최근 30거래일), `W`:주, `M`:월 |
| FID_ORG_ADJ_PRC | 수정주가 원주가 가격 | ✅ | `0`:수정주가 반영, `1`:수정주가 미반영 |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| stck_bsop_date | 주식 영업 일자 |
| stck_clpr | 주식 종가 |
| stck_oprc | 주식 시가 |
| stck_hgpr | 주식 최고가 |
| stck_lwpr | 주식 최저가 |
| acml_vol | 누적 거래량 |
| acml_tr_pbmn | 누적 거래 대금 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_vrss | 전일 대비 |
| prdy_ctrt | 전일 대비율 |
| flng_cls_code | 락 구분 코드 |
| prtt_rate | 분할 비율 |
| mod_yn | 변경 여부 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-daily-price
headers["tr_id"] = "FHKST01010400"
params = {
    "FID_COND_MRKT_DIV_CODE": "J",
    "FID_INPUT_ISCD": "005930",
    "FID_PERIOD_DIV_CODE": "D",
    "FID_ORG_ADJ_PRC": "0"
}
res = requests.get(".../inquire-daily-price", headers=headers, params=params)
```

---

### 5. 주식현재가 호가/예상체결 [v1_국내주식-011]

```
GET /uapi/domestic-stock/v1/quotations/inquire-asking-price-exp-ccn
```

**언제 사용:** 주식 종목의 매도/매수 호가 10단계 및 예상 체결 정보를 조회할 때. 호가별 잔량, 예상 체결가, 예상 체결량을 반환한다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHKST01010200` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT, `UN`:통합 |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리 |

**Response** (`output1` — 호가, `output2` — 예상체결)

| 필드 | 한글명 |
|------|--------|
| askp1 ~ askp10 | 매도호가 1~10 |
| bidp1 ~ bidp10 | 매수호가 1~10 |
| askp_rsqn1 ~ askp_rsqn10 | 매도호가 잔량 1~10 |
| bidp_rsqn1 ~ bidp_rsqn10 | 매수호가 잔량 1~10 |
| total_askp_rsqn | 총 매도호가 잔량 |
| total_bidp_rsqn | 총 매수호가 잔량 |
| antc_cnpr | 예상 체결가 |
| antc_cnqn | 예상 체결량 |
| antc_vol | 예상 거래량 |
| antc_cntg_vrss | 예상 체결 대비 |
| antc_cntg_vrss_sign | 예상 체결 대비 부호 |
| antc_cntg_ctrt | 예상 체결 대비율 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-asking-price-exp-ccn
headers["tr_id"] = "FHKST01010200"
params = {"FID_COND_MRKT_DIV_CODE": "J", "FID_INPUT_ISCD": "005930"}
res = requests.get(".../inquire-asking-price-exp-ccn", headers=headers, params=params)
```

---

### 6. 주식현재가 투자자 [v1_국내주식-012]

```
GET /uapi/domestic-stock/v1/quotations/inquire-investor
```

**언제 사용:** 주식 종목의 기관/외국인/개인 등 투자자별 당일 매매 동향을 조회할 때. 투자자별 매도/매수/순매수 수량과 금액을 반환한다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHKST01010900` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT, `UN`:통합 |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리 |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| stck_bsop_date | 주식 영업 일자 |
| stck_prpr | 주식 현재가 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_vrss | 전일 대비 |
| prdy_ctrt | 전일 대비율 |
| acml_vol | 누적 거래량 |
| prsn_ntby_qty | 개인 순매수 수량 |
| frgn_ntby_qty | 외국인 순매수 수량 |
| orgn_ntby_qty | 기관계 순매수 수량 |
| prsn_ntby_tr_pbmn | 개인 순매수 거래 대금 |
| frgn_ntby_tr_pbmn | 외국인 순매수 거래 대금 |
| orgn_ntby_tr_pbmn | 기관계 순매수 거래 대금 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-investor
headers["tr_id"] = "FHKST01010900"
params = {"FID_COND_MRKT_DIV_CODE": "J", "FID_INPUT_ISCD": "005930"}
res = requests.get(".../inquire-investor", headers=headers, params=params)
```

---

### 7. 주식현재가 회원사 [v1_국내주식-014]

```
GET /uapi/domestic-stock/v1/quotations/inquire-member
```

**언제 사용:** 주식 종목의 증권사(회원사)별 매매 동향을 조회할 때. 매도/매수 상위 5개 회원사의 거래량을 반환한다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHKST01010600` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT, `UN`:통합 |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리. ETN은 `Q`로 시작 |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| seln_mbcr_no1 ~ seln_mbcr_no5 | 매도 회원사 번호 1~5 |
| seln_mbcr_name1 ~ seln_mbcr_name5 | 매도 회원사명 1~5 |
| total_seln_qty1 ~ total_seln_qty5 | 총 매도 수량 1~5 |
| shnu_mbcr_no1 ~ shnu_mbcr_no5 | 매수 회원사 번호 1~5 |
| shnu_mbcr_name1 ~ shnu_mbcr_name5 | 매수 회원사명 1~5 |
| total_shnu_qty1 ~ total_shnu_qty5 | 총 매수 수량 1~5 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-member
headers["tr_id"] = "FHKST01010600"
params = {"FID_COND_MRKT_DIV_CODE": "J", "FID_INPUT_ISCD": "005930"}
res = requests.get(".../inquire-member", headers=headers, params=params)
```

---

### 8. 국내주식 기간별 시세(일/주/월/년) [v1_국내주식-016]

```
GET /uapi/domestic-stock/v1/quotations/inquire-daily-itemchartprice
```

**언제 사용:** 주식 종목의 일/주/월/년봉 차트 데이터를 조회할 때. 조회 시작/종료일을 직접 지정하며, 최대 100개 데이터를 반환한다. 수정주가/원주가 선택 가능. 백테스팅, 차트 렌더링 등에 사용.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHKST03010100` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT, `UN`:통합 |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리 |
| FID_INPUT_DATE_1 | 조회 시작일자 | ✅ | `YYYYMMDD` 형식 (ex. `20240101`) |
| FID_INPUT_DATE_2 | 조회 종료일자 | ✅ | `YYYYMMDD` 형식. 최대 100개 |
| FID_PERIOD_DIV_CODE | 기간 분류 코드 | ✅ | `D`:일봉, `W`:주봉, `M`:월봉, `Y`:년봉 |
| FID_ORG_ADJ_PRC | 수정주가 원주가 가격 | ✅ | `0`:수정주가, `1`:원주가 |

**Response**

`output1` — 종목 기본정보

| 필드 | 한글명 |
|------|--------|
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_ctrt | 전일 대비율 |
| stck_prdy_clpr | 주식 전일 종가 |
| acml_vol | 누적 거래량 |
| acml_tr_pbmn | 누적 거래 대금 |
| hts_kor_isnm | HTS 한글 종목명 |
| stck_prpr | 주식 현재가 |
| stck_shrn_iscd | 주식 단축 종목코드 |
| per | PER |
| pbr | PBR |
| eps | EPS |

`output2` — 기간별 OHLCV 배열

| 필드 | 한글명 |
|------|--------|
| stck_bsop_date | 주식 영업 일자 |
| stck_clpr | 주식 종가 |
| stck_oprc | 주식 시가 |
| stck_hgpr | 주식 최고가 |
| stck_lwpr | 주식 최저가 |
| acml_vol | 누적 거래량 |
| acml_tr_pbmn | 누적 거래 대금 |
| flng_cls_code | 락 구분 코드 |
| prtt_rate | 분할 비율 |
| mod_yn | 변경 여부 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_vrss | 전일 대비 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-daily-itemchartprice
headers["tr_id"] = "FHKST03010100"
params = {
    "FID_COND_MRKT_DIV_CODE": "J",
    "FID_INPUT_ISCD": "005930",
    "FID_INPUT_DATE_1": "20240101",
    "FID_INPUT_DATE_2": "20241231",
    "FID_PERIOD_DIV_CODE": "D",
    "FID_ORG_ADJ_PRC": "0"
}
res = requests.get(".../inquire-daily-itemchartprice", headers=headers, params=params)
# output2는 배열로 반환됨 (최대 100개)
ohlcv = res.json()["output2"]
```

---

### 9. 주식 당일 분봉 조회 [v1_국내주식-022]

```
GET /uapi/domestic-stock/v1/quotations/inquire-time-itemchartprice
```

**언제 사용:** 주식 종목의 당일 분봉 차트 데이터를 조회할 때. 특정 시각 이전 데이터를 반환하며, `FID_PW_DATA_INCU_YN`으로 과거 데이터 포함 여부를 제어한다. 연속조회(`tr_cont`)로 전체 분봉을 수집할 수 있다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHKST03010200` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT, `UN`:통합 |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리 |
| FID_INPUT_HOUR_1 | 입력 시간 | ✅ | 기준 시각 (`HHMMSS`, ex. `130000`) |
| FID_PW_DATA_INCU_YN | 과거 데이터 포함 여부 | ✅ | `Y`:포함, `N`:미포함 |
| FID_ETC_CLS_CODE | 기타 구분 코드 | ✅ | 공백 입력 |

**Response** (`output2` — 분봉 배열)

| 필드 | 한글명 |
|------|--------|
| stck_bsop_date | 주식 영업 일자 |
| stck_cntg_hour | 주식 체결 시간 |
| stck_prpr | 주식 현재가 (종가) |
| stck_oprc | 주식 시가 |
| stck_hgpr | 주식 최고가 |
| stck_lwpr | 주식 최저가 |
| cntg_vol | 체결 거래량 |
| acml_tr_pbmn | 누적 거래 대금 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-time-itemchartprice
headers["tr_id"] = "FHKST03010200"
params = {
    "FID_COND_MRKT_DIV_CODE": "J",
    "FID_INPUT_ISCD": "005930",
    "FID_INPUT_HOUR_1": "153000",
    "FID_PW_DATA_INCU_YN": "Y",
    "FID_ETC_CLS_CODE": ""
}
res = requests.get(".../inquire-time-itemchartprice", headers=headers, params=params)
```

---

### 10. 주식 일별 분봉 조회 [국내주식-213]

```
GET /uapi/domestic-stock/v1/quotations/inquire-time-dailychartprice
```

**언제 사용:** 특정 날짜의 주식 분봉 데이터를 조회할 때. 날짜(`FID_INPUT_DATE_1`)와 기준 시각(`FID_INPUT_HOUR_1`)을 함께 지정하여 해당 날짜의 분봉을 가져온다. 과거 특정일의 분봉 데이터를 수집할 때 사용.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHKST03010230` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT, `UN`:통합 |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리 |
| FID_INPUT_HOUR_1 | 입력 시간 | ✅ | 기준 시각 (`HHMMSS`, ex. `130000`) |
| FID_INPUT_DATE_1 | 입력 날짜 | ✅ | 조회 날짜 (`YYYYMMDD`, ex. `20241023`) |
| FID_PW_DATA_INCU_YN | 과거 데이터 포함 여부 | ✅ | `Y`:포함, `N`:미포함 |
| FID_FAKE_TICK_INCU_YN | 허봉 포함 여부 | ✅ | 공백 필수 입력 |

**Response** (`output2` — 분봉 배열)

| 필드 | 한글명 |
|------|--------|
| stck_bsop_date | 주식 영업 일자 |
| stck_cntg_hour | 주식 체결 시간 |
| stck_prpr | 주식 현재가 (종가) |
| stck_oprc | 주식 시가 |
| stck_hgpr | 주식 최고가 |
| stck_lwpr | 주식 최저가 |
| cntg_vol | 체결 거래량 |
| acml_tr_pbmn | 누적 거래 대금 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-time-dailychartprice
headers["tr_id"] = "FHKST03010230"
params = {
    "FID_COND_MRKT_DIV_CODE": "J",
    "FID_INPUT_ISCD": "005930",
    "FID_INPUT_HOUR_1": "153000",
    "FID_INPUT_DATE_1": "20241023",
    "FID_PW_DATA_INCU_YN": "Y",
    "FID_FAKE_TICK_INCU_YN": ""
}
res = requests.get(".../inquire-time-dailychartprice", headers=headers, params=params)
```

---

### 11. 주식현재가 당일시간대별체결

```
GET /uapi/domestic-stock/v1/quotations/inquire-time-itemconclusion
```

**언제 사용:** 주식 종목의 당일 특정 시각 기준 시간대별 체결 내역을 조회할 때. 분 단위 체결 요약 데이터를 반환한다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHPST01060000` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`:KRX, `NX`:NXT, `UN`:통합 |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리 |
| FID_INPUT_HOUR_1 | 입력 시간 | ✅ | 기준 시각 (`HHMMSS`) |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| stck_cntg_hour | 주식 체결 시간 |
| stck_prpr | 주식 현재가 |
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_ctrt | 전일 대비율 |
| askp | 매도호가 |
| bidp | 매수호가 |
| tday_rltv | 당일 체결강도 |
| acml_vol | 누적 거래량 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-time-itemconclusion
headers["tr_id"] = "FHPST01060000"
params = {
    "FID_COND_MRKT_DIV_CODE": "J",
    "FID_INPUT_ISCD": "005930",
    "FID_INPUT_HOUR_1": "140000"
}
res = requests.get(".../inquire-time-itemconclusion", headers=headers, params=params)
```

---

### 12. 주식현재가 시간외 일자별주가

```
GET /uapi/domestic-stock/v1/quotations/inquire-daily-overtimeprice
```

**언제 사용:** 주식/ETF/ETN 종목의 시간외 단일가 일자별 주가를 조회할 때. 장 마감 후 시간외 거래 가격의 일별 이력을 확인할 수 있다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHPST02320000` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`: 주식, ETF, ETN |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리. ETN은 `Q`로 시작 |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| bsop_hour | 영업 시각 |
| stck_prpr | 주식 현재가 |
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_ctrt | 전일 대비율 |
| acml_vol | 누적 거래량 |
| acml_tr_pbmn | 누적 거래 대금 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-daily-overtimeprice
headers["tr_id"] = "FHPST02320000"
params = {"FID_COND_MRKT_DIV_CODE": "J", "FID_INPUT_ISCD": "005930"}
res = requests.get(".../inquire-daily-overtimeprice", headers=headers, params=params)
```

---

### 13. 주식현재가 시간외 시간별체결

```
GET /uapi/domestic-stock/v1/quotations/inquire-time-overtimeconclusion
```

**언제 사용:** 주식/ETF/ETN 종목의 시간외 단일가 시간별 체결 내역을 조회할 때.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHPST02310000` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`: 주식, ETF, ETN |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리. ETN은 `Q`로 시작 |
| FID_HOUR_CLS_CODE | 시간 구분 코드 | ✅ | `1`: 시간외 (Default) |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| bsop_hour | 영업 시각 |
| stck_prpr | 주식 현재가 |
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_ctrt | 전일 대비율 |
| cntg_vol | 체결 거래량 |
| acml_vol | 누적 거래량 |
| acml_tr_pbmn | 누적 거래 대금 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-time-overtimeconclusion
headers["tr_id"] = "FHPST02310000"
params = {
    "FID_COND_MRKT_DIV_CODE": "J",
    "FID_INPUT_ISCD": "005930",
    "FID_HOUR_CLS_CODE": "1"
}
res = requests.get(".../inquire-time-overtimeconclusion", headers=headers, params=params)
```

---

### 14. 국내주식 시간외현재가

```
GET /uapi/domestic-stock/v1/quotations/inquire-overtime-price
```

**언제 사용:** 주식 종목의 시간외 단일가 현재가를 조회할 때. 장 종료 후 시간외 거래 진행 중의 현재 호가/체결 정보를 반환한다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHPST02300000` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`: 주식 |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리 |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| ovtm_untp_prpr | 시간외 단일가 현재가 |
| ovtm_untp_prdy_vrss | 시간외 단일가 전일 대비 |
| ovtm_untp_prdy_vrss_sign | 시간외 단일가 전일 대비 부호 |
| ovtm_untp_prdy_ctrt | 시간외 단일가 전일 대비율 |
| ovtm_untp_vol | 시간외 단일가 거래량 |
| ovtm_untp_tr_pbmn | 시간외 단일가 거래 대금 |
| ovtm_untp_mxpr | 시간외 단일가 상한가 |
| ovtm_untp_llam | 시간외 단일가 하한가 |
| ovtm_untp_askp | 시간외 단일가 매도호가 |
| ovtm_untp_bidp | 시간외 단일가 매수호가 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-overtime-price
headers["tr_id"] = "FHPST02300000"
params = {"FID_COND_MRKT_DIV_CODE": "J", "FID_INPUT_ISCD": "005930"}
res = requests.get(".../inquire-overtime-price", headers=headers, params=params)
```

---

### 15. 국내주식 시간외호가

```
GET /uapi/domestic-stock/v1/quotations/inquire-overtime-asking-price
```

**언제 사용:** 주식 종목의 시간외 단일가 호가를 조회할 때.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHPST02300400` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | 종목코드 6자리 |
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`: 주식 |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| ovtm_untp_askp1 ~ ovtm_untp_askp5 | 시간외 단일가 매도호가 1~5 |
| ovtm_untp_bidp1 ~ ovtm_untp_bidp5 | 시간외 단일가 매수호가 1~5 |
| ovtm_untp_askp_rsqn1 ~ ovtm_untp_askp_rsqn5 | 시간외 단일가 매도호가 잔량 1~5 |
| ovtm_untp_bidp_rsqn1 ~ ovtm_untp_bidp_rsqn5 | 시간외 단일가 매수호가 잔량 1~5 |
| total_ovtm_untp_askp_rsqn | 총 시간외 단일가 매도호가 잔량 |
| total_ovtm_untp_bidp_rsqn | 총 시간외 단일가 매수호가 잔량 |

```python
# GET /uapi/domestic-stock/v1/quotations/inquire-overtime-asking-price
headers["tr_id"] = "FHPST02300400"
params = {"FID_COND_MRKT_DIV_CODE": "J", "FID_INPUT_ISCD": "005930"}
res = requests.get(".../inquire-overtime-asking-price", headers=headers, params=params)
```

---

### 16. 국내주식 장마감 예상체결가

```
GET /uapi/domestic-stock/v1/quotations/exp-closing-price
```

**언제 사용:** 장마감 예상체결가 순위를 조회할 때. 상한가/하한가 마감 예상 종목이나 직전 대비 상승/하락률 상위 종목을 확인할 수 있다. 전체 시장 또는 특정 시장(거래소/코스닥 등)을 선택할 수 있다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHKST117300C0` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_RANK_SORT_CLS_CODE | 순위 정렬 구분 코드 | ✅ | `0`:전체, `1`:상한가마감예상, `2`:하한가마감예상, `3`:직전대비상승률상위, `4`:직전대비하락률상위 |
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J`: 주식 |
| FID_COND_SCR_DIV_CODE | 조건 화면 분류 코드 | ✅ | `11173` (고정값) |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | `0000`:전체, `0001`:거래소, `1001`:코스닥, `2001`:코스피200, `4001`:KRX100 |
| FID_BLNG_CLS_CODE | 소속 구분 코드 | ✅ | `0`:전체, `1`:종가범위연장 |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| stck_shrn_iscd | 주식 단축 종목코드 |
| hts_kor_isnm | HTS 한글 종목명 |
| stck_prpr | 주식 현재가 (예상체결가) |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_vrss | 전일 대비 |
| prdy_ctrt | 전일 대비율 |
| acml_vol | 누적 거래량 |

```python
# GET /uapi/domestic-stock/v1/quotations/exp-closing-price
headers["tr_id"] = "FHKST117300C0"
params = {
    "FID_RANK_SORT_CLS_CODE": "0",
    "FID_COND_MRKT_DIV_CODE": "J",
    "FID_COND_SCR_DIV_CODE": "11173",
    "FID_INPUT_ISCD": "0000",
    "FID_BLNG_CLS_CODE": "0"
}
res = requests.get(".../exp-closing-price", headers=headers, params=params)
```

---

## [ETF/ETN] 기본시세 API

> **경로 주의:** ETF/ETN API는 `/uapi/etfetn/v1/quotations/` 경로를 사용한다 (domestic-stock 아님).

### 17. ETF/ETN 현재가

```
GET /uapi/etfetn/v1/quotations/inquire-price
```

**언제 사용:** ETF 또는 ETN 종목의 현재가 및 NAV 정보를 조회할 때. ETF/ETN 전용 정보(NAV, 괴리율, 추적오차 등)를 포함한다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHPST02400000` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| fid_input_iscd | 입력 종목코드 | ✅ | ETF/ETN 종목코드 |
| fid_cond_mrkt_div_code | 조건 시장 분류 코드 | ✅ | `J` (고정) |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| stck_prpr | 주식 현재가 |
| prdy_vrss | 전일 대비 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_ctrt | 전일 대비율 |
| acml_vol | 누적 거래량 |
| acml_tr_pbmn | 누적 거래 대금 |
| nav | NAV (순자산가치) |
| nav_vrss_unpr_ctrt | NAV 대비 괴리율 |
| etf_txtn_type_name | ETF 세금 유형명 |
| stck_mxpr | 상한가 |
| stck_llam | 하한가 |

```python
# GET /uapi/etfetn/v1/quotations/inquire-price
url = "https://openapi.koreainvestment.com:9443/uapi/etfetn/v1/quotations/inquire-price"
headers["tr_id"] = "FHPST02400000"
params = {"fid_input_iscd": "069500", "fid_cond_mrkt_div_code": "J"}
res = requests.get(url, headers=headers, params=params)
```

---

### 18. ETF 구성종목 시세

```
GET /uapi/etfetn/v1/quotations/inquire-component-stock-price
```

**언제 사용:** ETF를 구성하는 종목별 현재 시세를 조회할 때. ETF 바스켓 내 각 종목의 비중, 현재가, 전일 대비를 확인할 수 있다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHKST121600C0` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J` |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | ETF 종목코드 |
| FID_COND_SCR_DIV_CODE | 조건 화면 분류 코드 | ✅ | `11216` (고정값) |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| stck_shrn_iscd | 주식 단축 종목코드 |
| hts_kor_isnm | HTS 한글 종목명 |
| stck_prpr | 주식 현재가 |
| prdy_vrss_sign | 전일 대비 부호 |
| prdy_vrss | 전일 대비 |
| prdy_ctrt | 전일 대비율 |
| acml_vol | 누적 거래량 |
| cpno_prmt_rate | 구성 종목 비중 |

```python
# GET /uapi/etfetn/v1/quotations/inquire-component-stock-price
url = "https://openapi.koreainvestment.com:9443/uapi/etfetn/v1/quotations/inquire-component-stock-price"
headers["tr_id"] = "FHKST121600C0"
params = {
    "FID_COND_MRKT_DIV_CODE": "J",
    "FID_INPUT_ISCD": "069500",
    "FID_COND_SCR_DIV_CODE": "11216"
}
res = requests.get(url, headers=headers, params=params)
```

---

### 19. ETF/ETN NAV 비교추이 (종목)

```
GET /uapi/etfetn/v1/quotations/nav-comparison-trend
```

**언제 사용:** ETF/ETN 종목의 현재 NAV와 시장가 비교 추이를 실시간으로 조회할 때. 괴리율 모니터링에 활용.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHPST02440000` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| FID_COND_MRKT_DIV_CODE | 조건 시장 분류 코드 | ✅ | `J` |
| FID_INPUT_ISCD | 입력 종목코드 | ✅ | ETF/ETN 종목코드 |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| bsop_hour | 영업 시각 |
| stck_prpr | 주식 현재가 |
| nav | NAV |
| nav_vrss_unpr_ctrt | NAV 대비 괴리율 |
| acml_vol | 누적 거래량 |

```python
# GET /uapi/etfetn/v1/quotations/nav-comparison-trend
url = "https://openapi.koreainvestment.com:9443/uapi/etfetn/v1/quotations/nav-comparison-trend"
headers["tr_id"] = "FHPST02440000"
params = {"FID_COND_MRKT_DIV_CODE": "J", "FID_INPUT_ISCD": "069500"}
res = requests.get(url, headers=headers, params=params)
```

---

### 20. ETF/ETN NAV 비교추이 (일자별)

```
GET /uapi/etfetn/v1/quotations/nav-comparison-daily-trend
```

**언제 사용:** ETF/ETN 종목의 일자별 NAV 비교 추이를 조회할 때. 지정 기간의 종가, NAV, 괴리율 이력을 반환한다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHPST02440200` |

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| fid_cond_mrkt_div_code | 조건 시장 분류 코드 | ✅ | `J` |
| fid_input_iscd | 입력 종목코드 | ✅ | ETF/ETN 종목코드 6자리 |
| fid_input_date_1 | 조회 시작일자 | ✅ | `YYYYMMDD` (ex. `20240101`) |
| fid_input_date_2 | 조회 종료일자 | ✅ | `YYYYMMDD` (ex. `20240220`) |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| stck_bsop_date | 주식 영업 일자 |
| stck_clpr | 주식 종가 |
| nav | NAV |
| nav_vrss_unpr_ctrt | NAV 대비 괴리율 |
| acml_vol | 누적 거래량 |
| acml_tr_pbmn | 누적 거래 대금 |

```python
# GET /uapi/etfetn/v1/quotations/nav-comparison-daily-trend
url = "https://openapi.koreainvestment.com:9443/uapi/etfetn/v1/quotations/nav-comparison-daily-trend"
headers["tr_id"] = "FHPST02440200"
params = {
    "fid_cond_mrkt_div_code": "J",
    "fid_input_iscd": "069500",
    "fid_input_date_1": "20240101",
    "fid_input_date_2": "20240220"
}
res = requests.get(url, headers=headers, params=params)
```

---

### 21. ETF/ETN NAV 비교추이 (분봉)

```
GET /uapi/etfetn/v1/quotations/nav-comparison-time-trend
```

**언제 사용:** ETF/ETN 종목의 분봉 단위 NAV 비교 추이를 조회할 때. 1분~120분봉 단위를 선택하여 장중 NAV와 시장가의 비교 추이를 확인한다.

**TR_ID**

| 구분 | TR_ID |
|------|-------|
| 실전/모의 | `FHPST02440100` |

> **주의:** `fid_cond_mrkt_div_code` 값은 `E` (고정)이며 `J`가 아님에 유의.

**Query Parameters**

| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| fid_hour_cls_code | 시간 구분 코드 | ✅ | `60`:1분, `180`:3분, `300`:5분, `600`:10분, `1800`:30분, `3600`:60분, `7200`:120분 |
| fid_cond_mrkt_div_code | 조건 시장 분류 코드 | ✅ | `E` (고정값) |
| fid_input_iscd | 입력 종목코드 | ✅ | ETF/ETN 종목코드 |

**Response** (`output`)

| 필드 | 한글명 |
|------|--------|
| stck_bsop_date | 주식 영업 일자 |
| bsop_hour | 영업 시각 |
| stck_prpr | 주식 현재가 |
| nav | NAV |
| nav_vrss_unpr_ctrt | NAV 대비 괴리율 |
| acml_vol | 누적 거래량 |

```python
# GET /uapi/etfetn/v1/quotations/nav-comparison-time-trend
url = "https://openapi.koreainvestment.com:9443/uapi/etfetn/v1/quotations/nav-comparison-time-trend"
headers["tr_id"] = "FHPST02440100"
params = {
    "fid_hour_cls_code": "60",       # 1분봉
    "fid_cond_mrkt_div_code": "E",   # E 고정 (J 아님!)
    "fid_input_iscd": "069500"
}
res = requests.get(url, headers=headers, params=params)
```
