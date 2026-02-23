---
name: python-project-setup
description: Poetry 기반으로 Python FastAPI 백엔드 API 프로젝트 기본 구조를 설정하는 스킬. 사용자가 Python 프로젝트 초기화, Poetry 프로젝트 생성, FastAPI 환경 설정을 요청할 때 반드시 이 스킬을 사용하세요. "파이썬 프로젝트 만들어줘", "poetry로 프로젝트 세팅", "백엔드 API 프로젝트 초기화", "Python 환경 설정", "FastAPI 프로젝트 세팅" 등의 요청에도 이 스킬을 적용하세요.
---

# Python FastAPI Project Setup with Poetry

Poetry 기반 FastAPI 백엔드 API 프로젝트 기본 구조를 단계별로 설정하는 스킬입니다.

## 전제 조건 확인

시작 전에 다음 항목을 확인하세요:

```bash
# Poetry 설치 여부 확인
poetry --version

# Python 설치 여부 확인
python --version
```

Poetry가 없다면 사용자에게 먼저 설치를 안내하세요:
```bash
curl -sSL https://install.python-poetry.org | python3 -
```

---

## Step 1: 프로젝트 이름 확인

사용자에게 프로젝트 이름을 물어보세요. 프로젝트 생성은 **항상 현재 디렉토리**에서 진행합니다.

---

## Step 2: DB 사용 여부 확인

사용자에게 아래 질문을 순서대로 합니다.

**질문 1:** "DB 접근이 필요한가요? (yes / no)"

- **no** → 질문 3으로 이동
- **yes** → 질문 2로 이동

**질문 2:** "어떤 DB를 사용하시나요? 아래 중 선택해주세요:
1. PostgreSQL
2. MySQL
3. MongoDB
4. SQLite"

**질문 3:** "캐싱이 필요한가요? (yes / no)"

- **no** → Step 3으로 이동
- **yes** → 질문 4로 이동

**질문 4:** "어떤 캐싱 서비스를 사용하시나요? 아래 중 선택해주세요:
1. Redis
2. Memcached
3. 로컬 캐싱 (cachetools)"

사용자의 모든 응답을 기억해두고 Step 4에서 해당 패키지를 포함합니다.

---

## Step 3: Poetry 프로젝트 생성 (현재 디렉토리 기준)

```bash
# 현재 디렉토리에서 바로 초기화 (새 폴더를 만들지 않음)
poetry init --name <project-name> --no-interaction

# FastAPI 프로젝트 디렉토리 구조 생성
mkdir -p src/api/v1/routers src/core src/schemas src/services tests
touch src/__init__.py
touch src/main.py
touch src/core/__init__.py
touch src/core/config.py
touch src/api/__init__.py
touch src/api/v1/__init__.py
touch src/api/v1/routers/__init__.py
touch src/schemas/__init__.py
touch src/services/__init__.py
touch tests/__init__.py
```

현재 디렉토리가 다음 구조로 설정됩니다:
```
./
├── pyproject.toml
├── .env.example
├── src/
│   ├── main.py              # FastAPI 앱 진입점
│   ├── core/
│   │   └── config.py        # pydantic-settings 기반 환경변수 설정
│   ├── api/
│   │   └── v1/
│   │       └── routers/     # 엔드포인트 라우터
│   ├── schemas/             # Pydantic 요청/응답 모델
│   └── services/            # 비즈니스 로직
└── tests/
```

`src/main.py` 기본 내용을 작성합니다:
```python
from fastapi import FastAPI

app = FastAPI(
    title="<project-name>",
    version="0.1.0",
)

@app.get("/health")
async def health_check():
    return {"status": "ok"}
```

`src/core/config.py` 기본 내용을 작성합니다:
```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    app_env: str = "development"
    app_host: str = "0.0.0.0"
    app_port: int = 8000

    class Config:
        env_file = ".env"

settings = Settings()
```

---

## Step 4: pyproject.toml에 의존성 명시

가상환경 생성 및 패키지 설치는 직접 실행하지 않습니다.
`pyproject.toml`을 아래 내용으로 작성합니다.

### 기본 구성 (DB 없음)

