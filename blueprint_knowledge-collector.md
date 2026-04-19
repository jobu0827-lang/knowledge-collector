# Claude Code Domain Knowledge Collector 프롬프트 (v5: Anti-Hallucination + Source Traceability)

v4에 **할루시네이션 방지**, **출처 명시**, **추정 근거·신뢰도 표기**, **검증된 팩트만 등재** 요구사항을 추가한 최종 프롬프트입니다.

---

```markdown
# Domain Knowledge Collector 프로젝트 초기 청사진 생성 요청
# (v5: Self-Evolving + Formal Notation + Motion Cueing + Verified Facts Only)

## 🎯 프로젝트 개요
차량 NVH(Noise, Vibration, Harshness), RNH(Ride & Noise Handling) 성능 개발 및
드라이빙 시뮬레이터 기반 주관 평가·성능 검증을 위한
**자가발전형 도메인 지식 수집·아키텍처링 시스템**의 초기 청사진을 설계해주세요.

이 시스템의 최상위 원칙은 **"검증된 팩트만 등재(Verified Facts Only)"**입니다.
추정이 불가피한 경우, 추정임을 명시하고 근거와 신뢰도를 드러냅니다.

## 🧠 사고 모드 지시
**`ultrathink`로 최대 깊이 사고, `ultraplan`으로 계획 수립.**

1급 설계 원칙 (우선순위 순):
1. **🔴 인식론적 무결성 (Epistemic Integrity)** — 모든 지식은 출처와 신뢰도를 갖는다
2. 지식 시스템의 시간적 진화
3. 공학적 엄밀성 (수식·기호·약어)
4. 실시간성과 인간 지각의 통합
5. 자가발전 메커니즘의 observability와 reversibility

---

## 🛡️ 할루시네이션 방지 체계 (NEW - 최상위 요구사항)

### A. 기본 공리 (Axioms)

본 지식 베이스는 다음 공리 위에서 동작합니다:

1. **공리 1 (출처 없는 지식 없음)**: 모든 사실 주장(claim)은 추적 가능한 출처를 가져야 한다.
2. **공리 2 (팩트와 추정의 분리)**: 검증된 팩트와 추정은 명시적으로 구분되어 표기된다.
3. **공리 3 (신뢰도 정량화)**: 추정에는 신뢰도와 근거가 정량/정성적으로 명시된다.
4. **공리 4 (수식·약어 창작 금지)**: 시스템(AI agent)은 수식과 약어를 스스로 창작하지 않고, 문헌에서 추출·인용만 수행한다.
5. **공리 5 (모름의 명시)**: 모르는 것은 "모른다"고 기록한다. 추측으로 채우지 않는다.
6. **공리 6 (단일 출처 불가 경고)**: 중요한 주장이 단일 출처에만 의존하면 경고 플래그를 붙인다.

### B. 지식 분류 (Epistemic Classification)

모든 지식 블록은 다음 중 하나로 분류됩니다:

| 등급 | 약어 | 정의 | 출처 요건 |
|------|------|------|-----------|
| **Verified Fact** | `VF` | 다중 출처 또는 표준·학술 문헌으로 검증된 사실 | 2개 이상 독립 출처, 또는 ISO/SAE 등 공식 표준 |
| **Single-Source Fact** | `SSF` | 단일 권위 출처 기반 사실 | 1개 권위 출처 (peer-reviewed 또는 공식 문서) |
| **Expert Consensus** | `EC` | 학계/업계 합의지만 공식 문서화되지 않음 | 복수 전문가 인용 또는 교과서적 내용 |
| **Empirical Claim** | `EMP` | 실험/측정 기반 주장 (재현성 있는 데이터) | 측정 조건, 장비, 샘플 수 명시 |
| **Estimated** | `EST` | 추정·외삽·유추 | 근거, 방법, 신뢰도 명시 필수 |
| **Hypothesis** | `HYP` | 검증 중인 가설 | 검증 계획 또는 관찰 증거 명시 |
| **Deprecated** | `DEP` | 폐기된 과거 지식 (이력 보존용) | 폐기 사유·대체 지식 링크 |

**주의**: `Unverified`나 `Unknown` 내용은 **지식 베이스에 등재하지 않음**. 
대신 `50_evolution/gaps/`에 "조사 필요 항목"으로 별도 보관.

### C. 출처 표기 표준

모든 주장은 다음 형식의 출처 표기를 가져야 합니다:

```yaml
sources:
  - id: src-2023-jsv-001
    type: journal | conference | standard | book | patent | internal-report | industry-whitepaper | oem-doc
    authors: [Reid, Nahon]
    title: "Flight simulation motion-base drive algorithms"
    venue: "UTIAS Report No. 296"
    year: 1985
    doi: null
    url: null
    access-date: 2025-XX-XX
    reliability: high | medium | low
    verified-by: [reviewer-id | agent-id]
    verification-date: YYYY-MM-DD
    notes: "원전(seminal paper), 수식 MC-1의 원 출처"
