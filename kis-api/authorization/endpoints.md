# KIS OAuth인증 Endpoints

실전 Base: `https://openapi.koreainvestment.com:9443`
모의 Base: `https://openapivts.koreainvestment.com:29443`

---


## 엔드포인트 선택 가이드

Claude는 아래 기준으로 적절한 엔드포인트를 선택한다.

| 상황 | 사용할 엔드포인트 |
|------|----------------|
| REST API 호출용 접근토큰 발급 | `POST /oauth2/tokenP` |
| 접근토큰 만료/폐기 | `POST /oauth2/revokeP` |
| POST 요청 Body 무결성 검증 (해시키 생성) | `POST /uapi/hashkey` |
| 웹소켓 실시간 데이터 수신용 접속키 발급 | `POST /oauth2/Approval` |

> **토큰 관리 주의사항:**
> - access_token 유효기간: 24시간 (1일 1회 발급 원칙)
> - 갱신발급주기: 6시간 (6시간 이내 재호출 시 기존 발급키로 응답)
> - 접속키(웹소켓) 유효기간: 24시간 (세션 연결 시 초기 1회만 사용)

---


## 접근토큰 발급

### 1. 접근토큰발급(P) — REST API 인증토큰 발급 [인증-001]

```
POST /oauth2/tokenP
```

**언제 사용:** REST API 호출에 필요한 OAuth 접근토큰(Bearer Token)을 발급받을 때. 본인 계좌에 필요한 인증 절차로, 인증을 통해 접근 토큰을 부여받아 오픈API 활용이 가능하다.

**Content-Type:** `application/json; charset=UTF-8`

**요청 Header:** 없음

**요청 Body**
| 파라미터 | 한글명 | 타입 | 필수 | 길이 | 설명 |
|---------|--------|------|------|------|------|
| grant_type | 권한부여 Type | String | ✅ | 18 | `client_credentials` 고정값 |
| appkey | 앱키 | String | ✅ | 36 | 한국투자증권 홈페이지에서 발급받은 appkey (절대 노출 금지) |
| appsecret | 앱시크릿키 | String | ✅ | 180 | 한국투자증권 홈페이지에서 발급받은 appsecret (절대 노출 금지) |

**응답 Body**
| 필드 | 한글명 | 타입 | 길이 | 설명 |
|------|--------|------|------|------|
| access_token | 접근토큰 | String | 350 | OAuth Access Token. API 호출 시 Header에 `Bearer {access_token}` 형식으로 입력 |
| token_type | 접근토큰유형 | String | 20 | `Bearer` 고정값 |
| expires_in | 접근토큰 유효기간 | Number | 10 | 유효기간(초). ex) 7776000 |
| access_token_token_expired | 접근토큰 유효기간(일시표시) | String | 50 | 유효기간(년:월:일 시:분:초). ex) `2022-08-30 08:10:10` |

**참고:**
- 일반개인고객/일반법인고객: Access token 유효기간 1일, 일정시간(6시간) 이내 재호출 시 직전 토큰값 리턴, OAuth 2.0의 Client Credentials Grant 절차 준용
- 제휴법인: Access token 유효기간 3개월, Refresh token 유효기간 1년, OAuth 2.0의 Authorization Code Grant 절차 준용

```python
# POST /oauth2/tokenP
import requests

url = "https://openapi.koreainvestment.com:9443/oauth2/tokenP"
headers = {"content-type": "application/json; charset=UTF-8"}
body = {
    "grant_type": "client_credentials",
    "appkey": "PSxxxxxxxxxxxxxxxxxx",
    "appsecret": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
}
res = requests.post(url, json=body, headers=headers)

# 응답 예시
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGci...",
    "token_type": "Bearer",
    "expires_in": 86400,
    "access_token_token_expired": "2026-03-17 12:00:00"
}
```

---

### 2. 접근토큰폐기(P) — 접근토큰 무효화 [인증-002]

```
POST /oauth2/revokeP
```

**언제 사용:** 부여받은 접근토큰을 더 이상 활용하지 않을 때. 토큰을 명시적으로 폐기하여 보안을 강화한다.

**Content-Type:** `application/json; charset=UTF-8`

**요청 Header:** 없음

**요청 Body**
| 파라미터 | 한글명 | 타입 | 필수 | 길이 | 설명 |
|---------|--------|------|------|------|------|
| appkey | 고객 앱Key | String | ✅ | 36 | 한국투자증권 홈페이지에서 발급받은 appkey (절대 노출 금지) |
| appsecret | 고객 앱Secret | String | ✅ | 180 | 한국투자증권 홈페이지에서 발급받은 appsecret (절대 노출 금지) |
| token | 접근토큰 | String | ✅ | 286 | 폐기할 OAuth Access Token (일반고객: 유효기간 1일, 법인: 유효기간 3개월) |

**응답 Body**
| 필드 | 한글명 | 타입 | 길이 | 설명 |
|------|--------|------|------|------|
| code | 응답코드 | String | 8 | HTTP 응답코드 |
| message | 응답메세지 | String | 450 | 응답메세지 |

