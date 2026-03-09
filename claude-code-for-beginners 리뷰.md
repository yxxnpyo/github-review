<div style="display:flex;flex-wrap:wrap;gap:10px;justify-content:center;">
  <a href="https://github.com/koki7o/claude-code-for-beginners" style="text-decoration:none;display:inline-flex;align-items:center;padding:6px 12px;border-radius:999px;background:#0f172a;color:#ffffff;font-weight:700;font-size:12px;"><span>Create Date</span>&nbsp;<span>2025-12-27</span></a>
  <a href="https://github.com/koki7o/claude-code-for-beginners/stargazers" style="text-decoration:none;display:inline-flex;align-items:center;padding:6px 12px;border-radius:999px;background:#b91c1c;color:#ffffff;font-weight:700;font-size:12px;"><span>Star</span>&nbsp;<span>196</span></a>
  <a href="https://github.com/koki7o/claude-code-for-beginners/fork" style="text-decoration:none;display:inline-flex;align-items:center;padding:6px 12px;border-radius:999px;background:#1d4ed8;color:#ffffff;font-weight:700;font-size:12px;"><span>Fork</span>&nbsp;<span>36</span></a>
</div>

<div style="display:flex;flex-wrap:wrap;gap:8px;justify-content:center;">
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#191919;color:#ffffff;font-weight:700;"><img src="_assets/siw-anthropic.png" alt="Anthropic" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Anthropic</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#000000;color:#ffffff;font-weight:700;"><img src="_assets/siw-markdown.png" alt="Markdown" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Markdown</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#181717;color:#ffffff;font-weight:700;"><img src="_assets/siw-github.png" alt="GitHub" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">GitHub</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#F05032;color:#ffffff;font-weight:700;"><img src="_assets/siw-git.png" alt="Git" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Git</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#CB3837;color:#ffffff;font-weight:700;"><img src="_assets/siw-npm.png" alt="npm" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">npm</span></span>
</div>

<img src="https://raw.githubusercontent.com/koki7o/claude-code-for-beginners/main/claude_code.png" alt="claude-code-for-beginners 배너" style="max-width: 100%; width: 100%; height: auto; display: block;" />
*출처: README 배너*

## 개요
koki7o/claude-code-for-beginners는 Claude Code 입문자를 위해 “설치 → 기본 사용 → 실전 개발 → 배포”까지 한 흐름으로 설계한 문서형 코스 레포입니다.
코드 라이브러리라기보다 커리큘럼 저장소에 가깝고, `README.md`에서 전체 학습 동선을 제시한 뒤 각 모듈 문서로 세부 내용을 확장합니다.
구성은 코어 10개 모듈과 고급 5개 모듈, 그리고 빠른 참조/문제 해결/챌린지 해설용 보조 문서로 나뉘어 있어 독학 난이도를 낮춘 점이 특징입니다.
특히 각 모듈마다 체크리스트와 연습 과제를 넣어 단순 읽기에서 끝나지 않게 설계했고, Git/MCP/배포까지 포함해 “초급자 코스”임에도 실무 연결성이 높습니다.
이 레포는 자체 소프트웨어를 제공한다기보다 Claude Code를 활용한 개발 학습 프레임워크를 제공합니다.
조회 기준 메타데이터는 생성일 2025-12-27, 스타 196, 포크 36입니다.

## 마크다운 모듈별 핵심 요약

### 0) 코스 허브 문서

#### README.md
- 링크: https://github.com/koki7o/claude-code-for-beginners/blob/main/README.md
- 핵심: 코스의 전체 내비게이션 역할을 하는 문서입니다.
- 내용 요약: 무료 코스라는 포지셔닝, 학습 대상, 학습 목표, 1~15 모듈 개요, 사전 준비물, 추가 학습 경로를 한 번에 안내합니다.
- 특징: 무료 본편과 유료 확장 패키지(Real Projects/Advanced Modules)를 분리 안내해서 학습 단계별 선택지가 명확합니다.

### 1) Core Modules (1~10)

#### Module 01 - Welcome to Claude Code
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-01-welcome-to-claude-code.md
- 핵심: Claude Code의 개념 이해와 초기 설치 온보딩에 집중합니다.
- 내용 요약: AI 페어 프로그래밍 개념 설명, Node.js/CLI/API Key 설치 절차, 기본 인터페이스 해설, 첫 Python 스크립트 실습까지 제공합니다.
- 실전 포인트: “처음 켜서 무엇을 입력해야 하는지”를 단계형으로 보여줘 입문 진입장벽을 낮춥니다.

