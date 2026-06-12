# 07. Operation Contracts

## 1. Purpose

이 문서는 MSG Foundation 파일럿 시스템의 설계 단계 산출물로, 주요 시스템 오퍼레이션의 사전 조건(preconditions)과 사후 조건(postconditions)을 정의한다. Operation Contract는 SSD의 system event와 이후 Class Diagram / Design Sequence Diagram 사이를 연결한다.

## 2. Contract Conventions

| Item | Convention |
|---|---|
| Scope | 분석/설계 산출물만 작성하며 구현 코드는 작성하지 않는다. |
| Money | 모든 금액은 decimal/fixed-point로 계산하고 저장/표시는 센트 단위로 반올림한다. |
| Dates | 주 시작일은 공휴일/재단 휴업일을 제외한 첫 영업일이다. |
| Actor Authorization | 사전 조건에는 최소 권한을 명시한다. 세부 인증 구현은 개발자 재량이다. |
| Q-008 | `expected mortgage repayments`는 활성 mortgage별 수혜자 실제 예상 주간 상환액의 합으로 확정한다. |

## 3. Contract Summary

| Contract | Operation | Related UC | Related SSD | Primary Domain Change |
|---|---|---|---|---|
| OC-001 | `createInvestment(...)` | UC-001 | n/a | Investment 생성 |
| OC-002 | `updateInvestmentReturn(...)` | UC-001 | n/a | Investment 예상 수익/갱신일 변경 |
| OC-003 | `updateOperatingExpenseEstimate(...)` | UC-002 | n/a | 활성 OperatingExpenseEstimate 생성/활성화 |
| OC-004 | `createMortgage(...)` | UC-003 | SSD-002 | Mortgage 생성 |
| OC-005 | `updateMortgageIncome(...)` | UC-003 | SSD-002 | Mortgage 소득/갱신일 변경 |
| OC-006 | `runWeeklyComputation(...)` | UC-004 | SSD-001 | WeeklyComputation 및 input snapshot 생성 |
| OC-007 | `checkHomeFundability(...)` | UC-005 | SSD-003 | Fundability result 산출, 상태 변경 없음 |
| OC-008 | `recordFundAllocation(...)` | UC-006 | SSD-003 | FundAllocation 생성 및 remaining amount 차감 |
| OC-009 | `generateWeeklyFundsReport(...)` | UC-007 | SSD-004 | Report view 생성, 선택적으로 audit event 기록 |
| OC-010 | `generateInvestmentListingReport(...)` | UC-008 | SSD-004 | Investment listing report view 생성 |
| OC-011 | `generateMortgageListingReport(...)` | UC-009 | SSD-004 | Mortgage listing report view 생성 |

## 4. Contracts

### OC-001. createInvestment

| Field | Description |
|---|---|
| Operation | `createInvestment(itemNumber, itemName, estimatedAnnualReturn, returnLastUpdatedDate)` |
| Cross References | UC-001, FR-001, DR-001 |
| Preconditions | Actor is authorized to manage investment data. `itemNumber` is not already used. `itemName` is not blank. `estimatedAnnualReturn >= 0`. `returnLastUpdatedDate` is provided. |
| Postconditions | A new `Investment` is created. `Investment.itemNumber` is set. `Investment.itemName` is set. `Investment.estimatedAnnualReturn` is set. `Investment.returnLastUpdatedDate` is set. The investment can contribute to future weekly investment income calculations. |
| Notes | Investment Advisor may provide the values, but the default pilot actor entering them is Foundation Manager. |

### OC-002. updateInvestmentReturn

| Field | Description |
|---|---|
| Operation | `updateInvestmentReturn(itemNumber, estimatedAnnualReturn, returnLastUpdatedDate)` |
| Cross References | UC-001, FR-001, FR-004, DR-001 |
| Preconditions | Actor is authorized to manage investment data. `Investment` with `itemNumber` exists. `estimatedAnnualReturn >= 0`. `returnLastUpdatedDate` is provided and represents the latest update date. |
| Postconditions | The matching `Investment.estimatedAnnualReturn` is replaced. `Investment.returnLastUpdatedDate` is replaced. Future weekly computations use the updated value. Existing finalized `WeeklyComputation` snapshots are not recalculated automatically. |
| Notes | Preserving existing snapshots supports report traceability. |

### OC-003. updateOperatingExpenseEstimate

