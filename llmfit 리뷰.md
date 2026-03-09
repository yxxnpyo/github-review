<div style="display:flex; flex-wrap:wrap; gap:10px; justify-content:center;">
  <a href="https://github.com/AlexsJones/llmfit" style="text-decoration:none;">
    <span style="display:inline-flex;align-items:center;gap:8px;padding:8px 14px;border-radius:999px;background:#111827;color:#ffffff;font-weight:700;">
      <span>Create Date</span>
      <span>2026-02-15</span>
    </span>
  </a>
  <a href="https://github.com/AlexsJones/llmfit/stargazers" style="text-decoration:none;">
    <span style="display:inline-flex;align-items:center;gap:8px;padding:8px 14px;border-radius:999px;background:#111827;color:#ffffff;font-weight:700;">
      <span>Star</span>
      <span>13,099</span>
    </span>
  </a>
  <a href="https://github.com/AlexsJones/llmfit/fork" style="text-decoration:none;">
    <span style="display:inline-flex;align-items:center;gap:8px;padding:8px 14px;border-radius:999px;background:#111827;color:#ffffff;font-weight:700;">
      <span>Fork</span>
      <span>733</span>
    </span>
  </a>
</div>
<div style="display:flex; flex-wrap:wrap; gap:8px; justify-content:center;">
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#000000;color:#ffffff;font-weight:700;"><img src="https://cdn.simpleicons.org/rust/white" alt="Rust" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Rust</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#3776AB;color:#ffffff;font-weight:700;"><img src="https://cdn.simpleicons.org/python/white" alt="Python" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Python</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#24C8DB;color:#ffffff;font-weight:700;"><img src="https://cdn.simpleicons.org/tauri/white" alt="Tauri" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Tauri</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#2496ED;color:#ffffff;font-weight:700;"><img src="https://cdn.simpleicons.org/docker/white" alt="Docker" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">Docker</span></span>
  <span style="display:inline-flex;align-items:center;gap:8px;padding:6px 12px;border-radius:4px;background:#2088FF;color:#ffffff;font-weight:700;"><img src="https://cdn.simpleicons.org/githubactions/white" alt="GitHub Actions" style="width:14px;height:14px;" /><span style="font-size:12px;color:#ffffff;">GitHub Actions</span></span>
</div>

<img src="https://raw.githubusercontent.com/AlexsJones/llmfit/main/demo.gif" alt="llmfit demo" style="max-width: 100%; width: 100%; height: auto; display: block;" />
*출처: README - demo.gif*

AlexsJones/llmfit은 "내 하드웨어에서 실제로 잘 도는 로컬 LLM이 무엇인가"를 빠르게 가려내기 위해 만든 Rust 기반 추천 도구입니다.
단순 모델 목록기가 아니라, RAM·VRAM·CPU·백엔드 정보를 읽고 각 모델을 점수화한 뒤 TUI, CLI, REST API, macOS 데스크톱 UI까지 같은 코어로 노출한다는 점이 핵심입니다.
특히 Apple Silicon의 통합 메모리, 다중 GPU, MoE(전문가 혼합) 구조, 양자화 차이까지 한 화면에서 다루려는 설계가 인상적입니다.
2026-03-09 조회 기준으로 GitHub 스타 13,099개, 포크 733개, 기본 브랜치 `main`, 최신 릴리즈 `v0.6.4`(2026-03-08), 오픈 이슈 29개, 오픈 PR 11개라서 반응 속도와 릴리즈 빈도 모두 꽤 높은 편입니다.
반면 문서 곳곳에 모델 수와 버전 정보가 서로 다르게 남아 있어, 도입 전에는 "코드는 빠르게 진화하지만 문서 정리는 조금 뒤처질 수 있다"는 전제를 갖고 보는 편이 좋습니다.

## 1. 🔍 핵심 기능 및 구현 방식

