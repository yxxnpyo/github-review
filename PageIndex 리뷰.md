<div style="display:flex;flex-wrap:wrap;gap:10px;justify-content:center;margin:4px 0 12px 0;">
  <a href="https://github.com/VectifyAI/PageIndex" target="_blank" rel="noopener" style="display:inline-flex;align-items:center;gap:10px;padding:8px 14px;border:1px solid #d0d7de;border-radius:12px;background:#f6f8fa;color:#24292f;text-decoration:none;font-weight:700;">
    <span style="font-size:14px;">📅</span>
    <span>Create Date</span>
    <span style="display:inline-flex;align-items:center;justify-content:center;min-width:92px;height:24px;padding:0 8px;border-radius:999px;background:#eaeef2;color:#24292f;font-weight:700;font-size:12px;">2025-04-01</span>
  </a>
  <a href="https://github.com/VectifyAI/PageIndex/stargazers" target="_blank" rel="noopener" style="display:inline-flex;align-items:center;gap:10px;padding:8px 14px;border:1px solid #d0d7de;border-radius:12px;background:#f6f8fa;color:#24292f;text-decoration:none;font-weight:700;">
    <span style="font-size:14px;">⭐</span>
    <span>Star</span>
    <span style="display:inline-flex;align-items:center;justify-content:center;min-width:58px;height:24px;padding:0 8px;border-radius:999px;background:#eaeef2;color:#24292f;font-weight:700;font-size:12px;">17,045</span>
  </a>
  <a href="https://github.com/VectifyAI/PageIndex/fork" target="_blank" rel="noopener" style="display:inline-flex;align-items:center;gap:10px;padding:8px 14px;border:1px solid #d0d7de;border-radius:12px;background:#f6f8fa;color:#24292f;text-decoration:none;font-weight:700;">
    <img src="_assets/si-github.png" alt="GitHub" style="width:16px;height:16px;" />
    <span>Fork</span>
    <span style="display:inline-flex;align-items:center;justify-content:center;min-width:52px;height:24px;padding:0 8px;border-radius:999px;background:#eaeef2;color:#24292f;font-weight:700;font-size:12px;">1,219</span>
  </a>
</div>

<div style="display:flex;flex-wrap:wrap;gap:8px;justify-content:center;margin:8px 0 14px 0;">
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#3776AB;color:#ffffff;font-weight:700;"><img src="_assets/siw-python.png" alt="Python" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Python</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#111111;color:#ffffff;font-weight:700;"><img src="_assets/siw-markdown.png" alt="Markdown" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Markdown</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#3775A9;color:#ffffff;font-weight:700;"><img src="_assets/siw-pypi.png" alt="PyPI" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">PyPI</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#F37626;color:#ffffff;font-weight:700;"><img src="_assets/siw-jupyter.png" alt="Jupyter" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Jupyter</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#333333;color:#ffffff;font-weight:700;"><img src="_assets/siw-json.png" alt="JSON" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">JSON</span></span>
</div>

<img src="https://github.com/user-attachments/assets/46201e72-675b-43bc-bfbd-081cc6b65a1d" alt="PageIndex 배너" style="max-width: 100%; width: 100%; height: auto; display: block;" />

*출처: PageIndex README 배너*


PageIndex는 긴 문서를 검색할 때 흔히 쓰는 "임베딩 유사도 + 벡터 DB" 접근을 정면으로 바꾼 프로젝트입니다.
이 레포는 문서를 잘게 쪼갠 조각(chunk) 대신, 문서 구조 자체를 계층 트리로 재구성하고 LLM이 그 트리를 따라가며 답을 찾도록 설계되어 있습니다.
쉽게 말해 "비슷한 문장"을 찾는 엔진이 아니라 "정답이 있을 법한 장/절"을 사람처럼 추론해 들어가는 방식에 가깝습니다.
README와 코드 모두 이 철학(Reasoning-based, No Vector DB, No Chunking)을 일관되게 강조하며, 구현도 `run_pageindex.py`(CLI) + `pageindex/page_index.py`(PDF 코어) + `pageindex/page_index_md.py`(Markdown 코어)로 역할 분리가 명확합니다.
결과적으로 이 프로젝트의 가치는 단순 검색 정확도뿐 아니라, **근거 추적 가능성(왜 이 답을 냈는지 설명 가능성)**과 **장문 문서 운영 안정성**에 있습니다.

