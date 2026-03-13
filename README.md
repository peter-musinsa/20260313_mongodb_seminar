# MongoDB GenAI Developer Day Notebooks
This repository contains all the workshops delivered at MongoDB's GenAI Developer Days. To find Developer Days happening in your city, check our [Events](https://www.mongodb.com/events) page. 

If you are unable to attend a Developer Day, you can work through the content and hands-on labs on your own:

* [Vector Search: Beginner to Pro](https://docs.google.com/presentation/d/e/2PACX-1vR4lPTcr2ZXPTkQLPq3HtTn4vSLG4VrFD3jkOjXmEDrrvyLEElTaz-6JC5KZN4__VJZ2h13aTabGXhG/pub)

* [Building RAG Applications with MongoDB](https://docs.google.com/presentation/d/e/2PACX-1vSN_7zZTqpXSmtUyDalox2kAoealuO4V_aVGqLuTuDKa3I3aJ9nQUdViQKasBNnu2zQVOpT5cubnyFd/pub)

* [The A to Z of Building AI Agents](https://docs.google.com/presentation/d/e/2PACX-1vRMH-7DLejrxrEgrReZTy4p9sKzN35uTaiDRZ8JAM9xtyLFz-utjJzk97FG8mGI96VEuLPnLZWzq10Q/pub)

**NOTE:** If you're running the workshops on your own, you will need to provide your own API keys for the hands-on labs. Refer to the [lab setup guide](https://mongodb-developer.github.io/ai-lab-setup/docs/setup-pre-reqs#expired-passkey-or-dont-have-a-passkey) for instructions on how to obtain these.

---

## 환경 세팅 및 실행 방법

환경 세팅, `.env` 설정, 실행 방법은 **[HELP.md](./HELP.md)** 를 참고하세요.

| 항목 | 내용 |
|------|------|
| 실행 환경 | Docker (MongoDB Atlas Local + Jupyter Lab) |
| 빠른 시작 | `docker-compose up` → `http://localhost:8888` |
| API 키 | 워크숍 패스키 또는 Voyage AI / LLM 키 직접 발급 |
| 실습 순서 | Vector Search → RAG → AI Agents |

## 실습 가이드 문서

각 실습의 개념 설명과 다이어그램은 `docs/` 폴더를 참고하세요.

| 실습 | 노트북 | 가이드 문서 |
|------|--------|-------------|
| Vector Search | `labs/vector-search-lab.ipynb` | [docs/vector-search-lab.md](./docs/vector-search-lab.md) |
| RAG | `labs/ai-rag-lab.ipynb` | [docs/ai-rag-lab.md](./docs/ai-rag-lab.md) |
| AI Agents | `labs/ai-agents-lab.ipynb` | [docs/ai-agents-lab.md](./docs/ai-agents-lab.md) |