- **하드웨어 감지와 실행 경로 판별이 프로젝트의 출발점입니다.**
  `SystemSpecs::detect()`는 `sysinfo`로 RAM과 CPU를 읽고, NVIDIA `nvidia-smi`, AMD `rocm-smi` 또는 sysfs, Windows WMI, Intel Arc, Apple Silicon, Ascend NPU까지 순차적으로 수집해 가장 적합한 GPU를 고릅니다.
  같은 모델의 다중 GPU는 카드 수를 합산해 총 VRAM으로 계산하고, AMD APU나 NVIDIA GB10/GB20 계열처럼 통합 메모리 성격이 있는 장비는 전체 시스템 메모리로 재해석합니다.
  이 설계 덕분에 "GPU는 있는데 VRAM 추정이 이상한 머신"이나 "Apple/AMD 통합 메모리 장비"를 일반적인 CLI 툴보다 더 현실적으로 다룹니다.
  근거: `llmfit-core/src/hardware.rs`.

- **모델 적합도 계산은 단순한 RAM 비교가 아니라 실행 경로 선택과 양자화 선택을 같이 합니다.**
  `ModelFit::analyze_with_context_limit()`는 먼저 모델의 컨텍스트 길이와 시스템 메모리 풀을 보고, GPU 적재, MoE 오프로딩, CPU 오프로딩, CPU 전용 중 가장 나은 경로를 고릅니다.
  그다음 `best_quant_for_budget_with()`로 Q8_0에서 Q2_K까지 내려가며 "현재 하드웨어에서 가장 품질이 높은 양자화"를 다시 찾고, Apple Silicon이면 MLX 양자화 계층을 먼저 시도합니다.
  그래서 실무에서 어떤 가치가 생기는지 보면, 사용자는 모델 크기만 보고 감으로 고르지 않고 "이 장비에서 어느 양자화까지 무리 없이 갈 수 있는가"를 바로 판단할 수 있습니다.
  근거: `llmfit-core/src/fit.rs`, `llmfit-core/src/models.rs`.

- **점수 체계는 품질, 속도, 적합도, 컨텍스트를 분리해 가중합합니다.**
  `ScoreComponents`가 네 축을 분리하고, 속도는 가능하면 GPU 메모리 대역폭 기반으로 추정하며 그렇지 않으면 백엔드별 상수로 폴백합니다.
  README는 이 방식을 llama.cpp 공개 벤치마크와 실측 경험으로 설명하고, 코드도 Apple Silicon일 때 MLX와 llama.cpp 상대 속도를 비교해 노트에 남기도록 돼 있습니다.
  이 접근은 절대 벤치마크를 보장하지는 않지만, "모델이 뜨냐 마냐"를 넘어서 "이 조합이 왜 추천 1순위인지"를 설명 가능한 추천으로 바꿔줍니다.
  근거: `llmfit-core/src/fit.rs`, `README.md`.

- **추천기에서 끝나지 않고 계획 모드와 API로 이어집니다.**
  `plan.rs`는 반대로 특정 모델을 고정한 뒤 필요한 VRAM, RAM, CPU 코어, 목표 TPS를 역산해 최소/권장 사양과 업그레이드 델타를 만듭니다.
  `serve_api.rs`는 같은 코어 결과를 `/health`, `/api/v1/system`, `/api/v1/models`, `/api/v1/models/top`, `/api/v1/models/{name}`로 노출해 노드 단위 스케줄링 입력으로 사용할 수 있게 합니다.
  그래서 실무에서 어떤 가치가 생기는지 보면, 개인 사용자에게는 모델 추천기이고 팀 관점에서는 "노드별 로컬 LLM 배치 API"로도 쓸 수 있습니다.
  근거: `llmfit-core/src/plan.rs`, `llmfit-tui/src/serve_api.rs`, `API.md`.

