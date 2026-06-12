# 07. Design Sequence Diagrams

## 1. Purpose

이 문서는 MSG Foundation 파일럿 시스템의 설계 단계 Sequence Diagram(DSD)을 설명한다. 실제 다이어그램 원본은 draw.io 형식으로 관리한다.

Draw.io file:

```text
docs/07-design/sequence-diagrams.drawio
```

## 2. DSD Boundary Rule

Design Sequence Diagram은 SSD와 달리 시스템 내부 객체 간 협력을 표현한다.

- UI, application service, repository, domain entity, calculator, report renderer를 표시한다.
- SSD의 actor-system event를 Operation Contract의 내부 처리 흐름으로 확장한다.
- 자격 심사, 고용 증거 확인, 주택 가격 검증, 90% 모기지 비교는 파일럿 범위 밖이므로 포함하지 않는다.

## 3. Included Diagrams

| DSD ID | Diagram Page | Related Use Case | Related Contract | Related Requirements |
|---|---|---|---|---|
| DSD-001 | Create Investment | UC-001 | OC-001 | FR-001 |
| DSD-002 | Update Investment Return | UC-001 | OC-002 | FR-001, FR-004 |
| DSD-003 | Update Operating Expense Estimate | UC-002 | OC-003 | FR-002, FR-005 |
| DSD-004 | Create Mortgage | UC-003 | OC-004 | FR-003, FR-006~FR-009 |
| DSD-005 | Update Mortgage Income | UC-003 | OC-005 | FR-003, FR-008, FR-009 |
| DSD-006 | Run Weekly Computation | UC-004 | OC-006 | FR-004~FR-012 |
| DSD-007 | Check Home Fundability | UC-005 | OC-007 | FR-013 |
| DSD-008 | Record Fund Allocation | UC-006 | OC-008 | FR-014 |
| DSD-009 | Generate Weekly Funds Report | UC-007 | OC-009 | FR-015 |
| DSD-010 | Generate Investment Listing Report | UC-008 | OC-010 | FR-016 |
| DSD-011 | Generate Mortgage Listing Report | UC-009 | OC-011 | FR-017 |

## 4. Diagram Notes

### DSD-001. Create Investment

The UI submits investment data to `InvestmentService`, which checks uniqueness through `InvestmentRepository`, creates an `Investment`, and persists it.

### DSD-002. Update Investment Return

`InvestmentService` loads the investment, applies the new expected return and update date, and saves the changed entity. Existing finalized weekly computation snapshots are not recalculated.

### DSD-003. Update Operating Expense Estimate

`OperatingExpenseService` creates or activates the latest `OperatingExpenseEstimate` and deactivates any previous active estimate if period-based records are used.

### DSD-004. Create Mortgage

`MortgageService` validates required mortgage fields, creates a `Mortgage`, and saves it. Applicant eligibility and 90% comparison are explicitly outside this flow.

### DSD-005. Update Mortgage Income

`MortgageService` updates income and update date, then may request `MortgageCalculator` for an updated affordability/grant preview.

### DSD-006. Run Weekly Computation

`WeeklyComputationService` loads active source data, calls `FundsAvailabilityCalculator`, delegates per-mortgage calculations to `MortgageCalculator`, creates input snapshots, attaches Q-008 warnings, and saves a `WeeklyComputation`.

### DSD-007. Check Home Fundability

`FundingService` loads a usable `WeeklyComputation`, compares home cost with `remainingAvailableAmount`, and returns the result without changing state.

### DSD-008. Record Fund Allocation

`FundingService` verifies the computation can fund the home cost, creates `FundAllocation`, applies the deduction to `WeeklyComputation.remainingAvailableAmount`, and saves both records.

### DSD-009. Generate Weekly Funds Report

`ReportService` loads the computation with snapshots and allocations, passes the view data to the report renderer, and returns a printable report with warnings.

### DSD-010. Generate Investment Listing Report

`ReportService` loads investments, builds a listing view with item number, item name, estimated annual return, and return last updated date, then passes it to the report renderer.

### DSD-011. Generate Mortgage Listing Report

`ReportService` loads mortgages, requests calculated weekly grant values where needed, applies report-view authorization for sensitive income fields, and passes the listing view to the report renderer.

## 5. Verification Checklist

- [x] Each DSD maps to one Operation Contract.
- [x] Internal objects are shown, unlike SSD.
- [x] Object interactions are consistent with the Class Diagram methods.
- [x] Q-008 remains visible in the weekly computation/report flow.
- [x] Out-of-pilot eligibility and 90% mortgage screening remain excluded.
- [x] Draw.io file contains eleven DSD pages.
