<p align="center">
  <a href="https://github.com/yxxnpyo/outsourcing" style="text-decoration:none;">
    <img alt="Create Date" src="https://img.shields.io/badge/Create%20Date-2026--03--11-2f363d?style=for-the-badge&logo=github&logoColor=white" />
  </a>
  <a href="https://github.com/yxxnpyo/outsourcing/stargazers" style="text-decoration:none;">
    <img alt="Star" src="https://img.shields.io/badge/Star-0-2f363d?style=for-the-badge&logo=github&logoColor=white" />
  </a>
  <a href="https://github.com/yxxnpyo/outsourcing/fork" style="text-decoration:none;">
    <img alt="Fork" src="https://img.shields.io/badge/Fork-0-2f363d?style=for-the-badge&logo=github&logoColor=white" />
  </a>
</p>
<p align="center">
  <img alt="JavaScript" src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=000000" />
  <img alt="Node.js" src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=ffffff" />
  <img alt="Shell" src="https://img.shields.io/badge/Shell-4EAA25?style=for-the-badge&logo=gnubash&logoColor=ffffff" />
  <img alt="YAML" src="https://img.shields.io/badge/YAML-CB171E?style=for-the-badge&logo=yaml&logoColor=ffffff" />
  <img alt="tmux" src="https://img.shields.io/badge/tmux-1BB91F?style=for-the-badge&logo=tmux&logoColor=ffffff" />
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/yxxnpyo/outsourcing/main/docs/outsourcing-hero.png" alt="outsourcing hero" style="max-width: 100%; width: 100%; height: auto; display: block;" />
</p>

yxxnpyo/outsourcing는 Claude Code를 PM으로 두고 Codex를 외주 구현 워커처럼 운영하자는 아이디어를 실제 플러그인 구조로 묶은 저장소입니다.
핵심은 "병렬 실행" 자체보다, Claude가 구현 본문을 길게 쓰지 않고 작업 분해와 검수에 집중하도록 프롬프트 구조를 재설계한 점입니다.
실제 구현은 `commands/outsourcing.md`에서 명령 진입점을 열고, `skills/outsourcing/scripts/outsourcing-job.js`가 작업 디렉터리 생성, 워커 실행, 상태 집계, 최종 리포트 생성을 담당하는 구조입니다.
README와 스크립트를 함께 보면 이 프로젝트는 단순한 래퍼가 아니라 템플릿 외부화, `tmux` 옵저버, 게이트 재검증, 토큰 측정까지 포함한 운영 실험 도구에 가깝습니다.
다만 GitHub API 기준 생성일이 2026-03-11이고 스타 0, 포크 0, 릴리즈와 태그도 아직 없어서, 성숙한 제품보다는 빠르게 방향을 검증 중인 초기 공개 버전으로 보는 편이 정확합니다.

## 1. 🔍 핵심 기능 및 구현 방식 (Core Features & Implementation)

- **PM/워커 역할 분리.**
  `commands/outsourcing.md`와 `skills/outsourcing/SKILL.md`는 Claude가 분해와 최종 합성만 맡고, Codex가 구현을 맡는 역할 계약을 먼저 고정합니다.
  실제 워커 프롬프트 조립은 `skills/outsourcing/scripts/outsourcing-job-worker.js`의 `buildPrompt()`가 담당하고, 고정 자산은 `templates/worker-core.md`, `templates/report-format.md`, `templates/phase-openers.json`, `templates/report-rules.json`에서 읽습니다.
  그래서 실무에서 어떤 가치가 생기는지 보자면, PM 모델이 같은 긴 지시문을 매번 다시 생성하지 않아도 되어 대화 길이와 구현 토큰 부담을 의식적으로 분리할 수 있습니다.

- **YAML 기반 작업 정의와 실행 오케스트레이션.**
  `outsourcing.config.yaml`이 기본 명령, 옵저버 모드, 재시도 수, 토큰 추정 가중치 같은 운영 옵션을 담고, `skills/outsourcing/scripts/outsourcing-job.js`의 `parseOutsourcingConfig()`와 `normalizeTask()`가 이를 실행 가능한 태스크로 바꿉니다.
  엔트리포인트는 `skills/outsourcing/scripts/outsourcing.sh`와 `outsourcing-job.sh`이고, 실제 코어 로직은 거의 모두 `outsourcing-job.js`에 모여 있습니다.
  이 구조는 "설정 파일 한 장으로 병렬 외주 실행 라운드 정의"가 가능하다는 뜻이라서, 여러 독립 작업을 반복해서 맡기는 팀에 맞습니다.