| Field | Description |
|---|---|
| Operation | `updateOperatingExpenseEstimate(estimatedAnnualOperatingExpenses, expensesLastUpdatedDate)` |
| Cross References | UC-002, FR-002, FR-005, DR-002 |
| Preconditions | Actor is authorized to manage operating expense data. `estimatedAnnualOperatingExpenses >= 0`. `expensesLastUpdatedDate` is provided. |
| Postconditions | A new or updated `OperatingExpenseEstimate` exists. It is marked as the active/latest estimate. Any prior active estimate is no longer the current active estimate if period-based records are used. Future weekly computations use this active estimate. Existing finalized computation snapshots are unchanged. |
| Notes | The source only requires the latest value and last updated date; full historical change tracking is not required for the pilot. |

### OC-004. createMortgage

| Field | Description |
|---|---|
| Operation | `createMortgage(accountNumber, mortgageesLastName, originalPurchasePrice, issueDate, weeklyPI, currentWeeklyIncome, incomeUpdatedDate, annualTax, taxUpdatedDate, annualInsurancePremium, insuranceUpdatedDate)` |
| Cross References | UC-003, SSD-002, FR-003, FR-006, FR-007, FR-008, FR-009, DR-003 |
| Preconditions | Actor is authorized to manage mortgage data. `accountNumber` is not already used. Required dates are provided. All monetary inputs are `>= 0`. Eligibility screening has already been handled outside the system. |
| Postconditions | A new `Mortgage` is created. Mortgage identity, purchase price, issue date, weekly P&I, current weekly income, annual tax, annual insurance premium, and update dates are set. `Mortgage.status` is set to `Active` unless explicitly created as `Draft`. The mortgage can be included in weekly computation when active. |
| Notes | This operation does not determine whether the couple qualifies for MSG Foundation assistance. |

### OC-005. updateMortgageIncome

| Field | Description |
|---|---|
| Operation | `updateMortgageIncome(accountNumber, currentWeeklyIncome, incomeUpdatedDate)` |
| Cross References | UC-003, SSD-002, FR-003, FR-008, FR-009, DR-003 |
| Preconditions | Actor is authorized to manage mortgage data. `Mortgage` with `accountNumber` exists. `currentWeeklyIncome >= 0`. `incomeUpdatedDate` is provided. Mortgage is not `Closed` or `Cancelled`. |
| Postconditions | `Mortgage.currentCombinedGrossWeeklyIncome` is replaced. `Mortgage.incomeLastUpdatedDate` is replaced. Future grant and affordability cap calculations use the updated income. Existing finalized computation snapshots are unchanged. |
| Notes | A similar update operation may be used for tax, insurance, P&I, or status changes. This contract focuses on income because it directly affects the 28% cap and grant calculation. |

### OC-006. runWeeklyComputation

| Field | Description |
|---|---|
| Operation | `runWeeklyComputation(weekStartDate, formulaVersion)` |
| Cross References | UC-004, SSD-001, FR-004~FR-012, DR-004, BR-001~BR-011 |
| Preconditions | Actor is authorized to run weekly computation. `weekStartDate` is the first business day of the week. Active investment data exists or the system can treat missing investments as zero with warning. Active operating expense estimate exists or the system displays a blocking warning. Active mortgages are available or mortgage totals are zero. Formula version is explicit. |
| Postconditions | Weekly investment income is calculated from investments. Weekly operating expense is calculated from the active operating expense estimate. Weekly escrow, total weekly mortgage cost, affordability cap, and weekly grant are calculated for active mortgages. Expected grants total is calculated. Expected mortgage repayments total is calculated as the sum of active mortgage expected beneficiary weekly repayments, where each repayment equals `total weekly mortgage cost - weekly grant`. Starting available amount is calculated as `weekly investment income - weekly operating expenses + expected mortgage repayments - expected grants`. A `WeeklyComputation` is created with input snapshots and result fields. Warnings are attached for stale or missing data. |
| Notes | Q-008 is resolved by user decision. `expected mortgage repayments` is the expected amount actually paid by beneficiaries to the foundation. |

### OC-007. checkHomeFundability

| Field | Description |
|---|---|
| Operation | `checkHomeFundability(computationId, homeCost, reference)` |
| Cross References | UC-005, SSD-003, FR-013, DR-005 |
| Preconditions | Actor is authorized to check fundability. `WeeklyComputation` with `computationId` exists. Computation is usable for the current week. `homeCost >= 0`. |
| Postconditions | The system compares `homeCost` with `WeeklyComputation.remainingAvailableAmount`. A fundability result is returned. If `homeCost <= remainingAvailableAmount`, result is `fundable = true`. If `homeCost > remainingAvailableAmount`, result is `fundable = false` and shortfall is calculated. No `FundAllocation` is created by this operation. `remainingAvailableAmount` is not changed. |
| Notes | The SSD separates checking fundability from recording an allocation to avoid accidental fund reduction. |

### OC-008. recordFundAllocation

