<div style="display:flex; flex-wrap:wrap; gap:10px; justify-content:center;">
  <a href="https://github.com/anthropics/knowledge-work-plugins" style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:999px;background:#111827;color:#ffffff;text-decoration:none;font-weight:700;"><span>Create Date</span><span>2026-02-25</span></a>
  <a href="https://github.com/anthropics/knowledge-work-plugins/stargazers" style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:999px;background:#f59e0b;color:#111827;text-decoration:none;font-weight:700;"><span>Star</span><span>7.8k</span></a>
  <a href="https://github.com/anthropics/knowledge-work-plugins/fork" style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:999px;background:#2563eb;color:#ffffff;text-decoration:none;font-weight:700;"><span>Fork</span><span>803</span></a>
</div>
<div style="display:flex; flex-wrap:wrap; gap:8px; justify-content:center; margin-top:10px;">
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#191919;color:#ffffff;font-weight:700;"><img src="_assets/siw-anthropic.png" alt="Anthropic" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Anthropic</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#2A3A5E;color:#ffffff;font-weight:700;"><img src="_assets/siw-modelcontextprotocol.png" alt="MCP" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">MCP</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#000000;color:#ffffff;font-weight:700;"><img src="_assets/siw-markdown.png" alt="Markdown" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Markdown</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#000000;color:#ffffff;font-weight:700;"><img src="_assets/siw-json.png" alt="JSON" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">JSON</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#3776AB;color:#ffffff;font-weight:700;"><img src="_assets/siw-python.png" alt="Python" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Python</span></span>
</div>

anthropics/knowledge-work-plugins는 Claude를 직무별 전문가로 빠르게 바꾸기 위한 플러그인 컬렉션이다.
핵심 구현은 코드 프레임워크가 아니라 Markdown+JSON 중심의 파일 구조라서 도입 속도가 매우 빠르다.
실제 저장소에는 플러그인 매니페스트 19개, 커맨드 79개, 스킬 91개, 에이전트 5개가 포함되어 있어 템플릿 수준을 넘어선 운영형 레퍼런스에 가깝다.
특히 `.mcp.json`로 외부 도구 연결을 분리하고 `commands/`와 `skills/`를 분리해 역할별 반복 업무를 재사용 가능한 방식으로 구조화했다.
조회 시점 기준 GitHub 화면에는 Star 7.8k, Fork 803으로 노출되어 커뮤니티 검증도 어느 정도 진행된 상태다.

## 1. 🔍 핵심 기능 및 구현 방식 (Core Features & Implementation)
- **역할별 플러그인 패키징과 공통 골격.**
각 플러그인은 `.claude-plugin/plugin.json` + `.mcp.json` + `commands/` + `skills/`를 공통 구조로 사용한다.
루트 README의 구조 설명과 실제 폴더 구성이 일치해 학습 비용이 낮다.
예를 들어 `finance/.claude-plugin/plugin.json`은 도메인 목적을 선언하고 `finance/.mcp.json`은 Snowflake, Databricks, Slack, Microsoft 365 같은 연결점을 독립 관리한다.
이 구조는 실무에서 어떤 가치가 생기냐면 도메인 로직 수정과 커넥터 교체를 분리해 운영 중 변경 리스크를 줄여준다는 점이다.

- **명령(Command)과 지식(Skill)의 분리 설계.**
`finance/commands/reconciliation.md`는 `/recon <account> <period>`처럼 즉시 실행 가능한 워크플로를 정의한다.
같은 도메인의 `finance/skills/reconciliation/SKILL.md`는 GL-Subledger 비교, Aging 기준, Escalation 임계치 같은 장기 지식을 담아 재사용성을 높인다.
`engineering/commands/debug.md`도 재현-격리-진단-수정 4단계를 고정해 문제 해결 편차를 줄인다.
실무에서 어떤 가치가 생기냐면 개인 숙련도에 따라 흔들리던 절차를 팀 공통 인터페이스로 표준화할 수 있다는 점이다.

- **메타 플러그인으로 생성/커스터마이징 자동화.**
`cowork-plugin-management/skills/create-cowork-plugin/SKILL.md`는 Discovery→Planning→Design→Implementation→Package 5단계를 명시해 신규 플러그인 제작 절차를 제품화했다.
`cowork-plugin-management/skills/cowork-plugin-customizer/SKILL.md`는 `~~` 플레이스홀더 기반의 조직 맞춤 치환과 MCP 연결 정리를 다룬다.
여기에 `finance/CONNECTORS.md` 같은 문서가 툴 카테고리 추상화(`~~data warehouse`)를 보완한다.
실무에서 어떤 가치가 생기냐면 플러그인 자체를 다시 만들지 않고도 조직별 변형을 빠르게 반복할 수 있다는 점이다.