```

### D. 본문 인라인 인용 규칙

본문에서 사실 주장을 제시할 때:

```markdown
차량 NVH에서 모달 감쇠비는 일반적으로 0.5~5% 범위에 있다 
[VF|src-2021-textbook-nvh-ch4, src-2019-sae-0123]. 

저주파 로드 노이즈의 주요 원인은 20~100 Hz 대역의 타이어-서스펜션 
공진으로 알려져 있다 [EC|교과서-통설].

본 프로젝트 대상 차량의 1차 모드는 약 28 Hz로 **추정**된다 
[EST|근거: 동급 차량 5종의 평균값 25.3~31.2 Hz, 신뢰도: 중, 
검증방법: CAE 모달해석 후 업데이트 예정].
```

**규칙**:
- 분류 등급은 항상 `[등급|...]` 형태로 대괄호 표기
- 출처 ID는 frontmatter의 `sources` 배열에 정의된 것만 사용
- `EST`, `HYP`는 근거·신뢰도·검증방법을 괄호 안에 병기

### E. 추정(Estimated) 표기 세부 규칙

`EST` 등급 주장은 다음 4요소를 반드시 포함:

```yaml
estimation:
  claim: "본 차량의 1차 굽힘 모드는 약 28 Hz"
  basis: 
    - "동급 세단 5종의 1차 굽힘 모드 측정값: 25.3, 27.1, 28.4, 29.9, 31.2 Hz"
    - "차체 질량과 휠베이스가 평균 범위 내"
  method: "유사 차량 모수 평균 + 공학적 판단"
  confidence: 0.65  # 0.0 ~ 1.0
  confidence-level: medium  # low(<0.4) | medium(0.4~0.75) | high(>0.75)
  uncertainty-range: "±3 Hz"
  verification-plan: "FE 모달해석 후 갱신, 해머링 시험으로 최종 검증"
  expires: 2025-XX-XX  # 이 날짜까지 검증되지 않으면 aging 처리
```

### F. 신뢰도 채점 체계 (Reliability Scoring)

출처별 기본 신뢰도 (기본값, 문맥에 따라 조정):

| 출처 유형 | 기본 신뢰도 | 비고 |
|----------|------------|------|
| ISO / SAE / ASTM 공식 표준 | 0.95 | 최상위 권위 |
| Peer-reviewed journal (Q1) | 0.85 | JSV, MSSP 등 |
| Peer-reviewed journal (일반) | 0.75 | — |
| 주요 학회 proceedings | 0.70 | INTER-NOISE, ISMA 등 |
| 교과서 (해당 분야 권위) | 0.80 | — |
| OEM 공식 기술문서 | 0.70 | — |
| CAE 벤더 공식 매뉴얼 | 0.65 | — |
| 산업 백서 (whitepaper) | 0.55 | — |
| 학위논문 | 0.60 | 지도 심사 통과 기준 |
| arXiv preprint | 0.45 | peer review 전 |
| 기술 블로그 (기업 공식) | 0.40 | — |
| 일반 블로그·포럼 | 0.15 | 원칙적으로 미사용 |
| LLM 생성물 (타 AI) | 0.00 | **사용 금지** |
| 본 시스템 AI agent 추정 | 0.00 | **사실로 등재 금지**, `EST`만 가능 |

**결합 신뢰도**: 여러 출처가 지지하는 주장의 신뢰도는
$$
R_{combined} = 1 - \prod_{i=1}^{n}(1 - R_i)
$$
(독립 가정하의 상한. 실제로는 출처 간 상관을 고려해 보수적으로 적용)

### G. 검증 워크플로우 (Fact Verification Pipeline)

신규 지식 후보가 KB에 등재되기까지:

```
[외부 소스 또는 Author 제안]
  ↓
