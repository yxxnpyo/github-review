<p align="center">
  <a href="https://github.com/openai/symphony"><img src="https://img.shields.io/badge/Repo-openai%2Fsymphony-111827?style=for-the-badge&logo=github&logoColor=white" alt="Repo" /></a>
  <img src="https://img.shields.io/badge/Stars-8.1k-1f2937?style=for-the-badge&logo=github&logoColor=white&labelColor=111827" alt="Stars" />
  <img src="https://img.shields.io/badge/Forks-532-1f2937?style=for-the-badge&logo=github&logoColor=white&labelColor=111827" alt="Forks" />
  <img src="https://img.shields.io/badge/Latest_Commit-2026--03--04-1f2937?style=for-the-badge&labelColor=111827" alt="Latest Commit" />
</p>

openai/symphony는 "Codex를 더 잘 쓰는 팁 모음"이 아니라, 티켓 단위 작업을 Codex에게 장기 실행시키는 운영 방식에 가깝습니다.
README의 표현대로 이 프로젝트는 "코딩 에이전트를 직접 감독하는 것"에서 "해야 할 일을 운영하는 것"으로 이동하려는 팀을 위한 도구입니다.
중요한 점은 Symphony가 일반적인 에이전트 런처가 아니라, Linear 티켓 상태, per-issue workspace, `WORKFLOW.md`, PR/검증/머지 절차를 하나의 흐름으로 묶는다는 것입니다.
그래서 Codex 사용자 입장에서는 "한 번 프롬프트를 잘 쓰는 법"보다 "반복되는 개발 운영을 Codex에 어떻게 맡길 것인가"를 고민할 때 읽을 가치가 있습니다.
2026-03-07 기준 GitHub 공개 페이지에는 별 8.1k, 포크 532, 최신 커밋 2026-03-04가 보이며, 현재 레포는 아직 커밋 수가 적은 엔지니어링 프리뷰 단계입니다.

## 한 줄 판단

**여러 개의 개발 티켓을 Codex에게 비동기적으로 맡기고 싶고, 팀이 이미 Linear + Git + CI + 리뷰 습관을 갖고 있다면 Symphony는 매우 흥미로운 출발점입니다.**

반대로 지금도 티켓 관리가 느슨하고, 사람 개발자가 직접 맥락을 계속 주입해야 하며, 작은 작업 몇 개만 가끔 Codex에 맡기는 수준이라면 아직은 과한 선택일 가능성이 큽니다.

## Symphony를 Codex에서 쓴다는 게 정확히 무슨 뜻인가

Symphony는 Codex를 "채팅 도우미"가 아니라 "워크플로 실행 엔진"처럼 다룹니다.
`SPEC.md`는 Symphony를 장기 실행 서비스로 정의하고, 이 서비스가 이슈 트래커를 읽고, 티켓별 워크스페이스를 만들고, Codex app-server 세션을 띄운 뒤, 결과를 다시 운영 흐름에 반영하도록 설명합니다.
즉 Codex를 한 터미널 세션 안에서 수동으로 부르는 것이 아니라, 티켓 상태 변화에 따라 자동으로 여러 번 실행되는 작업자로 취급합니다.

Codex 사용자의 눈으로 보면 핵심은 아래 네 가지입니다.

- **티켓이 프롬프트를 만든다**: `elixir/WORKFLOW.md`의 프롬프트 템플릿이 `issue.identifier`, `issue.title`, `issue.description`, 현재 상태, 재시도 횟수 같은 값을 주입해 실행 컨텍스트를 만듭니다.
- **작업마다 격리된 workspace를 쓴다**: `SPEC.md`와 `elixir/README.md`는 이슈별 워크스페이스를 강하게 전제합니다. Codex가 소스 레포 루트에서 아무거나 만지는 방식이 아니라, 각 티켓 사본 안에서만 일하게 만듭니다.
- **운영 규칙을 repo 안에 넣는다**: `WORKFLOW.md` front matter에 polling, concurrency, sandbox, hooks, tracker 설정을 넣고, 본문에는 에이전트 행동 규칙을 넣습니다.
- **Codex에게 Git/Linear 운영 습관까지 같이 준다**: `.codex/skills/commit`, `pull`, `push`, `linear`, `land`가 같이 들어 있어, 단순 코드 수정이 아니라 브랜치 동기화, PR 생성/갱신, Linear 코멘트 갱신, 머지 루프까지 연결됩니다.

