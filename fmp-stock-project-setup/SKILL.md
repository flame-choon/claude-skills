```skill
---
name: fmp-stock-project-setup
description: FMP(Financial Modeling Prep) API 기반 주식 데이터 분석 Python 프로젝트를 Poetry로 설정하는 스킬. "FMP 프로젝트 만들어줘", "주식 데이터 분석 프로젝트 세팅", "FMP API 프로젝트 초기화", "주식 앱 만들어줘", "파이썬으로 주식 데이터 가져오는 프로젝트" 등의 요청에 이 스킬을 적용하세요.
---

# FMP Stock Data Project Setup with Poetry

FMP API로 주식 데이터를 수집·가공·시각화하는 Python 프로젝트를 Poetry 기반으로 설정하는 스킬입니다.

## 전제 조건 확인

```bash
poetry --version
python --version
```

Poetry가 없다면 먼저 설치를 안내하세요:
```bash
curl -sSL https://install.python-poetry.org | python3 -
```

---

## Step 1: 프로젝트 이름 확인 후 Poetry 초기화

사용자에게 프로젝트 이름을 물어보세요. 프로젝트 생성은 **항상 현재 디렉토리** 기준입니다.

```bash
# 현재 디렉토리에서 초기화 (새 폴더 생성 없음)
poetry init --name <project-name> --no-interaction

# 디렉토리 구조 생성
mkdir -p src/fmp src/analysis src/display tests
touch src/__init__.py
touch src/fmp/__init__.py
touch src/analysis/__init__.py
touch src/display/__init__.py
touch tests/__init__.py
touch .env.example
```

생성되는 프로젝트 구조:
```
./
├── pyproject.toml
├── README.md                # 프로젝트 설명 및 시작 가이드
├── .env                     # FMP API Key 등 환경변수 (gitignore 대상)
├── .env.example             # 환경변수 템플릿
├── src/
│   ├── __init__.py
│   ├── fmp/                 # FMP API 호출 모듈
│   │   └── __init__.py
│   ├── analysis/            # 데이터 가공·분석 모듈
│   │   └── __init__.py
│   └── display/             # 출력·시각화 모듈
│       └── __init__.py
└── tests/
    └── __init__.py
