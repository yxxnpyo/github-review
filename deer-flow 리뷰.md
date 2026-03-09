<p align="center">
  <a href="https://github.com/bytedance/deer-flow"><img src="https://img.shields.io/badge/Create_Date-2025--05--07-1f2937?style=for-the-badge&labelColor=111827" alt="Create Date" /></a>
  <a href="https://github.com/bytedance/deer-flow/stargazers"><img src="https://img.shields.io/badge/Star-26%2C648-1f2937?style=for-the-badge&logo=github&logoColor=white&labelColor=111827" alt="Star" /></a>
  <a href="https://github.com/bytedance/deer-flow/forks"><img src="https://img.shields.io/badge/Fork-3%2C157-1f2937?style=for-the-badge&logo=github&logoColor=white&labelColor=111827" alt="Fork" /></a>
</p>
<p align="center">
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python" />
  <img src="https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white" alt="FastAPI" />
  <img src="https://img.shields.io/badge/LangChain-1C3C3C?style=for-the-badge&logo=langchain&logoColor=white" alt="LangChain" />
  <img src="https://img.shields.io/badge/Next.js-111827?style=for-the-badge&logo=nextdotjs&logoColor=white" alt="Next.js" />
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white" alt="TypeScript" />
</p>

bytedance/deer-flow는 "딥리서치 앱"보다는 에이전트가 실제로 일할 수 있게 만드는 실행 하네스에 가깝습니다.
README가 강조하듯 2.0은 1.x와 코드를 공유하지 않는 그라운드업 재작성이고, 실제 코드도 `backend/src/agents/lead_agent/agent.py`, `backend/src/subagents/executor.py`, `backend/src/sandbox/middleware.py`를 중심으로 멀티에이전트 실행, 샌드박스, 메모리를 한 덩어리로 묶고 있습니다.
즉 사용자는 "챗 UI 하나"를 받는 것이 아니라, 에이전트에게 파일시스템, 서브에이전트, 스킬, 업로드, 메신저 채널, MCP를 붙여서 제품화할 수 있는 런타임을 받습니다.
2026-03-09 기준 GitHub API 메타데이터는 생성일 2025-05-07, 스타 26,648개, 포크 3,157개, 기본 브랜치 `main`, open issues+PR 234개, open PR 28개, 최근 push 시각 2026-03-09T11:24:47Z입니다.
같은 시점에 releases와 tags API가 모두 비어 있어, 프로젝트는 빠르게 진화하지만 버전 고정은 릴리즈보다는 커밋 SHA 기준으로 가져가는 편이 안전합니다.

## 1. 🔍 핵심 기능 및 구현 방식 (Core Features & Implementation)

- **리드 에이전트 + 미들웨어 체인**: 실행 진입점은 `backend/src/agents/lead_agent/agent.py`의 `make_lead_agent()`입니다.
- **백그라운드 서브에이전트 실행**: `backend/src/tools/builtins/task_tool.py`와 `backend/src/subagents/executor.py`가 긴 작업을 별도 컨텍스트로 분리하고, 폴링과 스트리밍 이벤트까지 처리합니다.
- **세션 격리 샌드박스**: `backend/src/sandbox/middleware.py`와 `backend/docs/ARCHITECTURE.md`는 `/mnt/user-data/{workspace,uploads,outputs}` 구조와 thread 단위 경로 분리를 명확히 설명합니다.
- **장기 메모리와 파일 업로드 정리**: `backend/src/agents/memory/updater.py`는 대화 내용을 JSON 메모리로 축적하면서 업로드 이벤트 문장을 별도 필터링합니다.
- **게이트웨이 + 채널 확장**: `backend/src/gateway/app.py`와 `backend/src/channels/service.py`는 FastAPI REST와 Slack/Telegram/Feishu 채널을 같은 설정 계층으로 노출합니다.

이 프로젝트의 구조적 강점은 기능을 "도구 모음"으로 끝내지 않고, 데이터 흐름으로 묶어 놓았다는 점입니다.
브라우저 요청은 Nginx를 거쳐 LangGraph 서버와 Gateway API로 분기되고, 에이전트는 미들웨어 체인을 통과하며 thread 작업 디렉터리, 업로드 파일, 샌드박스, 메모리, 이미지 뷰어, clarification 흐름을 순서대로 획득합니다.
그 위에서 `task` 툴이 별도 서브에이전트를 띄우기 때문에, 긴 리서치나 코드 생성이 메인 컨텍스트를 과도하게 오염시키지 않습니다.
그래서 실무에서 어떤 가치가 생기는지 보면, "에이전트가 한 번 답하고 끝"이 아니라 "긴 작업을 운영 가능한 단위로 분해해 다시 합치는 구조"를 바로 가져다 쓸 수 있습니다.

