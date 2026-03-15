<div style="display:flex; flex-wrap:wrap; gap:10px; justify-content:center;">
  <a href="https://github.com/langchain-ai/deepagents" style="text-decoration:none;">
    <span style="display:inline-flex;align-items:center;gap:8px;padding:8px 14px;border-radius:999px;background:#111827;color:#ffffff;font-weight:700;">
      <span>Create Date</span>
      <span>2025-07-27</span>
    </span>
  </a>
  <a href="https://github.com/langchain-ai/deepagents/stargazers" style="text-decoration:none;">
    <span style="display:inline-flex;align-items:center;gap:8px;padding:8px 14px;border-radius:999px;background:#0f766e;color:#ffffff;font-weight:700;">
      <span>Star</span>
      <span>11,083</span>
    </span>
  </a>
  <a href="https://github.com/langchain-ai/deepagents/fork" style="text-decoration:none;">
    <span style="display:inline-flex;align-items:center;gap:8px;padding:8px 14px;border-radius:999px;background:#7c3aed;color:#ffffff;font-weight:700;">
      <span>Fork</span>
      <span>1,777</span>
    </span>
  </a>
</div>
<div style="display:flex; flex-wrap:wrap; gap:8px; justify-content:center; margin-top:12px;">
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#3776AB;color:#ffffff;font-weight:700;"><img src="_assets/siw-python.png" alt="Python" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Python</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#1C3C3C;color:#ffffff;font-weight:700;"><img src="_assets/siw-langchain.png" alt="LangChain" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">LangChain</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#0E1116;color:#ffffff;font-weight:700;"><img src="_assets/siw-langgraph.png" alt="LangGraph" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">LangGraph</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#191919;color:#ffffff;font-weight:700;"><img src="_assets/siw-anthropic.png" alt="Anthropic" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Anthropic</span></span>
</div>

<img src="https://raw.githubusercontent.com/langchain-ai/deepagents/main/.github/images/logo-light.svg" alt="deepagents 배너" style="max-width: 100%; width: 100%; height: auto; display: block;" />
*출처: README 배너*

langchain-ai/deepagents는 LangChain과 LangGraph 위에 "바로 실행 가능한 범용 에이전트 하네스"를 얹은 Python 중심 오픈소스다.
이 프로젝트의 핵심은 프롬프트, 툴, 컨텍스트 관리, 하위 에이전트 위임을 매번 손으로 조립하지 않아도 된다는 점이다.
README와 `examples/`를 보면 연구형 에이전트, Text-to-SQL, 콘텐츠 작성, 터미널 코딩 에이전트까지 같은 구조적 철학으로 확장한다.
코드 기준으로는 `libs/deepagents/deepagents/graph.py`의 `create_deep_agent()`가 기본 미들웨어 스택을 조립하고, `task` 도구로 하위 에이전트를 띄우며, 대화가 길어질 때는 요약과 파일 오프로딩으로 컨텍스트를 줄인다.
GitHub 운영 신호상 기본 브랜치는 `main`이고, 최신 SDK 릴리즈는 `deepagents==0.4.11`로 2026-03-13에 발행됐으며, 저장소는 2026-03-15에도 푸시가 이어지고 있다.
그래서 이 저장소는 "에이전트를 한번 만들어 보는 데모"보다 "반복 가능한 에이전트 실행 환경을 팀에 심는 도구상자"에 더 가깝다.

## 1. 🔍 핵심 기능 및 구현 방식 (Core Features & Implementation)

