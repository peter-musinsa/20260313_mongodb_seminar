# HELP.md — 프로젝트 세팅 가이드

## 이 프로젝트는 어디서 왔는가?

이 저장소는 **MongoDB GenAI Developer Day** 공식 워크숍 자료입니다.

- **원본 GitHub:** https://github.com/mongodb-developer/genai-devday-workshops
- **워크숍 플랫폼 (Instruqt):** https://play.instruqt.com/mongo-devrel-dev/invite/guu0hvgwxxnb/tracks/ai-labs-vector-search
- **Lab 셋업 가이드:** https://mongodb-developer.github.io/ai-lab-setup/

코드베이스를 직접 클론해서 가져왔으며, Instruqt 플랫폼은 해당 lab을 브라우저 기반 환경에서 실행할 수 있도록 제공하는 워크숍 플랫폼입니다.

---

## 프로젝트 구조

```
20260313_seminar/
├── labs/                        # 실습용 노트북 (직접 작성하며 배우는 버전)
│   ├── vector-search-lab.ipynb  # Lab 1: Vector Search 기초
│   ├── ai-rag-lab.ipynb         # Lab 2: RAG 애플리케이션
│   └── ai-agents-lab.ipynb      # Lab 3: AI Agent (LangGraph)
│
├── solutions/                   # 완성된 정답 노트북
│   ├── vector-search-lab.ipynb
│   ├── ai-rag-lab.ipynb
│   └── ai-agents-lab.ipynb
│
├── data/                        # 실습용 샘플 데이터
│   ├── books.json               # 도서 데이터 (Vector Search Lab용)
│   ├── mongodb_docs.json        # MongoDB 문서 (RAG Lab용)
│   ├── mongodb_docs_embeddings.json  # 사전 계산된 임베딩 (Agents Lab용)
│   └── images/                  # 멀티모달 검색 샘플 이미지
│
├── utils/
│   └── utils.py                 # 공통 헬퍼 함수 (index 생성, LLM 초기화 등)
│
├── .env                         # API 키 설정 파일 (직접 채워야 함)
├── requirements.txt             # Python 패키지 의존성
└── .devcontainer/               # VS Code Dev Container 설정
```

---

## API 키 구조 이해

```
텍스트/이미지 → [Voyage AI API] → 벡터 → [MongoDB] → 저장 + 검색
```

- **Voyage AI**: 텍스트/이미지를 숫자 배열(벡터)로 변환해주는 외부 서비스. MongoDB와 별개입니다.
- **MongoDB**: 벡터를 저장하고 유사도 검색을 해주는 DB. Vector Search는 원래 Atlas 클라우드 전용 기능이지만, `mongodb-atlas-local` 도커 이미지로 로컬에서도 동일하게 사용할 수 있습니다.
- **PASSKEY**: 원래 Voyage AI 계정을 직접 만들어야 하지만, 워크숍 패스키가 있으면 MongoDB가 운영하는 프록시 서버를 통해 Voyage AI + LLM API 키가 자동으로 주입됩니다. 별도 계정 불필요.

### 패스키가 없는 경우

두 가지를 직접 준비해야 합니다:

1. **Voyage AI API 키** — https://www.voyageai.com 에서 계정 생성 후 발급, `.env`에 `VOYAGE_API_KEY=<키>` 설정
2. **LLM API 키** — 아래 중 하나 선택

| Provider | 필요한 환경변수 |
|----------|---------------|
| Google Gemini | `GOOGLE_API_KEY` |
| AWS Bedrock | AWS 자격증명 (`~/.aws/credentials`) |
| Azure OpenAI | `AZURE_OPENAI_API_KEY` 등 |

노트북에서 `set_env()` 호출 셀은 건너뛰고, 환경변수를 직접 설정한 채로 실행하면 됩니다.
자세한 내용: https://mongodb-developer.github.io/ai-lab-setup/docs/setup-pre-reqs#expired-passkey-or-dont-have-a-passkey

---

## `.env` 파일 설정

프로젝트 루트의 `.env` 파일에 아래 값을 설정합니다.

### 패스키가 있는 경우 (워크숍 참가자)

```env
PASSKEY=<워크숍_패스키>
```

`MONGODB_URI`는 docker-compose 실행 시 자동으로 설정되므로 건드리지 않아도 됩니다.

### 패스키가 없는 경우