## 1. 🔍 핵심 기능 및 구현 방식 (Core Features & Implementation)

PageIndex의 핵심은 "문서를 구조화하고, 그 구조를 기반으로 추론 검색을 수행"하는 흐름입니다.
실제 코드 기준으로 보면 PDF/Markdown 인덱싱 엔진이 분리되어 있고, TOC(목차) 품질 편차를 흡수하기 위한 검증·보정 루프가 들어가 있어 단순 데모성 구현을 넘어 운영을 의식한 설계가 보입니다.
특히 이 프로젝트가 실무적으로 흥미로운 이유는, "검색 품질"을 모델 성능 문제로만 보지 않고 문서 구조화 품질 문제로 함께 다룬다는 점입니다.

### 핵심 기능 5가지

<img src="https://docs.pageindex.ai/images/cookbook/vectorless-rag.png" alt="PageIndex 벡터리스 RAG 개념도" style="max-width: 100%; width: 100%; height: auto; display: block;" />

*출처: PageIndex README - Introduction Figure*


- **(1) PDF를 계층형 트리 인덱스로 변환**
  - 문서를 `title`, `start_index`, `end_index`, `nodes[]` 형태의 트리 JSON으로 변환합니다.
  - 구현 근거: `pageindex/page_index.py`의 `tree_parser`, `post_processing`, `page_index_main`.
  - 중요 포인트: "문단 조각"이 아니라 "섹션 구조"를 유지하므로, 나중에 답변 근거를 페이지/절 단위로 설명하기 쉽습니다.

- **(2) TOC 상태별 다중 모드 처리 + 자동 폴백**
  - TOC에 페이지 정보가 있으면 `process_toc_with_page_numbers`, 없으면 `process_toc_no_page_numbers`, TOC가 부실하면 `process_no_toc`로 내려갑니다.
  - 구현 근거: `pageindex/page_index.py`의 `meta_processor`, `check_toc`, `process_*` 함수군.
  - 중요 포인트: 문서 품질이 들쑥날쑥한 실무 환경에서 단일 전략 실패를 완화합니다.

- **(3) TOC 검증/수정 루프(정확도 보정)**
  - 추출된 섹션 제목이 실제 페이지 텍스트에 등장하는지 비동기 검증하고(`verify_toc`), 오차가 있으면 `fix_incorrect_toc_with_retries`로 반복 수정합니다.
  - 구현 근거: `pageindex/page_index.py`의 `verify_toc`, `fix_incorrect_toc_with_retries`, `check_title_appearance*`.
  - 중요 포인트: 페이지 오프셋 밀림(오프바이원), 잘못된 섹션 매핑 같은 실전 오류를 잡아냅니다.

- **(4) 큰 노드 재귀 세분화(토큰/페이지 기반)**
  - 노드가 너무 크면(`max_page_num_each_node`, `max_token_num_each_node`) 해당 구간만 다시 파싱해 하위 트리를 만듭니다.
  - 구현 근거: `pageindex/page_index.py`의 `process_large_node_recursively`.
  - 중요 포인트: 장문 문서에서 컨텍스트 제한(LLM 입력 한계)에 덜 취약한 구조를 만듭니다.

- **(5) Markdown 전용 인덱서 + Tree Thinning**
  - `#`, `##` 헤딩을 계층으로 쓰고, 코드블록 내부 헤딩은 제외합니다. 필요하면 작은 노드를 합쳐(thinning) 트리 과복잡성을 줄입니다.
  - 구현 근거: `pageindex/page_index_md.py`의 `extract_nodes_from_markdown`, `build_tree_from_nodes`, `tree_thinning_for_index`.
  - 중요 포인트: 기술 문서/사내 가이드처럼 Markdown 자산이 많은 팀에서 바로 활용 가능합니다.

