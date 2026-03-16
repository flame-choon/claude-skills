# KIS [국내주식] 주문/계좌 Endpoints

실전 Base: `https://openapi.koreainvestment.com:9443`
모의 Base: `https://openapivts.koreainvestment.com:29443`

> **TR_ID 규칙:** POST(주문/정정) API는 `U` 접미사, GET(조회) API는 `R` 접미사

---


## 엔드포인트 선택 가이드

Claude는 아래 기준으로 적절한 엔드포인트를 선택한다.

| 상황 | 사용할 엔드포인트 |
|------|----------------|
| 현금 매수/매도 주문 | `POST /uapi/domestic-stock/v1/trading/order-cash` |
| 신용 매수/매도 주문 | `POST /uapi/domestic-stock/v1/trading/order-credit` |
| 미체결 주문 정정/취소 | `POST /uapi/domestic-stock/v1/trading/order-rvsecncl` |
| 정정/취소 가능 주문 목록 조회 | `GET /uapi/domestic-stock/v1/trading/inquire-psbl-rvsecncl` |
| 일별 주문체결 내역 조회 | `GET /uapi/domestic-stock/v1/trading/inquire-daily-ccld` |
| 보유 종목 잔고 조회 | `GET /uapi/domestic-stock/v1/trading/inquire-balance` |
| 매수 가능 금액/수량 조회 | `GET /uapi/domestic-stock/v1/trading/inquire-psbl-order` |
| 매도 가능 수량 조회 | `GET /uapi/domestic-stock/v1/trading/inquire-psbl-sell` |
| 신용 매수 가능 금액 조회 | `GET /uapi/domestic-stock/v1/trading/inquire-credit-psamount` |
| 예약주문 접수 | `POST /uapi/domestic-stock/v1/trading/order-resv` |
| 예약주문 정정/취소 | `POST /uapi/domestic-stock/v1/trading/order-resv-rvsecncl` |
| 예약주문 내역 조회 | `GET /uapi/domestic-stock/v1/trading/order-resv-ccnl` |
| 실현손익 포함 잔고 조회 | `GET /uapi/domestic-stock/v1/trading/inquire-balance-rlz-pl` |
| 계좌 전체 자산 현황 조회 | `GET /uapi/domestic-stock/v1/trading/inquire-account-balance` |
| 기간별 손익 합산 조회 | `GET /uapi/domestic-stock/v1/trading/inquire-period-profit` |
| 기간별 매매 손익 조회 | `GET /uapi/domestic-stock/v1/trading/inquire-period-trade-profit` |
| 통합증거금 현황 조회 | `GET /uapi/domestic-stock/v1/trading/intgr-margin` |
| 기간별 권리 현황 조회 | `GET /uapi/domestic-stock/v1/trading/period-rights` |
| 퇴직연금 체결기준 잔고 | `GET /uapi/domestic-stock/v1/trading/pension/inquire-present-balance` |
| 퇴직연금 일별 체결 조회 | `GET /uapi/domestic-stock/v1/trading/pension/inquire-daily-ccld` |
| 퇴직연금 매수 가능 조회 | `GET /uapi/domestic-stock/v1/trading/pension/inquire-psbl-order` |
| 퇴직연금 예수금 조회 | `GET /uapi/domestic-stock/v1/trading/pension/inquire-deposit` |
| 퇴직연금 잔고 조회 | `GET /uapi/domestic-stock/v1/trading/pension/inquire-balance` |

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
| tr_cont | 연속거래여부 | String | ❌ | 1 | 첫 조회 시 공란, 연속 조회 시 `N` (응답 tr_cont가 `M`이면 다음 페이지 있음) |
| custtype | 고객타입 | String | ✅ | 1 | `P`: 개인, `B`: 법인 |
| seq_no | 일련번호 | String | ❌ | 2 | [법인 필수] `001` |
| mac_address | 맥주소 | String | ❌ | 12 | [법인] Mac address |
| phone_number | 핸드폰번호 | String | ❌ | 12 | [법인 필수] 핸드폰번호 (하이픈 제거) |
| ip_addr | 접속단말 공인IP | String | ❌ | 12 | [법인 필수] 사용자 IP |
| gt_uid | Global UID | String | ❌ | 32 | [법인 전용] 거래별 UNIQUE 고유번호 |

---


## 주문 API

### 1. 주식주문(현금) [v1_국내주식-001]

```
POST /uapi/domestic-stock/v1/trading/order-cash
```

**언제 사용:** 보유 현금으로 국내주식을 매수하거나 매도할 때. ORD_QTY, ORD_UNPR 등을 String으로 전달해야 하며, POST body의 key값은 대문자로 작성해야 한다.

**TR_ID**
| 구분 | 실전 | 모의 |
|------|------|------|
| 매도 | `TTTC0011U` | `VTTC0011U` |
| 매수 | `TTTC0012U` | `VTTC0012U` |