- **런타임 제공자 계층과 데스크톱 UI도 같은 코어 위에 붙어 있습니다.**
  `ModelProvider` 트레이트는 설치 모델 조회와 pull 진행 상황 스트리밍을 표준화하고, 현재는 Ollama와 MLX 중심으로 구현돼 있습니다.
  macOS용 Tauri 앱은 `get_system_specs`, `get_model_fits`, `start_pull`, `poll_pull` 명령만 얇게 감싸 공통 코어 로직을 재사용합니다.
  즉 구현이 UI별로 찢어지지 않고, 코어 판단 로직을 한 번 만들고 인터페이스만 여러 개 얹는 구조라 유지보수성이 좋습니다.
  근거: `llmfit-core/src/providers.rs`, `llmfit-desktop/src/main.rs`, `llmfit-tui/src/main.rs`.

정리하면 이 프로젝트의 강점은 "모델 카탈로그 + 하드웨어 감지 + 추천 점수 + API 노출"이 하나의 실행 흐름으로 묶여 있다는 점입니다.
특히 `data/hf_models.json`을 바이너리에 내장해 외부 서비스가 끊겨도 기본 추천 기능은 유지되고, TUI·CLI·API·Desktop이 같은 데이터 모델을 공유해 기능 분산이 적습니다.
다만 README의 프로젝트 구조 설명은 아직 `src/` 단일 구조를 기준으로 적혀 있지만 실제 저장소는 `llmfit-core`, `llmfit-tui`, `llmfit-desktop` 워크스페이스라서, 문서만 읽고 구조를 이해하면 약간 헷갈릴 수 있습니다.

## 2. 💡 이 프로젝트는 왜 필요한가요?

- **기존 방식의 문제는 모델 선택이 너무 감각적이라는 점입니다.**
  로컬 LLM을 처음 쓰는 사람은 보통 "7B면 내 PC에 되나", "Apple Silicon에서는 MLX가 낫나", "MoE는 총 파라미터를 봐야 하나 활성 파라미터를 봐야 하나" 같은 질문에서 막힙니다.
  README가 강조하듯 이 프로젝트는 RAM, CPU, GPU, VRAM, 백엔드 차이를 실제 추천 과정에 넣어 이 모호함을 줄이려 합니다.
  근거: `README.md`, `llmfit-core/src/fit.rs`, `llmfit-core/src/hardware.rs`.

- **차별점은 정적 표보다 '실행 경로 중심'으로 생각한다는 점입니다.**
  단순 모델 리스트 서비스는 파라미터 수나 벤치마크만 보여주지만, llmfit은 GPU 적재, CPU 오프로딩, MoE 오프로딩, CPU-only를 서로 다른 실행 모드로 취급합니다.
  여기에 양자화 선택까지 같이 묶으니 "이 장비에서 이 모델이 가능하다"가 아니라 "어떤 조건에서 어떻게 가능하다"로 답이 바뀝니다.
  그래서 실무에서 어떤 가치가 생기는지 보면, 하드웨어 구매 전 검토나 노드 스케줄링 규칙 설계에 바로 쓸 수 있는 설명형 출력이 생깁니다.
  근거: `llmfit-core/src/fit.rs`, `llmfit-core/src/plan.rs`.

README는 메모리 대역폭 기반 속도 추정과 공개 벤치마크 참조를 함께 제시해 점수 산정의 논리를 설명합니다.
프로젝트 측 설명대로라면 인퍼런스 속도는 모델 크기와 메모리 대역폭에 크게 좌우되며, 이 가정을 코드에서도 실제 GPU 이름 매핑으로 풀어냅니다.
정량 수치는 어디까지나 프로젝트 측 추정치이므로 절대 성능 보장으로 읽으면 안 되지만, 최소한 "왜 이 순서로 추천했는가"를 납득시키는 설명은 충분합니다.

