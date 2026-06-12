# 03. Brief Use Cases

## 1. Purpose

이 문서는 MSG Foundation 파일럿 시스템의 요구사항 분석 산출물로, 시스템 도입 이후 누가 어떤 목적으로 시스템을 사용하는지 brief use case 스타일로 정의한다.

## 2. Scope Notes

- Rich Picture는 사용자가 수기로 작성한다.
- 자격 심사, 90% 모기지 비교 조건, 고용 증거 확인, 주택 가격 검증은 파일럿 시스템 범위 밖이다.
- 파일럿 시스템은 투자 데이터, 운영 비용 데이터, 모기지 데이터를 이용해 주간 가용 자금을 계산하고 보고서를 출력한다.
- Q-008 `expected mortgage repayments` 합산 범위는 `Open / Needs Discussion`이다. 본 문서에서는 권장 해석인 “수혜자가 실제로 납부할 예상 상환액”을 working assumption으로 사용한다.

## 3. Actors

| Actor | Role in Use Cases |
|---|---|
| Foundation Manager | 주간 계산 실행, 데이터 검토, 보고서 출력, 자금 배정 기록의 주 사용자. |
| Mortgage Program Officer | 모기지 계정과 소득/세금/보험 갱신 정보를 관리. |
| Investment Advisor | 투자 예상 수익 정보를 제공하거나 검토. 파일럿에서는 직접 사용자일 수도 있고 관리자에게 정보를 전달할 수도 있음. |
| Trustees / Board | 보고서를 검토하는 의사결정자. 데이터 수정 권한은 없음. |
| System Administrator | 사용자 접근, 백업, 운영 설정을 관리. 비즈니스 데이터 직접 수정은 기본 역할이 아님. |

## 4. Brief Use Cases

### UC-001. Manage Investments

| Field | Description |
|---|---|
| Primary Actor | Foundation Manager |
| Supporting Actor | Investment Advisor |
| Goal | 투자 항목과 예상 연간 수익, 마지막 갱신일을 관리한다. |
| Trigger | 투자 수익 추정치가 새로 제공되거나 분기별 갱신 시점이 도래한다. |
| Main Flow | Manager가 투자 항목을 생성/수정하고 item number, item name, estimated annual return, last updated date를 저장한다. |
| Alternatives | 중복 item number 또는 음수 금액은 거부한다. Investment Advisor가 직접 입력하지 않는 경우 Manager가 전달받은 값을 입력한다. |
| Postcondition | 최신 투자 데이터가 저장되고 주간 투자 수익 계산에 사용할 수 있다. |
| Related Requirements | FR-001, FR-004, NFR-002 |

### UC-002. Manage Operating Expense Estimate

| Field | Description |
|---|---|
| Primary Actor | Foundation Manager |
| Goal | 재단의 예상 연간 운영 비용과 마지막 갱신일을 관리한다. |
| Trigger | 운영 비용 추정치가 갱신되거나 분기별 검토 시점이 도래한다. |
| Main Flow | Manager가 estimated annual operating expenses와 expenses last updated date를 입력/수정한다. |
| Alternatives | 값이 누락되거나 음수이면 시스템은 저장을 거부하고 오류를 표시한다. |
| Postcondition | 최신 운영 비용 추정치가 저장되고 주간 운영 비용 계산에 사용할 수 있다. |
| Related Requirements | FR-002, FR-005, NFR-002 |

### UC-003. Manage Mortgages

| Field | Description |
|---|---|
| Primary Actor | Mortgage Program Officer |
| Supporting Actor | Foundation Manager |
| Goal | 모기지 계정의 핵심 데이터와 갱신일을 관리한다. |
| Trigger | 새 모기지가 등록되거나 소득, 세금, 보험료, P&I 정보가 갱신된다. |
| Main Flow | Officer가 account number, mortgagees last name, original purchase price, issue date, weekly P&I, current weekly income, tax, insurance premium, 각 last updated date를 저장한다. |
| Alternatives | 중복 account number, 누락된 갱신일, 음수 금액은 거부한다. 종료/취소된 모기지는 주간 계산 대상에서 제외한다. |
| Postcondition | 활성 모기지 데이터가 저장되고 주간 모기지 비용/보조금 계산에 사용할 수 있다. |
| Related Requirements | FR-003, FR-006, FR-007, FR-008, FR-009, NFR-002, NFR-005 |

### UC-004. Run Weekly Funds Computation

| Field | Description |
|---|---|
| Primary Actor | Foundation Manager |
| Goal | 해당 주의 시작 시 사용 가능한 주택 구매 자금을 계산한다. |
| Trigger | 매주 첫 영업일에 Manager가 주간 계산을 요청한다. |
| Main Flow | 시스템은 투자 수익, 운영 비용, 활성 모기지, 예상 보조금을 계산하고 `income - expenses + repayments - grants` 산식으로 starting available amount를 산출한다. |
| Alternatives | 필수 데이터가 없거나 오래된 경우 경고를 표시한다. Q-008 상환액 범위는 open issue 경고로 표시한다. |
| Postcondition | WeeklyComputation snapshot과 계산 결과가 저장되고 보고서에 사용할 수 있다. |
| Related Requirements | FR-004, FR-005, FR-006, FR-007, FR-008, FR-009, FR-010, FR-011, FR-012, NFR-001, NFR-002 |

### UC-005. Check Home Fundability

