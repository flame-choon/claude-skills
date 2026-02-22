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

### [fmp-stock-api](fmp-stock-api/SKILL.md)

FMP API endpoint를 코드로 작성할 때 참조하는 레퍼런스 스킬입니다. 잘못된 endpoint나 파라미터 사용을 방지합니다.

**적용 요청 예시**
- FMP API endpoint 코드 작성 시
- 주가 차트, 시세, 재무제표, 기업정보 조회 구현 시
- `/api/v3/` 대신 `/stable/` 경로 사용 확인이 필요할 때

**카테고리별 레퍼런스**

| 카테고리 | 파일 | 주요 Endpoint |
|---------|------|--------------|
| Charts | [chart/endpoints.md](fmp-stock-api/chart/endpoints.md) | EOD 차트(light/full), Intraday(1min~4hour) |
| Quote | [quote/endpoints.md](fmp-stock-api/quote/endpoints.md) | 단일/Batch 시세, After Market, 거래소 전체 |
| Company | [company/endpoints.md](fmp-stock-api/company/endpoints.md) | 기업 프로필, 임원, 시총, 유통주식수, M&A |
| Statements | [statements/endpoints.md](fmp-stock-api/statements/endpoints.md) | 재무제표 3종, TTM, 핵심지표, 성장률, 10-K |
| Directory | [directory/endpoints.md](fmp-stock-api/directory/endpoints.md) | 종목 검색, 스크리너, 심볼 리스트 |

**Base URL**: `https://financialmodelingprep.com/stable`

---

## 전제 조건

`fmp-stock-project-setup`, `python-project-setup` 스킬은 Poetry가 설치된 환경을 전제로 합니다.

```bash
# Poetry 설치 확인
poetry --version

# 미설치 시 설치
curl -sSL https://install.python-poetry.org | python3 -
```

## 참고

- [Poetry 공식 문서](https://python-poetry.org/docs)
- [FMP API 문서](https://financialmodelingprep.com/developer/docs)