**요청 Body**
| 파라미터 | 한글명 | 타입 | 필수 | 길이 | 설명 |
|---------|--------|------|------|------|------|
| CANO | 종합계좌번호 | String | ✅ | 8 | 계좌번호 앞 8자리 |
| ACNT_PRDT_CD | 계좌상품코드 | String | ✅ | 2 | 계좌번호 뒤 2자리 |
| PDNO | 상품번호 | String | ✅ | 12 | 종목코드 6자리 (ETN은 7자리) |
| SLL_TYPE | 매도유형 | String | ❌ | 2 | [매도 시] `01`:일반, `02`:임의, `05`:대차. 미입력 시 `01` |
| ORD_DVSN | 주문구분 | String | ✅ | 2 | `00`:지정가, `01`:시장가, `02`:조건부지정가, `03`:최유리지정가, `04`:최우선지정가, `05`:장전시간외, `06`:장후시간외, `07`:시간외단일가 |
| ORD_QTY | 주문수량 | String | ✅ | 10 | 주문수량 (String 타입) |
| ORD_UNPR | 주문단가 | String | ✅ | 19 | 주문단가. 시장가 등은 `"0"` 입력 |
| CNDT_PRIC | 조건가격 | String | ❌ | 19 | 스탑지정가 호가 시만 필수 |
| EXCG_ID_DVSN_CD | 거래소ID구분코드 | String | ❌ | 3 | `KRX`, `NXT`, `SOR` |

**응답 Body (output)**
| 필드 | 한글명 | 타입 | 설명 |
|------|--------|------|------|
| KRX_FWDG_ORD_ORGNO | 거래소전송주문조직번호 | String | 거래소코드 |
| ODNO | 주문번호 | String | 접수된 주문번호 |
| ORD_TMD | 주문시간 | String | HHmmss 형식 |

```python
# POST /uapi/domestic-stock/v1/trading/order-cash — 삼성전자 매수 (시장가 100주)
import requests

url = "https://openapi.koreainvestment.com:9443/uapi/domestic-stock/v1/trading/order-cash"
headers = {
    "content-type": "application/json; charset=utf-8",
    "authorization": "Bearer {access_token}",
    "appkey": "{appkey}",
    "appsecret": "{appsecret}",
    "tr_id": "TTTC0012U",  # 매수
    "custtype": "P"
}
body = {
    "CANO": "12345678",
    "ACNT_PRDT_CD": "01",
    "PDNO": "005930",
    "ORD_DVSN": "01",   # 시장가
    "ORD_QTY": "100",
    "ORD_UNPR": "0"
}
res = requests.post(url, json=body, headers=headers)

# 응답 예시
{
    "rt_cd": "0",
    "msg_cd": "APBK0013",
    "msg1": "주문 전송 완료 되었습니다.",
    "output": {
        "KRX_FWDG_ORD_ORGNO": "91252",
        "ODNO": "0000117057",
        "ORD_TMD": "101830"
    }
}
```

---

### 2. 주식주문(신용) [v1_국내주식-002]

```
POST /uapi/domestic-stock/v1/trading/order-credit
```

**언제 사용:** 신용거래(융자 매수, 대주 매도)로 주식을 주문할 때. **모의투자 미지원.**

**TR_ID**
| 구분 | 실전 | 모의 |
|------|------|------|
| 매도 | `TTTC0051U` | 미지원 |
| 매수 | `TTTC0052U` | 미지원 |

**요청 Body (주요)**
| 파라미터 | 한글명 | 타입 | 필수 | 설명 |
|---------|--------|------|------|------|
| CANO | 종합계좌번호 | String | ✅ | 계좌번호 앞 8자리 |
| ACNT_PRDT_CD | 계좌상품코드 | String | ✅ | 계좌번호 뒤 2자리 |
| PDNO | 상품번호 | String | ✅ | 종목코드 6자리 |
| CRDT_TYPE | 신용유형 | String | ✅ | [매도] `22`:유통대주신규, `24`:자기대주신규 / [매수] `21`:자기융자신규, `23`:유통융자신규 |
| LOAN_DT | 대출일자 | String | ✅ | [신규매수] 오늘날짜 / [매도] 해당 종목 대출일자 |
| ORD_DVSN | 주문구분 | String | ✅ | `00`:지정가, `01`:시장가 등 |
| ORD_QTY | 주문수량 | String | ✅ | 주문수량 |
| ORD_UNPR | 주문단가 | String | ✅ | 주문단가 |
| SLL_TYPE | 매도유형 | String | ❌ | 공란 입력 |

**응답 Body (output)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| ODNO | 주문번호 | 접수된 주문번호 |
| ORD_TMD | 주문시간 | HHmmss |

```python
# POST /uapi/domestic-stock/v1/trading/order-credit — 신용 매수
headers["tr_id"] = "TTTC0052U"
body = {
    "CANO": "12345678", "ACNT_PRDT_CD": "01",
    "PDNO": "005930", "SLL_TYPE": "",
    "CRDT_TYPE": "23", "LOAN_DT": "20260316",
    "ORD_DVSN": "00", "ORD_QTY": "10", "ORD_UNPR": "75000"
}
```

---

### 3. 주식주문(정정취소) [v1_국내주식-003]

```
POST /uapi/domestic-stock/v1/trading/order-rvsecncl
```

**언제 사용:** 미체결 상태의 주문을 정정하거나 취소할 때. 정정 시 KRX_FWDG_ORD_ORGNO와 ODNO(원주문번호)가 필요하다.

**TR_ID**
| 구분 | 실전 | 모의 |
|------|------|------|
| 정정/취소 | `TTTC0036U` | `VTTC0036U` |