### 기술 스택

- **언어**: Python
- **핵심 라이브러리**: `openai`, `PyPDF2`, `pymupdf`, `tiktoken`, `pyyaml`, `python-dotenv`
- **설정 파일**: `pageindex/config.yaml`
- **CLI 엔트리**: `run_pageindex.py`
- 근거: `requirements.txt`, `run_pageindex.py`, `pageindex/utils.py`, `pageindex/config.yaml`

### 코드 구조 요약 (실무 관점)

- **입력/실행 계층**: `run_pageindex.py`
  - PDF와 Markdown 흐름을 분기하고, 옵션을 병합해 실행합니다.
- **PDF 코어 계층**: `pageindex/page_index.py`
  - TOC 탐지/정합성 검증/보정/트리 후처리/요약 생성까지 담당합니다.
- **Markdown 코어 계층**: `pageindex/page_index_md.py`
  - 헤딩 파싱과 트리 구성, thinning, summary 처리를 담당합니다.
- **공통 인프라 계층**: `pageindex/utils.py`
  - API 재시도, 토큰 계산, JSON 파싱, Config 로딩을 맡습니다.

실무적으로는 이 구조가 꽤 유리합니다.
기능 추가 포인트가 비교적 명확하기 때문입니다.
예를 들어 내부 LLM 게이트웨이로 바꾸려면 `utils.py`의 API 래퍼 계층을 교체하면 되고, 문서 전처리 정책을 바꾸려면 `page_index.py`의 TOC/검증 경로를 조정하면 됩니다.
즉 "어디를 건드려야 하는지"가 보이는 구조라는 점이 유지보수성을 높입니다.

## 2. 💡 이 프로젝트는 왜 필요한가요? (Why & Background)

전통적인 벡터 RAG는 "유사한 문장"은 잘 찾지만, 전문 문서 QA에서는 "정답이 위치한 구조적 맥락"을 놓칠 때가 많습니다.
PageIndex가 겨냥하는 문제는 정확히 이 지점입니다.
README가 `similarity ≠ relevance`를 전면에 둔 이유도 같습니다.
비슷해 보이는 문장보다, 실제 정답이 있는 장/절을 논리적으로 찾는 게 더 중요하다는 문제의식입니다.

이 프로젝트의 접근은 단순히 검색 엔진 교체가 아니라, 검색의 단위를 바꾸는 것입니다.
기존 방식이 "chunk top-k" 중심이라면 PageIndex는 "문서 트리에서 관련 노드 후보를 추론"하는 방식입니다.
그래서 답변의 근거(어느 섹션/페이지를 근거로 삼았는지)를 추적하기 쉬워지고, 감사/컴플라이언스처럼 출처 설명이 중요한 도메인에 더 잘 맞습니다.

또 하나 중요한 맥락은 성능 주장입니다.
README는 Mafin 2.5 사례에서 FinanceBench 기준 98.7%를 언급합니다.
이 수치는 프로젝트 측 사례/주장으로 읽어야 하며, 도입 전에는 자사 문서셋으로 재현 검증이 필요합니다.
그럼에도 "벡터 유사도 한계" 문제를 정면으로 겨냥한 접근이라는 점에서, 대안 아키텍처로 검토할 가치가 충분합니다.

### 벤치마크를 실무에 연결해서 읽는 방법

<img src="https://github.com/user-attachments/assets/571aa074-d803-43c7-80c4-a04254b782a3" alt="FinanceBench 성능 비교 그래프" style="max-width: 100%; width: 100%; height: auto; display: block;" />

*출처: PageIndex README - FinanceBench Case Study*