```

`.env.example` 내용을 생성해 주세요:
```
FMP_API_KEY=your_api_key_here
```

---

## Step 2: pyproject.toml에 의존성 직접 작성

`poetry init`으로 생성된 `pyproject.toml`을 아래 내용으로 **덮어쓰세요**.

> ⚠️ **`poetry install` / `poetry add`는 이 단계에서 실행하지 않습니다.**
> 패키지 설치는 사용자가 직접 자신의 터미널에서 가상환경을 활성화한 뒤 수행합니다.
> (Claude Code와 사용자의 가상환경이 서로 달라 충돌이 발생할 수 있기 때문입니다.)

```toml
[tool.poetry]
package-mode = false
name = "<project-name>"
version = "0.1.0"
description = "FMP API 기반 주식 데이터 분석 프로젝트"
authors = ["Your Name <you@example.com>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.11"
requests = "^2.31.0"       # FMP REST API 호출
pandas = "^2.2.0"          # 데이터프레임 기반 가공
numpy = "^1.26.0"          # 수치 연산
matplotlib = "^3.8.0"      # 정적 차트
plotly = "^5.18.0"         # 인터랙티브 차트
python-dotenv = "^1.0.0"   # .env 파일 로드

[tool.poetry.group.dev.dependencies]
pytest = "^8.0.0"
black = "^24.0.0"
ruff = "^0.3.0"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

> **선택적 패키지** — 필요 시 `[tool.poetry.dependencies]` 블록에 직접 추가하세요:
> ```toml
> rich = "^13.0.0"        # 터미널 테이블·컬러 출력
> streamlit = "^1.30.0"   # 웹 대시보드 앱
> typer = "^0.12.0"       # CLI 인터페이스 구성
> pydantic = "^2.0.0"     # API 응답 데이터 모델링
> ```

---

## Step 3: FMP API 기본 코드 템플릿 생성

`src/fmp/client.py`를 다음 내용으로 생성해 주세요:

```python
# src/fmp/client.py
import os
import requests
from dotenv import load_dotenv

load_dotenv()

BASE_URL = "https://financialmodelingprep.com/stable"
API_KEY = os.getenv("FMP_API_KEY")


def get(endpoint: str, params: dict = {}) -> dict | list:
    """FMP API GET 요청 공통 함수"""
    url = f"{BASE_URL}/{endpoint}"
    params["apikey"] = API_KEY
    response = requests.get(url, params=params)
    response.raise_for_status()
    return response.json()


def get_quote(symbol: str) -> dict:
    """실시간 주가 조회"""
    data = get(f"quote/{symbol}")
    return data[0] if data else {}


def get_income_statement(symbol: str, period: str = "annual", limit: int = 5) -> list:
    """손익계산서 조회"""
    return get(f"income-statement/{symbol}", {"period": period, "limit": limit})


def get_historical_price(symbol: str, from_date: str, to_date: str) -> list:
    """기간별 주가 이력 조회"""
    return get(
        f"historical-price-full/{symbol}",
        {"from": from_date, "to": to_date}
    ).get("historical", [])
```

---

## Step 4: README.md 생성

프로젝트 루트에 `README.md`를 아래 내용으로 생성해 주세요.
`<project-name>`과 `<간단한 프로젝트 설명>`은 실제 프로젝트에 맞게 채워 넣으세요.

```markdown
# <project-name>

<간단한 프로젝트 설명>

FMP(Financial Modeling Prep) API를 활용하여 주식 데이터를 수집·가공·시각화하는 Python 프로젝트입니다.

## 프로젝트 구조

```
./
├── pyproject.toml           # 프로젝트 메타데이터 및 의존성 정의
├── README.md
├── .env                     # 환경변수 (gitignore 대상)
├── .env.example             # 환경변수 템플릿
├── src/
│   ├── fmp/                 # FMP API 호출 모듈
│   ├── analysis/            # 데이터 가공·분석 모듈
│   └── display/             # 출력·시각화 모듈
└── tests/
```

## 시작하기

### 1. 환경변수 설정

```bash
cp .env.example .env
```

`.env` 파일을 열어 FMP API Key를 입력하세요:

```
FMP_API_KEY=your_api_key_here
```

> FMP API Key는 https://financialmodelingprep.com 에서 발급받을 수 있습니다.

### 2. 가상환경 활성화

Poetry 2.x 기준:

```bash
poetry env activate
# 출력된 source ... 명령어를 복사해서 직접 실행
```

### 3. 패키지 설치

```bash
poetry install
```

### 4. 실행

```bash
poetry run python src/main.py
```

## 주요 의존성

| 패키지 | 용도 |
|--------|------|
| requests | FMP REST API 호출 |
| pandas | 데이터프레임 기반 가공 |
| numpy | 수치 연산 |
| matplotlib | 정적 차트 시각화 |
| plotly | 인터랙티브 차트 시각화 |
| python-dotenv | 환경변수 관리 |

## 참고

- [FMP API 문서](https://financialmodelingprep.com/developer/docs)
- [Poetry 문서](https://python-poetry.org/docs)
```

---

## Step 5: 프로젝트 초기화 완료 안내

프로젝트 파일 생성이 모두 완료되면 사용자에게 아래 안내문을 출력하세요:

---

**✅ 프로젝트 초기화가 완료되었습니다.**

이제 **사용자 본인의 터미널**에서 아래 순서대로 실행하세요.

**1. `.env` 파일 생성 후 FMP API Key 입력**
```bash
cp .env.example .env
# .env 파일을 열어 FMP_API_KEY 값을 실제 키로 교체
```

**2. 가상환경 활성화 (Poetry 2.x)**
```bash
# 활성화 명령어 출력
poetry env activate

# 출력된 source 명령어를 복사해서 직접 실행 (예시)
source /Users/username/Library/Caches/pypoetry/virtualenvs/<project>-py3.11/bin/activate
```

> `poetry env activate`는 활성화 명령어를 **출력만** 합니다.
> 출력된 `source ...` 명령어를 복사해서 직접 실행해야 가상환경이 활성화됩니다.

**3. 패키지 설치**
```bash
poetry install
```

**4. 스크립트 실행**
```bash
poetry run python src/main.py
```

---

## 자주 쓰는 Poetry 명령어 요약

| 명령어 | 설명 |
|--------|------|
| `poetry install` | 의존성 설치 |
| `poetry add <package>` | 패키지 추가 |
| `poetry remove <package>` | 패키지 제거 |
| `poetry env activate` | 가상환경 활성화 경로 출력 (Poetry 2.x) |
| `poetry run <cmd>` | 가상환경에서 명령 실행 |
| `poetry show` | 설치된 패키지 목록 |
| `poetry env info` | 가상환경 정보 |

---

## 주의사항

- `.env` 파일은 반드시 `.gitignore`에 추가하세요. FMP API Key가 외부에 노출되지 않도록 합니다.
- `poetry install` / `poetry add`는 반드시 **사용자 본인의 터미널**에서 실행하세요. Claude Code가 실행한 가상환경과 사용자 환경이 달라 패키지가 누락될 수 있습니다.
- FMP 무료 플랜은 API 호출 횟수에 제한이 있으므로, 개발 중에는 응답 데이터를 로컬에 캐싱하는 것을 권장합니다.
- `poetry.lock` 파일은 재현 가능한 환경을 위해 반드시 커밋하세요.
- Streamlit 앱을 구성하는 경우 `poetry run streamlit run src/app.py`로 실행합니다.
```