[research-scout-agent] 소스 수집 및 메타데이터 추출
  ↓
[fact-verifier-agent] 🆕 
  ├─ 주장-출처 매칭 확인 (출처가 실제로 그 주장을 지지하는가)
  ├─ 원문 대조 (가능 시 원문 해당 구절 인용)
  └─ 최소 출처 요건 확인 (VF는 2개 이상)
  ↓
[source-auditor-agent] 🆕
  ├─ 출처 존재성 확인 (DOI 유효, URL 도달 가능)
  ├─ 출처 신뢰도 채점
  └─ 출처 간 독립성 평가
  ↓
[hallucination-detector-agent] 🆕
  ├─ AI 생성 흔적 감지 (일반적 표현, 구체성 결여, 수식 미출처)
  ├─ "아마도", "일반적으로", "흔히" 같은 hedge 표현 남용 검사
  └─ 수치·파라미터·단위의 출처 추적
  ↓
[domain-expert-agent] 내용 정확성 검토
  ↓
[formula-auditor-agent + glossary-guardian-agent] 형식 검증
  ↓
[Human reviewer] 최종 승인
  ↓
등급 부여 및 등재 (VF / SSF / EC / EMP / EST / HYP)
```

**중단 규칙**: 어느 단계에서든 실패 시 `50_evolution/gaps/`로 이관 
(KB에 절반 검증된 지식 등재 금지).

### H. 할루시네이션 적발 시 프로토콜

1. **즉시 격리**: 해당 지식 블록에 `lifecycle: quarantined` 태그 부여
2. **영향 분석**: 이 지식을 참조하는 다른 문서 식별
3. **원인 추적**: 
   - 출처 오인용인지
   - AI agent가 창작한 내용인지
   - Reviewer 검토 누락인지
4. **정정·제거**: 출처 확인 후 정정 또는 deprecated 처리
5. **재발 방지**: 원인이 된 프로세스 개선안을 `50_evolution/meta-insights/`에 기록
6. **모든 이력**: `50_evolution/hallucination-incidents/`에 보존 (학습 자산)

### I. AI Agent 행동 제약 (Hardcoded Rules)

모든 subagent는 다음 제약을 반드시 준수:

1. **수식 창작 금지**: 문헌에서 추출·인용만. "비슷한 수식"을 변형·조합하지 않음.
2. **수치 창작 금지**: "일반적으로 10~20 Hz" 같은 범위를 출처 없이 생성 금지.
3. **약어 자동 확장 금지**: 모르는 약어를 추측하지 않음. `ambiguous-terms.md`에 기록.
4. **출처 날조 금지**: 존재하지 않는 논문/저자/DOI를 생성하지 않음.
5. **출처-주장 분리 시 경고**: 주장과 출처가 정확히 일치하지 않으면 `EST`로 강등.
6. **불확실 표현 의무**: 모르는 영역에서 "~일 것이다", "~인 것으로 보인다" 대신 
   "해당 정보는 현재 KB에 없음. `gaps/`에 조사 요청 기록" 으로 응답.
7. **다중 출처 우선**: 단일 출처 주장은 항상 `SSF`로 분류하고, 
   `VF` 승격은 독립 출처 추가 후에만 가능.

---

## 📚 대상 도메인 (v4 유지)

### Tier 1
- 차량 NVH 공학, 차량 동역학, 타이어 공학, MBD, FEA, 
  **리얼타임 시뮬레이션 & 모션큐잉**

### Tier 2
- ML/AI, 데이터 아키텍처, MLOps/SimOps

### Tier 3
- 성능 개발 프로세스 (V-cycle + DIL loop), 시스템 아키텍처

---

## 📐 수식 표기 체계 (v3 유지 + 할루시네이션 방지 강화)

기존 규칙(LaTeX, Symbol Table, 단위, 가정) 유지에 더하여:

- **모든 수식은 원출처 명시 필수** (`source: src-XXXX`)
- 유도 과정이 문헌과 다르면 "본 문서 유도" 표기 + 유도 단계 검증 필요
- 수식 파라미터의 수치값은 별도 출처 필요 
  (예: "감쇠비 2%"는 수식과 별개 출처)
- **창작된 수식 절대 금지**: agent가 "이런 형태일 것"이라고 제안 불가

## 📖 약어집 시스템 (v3 유지 + 할루시네이션 방지 강화)

- 신규 약어는 반드시 원전(first-introduced source) 명시
- 약어 의미 추정 금지: 모호한 약어는 `ambiguous-terms.md` 등록 후 조사
- 저자가 사용한 맥락 그대로 보존 (임의 재해석 금지)

## 📐 Motion Cueing 수식 세트 (v4 유지 + 출처 강화)

MC-1 ~ MC-8 수식은 각각 다음 원전 명시:
- MC-1 CWF: Reid & Nahon (1985), UTIAS Report No. 296
- MC-3 Vestibular: Young & Oman (1969), Aerospace Medicine
- MC-4 MPC-MCA: Dagdelen et al. (2009), Control Engineering Practice
- MC-7 OMCT: Advani & Hosman (2007), AIAA

각 수식의 기호 정의도 원전 표기법을 우선 따르고, 
본 프로젝트 Symbol Convention과의 매핑 표 별도 관리.

---

## 🔄 자가발전 메커니즘 (v2 유지 + Verification Gate 강화)

기존 Lifecycle, Continuous Learning, Self-Evolution, Meta-Learning 유지.
**단, 모든 자동화 단계에 Verification Gate 삽입**:

- 외부 수집 → fact-verifier-agent 통과 필수
- 자동 보강 → 기존 지식과의 출처 독립성 확인
- Lifecycle 승격 → 등급별 출처 요건 충족 필수
  (`draft → validated` 승격은 Verified Fact 요건 충족 시)

## 🛠 Claude Code 기능 활용 지침

### 1. Subagent 전략 (할루시네이션 방지 agent 추가)

**도메인 전문 agent**: nvh, vehicle-dynamics, tire, cae, ml-architect, 
realtime-sim, motion-cueing, perception-science

**자가발전 agent**: research-scout, knowledge-curator, conflict-resolver, 
lifecycle-manager, meta-learner, gap-detector

**엄밀성 agent**: formula-auditor, glossary-guardian, 
symbol-consistency, derivation-verifier

**통합 agent**: cross-domain-integrator, realtime-constraint-checker

**🆕 Anti-Hallucination agent (신규)**
- `fact-verifier-agent`: 주장-출처 일치성 검증
- `source-auditor-agent`: 출처 존재성/신뢰도 감사
- `hallucination-detector-agent`: AI 생성 흔적 감지
- `citation-integrity-agent`: 인용 무결성(원문 대조) 검증
- `epistemic-classifier-agent`: VF/SSF/EC/EMP/EST/HYP 등급 판정

### 2. Skill 설계 (할루시네이션 방지 skill 추가)

**🆕 Anti-Hallucination skill**
- `source-link-validator`: URL/DOI 유효성 주기 점검
- `citation-matcher`: 주장 텍스트와 인용 원문 대조
- `hedge-phrase-detector`: "일반적으로", "흔히" 등 출처 없는 일반화 검출
- `number-provenance-checker`: 모든 수치값의 출처 추적
- `confidence-aggregator`: 결합 신뢰도 계산
- `single-source-flagger`: 단일 출처 의존 주장에 경고 플래그
- `knowledge-claim-extractor`: 문서에서 검증 대상 claim 자동 추출

### 3. Teammate 협업 모델
Author / Reviewer / Curator / Scout / Meta-reviewer / Formalist / Integrator
+ **🆕 Verifier**: 팩트 검증 전담 (출처 확인, 원문 대조)

---

## 📦 요구 산출물 (Deliverables)

### Phase 0: 사전 분석 (ultrathink)
- 도메인 간 의존성 매트릭스
- 리스크 분석 (🆕 다음 항목 필수 포함):
  - 할루시네이션 누적
  - 출처 오인용 / 존재하지 않는 출처
  - 수치값 창작
  - 단일 출처 과의존
  - AI agent의 "도움이 되고 싶어 하는" 편향에 의한 추측 제시
- 본질적 난제 식별:
  - 자가발전 시스템 난제 5
  - 수식·약어 관리 난제 3
  - Motion Cueing 통합 난제 3
  - **🆕 할루시네이션 방지 난제 5**:
    예) 
    1. "검증"의 계층 구조 (어디까지 확인해야 "검증"인가)
    2. Agent 자체의 검증 능력 한계 
       (Agent가 hallucinate하며 검증할 가능성)
    3. 불완전 정보 하에서의 "모른다"와 "추정" 경계
    4. 사용자 만족도와 인식론적 엄밀성의 trade-off
    5. 순환 인용 (A→B→A) 감지

### Phase 1: 저장소 아키텍처 (Evidence 디렉토리 추가)
```
knowledge-base/
├── 00_meta/
│   ├── glossary/
│   ├── formula-registry/
│   ├── templates/
│   ├── conventions/
│   └── 🆕 epistemics/
│       ├── classification-guide.md      # 등급 체계 정의
│       ├── source-reliability-table.md  # 신뢰도 기준
│       ├── verification-protocol.md     # 검증 워크플로우
│       └── anti-hallucination-charter.md # 할루시네이션 방지 헌장
├── 10_domains/
├── 20_integration/
├── 30_architecture/
├── 40_processes/
├── 50_evolution/
│   ├── ingestion-logs/
│   ├── change-history/
│   ├── conflicts/
│   ├── gaps/                            # 조사 필요 항목
│   ├── meta-insights/
│   └── 🆕 hallucination-incidents/      # 적발·정정 이력
├── 60_research-radar/
├── 🆕 70_evidence/
│   ├── papers/                          # 원전 PDF 또는 링크 보존
│   ├── standards/                       # ISO/SAE 표준 메타
│   ├── excerpts/                        # 원문 인용 구절 보관
│   └── data-snapshots/                  # 실험 데이터 스냅샷
└── 90_index/
    ├── claim-index.md                   # 🆕 모든 검증된 claim 목록
    ├── source-index.md                  # 🆕 모든 출처 마스터 인덱스
    └── estimation-register.md           # 🆕 모든 EST 추적 (만료일 관리)