```python
# POST /oauth2/revokeP
import requests

url = "https://openapi.koreainvestment.com:9443/oauth2/revokeP"
headers = {"content-type": "application/json; charset=UTF-8"}
body = {
    "appkey": "PSxxxxxxxxxxxxxxxxxx",
    "appsecret": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...",
    "token": "eyJ0eXAiOiJKV1QiLCJhbGci..."
}
res = requests.post(url, json=body, headers=headers)

# 응답 예시
{
    "code": "200",
    "message": "접근토큰이 폐기되었습니다."
}
```

---


## 보안 (Hashkey)

### 3. Hashkey — POST Body 무결성 검증용 해시키 생성

```
POST /uapi/hashkey
```

**언제 사용:** POST로 보내는 요청(주로 주문/정정/취소 API 해당)의 body 값을 사전에 암호화할 때. 보안을 위한 요소로 사용자가 보낸 요청 값을 중간에 탈취하여 변조하지 못하도록 한다. 해시키는 비필수값으로 사용하지 않아도 POST API 호출은 가능하다.

**Content-Type:** `application/json`

**요청 Header**
| 파라미터 | 한글명 | 타입 | 필수 | 길이 | 설명 |
|---------|--------|------|------|------|------|
| content-type | 컨텐츠타입 | String | ❌ | 40 | `application/json; charset=utf-8` |
| appkey | 앱키 | String | ✅ | 36 | 한국투자증권 홈페이지에서 발급받은 appkey (절대 노출 금지) |
| appsecret | 앱시크릿키 | String | ✅ | 180 | 한국투자증권 홈페이지에서 발급받은 appsecret (절대 노출 금지) |

**요청 Body**
| 파라미터 | 한글명 | 타입 | 필수 | 길이 | 설명 |
|---------|--------|------|------|------|------|
| JsonBody | 요청값 | Object | ✅ | - | POST로 보낼 body값 (해시 대상 데이터) |

**응답 Body**
| 필드 | 한글명 | 타입 | 길이 | 설명 |
|------|--------|------|------|------|
| JsonBody | 요청값 | Object | - | 요청한 JsonBody |
| HASH | 해쉬키 | String | 256 | [POST API 대상] Client가 요청하는 Request Body를 hashkey api로 생성한 Hash값. * API문서 > hashkey 참조 |

```python
# POST /uapi/hashkey
import requests

url = "https://openapi.koreainvestment.com:9443/uapi/hashkey"
headers = {
    "content-type": "application/json; charset=utf-8",
    "appkey": "PSxxxxxxxxxxxxxxxxxx",
    "appsecret": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
}
body = {
    "CANO": "00000000",
    "ACNT_PRDT_CD": "01",
    "OVRS_EXCG_CD": "SHAA"
}
res = requests.post(url, json=body, headers=headers)

# 응답 예시
{
    "HASH": "a1b2c3d4e5f6..."
}
```

---


## 웹소켓 접속키 발급

### 4. 실시간 (웹소켓) 접속키 발급 [실시간-000]

```
POST /oauth2/Approval
```

**언제 사용:** 실시간(웹소켓) 접속키를 발급받을 때. 웹소켓 이용 시 해당 키를 appkey와 appsecret 대신 헤더에 넣어 API를 호출한다. 접속키의 유효기간은 24시간이지만, 세션 연결 시 초기 1회만 사용하기 때문에 접속키 인증 후에는 세션종료되지 않는 이상 접속키 신규 발급받지 않으셔도 365일 내내 웹소켓 데이터 수신이 가능하다.

**Content-Type:** `application/json; utf-8`

**요청 Header**
| 파라미터 | 한글명 | 타입 | 필수 | 길이 | 설명 |
|---------|--------|------|------|------|------|
| content-type | 컨텐츠타입 | String | ❌ | 20 | `application/json; utf-8` |

**요청 Body**
| 파라미터 | 한글명 | 타입 | 필수 | 길이 | 설명 |
|---------|--------|------|------|------|------|
| grant_type | 권한부여타입 | String | ✅ | 18 | `client_credentials` 고정값 |
| appkey | 앱키 | String | ✅ | 36 | 한국투자증권 홈페이지에서 발급받은 appkey (절대 노출 금지) |
| secretkey | 시크릿키 | String | ✅ | 180 | 한국투자증권 홈페이지에서 발급받은 appsecret (절대 노출 금지). * 주의: appsecret와 secretkey는 동일하오니 착오없으시기 바랍니다. |

**응답 Body**
| 필드 | 한글명 | 타입 | 길이 | 설명 |
|------|--------|------|------|------|
| approval_key | 웹소켓 접속키 | String | 286 | 웹소켓 이용 시 발급받은 접속키를 appkey와 appsecret 대신 헤더에 넣어 API 호출 |

```python
# POST /oauth2/Approval
import requests

url = "https://openapi.koreainvestment.com:9443/oauth2/Approval"
headers = {"content-type": "application/json; utf-8"}
body = {
    "grant_type": "client_credentials",
    "appkey": "PSxxxxxxxxxxxxxxxxxx",
    "secretkey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
}
res = requests.post(url, json=body, headers=headers)

# 응답 예시
{
    "approval_key": "a1b2c3d4-e5f6-7890-abcd-ef1234567890..."
}
```
