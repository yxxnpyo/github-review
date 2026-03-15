<div style="display:flex; flex-wrap:wrap; gap:10px; justify-content:center;">
  <a href="https://github.com/langchain-ai/langchain-skills" style="text-decoration:none;">
    <span style="display:inline-flex;align-items:center;gap:8px;padding:8px 14px;border-radius:999px;background:#111827;color:#ffffff;font-weight:700;">
      <span>Create Date</span>
      <span>2026-01-22</span>
    </span>
  </a>
  <a href="https://github.com/langchain-ai/langchain-skills/stargazers" style="text-decoration:none;">
    <span style="display:inline-flex;align-items:center;gap:8px;padding:8px 14px;border-radius:999px;background:#0f766e;color:#ffffff;font-weight:700;">
      <span>Star</span>
      <span>368</span>
    </span>
  </a>
  <a href="https://github.com/langchain-ai/langchain-skills/fork" style="text-decoration:none;">
    <span style="display:inline-flex;align-items:center;gap:8px;padding:8px 14px;border-radius:999px;background:#7c3aed;color:#ffffff;font-weight:700;">
      <span>Fork</span>
      <span>36</span>
    </span>
  </a>
</div>
<div style="display:flex; flex-wrap:wrap; gap:8px; justify-content:center; margin-top:12px;">
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#1C3C3C;color:#ffffff;font-weight:700;"><img src="_assets/siw-langchain.png" alt="LangChain" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">LangChain</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#0E1116;color:#ffffff;font-weight:700;"><img src="_assets/siw-langgraph.png" alt="LangGraph" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">LangGraph</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#CB3837;color:#ffffff;font-weight:700;"><img src="_assets/siw-npm.png" alt="npm" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">npm</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#000000;color:#ffffff;font-weight:700;"><img src="_assets/siw-markdown.png" alt="Markdown" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Markdown</span></span>
</div>

langchain-ai/langchain-skills는 실행 엔진을 새로 만드는 저장소라기보다, LangChain 생태계에서 에이전트를 만들 때 바로 불러 쓸 수 있는 `SKILL.md` 묶음을 배포하는 콘텐츠 레포지토리다.
README 기준으로는 Claude Code, Cursor, Windsurf, Deep Agents CLI 같은 Agent Skills 사양 호환 도구에 설치할 수 있고, 실제 파일 구조도 `config/skills/*/SKILL.md`와 설치 스크립트 중심으로 매우 단순하다.
핵심은 LangChain, LangGraph, Deep Agents를 경쟁 프레임워크로 보지 않고 계층형 선택지로 정리해 준다는 점이다.
실제로 `framework-selection`, `deep-agents-core`, `langchain-rag`, `langgraph-human-in-the-loop` 같은 스킬은 개념 설명보다 "언제 이걸 써야 하는지"와 "바로 가져다 쓸 코드"를 같이 넣는다.
GitHub 운영 신호상 기본 브랜치는 `main`, 저장소 생성일은 2026-01-22, 최신 릴리즈는 `v0.1.0`이며 2026-03-11까지 커밋이 이어졌고 현재 오픈 이슈와 오픈 PR은 모두 0개다.
그래서 이 프로젝트는 기능이 많은 소프트웨어라기보다, 빠르게 변하는 LangChain 계열 실전 패턴을 스킬 형태로 유통하는 "지식 패키지"로 이해하는 편이 정확하다.

## 1. 🔍 핵심 기능 및 구현 방식 (Core Features & Implementation)

- **스킬 콘텐츠 저장소 구조**: `config/skills/` 아래에 11개 디렉터리가 있고, 각각 `SKILL.md` 하나를 중심으로 지식 단위를 나눈다.
- **설치 대상 분기 스크립트**: `install.sh`는 `--claude`, `--deepagents`, `--global`, `--force` 플래그를 해석해 설치 위치를 달리하고, Deep Agents 전역 설치일 때만 `config/AGENTS.md`를 함께 복사한다.
- **에이전트별 배포 메타데이터**: `.claude-plugin/plugin.json`과 `.claude-plugin/marketplace.json`은 Claude Code 플러그인 형태로 이 저장소를 마켓플레이스에서 곧바로 설치할 수 있게 만든다.
- **실전 레퍼런스형 스킬 본문**: `config/skills/framework-selection/SKILL.md`, `config/skills/deep-agents-core/SKILL.md`, `config/skills/langchain-rag/SKILL.md`, `config/skills/langgraph-human-in-the-loop/SKILL.md`는 설명과 예제 코드를 함께 넣어 "읽는 문서"이면서 동시에 "불러오는 작업 지침" 역할을 한다.