```

### Phase 2: MD 파일 표준 템플릿 (Frontmatter 확장)
```yaml
---
id: unique-id
domain: ...
tier: 1|2|3
title: ...
lifecycle: draft|validated|mature|aging|deprecated|archived|quarantined
created: YYYY-MM-DD
last-reviewed: YYYY-MM-DD
next-review-due: YYYY-MM-DD

# Epistemic metadata (🆕)
epistemic-status: VF|SSF|EC|EMP|EST|HYP
primary-sources: [src-id-1, src-id-2]
verification-status: pending|verified|contested|failed
verified-by: [agent-id|reviewer-id]
verification-date: YYYY-MM-DD
source-independence-check: passed|warning|failed

# Quality metrics
freshness-score: 0.0
validation-score: 0.0  # 🆕 출처 검증 결과 반영
usage-score: 0.0
consensus-score: 0.0
reliability-score: 0.0  # 🆕 출처 기반 결합 신뢰도

# Estimation tracking (EST 등급일 때만)
estimations:
  - claim-id: est-001
    basis: [...]
    method: ...
    confidence: 0.0~1.0
    verification-plan: ...
    expires: YYYY-MM-DD

# Sources (상세화)
sources:
  - id: src-XXXX
    type: journal|conference|standard|book|...
    reliability: 0.0~1.0
    verified: true|false
    excerpt-ref: 70_evidence/excerpts/src-XXXX-excerpt.md