벤치마크 수치는 방향성을 보여주지만, 실무에서는 "수치"보다 "운영 시나리오 적합성"이 더 중요합니다.
예를 들어 같은 질문 정확도라도, 답변 근거를 빠르게 검증할 수 있다면 실제 현업의 검수 시간이 줄어들고, 이는 운영 비용 절감으로 이어집니다.
PageIndex가 페이지/노드 단위 근거를 제공하는 구조라는 점은 이런 실무 KPI(검수 시간, 재질문 비율, 잘못된 근거 탐지 시간)에 직접 연결됩니다.

권장 검증 방식은 단순합니다.
1) 기존 RAG와 질문셋을 동일하게 맞춰 비교하고, 2) 정답률뿐 아니라 "근거 페이지 적합성"을 같이 채점하고, 3) 실패 케이스를 문서 선택/노드 선택/최종 생성 단계로 분해합니다.
이 3단계를 거치면 도입 효과를 체감 지표로 판단할 수 있습니다.

### 에코시스템 확장 맥락

이 레포는 로컬 OSS 실행용 코어에 집중하지만, README 기준으로 Chat Platform, MCP, API, Docs, Discord까지 연결 고리가 명확합니다.
즉 "로컬 실험"으로 시작해 "서비스 통합"으로 확장하는 경로가 준비된 상태입니다.
많은 팀이 PoC 이후 확장 단계에서 막히는데, PageIndex는 이 전환 동선을 문서 차원에서 비교적 분명하게 제공한다는 점이 장점입니다.

## 3. 🎯 어떤 상황에서 쓰면 좋을까요? (Use Cases)

PageIndex는 일반 웹문서 검색보다, 구조가 길고 답의 근거 추적이 필요한 문서군에서 강점을 냅니다.
특히 "정확도"만큼 "설명 가능성"이 중요한 조직(재무, 규제, 법무, 엔터프라이즈 운영)에 적합합니다.
즉, 답변을 잘하는 것만으로는 부족하고 "왜 이 답인지"를 설명해야 하는 현장일수록 적합도가 올라갑니다.

- **시나리오 A: 재무/규제 보고서 질의응답**
  - 상황: 연차보고서/공시문서에서 특정 지표의 근거 문장을 찾아야 함.
  - 적용 방법: 문서를 트리로 만든 뒤 관련 노드와 페이지 범위를 좁혀 근거 답변 생성.
  - 기대 결과: 답변 + 근거 페이지를 같이 제시하기 쉬워 검증 비용이 줄어듭니다.

- **시나리오 B: 운영 런북/매뉴얼 기반 장애 대응**
  - 상황: 수백 페이지 운영문서에서 장애 코드별 절차를 빠르게 찾아야 함.
  - 적용 방법: `node_id`/페이지 범위 기준으로 탐색 규칙을 만들고 재사용.
  - 기대 결과: 키워드 검색보다 "절차 문맥"을 유지한 탐색이 가능해집니다.

- **시나리오 C: 다문서 지식베이스 검색 파이프라인**
  - 상황: 문서가 수십~수백 개라 먼저 문서 후보를 골라야 함.
  - 적용 방법: `tutorials/doc-search`의 metadata/semantics/description 전략으로 doc_id 선별 후 tree search.
  - 기대 결과: 문서 선택(outer retrieval)과 문서 내부 검색(inner retrieval)을 분리해 유지보수가 쉬워집니다.

핵심은 "질문 → 문서 후보 → 문서 내부 노드"의 2단계/3단계 검색 구조를 설계할 수 있다는 점입니다.
이 구조는 나중에 규칙 기반 필터, 사용자 선호, 도메인 룰을 추가하기도 쉽습니다.
장기적으로는 조직 지식(예: 특정 산업에서 중요한 절 우선 탐색 규칙)을 프롬프트 정책으로 녹여 성능을 더 끌어올릴 수 있습니다.

## 4. 🚀 어떻게 사용하나요? (How to use)

### A) 가장 빠른 로컬 실행 흐름

1. 의존성 설치
```bash
pip3 install --upgrade -r requirements.txt
```

2. OpenAI 키 설정 (`.env`)
```env
CHATGPT_API_KEY=your_openai_key_here
```

3. PDF 인덱싱 실행
```bash
python3 run_pageindex.py --pdf_path /path/to/document.pdf
```