이 저장소의 중요한 포인트는 구현 복잡도가 낮다는 점 자체에 있다.
`pyproject.toml`을 보면 패키지명과 버전만 정의돼 있고 실제 Python 패키지는 비워져 있으며, 실행 로직의 대부분은 `install.sh`의 파일 복사와 스킬 본문 콘텐츠에 들어 있다.
즉, 여기서의 핵심 기능은 런타임 코드가 아니라 "어떤 상황에 어떤 프레임워크와 패턴을 선택해야 하는지"를 에이전트가 바로 읽을 수 있는 형식으로 정리하는 것이다.
그래서 실무에서 어떤 가치가 생기는지로 보면, 팀이 매번 LangChain 문서 전체를 훑지 않아도 특정 작업에 맞는 축약된 작업 매뉴얼을 에이전트에게 직접 주입할 수 있다.

대표적으로 `framework-selection`은 LangChain, LangGraph, Deep Agents를 선택 문제처럼 단순 비교하지 않고 계층 구조로 설명한다.
`deep-agents-core`는 `create_deep_agent()` 설정 예제와 `SKILL.md` 형식을 함께 보여 주어, Deep Agents를 처음 만지는 사람에게 빠른 출발점을 제공한다.
`langchain-rag`는 로더, 스플리터, 임베딩, 벡터스토어를 한 번에 이어 붙인 파이프라인 예제를 제공하고, `langgraph-human-in-the-loop`는 `interrupt()`와 `Command(resume=...)` 같은 실제 함정을 짚는다.
즉, 기능 이름보다 "잘 틀리는 지점까지 같이 패키징해 둔 지식"이 이 레포의 실질적인 코어다.

## 2. 💡 이 프로젝트는 왜 필요한가요? (Why & Background)

- **해결하려는 문제**: LangChain 계열 생태계는 빠르게 진화하지만, 초보자나 실무자는 "지금 어떤 레이어를 써야 하는가"와 "최신 패턴은 무엇인가"에서 자주 막힌다.
- **프로젝트의 접근**: README가 말하듯 Agent Skills 사양에 맞춘 설치 가능한 스킬 묶음으로 지식을 패키징해서, 문서 탐색을 에이전트 친화적 워크플로로 바꾼다.
- **차별점**: 일반 튜토리얼 문서가 아니라 Claude Code 플러그인, `npx skills`, Deep Agents CLI 설치 스크립트까지 연결해 실제 도구 안으로 들어가게 한다.

기존 방식의 문제는 자료가 없어서가 아니라, 자료가 너무 넓고 흩어져 있다는 데 있다.
LangChain, LangGraph, Deep Agents는 서로 연결돼 있는데도 입문자는 종종 별개의 선택지처럼 받아들이고, 그 결과 불필요하게 낮은 레이어부터 조립하거나 반대로 너무 높은 레이어를 무리하게 쓴다.
`framework-selection` 스킬은 바로 이 지점을 겨냥해서, 질문 순서에 따라 어느 프레임워크 레이어를 선택해야 하는지 결정 가이드를 제공한다.
그래서 실무에서 어떤 가치가 생기는지로 보면, 팀이 새 프로젝트를 시작할 때 프레임워크 선택 실수와 온보딩 시간을 같이 줄일 수 있다.

이 프로젝트의 또 다른 차별점은 "지식 저장소"에서 멈추지 않고 설치 경험까지 제품화했다는 점이다.
README에는 `npx skills add langchain-ai/langchain-skills --skill '*' --yes` 방식과 Claude Code 플러그인 설치 흐름이 함께 있고, `install.sh`는 현재 디렉터리용 로컬 설치와 전역 설치를 분리한다.
즉 단순 문서 링크 모음이 아니라, 실전 개발 도구 안에서 불러 쓸 수 있는 지식 팩으로 유통된다.
운영 신호상으로도 2026-03-09에 `v0.1.0`이 릴리즈됐고, 2026-03-11에는 `eval()` 예제를 안전한 `add` 툴 예제로 교체하는 보안성 관련 수정이 반영됐다.
문서상 비전과 실제 운영 상태가 비교적 잘 맞아떨어지는 편이다.

정량 벤치마크를 내세우는 저장소는 아니다.
대신 스킬 내용이 RAG, HITL, Deep Agents 아키텍처처럼 고비용 실수를 줄이는 레퍼런스에 집중돼 있고, README의 경고처럼 아직 early development 단계라 API와 내용이 바뀔 수 있음을 공개적으로 밝힌다.
이건 성능 경쟁형 저장소라기보다, 최신 모범 사례를 짧은 주기로 정리하는 지식 저장소라는 뜻이다.