| Field | Description |
|---|---|
| Operation | `recordFundAllocation(computationId, homeCost, reference)` |
| Cross References | UC-006, SSD-003, FR-014, DR-005 |
| Preconditions | Actor is authorized to record allocations. `WeeklyComputation` with `computationId` exists and is not archived/cancelled. `homeCost >= 0`. `homeCost <= WeeklyComputation.remainingAvailableAmount`. Allocation is approved by the applicable offline/business process. |
| Postconditions | A new `FundAllocation` is created. `FundAllocation.computationId` references the weekly computation. `FundAllocation.homeCost`, `reference`, `fundable = true`, and `remainingAfterAllocation` are set. `WeeklyComputation.remainingAvailableAmount` is decreased by `homeCost`. The allocation appears in the weekly funds report. |
| Notes | This operation records a funding decision within the weekly available amount. It does not perform applicant eligibility screening. |

### OC-009. generateWeeklyFundsReport

| Field | Description |
|---|---|
| Operation | `generateWeeklyFundsReport(computationId)` |
| Cross References | UC-007, SSD-004, FR-015, DR-004, NFR-002, NFR-003 |
| Preconditions | Actor is authorized to view the weekly funds report. `WeeklyComputation` with `computationId` exists. |
| Postconditions | A weekly funds report view is produced. The report includes week start date, formula version, generated timestamp, input summary, expected mortgage repayments, expected grants, starting available amount, allocations, remaining available amount, and data freshness warnings. Source data is not modified. If audit logging is enabled, a report-generated audit event is recorded. |
| Notes | Output may be screen, printable text/HTML, or other developer-selected medium as long as print-on-request is supported. |

### OC-010. generateInvestmentListingReport

| Field | Description |
|---|---|
| Operation | `generateInvestmentListingReport()` |
| Cross References | UC-008, SSD-004, FR-016, DR-001, NFR-002, NFR-003 |
| Preconditions | Actor is authorized to view investment reports. Investment records may be empty, but the report request is valid. |
| Postconditions | An investment listing report view is produced. The report includes each investment item number, item name, estimated annual return, and return last updated date. If no investments exist, the report includes an empty-state message. Source data is not modified. If audit logging is enabled, a report-generated audit event is recorded. |
| Notes | This operation supports the required investment listing report and is separate from creating or updating investment records. |

### OC-011. generateMortgageListingReport

| Field | Description |
|---|---|
| Operation | `generateMortgageListingReport()` |
| Cross References | UC-009, SSD-004, FR-017, DR-003, NFR-002, NFR-003, NFR-005 |
| Preconditions | Actor is authorized to view mortgage reports and any sensitive income fields included by their role. Mortgage records may be empty, but the report request is valid. |
| Postconditions | A mortgage listing report view is produced. The report includes account number, mortgagees last name, original purchase price, issue date, weekly P&I, current weekly income where authorized, income/tax/insurance update dates, annual tax, annual insurance premium, and estimated weekly grant. If no mortgages exist, the report includes an empty-state message. Source data is not modified. If audit logging is enabled, a report-generated audit event is recorded. |
| Notes | This operation does not perform applicant eligibility screening, 90% mortgage comparison, or employment evidence verification. |

## 5. Out-of-Pilot Operation Candidates

| Operation Candidate | Reason Excluded |
|---|---|
| `screenApplicantEligibility(...)` | 수혜자 심사/선정은 시스템 밖에서 오프라인으로 수행한다. |
| `calculateCommercial90PercentMortgage(...)` | 90% 모기지 비교 조건은 파일럿 시스템 직접 계산 범위가 아니다. |
| `verifyEmploymentEvidence(...)` | 고용 증거 데이터는 파일럿 데이터 목록에 포함되지 않는다. |
| `compareHomePriceToLocalMedian(...)` | 지역 중간 주택 가격 데이터는 파일럿 범위에 포함되지 않는다. |

## 6. Traceability to Next Design Artifacts

| Next Artifact | How This Document Is Used |
|---|---|
| Class Diagram | Contract postconditions identify required attributes, methods, and associations. |
| Design Sequence Diagram | Each contract maps to internal object collaboration in sequence diagrams. |
| Test Strategy | Preconditions and postconditions become validation and acceptance checks. |

## 7. Resolved Calculation Decision

Q-008 is resolved by user decision:

```text
expected mortgage repayments
= sum(active mortgage expected beneficiary weekly repayment)
```

Per active mortgage:

```text
expected beneficiary weekly repayment
= total weekly mortgage cost - weekly grant
= min(total weekly mortgage cost, affordability cap)
```

This avoids double-counting grants in the Q-001 formula.
