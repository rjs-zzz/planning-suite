# Planning Suite — P1~P6 기획 체인 + Orchestrator

[English README](README.en.md)

중장기 발전계획·경영계획·사업계획 등 **계획서 수립 업무 전체**를
6단계로 쪼개 순차 실행하고, 단계 간 정합을 자동 검증하는 Claude Skill
7종 세트입니다.

공공기관·관공서의 **중장기 발전계획, 경영계획, 부서 업무계획** 작성에
특히 유용합니다 — 환경분석부터 목표 체계, 전략과제, 타당성 검토,
로드맵, 환류 체계까지 기재부·행안부 보고서 양식 그대로 이어서
산출하고, 단계별 정합성을 자동으로 검증합니다.

## 구성

| Phase | 스킬 | 산출물 |
|:---:|---|---|
| P1 진단 | `env-scanning-loop-p1` | 환경분석 (PEST·3C·VRIO·SWOT) |
| P2 목표 | `goal-setting-loop-p2` | 미션·비전·OKR·KPI 체계 |
| P3 전략 | `strategy-option-loop-p3` | 전략과제 풀·ERRC·우선순위 |
| P4 검토 | `feasibility-review-loop-p4` | 타당성 스크리닝 (경제성·법률·자원) |
| P5 실행 | `roadmap-design-loop-p5` | 로드맵·RACI·KPI 전개 |
| P6 환류 | `feedback-loop-p6` | 모니터링·컨틴전시·차기 사이클 인계 |
| — | `planning-orchestrator` | **관제 계층** — 위 6단계를 연결·검증·회귀 통제 |

`planning-orchestrator`는 콘텐츠를 직접 생산하지 않는 유일한
비생산 스킬입니다. 각 Phase 스킬의 산출물을 받아 **Q-Gate**(스키마·
이월·상호참조·회귀신호 4종 검증)를 거치게 하고, 문제가 있으면 정확히
어느 단계로 되돌려야 하는지(재전파 범위 한정)까지 판정합니다.

## 빠른 시작