**요청 Body**
| 파라미터 | 한글명 | 타입 | 필수 | 설명 |
|---------|--------|------|------|------|
| CANO | 종합계좌번호 | String | ✅ | 계좌번호 앞 8자리 |
| ACNT_PRDT_CD | 계좌상품코드 | String | ✅ | 계좌번호 뒤 2자리 |
| KRX_FWDG_ORD_ORGNO | 거래소전송주문조직번호 | String | ✅ | 주문 시 응답받은 조직번호 |
| ORGN_ODNO | 원주문번호 | String | ✅ | 정정/취소할 원주문번호 |
| ORD_DVSN | 주문구분 | String | ✅ | 정정 시 변경할 주문구분 |
| RVSE_CNCL_DVSN_CD | 정정취소구분코드 | String | ✅ | `01`:정정, `02`:취소 |
| ORD_QTY | 주문수량 | String | ✅ | 정정 수량 (전량취소 시 `0`) |
| ORD_UNPR | 주문단가 | String | ✅ | 정정 단가 (취소 시 `0`) |
| QTY_ALL_ORD_YN | 잔량전부주문여부 | String | ✅ | `Y`:잔량전부, `N`:일부 |

**응답 Body (output)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| KRX_FWDG_ORD_ORGNO | 거래소전송주문조직번호 | |
| ODNO | 주문번호 | 정정/취소 처리된 주문번호 |
| ORD_TMD | 주문시간 | |

```python
# POST /uapi/domestic-stock/v1/trading/order-rvsecncl — 주문 취소
headers["tr_id"] = "TTTC0036U"
body = {
    "CANO": "12345678", "ACNT_PRDT_CD": "01",
    "KRX_FWDG_ORD_ORGNO": "91252", "ORGN_ODNO": "0000117057",
    "ORD_DVSN": "00", "RVSE_CNCL_DVSN_CD": "02",
    "ORD_QTY": "0", "ORD_UNPR": "0", "QTY_ALL_ORD_YN": "Y"
}
```

---


## 조회 API — 주문/체결

### 4. 주식정정취소가능주문조회 [v1_국내주식-004]

```
GET /uapi/domestic-stock/v1/trading/inquire-psbl-rvsecncl
```

**언제 사용:** 정정 또는 취소가 가능한 미체결 주문 목록을 조회할 때. **모의투자 미지원.**

**TR_ID:** 실전 `TTTC0084R` | 모의 미지원

**Query Parameters**
| 파라미터 | 한글명 | 타입 | 필수 | 설명 |
|---------|--------|------|------|------|
| CANO | 종합계좌번호 | String | ✅ | 계좌번호 앞 8자리 |
| ACNT_PRDT_CD | 계좌상품코드 | String | ✅ | 계좌번호 뒤 2자리 |
| CTX_AREA_FK100 | 연속조회검색조건100 | String | ✅ | 최초 조회 시 공란, 연속 조회 시 응답값 입력 |
| CTX_AREA_NK100 | 연속조회키100 | String | ✅ | 최초 조회 시 공란, 연속 조회 시 응답값 입력 |
| INQR_DVSN_1 | 조회구분1 | String | ✅ | `0`:조회전체, `1`:ELW제외, `2`:ELW만 |
| INQR_DVSN_2 | 조회구분2 | String | ✅ | `0`:전체, `1`:매도, `2`:매수 |

**응답 Body (output1 — 주문 목록)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| ord_gno_brno | 주문채번지점번호 | |
| odno | 주문번호 | 취소/정정할 주문번호 |
| orgn_odno | 원주문번호 | |
| ord_dvsn_name | 주문구분명 | 지정가/시장가 등 |
| pdno | 상품번호 | 종목코드 |
| prdt_name | 상품명 | 종목명 |
| sll_buy_dvsn_cd | 매도매수구분코드 | `01`:매도, `02`:매수 |
| ord_qty | 주문수량 | |
| ord_unpr | 주문단가 | |
| ord_tmd | 주문시간 | |
| tot_ccld_qty | 총체결수량 | |
| rmn_qty | 잔여수량 | 정정/취소 가능 수량 |

```python
# GET /uapi/domestic-stock/v1/trading/inquire-psbl-rvsecncl
import requests

url = "https://openapi.koreainvestment.com:9443/uapi/domestic-stock/v1/trading/inquire-psbl-rvsecncl"
headers["tr_id"] = "TTTC0084R"
params = {
    "CANO": "12345678", "ACNT_PRDT_CD": "01",
    "CTX_AREA_FK100": "", "CTX_AREA_NK100": "",
    "INQR_DVSN_1": "0", "INQR_DVSN_2": "0"
}
res = requests.get(url, headers=headers, params=params)
```

---

### 5. 주식일별주문체결조회 [v1_국내주식-005]

```
GET /uapi/domestic-stock/v1/trading/inquire-daily-ccld
```

**언제 사용:** 일별 주문 및 체결 내역을 조회할 때. 조회 기간에 따라 TR_ID가 다르다.

**TR_ID**
| 구분 | 실전 | 모의 |
|------|------|------|
| 3개월 이내 | `TTTC0081R` | `VTTC0081R` |
| 3개월 이전 | `CTSC9215R` | `VTSC9215R` |