- **미들웨어 조립형 엔트리포인트**: `libs/deepagents/deepagents/graph.py`의 `create_deep_agent()`는 모델 해석, 기본 프롬프트 결합, `TodoListMiddleware`, `FilesystemMiddleware`, `SubAgentMiddleware`, `SummarizationMiddleware`, 프롬프트 캐싱, 툴 콜 패치를 한 번에 묶어 `create_agent(...).with_config(...)`로 반환한다.
- **하위 에이전트 위임 도구**: `libs/deepagents/deepagents/middleware/subagents.py`는 `task`라는 `StructuredTool`을 만들고, `_EXCLUDED_STATE_KEYS`로 부모 상태를 정리한 뒤 하위 에이전트에 새 `HumanMessage`만 넘겨 stateless하게 실행한다.
- **파일시스템과 실행 환경 추상화**: `libs/deepagents/deepagents/middleware/filesystem.py`는 `ls`, `read_file`, `write_file`, `edit_file`, `glob`, `grep`, `execute`를 내장 도구로 제공하고, 이미지 파일과 긴 파일을 위한 페이지네이션 규칙까지 포함한다.
- **컨텍스트 압축과 기억 보조**: `libs/deepagents/deepagents/middleware/summarization.py`는 오래된 메시지를 `/conversation_history/{thread_id}.md`에 오프로딩하고, `memory.py`와 `skills.py`는 `AGENTS.md`와 `SKILL.md`를 프롬프트에 주입해 장기 컨텍스트와 온디맨드 워크플로를 결합한다.

이 설계가 중요한 이유는 Deep Agents가 단순히 "툴 몇 개를 끼워 넣은 래퍼"가 아니라, 에이전트가 실제로 오래 일할 때 생기는 문제를 먼저 코드에 반영했기 때문이다.
예를 들어 `SubAgentMiddleware`는 복잡한 작업을 격리된 컨텍스트 창으로 넘기고, `SummarizationMiddleware`는 모델 프로필이 있으면 컨텍스트 창의 85% 근처에서 자동 압축을 시작하도록 기본값을 계산한다.
`SkillsMiddleware`는 여러 소스의 스킬 디렉터리를 순서대로 읽고, 나중 소스가 앞선 스킬을 덮어쓰는 구조라서 팀 공용 스킬과 프로젝트 스킬을 겹쳐 쓰기 좋다.
그래서 실무에서 어떤 가치가 생기는지로 번역하면, 개인별 프롬프트 노하우가 아니라 팀 차원의 작업 패턴을 재사용 가능한 미들웨어와 파일 규약으로 굳힐 수 있다는 뜻이다.

검증 체계도 얇지 않다.
`libs/deepagents/tests/` 아래에 71개 테스트 파일이 있고, `.github/workflows/ci.yml`은 `deepagents`, `cli`, `harbor`, `daytona`, `modal`, `runloop`, `quickjs` 패키지를 바뀐 범위에 맞춰 나눠 테스트한다.
특히 핵심 SDK와 CLI는 Python 3.11부터 3.14까지 매트릭스로 검사하도록 짜여 있어서, "빠르게 움직이지만 아무 검증 없이 배포하는 프로젝트"와는 결이 다르다.

<img src="https://raw.githubusercontent.com/langchain-ai/deepagents/main/libs/cli/images/cli.png" alt="Deep Agents CLI" style="max-width: 100%; width: 100%; height: auto; display: block;" />
*출처: README - Deep Agents CLI*

## 2. 💡 이 프로젝트는 왜 필요한가요? (Why & Background)

- **기존 방식의 문제**: LangGraph나 LangChain 자체는 강력하지만, 일반적인 팀 입장에서는 툴 정의, 파일 입출력, 실행 환경, 요약, 기억, 승인 흐름을 매번 직접 붙여야 한다.
- **Deep Agents의 접근**: README가 말하는 "batteries-included agent harness"처럼 자주 필요한 행동을 기본 제공하고, 필요할 때만 모델·툴·프롬프트를 바꾸는 구조를 택한다.
- **차별점**: `examples/deep_research/agent.py`, `examples/text-to-sql-agent/README.md`, `examples/content-builder-agent/content_writer.py`처럼 서로 다른 업무를 같은 뼈대로 구현한다는 점이 단순 샘플 모음과 다르다.

기존 방식의 문제는 기능 부족보다도 조합 비용에 있다.
에이전트 하나를 만들 때도 계획용 도구, 파일 저장소, 실행 권한, 긴 대화 처리, 역할 분리, 사람 승인 흐름이 동시에 얽히는데, 이런 부분은 데모에서는 가려져도 운영 단계에서 바로 비용으로 돌아온다.
Deep Agents는 이 조합 비용을 줄이기 위해 처음부터 "계획 + 파일시스템 + 서브에이전트 + 요약"을 기본값으로 묶어 둔다.
그래서 실무에서 어떤 가치가 생기는지로 보면, 팀이 새로운 업무 자동화를 시작할 때 매번 아키텍처를 제로부터 다시 설계하지 않아도 되는 출발점이 생긴다.