**1. 설치** — 아래 [설치](#설치). Claude.ai는 필요한 스킬의 zip만 올리면 됩니다.

**2. 요청** — 평소 말투로 쓰면 됩니다. 발동하지 않으면 스킬 이름을 붙이세요.

- 계획서 전체: "planning-orchestrator로 우리 기관 3개년 발전계획 만들어줘."
- 한 단계만: "우리 부서 SWOT 분석해줘" / "내년 OKR 잡아줘" / "이 과제 5개 타당성 검토해줘" / "로드맵 만들어줘"

**3. 준비물** — 꼭 필요한 것은 **무슨 계획인지, 어느 조직인지, 기간** 세 가지입니다.
아래 자료는 있는 만큼만 주면 됩니다. 없으면 스킬이 가정으로 채우고 표시합니다.

| 자료 | 쓰는 단계 | 없으면 |
|---|---|---|
| A 방침 — 미션·비전, 상위 계획 | P1·P2 | 신규 수립으로 가정 |
| B 실적 — 최근 실적·현황 수치 | P1·P2·P6 | 내부 분석은 추정, 목표 기준선은 "확인 필요" (수치를 지어내지 않음) |
| C 현행 사업·업무 목록 | P3 | 줄이거나 없앨 사업을 일반 관행으로 가정 |
| D 재무 — 예산서·견적, 가용 상한 | P3·P4·P5 | 타당성 검토는 금액 없이 등급 판정, 총량 판정 보류 |
| E 조직도·고정 일정 | P5 | 담당을 역할 명칭으로 표기 (부서명을 지어내지 않음) |
| F 적용 법령·내부 규정 | P4 | 일반 법령만 식별, "확인 필요" 표기 |
| G 기존 점검 체계 | P6 | 표준 점검 체계로 설계 |

"준비물 양식 줘"라고 하면 채워 넣을 양식을 보여줍니다. 스킬은 **필수가 빠졌을 때만** 멈춰서 묻고,
산출물 첫머리에 **입력 현황**과 **바뀌면 결론이 달라지는 입력 3개**를 적습니다.
→ [통합 모드 입력 점검 예시](examples/03_intake-통합모드.md)

**4. 조직 맞춤** — 아래 블록을 한 번 써서 대화에 붙여넣으세요. 7개 스킬이 모두 같은 블록을 읽습니다.
(프로젝트 지침처럼 매번 자동으로 들어가는 곳에 저장해 두면 다시 붙여넣지 않아도 됩니다.)

```
[조직 프로필]
- 조직 유형: (예: 공공기관 / 지자체 / 민간기업 / 병원 / 학교 / 개인)
- 상급·감독 기관:
- 자주 걸리는 법령·규정:
- 평가·감사 체계: (예: 경영평가, 감사, 이사회 보고)
- 승인 경로: (예: 부서장 전결 → 경영진 → 이사회)
- 주요 이해관계자:
- 예산 주기·고정 일정:
```

**최신 동향 탐색** — 실행할 때마다 관련 신규 이론·연구·사례·제도를 인터넷에서 찾아 반영합니다.

- 검색어에는 내부 안건명·기관명·금액을 넣지 않습니다. 일반화해도 사안이 드러나면 검색하지 않습니다.
- 실제로 연 출처만 싣고, 건마다 **반영 / 참고 / 기각**을 판정해 붙임에 남깁니다. 새롭다는 이유만으로 채택하지 않습니다.
- 웹 검색이 꺼져 있으면 "탐색 불가"로 표시하고 진행합니다 (기억으로 지어내지 않음).
- 건너뛰려면 "탐색 생략", 더 넓게 찾으려면 "탐색 강화"라고 하세요.

**주의** — 개인정보·대외비는 가리고 올리세요. 산출물은 판단을 돕는 자료입니다.
한 모델이 분석과 검증을 함께 하므로, 중요한 계획서는 `red-team`(problem-solver 플러그인)으로 독립 검증을 받고 사람이 다시 확인해야 합니다.

## 자가개선 체계

스킬이 스스로 고치지 않습니다. **탐색 → 개선안 → 시험 → PR → 승인** 순서로만 바뀝니다.

| 구성 | 위치 | 역할 |
|---|---|---|
| 지식 원장 | `shared/knowledge_ledger.md` | 검증된 최신 동향을 쌓아 두고, 탐색이 먼저 읽어 중복 검색을 줄임 |
| 개선 후보 | 실행 결과 끝의 `○ 스킬 개선 후보` → `improvement_inbox.md` | 실행 중 발견한 "스킬보다 나은 방법"을 모음 |
| 시험 사례 | `evals/` | 변경 전후를 같은 사례로 비교해 "나빠지지 않았는지" 판정 |
| 월간 점검 | `MAINTENANCE.md` | 신규 방법 탐색 → 개선안 → 시험 → PR. main에 직접 올리지 않음 |

실행 결과에 `○ 스킬 개선 후보`가 나오면 `improvement_inbox.md`에 붙여 두세요. 월간 점검이 모아서 검토합니다.

## 왜 이 세트인가

기획 업무에서 가장 흔한 실패는 두 가지입니다: **단계 사이에서 앞
단계 결과물이 유실되는 것**, 그리고 **하나의 기획 요청에 여러 Phase가
섞여 있어 어디서부터 손대야 할지 불분명한 것**. 이 세트는 그 두
문제를 정면으로 다룹니다.

- **단계별 완결 스킬**: P1~P6 각각 단독으로도 실행 가능 — 필요한
  Phase 하나만 골라 써도 됩니다.
- **[DATA-P○-OUTPUT] 인터페이스**: P2~P6은 다음 단계가 바로 이어
  받을 수 있는 구조화 블록을 출력합니다. 세션이 끊겨도 이 블록을
  다음 세션에 붙여넣으면 이어집니다. (P1은 서술 맥락이 산출물의
  본질이므로 블록 대신 환경분석 보고서 자체가 인계 단위입니다 —
  orchestrator의 Q-Gate도 P1만 유연 검증으로 처리합니다.)
- **Q-Gate + 회귀 통제**: orchestrator가 있으면 "P5 실행 단계에서
  발견된 문제가 사실 P2 목표 설정 오류였다"는 상황에서, 전체를
  다시 하지 않고 영향받는 부분만 재작업하도록 범위를 좁혀줍니다.
- **가정 전환 프로토콜**: 실행 중 정보가 부족해도 멈추지 않고
  `[가정 A1...]`로 라벨링한 뒤 계속 진행 — 확신도와 근거를 함께
  기록해 사용자가 완성본을 보고 사후 교정할 수 있게 합니다.

## 설치

> **Claude · ChatGPT · Gemini 통합 설치 안내** (problem-solver와 공용) — [INSTALL.md](https://github.com/rjs-zzz/problem-solver/blob/main/INSTALL.md)

### Claude Code — 한 줄 설치 (권장)

```
/plugin marketplace add rjs-zzz/planning-suite
/plugin install goal-setting-loop-p2@planning-suite
```

필요한 Phase만 골라 설치하세요. 계획서 전체를 쓸 때는 관제 스킬 하나만 설치하면
**P1~P6가 의존성으로 함께 설치**됩니다.

```
/plugin install planning-orchestrator@planning-suite
```

설치 가능한 플러그인: `env-scanning-loop-p1` `goal-setting-loop-p2`
`strategy-option-loop-p3` `feasibility-review-loop-p4` `roadmap-design-loop-p5`
`feedback-loop-p6` `planning-orchestrator` `visual-presenter`

설치한 뒤 새 버전을 받으려면 `/plugin` → **Marketplaces** 탭에서 `planning-suite`를 고르고
**Update marketplace**를 누릅니다. 같은 화면에서 **Enable auto-update**를 켜 두면 이후에는 자동으로 갱신됩니다
(외부 마켓플레이스는 자동 갱신이 기본으로 꺼져 있습니다).

#### Claude Code에서 달라지는 점

| 항목 | 웹·데스크톱 채팅 | Claude Code |
|---|---|---|
| 부르기 | "goal-setting-loop-p2로 …" | 같은 문장, 또는 `/goal-setting-loop-p2:goal-setting-loop-p2 …` |
| 단계 잇기 | `[DATA-PN-OUTPUT]` 블록 복사·붙여넣기 | 붙여넣기 없음 — 산출물이 `outputs/`에 저장되고 다음 단계가 찾아 읽음 |
| 세션이 끊겼을 때 | 마지막 `[ORCH-STATE]` 붙여넣기 | `outputs/ORCH-STATE_<사안>.md`에서 자동 복원 |
| P1 정밀 모드 | 불가 (정식으로 대체) | PEST·3C·VRIO 하위 에이전트(`pest-analyst`·`market-analyst`·`vrio-analyst`) 병렬 독립 실행 + 델타 분석 |
| 시각화 | 응답 안 미리보기 | `outputs/…v1.html` 단일 파일로 저장 |

- 작업 폴더 하나를 사안별로 쓰면 산출물·상태 원장이 한곳에 쌓입니다. 기존 파일은 덮어쓰지 않고 `_v2`로 올립니다
  (상태 원장 `ORCH-STATE_<사안>.md`만 예외로 갱신).
- 독립 검증은 problem-solver 저장소의 `/problem-solver:red-team-run outputs/계획서.md`로 돌립니다.

### Claude.ai (웹/데스크톱/모바일) — 필요한 파일만 올리기

1. 아래에서 필요한 zip을 내려받습니다.
2. Skills 업로드 화면에서 그 zip을 그대로 올립니다 — 압축을 풀거나 다시 묶지 않습니다
   (메뉴 경로는 플랜·시점에 따라 다릅니다 — [공식 안내](https://support.claude.com/en/articles/12512180) 참조).

| 하려는 일 | 올릴 파일 |
|---|---|
| 환경분석·SWOT | [`dist/env-scanning-loop-p1.zip`](dist/env-scanning-loop-p1.zip) |
| 목표·OKR·KPI | [`dist/goal-setting-loop-p2.zip`](dist/goal-setting-loop-p2.zip) |
| 전략과제 발굴 | [`dist/strategy-option-loop-p3.zip`](dist/strategy-option-loop-p3.zip) |
| 타당성 검토 | [`dist/feasibility-review-loop-p4.zip`](dist/feasibility-review-loop-p4.zip) |
| 로드맵·RACI | [`dist/roadmap-design-loop-p5.zip`](dist/roadmap-design-loop-p5.zip) |
| 환류·모니터링 | [`dist/feedback-loop-p6.zip`](dist/feedback-loop-p6.zip) |
| 계획서 전체 (위 6개와 함께) | [`dist/planning-orchestrator.zip`](dist/planning-orchestrator.zip) |
| 결과 시각화 (표·도식·모션) | [`dist/visual-presenter.zip`](dist/visual-presenter.zip) |

새 버전이 나오면 같은 zip을 다시 내려받아 올리면 됩니다. 변경 내용은 [CHANGELOG](CHANGELOG.md)에 있습니다.

### ChatGPT · Gemini — 같은 zip 사용

ChatGPT와 Gemini도 같은 SKILL.md 형식(Agent Skills 표준)을 읽습니다. 위 표의 zip을 그대로 씁니다.

| AI | 방법 |
|---|---|
| ChatGPT | Skills → Create → Upload from your computer에서 필요한 zip을 올립니다 |
| Gemini (웹 gemini.google.com · Mac 앱) | 설정 → 스킬 → 업로드. zip이 거부되면 압축을 풀어 스킬 폴더를 올립니다 |
| Gemini CLI | `gemini skills install https://github.com/rjs-zzz/planning-suite.git --path plugins/<이름>/skills/<이름>` (예: `<이름>` = `goal-setting-loop-p2`) |

- 부를 때: ChatGPT는 `@스킬명`, Gemini는 `/스킬명`. 요청이 스킬 설명과 맞으면 알아서 고르기도 합니다.
- ChatGPT 데스크톱 앱은 Plugins → Add → Add a marketplace에 `rjs-zzz/planning-suite`를 넣는 방법도 있습니다
  (OpenAI 문서상 Claude 형식 마켓플레이스와 호환). 웹·모바일에서 보이는지는 플랜·시점에 따라 다릅니다.
- 다른 회사 AI로 문서를 옮길 때는 기관명·인명을 일반화하고 대외비 원문은 넣지 않습니다.
- 메뉴 이름과 지원 범위는 각 회사의 개편에 따라 바뀝니다 (2026-11 기준).
- 단계 사이 인계는 `[DATA-PN-OUTPUT]` 블록을 다음 단계 대화에 붙여 넣으면 되므로 AI 종류와 무관하게 동작합니다.

### 설치 전에 결과물부터 보기

[`examples/`](examples/) 에 실제 산출물 예시가 있습니다.

- [P2 목표 체계 산출물 + DATA 블록](examples/01_p2-goal-system.md)
- [Q-Gate Rollback 판정과 재전파 범위 한정](examples/02_qgate-rollback.md)
- [통합 모드 입력 점검 — 자료를 한 번에 요청](examples/03_intake-통합모드.md)

## 사용법

**계획서 전체가 필요할 때**: `planning-orchestrator`를 호출하면
현재 가진 자산(이미 환경분석이 있는지 등)을 확인해 시작점을 제안하고,
Phase 스킬을 순서대로 불러와 실행합니다.

**한 단계만 필요할 때**: 해당 Phase 스킬을 직접 호출하세요.
예: "우리 조직 SWOT 분석해줘" → `env-scanning-loop-p1`이 단독 발동.

**단일 결론(선택·가부 판단)만 필요할 때**: 이 세트가 아니라
별도 공개된 `problem-solver` 스킬을 쓰세요. 계획 문서의 한 파트가
아니라 "A냐 B냐" 같은 단발 판단이 목적이면 그쪽이 맞습니다.
계획 작업 중 단일 판정이 필요하면 Phase 스킬이 problem-solver로 위임하고,
반환된 `[DATA-PS-OUTPUT]` 블록을 해당 쟁점 판정으로 승계합니다
(사용자 요청 또는 One-way 쟁점 한정 — 위임 남발 방지).

**완성된 계획서를 독립 검증할 때**: `problem-solver` 플러그인에 함께 든 `red-team` 스킬을 쓰세요.
Q-Gate는 같은 대화·같은 모델이 하는 단계 간 정합 점검이라 독립 검증이 아닙니다.
완성본을 **프로젝트 밖 새 대화**에 붙여넣고 **작성과 다른 모델**로 "red-team으로"라고 하면
5인 반대자 패널이 문서만 보고 공격·판정해 `[RT-RETURN]`을 돌려줍니다 (문서를 고쳐 쓰지는 않음).

## 구조

```
planning-suite/
├── .claude-plugin/
│   └── marketplace.json          # 마켓플레이스 카탈로그 (8개 플러그인)
├── plugins/
│   ├── env-scanning-loop-p1/
│   │   ├── .claude-plugin/plugin.json
│   │   ├── agents/               # Claude Code 전용 — PEST·3C·VRIO 병렬 분석 하위 에이전트
│   │   └── skills/env-scanning-loop-p1/
│   │       ├── SKILL.md
│   │       ├── agents/           # PEST·3C(market)·VRIO 서브 에이전트, orchestrator 로직
│   │       └── templates/
│   ├── goal-setting-loop-p2/…    # references/ (BSC·OKR·Golden Circle) + templates/
│   ├── strategy-option-loop-p3/… # references/ (포트폴리오 매트릭스·ERRC·스코어링)
│   ├── feasibility-review-loop-p4/…
│   ├── roadmap-design-loop-p5/…  # references/ (마일스톤·RACI·KPI 설계)
│   ├── feedback-loop-p6/…        # references/ (PDCA·이중고리 학습·컨틴전시)
│   └── planning-orchestrator/
│       └── skills/planning-orchestrator/
│           ├── references/       # Q-Gate 체크리스트, 통합 입력 점검(intake_master.md)
│           └── templates/        # 관제 대시보드 템플릿
├── dist/                         # Claude.ai 업로드용 zip 8개 (tools/build_dist.py로 생성)
├── examples/                     # 산출물·입력 점검 예시
├── COMMON_CONTROLS.md            # 공통 실행 통제 블록 단일 원본
├── shared/frontier_scan.md        # 최신 동향 탐색 공통 절차 원본 (각 스킬 references/로 복사)
├── CLAUDE.md                     # 저장소 작업 규칙 (Claude Code로 고칠 때 자동으로 읽힘)
└── tools/                        # sync_common.py · build_dist.py · build_evals.py · validate.py
```

## 사용 시 참고

- **조직 맥락 커스터마이징**: 가장 쉬운 방법은 위 `[조직 프로필]` 블록을 대화에 붙여넣는
  것입니다. 조직 전체가 같은 기준을 쓰게 하려면 각 SKILL.md의 "상시 적용 맥락" 섹션을
  소속 조직의 법령명·이해관계자 맵·평가 체계로 고쳐 배포하세요.
- **자주 막히는 지점**
  - 스킬이 발동하지 않는다 → 요청 앞에 스킬 이름을 붙입니다 (예: "goal-setting-loop-p2로").
  - 질문 없이 바로 받고 싶다 → "없이 진행" 또는 "약식으로"라고 합니다.
  - 엉뚱한 단계가 돈다 → 원하는 산출물 이름(환경분석·OKR·로드맵 등)을 요청에 적습니다.
  - 세션이 끊겼다 → 마지막 `[DATA-P○-OUTPUT]`·`[ORCH-STATE]` 블록을 새 대화에 붙여넣습니다
    (Claude Code는 `outputs/`에서 스스로 이어받습니다).
- **저장소를 고친 뒤**: `python tools/sync_common.py` → `python tools/build_dist.py` →
  `python tools/build_evals.py` → `python tools/validate.py` 순으로 실행하면 공통 블록·zip·시험 사례·버전·
  매니페스트가 맞는지 확인됩니다. GitHub Actions가 push·PR마다 같은 점검을 돌립니다.
- **BSC 관점**: P2는 기본값이 공공형 BSC(공공가치 최상위)입니다.
  영리·수익사업 맥락이면 SKILL.md에서 표준형 BSC(재무 최상위)로
  전환하도록 안내되어 있습니다.
- **언어**: 7개 스킬 모두 한국어 전용입니다. 지시문·프레임워크 규칙·
  출력 템플릿이 한국어로 작성되어 있고, 출력 형식도 한국 정부 보고서
  양식(두괄식·개조식·【 】 제목 체계)을 따릅니다. 영어 환경에서
  쓰려면 각 SKILL.md의 "출력 표준" 섹션과 references·templates를
  직접 번역해야 합니다.
- **하위 에이전트 사용 범위**: 각 스킬의 「에이전트」는 역할 전환(내부 추론)입니다. 실제 하위 에이전트는
  두 경우에만 씁니다 — P1의 병렬 분석 트랙(정밀 모드: 독립 2트랙 산출 후 델타 비교)과 problem-solver의
  레드팀 독립 실행. 같은 모델로 결과를 다시 확인하는 검증 목적 위임은 금지입니다.

## 라이선스

MIT License — 자유롭게 사용, 수정, 배포, 상용 이용 가능합니다.
저작자 표시는 필수는 아니지만 있으면 감사하겠습니다.