## 3. 🎯 어떤 상황에서 쓰면 좋을까요? (Use Cases)

- **권장 팀 성격**: LangChain 계열 툴을 이미 쓰거나, 에이전트 개발을 시작했지만 프레임워크 선택과 패턴 정리가 자주 흔들리는 팀에 잘 맞는다.
- **권장 환경**: Claude Code, Deep Agents CLI, Agent Skills 호환 코딩 에이전트를 내부 표준 도구로 쓰는 환경에서 특히 유용하다.

이 저장소는 코드 생성기라기보다 "에이전트가 먼저 읽고 따라야 할 플레이북"에 가깝다.
그래서 가장 잘 맞는 곳은 새로운 기능 하나를 만드는 순간보다, 여러 프로젝트에서 같은 실수를 반복하고 있을 때다.
실무에서 어떤 가치가 생기는지로 보면, 팀의 암묵지(누가 알던 최신 패턴)를 재설치 가능한 공개 자산으로 바꾸는 데 도움이 된다.

- **시나리오 1. 팀이 새 에이전트 프로젝트의 기술 선택으로 자주 흔들릴 때.**
상황: LangChain으로 충분한지, LangGraph가 필요한지, Deep Agents로 바로 가야 하는지 논쟁이 반복된다.
적용 방법: `config/skills/framework-selection/SKILL.md`를 먼저 로드해 결정 표와 프레임워크 프로필을 기준으로 선택한다.
기대 결과: 설계 토론이 취향 싸움보다 요구사항 기준 분류로 바뀌고, 이후 어떤 스킬을 이어서 불러야 할지도 자연스럽게 정리된다.

- **시나리오 2. Deep Agents 기반 작업형 에이전트를 빠르게 세팅해야 할 때.**
상황: 파일 관리, 스킬, 메모리, 서브에이전트까지 포함된 작업형 에이전트를 빨리 만들어야 한다.
적용 방법: `config/skills/deep-agents-core/SKILL.md`의 `create_deep_agent()` 예제와 `config/AGENTS.md`의 우선 호출 규칙을 같이 사용한다.
기대 결과: 추상 개념 설명 대신 바로 동작 가능한 설정 조합을 참조하게 되어, 초기 시행착오가 줄어든다.

- **시나리오 3. RAG나 승인 워크플로 같은 특정 패턴을 짧게 재사용하고 싶을 때.**
상황: 전체 문서를 읽기엔 시간이 없고, 당장 필요한 패턴만 정확히 넣고 싶다.
적용 방법: `langchain-rag`나 `langgraph-human-in-the-loop` 스킬만 선택적으로 로드해 예제 코드와 주의사항을 바로 참조한다.
기대 결과: 문서 탐색 시간이 줄고, 특히 `interrupt()` 재시작 같은 함정을 놓칠 가능성이 낮아진다.

추가로 이 저장소는 에이전트별 설치 방식 차이를 흡수하는 데에도 쓸모가 있다.
한 팀 안에서 Claude Code와 Deep Agents CLI를 혼용하더라도, `install.sh`와 플러그인 메타 파일 덕분에 같은 스킬 세트를 여러 도구에 비슷한 경험으로 배포할 수 있다.
그래서 실무에서 어떤 가치가 생기는지로 보면, 지식 표준화와 설치 표준화를 동시에 묶어 갈 수 있다.

## 4. 🚀 어떻게 사용하나요? (How to use)

- **가장 빠른 시작 방법**: README 기준으로는 `npx skills`를 이용한 설치가 가장 빠르다.
- **Claude Code 직접 설치**: 플러그인 마켓플레이스 명령으로 바로 붙일 수 있다.
- **Deep Agents CLI 설치**: 저장소를 clone한 뒤 `install.sh --deepagents` 또는 `--deepagents --global`을 사용한다.

온라인 환경에서는 아래 흐름이 가장 간단하다.

```bash
npx skills add langchain-ai/langchain-skills --skill '*' --yes
```

Claude Code를 쓰고 있다면:

```bash
/plugin marketplace add langchain-ai/langchain-skills
/plugin install langchain-skills@langchain-skills
```

Deep Agents CLI용으로는:

```bash
git clone --branch main https://github.com/langchain-ai/langchain-skills.git
cd langchain-skills
./install.sh --deepagents --global
```

설치 후에는 README와 `config/AGENTS.md`가 안내하듯 `OPENAI_API_KEY`, `ANTHROPIC_API_KEY` 같은 모델 키를 잡아야 실제 코딩 에이전트 안에서 스킬을 활용할 수 있다.
이 점이 중요하다.
이 저장소 자체는 모델을 실행하지 않지만, 실제 효용은 결국 스킬을 읽는 에이전트가 있어야 나타난다.
그래서 실무에서 어떤 가치가 생기는지로 보면, 도입 비용은 낮지만 전제 조건은 분명한 편이다.

