---
name: kis-api
description: 한국투자증권(KIS) Open API 레퍼런스. KIS API 엔드포인트 코드 작성 시 사용. 인증 토큰 발급, 공통 헤더 설정, 환경변수 구성 등 기본 패턴을 제공.
---

# KIS (한국투자증권) Open API 레퍼런스

> 공식 문서: https://apiportal.koreainvestment.com/apiservice-summary

## Base URL

```
# 실전투자
https://openapi.koreainvestment.com:9443

# 모의투자
https://openapivts.koreainvestment.com:29443
```

> ⚠️ **주의**: 실전/모의 환경에 따라 Base URL과 tr_id 접두사가 다름. 실전은 `T`로 시작, 모의는 `V`로 시작하는 tr_id 사용.

---

## Authentication

### 엔드포인트 선택 가이드

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

세부 파라미터 및 예시는 `authorization/endpoints.md` 참조.

### 1단계: Access Token 발급 — `POST /oauth2/tokenP`

**요청 Header:** 없음 / **Content-Type:** `application/json; charset=UTF-8`

**요청 Body**:

| 파라미터 | 타입 | 필수 | 설명 |
|---------|------|------|------|
| `grant_type` | String | ✅ | `client_credentials` 고정값 |
| `appkey` | String | ✅ | 발급받은 appkey (절대 노출 금지) |
| `appsecret` | String | ✅ | 발급받은 appsecret (절대 노출 금지) |

**응답 Body**:

| 필드 | 타입 | 설명 |
|------|------|------|
| `access_token` | String | API 호출 시 `Bearer {access_token}` 형식으로 사용 |
| `token_type` | String | `Bearer` 고정값 |
| `expires_in` | Number | 유효기간(초). ex) `86400` |
| `access_token_token_expired` | String | 유효기간(일시). ex) `2026-03-17 12:00:00` |

> - 일반개인/법인: 유효기간 1일, 6시간 이내 재호출 시 기존 토큰 반환
> - 제휴법인: Access token 3개월, Refresh token 1년 (Authorization Code Grant 방식)

### 2단계: API 호출 시 공통 헤더

```python
headers = {
    "content-type": "application/json; charset=utf-8",
    "authorization": f"Bearer {access_token}",
    "appkey": APP_KEY,
    "appsecret": APP_SECRET,
    "tr_id": "TR_ID_HERE",       # 각 API별로 다름 (아래 참조)
    "custtype": "P",              # P: 개인, B: 법인
}
```

### 접근토큰 폐기 — `POST /oauth2/revokeP`

더 이상 사용하지 않는 토큰을 명시적으로 무효화할 때 사용.

**요청 Body**: `appkey`, `appsecret`, `token`(폐기할 access token) 필수

**응답**: `{"code": "200", "message": "접근토큰이 폐기되었습니다."}`

### Hashkey (POST Body 무결성 검증) — `POST /uapi/hashkey`

주문/정정/취소 등 POST API 호출 시 body 변조 방지용. **비필수값** — 사용하지 않아도 POST API 호출 가능.

**요청 Header**: `appkey`, `appsecret` 필수 / **요청 Body**: POST로 보낼 JSON body 전체

**응답**: `{"HASH": "a1b2c3d4..."}` → 이 값을 POST 요청 헤더 `hashkey`에 추가

### 웹소켓 접속키 발급 — `POST /oauth2/Approval`

실시간 웹소켓 이용 시 접속키 발급. 세션 연결 시 초기 1회만 사용하므로, 세션이 끊기지 않으면 재발급 불필요.

**요청 Body**: `grant_type`(`client_credentials`), `appkey`, `secretkey`(= appsecret) 필수

> ⚠️ 웹소켓 발급 시 파라미터명이 `appsecret`이 아닌 `secretkey`임에 주의.

**응답**: `{"approval_key": "a1b2c3d4-e5f6-..."}` → 웹소켓 헤더에서 appkey/appsecret 대신 사용