```toml
[tool.poetry]
name = "<project-name>"
version = "0.1.0"
description = ""
authors = ["Your Name <you@example.com>"]
readme = "README.md"
package-mode = false

[tool.poetry.dependencies]
python = "^3.11"
fastapi = "*"            # FastAPI 웹 프레임워크
uvicorn = "*"            # ASGI 서버 (FastAPI 실행)
httpx = "*"              # HTTP 클라이언트 (외부 REST API 호출)
pydantic = "*"           # 데이터 유효성 검사 및 직렬화
pydantic-settings = "*"  # 환경변수 기반 설정 관리
python-dotenv = "*"      # .env 파일 로드

[tool.poetry.group.dev.dependencies]
pytest = "*"
pytest-asyncio = "*"
httpx = "*"              # FastAPI TestClient용
black = "*"
ruff = "*"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

### DB 선택 시 추가할 패키지

| DB | 추가 패키지 |
|----|------------|
| PostgreSQL | `sqlalchemy = "*"`, `psycopg2-binary = "*"` |
| MySQL | `sqlalchemy = "*"`, `pymysql = "*"` |
| MongoDB | `motor = "*"`, `beanie = "*"` |
| SQLite | `sqlalchemy = "*"` |

### 캐싱 선택 시 추가할 패키지

| 캐싱 서비스 | 추가 패키지 |
|------------|------------|
| Redis | `redis = "*"` |
| Memcached | `pymemcache = "*"` |
| 로컬 캐싱 | `cachetools = "*"` |

예시 (PostgreSQL + Redis 선택 시):
```toml
[tool.poetry.dependencies]
python = "^3.11"
fastapi = "*"
uvicorn = "*"
httpx = "*"
pydantic = "*"
pydantic-settings = "*"
python-dotenv = "*"
sqlalchemy = "*"
psycopg2-binary = "*"
redis = "*"
```

---

## Step 5: .env.example 파일 생성

선택한 패키지 조합에 따라 `.env.example` 파일을 현재 디렉토리에 생성합니다.
실제 값은 비워두고 키 이름만 제공합니다.

### 기본 구성 (항상 포함)

```env
# ─────────────────────────────
# Application
# ─────────────────────────────
APP_ENV=development
APP_HOST=0.0.0.0
APP_PORT=8000

# ─────────────────────────────
# HTTP Client (httpx)
# ─────────────────────────────
API_BASE_URL=
API_TIMEOUT=30
```

### DB 선택 시 추가할 항목

| DB | 추가 env 키 |
|----|------------|
| PostgreSQL | `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD` |
| MySQL | `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD` |
| MongoDB | `MONGODB_URI`, `MONGODB_DB_NAME` |
| SQLite | `SQLITE_DB_PATH` |

예시 (PostgreSQL):
```env
# ─────────────────────────────
# Database (PostgreSQL)
# ─────────────────────────────
DB_HOST=localhost
DB_PORT=5432
DB_NAME=
DB_USER=
DB_PASSWORD=
```

### 캐싱 선택 시 추가할 항목

| 캐싱 서비스 | 추가 env 키 |
|------------|------------|
| Redis | `REDIS_HOST`, `REDIS_PORT`, `REDIS_PASSWORD`, `REDIS_DB` |
| Memcached | `MEMCACHED_HOST`, `MEMCACHED_PORT` |
| 로컬 캐싱 | (env 키 불필요) |

> `.env.example` 생성 후 사용자에게 안내:
> "`.env.example`을 복사하여 `.env` 파일을 만들고 실제 값을 채워주세요.
> `.env`는 절대 git에 커밋하지 마세요. `.gitignore`에 `.env`를 추가하는 것을 권장합니다."

---

## Step 6: 사용자에게 다음 안내 메시지 전달

프로젝트 초기화가 완료되면 아래 내용을 사용자에게 안내하세요:

---

> 프로젝트 초기화가 완료되었습니다.
> 가상환경 활성화 및 패키지 설치는 아래 순서로 직접 진행해주세요:
>
> ```bash
> # 1. 가상환경 활성화
> poetry env activate
>
> # 2. 의존성 설치
> poetry install
>
> # 3. FastAPI 서버 실행
> poetry run uvicorn src.main:app --reload --host 0.0.0.0 --port 8000
> ```
>
> 서버 실행 후 아래 주소에서 자동 생성된 API 문서를 확인할 수 있습니다:
> - Swagger UI: http://localhost:8000/docs
> - ReDoc: http://localhost:8000/redoc

---

## 자주 쓰는 Poetry 명령어 요약

| 명령어 | 설명 |
|--------|------|
| `poetry install` | 의존성 설치 |
| `poetry add <package>` | 패키지 추가 |
| `poetry remove <package>` | 패키지 제거 |
| `poetry env activate` | 가상환경 활성화 |
| `poetry run <cmd>` | 가상환경에서 명령 실행 |
| `poetry show` | 설치된 패키지 목록 |
| `poetry env info` | 가상환경 정보 |

---

## 주의사항

- `poetry init`은 현재 디렉토리에 `pyproject.toml`만 생성하며 새 폴더를 만들지 않습니다.
- `.gitignore`에 `.venv/`와 `.env`를 추가하는 것을 권장하세요.
- 프로젝트를 공유할 때는 `poetry.lock` 파일도 함께 커밋하세요.
- `package-mode = false`는 패키징 없이 의존성 관리만 할 때 필요한 설정입니다.
- FastAPI 서버 실행 시 `--reload` 옵션은 개발 환경에서만 사용하세요.