- **폐쇄망(인터넷 불가) 환경 사용 가능 여부**: 조건부 가능하다.
- **폐쇄망 절차**: 저장소 자체는 단순 파일 복사형이라 오프라인 반입이 쉽지만, 이를 읽을 에이전트와 모델 런타임까지 완전히 오프라인인지가 관건이다.

폐쇄망에서는 두 층으로 나눠 생각하는 편이 좋다.
첫째, `langchain-skills` 저장소 자체는 Git 미러나 zip 반입만 있으면 충분하다.
둘째, 이 스킬을 소비하는 Claude Code, Deep Agents CLI, 또는 내부 에이전트 런타임이 어떤 모델 공급자와 연결되는지가 별도 문제다.

실행 절차는 대체로 아래와 같다.

```bash
# 내부 Git 미러 사용
git clone --branch main <internal-mirror>/langchain-ai/langchain-skills.git
cd langchain-skills
./install.sh --deepagents --global
```

또는 압축본 반입 후:

```bash
unzip langchain-skills-main.zip
cd langchain-skills-main
./install.sh --claude --global
```

`.env` 대신 사내 비밀 저장소나 셸 프로파일로 API 키를 주입하는 편이 낫고, 외부 모델 API를 못 쓰는 망이라면 이 스킬 저장소만 가져와서는 완전한 사용이 어렵다.
반대로 내부 LLM 게이트웨이와 Agent Skills 호환 런타임이 이미 있다면, 이 저장소는 거의 그대로 가져가도 된다.
즉 온라인과 폐쇄망의 차이는 스킬 콘텐츠보다 모델 연결성에서 더 크게 난다.

## 5. ✨ 사용하면 무엇이 좋아지나요? (Benefits)

- **온보딩 속도 개선**: 초보자도 "무슨 문서를 읽지?"보다 "어떤 스킬을 먼저 불러오지?"로 시작할 수 있다.
- **지식 재사용성**: 프레임워크 선택, RAG, HITL, Deep Agents 구성처럼 자주 반복되는 패턴을 설치 가능한 단위로 재사용한다.
- **도구 간 확장성**: `npx skills`, Claude Code 플러그인, Deep Agents CLI 스크립트를 함께 제공해 같은 지식을 여러 에이전트 인터페이스에 재배포할 수 있다.

가장 큰 장점은 문서 소비 방식을 바꾼다는 점이다.
일반 문서는 사람이 브라우저에서 검색해 읽어야 하지만, 이 저장소의 스킬은 에이전트가 먼저 읽고 작업 흐름에 반영하도록 설계돼 있다.
`config/AGENTS.md`가 "코드를 쓰기 전에 먼저 스킬을 호출하라"고 못 박는 이유도 여기에 있다.
그래서 실무에서 어떤 가치가 생기는지로 보면, 팀의 좋은 패턴이 개인의 기억이 아니라 에이전트의 기본 행동으로 스며들 수 있다.

주의할 점도 있다.
첫째, 이 저장소에는 테스트 디렉터리, CI 워크플로, 예제 실행 코드가 사실상 없어서 품질 보증의 무게중심이 자동 검증보다 콘텐츠 리뷰에 가깝다.
둘째, early development 경고가 명시돼 있으므로 스킬 내용이 빠르게 바뀔 수 있고, 지금 기준 최신 릴리즈도 `v0.1.0`으로 아직 초기 단계다.
셋째, 스킬 본문이 길고 풍부한 대신 실제 프로젝트 맥락과 정확히 맞는지는 각 팀이 추가 검토해야 한다.
즉 "설치하면 끝"이 아니라 "좋은 출발점을 설치한다"에 가깝다.

운영 신호는 오히려 긍정적이다.
오픈 이슈와 오픈 PR이 모두 0개이고, 2026-03-11까지 최근 커밋이 이어졌으며, 릴리즈도 2026-03-09에 한 번 정리돼 있다.
규모는 아직 작지만 유지보수 부담이 폭주한 상태는 아니다.
버전 고정과 내부 보강 스킬만 병행하면 팀 표준 플레이북 저장소로 쓰기 좋다.

**도입 판단 한줄 정리:** LangChain 계열 에이전트 개발에서 "최신 모범 사례를 에이전트가 먼저 읽게 만들고 싶다"면 매우 실용적인 저장소지만, 자동 검증보다는 콘텐츠 품질과 팀별 커스터마이징 역량에 더 의존한다.
