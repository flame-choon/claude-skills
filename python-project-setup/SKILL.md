---
name: python-project-setup
description: Poetry 기반으로 Python 프로젝트 기본 구조를 설정하는 스킬. 사용자가 Python 프로젝트 초기화, Poetry 프로젝트 생성, 가상환경 설정, boto3 패키지 설치를 요청할 때 반드시 이 스킬을 사용하세요. "파이썬 프로젝트 만들어줘", "poetry로 프로젝트 세팅", "AWS 프로젝트 초기화", "Python 환경 설정" 등의 요청에도 이 스킬을 적용하세요.
---

# Python Project Setup with Poetry

Poetry 기반 Python 프로젝트 기본 구조를 단계별로 설정하는 스킬입니다.

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

## Step 1: Poetry 프로젝트 생성 (현재 디렉토리 기준)

사용자에게 프로젝트 이름을 물어보세요. 단, 프로젝트 생성은 **항상 현재 디렉토리**에서 진행합니다.

```bash
# 현재 디렉토리에서 바로 초기화 (새 폴더를 만들지 않음)
poetry init --name <project-name> --no-interaction

# 소스 및 테스트 디렉토리 생성 (프로젝트명 폴더는 생성하지 않음)
mkdir -p src tests
touch src/__init__.py tests/__init__.py
```

현재 디렉토리가 다음 구조로 설정됩니다:
```
./                           # 현재 위치한 폴더 (새 폴더 생성 없음)
├── pyproject.toml           # 프로젝트 메타데이터 및 의존성 관리
├── src/
│   └── __init__.py
└── tests/
    └── __init__.py
```

`pyproject.toml` 기본 내용을 사용자에게 보여주세요:
```toml
[tool.poetry]
name = "<project-name>"
version = "0.1.0"
description = ""
authors = ["Your Name <you@example.com>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.11"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

---

## Step 2: 가상환경 생성 및 boto3 설치

```bash
# 가상환경 생성 + boto3 설치 (한 번에)
poetry install

# boto3 패키지 추가
poetry add boto3
```

설치 확인:
```bash
# 가상환경 정보 확인
poetry env info

# 설치된 패키지 목록 확인
poetry show
```

---

## Step 3: 가상환경 활성화

```bash
# 가상환경 쉘 진입
poetry shell

# 또는 특정 명령어를 가상환경 내에서 실행
poetry run python <script.py>
```

---

## 완성된 pyproject.toml 예시

설치 후 `pyproject.toml`은 다음과 같이 업데이트됩니다:

```toml
[tool.poetry]
name = "<project-name>"
version = "0.1.0"
description = ""
authors = ["Your Name <you@example.com>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.11"
boto3 = "^1.34.0"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

---

## 추가 패키지 설치 (선택사항)

AWS 작업에 유용한 추가 패키지들을 필요에 따라 제안하세요:

```bash
# 개발 의존성 (테스트, 린팅)
poetry add --group dev pytest black ruff

# AWS 환경변수 관리
poetry add python-dotenv

# 타입 힌트 지원
poetry add boto3-stubs[essential]
```

---

## 자주 쓰는 Poetry 명령어 요약

| 명령어 | 설명 |
|--------|------|
| `poetry install` | 의존성 설치 |
| `poetry add <package>` | 패키지 추가 |
| `poetry remove <package>` | 패키지 제거 |
| `poetry shell` | 가상환경 활성화 |
| `poetry run <cmd>` | 가상환경에서 명령 실행 |
| `poetry show` | 설치된 패키지 목록 |
| `poetry env info` | 가상환경 정보 |

---

## 주의사항

- `poetry new`는 새 디렉토리를 생성합니다. 기존 디렉토리에 초기화하려면 `poetry init`을 사용하세요.
- `.gitignore`에 `.venv/` 또는 Poetry 캐시 경로를 추가하는 것을 권장하세요.
- 프로젝트를 공유할 때는 `poetry.lock` 파일도 함께 커밋하세요.