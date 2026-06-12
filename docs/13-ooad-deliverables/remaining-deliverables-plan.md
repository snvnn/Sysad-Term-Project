# 13. OOAD Remaining Deliverables Plan

## 1. Purpose

이 문서는 Rich Picture 이후 남은 OOAD 과제 산출물을 하나씩 작성하기 위한 참고 계획이다. 현재 과제 범위는 분석/설계 산출물 작성이며, 구현 코드는 작성하지 않는다.

## 2. Scope Decision

| Item | Decision |
|---|---|
| Rich Picture | 사용자가 수기로 작성하므로 이 저장소에서는 작성하지 않음. 필요 시 사진/스캔본 링크만 추가. |
| Brief Use Cases | 작성 대상. 짧고 제출용으로 읽기 쉬운 스타일 유지. |
| System Sequence Diagrams, SSD | 작성 대상. Actor ↔ System 경계만 표현하고 내부 객체는 넣지 않음. |
| Operation Contracts | 작성 대상. 각 시스템 오퍼레이션의 precondition/postcondition 중심. |
| Class Diagram | 작성 대상. 업그레이드된 도메인 모델에 attributes/methods/relationships를 반영. |
| Design Sequence Diagrams | 작성 대상. 내부 객체 간 협력 흐름을 표현. |
| Domain open issue cleanup | 완료됨. 현재 핵심 도메인 open issue는 Q-008 하나만 남음. |

## 3. Current Remaining Open Issues

### Q-008 / OI-REQ-002 — Expected Mortgage Repayments Scope

`expected mortgage repayments`의 정확한 합산 범위는 출처에서 명확하지 않다.

Current status: `Open / Needs Discussion`

Recommended working interpretation:

```text
expected mortgage repayments = expected amount actually paid by beneficiaries to the foundation
```

Reason:

- Q-001 산식이 `+ expected mortgage repayments - expected grants`로 결정되었으므로,
- `expected mortgage repayments`를 보조금 지급 전 전체 P&I + escrow 총액으로 해석하면 `expected grants`와 중복 계산될 위험이 있다.

Impact:

- Use Case, SSD, Operation Contract, Class Diagram, Design Sequence Diagram은 진행 가능하다.
- 단, 계산 정확성 확정을 위해 최종 구현 전에는 Q-008을 반드시 사용자/도메인 담당자에게 확인해야 한다.

## 4. Planned Deliverable Order

### Step 1. Brief Use Cases

Status: `Completed`

Output file:

```text
docs/03-requirements/use-cases.md
```

Style:

- Brief style
- 각 유스케이스는 5~10줄 정도
- 과도한 narrative 대신 actor, goal, trigger, main flow, alternatives, postcondition 중심

Candidate use cases:

| ID | Use Case | Primary Actor | Notes |
|---|---|---|---|
| UC-001 | Manage Investments | Foundation Manager | 투자 항목과 예상 연간 수익 관리 |
| UC-002 | Manage Operating Expense Estimate | Foundation Manager | 운영비 추정치 관리 |
| UC-003 | Manage Mortgages | Mortgage Program Officer | 모기지 계정과 갱신일 관리 |
| UC-004 | Run Weekly Funds Computation | Foundation Manager | 핵심 주간 가용 자금 계산 |
| UC-005 | Check Home Fundability | Foundation Manager | 주택 비용이 남은 가용 자금 이하인지 확인 |
| UC-006 | Record Fund Allocation | Foundation Manager | 승인된 주택 비용만큼 가용 자금 차감 |
| UC-007 | Generate Weekly Funds Report | Foundation Manager / Trustees | 주간 계산 결과 출력 |
| UC-008 | Generate Investment Listing | Foundation Manager / Trustees | 투자 목록 출력 |
| UC-009 | Generate Mortgage Listing | Foundation Manager / Trustees | 모기지 목록 출력 |

Verification:

- 모든 Must FR이 최소 하나의 Use Case와 연결되어야 함.
- 자격 심사/90% 모기지 조건은 out-of-pilot으로 표시해야 함.

### Step 2. System Sequence Diagrams, SSD

Status: `Completed`

Output files:

```text
docs/03-requirements/system-sequence-diagrams.md
docs/03-requirements/system-sequence-diagrams.drawio
```

Rules:

- SSD는 내부 객체를 표현하지 않는다.
- 참여자는 `Actor`와 `MSG Foundation Pilot System`만 사용한다.
- 상세 객체 협력은 Design Sequence Diagram에서 다룬다.

Candidate SSDs:

| ID | SSD | Based on Use Case |
|---|---|---|
| SSD-001 | Run Weekly Funds Computation | UC-004 |
| SSD-002 | Manage Mortgage Data | UC-003 |
| SSD-003 | Check Home Fundability | UC-005 |
| SSD-004 | Generate Reports | UC-007~UC-009 |

Verification:

- SSD 메시지가 Use Case main flow와 일치해야 함.
- 내부 service/repository/calculator는 등장하지 않아야 함.

### Step 3. Operation Contracts

Status: `Completed`

Output file:

```text
docs/07-design/operation-contracts.md
```

Candidate contracts:

| ID | Operation | Related Use Case |
|---|---|---|
| OC-001 | createInvestment | UC-001 |
| OC-002 | updateInvestmentReturn | UC-001 |
| OC-003 | updateOperatingExpenseEstimate | UC-002 |
| OC-004 | createMortgage | UC-003 |
| OC-005 | updateMortgageIncome | UC-003 |
| OC-006 | runWeeklyComputation | UC-004 |
| OC-007 | checkHomeFundability | UC-005 |
| OC-008 | recordFundAllocation | UC-006 |
| OC-009 | generateWeeklyFundsReport | UC-007 |

Required fields per contract:

- Operation
- Cross References
- Preconditions
- Postconditions
- Notes / Open Issues

Verification:

- Postconditions must describe domain state changes.
- `runWeeklyComputation` must mention Q-008 as open where repayment scope is used.

### Step 4. Class Diagram

Target file:

```text
docs/07-design/class-diagram.md
```

Candidate classes:

| Class | Purpose |
|---|---|
| Money | Decimal/fixed-point amount handling |
| Investment | Investment data and annual return |
| OperatingExpenseEstimate | Annual operating expense estimate |
| Mortgage | Mortgage data, escrow, grant inputs |
| WeeklyComputation | Weekly calculation snapshot/result |
| WeeklyComputationItem | Input snapshot item |
| FundAllocation | Approved/checked home cost allocation |
| MortgageCalculator | Mortgage cost, affordability cap, grant calculation |
| FundsAvailabilityCalculator | Q-001 available amount calculation |
| ReportService | Report generation facade |

Verification:

- Attributes must align with `docs/08-data/data-model.md`.
- Methods must support operation contracts.
- Relationships and multiplicities must match domain model.

### Step 5. Design Sequence Diagrams

Target file:

```text
docs/07-design/sequence-diagrams.md
```

Candidate diagrams:

| ID | Sequence Diagram | Related Use Case / Contract |
|---|---|---|
| SD-001 | Run Weekly Funds Computation | UC-004 / OC-006 |
| SD-002 | Check Home Fundability | UC-005 / OC-007 |
| SD-003 | Record Fund Allocation | UC-006 / OC-008 |
| SD-004 | Generate Weekly Funds Report | UC-007 / OC-009 |

Rules:

- Unlike SSD, design sequence diagrams should show internal objects.
- Suggested participants: UI, Application Service, Repository, Domain Calculator, Report Renderer.
- Keep diagrams readable and not over-engineered.

Verification:

- Each design sequence should be traceable to one Use Case and one Operation Contract.
- Object interactions should be consistent with Class Diagram methods.

## 5. Index / Traceability Updates After Each Step

After each deliverable is created:

1. Add the file to `docs/PROJECT_INDEX.md`.
2. If relevant, update `README.md` deliverables list.
3. If requirements coverage changes, update `docs/03-requirements/requirements-traceability-matrix.md`.
4. Run markdown checks.
5. Commit and push a small docs-focused commit.

## 6. Recommended Commit Sequence

| Step | Suggested Commit Message |
|---|---|
| Plan | `docs: add OOAD deliverables plan` |
| Use Cases | `docs: add brief use cases` |
| SSD | `docs: add system sequence diagrams` |
| Operation Contracts | `docs: add operation contracts` |
| Class Diagram | `docs: add class diagram` |
| Design Sequences | `docs: add design sequence diagrams` |
| Final Review | `docs: finalize OOAD deliverable package` |

## 7. Completion Criteria

The OOAD package is ready when:

- Rich Picture is acknowledged as user-owned/manual.
- Brief Use Cases exist and cover Must requirements.
- SSDs exist and stay at actor-system boundary.
- Operation Contracts define pre/postconditions for core operations.
- Class Diagram includes attributes, methods, and relationships.
- Design Sequence Diagrams show internal collaboration for core flows.
- Remaining open issues are explicitly documented, especially Q-008.
- Project index and README point to all deliverables.
- Git history shows incremental commits for each step.
