# 03. Software Requirements Specification

## 1. Scope

이 문서는 The MSG Foundation 파일럿 시스템의 기능/비기능 요구사항을 정의한다. 현재 과제는 분석과 설계로 제한되며 구현은 수행하지 않는다.

## 2. Functional Requirements

### Data Management

| ID | Requirement | Priority | Acceptance Criteria | Source |
|---|---|---:|---|---|
| FR-001 | 시스템은 투자 항목을 관리해야 한다. | Must | item number, item name, estimated annual return, last updated date를 저장/조회할 수 있다. | PDF p.4 |
| FR-002 | 시스템은 운영 비용 데이터를 관리해야 한다. | Must | estimated annual operating expenses와 last updated date를 저장/조회할 수 있다. | PDF p.4 |
| FR-003 | 시스템은 모기지 계정 데이터를 관리해야 한다. | Must | account number, mortgagees last name, original purchase price, issue date, weekly P&I, current weekly income, tax, insurance premium 및 갱신일을 저장/조회할 수 있다. | PDF p.4-5 |
| FR-004 | 시스템은 투자 예상 연간 수익을 주간 금액으로 환산해야 한다. | Must | 모든 투자 항목의 estimated annual return 합계를 52로 나누어 weekly investment income을 계산한다. | PDF p.3 |
| FR-005 | 시스템은 운영 비용을 주간 금액으로 환산해야 한다. | Must | estimated annual operating expenses를 52로 나누어 weekly operating expense를 계산한다. | PDF p.3 |
| FR-006 | 시스템은 각 모기지의 주간 에스크로 지불액을 계산해야 한다. | Must | `(annual real-estate tax + annual homeowner insurance premium) / 52`로 계산한다. | PDF p.3 |
| FR-007 | 시스템은 각 모기지의 총 주간 모기지 비용을 계산해야 한다. | Must | `weekly P&I + weekly escrow`를 계산한다. | PDF p.3 |
| FR-008 | 시스템은 각 모기지의 부부 부담 한도를 계산해야 한다. | Must | `current combined gross weekly income * 0.28`을 계산한다. | PDF p.3 |
| FR-009 | 시스템은 각 모기지의 주간 보조금 예상액을 계산해야 한다. | Must | `max(0, total weekly mortgage cost - income cap)`으로 계산한다. | PDF p.3 |
| FR-010 | 시스템은 해당 주의 총 예상 모기지 상환액을 계산해야 한다. | Must | 활성 모기지들의 주간 상환 관련 금액 합계를 산출한다. 정확한 의미는 OI-REQ-001에 따름. | PDF p.3-4 |
| FR-011 | 시스템은 해당 주의 총 예상 보조금 금액을 계산해야 한다. | Must | 활성 모기지별 weekly grant estimate 합계를 산출한다. | PDF p.3-4 |
| FR-012 | 시스템은 주 시작 시 사용 가능한 금액을 계산해야 한다. | Must | 투자수익, 운영비용, 모기지 상환액, 보조금 총액을 이용해 산출한다. 정확한 산식은 OI-REQ-001에 따름. | PDF p.3-4 |
| FR-013 | 시스템은 주택 비용이 가용 모기지 금액 이하인지 판단할 수 있어야 한다. | Should | 입력된 주택 비용이 current available amount 이하이면 fundable로 표시한다. | PDF p.4 |
| FR-014 | 시스템은 승인된 주택 비용만큼 해당 주 가용 모기지 금액을 감소시켜야 한다. | Should | fund allocation record를 통해 weekly available amount가 감소한다. | PDF p.4 |
| FR-015 | 시스템은 주간 자금 결산서를 생성해야 한다. | Must | 계산 입력, 계산 결과, 사용 가능 금액, 보조금 총액을 포함한다. | PDF p.5 |
| FR-016 | 시스템은 모든 투자 목록 보고서를 생성해야 한다. | Must | 모든 투자 항목과 갱신일을 표시한다. | PDF p.5 |
| FR-017 | 시스템은 모든 모기지 목록 보고서를 생성해야 한다. | Must | 모든 모기지 계정과 주요 금액/갱신일을 표시한다. | PDF p.5 |

### Eligibility Requirements — Future/Out-of-Pilot Candidate