문서상 비전과 운영 신호를 따로 보면 더 흥미롭습니다.
저장소는 2026-02-15에 생성됐는데 2026-03-09 기준 스타 13,099개, 포크 733개, 최근 push 2026-03-09, 최신 릴리즈 `v0.6.4`가 2026-03-08에 발행돼 초기 프로젝트치고 반응이 매우 빠릅니다.
또 오픈 이슈 29개와 오픈 PR 11개가 남아 있어 사용자의 요구와 외부 기여가 활발한 편입니다.
반대로 `CHANGELOG.md` 최상단은 아직 `0.3.7`이고, README는 모델 데이터가 206개라고 쓰지만 실제 JSON은 536개여서 릴리즈/문서 싱크는 운영 리스크로 봐야 합니다.

## 3. 🎯 어떤 상황에서 쓰면 좋을까요?

이 도구는 "모델 추천기"로만 보면 절반만 보는 셈입니다.
개인 개발자에게는 하드웨어 맞춤 탐색기이고, 팀에게는 노드 상태를 표준화해 주는 로컬 추론 운영 도구에 더 가깝습니다.
그래서 실무에서 어떤 가치가 생기는지 기준으로 보면, 추론 비용을 줄이는 단계와 장비 기획 단계 모두에서 쓸모가 있습니다.

- **시나리오 1. 개인 노트북이나 워크스테이션에서 첫 로컬 LLM을 고를 때.**
  상황: 16GB RAM 또는 24GB VRAM 같은 제한된 장비에서 "어떤 모델이 실제 usable한가"를 빠르게 알고 싶습니다.
  적용 방법: `llmfit` 또는 `llmfit --cli`, 필요하면 `--memory`, `--max-context`를 줘서 현재 장비 기준 추천을 받습니다.
  기대 결과: 막연한 모델 비교가 아니라 실행 가능성, 예상 tok/s, 적정 양자화까지 한 번에 보게 됩니다.

- **시나리오 2. 여러 노드에 로컬 추론을 분산 배치할 때.**
  상황: 팀이 여러 대의 GPU 머신을 가지고 있고, 각 노드마다 어떤 모델을 태울지 중앙 스케줄러가 판단해야 합니다.
  적용 방법: 각 노드에서 `llmfit serve`를 띄우고 `/api/v1/models/top?min_fit=good` 같은 엔드포인트를 중앙에서 수집합니다.
  기대 결과: 장비별 모델 적합도와 필터 결과를 공통 JSON 스키마로 가져와 배치 자동화나 대시보드 입력으로 쓸 수 있습니다.

- **시나리오 3. 장비 업그레이드나 모델 전환 계획을 세울 때.**
  상황: 특정 모델을 쓰고 싶은데 VRAM을 얼마나 더 사야 하는지, MLX와 llama.cpp 중 어느 경로가 맞는지 판단이 필요합니다.
  적용 방법: `plan` 기능으로 목표 컨텍스트와 TPS를 넣고 최소/권장 사양, 업그레이드 델타를 확인합니다.
  기대 결과: 추상적인 "큰 GPU가 필요하다"가 아니라 VRAM, RAM, CPU 코어 단위의 계획표가 나옵니다.

이 유스케이스가 중요한 이유는, 대부분의 로컬 LLM 도입 실패가 모델 품질 자체보다 "장비와 실행 경로를 잘못 고른 것"에서 시작되기 때문입니다.
llmfit은 바로 그 실패 지점을 흡수하려는 도구입니다.
다만 현재 provider 구현이 Ollama/MLX 중심이라, vLLM 같은 서버형 런타임을 즉시 제어하는 오케스트레이터로 보기에는 아직 확장 여지가 남아 있습니다.

## 4. 🚀 어떻게 사용하나요?

- **가장 빠른 온라인 시작은 패키지 설치 후 TUI 또는 CLI를 바로 여는 방식입니다.**
  Homebrew라면 `brew install llmfit`, Windows라면 `scoop install llmfit`가 가장 간단합니다.
  설치 뒤 `llmfit`으로 TUI를 열거나 `llmfit --cli`, `llmfit recommend --json --limit 5`, `llmfit serve --host 0.0.0.0 --port 8787`로 용도별 진입이 가능합니다.
  최신 릴리즈 기준은 `v0.6.4`입니다.