정량 성능 벤치마크 수치를 README 전면에 크게 내세우는 프로젝트는 아니다.
대신 `.github/workflows/evals.yml`이 매일 여러 모델 조합으로 평가를 돌리도록 설계돼 있고, 2026-03-13에 공개된 `deepagents==0.4.11` 릴리즈 노트에도 `MemoryAgentBench evaluation suite` 추가가 명시돼 있다.
즉, 이 프로젝트는 "우리가 빨라요"라는 마케팅 숫자보다, 다양한 모델과 실제 에이전트 행태를 계속 측정하는 운영 흐름을 더 중요하게 보는 편에 가깝다.

문서상 비전과 운영 신호를 분리해서 보면 더 명확하다.
문서상으로는 범용 에이전트 하네스를 표방하지만, GitHub 운영 신호상으로도 2026-03-15 기준 스타 11,083개, 포크 1,777개, 오픈 이슈 98개, 오픈 PR 71개, 최신 푸시 2026-03-15라는 수치가 확인된다.
활동성은 매우 높지만 `libs/deepagents/pyproject.toml`의 분류가 여전히 Beta이므로, 안정성보다 속도와 실험성을 우선하는 구간이 함께 존재한다고 보는 편이 정확하다.
그래서 도입 시에는 "활발해서 믿음직하다"와 "활발해서 변동성이 있다"를 동시에 받아들여야 한다.

## 3. 🎯 어떤 상황에서 쓰면 좋을까요? (Use Cases)

- **권장 팀 성격**: Python 기반으로 LangChain/LangGraph를 이미 쓰고 있거나, 에이전트형 워크플로를 사내 업무에 재사용 가능한 형태로 정착시키려는 팀에 잘 맞는다.
- **권장 환경**: 단발성 챗봇보다 파일 생성, 조사, 승인, 반복 실행처럼 상태와 작업 흔적이 남아야 하는 환경에 더 적합하다.

이 프로젝트의 유스케이스가 넓어 보이는 이유는 "모든 일을 잘해서"라기보다, 에이전트 작업을 반복 가능하게 만드는 공통 뼈대를 제공하기 때문이다.
README와 예제 디렉터리를 같이 보면 리서치, 데이터 질의, 콘텐츠 생성, 코딩 보조가 전부 같은 하네스 위에서 움직인다.
그래서 실무에서 어떤 가치가 생기는지로 보면, 업무별 에이전트를 따로따로 만들지 않고 공통 실행 규약을 공유하는 포트폴리오를 만들 수 있다.

<img src="https://raw.githubusercontent.com/langchain-ai/deepagents/main/examples/ralph_mode/ralph_mode_diagram.png" alt="Ralph Mode Diagram" style="max-width: 100%; width: 100%; height: auto; display: block;" />
*출처: examples/ralph_mode/README.md - Ralph Mode Diagram*

- **시나리오 1. 리서치 팀이 기술 동향이나 경쟁사 조사를 자동화할 때.**
상황: 질문 하나에 여러 자료 수집과 비교가 필요한 경우다.
적용 방법: `examples/deep_research/agent.py`처럼 `tavily_search`, `think_tool`, `research-agent` 서브에이전트를 묶어 조사 단위를 병렬화한다.
기대 결과: 메인 에이전트는 전체 구조를 조정하고, 서브에이전트는 토픽별 검색과 정리에 집중하므로 긴 조사 작업에서도 맥락이 덜 엉킨다.

- **시나리오 2. 데이터 팀이 자연어 질의를 SQL 분석으로 연결할 때.**
상황: 비개발자가 "어느 직원이 가장 많은 매출을 냈나" 같은 질문을 던지는 환경이다.
적용 방법: `examples/text-to-sql-agent/README.md`가 보여주듯 `write_todos`, 스키마 탐색 스킬, 쿼리 작성 스킬, SQLite 도구를 결합한다.
기대 결과: 단순 질의응답보다 한 단계 나아가, 계획 세우기와 스키마 확인을 거친 뒤 안전하게 쿼리를 조합하는 흐름을 만들 수 있다.