- **Observer Mode와 단계별 상태 추적.**
  `skills/outsourcing/scripts/outsourcing-observer.js`는 `tmux` 세션 생성, pane 분할, 캡처, 종료를 담당합니다.
  `skills/outsourcing/scripts/outsourcing-job.js`의 `parseObserverBlocks()`와 `captureObserverState()`는 워커 출력 앞에 불릿이나 박스 문자가 붙어도 `[OUTSOURCING]` 마커부터 다시 파싱하도록 작성돼 있습니다.
  이런 구현은 데모용 시각화가 아니라, 사람은 pane을 보고 기계는 고정 마커만 읽게 분리했다는 점이 중요합니다.

- **게이트 실행과 자동 재위임.**
  같은 `outsourcing-job.js` 안의 `gates` 처리 구간은 각 태스크가 끝난 뒤 `bash -lc`로 개별 검증 명령을 실행하고, 결과를 `gates.json`으로 남깁니다.
  이어서 `cmdAutofix()`는 실패 게이트 이름이나 워커 종료 상태를 짧은 correction 문장으로 바꿔 다시 던집니다.
  그래서 실무에서 어떤 가치가 생기는지 보자면, "실패하면 다시 사람 손으로 설명을 길게 적는다"가 아니라 최소한의 수정 지시로 재실행 루프를 붙일 수 있습니다.

- **토큰 측정과 최종 리포트 생성.**
  `estimateTokenMetrics()`는 Claude 추정 토큰, 워커 프롬프트/출력 토큰, 실제 사용량이 있으면 그 수치까지 합쳐 `final-report.json`과 `final-report.md`를 만듭니다.
  README의 "Final token metrics" 섹션과 `templates/final-report.md`를 보면 이 저장소의 차별점이 단순 성공/실패가 아니라 "Claude 토큰을 얼마나 다른 모델로 밀어냈는가"를 결과물로 남긴다는 데 있음을 알 수 있습니다.
  아직 외부 벤치마크는 없지만, 구현은 이미 그 방향에 맞춰 꽤 구체적으로 닦여 있습니다.

기술 스택은 Node.js 기반 JavaScript, Bash 래퍼, YAML 설정, `tmux`, 그리고 Claude Code 플러그인 매니페스트(`.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`) 조합으로 요약할 수 있습니다.
구조도도 분명합니다.
명령 진입점은 `commands/outsourcing.md`, 코어 로직은 `skills/outsourcing/scripts/outsourcing-job.js`, 설정/인프라는 `outsourcing.config.yaml`, 테스트 자산은 `test-fixtures/`가 맡습니다.

## 2. 💡 이 프로젝트는 왜 필요한가요? (Why & Background)

기존 LLM 코딩 흐름의 문제는 계획과 구현이 한 세션 안에서 섞이면서, 가장 비싼 모델이 구현 토큰까지 모두 떠안는다는 점입니다.
README 첫머리의 설명처럼 이 프로젝트는 총 토큰을 무조건 줄이겠다는 약속보다, Claude가 "무엇을 만들지"를 관리하고 Codex가 "어떻게 만들지"를 수행하도록 역할을 나누는 데 집중합니다.
그래서 실무에서 어떤 가치가 생기는지 보자면, 리더 모델은 태스크 분해, 시그니처 확정, 검수에 더 오래 남고 구현 상세는 워커로 밀어낼 수 있습니다.

차별점은 단순 병렬 실행기가 아니라는 점입니다.
워커 프롬프트를 디스크의 고정 템플릿으로 분리하고, phase 보고 형식을 고정하고, observer pane은 사람이 보기 위한 화면으로만 두며, 결과 평가는 게이트와 최종 리포트로 회수합니다.
이 접근은 "에이전트 여러 개 띄우기"보다 운영 규약을 먼저 세운 설계라서, 나중에 실패 원인을 찾거나 팀 표준을 정리하기가 쉽습니다.

