# Claude Skills Collection

Claude Code에서 사용하는 커스텀 스킬 모음입니다.

## 스킬 목록

### [fmp-stock-project-setup](fmp-stock-project-setup/SKILL.md)

FMP(Financial Modeling Prep) API 기반 주식 데이터 분석 Python 프로젝트를 Poetry로 설정하는 스킬입니다.

**적용 요청 예시**
- "FMP 프로젝트 만들어줘"
- "주식 데이터 분석 프로젝트 세팅"
- "FMP API 프로젝트 초기화"
- "파이썬으로 주식 데이터 가져오는 프로젝트"

**생성되는 구조**
```
./
├── pyproject.toml
├── README.md
├── .env
├── .env.example
├── src/
│   ├── fmp/          # FMP API 호출 모듈
│   ├── analysis/     # 데이터 가공·분석 모듈
│   └── display/      # 출력·시각화 모듈
└── tests/
```

**주요 의존성**: `requests`, `pandas`, `numpy`, `matplotlib`, `plotly`, `python-dotenv`

---

### [python-project-setup](python-project-setup/SKILL.md)

Poetry 기반 Python 프로젝트 기본 구조를 설정하는 스킬입니다. AWS 작업을 위한 boto3 설치를 포함합니다.

**적용 요청 예시**
- "파이썬 프로젝트 만들어줘"
- "Poetry로 프로젝트 세팅"
- "AWS 프로젝트 초기화"
- "Python 환경 설정"

**생성되는 구조**
```
./
├── pyproject.toml
├── src/
│   └── __init__.py
└── tests/
    └── __init__.py
```

**주요 의존성**: `boto3`

---

## 전제 조건

모든 스킬은 Poetry가 설치된 환경을 전제로 합니다.

```bash
# Poetry 설치 확인
poetry --version

# 미설치 시 설치
curl -sSL https://install.python-poetry.org | python3 -
```

## 참고

- [Poetry 공식 문서](https://python-poetry.org/docs)
- [FMP API 문서](https://financialmodelingprep.com/developer/docs)