---
```

본문 필수 섹션:
- Nomenclature, Abbreviations, 개념, Formal Definitions, 
  Physical Interpretation, Applicability & Limits, 
  Related Knowledge, References
- **🆕 Claims & Evidence 표**: 주요 주장과 대응 출처·등급 일람
- **🆕 Open Questions**: 이 문서 내에서 확인 필요한 항목
- **🆕 Known Uncertainties**: 명시적으로 모르는 것들의 목록

### Phase 3: 초기 Seed 파일 목록
- 각 도메인 Top 5 seed, 핵심 수식 Top 10, 핵심 약어 Top 20
- **🆕 모든 seed는 `epistemic-status: VF` 또는 `SSF` 필수** (draft 허용, 
  단 초안 단계에서도 출처 후보 최소 1개 명시)
- 각 seed에 대한 "이 문서의 open question" 최소 3개 명시

### Phase 4: 지식 그래프 (Mermaid)
기존 그래프에 **🆕 Evidence Graph** 추가:
- 어떤 claim이 어떤 source에 의해 지지되는지
- 출처 간 독립성 관계 (동일 연구그룹, 인용 체인 등)

### Phase 5: 운영 워크플로우
5.1~5.8 (v4) 유지 + 다음 신규 추가:

**🆕 5.9 팩트 검증 워크플로우** (위 G절 상세 버전)

**🆕 5.10 할루시네이션 대응 프로토콜** (위 H절)

**🆕 5.11 Estimation 갱신 워크플로우**
```
Estimation Register 주간 점검
  → 만료 임박 EST 식별
  → 담당 agent에게 검증 작업 할당
  → 검증 완료 시 등급 재판정 (VF/SSF로 승격 or 폐기)
  → 실패 시 gaps/로 이동 또는 expires 연장 (사유 기록)