그래서 실무에서 어떤 가치가 생기냐면, "버그 하나 고쳐줘"보다 "이 보드에 쌓인 일을 질서 있게 처리해줘"에 가까운 운영이 가능해집니다.

## Codex에서 언제 쓰면 좋은가

### 1. 티켓 기반 개발이 이미 굴러가는 팀

Symphony는 Linear를 전제로 시작합니다.
README와 `SPEC.md` 모두 현재 버전의 이슈 트래커를 Linear로 가정하고 있고, Elixir 구현도 `LINEAR_API_KEY`와 `linear_graphql` 도구를 중심에 둡니다.
즉 "할 일 목록"이 아니라, 상태가 있는 티켓 운영이 이미 되고 있는 팀일수록 맞습니다.

이런 경우에 특히 맞습니다.

- 백로그보다 **상태 전이**가 중요할 때
- 티켓별 브랜치, PR, 리뷰, 검증이 이미 익숙할 때
- 사람이 직접 모든 중간 코멘트를 쓰는 비용이 아까울 때
- 여러 작업을 동시에 Codex에게 태우고 싶을 때

### 2. Codex를 사람 옆 보조가 아니라 반자율 실행자로 쓰고 싶을 때

`elixir/README.md`는 Symphony가 Codex를 app-server mode로 실행한다고 명시합니다.
그리고 `WORKFLOW.md`는 "이건 unattended orchestration session이다", "진짜 blocker가 아니면 사람에게 후속 액션을 요구하지 마라" 같은 규칙을 강하게 둡니다.
즉 채팅으로 세세하게 지시하기보다, 시작 조건과 운영 정책을 잘 설계해 놓고 여러 턴을 맡기는 모델입니다.

그래서 이런 상황에 좋습니다.

- 반복적인 유지보수 작업이 많을 때
- 한 작업이 끝날 때까지 여러 번 수정, 테스트, 코멘트 업데이트가 필요할 때
- 작업 상태를 사람이 일일이 추적하는 대신 시스템이 workpad를 유지하길 원할 때

### 3. "에이전트 품질"보다 "에이전트 운영"이 병목일 때

많은 팀은 Codex 자체가 부족해서보다, Codex를 어디서 시작시키고 어떤 규칙으로 멈추게 할지 정하지 못해서 막힙니다.
Symphony는 바로 그 운영면을 다룹니다.
`SPEC.md`가 orchestration, retry, reconciliation, observability를 따로 강조하는 이유도 여기 있습니다.

그래서 지금 당신의 병목이 아래라면 Symphony 쪽이 더 맞습니다.

- 에이전트가 어디서 작업해야 하는지 자주 헷갈린다.
- 중간에 멈춘 작업을 이어받는 기준이 없다.
- 브랜치, PR, 리뷰, 티켓 코멘트가 서로 따로 논다.
- "한 번 잘 됨"은 있는데 "계속 굴러감"이 없다.

## Codex에서 어떻게 활용하면 되나

제가 보기엔 Codex 사용자에게는 세 가지 활용 방식이 있습니다.

### 방식 A. Symphony를 "설계 스펙"으로 쓰기

가장 안전한 시작은 레포 전체를 바로 도입하지 않고, `SPEC.md`를 Codex용 아키텍처 스펙으로 사용하는 것입니다.
README도 첫 번째 옵션으로 "좋아하는 코딩 에이전트에게 원하는 언어로 구현하라"고 안내합니다.
즉 Go, Python, TypeScript, Elixir 등 당신 팀 스택에 맞는 오케스트레이터를 직접 만들 때 기준 문서로 삼을 수 있습니다.

이 접근이 좋은 경우:

- 팀 표준 언어가 Elixir가 아닐 때
- 사내 이슈 트래커나 인증 체계가 달라서 레퍼런스 구현을 바로 쓰기 어렵을 때
- trusted environment보다 더 강한 승인 정책이 필요할 때

Codex에게 이렇게 시킬 수 있습니다.

```text
SPEC.md를 기준으로 우리 팀용 Symphony 아키텍처를 TypeScript로 설계해줘.
Linear 대신 Jira를 쓰는 버전으로 domain model과 orchestrator 책임을 다시 나눠줘.
지금 레포 구조에 맞는 WORKFLOW.md 초안을 만들어줘.
```