```sh
brew install llmfit
llmfit
llmfit --cli
llmfit recommend --json --limit 5
llmfit serve --host 0.0.0.0 --port 8787
```

- **소스 기준으로 쓰고 싶다면 워크스페이스 전체를 가져와 빌드합니다.**
  저장소는 Rust 워크스페이스이며 기본 멤버가 `llmfit-core`, `llmfit-tui`라서 일반적인 `cargo build --release` 흐름으로 CLI/TUI를 만들 수 있습니다.
  모델 데이터는 `data/hf_models.json`이 `include_str!`로 컴파일 시점에 포함되므로, 빌드만 끝나면 추천/계획/API 기능 자체는 외부 호출 없이도 동작 가능합니다.
  다만 `scripts/scrape_hf_models.py`로 모델 목록을 갱신하거나, Ollama에서 새 모델을 pull 하거나, quick install 스크립트로 바이너리를 받는 단계는 온라인 접근이 필요합니다.

```sh
git clone --depth 1 --branch v0.6.4 https://github.com/AlexsJones/llmfit.git
cd llmfit
cargo build --release
./target/release/llmfit --cli
```

- **폐쇄망에서도 조건부로 사용 가능합니다.**
  가능 조건은 "바이너리 또는 소스와 의존성이 이미 내부에 있고, 로컬 런타임도 사내망에서 접근 가능하다"입니다.
  내부 Git 미러가 있다면 `v0.6.4` 태그 기준으로 미러에서 clone 하고, 바이너리 배포가 더 쉽다면 릴리즈 tarball을 내부 아티팩트 저장소에 복사해 두는 편이 현실적입니다.
  Cargo를 오프라인으로 빌드하려면 `cargo vendor` 결과물이나 내부 crate mirror가 필요하고, 그렇지 않으면 소스 빌드는 막힙니다.

```sh
# 예시: 내부 Git 미러에서 소스 확보
git clone --depth 1 --branch v0.6.4 ssh://git.internal/AlexsJones/llmfit.git

# 예시: 내부 아티팩트 저장소에서 미리 받아둔 바이너리 사용
tar -xzf llmfit-v0.6.4-x86_64-unknown-linux-musl.tar.gz
./llmfit --cli
```

온라인과 폐쇄망의 차이는 분명합니다.
온라인에서는 quick install, 릴리즈 다운로드, HuggingFace 스크레이프, Ollama 원격 태그 확인이 모두 가능하지만, 폐쇄망에서는 내장된 모델 DB와 이미 설치된 로컬 런타임 범위 안에서만 동작합니다.
즉 추천·계획·API는 비교적 오프라인 친화적이지만, 모델 목록 최신화와 새 모델 다운로드는 내부 미러가 없으면 사실상 어렵습니다.
환경 변수는 `OLLAMA_HOST`, `MLX_LM_HOST`, `OLLAMA_CONTEXT_LENGTH` 정도만 알면 되고 별도 API 키는 필요하지 않습니다.

## 5. ✨ 사용하면 무엇이 좋아지나요?

- **개발 효율이 올라갑니다.**
  보통 로컬 LLM 도입에서는 모델 카드, 벤치마크 글, 커뮤니티 추천을 오가며 손으로 비교표를 만듭니다.
  llmfit은 이 과정을 TUI/CLI/API 한 세트로 묶어 탐색 비용을 줄입니다.
  그래서 실무에서 어떤 가치가 생기는지 보면, "지금 이 장비에서 바로 돌릴 수 있는 후보군"을 빠르게 좁혀 실험 주기를 줄여줍니다.