**Query Parameters**
| 파라미터 | 한글명 | 타입 | 필수 | 설명 |
|---------|--------|------|------|------|
| CANO | 종합계좌번호 | String | ✅ | 계좌번호 앞 8자리 |
| ACNT_PRDT_CD | 계좌상품코드 | String | ✅ | 계좌번호 뒤 2자리 |
| INQR_STRT_DT | 조회시작일자 | String | ✅ | YYYYMMDD |
| INQR_END_DT | 조회종료일자 | String | ✅ | YYYYMMDD |
| SLL_BUY_DVSN_CD | 매도매수구분코드 | String | ✅ | `00`:전체, `01`:매도, `02`:매수 |
| INQR_DVSN | 조회구분 | String | ✅ | `00`:역순, `01`:정순 |
| PDNO | 상품번호 | String | ❌ | 특정 종목만 조회 시 종목코드 입력 |
| CCLD_DVSN | 체결구분 | String | ✅ | `00`:전체, `01`:체결, `02`:미체결 |
| ORD_GNO_BRNO | 주문채번지점번호 | String | ❌ | |
| ODNO | 주문번호 | String | ❌ | 특정 주문만 조회 시 |
| INQR_DVSN_3 | 조회구분3 | String | ✅ | `00`:전체, `01`:현금, `02`:융자, `03`:대출, `04`:대주 |
| EXCG_DVSN_CD | 거래소구분코드 | String | ✅ | `01`:KRX, `02`:NXT |
| CTX_AREA_FK100 | 연속조회검색조건 | String | ✅ | 최초 공란 |
| CTX_AREA_NK100 | 연속조회키 | String | ✅ | 최초 공란 |

**응답 Body (output1 — 체결 목록)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| ord_dt | 주문일자 | YYYYMMDD |
| odno | 주문번호 | |
| orgn_odno | 원주문번호 | |
| sll_buy_dvsn_cd_name | 매도매수구분명 | |
| pdno | 상품번호 | 종목코드 |
| prdt_name | 상품명 | 종목명 |
| ord_qty | 주문수량 | |
| ord_unpr | 주문단가 | |
| tot_ccld_qty | 총체결수량 | |
| avg_prvs | 평균단가 | 체결 평균가 |
| pchs_amt | 매입금액 | |

```python
# GET /uapi/domestic-stock/v1/trading/inquire-daily-ccld — 오늘 체결 내역
headers["tr_id"] = "TTTC0081R"
params = {
    "CANO": "12345678", "ACNT_PRDT_CD": "01",
    "INQR_STRT_DT": "20260316", "INQR_END_DT": "20260316",
    "SLL_BUY_DVSN_CD": "00", "INQR_DVSN": "01",
    "PDNO": "", "CCLD_DVSN": "00",
    "ORD_GNO_BRNO": "", "ODNO": "",
    "INQR_DVSN_3": "00", "EXCG_DVSN_CD": "01",
    "CTX_AREA_FK100": "", "CTX_AREA_NK100": ""
}
res = requests.get(url.replace("inquire-psbl-rvsecncl", "inquire-daily-ccld"), headers=headers, params=params)
```

---


## 조회 API — 잔고/자산

### 6. 주식잔고조회 [v1_국내주식-006]

```
GET /uapi/domestic-stock/v1/trading/inquire-balance
```

**언제 사용:** 현재 보유 중인 주식 종목별 잔고와 평가 금액, 손익 정보를 조회할 때.

**TR_ID:** 실전 `TTTC8434R` | 모의 `VTTC8434R`

**Query Parameters**
| 파라미터 | 한글명 | 타입 | 필수 | 설명 |
|---------|--------|------|------|------|
| CANO | 종합계좌번호 | String | ✅ | 계좌번호 앞 8자리 |
| ACNT_PRDT_CD | 계좌상품코드 | String | ✅ | 계좌번호 뒤 2자리 |
| AFHR_FLPR_YN | 시간외단일가여부 | String | ✅ | `N`:기본값, `Y`:시간외단일가, `X`:NXT정규장 |
| OFL_YN | 오프라인여부 | String | ❌ | 공란(Default) |
| INQR_DVSN | 조회구분 | String | ✅ | `01`:대출일별, `02`:종목별 |
| UNPR_DVSN | 단가구분 | String | ✅ | `01`:기본값 |
| FUND_STTL_ICLD_YN | 펀드결제분포함여부 | String | ✅ | `N`:포함안함, `Y`:포함 |
| FNCG_AMT_AUTO_RDPT_YN | 융자금액자동상환여부 | String | ✅ | `N`:기본값 |
| PRCS_DVSN | 처리구분 | String | ✅ | `00`:전일매매포함, `01`:전일매매미포함 |
| CTX_AREA_FK100 | 연속조회검색조건 | String | ✅ | 최초 공란 |
| CTX_AREA_NK100 | 연속조회키 | String | ✅ | 최초 공란 |

**응답 Body (output1 — 종목별 잔고)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| pdno | 상품번호 | 종목코드 |
| prdt_name | 상품명 | 종목명 |
| hldg_qty | 보유수량 | |
| ord_psbl_qty | 주문가능수량 | |
| pchs_avg_pric | 매입평균가격 | |
| pchs_amt | 매입금액 | |
| prpr | 현재가 | |
| evlu_amt | 평가금액 | |
| evlu_pfls_amt | 평가손익금액 | |
| evlu_pfls_rt | 평가손익율 | |
| fltt_rt | 등락율 | |

**응답 Body (output2 — 계좌 합산)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| dnca_tot_amt | 예수금총금액 | |
| nxdy_excc_amt | 익일정산금액 | |
| prvs_rcdl_excc_amt | 가수도정산금액 | |
| tot_evlu_amt | 총평가금액 | 주식+예수금 |
| nass_amt | 순자산금액 | |
| pchs_amt_smtl_amt | 매입금액합계 | |
| evlu_amt_smtl_amt | 평가금액합계 | |
| evlu_pfls_smtl_amt | 평가손익합계 | |