### 방식 B. Elixir 레퍼런스 구현을 "운영 템플릿"으로 쓰기

두 번째는 `elixir/README.md`와 `elixir/WORKFLOW.md`를 사실상의 템플릿으로 가져오는 방식입니다.
이 경우 중요한 건 "Symphony 자체를 쓰는 것"보다 "Symphony가 Codex를 운영하는 방식"을 가져오는 것입니다.

특히 바로 재사용 가능한 조각은 아래입니다.

- `WORKFLOW.md` front matter 구조
- `hooks.after_create`로 워크스페이스를 bootstrap 하는 방식
- `codex.command`, `approval_policy`, `thread_sandbox`, `turn_sandbox_policy` 설정 패턴
- `.codex/skills/commit`, `pull`, `push`, `linear`, `land`
- `## Codex Workpad`를 단일 소스 오브 트루스로 유지하는 운영 습관

Codex에게는 이런 식의 요청이 실용적입니다.

```text
openai/symphony/elixir/WORKFLOW.md를 참고해서 우리 repo용 WORKFLOW.md를 만들어줘.
openai/symphony의 .codex/skills 중 commit, pull, push만 우리 repo에 이식해줘.
hooks.after_create를 우리 monorepo bootstrap 절차에 맞게 다시 써줘.
```

### 방식 C. 전체 자동화 서비스를 실제로 돌리기

세 번째가 가장 강력하지만 가장 무거운 방식입니다.
Linear에서 티켓을 읽고, 이슈별 workspace를 만들고, Codex app-server를 띄우고, PR과 리뷰 사이클까지 이어지는 서비스를 실제로 굴리는 것입니다.

이 방식이 맞는 경우:

- 티켓 수가 많고 동시 처리 가치가 분명할 때
- CI와 리뷰 체계가 갖춰져 있어 사람이 마지막 판단만 하면 될 때
- "Codex 세션 1개"보다 "Codex 작업자 풀"이 더 필요한 단계일 때

대신 운영 부담도 분명합니다.
README가 trusted environment에서 시험하라고 경고하는 이유를 가볍게 보면 안 됩니다.
`elixir/WORKFLOW.md` 예시에는 `approval_policy: never`도 들어가 있는데, 이건 잘 설계된 검증과 격리 없이는 위험할 수 있습니다.

## 실제 도입 순서

제가 Codex 사용자라면 아래 순서로 시작하겠습니다.

### 1. 풀버전 도입 전에 "부분 이식"부터 한다

처음부터 서비스 전체를 띄우기보다, 아래 세 가지만 먼저 가져오면 충분히 가치가 있습니다.

- `WORKFLOW.md` 스타일의 repo-owned prompt
- `.codex/skills/pull`, `push`, `commit`
- workpad 중심 운영 규칙

이 세 가지만 있어도 Codex가 브랜치 정리, PR 본문, 진행 코멘트, 검증 루틴을 훨씬 일관되게 수행합니다.

### 2. 그 다음에 workspace 격리를 붙인다

Symphony의 가장 중요한 습관은 "source repo에서 바로 일하지 않는다"는 점입니다.
`SPEC.md`와 `elixir/AGENTS.md`는 workspace safety를 매우 강하게 봅니다.
이 부분을 따라가면 Codex가 현재 작업과 무관한 파일을 건드릴 위험을 줄일 수 있습니다.

### 3. 마지막에 Linear 연동과 자동 상태 전이를 붙인다

이 단계부터가 진짜 Symphony 영역입니다.
`linear_graphql` 도구, `Human Review`/`Rework`/`Merging` 같은 상태 전이, PR 피드백 sweep, merge loop까지 얹으면 사람이 직접 조정하던 운영이 많이 자동화됩니다.

하지만 이건 팀의 GitHub/Linear 규율이 이미 있어야 효과가 큽니다.
규율이 없으면 자동화가 문제를 가리는 쪽으로 작동할 수 있습니다.

## 이런 경우에 특히 추천한다