다만 README의 "Verification status"는 실제 1-task, 3-task 실행을 이미 검증했다고 적고 있지만, 저장소 안에는 그 주장을 자동으로 재현하는 GitHub Actions 워크플로는 없습니다.
`test-fixtures/practical-one-task.yaml`, `test-fixtures/practical-three-task.yaml`, `test-fixtures/practical-smoke-workspace/README.md`를 보면 실험 재료는 제공되지만, 수치형 벤치마크 표나 반복 실험 로그 요약은 아직 없습니다.
즉 "토큰 절감"은 현재 단계에서 프로젝트 측 방향성으로는 설득력이 있지만, 외부 도입 판단에는 자체 재현 검증이 필요합니다.

GitHub 운영 신호도 이 해석을 뒷받침합니다.
GitHub API 기준 저장소는 2026-03-11에 생성됐고, 같은 날 `Prepare outsourcing plugin for release`, `Refine Claude token measurement and session matching`, `Add marketplace-ready plugin source layout` 같은 커밋이 빠르게 이어졌습니다.
반면 2026-03-11 조회 시점에 스타 0, 포크 0, 오픈 이슈 0, 오픈 PR 0, 릴리즈 0, 태그 0이어서, 활발한 사용자 반응보다 제작자의 집중 개발이 먼저인 단계로 보입니다.

## 3. 🎯 어떤 상황에서 쓰면 좋을까요? (Use Cases)

이 도구는 "에이전트를 많이 붙일 수 있다"보다 "작업을 분해하고 검수하는 운영 습관이 이미 있는 팀"에서 더 가치가 큽니다.
README와 SKILL 문서가 공통으로 최소 3개 이상의 독립 태스크를 권장하는 이유도, 이 플러그인의 오버헤드가 아주 작은 수정에는 맞지 않기 때문입니다.
그래서 실무에서 어떤 가치가 생기는지 보자면, 병렬화 자체보다 역할 분리 규약을 강제하고 싶은 팀에서 효과가 납니다.

- **시나리오 1.**
  상황: 백엔드 API, DB 마이그레이션, 프론트 UI처럼 서로 간섭이 적은 작업이 동시에 열려 있습니다.
  적용 방법: Claude가 시그니처와 제약만 정의하고, `outsourcing.config.yaml`에 태스크별 payload와 gate를 넣어 여러 Codex 워커로 위임합니다.
  기대 결과: 구현 상세는 워커로 분산되고, 최종 검수는 한곳에서 회수돼 PM 역할의 문맥 오염이 줄어듭니다.

- **시나리오 2.**
  상황: 한 명이 여러 에이전트를 돌리지만, 진행 상태를 눈으로 확인하고 싶습니다.
  적용 방법: `tmux` 기반 Observer Mode를 켜고 pane별 phase 메모만 보면서, 실제 로그 해석은 마커 파서에 맡깁니다.
  기대 결과: 사람은 진행 상황을 직관적으로 보고, 자동화는 고정된 상태 전이만 읽어 들여 운영 복잡도를 낮출 수 있습니다.

- **시나리오 3.**
  상황: 사내에서 "LLM에게 맡긴 작업이 왜 실패했는지"를 사후 분석해야 합니다.
  적용 방법: `gates.json`, `report.json`, `final-report.json`을 보존하고 실패 시 `autofix`로 correction을 짧게 넣어 재위임합니다.
  기대 결과: 재현 가능한 실패 히스토리와 토큰 측정 기록이 남아, 단순 채팅형 자동화보다 회고가 쉬워집니다.

반대로 단일 파일 수정, 긴 공통 문맥을 모든 워커가 반복해서 읽어야 하는 작업, 혹은 Claude가 직접 구현하는 편이 더 싼 수준의 소규모 업무에는 과합니다.
이 저장소는 오케스트레이션 규칙을 얻는 대신 준비 비용을 지불하는 도구라는 점을 기억해야 합니다.

## 4. 🚀 어떻게 사용하나요? (How to use)

가장 빠른 시작 경로는 README가 안내하는 로컬 플러그인 방식입니다.
저장소 구조가 이미 Claude Code 플러그인 규칙에 맞춰져 있으므로, 설치보다 "작업 정의 파일을 어떻게 채우느냐"가 실제 사용성에 더 큰 영향을 줍니다.

**온라인 환경 절차**

```bash
git clone https://github.com/yxxnpyo/outsourcing.git
cd outsourcing/skills/outsourcing
npm install
cd ../..
claude --plugin-dir .
```

