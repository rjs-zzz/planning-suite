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

### Claude Code — 한 줄 설치 (권장)

```
/plugin marketplace add rjs-zzz/planning-suite
/plugin install goal-setting-loop-p2@planning-suite
```

필요한 Phase만 골라 설치하세요. 7종 전부 설치는 계획서 전체를 작성할 때만
권장합니다 — 스킬이 많을수록 트리거가 겹칠 소지가 커집니다.
`planning-orchestrator`는 2개 이상 Phase가 얽힌 작업에서만 필요합니다.

설치 가능한 플러그인: `env-scanning-loop-p1` `goal-setting-loop-p2`
`strategy-option-loop-p3` `feasibility-review-loop-p4` `roadmap-design-loop-p5`
`feedback-loop-p6` `planning-orchestrator`

### Claude.ai (웹/데스크톱/모바일)

`plugins/<스킬명>/skills/<스킬명>/` 폴더를 zip으로 압축해 Skills 업로드
화면에서 올리면 됩니다(메뉴 경로는 플랜·시점에 따라 다릅니다 —
[공식 안내](https://support.claude.com/en/articles/12512180) 참조).

### 설치 전에 결과물부터 보기

[`examples/`](examples/) 에 실제 산출물 예시가 있습니다.

- [P2 목표 체계 산출물 + DATA 블록](examples/01_p2-goal-system.md)
- [Q-Gate Rollback 판정과 재전파 범위 한정](examples/02_qgate-rollback.md)

## 사용법

**계획서 전체가 필요할 때**: `planning-orchestrator`를 호출하면
현재 가진 자산(이미 환경분석이 있는지 등)을 확인해 시작점을 제안하고,
Phase 스킬을 순서대로 불러와 실행합니다.

**한 단계만 필요할 때**: 해당 Phase 스킬을 직접 호출하세요.
예: "우리 조직 SWOT 분석해줘" → `env-scanning-loop-p1`이 단독 발동.

**단일 결론(선택·가부 판단)만 필요할 때**: 이 세트가 아니라
별도 공개된 `problem-solver` 스킬을 쓰세요. 계획 문서의 한 파트가
아니라 "A냐 B냐" 같은 단발 판단이 목적이면 그쪽이 맞습니다.

## 구조

```
planning-suite/
├── .claude-plugin/
│   └── marketplace.json          # 마켓플레이스 카탈로그 (7개 플러그인)
├── plugins/
│   ├── env-scanning-loop-p1/
│   │   ├── .claude-plugin/plugin.json
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
│           ├── references/       # Q-Gate 체크리스트 (Phase별 스키마 검증 기준)
│           └── templates/        # 관제 대시보드 템플릿
├── examples/                     # 실제 산출물 예시
├── COMMON_CONTROLS.md            # 공통 실행 통제 블록 단일 원본
└── tools/sync_common.py          # 위 블록을 전 스킬에 동기화
```

## 사용 시 참고

- **조직 맥락 커스터마이징**: 각 스킬 SKILL.md의 "상시 적용 맥락"
  섹션은 범용으로 작성돼 있습니다. 소속 조직(공공기관, 기업 등)에
  맞는 법령명·이해관계자 맵·평가 체계로 채워 넣으면 정확도가
  크게 올라갑니다. 섹션 내 안내문에 커스터마이징 지점을 표시해
  두었습니다.
- **BSC 관점**: P2는 기본값이 공공형 BSC(공공가치 최상위)입니다.
  영리·수익사업 맥락이면 SKILL.md에서 표준형 BSC(재무 최상위)로
  전환하도록 안내되어 있습니다.
- **언어**: 7개 스킬 모두 한국어 전용입니다. 지시문·프레임워크 규칙·
  출력 템플릿이 한국어로 작성되어 있고, 출력 형식도 한국 정부 보고서
  양식(두괄식·개조식·【 】 제목 체계)을 따릅니다. 영어 환경에서
  쓰려면 각 SKILL.md의 "출력 표준" 섹션과 references·templates를
  직접 번역해야 합니다.
- **서브에이전트 스폰 금지**: 각 스킬의 「에이전트」는 역할 전환
  (내부 추론)을 의미하며, Claude.ai 채팅에서 실제 서브에이전트를
  스폰하지 않습니다. Claude Code 등 멀티에이전트가 가능한 환경에서도
  검증·재확인 목적의 위임은 금지되어 있습니다. 단, P1 정밀 모드의
  이중 실행은 같은 결과를 다시 검사하는 것이 아니라 독립 2트랙 산출
  후 델타를 비교하는 절차이므로 이 금지 대상에 해당하지 않습니다.

## 라이선스

MIT License — 자유롭게 사용, 수정, 배포, 상용 이용 가능합니다.
저작자 표시는 필수는 아니지만 있으면 감사하겠습니다.