환경변수 권장:
```
KIS_APP_KEY
KIS_APP_SECRET
KIS_ACCOUNT_NO      # 계좌번호 (8자리)
KIS_ACCOUNT_SUFFIX  # 계좌 상품코드 (2자리, 보통 "01")
```

---

## 카테고리별 Endpoint 문서

세부 endpoint 및 파라미터는 각 카테고리 파일 참조:

| 카테고리 | 파일 | 주요 용도 |
|---------|------|---------|
| 인증 | `authorization/endpoints.md` | 접근토큰 발급/폐기, Hashkey, 웹소켓 접속키 |

---

## 의존성 설정

KIS API 관련 코드 작성 시 아래 패키지들이 필요하다. **패키지 설치는 하지 않으며**, 프로젝트 방식에 따라 의존성 정의만 추가한다.

### Poetry 기반 프로젝트 (`pyproject.toml` 존재)

```toml
[tool.poetry.dependencies]
python = "^3.9"
requests = "^2.31.0"
python-dotenv = "^1.0.0"
```

### 일반 프로젝트 (`requirements.txt` 존재)

```
requests>=2.31.0
python-dotenv>=1.0.0
```

> 💡 **판단 기준**: `pyproject.toml` 파일이 프로젝트 루트에 있으면 Poetry 기반. 없고 `requirements.txt`가 있으면 해당 파일에 추가. 둘 다 없으면 `requirements.txt`를 새로 생성하여 추가.

---

## 환경변수 설정 (.env)

### .env.example 생성/수정

프로젝트 루트에 `.env.example` 파일에 아래 내용을 포함한다:

```dotenv
# 한국투자증권 Open API
# https://apiportal.koreainvestment.com 에서 앱 등록 후 발급
KIS_APP_KEY=your_app_key_here
KIS_APP_SECRET=your_app_secret_here
KIS_ACCOUNT_NO=12345678        # 계좌번호 8자리
KIS_ACCOUNT_SUFFIX=01          # 계좌 상품코드 (보통 01)
KIS_IS_REAL=true               # true: 실전, false: 모의
```

### 안내 사항

1. **`.env.example`은 Git에 커밋**한다 — 팀원들이 어떤 환경변수가 필요한지 알 수 있도록
2. **`.env`는 절대 Git에 커밋하지 않는다** — `.gitignore`에 `.env` 가 포함되어 있는지 확인
3. 실제 사용 시 `.env.example`을 복사해 `.env`로 만들고 실제 값 입력:
   ```bash
   cp .env.example .env
   ```

---

## Python 기본 클라이언트 패턴

```python
import os
import requests
from dotenv import load_dotenv

load_dotenv()

APP_KEY = os.environ["KIS_APP_KEY"]
APP_SECRET = os.environ["KIS_APP_SECRET"]
IS_REAL = os.environ.get("KIS_IS_REAL", "true").lower() == "true"

BASE_URL = (
    "https://openapi.koreainvestment.com:9443"
    if IS_REAL
    else "https://openapivts.koreainvestment.com:29443"
)


def get_access_token() -> str:
    url = f"{BASE_URL}/oauth2/tokenP"
    body = {
        "grant_type": "client_credentials",
        "appkey": APP_KEY,
        "appsecret": APP_SECRET,
    }
    res = requests.post(url, json=body)
    res.raise_for_status()
    return res.json()["access_token"]
```

---

## 자주 발생하는 실수

| ❌ 잘못된 사용 | ✅ 올바른 사용 |
|------------|------------|
| 토큰 매 요청마다 재발급 | 토큰 캐싱 후 만료 시에만 재발급 (6시간 이내 재호출은 기존 토큰 반환) |
| 웹소켓 접속키 발급 시 `appsecret` 파라미터 사용 | `secretkey` 파라미터 사용 (`/oauth2/Approval` 한정) |
| 웹소켓 세션 유지 중 매번 접속키 재발급 | 세션 연결 시 초기 1회만 발급 후 세션 종료 전까지 재사용 |