4. Markdown 인덱싱 실행
```bash
python3 run_pageindex.py --md_path /path/to/document.md
```

5. 결과 확인
- `./results/<파일명>_structure.json`

### B) 자주 쓰는 옵션 빠르게 이해하기

- `--model`: LLM 모델 선택
- `--toc-check-pages`: TOC 검사 페이지 범위
- `--max-pages-per-node`, `--max-tokens-per-node`: 큰 노드 분할 기준
- `--if-add-node-id`, `--if-add-node-summary`, `--if-add-doc-description`, `--if-add-node-text`
- 근거: `run_pageindex.py`, `pageindex/config.yaml`, README optional parameters

옵션 설계도 실무 관점에서 괜찮습니다.
예를 들어 노드가 너무 커서 정답 탐색이 흐려지는 경우 `max-pages-per-node`와 `max-tokens-per-node`를 조정해 탐색 단위를 재조정할 수 있습니다.
반대로 속도가 중요하면 요약 생성 옵션을 단계적으로 켜면서 처리 시간을 관리할 수 있습니다.

### C) 폐쇄망(인터넷 불가) 환경 사용 가능 여부

- **결론**: 조건부 가능
- **이유**: 패키지와 모델 호출 경로를 내부화하면 가능하지만, 기본 코드는 OpenAI API 직접 호출을 전제로 합니다(`pageindex/utils.py`의 `openai.OpenAI`, `AsyncOpenAI`).

### D) 폐쇄망에서 실제로 돌리는 방법

1. 인터넷 가능 구간에서 wheel 사전 수집
- `openai`, `pymupdf`, `PyPDF2`, `tiktoken`, `pyyaml`, `python-dotenv` 등

2. 내부 Git 미러 또는 반입 소스 사용
- 외부 GitHub 차단망이면 내부 mirror에서 clone

3. 내부 비밀관리로 키 주입
- `.env` 직접 파일 대신 Vault/Secret Manager/K8s Secret 권장

4. 외부 API 차단망이면 LLM 래퍼 교체
- `ChatGPT_API`, `ChatGPT_API_async`를 내부 LLM 게이트웨이에 맞게 수정

5. 결과 저장 경로/권한 검증
- `./results` 쓰기 가능 여부, 파일 권한, 로그 경로 확인

즉, "인터넷 완전 차단 + 외부 API 불가" 환경에서는 코드 수정이 필요합니다.
반대로 "내부망에서 외부 API 프록시 허용" 환경이면 비교적 빠르게 붙일 수 있습니다.

### E) 운영으로 넘길 때 체크리스트

- 샘플 문서셋(최소 20개)으로 품질 회귀 테스트 만들기
- 답변 정확도 외에 근거 적합도 지표 추가하기
- 실패 케이스를 TOC 탐지/노드 분할/최종 생성 단계로 분류하기
- API 장애 시 재시도/백오프/대체 모델 정책 정의하기

이 체크리스트를 먼저 만들면, PoC는 잘되는데 운영에서 흔들리는 문제를 크게 줄일 수 있습니다.

## 5. ✨ 사용하면 무엇이 좋아지나요? (Benefits)

PageIndex의 가장 큰 이점은 검색 성능 그 자체보다 **검색 결과를 설명하고 운영하는 비용을 줄여준다**는 데 있습니다.
도입 이후 팀이 얻는 체감 가치는 "정답률 상승"과 "근거 추적 쉬움"이 함께 오는지에서 결정됩니다.
즉, 단순히 모델 점수가 아니라 조직의 의사결정 속도와 검수 품질이 개선되는지가 핵심 평가 기준입니다.

- **개발 효율 측면**
  - `run_pageindex.py`와 `cookbook/`, `tutorials/`가 있어 PoC 시작 속도가 빠릅니다.
  - PDF/Markdown 흐름이 분리되어 문서 타입별 실험이 쉽습니다.