```python
# GET /uapi/domestic-stock/v1/trading/inquire-balance
import requests

url = "https://openapi.koreainvestment.com:9443/uapi/domestic-stock/v1/trading/inquire-balance"
headers["tr_id"] = "TTTC8434R"
params = {
    "CANO": "12345678", "ACNT_PRDT_CD": "01",
    "AFHR_FLPR_YN": "N", "OFL_YN": "",
    "INQR_DVSN": "02", "UNPR_DVSN": "01",
    "FUND_STTL_ICLD_YN": "N", "FNCG_AMT_AUTO_RDPT_YN": "N",
    "PRCS_DVSN": "00",
    "CTX_AREA_FK100": "", "CTX_AREA_NK100": ""
}
res = requests.get(url, headers=headers, params=params)

# 응답 예시
{
    "rt_cd": "0",
    "output1": [
        {
            "pdno": "005930", "prdt_name": "삼성전자",
            "hldg_qty": "100", "pchs_avg_pric": "71500.00",
            "evlu_amt": "7500000", "evlu_pfls_amt": "500000", "evlu_pfls_rt": "7.14"
        }
    ],
    "output2": [
        {
            "dnca_tot_amt": "1000000",
            "tot_evlu_amt": "8500000",
            "evlu_pfls_smtl_amt": "500000"
        }
    ]
}
```

---

### 7. 주식잔고조회_실현손익 [v1_국내주식-041]

```
GET /uapi/domestic-stock/v1/trading/inquire-balance-rlz-pl
```

**언제 사용:** 보유 잔고에 실현손익(매도 확정 손익)을 포함하여 조회할 때.

**TR_ID:** 실전 `TTTC8435R` | 모의 `VTTC8435R`

**Query Parameters** — inquire-balance와 동일 (CANO, ACNT_PRDT_CD, AFHR_FLPR_YN, OFL_YN, INQR_DVSN, UNPR_DVSN, FUND_STTL_ICLD_YN, FNCG_AMT_AUTO_RDPT_YN, PRCS_DVSN, CTX_AREA_FK100, CTX_AREA_NK100)

**응답 Body (output1 추가 필드)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| rlzt_pfls | 실현손익 | 매도 확정 손익 |
| rlzt_erng_rt | 실현수익률 | |
| tot_evlu_pfls_amt | 총평가손익금액 | 미실현+실현 |

```python
headers["tr_id"] = "TTTC8435R"
# 나머지 params는 inquire-balance와 동일
```

---

### 8. 투자계좌자산현황조회 [v1_국내주식-048]

```
GET /uapi/domestic-stock/v1/trading/inquire-account-balance
```

**언제 사용:** 계좌 전체 자산(현금 + 주식 평가금액)의 현황을 한눈에 조회할 때.

**TR_ID:** 실전 `TTTC8418R` | 모의 `VTTC8418R`

**Query Parameters**
| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| CANO | 종합계좌번호 | ✅ | |
| ACNT_PRDT_CD | 계좌상품코드 | ✅ | |
| INQR_DVSN_1 | 조회구분1 | ✅ | `0`:기본 |
| BSPR_BF_DT_APLY_YN | 기준일자전적용여부 | ✅ | `N`:기본 |

**응답 Body (output2 — 계좌 자산 합계)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| tot_asst_amt | 총자산금액 | |
| tot_stck_evlu_amt | 주식총평가금액 | |
| dnca_tot_amt | 예수금총금액 | |
| tot_evlu_pfls_amt | 총평가손익금액 | |

```python
headers["tr_id"] = "TTTC8418R"
params = {
    "CANO": "12345678", "ACNT_PRDT_CD": "01",
    "INQR_DVSN_1": "0", "BSPR_BF_DT_APLY_YN": "N"
}
```

---


## 조회 API — 매수/매도 가능

### 9. 매수가능조회 [v1_국내주식-007]

```
GET /uapi/domestic-stock/v1/trading/inquire-psbl-order
```

**언제 사용:** 특정 종목을 특정 단가로 매수 가능한 최대 수량과 금액을 조회할 때.

**TR_ID:** 실전 `TTTC8908R` | 모의 `VTTC8908R`

**Query Parameters**
| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| CANO | 종합계좌번호 | ✅ | |
| ACNT_PRDT_CD | 계좌상품코드 | ✅ | |
| PDNO | 상품번호 | ✅ | 종목코드 6자리 |
| ORD_UNPR | 주문단가 | ✅ | 매수 희망가 |
| ORD_DVSN | 주문구분 | ✅ | `00`:지정가, `01`:시장가 등 |
| CMA_EVLU_AMT_ICLD_YN | CMA평가금액포함여부 | ✅ | `Y`:포함, `N`:미포함 |
| OVRS_ICLD_YN | 해외포함여부 | ✅ | `Y`:포함, `N`:미포함 |

**응답 Body (output)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| ord_psbl_cash | 주문가능현금 | |
| ord_psbl_sbst | 주문가능대용 | |
| ruse_psbl_amt | 재사용가능금액 | |
| fund_rpch_chgs | 펀드환매대금 | |
| nrcvb_buy_amt | 미수없는 매수금액 | 증거금 100% 기준 매수가능금액 |
| nrcvb_buy_qty | 미수없는 매수수량 | 증거금 100% 기준 매수가능수량 |
| max_buy_amt | 최대매수금액 | |
| max_buy_qty | 최대매수수량 | |
| cma_evlu_amt | CMA평가금액 | |

```python
headers["tr_id"] = "TTTC8908R"
params = {
    "CANO": "12345678", "ACNT_PRDT_CD": "01",
    "PDNO": "005930", "ORD_UNPR": "75000",
    "ORD_DVSN": "00",
    "CMA_EVLU_AMT_ICLD_YN": "N", "OVRS_ICLD_YN": "N"
}
```

