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

## 시스템 구성

```
insurance_claim_system/
├── main.py                    # FastAPI 백엔드 서버
├── app.py                     # Streamlit 웹 인터페이스  
├── requirements.txt           # 의존성 패키지 목록
├── check_db.py               # SQLite 데이터베이스 확인 스크립트
├── .env                      # 환경변수 설정
├── insurance_system.db       # SQLite 데이터베이스
├── faiss_db/                 # 벡터 데이터베이스 저장소
│   ├── user_1_생명보험/      # 사용자별 보험 종류별 벡터 인덱스
│   ├── user_1_손해보험/
│   ├── user_1_자동차보험/
│   ├── user_2_생명보험/
│   └── ...
└── README.md                 # 이 파일
```

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

## 설치 및 실행

### 1. 의존성 설치
```bash
pip install -r requirements.txt
```

### 2. 환경변수 설정
```bash
cp .env.example .env
```

`.env` 파일을 편집하여 API 키 설정:
```
OPENAI_API_KEY=your_openai_api_key_here
HUGGINGFACE_TOKEN=your_huggingface_token_here
```

### 3. 시스템 실행
개별 실행 (권장):
```bash
# FastAPI 서버 (터미널 1)
source venv/bin/activate
python main.py

# Streamlit 앱 (터미널 2) 
source venv/bin/activate
streamlit run app.py --server.port 8501
```

### 4. 접속
- **웹 인터페이스**: http://localhost:8501
- **API 문서**: http://localhost:8005/docs  
- **API 서버**: http://localhost:8005

### 5. 데이터베이스 확인
```bash
# SQLite DB 내용 확인
python check_db.py

# 또는 sqlite3 명령어 사용
sqlite3 insurance_system.db ".tables"
sqlite3 insurance_system.db "SELECT * FROM users;"
sqlite3 insurance_system.db "SELECT * FROM insurances;"
```

## 사용 방법

### 1. 사용자 선택
1. 웹 인터페이스(http://localhost:8501) 접속
2. "사용자 선택" 메뉴에서 선택
3. "사용자 확인" 버튼으로 로그인

### 2. 보험 약관 업로드
1. "보험 업로드" 메뉴 선택 (사용자 로그인 필수)
2. 보험 종류 선택 (생명보험/손해보험/자동차보험)
3. PDF 파일 업로드 후 "보험 약관 업로드 시작"
4. 사용자별로 독립적인 약관 데이터베이스 구축

### 3. 청구 정보 입력
1. "청구 정보 입력" 메뉴에서 보험 청구 정보 작성
2. 보험 종류, 청구 금액, 청구 유형, 현재 상황 입력
3. "청구 정보 저장" (등록된 약관 없으면 경고 표시)

### 4. 심사 실행
1. "심사 실행" 메뉴 선택
2. "청구 심사 실행" 버튼 클릭
3. **한줄 요약**: 통과 확률 즉시 확인 (색상 구분)
4. 상세 AI 심사 의견 및 관련 약관 검토

### 5. 시스템 상태 확인
- 사이드바에서 현재 사용자의 보험 등록 현황 실시간 확인
- 각 보험 종류별 등록된 파일 수 표시

## API 엔드포인트

### 사용자 관리
- `GET /users` - 더미 사용자 목록 조회
- `GET /users/{user_name}` - 특정 사용자 정보 조회
- `GET /insurance-types` - 지원하는 보험 종류 목록

### 보험 약관 관리  
- `POST /upload-insurance` - 사용자별 보험 약관 PDF 업로드
```json
{
  "insurance_type": "생명보험",
  "user_id": 1,
  "file": "multipart/form-data PDF file"
}
```

- `GET /insurances/{insurance_type}?user_id={user_id}` - 사용자별 업로드된 약관 목록

### RAG 검색 및 분석
- `POST /search-similar` - 사용자별 유사도 기반 약관 검색
```json
{
  "prompt": "검색 쿼리",
  "insurance_type": "생명보험",
  "user_id": 1,
  "top_k": 3
}
```

**응답 예시**:
```json
{
  "prompt": "교통사고 골절상 치료비 청구",
  "response": "상세한 AI 심사 의견...",
  "summary": "심사 통과 확률 높음 - 약관 적용 가능",
  "pdf_titles": ["약관_30652(09)_20250901_(1).pdf"]
}
```

### 시스템 모니터링
- `GET /debug-stats?user_id={user_id}` - 사용자별 벡터스토어 상태 확인
- `GET /health` - 서버 상태 확인

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

## 참고사항 및 주요 특징

### 🔐 보안 및 데이터 격리
- **사용자별 데이터 격리**: 각 사용자의 보험 데이터가 완전히 분리됨
- **벡터스토어 독립성**: `faiss_db/user_{user_id}_{insurance_type}` 구조
- **SQLite 관계형 데이터**: 사용자-보험 외래키 관계로 데이터 무결성 보장

### 🤖 AI 분석 정확도
- **이중 프롬프트 시스템**: 상세 분석 + 한줄 요약 별도 생성
- **컨텍스트 최적화**: MMR 알고리즘으로 중복 제거 및 다양성 확보

## BGE-M3 임베딩 사용하기

BGE-M3 임베딩을 사용하려면:

1. 추가 패키지 설치:
   ```bash
   source venv/bin/activate
   pip install torch sentence-transformers transformers huggingface_hub langchain-huggingface
   ```

2. main.py에서 get_embeddings() 함수 수정:
   ```python
   def get_embeddings():
       if openai_key:
           return OpenAIEmbeddings()
       else:
           # BGE 임베딩 사용
           return get_bge_embeddings()
   ```

3. .env 파일에 HuggingFace 토큰 추가:
   ```
   HUGGINGFACE_TOKEN=your_token_here
   ```

## 문제 해결

### 모델 다운로드 오류
HuggingFace 토큰을 설정하거나 인터넷 연결을 확인하세요.

### 메모리 부족 오류  
CPU 환경에서는 큰 PDF 파일 처리 시 메모리 사용량이 높을 수 있습니다. 파일 크기를 줄이거나 청크 크기를 조정하세요.

### 포트 충돌
8005, 8501 포트가 사용 중인 경우 다음 명령어로 해결:
```bash
# 포트 사용 프로세스 확인 후 종료
lsof -ti:8005 | xargs kill -9
lsof -ti:8501 | xargs kill -9
```

### 데이터베이스 스키마 변경
데이터베이스 구조 변경 후 다음과 같이 초기화:
```bash
rm insurance_system.db  # 기존 DB 삭제
python main.py          # 새 스키마로 재생성
```

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
