# CLaiM
> RAG 기반 보험청구심사 자동화 서비스

보험 약관 PDF를 기반으로 한 RAG(Retrieval-Augmented Generation) 시스템으로, 사용자의 보험 청구 정보에 대해 유사한 약관을 검색하고 심사 의견을 제공합니다.

## 주요 특징

### 🔐 사용자별 보험 관리 시스템
- **독립적인 보험 관리**: 각 사용자별로 별도의 보험 약관 등록 및 관리
- **사용자별 벡터스토어**: `faiss_db/user_{user_id}_{insurance_type}` 구조로 격리

### 🤖 AI 기반 심사 요약 
- **한줄 요약**: "심사 통과 확률 높음/보통/낮음" 형태로 즉시 확인 가능
- **LLM 분석**: OpenAI/Qwen 모델을 통한 지능적인 승인 가능성 판단


##  시스템 아키텍처 및 데이터 플로우

<img width="1352" height="492" alt="Image" src="https://github.com/user-attachments/assets/5cfc1d64-e246-46cf-b455-77e1b31c0472" />

## UI

<img width="961" height="647" alt="Image" src="https://github.com/user-attachments/assets/ae94e117-7c0c-4bd6-afb4-603bd72f440c" />

## 주요 기능

### 1. 사용자 관리 시스템
- **사용자별 프로필**: 나이, 성별, 기존 병력 정보 포함
- **독립적인 세션**: 각 사용자별로 별도의 보험 데이터 관리
- **SQLite 통합**: 사용자 정보와 보험 데이터 영구 저장

### 2. 보험 약관 관리
- **종류별 분류**: 생명보험, 손해보험, 자동차보험 3개 카테고리
- **사용자별 업로드**: 각 사용자가 자신만의 약관 등록 가능
- **PDF 처리**: 텍스트 추출, 정규화, 청크 분할
- **벡터 저장**: 사용자별 FAISS 벡터스토어 생성 및 관리

### 3. 지능형 심사 시스템
- **RAG 검색**: 상위 3개 유사 약관 검색 (k=3)
- **MMR 다양성**: Maximum Marginal Relevance로 결과 최적화
- **AI 분석**: GPT-4o / Qwen 1.5 기반 심사 의견 제공
- **승인 확률**: 높음/보통/낮음으로 간단 요약


## 기술 스택

- **Backend**: FastAPI, Python 3.13, SQLAlchemy, SQLite
- **Frontend**: Streamlit
- **AI/ML**: 
  - **Embedding**: OpenAI Embeddings, BAAI/BGE-M3 (선택 가능)
  - **LLM**: OpenAI GPT-4o, Qwen 1.5(0.5B) (선택 가능)
  - **Vector DB**: FAISS (사용자별 독립 인덱스)
  - **Framework**: LangChain
- **Database**: SQLite (사용자/보험 데이터)
- **PDF Processing**: PyPDF (텍스트 추출 및 정규화)
- **Dependencies**: uvicorn, requests, python-dotenv


## 👥 Contributors

<a href="https://github.com/Light-Weight-DH">
  <img src="https://avatars.githubusercontent.com/Light-Weight-DH" width="60px" style="border-radius:50%;" />
</a>
<a href="https://github.com/Hyunuk17">
  <img src="https://avatars.githubusercontent.com/Hyunuk17" width="60px" style="border-radius:50%;" />
</a>
<a href="https://github.com/wxxwls">
  <img src="https://avatars.githubusercontent.com/wxxwls" width="60px" style="border-radius:50%;" />
</a>

- [@rokart.F](https://github.com/Light-Weight-DH)  
- [@Hyunuk17](https://github.com/Hyunuk17)  
- [@wxxwls](https://github.com/wxxwls)  