---

### 10. 매도가능수량조회 [국내주식-165]

```
GET /uapi/domestic-stock/v1/trading/inquire-psbl-sell
```

**언제 사용:** 특정 종목의 매도 가능 수량을 조회할 때. (실제 보유수량 - 매도주문 중 수량)

**TR_ID:** 실전 `TTTC8434R` | 모의 `VTTC8434R`

> ⚠️ **주의:** inquire-balance와 동일한 TR_ID를 사용하므로 tr_id를 반드시 확인한다.

**Query Parameters**
| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| CANO | 종합계좌번호 | ✅ | |
| ACNT_PRDT_CD | 계좌상품코드 | ✅ | |
| PDNO | 상품번호 | ✅ | 종목코드 |
| CRDT_TYPE | 신용유형 | ✅ | `00`:기본 |
| LOAN_DT | 대출일자 | ✅ | 신용매도 시 대출일, 일반은 공란 |

**응답 Body (output)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| ord_psbl_qty | 주문가능수량 | 매도 가능 수량 |
| sll_psbl_qty_smtl | 매도가능수량합계 | |

```python
headers["tr_id"] = "TTTC8434R"
params = {
    "CANO": "12345678", "ACNT_PRDT_CD": "01",
    "PDNO": "005930", "CRDT_TYPE": "00", "LOAN_DT": ""
}
```

---

### 11. 신용매수가능조회 [v1_국내주식-042]

```
GET /uapi/domestic-stock/v1/trading/inquire-credit-psamount
```

**언제 사용:** 신용거래 시 매수 가능한 융자 금액을 조회할 때. **모의투자 미지원.**

**TR_ID:** 실전 `TTTC0940R` | 모의 미지원

**Query Parameters**
| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| CANO | 종합계좌번호 | ✅ | |
| ACNT_PRDT_CD | 계좌상품코드 | ✅ | |
| PDNO | 상품번호 | ✅ | 종목코드 |
| CRDT_TYPE | 신용유형 | ✅ | `21`:자기융자, `23`:유통융자 |

**응답 Body (output)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| psbl_amt | 가능금액 | 신용매수 가능금액 |
| max_loan_amt | 최대차입금액 | |

---


## 예약주문 API

### 12. 주식예약주문 [v1_국내주식-017]

```
POST /uapi/domestic-stock/v1/trading/order-resv
```

**언제 사용:** 장 마감 후 익일 자동으로 체결되도록 예약주문을 접수할 때. **모의투자 미지원.**

**TR_ID:** 실전 `TTTC0803U` | 모의 미지원

**요청 Body**
| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| CANO | 종합계좌번호 | ✅ | |
| ACNT_PRDT_CD | 계좌상품코드 | ✅ | |
| PDNO | 상품번호 | ✅ | 종목코드 |
| SLL_BUY_DVSN_CD | 매도매수구분코드 | ✅ | `01`:매도, `02`:매수 |
| ORD_DVSN | 주문구분 | ✅ | `00`:지정가, `01`:시장가 등 |
| ORD_QTY | 주문수량 | ✅ | 주문수량 |
| ORD_UNPR | 주문단가 | ✅ | 주문단가 |
| RSVN_ORD_END_DT | 예약주문종료일자 | ✅ | YYYYMMDD |

**응답 Body (output)**
| 필드 | 설명 |
|------|------|
| RSVN_ORD_SEQ | 예약주문순번 |
| ODNO | 예약주문번호 |

---

### 13. 주식예약주문정정취소 [v1_국내주식-018,019]

```
POST /uapi/domestic-stock/v1/trading/order-resv-rvsecncl
```

**언제 사용:** 접수된 예약주문을 정정하거나 취소할 때. **모의투자 미지원.**

**TR_ID**
| 구분 | 실전 |
|------|------|
| 정정 | `TTTC0037U` |
| 취소 | `TTTC0038U` |

**요청 Body (주요)**
| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| CANO | 종합계좌번호 | ✅ | |
| ACNT_PRDT_CD | 계좌상품코드 | ✅ | |
| RSVN_ORD_SEQ | 예약주문순번 | ✅ | 예약주문 조회에서 확인한 순번 |
| PDNO | 상품번호 | ✅ | 종목코드 |
| ORD_DVSN | 주문구분 | ✅ | 변경할 주문구분 (정정 시) |
| ORD_QTY | 주문수량 | ✅ | 변경할 수량 |
| ORD_UNPR | 주문단가 | ✅ | 변경할 단가 |

---

### 14. 주식예약주문조회 [v1_국내주식-020]

```
GET /uapi/domestic-stock/v1/trading/order-resv-ccnl
```

**언제 사용:** 접수된 예약주문 내역을 조회할 때. **모의투자 미지원.**

**TR_ID:** 실전 `TTTC0804R` | 모의 미지원

**Query Parameters**
| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| CANO | 종합계좌번호 | ✅ | |
| ACNT_PRDT_CD | 계좌상품코드 | ✅ | |
| INQR_STRT_DT | 조회시작일자 | ✅ | YYYYMMDD |
| INQR_END_DT | 조회종료일자 | ✅ | YYYYMMDD |
| SLL_BUY_DVSN_CD | 매도매수구분 | ✅ | `00`:전체, `01`:매도, `02`:매수 |
| PDNO | 상품번호 | ❌ | 특정 종목 조회 시 |
| CTX_AREA_FK100 | 연속조회검색조건 | ✅ | 최초 공란 |
| CTX_AREA_NK100 | 연속조회키 | ✅ | 최초 공란 |