#### Module 02 - Starting Your First Project
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-02-starting-your-first-project.md
- 핵심: 프로젝트 시작 전략을 4가지(신규/기존/템플릿/클론)로 분해합니다.
- 내용 요약: 각 접근법의 사용 시점, 진행 절차, 장단점을 비교하고 마지막에 선택 매트릭스로 정리합니다.
- 실전 포인트: 초급자가 상황별로 어떤 시작 방식을 택해야 하는지 의사결정 기준을 제공합니다.

#### Module 03 - Understanding Tools
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-03-understanding-tools.md
- 핵심: Claude Code 내부 도구(Read/Write/Edit/Glob/Grep/Bash/Task/Web/LSP)의 역할을 체계화합니다.
- 내용 요약: 도구별 사용 목적, 실제 조합 흐름, 디버깅/기능 추가 같은 시나리오별 툴 체인을 설명합니다.
- 실전 포인트: 사용자가 도구 사용을 명시적으로 가이드하는 프롬프트 패턴까지 포함해 통제력을 높입니다.

#### Module 04 - Working with Files and Code
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-04-working-with-files.md
- 핵심: 파일 읽기/쓰기/수정/리팩터링/탐색의 기본기를 다룹니다.
- 내용 요약: 기존 코드 이해법, 안전한 편집 단위, 중복 제거/함수 구조 개선/네이밍 개선 등 유지보수 중심 조언이 많습니다.
- 실전 포인트: “빠르게 고치기”보다 “기존 규칙을 깨지 않는 수정”을 강조해 팀 개발 적응에 유리합니다.

#### Module 05 - Prompt Engineering
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-05-prompt-engineering.md
- 핵심: 결과 품질을 좌우하는 프롬프트 작성법을 모듈화합니다.
- 내용 요약: 좋은/나쁜 프롬프트 비교, 기술 스택·아키텍처·스타일 요구 명시법, 반복 개선(Iterative) 패턴을 다룹니다.
- 실전 포인트: Scaffold/Feature/Fix/Refactor/Test 패턴을 제시해 작업 유형별 템플릿처럼 재사용할 수 있습니다.

#### Module 06 - Background Agents
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-06-background-agents.md
- 핵심: Explore/Plan/General-purpose 에이전트 활용법과 병렬 실행 전략을 설명합니다.
- 내용 요약: 에이전트를 언제 쓰는지, 결과를 어떻게 해석하는지, 병렬 조사 시나리오에서 어떻게 합치는지까지 다룹니다.
- 실전 포인트: 복잡 작업을 “분해-위임-통합”으로 처리하는 사고방식을 학습할 수 있습니다.

#### Module 07 - Git Operations
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-07-git-operations.md
- 핵심: 커밋/브랜치/PR/리뷰/충돌해결까지 Git 워크플로를 Claude Code 관점으로 정리합니다.
- 내용 요약: 커밋 메시지 작성 규칙, 브랜치 네이밍, PR 본문 구조, gh CLI 연동 방법을 실습 중심으로 다룹니다.
- 실전 포인트: 단순 Git 명령 소개를 넘어서 “변경 검토 후 커밋” 루틴을 강조합니다.

#### Module 08 - Debugging and Testing
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-08-debugging-and-testing.md
- 핵심: 에러 해석과 테스트 자동화를 결합한 문제 해결 루틴을 제공합니다.
- 내용 요약: 에러 메시지 구조 해석, 체계적 디버깅 프로세스, 단위/통합 테스트, TDD, 커버리지 개선을 순서대로 다룹니다.
- 실전 포인트: null/async/로직/성능 이슈 같은 빈출 장애 패턴을 별도 시나리오로 묶어 학습 효율이 좋습니다.

#### Module 09 - Real-World Project
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-09-real-world-project.md
- 핵심: 종합 실습 모듈로, 실제 애플리케이션 구축 전 과정을 단계별로 안내합니다.
- 내용 요약: 기본 예제로 Task Management API를 선정하고 기획, DB 모델링, 인증, 검증, 에러 핸들링, 테스트, 문서화, 배포 준비까지 10개 페이즈로 전개합니다.
- 실전 포인트: 모듈별 지식을 단일 프로젝트로 통합해 “학습 → 실전 전환” 구간을 메웁니다.

#### Module 10 - Workflow Best Practices
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-10-workflow-best-practices.md
- 핵심: 코드 품질과 협업 생산성을 높이는 운영 습관을 정리합니다.
- 내용 요약: TodoWrite 기반 작업 관리, self-review, 문서화, 로깅, 보안, 성능 최적화, 개발자 마인드셋까지 포괄합니다.
- 실전 포인트: 기능 구현 능력 이후에 필요한 “지속 가능한 개발 방식”을 명시적으로 훈련시킵니다.