- **일부 도메인은 실제 스크립트 실행까지 포함.**
`data/skills/data-context-extractor/scripts/package_data_skill.py`는 SKILL frontmatter 검증 후 zip 패키징까지 수행한다.
`bio-research/skills/instrument-data-to-allotrope/scripts/convert_to_asm.py`는 장비 타입 탐지, 신뢰도 임계치, fallback 파서, provenance 메타데이터, 임시 파일 기반 원자적 저장까지 구현한다.
즉 이 저장소는 단순 프롬프트 모음이 아니라 운영 자동화 스크립트 샘플도 함께 제공한다.
실무에서 어떤 가치가 생기냐면 규제/재현성이 중요한 분야에서도 최소 실행 체인을 바로 가져다 쓸 수 있다는 점이다.

이 레포의 구현 포인트는 "역할별 지식 캡슐화"와 "연결 계층 분리"를 파일 단위로 강제한다는 데 있다.
그래서 팀이 커져도 플러그인 단위로 책임을 분리하기 쉽고, 신규 팀원 온보딩도 빠르다.

## 2. 💡 이 프로젝트는 왜 필요한가요? (Why & Background)
기존 조직에서는 프롬프트, 체크리스트, 도구 연결 방법이 사람마다 흩어져 있어 같은 업무를 반복해도 산출물 품질이 들쭉날쭉해진다.
게다가 부서마다 필요한 툴이 달라서 자동화 시도를 해도 재사용이 어렵고, 결과적으로 AI 활용이 개인 역량 의존으로 귀결된다.
이 프로젝트는 그 문제를 플러그인 단위의 표준 구조로 바꿔서 해결한다.

README가 강조하듯 각 플러그인은 "skills + commands + connectors"를 묶어 역할별 실행 시스템을 만든다.
즉 단순히 답변 품질을 높이는 것이 아니라 "어떤 도구를 언제 조회하고 어떤 순서로 처리할지"를 워크플로로 고정한다.
실무에서 어떤 가치가 생기냐면 리더 입장에서 품질 편차를 관리할 수 있고, 실무자 입장에서는 매번 작업 방법을 재발명하지 않아도 된다는 점이다.

정량 벤치마크 수치(예: 처리시간 x% 절감)는 저장소에 명시되어 있지 않다.
대신 운영 맥락 근거로는 도메인별 커맨드/스킬의 폭(예: finance, legal, product-management, bio-research)과 7.8k 스타 규모의 커뮤니티 반응이 확인된다.
따라서 이 레포는 "성능 벤치마크 저장소"라기보다 "조직형 AI 운영 템플릿 저장소"로 보는 것이 정확하다.

## 3. 🎯 어떤 상황에서 쓰면 좋을까요? (Use Cases)
이 저장소는 개인 생산성 향상보다 "조직 표준화"가 먼저 필요한 팀에서 효과가 크다.
특히 여러 SaaS를 동시에 쓰는 팀일수록 MCP 연결과 업무 절차를 함께 관리해야 하므로 적용 가치가 커진다.

- **시나리오 1: 재무팀 월말 결산 자동화 표준화.**
상황: 결산/조정 분개/계정대사가 담당자마다 다르게 수행되어 리뷰 비용이 큰 팀.
적용 방법: `finance/commands/reconciliation.md`와 `finance/skills/reconciliation/SKILL.md`를 기준으로 계정대사 절차와 에스컬레이션 기준을 팀 공통 템플릿으로 고정.
기대 결과: 월말 close 시 산출물 형식이 통일되고, 검토자는 차이점이 아니라 예외 건만 집중 점검 가능.

- **시나리오 2: 제품/세일즈/CS 간 고객 맥락 단절 해소.**
상황: 콜 준비, 티켓 요약, 로드맵 업데이트가 서로 다른 문맥으로 작성되어 핸드오프 손실이 발생하는 조직.
적용 방법: `sales`, `customer-support`, `product-management` 플러그인을 병행 설치하고 공통 MCP 소스(Slack, Notion, Jira 등)를 연결.
기대 결과: 고객 맥락이 부서 간 단절되지 않고 재활용되어 중복 조사 시간을 줄임.

- **시나리오 3: 내부 AI 운영 플랫폼 구축 전 프로토타입.**
상황: 사내 전용 에이전트 플랫폼을 만들기 전에 운영 모델을 빠르게 검증해야 하는 팀.
적용 방법: `cowork-plugin-management`로 파일 기반 플러그인 설계를 먼저 검증하고, 필요 시 `partner-built/*` 구조를 참조해 에이전트/설정 계층까지 확장.
기대 결과: 대규모 개발 투자 전에 업무별 운영 규칙을 먼저 확정해 시행착오를 줄임.