Claude Code 안에서는 아래 흐름으로 이어집니다.

```text
/reload-plugins
/outsourcing implement auth, DB, and API tasks in parallel
```

실행 전에 `outsourcing.config.yaml`에 태스크 목록, `cwd`, gate 명령, payload를 채워야 합니다.
기본 브랜치는 GitHub API 기준 `main`이고, 2026-03-11 조회 시점에 최신 릴리즈와 태그는 없어서 버전 고정은 커밋 SHA 기준으로 하는 편이 안전합니다.

**폐쇄망(인터넷 불가) 환경 절차**

```bash
git clone <internal-mirror>/outsourcing.git
cd outsourcing/skills/outsourcing
npm install --offline
claude --plugin-dir .
```

폐쇄망에서는 저장소 자체는 내부 Git 미러로 들여올 수 있고, 의존성도 `yaml` 하나라서 사내 npm 캐시나 사전 패키지 번들로 대응하기 쉽습니다.
다만 실제 워커 실행은 `codex` CLI와 Claude Code가 외부 모델 서비스에 연결돼야 하므로, 완전한 인터넷 차단 환경에서는 사내 프록시나 별도 게이트웨이가 없으면 기능 핵심이 막힙니다.
즉 이 프로젝트의 폐쇄망 사용 가능 여부는 "저장소 설치는 가능, 모델 호출은 환경 의존"인 조건부 가능으로 보는 것이 맞습니다.

온라인과 폐쇄망의 차이는 저장소 설치보다 인증과 모델 접근 경로에서 크게 납니다.
템플릿, YAML 설정, gate, observer 파서는 모두 로컬 자산이라서 유지되지만, 실제 구현 생산성은 Codex와 Claude가 어떤 네트워크 정책 아래 동작하느냐에 달려 있습니다.

## 5. ✨ 사용하면 무엇이 좋아지나요? (Benefits)

가장 큰 이점은 역할 분리를 강제하는 운영 프레임을 얻는다는 점입니다.
여러 에이전트를 병렬로 띄우는 행위 자체는 어렵지 않지만, 이 저장소는 payload 구조, phase 보고 형식, 게이트, 재위임, 최종 리포트까지 묶어 "다음에도 같은 방식으로 굴릴 수 있는 습관"을 남깁니다.
그래서 실무에서 어떤 가치가 생기는지 보자면, 사람이 매번 작업 방식부터 다시 합의하지 않아도 되는 점이 큽니다.

또 하나의 장점은 측정 가능성입니다.
`estimateTokenMetrics()`와 관련 템플릿은 Claude 측 절감 효과를 완벽하게 증명하진 못해도, 최소한 무엇을 근거로 계산했는지를 파일로 남깁니다.
LLM 운영에서 가장 부족한 부분이 "이번 자동화가 실제로 이득이었는가"인데, 이 저장소는 그 질문에 답할 자리를 만들어 둡니다.

에코시스템 확장성도 괜찮습니다.
`.claude-plugin/plugin.json`과 `.claude-plugin/marketplace.json`이 함께 들어 있어 로컬 플러그인 개발 흐름과 마켓플레이스 배포 흐름을 모두 염두에 두고 있습니다.
README가 `/plugin marketplace add <owner>/<repo>`와 `/plugin install outsourcing@<owner>/<repo>` 예시까지 적어 둔 점은, 단발성 스크립트보다 배포 가능한 플러그인 자산으로 보겠다는 의도를 보여 줍니다.

현실적인 트레이드오프도 분명합니다.
첫째, `tmux`, Claude Code, Codex CLI, Node.js까지 요구하므로 진입 조건이 가볍지 않습니다.
둘째, 저장소가 오늘 공개된 초기 단계라 운영 성숙도와 사용자 피드백이 아직 쌓이지 않았습니다.
셋째, 토큰 절감 모델은 흥미롭지만 현재는 저장소 내부 추정 로직에 많이 기대고 있어, 팀마다 직접 재현 검증을 해야 합니다.

도입 판단 한줄 정리.
Claude를 "리드 엔지니어 겸 PM"으로 남기고 Codex를 실무 구현 워커로 조직적으로 굴리고 싶다면 충분히 볼 가치가 있지만, 지금 단계에서는 프로덕션 표준 도구보다 실험적 운영 프레임워크에 가깝습니다.