```env
MONGODB_URI=mongodb://admin:mongodb@localhost:27017/
VOYAGE_API_KEY=<Voyage AI 키>       # https://www.voyageai.com 에서 발급

# LLM은 아래 중 하나만 설정
GOOGLE_API_KEY=<Google Gemini 키>
# 또는 AWS Bedrock: ~/.aws/credentials 파일로 설정 (환경변수 불필요)
# 또는 Azure OpenAI:
# AZURE_OPENAI_API_KEY=<키>
# AZURE_OPENAI_ENDPOINT=<엔드포인트>
```

---

## 로컬에서 실행하는 방법

### 방법 1: docker-compose (권장)

Docker만 설치되어 있으면 됩니다. MongoDB Atlas Local + Jupyter Lab을 한 번에 실행합니다.

#### 1단계 — `.env` 설정

위의 `.env` 파일 설정 섹션을 참고하여 설정합니다.

#### 2단계 — 실행 전 확인

```bash
cat .env  # PASSKEY 또는 VOYAGE_API_KEY가 설정되어 있는지 확인
```

#### 2단계 — 실행

```bash
docker-compose up
```

- MongoDB Atlas Local이 먼저 기동되고 헬스체크 통과 후 Jupyter Lab 컨테이너 시작
- `pip install -r requirements.txt` 자동 실행
- `http://localhost:8888` 접속 (토큰/패스워드 없이 바로 접속)
- `labs/` 폴더에서 노트북 실행

#### 종료

```bash
docker-compose down        # 컨테이너 종료 (MongoDB 데이터 유지)
docker-compose down -v     # 컨테이너 + MongoDB 데이터 전체 삭제
```

---

### 방법 2: 로컬 Python 환경

#### 1단계 — Python 가상환경 생성 및 의존성 설치

```bash
python3.11 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

> Python 3.11이 없으면: `brew install python@3.11`

#### 2단계 — `.env` 파일 설정

```env
MONGODB_URI=mongodb://admin:mongodb@localhost:27017/
PASSKEY=<워크숍_패스키>
```

#### 3단계 — MongoDB Atlas Local 실행

```bash
docker run -d -p 27017:27017 \
  -e MONGODB_INITDB_ROOT_USERNAME=admin \
  -e MONGODB_INITDB_ROOT_PASSWORD=mongodb \
  mongodb/mongodb-atlas-local:latest
```

#### 4단계 — Jupyter Lab 실행

```bash
jupyter lab
```

브라우저에서 `http://localhost:8888` 접속.

---

## 실습 순서

| 순서 | 노트북 | 핵심 내용 |
|------|--------|-----------|
| 1 | `labs/vector-search-lab.ipynb` | 임베딩 생성, 벡터 인덱스 생성, 벡터 검색, 하이브리드 검색 |
| 2 | `labs/ai-rag-lab.ipynb` | 문서 청킹, RAG 파이프라인, LLM 연동 |
| 3 | `labs/ai-agents-lab.ipynb` | LangGraph 에이전트, 도구 정의, 메모리 추가 |

막히면 `solutions/` 폴더의 정답 노트북을 참고하세요.

---

## LLM 프로바이더 선택

`utils/utils.py`의 `get_llm(provider)` 함수가 다음 LLM을 지원합니다:

| Provider | 모델 | 필요한 추가 설정 |
|----------|------|-----------------|
| `aws` | Claude Sonnet 4.5 (Bedrock) | AWS 자격증명 필요 |
| `google` | Gemini 2.5 Pro | `GOOGLE_API_KEY` |
| `microsoft` | GPT-4.1 (Azure OpenAI) | `AZURE_OPENAI_*` 환경변수 |

워크숍 환경에서는 패스키(`PASSKEY`)를 통해 프록시 서버로 자동 설정됩니다.

---

## 참고 발표자료

- [Vector Search: Beginner to Pro](https://docs.google.com/presentation/d/e/2PACX-1vR4lPTcr2ZXPTkQLPq3HtTn4vSLG4VrFD3jkOjXmEDrrvyLEElTaz-6JC5KZN4__VJZ2h13aTabGXhG/pub)
- [Building RAG Applications with MongoDB](https://docs.google.com/presentation/d/e/2PACX-1vSN_7zZTqpXSmtUyDalox2kAoealuO4V_aVGqLuTuDKa3I3aJ9nQUdViQKasBNnu2zQVOpT5cubnyFd/pub)
- [The A to Z of Building AI Agents](https://docs.google.com/presentation/d/e/2PACX-1vRMH-7DLejrxrEgrReZTy4p9sKzN35uTaiDRZ8JAM9xtyLFz-utjJzk97FG8mGI96VEuLPnLZWzq10Q/pub)