핵심은 "플러그인 하나"보다 "직무별 플러그인 묶음"으로 운영할 때 효과가 커진다는 점이다.
실무에서 어떤 가치가 생기냐면 부서 간 공통 인터페이스를 만들면서도 각 팀의 세부 프로세스를 유지할 수 있다는 점이다.

## 4. 🚀 어떻게 사용하나요? (How to use)
가장 빠른 시작은 Marketplace 등록 후 필요한 직무 플러그인 1~2개만 먼저 설치해서 업무 루틴에 붙여보는 방식이다.
아래 절차는 README와 플러그인 내부 문서를 기반으로 정리한 최소 실행 루트다.

**온라인 환경 절차**
```bash
claude plugin marketplace add anthropics/knowledge-work-plugins
claude plugin install sales@knowledge-work-plugins
```
1. 먼저 핵심 직무 플러그인 하나를 설치한다.
2. 해당 플러그인의 `.mcp.json`과 `CONNECTORS.md`를 열어 실제 조직 도구와 매핑한다.
3. `/sales:call-prep`, `/data:write-query` 같은 커맨드를 즉시 실행해 입력-처리-출력 흐름을 검증한다.

**폐쇄망(인터넷 불가) 환경 절차**
1. 내부 Git 미러(예: 사내 GitLab/Gitea)에 이 저장소를 미러링하고 사내망에서 clone한다.
2. 필요한 플러그인 디렉터리만 로컬로 로드한다.
3. `.mcp.json`의 외부 URL을 사내 MCP 게이트웨이 주소로 치환하거나, 사용 불가 항목은 명시적으로 비활성화한다.
4. `CONNECTORS.md`의 카테고리 플레이스홀더(`~~data warehouse`, `~~chat`) 기준으로 내부 대체 도구를 연결한다.
5. 외부 SaaS 전용 MCP(예: hosted Slack/Gmail) 의존 기능은 완전 오프라인에서 대체 불가일 수 있으므로 사내 프록시/동기화 레이어가 없으면 제한 기능으로 운영한다.

**온라인 vs 폐쇄망 차이점**
- 온라인: 기본 `.mcp.json` URL을 거의 그대로 활용 가능.
- 폐쇄망: 미러 운영, MCP 엔드포인트 치환, 인증 체계 내재화가 필수.
- 온라인: 설치 즉시 확장 가능.
- 폐쇄망: 운영팀과 보안팀 협업이 선행되어야 안정화 가능.

## 5. ✨ 사용하면 무엇이 좋아지나요? (Benefits)
- **도입 이득(효율/유지보수).**
파일 기반 구조라 코드 빌드 파이프라인 없이 정책 변경이 가능하다.
커맨드와 스킬이 분리되어 있어 즉시 실행 지시와 장기 지식 문서를 독립적으로 개선할 수 있다.
실무에서 어떤 가치가 생기냐면 "업무 표준 문서"와 "실행 자동화"가 같은 저장소에서 함께 버전 관리된다는 점이다.

- **리스크/비용 절감.**
`CONNECTORS.md`의 카테고리 추상화 덕분에 특정 SaaS 종속도를 낮추고 도구 교체 비용을 줄일 수 있다.
`bio-research` 스크립트처럼 fallback·검증·provenance 패턴을 재사용하면 운영 중 데이터 신뢰성 사고를 줄이는 데 도움이 된다.
실무에서 어떤 가치가 생기냐면 빠른 자동화와 통제 가능성을 동시에 챙길 수 있다는 점이다.

- **현실적 트레이드오프.**
저장소 루트 기준 CI 워크플로/테스트 폴더가 확인되지 않아 품질 게이트는 조직이 별도로 구축해야 한다.
GitHub 화면 기준 릴리즈/패키지 배포가 비어 있어 버전 안정성 관리는 태그 전략과 내부 배포 규칙에 의존한다.
README의 Marketplace 소개(11개)와 실제 플러그인 매니페스트 수(19개) 사이에 차이가 있어 온보딩 시 문서 동기화 확인이 필요하다.

- **에코시스템/확장성.**
MCP 중심 설계라 Slack, Notion, Atlassian, BigQuery, Databricks 등 다중 SaaS를 동일 패턴으로 연결할 수 있다.
`cowork-plugin-management`가 신규 플러그인 생성과 커스터마이징 가이드를 내장하고 있어 조직 내부 확장 루프를 만들기 좋다.
실무에서 어떤 가치가 생기냐면 "한 번 정의한 운영 문법"을 다른 직무로 수평 확장하기 쉽다는 점이다.

**도입 판단 한줄 정리:**
업무 절차를 팀 단위로 표준화해야 하고 MCP 기반 도구 연결을 유연하게 관리하려는 조직이라면, 이 레포는 바로 적용 가능한 실전 템플릿이다.