기술 스택도 분명합니다.
백엔드는 Python 3.12+, LangGraph, LangChain, FastAPI, uv를 쓰고 `backend/pyproject.toml`에는 MCP adapter, Slack SDK, Telegram Bot, Kubernetes, Firecrawl, Tavily까지 포함되어 있습니다.
프런트는 `frontend/package.json` 기준 Next.js 16, React 19, Tailwind CSS 4, Radix UI, LangGraph SDK를 사용합니다.
CI는 현재 `.github/workflows/backend-unit-tests.yml` 중심이고, 로컬 스캔 기준 `backend/tests/`에 27개 테스트 파일이 있으며 `skills/public/`에는 17개 공개 스킬 디렉터리가 들어 있습니다.

- **구현 근거 경로**
- `README.md`
- `backend/src/agents/lead_agent/agent.py`
- `backend/src/subagents/executor.py`
- `backend/src/tools/builtins/task_tool.py`
- `backend/src/agents/memory/updater.py`
- `backend/src/gateway/app.py`

## 2. 💡 이 프로젝트는 왜 필요한가요? (Why & Background)

- **문제 정의**: 단순 챗봇 UI가 아니라, 장시간 작업과 파일 생성까지 책임지는 에이전트 런타임이 필요합니다.
- **접근 방식**: DeerFlow는 리서치 프레임워크를 넘어 샌드박스, 메모리, 스킬, 채널을 한 하네스로 묶습니다.
- **실무 포인트**: 릴리즈 체계는 아직 약하지만, 운영 확장성은 매우 넓습니다.

기존의 많은 AI 앱 템플릿은 UI는 빨리 나오지만, 에이전트가 실제로 파일을 만지고 장시간 작업하고 외부 시스템과 이어지는 지점에서 급격히 얇아집니다.
README의 "From Deep Research to Super Agent Harness" 섹션은 바로 그 한계를 문제로 삼고, 연구 프레임워크를 범용 실행 하네스로 다시 정의합니다.
즉 사용자가 원하는 것은 "검색 잘하는 봇"이 아니라, 스킬과 샌드박스를 붙여 보고서, 웹페이지, 슬라이드, 코드 변경까지 이어지는 운영 기반이라는 판단입니다.
그래서 실무에서 어떤 가치가 생기는지 보면, PoC 단계에서 만든 에이전트 데모를 내부 툴이나 자동화 워크플로로 확장할 때 갈아엎을 부분이 줄어듭니다.

차별점은 기능 개수보다 결합 방식에 있습니다.
예를 들어 서브에이전트는 단순 병렬 호출이 아니라 background executor와 상태 추적을 갖고 있고, 메모리는 단순 벡터DB 연결 대신 사용자 요약과 사실 리스트를 JSON 구조로 보존하며, 샌드박스는 가상 경로 계약까지 같이 제공합니다.
`backend/src/client.py`의 임베디드 Python 클라이언트도 같은 런타임을 HTTP 서버 없이 재사용하게 해, "웹앱"과 "라이브러리"를 동시에 제공하는 구조를 만듭니다.
그래서 실무에서 어떤 가치가 생기는지 보면, SaaS형 서비스로 띄우든 내부 Python 자동화에 끼워 넣든 코어 로직을 이중으로 관리할 필요가 줄어듭니다.

운영 신호도 나쁘지 않습니다.
GitHub API 기준 저장소는 2026-03-09에도 업데이트가 있었고, open PR 28개 중 일부가 같은 날짜에 올라와 활발한 기여 흐름이 보입니다.
다만 같은 시점에 정식 releases와 tags가 비어 있어, 문서상 비전과 커뮤니티 열기는 강하지만 배포 버전 체계는 아직 느슨하다고 보는 편이 맞습니다.
따라서 도입 시 "최신 main을 바로 따라가도 되는가"보다는 "우리 팀이 고정할 커밋 SHA와 내부 검증 루틴이 있는가"가 더 중요합니다.

<img src="https://sf16-sg.tiktokcdn.com/obj/eden-sg/hubseh7bsbps/20251208-160108.png" alt="InfoQuest 통합 배너" style="max-width: 100%; width: 100%; height: auto; display: block;" />
*출처: README - InfoQuest*