### 2) Advanced Modules (11~15)

#### Module 11 - MCP Servers
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-11-mcp-servers.md
- 핵심: Claude Code 확장을 위한 MCP 개념과 서버 운영 방법을 다룹니다.
- 내용 요약: `.mcp.json` 설정, Filesystem/DB/GitHub 서버, 그리고 실사용 가치가 높은 Context7·Playwright·DeepWiki 중심으로 설명합니다.
- 실전 포인트: “많이 설치”보다 “목적 맞는 핵심 서버 선택”을 강조해 운영 복잡도를 줄입니다.

#### Module 12 - Skills and Hooks
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-12-skills-and-hooks.md
- 핵심: Claude Code를 프로젝트/개인 성향에 맞게 커스터마이징하는 법을 정리합니다.
- 내용 요약: CLAUDE.md 메모리 구조, `.claude/rules` 경로 스코프, 5계층 설정 우선순위, skills/commands/hooks/custom agent 기초를 다룹니다.
- 실전 포인트: 팀 규칙을 Claude 행동으로 고정해 반복 설명 비용을 줄일 수 있습니다.

#### Module 13 - Languages and Frameworks
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-13-languages-and-frameworks.md
- 핵심: 언어/프레임워크가 달라져도 Claude Code 활용 패턴을 유지하는 방법을 제시합니다.
- 내용 요약: Python(Flask/Django/FastAPI), JS/TS(Node/React/Next), Go, Rust, Java를 다루며 다중 언어 프로젝트 전략도 포함합니다.
- 실전 포인트: 스택 전환 시에도 프롬프트 구조와 작업 분해 원칙을 재사용할 수 있게 설계되어 있습니다.

#### Module 14 - API Integration
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-14-api-integration.md
- 핵심: 외부 API 연동을 실무 수준으로 끌어올리는 모듈입니다.
- 내용 요약: WebSearch/WebFetch 기반 문서 조사, REST 통합, 인증(API Key/OAuth/JWT), Rate Limit 대응, 재시도(Exponential Backoff), API wrapper 작성까지 연결합니다.
- 실전 포인트: 단발성 호출이 아니라 “장기 운영 가능한 API 클라이언트 구조”를 목표로 합니다.

#### Module 15 - Production Deployment
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/module-15-production-deployment.md
- 핵심: 배포와 운영에 필요한 실제 체크리스트를 제공합니다.
- 내용 요약: Vercel/Heroku/VPS 배포 비교, Docker 멀티스테이지, CI/CD, 환경변수 관리, 모니터링, 프로덕션 마이그레이션 절차를 다룹니다.
- 실전 포인트: “코드 완성”이 아니라 “서비스 운영 가능 상태”를 완료 기준으로 제시합니다.

### 3) Supplement Modules

#### supplement-quick-reference.md
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/supplement-quick-reference.md
- 핵심: 자주 쓰는 명령과 프롬프트 패턴을 압축한 치트시트입니다.
- 내용 요약: 시작/종료, 슬래시 명령, 파일/코드/Git 작업 프롬프트, 패턴별 템플릿, 언어별 커맨드, 빠른 트러블슈팅을 한 문서에 모았습니다.
- 실전 포인트: 학습 단계보다 실무 중 빠른 참조에 특히 유용합니다.

#### supplement-troubleshooting.md
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/supplement-troubleshooting.md
- 핵심: 설치부터 런타임까지 발생 가능한 실패 케이스를 증상 기반으로 정리한 문서입니다.
- 내용 요약: command not found, 권한 오류, API key 문제, rate limit, 모듈 누락, Git/MCP 오류 등 상황별 진단 절차를 제공합니다.
- 실전 포인트: 오류 메시지 단위로 대응 순서를 제시해 초급자의 시행착오 시간을 크게 줄여줍니다.

#### supplement-challenge-solutions.md
- 파일: https://github.com/koki7o/claude-code-for-beginners/blob/main/supplement-challenge-solutions.md
- 핵심: 모듈 챌린지에 대한 해설과 평가 기준을 제공하는 자기점검 문서입니다.
- 내용 요약: 난이도별 도전 과제 풀이 힌트, 흔한 실수, 확장 연습 아이디어, 프롬프트 개선 팁을 포함합니다.
- 실전 포인트: 단순 정답지가 아니라 “왜 이 접근이 좋은가”를 비교 검토하게 만드는 구조입니다.

## 한 줄 정리
이 레포는 Claude Code를 빠르게 익히는 튜토리얼을 넘어서, 입문자가 실전 개발 루틴(기획-구현-검증-배포)까지 연결하도록 설계된 문서형 커리큘럼 저장소입니다.