| Field | Description |
|---|---|
| Primary Actor | Foundation Manager |
| Goal | 신규 주택 비용이 현재 남은 가용 자금 이하인지 확인한다. |
| Trigger | 주중 신규 주택 구매 요청 또는 내부 fundability 확인 요청이 발생한다. |
| Main Flow | Manager가 home cost와 optional reference를 입력하면 시스템은 home cost와 remaining available amount를 비교한다. |
| Alternatives | home cost가 남은 가용 자금을 초과하면 fundable=false와 shortfall을 표시한다. |
| Postcondition | 해당 주택 비용의 fundability 결과가 확인된다. 이 단계만으로 가용 자금이 차감되지는 않는다. |
| Related Requirements | FR-013, NFR-001 |

### UC-006. Record Fund Allocation

| Field | Description |
|---|---|
| Primary Actor | Foundation Manager |
| Goal | 승인된 주택 비용만큼 해당 주의 남은 가용 자금을 차감한다. |
| Trigger | UC-005 결과 주택 비용이 fundable이고 Manager가 배정을 기록한다. |
| Main Flow | 시스템은 FundAllocation을 생성하고 remaining available amount를 home cost만큼 감소시킨다. |
| Alternatives | home cost가 남은 가용 자금을 초과하면 배정을 거부한다. 이미 final/archived된 계산에는 배정을 기록하지 않는다. |
| Postcondition | FundAllocation 기록이 저장되고 WeeklyComputation의 remaining available amount가 갱신된다. |
| Related Requirements | FR-014, DR-005, NFR-002 |

### UC-007. Generate Weekly Funds Report

| Field | Description |
|---|---|
| Primary Actor | Foundation Manager |
| Supporting Actor | Trustees / Board |
| Goal | 주간 자금 계산 결과와 입력 요약을 요청 시 출력한다. |
| Trigger | Manager 또는 Trustees가 주간 결산 보고서를 요청한다. |
| Main Flow | 시스템은 WeeklyComputation snapshot, 입력 요약, expected grants, starting/remaining available amount, warnings를 표시한다. |
| Alternatives | 계산 결과가 없으면 먼저 UC-004를 실행하라는 메시지를 표시한다. Q-008은 남은 open issue로 표시한다. |
| Postcondition | 주간 자금 결산서가 화면/파일/인쇄 가능한 형태로 제공된다. |
| Related Requirements | FR-015, NFR-002, NFR-003 |

### UC-008. Generate Investment Listing

| Field | Description |
|---|---|
| Primary Actor | Foundation Manager |
| Supporting Actor | Trustees / Board, Investment Advisor |
| Goal | 모든 투자 항목과 예상 연간 수익, 마지막 갱신일을 요청 시 출력한다. |
| Trigger | 사용자가 투자 목록 보고서를 요청한다. |
| Main Flow | 시스템은 모든 투자 항목을 item number 기준으로 조회하고 item name, estimated annual return, last updated date를 표시한다. |
| Alternatives | 투자 데이터가 없으면 empty state 메시지를 표시한다. |
| Postcondition | 투자 목록 보고서가 제공된다. |
| Related Requirements | FR-016, NFR-002, NFR-003 |

### UC-009. Generate Mortgage Listing

| Field | Description |
|---|---|
| Primary Actor | Foundation Manager |
| Supporting Actor | Trustees / Board, Mortgage Program Officer |
| Goal | 모든 모기지 계정과 주요 금액/갱신일을 요청 시 출력한다. |
| Trigger | 사용자가 모기지 목록 보고서를 요청한다. |
| Main Flow | 시스템은 모기지 계정을 조회하고 account number, mortgagees last name, purchase price, issue date, P&I, income, tax, insurance, update dates, estimated weekly grant를 표시한다. |
| Alternatives | 모기지 데이터가 없으면 empty state 메시지를 표시한다. 민감한 소득 정보는 권한에 따라 요약 표시할 수 있다. |
| Postcondition | 모기지 목록 보고서가 제공된다. |
| Related Requirements | FR-017, NFR-002, NFR-003, NFR-005 |

## 5. Out-of-Pilot Use Cases

| Candidate Use Case | Reason Excluded |
|---|---|
| Screen Applicant Eligibility | 수혜자 심사/선정은 시스템 밖에서 사람이 오프라인으로 판단한다. |
| Calculate 90% Commercial Mortgage Comparison | 90% 모기지 비교 조건은 파일럿 시스템의 직접 계산/관리 범위가 아니다. |
| Verify Employment Evidence | 고용 증거는 파일럿 데이터 목록에 포함되지 않는다. |
| Compare Home Price with Local Median | 지역 중간 주택 가격 데이터는 파일럿 범위에 포함되지 않는다. |
| Manage Applicant Documents | 문서 관리/OCR/외부 연동은 future scope이다. |

## 6. Use Case to Requirement Coverage

| Requirement | Covered By |
|---|---|
| FR-001 | UC-001 |
| FR-002 | UC-002 |
| FR-003 | UC-003 |
| FR-004 | UC-004 |
| FR-005 | UC-004 |
| FR-006 | UC-003, UC-004 |
| FR-007 | UC-003, UC-004 |
| FR-008 | UC-003, UC-004 |
| FR-009 | UC-003, UC-004 |
| FR-010 | UC-004 |
| FR-011 | UC-004 |
| FR-012 | UC-004 |
| FR-013 | UC-005 |
| FR-014 | UC-006 |
| FR-015 | UC-007 |
| FR-016 | UC-008 |
| FR-017 | UC-009 |
| FR-018~FR-021 | Out of Pilot |

## 7. Notes for Next Deliverable

- SSD 작성 시 내부 객체를 표시하지 말고 `Actor`와 `MSG Foundation Pilot System` 사이의 system events만 표현한다.
- UC-004, UC-005, UC-007은 SSD 우선 작성 대상이다.
- Q-008은 계산 관련 문서와 보고서에서 계속 open issue로 표시한다.