- **시나리오 3. 콘텐츠 팀이나 1인 운영자가 초안 작성과 리서치를 묶고 싶을 때.**
상황: 브랜드 톤, 리서치, 산출물 저장 위치를 한 번에 관리해야 하는 경우다.
적용 방법: `examples/content-builder-agent/content_writer.py`는 `memory=["./AGENTS.md"]`, `skills=["./skills/"]`, `subagents=load_subagents(...)` 조합으로 브랜드 보이스와 리서치 워크플로를 분리한다.
기대 결과: 초안 품질을 올리는 것뿐 아니라, 글쓰기 기준과 산출물 구조를 파일 단위로 남겨 팀 자산화하기 쉬워진다.

추가로 `examples/ralph_mode/README.md`는 매 루프를 새 컨텍스트로 시작하는 자율 루프 패턴까지 보여 준다.
이는 "대화를 계속 이어 가는 에이전트"만이 아니라 "파일과 Git을 기억 장치로 삼는 반복 실행형 에이전트"도 충분히 설계 가능하다는 뜻이다.
그래서 도입 전 유스케이스를 고를 때는 챗봇 여부보다, 결과물이 파일과 작업 이력으로 남는가를 기준으로 보면 적합성을 더 잘 판단할 수 있다.

## 4. 🚀 어떻게 사용하나요? (How to use)

- **온라인 환경에서 가장 빠른 시작 방법**: SDK는 `pip install deepagents==0.4.11` 또는 `uv add deepagents==0.4.11`로 바로 시작할 수 있다.
- **CLI가 필요할 때**: `libs/cli/README.md` 기준 설치 스크립트나 `uv tool install 'deepagents-cli[anthropic,groq]'` 방식으로 터미널 에이전트까지 바로 확장할 수 있다.

가장 짧은 시작 예시는 아래 정도면 충분하다.

```bash
pip install deepagents==0.4.11
```

```python
from deepagents import create_deep_agent

agent = create_deep_agent()
result = agent.invoke(
    {
        "messages": [
            {"role": "user", "content": "Research LangGraph and write a summary"}
        ]
    }
)
print(result["messages"][-1].content)
```

모델을 바꾸고 싶다면 `create_deep_agent(model="openai:gpt-5")`처럼 provider:model 형식을 쓰거나, LangChain 모델 객체를 직접 넘기면 된다.
이 방식이 좋은 이유는 Deep Agents가 특정 벤더에 묶인 프레임워크가 아니라, LangChain 모델 생태계를 전제로 한 하네스이기 때문이다.
그래서 실무에서 어떤 가치가 생기는지로 보면, 팀이 모델 공급자를 바꿔도 작업 패턴 자체는 최대한 유지할 수 있다.

- **폐쇄망(인터넷 불가) 환경 사용 가능 여부**: 조건부 가능하다.
- **폐쇄망 권장 절차**: 내부 Git 미러 또는 내부 아티팩트 저장소에 소스와 wheel을 미리 반입하고, 모델 접근도 내부에서 허용된 엔드포인트로 맞춰야 한다.

폐쇄망에서는 온라인과 접근법이 다르다.
온라인은 `pip install`, 외부 문서, Tavily 검색, 원격 샌드박스(Modal/Daytona/Runloop)를 곧바로 사용할 수 있지만, 폐쇄망은 패키지와 모델 경로를 먼저 조직 인프라에 맞게 재배치해야 한다.
가장 현실적인 절차는 2026-03-13 릴리즈 `deepagents==0.4.11`의 wheel 또는 tarball을 내부 아티팩트 저장소에 올리고, `langchain`, `langgraph`, `langchain-anthropic` 같은 의존성을 사내 PyPI 미러나 wheelhouse로 함께 준비하는 방식이다.

예시는 이렇게 잡는 편이 안전하다.

```bash
# 내부 Git 미러가 있을 때
git clone --branch main <internal-mirror>/langchain-ai/deepagents.git

# 또는 내부 wheelhouse가 있을 때
pip install --no-index --find-links /opt/wheelhouse deepagents==0.4.11
```

