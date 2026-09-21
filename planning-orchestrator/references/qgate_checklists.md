# Q-Gate 체크리스트 (Phase B 착수 시 로드)

검증 범위는 단계 간 정합 4종(스키마/이월/상호 참조/회귀 신호)에 한정.
개별 스킬 내부 게이트의 재수행 금지.

## P1 → P2 게이트 (env-scanning-loop-p1 산출물)

- **스키마** [유연 검증 — P1은 정형 블록 미출력 가능]:
  SWOT 4분면 존재 / SO·WO·ST·WT 전략방향 존재 / 분석 프레임워크
  (PEST·3C·5F·VRIO 중 수행분) 결과 식별 가능
- **이월**: 근거 불충분·미확인 항목의 표기 여부
- **회귀 신호**: 전략방향이 SWOT 요인과 무관하게 돌출 / 내부 분석
  (강점·약점) 근거 부재
- P2 브리핑 패키지: SWOT 요약 + 전략방향 + 제약·가정 → goal-setting-loop-p2
  Case 1 입력 형식으로 정리

## P2 → P3 게이트 ([DATA-P2-OUTPUT])

- **스키마 필수 필드**: meta(subject·horizon·bsc_type) / mission_vision
  (why·how·what) / strategy_map(5관점) / okr_summary(O·KR·목표치·유형) /
  assumptions / handoff_to_p3
- **이월**: assumptions 전건 보존 / 미해결 쟁점 표기
- **상호 참조**: okr_summary의 O가 strategy_map 관점 목표와 연결 /
  SWOT↔목표 매핑에서 "제외" 처리된 전략방향의 사유 존재
- **회귀 신호**: KR에 기준선·목표치 부재 / Objective 6개 이상
  (우선순위 부재 신호) / 비전에 시한 없음

## P3 → P4 게이트 ([DATA-P3-OUTPUT])

- **스키마 필수 필드**: meta(pool_size) / strategy_pool(원천·목표 연계·
  성격·간이점수) / errc_decisions(자원균형 포함) / coverage /
  conflicts / rejected_pool / assumptions / handoff_to_p4
- **이월**: P2 assumptions + P3 신규 가정 누계 / rejected_pool 보존
- **상호 참조**: strategy_pool의 목표 참조(O/KR)가 P2 okr_summary에
  실재 / coverage에 P2 전체 Objective 포함
- **회귀 신호**: 고아 Objective 존재(coverage 미충족) / E·R 없이 C만
  존재 / conflicts 미기재인데 자원 경합 과제 다수

## P4 → P5 게이트 ([DATA-P4-OUTPUT])

- **스키마 필수 필드**: meta(mode·reviewed) / passed_pool(조정
  우선순위) / conditional_pool(이행 조건) / hold_drop / resource_totals
  (총량 판정) / legal_flags / conflict_rulings / assumptions /
  handoff_to_p5
- **이월**: legal_flags 미해결 건 명시 / Hold·Drop 사유 보존
- **상호 참조**: reviewed 수 = P3 pool_size (전수 검토 확인) /
  conflict_rulings가 P3 conflicts 전건 커버
- **회귀 신호**: resource_totals "초과" 상태로 종결(조정 내역 없음) /
  정성 모드인데 NPV 수치 존재(수치 창작 위반) / Drop 사유 부재

## P5 → P6 게이트 ([DATA-P5-OUTPUT])

- **스키마 필수 필드**: meta(horizon·tasks) / roadmap_summary
  (critical_path 포함) / milestones / prerequisites_tracks /
  raci_summary / kpi_map / risk_watch / governance / assumptions /
  handoff_to_p6
- **이월**: P4 conditional_pool 이행 조건 → prerequisites_tracks 전건
  반영 / legal_flags 미해결 건 → 병행 트랙 존재
- **상호 참조**: kpi_map의 KR 참조가 P2 okr_summary와 일치 /
  roadmap 과제가 P4 passed+conditional과 일치 (무단 추가·누락 없음)
- **회귀 신호**: [신규] 라벨 KPI 과다(과반 이상 — P2 목표 체계 결함
  신호) / 임계 경로 미식별 / A 복수 지정 흔적

## P6 → 차기 사이클 게이트 ([DATA-P6-OUTPUT])

- **스키마 필수 필드**: meta(mode) / monitoring_system / routing_table /
  assumption_checks / contingency / assumptions / handoff_to_next_cycle
  (점검 모드 시 lessons 추가)
- **이월**: assumption_checks가 P2~P5 가정 누계 전수 커버 —
  오케스트레이터가 보유한 가정 누계와 건수 대조
- **상호 참조**: monitoring_system이 P5 governance 승계 표기 /
  contingency 트리거의 원천(P1 위협·P4 리스크·P5 워치) 표기
- **회귀 신호**: 정량 발동 조건 없는 트리거 / routing_table 공백 유형
- Pass 시: cycle+1, handoff_to_next_cycle → 차기 P1 브리핑 패키지 발급

## 판정 기준 공통 규칙

- **Pass**: 필수 필드 전건 존재 + 이월 유실 없음 + 참조 불일치 없음
- **Hold**: 필드 결손 1~2건 또는 표기 미비 — 해당 Phase 재실행 없이
  보완 가능 (보완 요구 목록 발급, 재접수 시 결손 항목만 재검)
- **Rollback**: 참조 불일치·회귀 신호 — 구조적 모순으로 재실행 필요
- 판정 근거는 체크 항목 단위로 표기 (통과 ○ / 결손 △ / 모순 ✕)

## 재전파 범위 판정 규칙 (Rollback 시)

1. 갱신 대상 필드 식별 (예: P2 KR2-1 수정)
2. 후속 단계에서 해당 필드를 참조하는 산출물 추적:
   - P3: 해당 KR 연계 과제만 / P4: 그 과제의 판정만 /
     P5: 그 과제의 과업·KPI만 / P6: 해당 가정·트리거만
3. 재실행 지시서 발급: [회귀 목적지 Phase | 수정 대상 | 후속 재검토
   목록 (Phase·항목 단위) | 무영향 확정 항목]
4. 재접수 시 재검토 목록 항목만 Q-Gate 재수행 (전체 재검 금지)

## 가정 누계 관리 규칙

- 각 게이트 통과 시 assumptions를 상태 원장에 누적 기록
  (원천 Phase 표기): P6 게이트에서 전수 대조의 기준
- [확정]/[기각] 갱신 접수 시 누계에 반영 — 기각 건은 재전파 규칙 적용