| ID | Requirement | Priority | Acceptance Criteria | Status |
|---|---|---:|---|---|
| FR-018 | 부부의 결혼 기간 조건을 검증한다. | Could | 결혼 기간이 1년 이상 10년 미만인지 판단한다. | Out of pilot unless confirmed |
| FR-019 | 부부 양측의 유급 고용과 전년도 48주 정규직 고용 증거를 검증한다. | Could | 증빙 제출 여부를 기록한다. | Out of pilot unless confirmed |
| FR-020 | 주택 가격이 지역 중간 가격보다 낮은지 검증한다. | Could | 지역 중간 가격 데이터와 비교한다. | Out of pilot unless confirmed |
| FR-021 | 90% 모기지 조건 또는 저축 부족 조건을 판단한다. | Could | 일반 금융기관 이용 곤란 여부를 표시한다. | Needs Verification |

## 3. Non-functional Requirements

| ID | Requirement | Priority | Acceptance Criteria |
|---|---|---:|---|
| NFR-001 | 계산 정확성 | Must | 금액 계산은 센트 단위 decimal arithmetic을 사용하고 테스트 케이스로 검증 가능해야 한다. |
| NFR-002 | 추적성 | Must | 보고서에 사용된 입력 데이터와 갱신일을 확인할 수 있어야 한다. |
| NFR-003 | 사용성 | Should | 컴퓨터 경험이 적은 수탁자/관리자도 보고서를 이해할 수 있어야 한다. |
| NFR-004 | 최소 범위 | Must | 파일럿은 주간 자금 계산에 필요한 세 데이터 유형만 포함한다. |
| NFR-005 | 보안 | Must | 소득/모기지 정보는 권한 있는 사용자만 조회/수정할 수 있다. |
| NFR-006 | 감사 가능성 | Should | 계산 실행과 데이터 변경은 누가 언제 했는지 추적 가능해야 한다. |
| NFR-007 | 확장성 | Should | 추후 신청 심사, 문서 관리, 외부 연동으로 확장 가능한 구조여야 한다. |
| NFR-008 | 가용성 | Could | 파일럿은 업무 시간 중 사용 가능하면 충분하며 고가용성은 필수 아님. |

## 4. Data Requirements

| ID | Data Object | Fields |
|---|---|---|
| DR-001 | Investment | item number, item name, estimated annual return, return last updated date |
| DR-002 | Operating Expense | estimated annual operating expenses, expenses last updated date |
| DR-003 | Mortgage | account number, mortgagees last name, original purchase price, issue date, weekly P&I, current weekly gross income, income updated date, annual tax, tax updated date, annual insurance premium, insurance updated date |
| DR-004 | Weekly Computation | week start date, weekly investment income, weekly operating expense, expected mortgage repayment, expected grants, starting available amount, remaining available amount |
| DR-005 | Fund Allocation | week start date, mortgage/account or application reference, home cost, approved/fundable flag, amount deducted |

## 5. Business Rules

| ID | Rule |
|---|---|
| BR-001 | Weekly investment income = sum(estimated annual return of all investments) / 52. |
| BR-002 | Weekly operating expense = estimated annual operating expenses / 52. |
| BR-003 | Weekly escrow payment = (annual real-estate tax + annual homeowner insurance premium) / 52. |
| BR-004 | Total weekly mortgage cost = weekly P&I + weekly escrow. |
| BR-005 | Couple weekly affordability cap = current combined gross weekly income * 28%. |
| BR-006 | Weekly grant = max(0, total weekly mortgage cost - affordability cap). |
| BR-007 | A new home purchase is fundable within the current week if home cost <= remaining available mortgage amount. |
| BR-008 | When a home purchase is funded, remaining weekly available amount decreases by home cost. |

## 6. Open Issues

| ID | Issue | Impact |
|---|---|---|
| OI-REQ-001 | Starting available amount formula sign convention is ambiguous. | Affects core computation. |
| OI-REQ-002 | Meaning of “total expected mortgage repayments” is ambiguous. | Affects weekly funds report. |
| OI-REQ-003 | Whether eligibility screening is in pilot scope is ambiguous. | Affects data model and UI. |
| OI-REQ-004 | Report medium is not specified. | Affects architecture/UI. |
| OI-REQ-005 | Rounding rules are not specified. | Affects financial correctness. |