- **Linear를 중심으로 엔지니어링 작업을 운영하는 팀**
- **Codex를 매일 여러 번 쓰고 있고, 같은 설명을 반복하는 데 지친 팀**
- **PR과 리뷰는 사람이 하되, 구현과 중간 업데이트는 에이전트에게 넘기고 싶은 팀**
- **worktree 또는 issue workspace 기반 개발 습관을 받아들일 수 있는 팀**
- **한두 번의 성공 데모보다 지속적인 운영 루프가 더 중요한 팀**

## 이런 경우엔 아직 쓰지 않는 편이 낫다

- **작업량이 작고, Codex는 가끔만 쓰는 개인 프로젝트**
- **Linear를 안 쓰거나, 티켓 상태 관리가 아직 느슨한 팀**
- **테스트/CI가 약해서 에이전트 검증 결과를 신뢰하기 어려운 팀**
- **승인 없는 장기 실행 에이전트를 trusted environment에 둘 수 없는 조직**
- **도입보다 빠른 결과가 더 중요한 초기 해커톤성 작업**

Symphony는 "에이전트를 더 똑똑하게 만든다"기보다 "에이전트를 더 운영 가능하게 만든다"에 가깝습니다.
그래서 운영 성숙도가 낮을수록 효과가 작아질 수 있습니다.

## 내가 Codex에서 바로 가져갈 만한 포인트

코드 구현까지 안 보더라도, 이 레포에서 바로 흡수할 만한 실무 포인트는 분명합니다.

1. `WORKFLOW.md`를 repo 안에 두고 프롬프트와 정책을 버전 관리한다.
2. Codex에게도 `pull`, `push`, `commit` 같은 운영 기술을 스킬로 분리해 준다.
3. 작업당 하나의 workpad를 유지해 상태, 검증, acceptance criteria를 한 곳에 모은다.
4. 티켓이 아직 active state인지에 따라 Codex의 행동을 다르게 만든다.
5. app-server나 장기 실행 세션을 쓸수록 observability와 token accounting을 같이 설계한다.

특히 `elixir/docs/token_accounting.md`와 `elixir/docs/logging.md`는 "에이전트가 돌아간다" 이후에 무엇을 봐야 하는지 보여준다는 점에서 꽤 중요합니다.
Codex 자동화를 조금만 크게 굴려도 결국 필요한 것은 프롬프트보다 운영 가시성입니다.

## 빠른 실험 체크리스트

작게 시험해 보고 싶다면 이 정도가 현실적인 최소 단위입니다.

- Linear 테스트 프로젝트 하나 만들기
- `Todo`, `In Progress`, `Human Review`, `Rework`, `Merging`, `Done` 상태 준비하기
- Codex가 잘 작동하는 저장소 하나 고르기
- `WORKFLOW.md` 초안을 repo에 만들기
- `pull`, `push`, `commit` 스킬만 먼저 가져오기
- workspace bootstrap hook 작성하기
- CI가 최소한 한두 개는 자동으로 돌게 만들기
- 내부용 비중요 티켓 몇 개에서만 먼저 시험하기

이 정도만 해도 "Symphony 전체를 쓸지", "일부 패턴만 가져갈지" 판단이 가능합니다.

## 결론

openai/symphony는 아직 제품형 툴이라기보다 운영 패턴에 가깝습니다.
하지만 Codex를 개인 도우미에서 팀 단위 작업자 풀로 확장하고 싶다면, 지금 공개된 자료만으로도 꽤 많은 힌트를 줍니다.

제 판단으로는 **Codex를 장기적으로 팀 운영에 넣고 싶은 사람이라면 반드시 읽어볼 만한 레포**입니다.
다만 바로 "설치해서 끝"이라고 생각하면 실망할 가능성이 높고, **`SPEC.md`와 `WORKFLOW.md`, `.codex/skills`를 자기 조직에 맞게 재구성하는 출발점**으로 보는 편이 정확합니다.

## 참고한 자료

- https://github.com/openai/symphony
- https://github.com/openai/symphony/blob/main/README.md
- https://github.com/openai/symphony/blob/main/SPEC.md
- https://github.com/openai/symphony/blob/main/elixir/README.md
- https://github.com/openai/symphony/blob/main/elixir/WORKFLOW.md
- https://github.com/openai/symphony/blob/main/elixir/AGENTS.md
- https://github.com/openai/symphony/tree/main/.codex/skills
- https://github.com/openai/symphony/blob/main/.github/pull_request_template.md