- **정확도/설명 가능성 측면**
  - 트리 노드 + 페이지 범위 중심으로 근거를 제시할 수 있어 리뷰/감사 대응에 유리합니다.
  - TOC 검증/수정 루프가 있어 문서 품질 편차에 대한 회복력이 있습니다.

- **유지보수 측면**
  - 문서 후보 선택(doc-search)과 문서 내부 탐색(tree-search)을 분리해 아키텍처를 점진적으로 고도화하기 좋습니다.
  - 노드 요약/문서 설명 생성 옵션을 활용하면 다문서 분류 파이프라인으로 확장하기 쉽습니다.

- **에코시스템 확장성 측면**
  - README 기준으로 Chat Platform, MCP, API, Docs, Discord까지 연결 포인트가 열려 있습니다.
  - 즉, 오픈소스 로컬 실행에서 시작해 서비스형 통합으로 확장 가능한 경로가 비교적 명확합니다.

### 줄일 수 있는 리스크/비용

- "비슷하지만 틀린" 문단을 집어오는 리스크를 구조적으로 완화
- 근거 섹션 추적 시간 감소(디버깅/검수 비용 절감)
- 장문 문서에서 재탐색 반복 비용 감소
- 팀 내 답변 품질 편차(사람마다 검색 품질이 다른 문제) 완화

### 현실적 트레이드오프

- OpenAI API 의존(비용/레이트리밋/네트워크 영향)
- 문서 원본 품질(특히 변환 Markdown 계층 보존 여부)에 민감
- 벤치마크 수치는 프로젝트 주장 기준이므로 사내 데이터 재검증 필요
- 고품질 운영을 위해서는 평가셋/검수 프로세스 구축이 필요

### 도입 관점 최종 정리

PageIndex는 "벡터 검색의 대체재"라기보다, "문서 구조 기반 추론 검색"이라는 별도 철학을 가진 프레임워크로 보는 게 정확합니다.
그래서 기존 RAG를 완전히 버리기보다, 문서 특성에 따라 하이브리드 전략(문서 선택은 시맨틱, 문서 내부는 트리 추론)을 택하는 접근이 현실적입니다.
이런 형태로 설계하면 정확도와 설명 가능성 사이의 균형을 더 잘 맞출 수 있습니다.

**도입 판단 한줄 정리**: "근거 추적이 중요한 장문 문서 QA"가 핵심 과제라면, PageIndex는 벡터 중심 RAG의 대안으로 충분히 검토할 가치가 있습니다.

### 실무 도입 로드맵 (3단계 제안)

- **1단계: 탐색(PoC)**
  - 목표: 기존 RAG 대비 탐색 품질 차이를 빠르게 확인
  - 방법: 대표 문서 10~20개로 트리 생성, 질문셋 50문항 비교
  - 산출물: 성공/실패 유형 리포트(근거 페이지 정확도 포함)

- **2단계: 검증(Pilot)**
  - 목표: 실제 운영팀이 체감하는 개선 여부 확인
  - 방법: 현업 검수자에게 블라인드 비교(기존 vs PageIndex) 진행
  - 산출물: 검수 시간, 재질문 비율, 오답 원인 분포

- **3단계: 확장(Production)**
  - 목표: 서비스 연동과 운영 자동화
  - 방법: API/MCP 연동, 장애 대응 정책, 모니터링 지표 정착
  - 산출물: 월간 성능 리포트, 모델/프롬프트 업데이트 프로세스

### 운영 지표 예시 (추천)

- **정확도 지표**: 정답률, 근거 페이지 적합률, 무근거 답변 비율
- **운영 지표**: 평균 응답 시간, 재시도율, API 실패율
- **비용 지표**: 질의당 토큰 비용, 월간 총비용, 케이스당 검수 인건비

이렇게 지표를 나눠보면 "성능은 좋은데 운영이 무너지는" 상황을 조기에 잡을 수 있습니다.
특히 규제가 강한 도메인에서는 정확도만큼 근거 일관성이 중요하므로, 근거 적합률과 무근거 답변 비율을 별도 핵심지표로 두는 것이 좋습니다.