- **성능과 운영 판단을 같은 기준으로 보게 됩니다.**
  추천 점수, 예상 tok/s, 실행 모드, 양자화, 컨텍스트, fit level을 한 화면에 보여주기 때문에 성능과 안정성을 분리하지 않고 판단할 수 있습니다.
  REST API까지 같은 스키마로 제공되니 수작업 표 대신 자동화 파이프라인에 넣기 쉽습니다.
  CI도 Linux/macOS/Windows 매트릭스에서 test, fmt, clippy, cargo check를 돌리고, 릴리즈 워크플로는 다중 플랫폼 바이너리와 Docker 이미지를 함께 배포해 배포 체계가 생각보다 단단합니다.

- **리스크와 비용도 줄일 수 있습니다.**
  무리한 모델 선택으로 시간을 버리거나, 장비 증설 전에 정확한 필요 사양을 모른 채 구매하는 일을 줄여줍니다.
  특히 `plan` 모드는 "GPU 하나 더 필요한가, 컨텍스트를 줄이면 되나, CPU-only로도 버틸 수 있나" 같은 질문을 숫자로 바꿔줍니다.
  로컬 런타임 설치 모델 감지와 pull 진행률 스트리밍까지 붙어 있어, 추천 후 실제 실행까지 이어지는 간격도 짧습니다.

좋아지는 점만 있는 것은 아닙니다.
첫째, 점수는 어디까지나 휴리스틱이라 실제 프롬프트, KV 캐시 정책, 백엔드 최적화 수준에 따라 체감 성능이 달라질 수 있습니다.
둘째, 문서 싱크가 완벽하지 않습니다.
`MODELS.md`는 106개 모델을 말하고 README는 206개라 쓰지만 실제 `hf_models.json`은 536개 엔트리라, 문서만 보고 자동화 연동을 설계하면 혼선이 생길 수 있습니다.
셋째, provider 계층은 잘 열어뒀지만 현재 즉시 체감되는 통합은 Ollama/MLX 쪽이 강하고, 서버형 추론 엔진은 아직 "확장 가능한 방향"에 더 가깝습니다.

에코시스템 관점에서는 확장성이 좋습니다.
Rust 코어를 TUI, Axum API, Tauri 데스크톱으로 공유하고, Docker 및 Homebrew 릴리즈 자동화까지 연결돼 있어 "작은 유틸리티"에서 "운영 가능한 툴"로 넘어갈 기반이 이미 보입니다.
반대로 빠른 성장 속도 때문에 문서 최신화가 뒤따라오지 못하는 구간이 보이므로, 도입 시에는 릴리즈 태그와 실제 코드 경로를 우선 신뢰하는 편이 안전합니다.

**도입 판단 한줄 정리:** 로컬 LLM을 감으로 고르지 않고 하드웨어 적합도 중심으로 체계화하고 싶다면 매우 매력적이지만, 문서 수치보다 실제 릴리즈와 코드 상태를 기준으로 읽어야 하는 프로젝트입니다.

---

### 근거 메모

- 저장소 메타: GitHub API 조회 기준 `created_at=2026-02-15`, `default_branch=main`, `stargazers_count=13099`, `forks_count=733`, `updated_at=2026-03-09`, `open issues=29`, `open PRs=11`.
- 최신 릴리즈: GitHub Releases API 기준 `v0.6.4`, published at `2026-03-08`.
- 핵심 코드 경로: `llmfit-core/src/hardware.rs`, `llmfit-core/src/fit.rs`, `llmfit-core/src/models.rs`, `llmfit-core/src/plan.rs`, `llmfit-core/src/providers.rs`, `llmfit-tui/src/serve_api.rs`, `llmfit-desktop/src/main.rs`.
- 운영 근거: `.github/workflows/ci.yml`, `.github/workflows/release.yml`, `.github/workflows/docker.yml`.
- 검증 한계: 이 환경에는 `cargo` 실행기가 없어 로컬 `cargo test`는 직접 돌리지 못했고, 대신 저장소의 CI 정의와 `scripts/test_api.py`를 근거로 테스트 체계를 확인했습니다.