## 3. 🎯 어떤 상황에서 쓰면 좋을까요? (Use Cases)

DeerFlow는 "자체 에이전트 제품을 만들고 싶은 팀"에게 더 잘 맞고, 단순 챗봇 래퍼가 필요한 팀에게는 다소 무거울 수 있습니다.
특히 파일 입출력, 장시간 작업, 내부 운영툴 연동, 모바일 채널 접점, 스킬 기반 반복 작업이 동시에 필요한 경우에 가치가 커집니다.
그래서 실무에서 어떤 가치가 생기는지 보면, 여러 오픈소스를 조합해 직접 런타임을 꿰매는 시간을 줄이고 제품 레이어에 더 빨리 올라갈 수 있습니다.

- **시나리오 1**
- 상황: 사내 리서치 어시스턴트가 웹 조사, 요약, 파일 생성, 슬라이드 초안까지 한 흐름으로 처리해야 합니다.
- 적용 방법: `README.md`의 Docker 빠른 시작으로 전체 스택을 띄우고, `skills/public/`의 리서치/프레젠테이션 계열 스킬을 활성화합니다.
- 기대 결과: 긴 작업을 서브에이전트로 분리하고 결과를 artifacts와 파일로 남겨, 단발성 답변보다 재사용 가능한 산출물 중심 흐름을 만들 수 있습니다.

- **시나리오 2**
- 상황: Slack이나 Telegram에서 팀원이 바로 에이전트에게 일을 던지고 싶습니다.
- 적용 방법: `config.yaml`의 `channels` 설정과 `backend/src/channels/service.py` 구조를 따라 Bot 토큰과 세션 정책을 채웁니다.
- 기대 결과: 공용 UI를 열지 않아도 메신저 채널별 기본 모델, recursion_limit, subagent 여부를 다르게 운영할 수 있습니다.

- **시나리오 3**
- 상황: AI 에이전트 기능을 제품에 임베드해야 하지만 별도 서버 운영은 피하고 싶습니다.
- 적용 방법: `backend/src/client.py`의 `DeerFlowClient`를 사용해 Python 프로세스 안에서 chat, stream, upload, skill 관리 기능을 호출합니다.
- 기대 결과: HTTP 게이트웨이와 거의 같은 응답 계약을 유지하면서도 배포 구성을 단순화할 수 있습니다.

반대로 "UI 예제 몇 개가 필요하다" 또는 "단일 모델 챗앱을 오늘 안에 띄우고 싶다" 정도라면 이 저장소는 과할 수 있습니다.
백엔드와 프런트가 모두 존재하고, 모델 설정, 도구 설정, 스킬 경로, 샌드박스 모드, 채널 옵션까지 알아야 하므로 초반 학습량이 분명히 있습니다.
그래서 실무에서 어떤 가치가 생기는지 보려면, 단기 속도보다 중장기 확장성과 운영 통제권이 더 중요한 팀인지 먼저 판단하는 편이 좋습니다.

## 4. 🚀 어떻게 사용하나요? (How to use)

가장 빠른 시작은 README가 안내하는 루트를 그대로 따르는 것입니다.
루트에서 `make config`로 `config.yaml`과 `.env`를 생성하고, 모델 API 키를 넣은 뒤 Docker 또는 로컬 개발 모드로 올리면 됩니다.
`make dev`는 LangGraph, Gateway, Frontend, Nginx를 한 번에 올리고 기본 접속 주소는 `http://localhost:2026`입니다.
그래서 실무에서 어떤 가치가 생기는지 보면, 각 서비스를 수동으로 연결하는 초기 부팅 비용이 낮고 아키텍처를 읽으면서 바로 손에 익힐 수 있습니다.

- **온라인 환경 빠른 시작**
```bash
git clone https://github.com/bytedance/deer-flow.git
cd deer-flow
make config
# config.yaml, .env 편집
make docker-init
make docker-start
```
- 모델은 `config.yaml`의 `models` 섹션에 OpenAI 호환 또는 LangChain provider 클래스로 등록합니다.
- 로컬 개발이라면 `make check`, `make install`, `make dev` 순서가 기본 경로입니다.