**응답 Body (output1 — 예약주문 목록)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| rsvn_ord_seq | 예약주문순번 | |
| rsvn_ord_rcit_dt | 예약접수일자 | |
| pdno | 상품번호 | 종목코드 |
| prdt_name | 상품명 | |
| sll_buy_dvsn_cd_name | 매도매수구분명 | |
| ord_dvsn_name | 주문구분명 | |
| ord_qty | 주문수량 | |
| ord_unpr | 주문단가 | |
| rsvn_ord_end_dt | 예약종료일자 | |

---


## 손익/권리 조회 API

### 15. 기간별손익일별합산조회 [v1_국내주식-052]

```
GET /uapi/domestic-stock/v1/trading/inquire-period-profit
```

**언제 사용:** 특정 기간의 일별 실현손익을 합산하여 조회할 때.

**TR_ID:** 실전 `TTTC8715R` | 모의 `VTTC8715R`

**Query Parameters**
| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| CANO | 종합계좌번호 | ✅ | |
| ACNT_PRDT_CD | 계좌상품코드 | ✅ | |
| AFHR_FLPR_YN | 시간외단가여부 | ✅ | `N` |
| OFL_YN | 오프라인여부 | ✅ | 공란 |
| INQR_DVSN | 조회구분 | ✅ | `00`:전체 |
| UNPR_DVSN | 단가구분 | ✅ | `01` |
| FUND_STTL_ICLD_YN | 펀드결제포함여부 | ✅ | `N` |
| FNCG_AMT_AUTO_RDPT_YN | 융자금액자동상환여부 | ✅ | `N` |
| PRCS_DVSN | 처리구분 | ✅ | `00` |
| CTX_AREA_FK100 | 연속조회검색조건 | ✅ | 최초 공란 |
| CTX_AREA_NK100 | 연속조회키 | ✅ | 최초 공란 |

**응답 Body (output1 — 일별 손익)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| trad_dt | 거래일자 | YYYYMMDD |
| pdno | 상품번호 | |
| prdt_name | 상품명 | |
| sll_qty | 매도수량 | |
| pchs_avg_pric | 매입평균가 | |
| sll_pric | 매도단가 | |
| sll_amt | 매도금액 | |
| rlzt_pfls | 실현손익 | |
| rlzt_erng_rt | 실현수익률 | |
| tax | 세금 | |
| sll_fee | 매도수수료 | |

---

### 16. 기간별매매손익현황조회 [v1_국내주식-060]

```
GET /uapi/domestic-stock/v1/trading/inquire-period-trade-profit
```

**언제 사용:** 기간별 종목별 매매 손익 현황을 조회할 때.

**TR_ID:** 실전 `TTTC8708R` | 모의 `VTTC8708R`

**Query Parameters**
| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| CANO | 종합계좌번호 | ✅ | |
| ACNT_PRDT_CD | 계좌상품코드 | ✅ | |
| INQR_STRT_DT | 조회시작일자 | ✅ | YYYYMMDD |
| INQR_END_DT | 조회종료일자 | ✅ | YYYYMMDD |
| INQR_DVSN | 조회구분 | ✅ | `00`:정순, `01`:역순 |
| PDNO | 상품번호 | ❌ | 종목 필터링 |
| INQR_DVSN_2 | 조회구분2 | ✅ | `00`:전체 |
| CTX_AREA_FK100 | 연속조회검색조건 | ✅ | 최초 공란 |
| CTX_AREA_NK100 | 연속조회키 | ✅ | 최초 공란 |

**응답 Body (output1)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| pdno | 상품번호 | |
| prdt_name | 상품명 | |
| trad_dvsn_name | 거래구분명 | |
| buy_qty | 매수수량 | |
| buy_amt | 매수금액 | |
| sll_qty | 매도수량 | |
| sll_amt | 매도금액 | |
| rlzt_pfls | 실현손익 | |
| rlzt_erng_rt | 실현수익률 | |

---

### 17. 주식통합증거금 현황 [국내주식-191]

```
GET /uapi/domestic-stock/v1/trading/intgr-margin
```

**언제 사용:** 통합증거금 계좌의 증거금 현황과 여유 증거금을 확인할 때.

**TR_ID:** 실전 `TTTC1028R` | 모의 `VTTC1028R`

**Query Parameters**
| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| CANO | 종합계좌번호 | ✅ | |
| ACNT_PRDT_CD | 계좌상품코드 | ✅ | |
| CRDT_TYPE | 신용유형 | ✅ | `00` |
| INQR_DVSN | 조회구분 | ✅ | `1` |

**응답 Body (output)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| intgr_krwon | 통합증거금(원화) | |
| slbl_krwon | 여유증거금(원화) | |
| pred_bfee_krwon | 예상매매수수료 | |

---

### 18. 기간별계좌권리현황조회 [국내주식-211]

```
GET /uapi/domestic-stock/v1/trading/period-rights
```

**언제 사용:** 배당, 권리금 등 기간별 계좌 권리 현황을 조회할 때.

**TR_ID:** 실전 `TTTC1044R` | 모의 `VTTC1044R`

**Query Parameters**
| 파라미터 | 한글명 | 필수 | 설명 |
|---------|--------|------|------|
| CANO | 종합계좌번호 | ✅ | |
| ACNT_PRDT_CD | 계좌상품코드 | ✅ | |
| INQR_STRT_DT | 조회시작일자 | ✅ | YYYYMMDD |
| INQR_END_DT | 조회종료일자 | ✅ | YYYYMMDD |
| CTX_AREA_FK100 | 연속조회검색조건 | ✅ | 최초 공란 |
| CTX_AREA_NK100 | 연속조회키 | ✅ | 최초 공란 |