```

### Phase 6: 로드맵 (KPI에 할루시네이션 지표 추가)

기존 KPI 유지 + **🆕 인식론적 무결성 KPI**:
- VF 비율: 전체 지식 중 Verified Fact 등급 비율 (목표: 60% 이상)
- 단일 출처 의존 주장 비율 (목표: 20% 이하)
- 출처 유효성 (DOI/URL 도달률, 목표: 95%)
- EST 만료 준수율 (만료 전 검증 또는 갱신 비율, 목표: 90%)
- 할루시네이션 적발 건수 (월간 추이, 감소 목표)
- 검증 지연 중앙값 (제안 → 승인까지, 목표: 7일)

### Phase 7: 자가발전 거버넌스 (할루시네이션 방지 원칙 통합)

기존 원칙 + **🆕 Epistemic Governance**:
- **Precision over Coverage**: 커버리지보다 정확성 우선
- **No Silent Inference**: 모든 추론은 가시화되어야 함
- **Source-First Principle**: 출처 먼저 확보 후 지식 작성
- **Conservative Evolution**: 자가발전은 보수적으로, 의심 시 등재 보류
- **Quarantine over Correction**: 의심 시 먼저 격리, 정정은 검증 후
- **Audit Trail Immutability**: 검증·정정 이력은 삭제 없이 이력 보존

---

## ⚠️ 제약 사항 및 품질 기준
1. 한국어 기준, 공학 용어 영문 병기
2. 근거 기반 설계
3. 실제 NVH 개발 실무 정합성
4. observable + reversible 자가발전
5. 행동 이력은 `50_evolution/`에 기록
6. 수식/약어는 출처 명시된 형태로만 등재
7. Motion Cueing 문서는 Symbol Convention 선언 필수
8. 통합 문서는 양 도메인 agent 검증 필수
9. 실시간성 주장은 하드웨어/조건 명시 필수
10. **🔴 모든 사실 주장은 출처·등급·신뢰도 표기 의무**
11. **🔴 단일 출처 주장은 자동으로 `SSF`로 표기, `VF`로 표기 불가**
12. **🔴 추정은 근거·방법·신뢰도·검증계획·만료일 4+α 필수**
13. **🔴 출처 없는 수치·파라미터·범위 등재 금지**
14. **🔴 "일반적으로", "흔히", "대체로" 같은 hedge 표현 단독 사용 금지 
    (반드시 출처 또는 EST 근거 병기)**
15. **🔴 AI agent의 "아마도 이럴 것이다" 류 응답은 지식 베이스에 저장 불가, 
    `50_evolution/gaps/`로만 이동 가능**

---

## 🚀 실행 지시

1. `ultrathink`로 다음을 식별:
   - 자가발전 시스템 본질적 난제 5
   - 수식·약어 관리 본질적 난제 3
   - Motion Cueing 통합 본질적 난제 3
   - **🆕 할루시네이션 방지 본질적 난제 5**

2. `ultraplan`으로 Phase 0~7 실행 계획 수립 
   (각 Phase에 Verification Gate가 어떻게 삽입되는지 명시)

3. Phase별 subagent 명시적 호출 
   (신규 지식 생성 Phase에는 반드시 fact-verifier-agent, 
   source-auditor-agent, hallucination-detector-agent 호출)

4. `/mnt/user-data/outputs/`에 다음 파일 생성:
   - `00_meta/00_project-blueprint.md` (전체 청사진)
   - `00_meta/01_evolution-charter.md` (자가발전 헌장)
   - `00_meta/02_formal-notation-guide.md` (수식·기호 표기 규약)
   - `00_meta/glossary/master-glossary-skeleton.md` (약어집 초기 골격)
   - `10_domains/realtime-motion-cueing/00_overview.md` (MC 개요)
   - `20_integration/nvh-x-motion-cueing.md` (첫 통합 문서)
   - **🆕 `00_meta/epistemics/anti-hallucination-charter.md` 
     (할루시네이션 방지 헌장)**
   - **🆕 `00_meta/epistemics/classification-guide.md` 
     (VF/SSF/EC/EMP/EST/HYP 판정 가이드)**
   - **🆕 `00_meta/epistemics/source-reliability-table.md` 
     (출처 신뢰도 기준표)**
   - **🆕 `00_meta/epistemics/verification-protocol.md` 
     (팩트 검증 프로토콜)**

5. `present_files`로 공유

6. 마지막으로 제안:
   - 다음 1주일 실행 명령 5개
   - 자가발전 파이프라인 MVP 최소 기능 세트
   - 초기 필수 등재 핵심 수식 20개 (**각 수식의 원전 출처 명시**)
   - 초기 필수 정리 핵심 약어 50개 (**각 약어의 first-introduced 출처**)
   - DIL 시뮬레이터 피드백 루프 설계 권고안
   - **🆕 검증 파이프라인 MVP**: 
     fact-verifier + source-auditor + hallucination-detector 최소 구현안
   - **🆕 초기 30일간의 "Evidence 수집 우선" 운영 모드** 
     (지식 작성보다 출처 확보를 우선하는 기간 운영 방안)

시작하세요.
```