- **폐쇄망 환경 사용 가능 여부**
- 결론부터 말하면 "조건부 가능"입니다.
- 소스는 내부 Git 미러로 복제하고, Python 패키지는 내부 wheel 캐시 또는 사설 PyPI, 프런트는 내부 npm registry 또는 pnpm store 미러가 필요합니다.
- 샌드박스 이미지를 쓴다면 `make setup-sandbox` 전에 Docker 이미지를 내부 레지스트리로 미러링해야 합니다.
- 외부 API를 완전히 차단한 환경에서는 Tavily, Firecrawl, InfoQuest 같은 외부 검색 도구는 그대로 쓸 수 없고, 모델도 사내 OpenAI 호환 엔드포인트나 온프레미스 모델로 대체해야 합니다.
- `.env` 주입은 동일하지만 실제 값은 외부 SaaS 키 대신 내부 게이트웨이 URL과 내부 인증 정보로 바뀝니다.

- **폐쇄망 예시 절차**
```bash
git clone <internal-mirror>/bytedance/deer-flow.git
cd deer-flow
cp config.example.yaml config.yaml
# models.use는 유지하되 model/api_key/base_url을 내부 엔드포인트로 교체
uv sync --offline
cd frontend && pnpm install --offline
cd ..
make dev
```

온라인과 폐쇄망의 차이는 "기능 존재 여부"보다 "도구 생태계의 폭"에서 큽니다.
온라인에서는 웹 검색, 외부 MCP, 클라우드 모델을 쉽게 붙일 수 있지만, 폐쇄망에서는 내부 미러와 내부 모델이 준비돼야 동일한 경험이 나옵니다.
그래서 실무에서 어떤 가치가 생기는지 보자면, 아키텍처는 폐쇄망에도 이식 가능하지만 준비되지 않은 조직에서 즉시 도입하기에는 운영팀 협업이 필수입니다.

## 5. ✨ 사용하면 무엇이 좋아지나요? (Benefits)

- **제품화 속도 향상**: 멀티에이전트, 샌드박스, 업로드, 스킬, 채널, 메모리를 따로 붙일 필요가 없습니다.
- **실행 가능한 결과물 중심**: `artifacts`, 파일 출력, 채널 응답, 임베디드 클라이언트까지 있어 답변보다 산출물 전달에 강합니다.
- **확장 표면이 넓음**: MCP, 커스텀 스킬, Python 함수, OpenAI 호환 모델, 메신저 채널까지 확장 포인트가 많습니다.
- **운영 리스크도 같이 존재**: 릴리즈 태그 부재, 넓은 설정 표면, 외부 의존성, 프런트 검증 체계의 상대적 약함은 도입 전에 감수해야 합니다.

가장 큰 이득은 "실험용 에이전트"와 "운영 가능한 에이전트 제품" 사이의 틈을 메워준다는 점입니다.
특히 내부 자동화 도구를 만들 때는 파일시스템과 장시간 작업, 병렬 처리, 상태 유지, 배포 구조가 한꺼번에 필요해지는데, DeerFlow는 이 결합을 이미 해둔 상태로 제공합니다.
그래서 실무에서 어떤 가치가 생기는지 보면, 팀이 진짜 차별화해야 하는 도메인 스킬과 제품 경험에 집중할 시간을 벌어 줍니다.

주의할 점도 명확합니다.
현재 GitHub 운영 신호는 활발하지만 정식 릴리즈가 없으므로 버전 업그레이드는 semver보다 커밋 기준 검증이 현실적입니다.
또 `.github/workflows`에서 확인되는 자동 검증은 주로 백엔드 단위 테스트에 집중되어 있어, 프런트 변경이 많은 팀이라면 별도 CI 보강이 필요합니다.
그래서 실무에서 어떤 가치가 생기는지 역으로 해석하면, "강한 기반"은 제공하지만 "우리 조직용 안정화 층"은 여전히 사용자가 쌓아야 합니다.

에코시스템 관점에서는 공식 웹사이트, BytePlus InfoQuest 연계, Claude Code 연동 스킬, 17개 공개 스킬, 메신저 채널, MCP 구성이 모두 존재해 확장성은 분명히 좋습니다.
반면 이 넓은 표면은 곧 운영 복잡도이기도 하므로, 첫 도입은 `main` HEAD 추종보다 특정 커밋 고정과 최소 기능셋 구성으로 시작하는 편이 안전합니다.

- **도입 판단 한줄 정리**
- "에이전트를 진짜 제품처럼 운영하고 싶다면 매우 강력한 기반이지만, 안정적 배포를 원한다면 먼저 커밋 고정과 내부 검증 파이프라인을 같이 설계해야 하는 저장소"입니다.