**응답 Body (output1)**
| 필드 | 한글명 | 설명 |
|------|--------|------|
| bass_dt | 기준일자 | |
| pdno | 상품번호 | |
| prdt_name | 상품명 | |
| rcts_type_name | 권리유형명 | 배당/무상/유상 등 |
| rcts_amt | 권리금액 | |

---


## 퇴직연금 API

> 퇴직연금(IRP/DC) 전용 API. 일반 계좌 API와 URL 구조가 다르다.

### 19. 퇴직연금 체결기준잔고 [v1_국내주식-032]

```
GET /uapi/domestic-stock/v1/trading/pension/inquire-present-balance
```

**TR_ID:** 실전 `TTTC8483R` | 모의 `VTTC8483R`

**Query Parameters:** CANO, ACNT_PRDT_CD, CTX_AREA_FK100, CTX_AREA_NK100

**응답 주요 필드 (output1)**
| 필드 | 설명 |
|------|------|
| pdno | 종목코드 |
| prdt_name | 종목명 |
| hldg_qty | 보유수량 |
| pchs_avg_pric | 매입평균가 |
| evlu_pfls_amt | 평가손익금액 |
| evlu_pfls_rt | 평가손익률 |

---

### 20. 퇴직연금 일별주문체결조회 [v1_국내주식-033]

```
GET /uapi/domestic-stock/v1/trading/pension/inquire-daily-ccld
```

**TR_ID:** 실전 `TTTC8500R` | 모의 `VTTC8500R`

**Query Parameters:** CANO, ACNT_PRDT_CD, INQR_STRT_DT, INQR_END_DT, SLL_BUY_DVSN_CD, CTX_AREA_FK100, CTX_AREA_NK100

**응답 주요 필드 (output1)**
| 필드 | 설명 |
|------|------|
| ord_dt | 주문일자 |
| pdno | 종목코드 |
| ord_qty | 주문수량 |
| tot_ccld_qty | 총체결수량 |
| avg_prvs | 평균단가 |

---

### 21. 퇴직연금 매수가능조회 [v1_국내주식-034]

```
GET /uapi/domestic-stock/v1/trading/pension/inquire-psbl-order
```

**TR_ID:** 실전 `TTTC0990R` | 모의 `VTTC0990R`

**Query Parameters:** CANO, ACNT_PRDT_CD, PDNO, ORD_UNPR, ORD_DVSN

**응답 주요 필드**
| 필드 | 설명 |
|------|------|
| nrcvb_buy_amt | 미수없는 매수가능금액 |
| nrcvb_buy_qty | 미수없는 매수가능수량 |
| max_buy_amt | 최대매수금액 |
| max_buy_qty | 최대매수수량 |

---

### 22. 퇴직연금 예수금조회 [v1_국내주식-035]

```
GET /uapi/domestic-stock/v1/trading/pension/inquire-deposit
```

**TR_ID:** 실전 `TTTC1413R` | 모의 `VTTC1413R`

**Query Parameters:** CANO, ACNT_PRDT_CD

**응답 주요 필드**
| 필드 | 설명 |
|------|------|
| psbl_dpst_amt | 가능예수금액 |
| tot_acnt_bal_amt | 총계좌잔액 |
| dnca_tot_amt | 예수금총금액 |

---

### 23. 퇴직연금 잔고조회 [v1_국내주식-036]

```
GET /uapi/domestic-stock/v1/trading/pension/inquire-balance
```

**TR_ID:** 실전 `TTTC8485R` | 모의 `VTTC8485R`

**Query Parameters:** CANO, ACNT_PRDT_CD, CTX_AREA_FK100, CTX_AREA_NK100

**응답 주요 필드 (output1)**
| 필드 | 설명 |
|------|------|
| pdno | 종목코드 |
| prdt_name | 종목명 |
| hldg_qty | 보유수량 |
| pchs_avg_pric | 매입평균가 |
| evlu_amt | 평가금액 |
| evlu_pfls_amt | 평가손익금액 |

```python
# GET /uapi/domestic-stock/v1/trading/pension/inquire-balance
import requests

url = "https://openapi.koreainvestment.com:9443/uapi/domestic-stock/v1/trading/pension/inquire-balance"
headers["tr_id"] = "TTTC8485R"
params = {
    "CANO": "12345678", "ACNT_PRDT_CD": "01",
    "CTX_AREA_FK100": "", "CTX_AREA_NK100": ""
}
res = requests.get(url, headers=headers, params=params)
```

---


## 공통 응답 구조

모든 API의 응답은 아래 공통 필드를 포함한다.

| 필드 | 타입 | 설명 |
|------|------|------|
| rt_cd | String | `0`: 성공, 그 외: 오류 |
| msg_cd | String | 응답코드 (ex: `APBK0013`) |
| msg1 | String | 응답메시지 |
| output / output1 / output2 | Object 또는 Array | API별 응답 데이터 |
| tr_cont | String | 연속조회 여부 (`M`: 다음 페이지 있음, `D`/`F`: 마지막) |

> **연속조회:** `tr_cont`가 `M`이면 응답의 `CTX_AREA_FK100`, `CTX_AREA_NK100` 값을 다음 요청 Query에 그대로 입력하여 반복 호출한다.