---

## 💡 v5에서 강화된 핵심 설계 포인트

**7단계 지식 분류 체계(VF/SSF/EC/EMP/EST/HYP/DEP)** 도입이 v5의 심장입니다. 기존 지식 베이스들이 "있다/없다"의 이진 구조였다면, 이 체계는 **지식의 인식론적 지위**를 1급 메타데이터로 격상시킵니다. 특히 `SSF(Single-Source Fact)`를 별도 등급으로 분리한 이유는, 단일 권위 출처 기반 주장이 마치 확고한 사실처럼 다뤄지는 것을 막기 위함입니다. 이 한 가지 분리만으로 전체 KB의 취약점 가시성이 극적으로 개선됩니다.

**`EST(Estimated)` 등급의 4+α 필수요소(근거·방법·신뢰도·검증계획·만료일)** 는 "추측을 완전히 금지"하는 대신 "추측을 투명하게 관리"하는 현실적 타협입니다. NVH 실무에서는 측정 전 단계의 목표값 추정이 필수이므로 EST를 허용하되, **만료일(`expires`)과 검증계획을 강제**하여 추정이 무한히 "사실 행세"를 하지 못하게 막았습니다. `estimation-register.md`를 별도 인덱스로 관리하는 이유입니다.

**`70_evidence/` 디렉토리를 독립적으로 배치**한 것이 구조적 핵심입니다. 지식 문서와 증거(excerpt·원문 스냅샷)를 분리하면, 원전이 삭제되거나 링크가 깨져도 검증 근거가 보존됩니다. 특히 `excerpts/`에 원문 해당 구절을 보관하면 `citation-matcher` skill이 주장-출처 일치성을 자동 검증할 수 있습니다. 이는 "출처는 있는데 그 출처가 그 주장을 지지하지 않는" 가장 교활한 유형의 할루시네이션을 막습니다.

