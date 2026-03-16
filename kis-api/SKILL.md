---
name: kis-api
description: 한국투자증권(KIS) Open API 레퍼런스. 국내 주식 종목 정보 조회, 현재가, 차트, 호가 등 KIS API 엔드포인트 코드 작성 시 사용. 잘못된 tr_id나 헤더 누락을 방지.
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

### 1단계: Access Token 발급

```
POST /oauth2/tokenP
```

**요청 Body (JSON)**:
```json
{
  "grant_type": "client_credentials",
  "appkey": "YOUR_APP_KEY",
  "appsecret": "YOUR_APP_SECRET"
}
```

**응답**:
```json
{
  "access_token": "eyJ...",
  "token_type": "Bearer",
  "expires_in": 86400,
  "access_token_token_expired": "2024-01-02 00:00:00"
}
```

> 토큰 유효기간: 24시간. 만료 전 재발급 필요.

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
| 주식 현재가 | `quote/endpoints.md` | 현재가, 체결가, 호가 |
| 주식 차트 | `chart/endpoints.md` | 일봉/주봉/월봉, 분봉 |
| 종목 정보 | `stock-info/endpoints.md` | 종목 기본정보, 상장 종목 목록 |

---

## 의존성 설정

KIS API 관련 코드 작성 시 아래 패키지들이 필요하다. **패키지 설치는 하지 않으며**, 프로젝트 방식에 따라 의존성 정의만 추가한다.

### Poetry 기반 프로젝트 (`pyproject.toml` 존재)

```toml
[tool.poetry.dependencies]
python = "^3.9"
requests = "^2.31.0"
pandas = "^2.0.0"
python-dotenv = "^1.0.0"
```

### 일반 프로젝트 (`requirements.txt` 존재)

```
requests>=2.31.0
pandas>=2.0.0
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


def kis_get(path: str, tr_id: str, params: dict, token: str) -> dict:
    headers = {
        "content-type": "application/json; charset=utf-8",
        "authorization": f"Bearer {token}",
        "appkey": APP_KEY,
        "appsecret": APP_SECRET,
        "tr_id": tr_id,
        "custtype": "P",
    }
    res = requests.get(f"{BASE_URL}{path}", headers=headers, params=params)
    res.raise_for_status()
    data = res.json()
    if data.get("rt_cd") != "0":
        raise ValueError(f"KIS API 오류: {data.get('msg_cd')} - {data.get('msg1')}")
    return data
```

---

## 공통 응답 구조

```json
{
  "rt_cd": "0",        // "0": 성공, 그 외: 오류
  "msg_cd": "MAPIA000",
  "msg1": "정상처리 되었습니다.",
  "output": { ... }    // 또는 "output1", "output2" (복수 결과)
}
```

> ⚠️ HTTP 200이어도 `rt_cd`가 `"0"`이 아니면 오류. 반드시 확인 필요.

---

## 자주 발생하는 실수

| ❌ 잘못된 사용 | ✅ 올바른 사용 |
|------------|------------|
| 모의환경에서 실전 tr_id 사용 (`FHKST01010100`) | 모의환경 tr_id (`VHKST01010100`) |
| `rt_cd` 확인 없이 바로 `output` 접근 | `rt_cd == "0"` 확인 후 접근 |
| 토큰 매 요청마다 재발급 | 토큰 캐싱 후 만료 시에만 재발급 |
| `tr_cont` 미처리 (연속 조회) | `tr_cont`가 `"F"`일 때까지 반복 조회 |
| 계좌번호를 통째로 1개 필드에 | `CANO`(8자리) + `ACNT_PRDT_CD`(2자리)로 분리 |