비밀값 주입은 `.env`를 로컬에 두기보다 사내 비밀 저장소나 배포 시 환경 변수 주입 방식으로 처리하는 편이 낫다.
예제 중 `deep_research`는 `TAVILY_API_KEY`, `content-builder-agent`는 `GOOGLE_API_KEY`, `text-to-sql-agent`는 데이터베이스 파일과 모델 키가 필요하므로, 외부 API를 못 쓰는 망에서는 내부 대체 수단이 없으면 그대로는 재현이 어렵다.
반대로 코어 SDK 자체는 LangChain 호환 모델 객체를 직접 받을 수 있으니, 내부 게이트웨이 또는 온프레미스 LLM이 tool calling을 지원하면 기본 흐름은 유지할 수 있다.

## 5. ✨ 사용하면 무엇이 좋아지나요? (Benefits)

- **개발 효율**: 계획, 파일 조작, 요약, 위임을 기본 제공하므로 에이전트 프로토타입에서 운영형 구조로 넘어가는 시간이 짧아진다.
- **유지보수성**: `AGENTS.md`, `SKILL.md`, 예제 구조처럼 사람이 읽을 수 있는 파일 규약 위에 기능을 얹어서, 프롬프트 로직이 코드와 함께 관리된다.
- **확장성**: CLI, ACP(`libs/acp/README.md`), MCP 어댑터, 원격 샌드박스 파트너 패키지(`libs/partners/*`)까지 이어져서 단일 SDK로 끝나지 않는다.

가장 큰 이득은 "한 번 만든 에이전트를 다시 만들기 쉬워진다"는 점이다.
실험 단계에서는 대개 프롬프트 한 장과 툴 몇 개로도 충분하지만, 실제 업무에 넣는 순간 승인 흐름, 장기 맥락, 파일 출력, 역할 분리가 동시에 필요해진다.
Deep Agents는 이런 운영 요소를 아예 초기에 모델링해 두어서, 실험 코드가 곧장 작업용 에이전트 골격으로 이어질 가능성을 높인다.
그래서 실무에서 어떤 가치가 생기는지로 보면, 팀이 새 자동화 과제를 시작할 때마다 "이번엔 어떤 프롬프트 조합을 쓰지"보다 "어떤 미들웨어와 스킬을 재사용하지"를 먼저 떠올리게 만든다.

다만 트레이드오프도 분명하다.
`libs/deepagents/deepagents/backends/local_shell.py`가 직접 경고하듯 `LocalShellBackend`는 로컬 호스트에서 아무 제약 없이 셸 명령을 실행하므로, 사람 승인이나 원격 샌드박스 없이 쓰기엔 위험하다.
저장소 자체도 Beta 단계라서, 활동성이 높은 만큼 API와 동작 세부가 빨리 바뀔 수 있고, 오픈 이슈 98개와 오픈 PR 71개는 강한 커뮤니티 신호이면서 동시에 빠른 변화의 신호이기도 하다.
또한 멋진 예제 상당수가 외부 API와 네트워크 접근을 전제로 하므로, 비용 통제와 네트워크 정책을 함께 설계하지 않으면 "기능은 좋은데 우리 환경에 안 맞네"가 될 수 있다.

에코시스템 관점에서는 꽤 매력적이다.
CLI는 대화 재개, 웹 검색, 승인 흐름, 헤드리스 실행을 제공하고, ACP 통합은 Zed 같은 에디터 안으로 들어갈 수 있게 해 주며, 파트너 패키지는 Modal·Runloop·Daytona 원격 샌드박스까지 연결한다.
즉 Deep Agents는 단순 SDK라기보다 "에이전트 실행 경험을 둘러싼 운영 표면"을 넓혀 가는 프로젝트다.
버전 고정만 잘하면 도입 후의 확장 여지도 큰 편이다.

**도입 판단 한줄 정리:** Python과 LangChain 스택에서 범용 에이전트를 빠르게 제품화하려는 팀에게는 매우 강한 출발점이지만, 보안 설계와 버전 고정, 외부 모델 의존성을 함께 관리할 준비가 되어 있을 때 가장 빛난다.