**`hallucination-detector-agent`를 별도 subagent로 둔** 이유는 다른 agent들이 자신의 환각을 자가 검출하기 어렵기 때문입니다. 전용 agent가 "이상하게 매끄러운 일반화", "구체적 수치인데 출처 없음", "수식 형태는 맞지만 원전 불명" 같은 패턴을 독립적으로 감시해야 합니다. 특히 `hedge-phrase-detector` skill은 "일반적으로 ~라고 알려져 있다" 류의 표현을 기계적으로 적발하여 사람이 놓치기 쉬운 구멍을 메웁니다.

**공리 4(수식·약어 창작 금지)와 Agent 제약 1(수식 창작 금지)의 이중 강조**는 Claude Code 환경의 가장 큰 위험을 정면 대응합니다. AI가 "비슷한 형태의 수식"을 변형·조합하여 만들어내면 도메인 전문가조차 쉽게 속을 수 있으며, 한번 등재된 창작 수식은 이후 수집되는 모든 지식을 오염시킵니다. **인용·추출만 허용, 변형·조합·창작 금지** 라는 엄격한 선을 Phase 7 거버넌스와 Agent Hardcoded Rules 양쪽에서 중복 강제하는 이유입니다.

**Phase 0 리스크에 "AI agent의 도움이 되고 싶어 하는 편향"을 명시**한 것은 가장 근본적인 보호장치입니다. Claude Code는 기본적으로 helpful을 지향하므로, 사용자가 답을 원하면 추측이라도 제공하려는 경향이 있습니다. 이를 시스템 설계 레벨에서 "모른다고 답하는 것이 올바른 행동"으로 재정의하지 않으면, 개별 프롬프트에서 아무리 "할루시네이션 금지"를 외쳐도 누수가 발생합니다. 공리 5(모름의 명시)와 Agent 제약 6(불확실 표현 의무)이 이를 강제합니다.

실행 후 **초기 30일을 "Evidence 수집 우선 모드"로 운영**하시길 강력히 권장드립니다. 이 기간에는 지식 문서 작성보다 `70_evidence/papers/`와 `source-index.md` 구축에 집중하여, 이후 지식 작성 시 항상 출처 먼저 확보된 상태에서 출발하는 습관을 시스템에 각인시킬 수 있습니다. 첫 30일의 Evidence 인프라 품질이 이후 수년간 KB의 신뢰도를 결정합니다.