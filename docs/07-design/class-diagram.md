# 07. Class Diagram

## 1. Purpose

이 문서는 MSG Foundation 파일럿 시스템의 설계 단계 Class Diagram을 설명한다. 실제 다이어그램 원본은 draw.io 형식으로 관리한다.

Draw.io file:

```text
docs/07-design/class-diagram.drawio
```

## 2. Included Classes

| Class | Key Attributes | Key Operations |
|---|---|---|
| `Money` | `amount`, `currency` | `plus`, `minus`, `multiply`, `divide`, `roundToCents`, `isNonNegative` |
| `Investment` | `itemNumber`, `itemName`, `estimatedAnnualReturn`, `returnLastUpdatedDate` | `weeklyReturn`, `updateReturn` |
| `OperatingExpenseEstimate` | `estimateId`, `estimatedAnnualOperatingExpenses`, `expensesLastUpdatedDate`, `active` | `weeklyExpense`, `activate`, `replaceEstimate` |
| `Mortgage` | `accountNumber`, `mortgageesLastName`, `originalPurchasePrice`, `mortgageIssuedDate`, `weeklyPrincipalAndInterestPayment`, `currentCombinedGrossWeeklyIncome`, `annualRealEstateTax`, `annualHomeownerInsurancePremium`, `status` | `weeklyEscrow`, `totalWeeklyMortgageCost`, `affordabilityCap`, `weeklyGrant`, `expectedBeneficiaryRepayment`, `updateIncome`, `isActive` |
| `WeeklyComputation` | `computationId`, `weekStartDate`, `formulaVersion`, `weeklyInvestmentIncome`, `weeklyOperatingExpense`, `expectedMortgageRepayments`, `expectedGrants`, `startingAvailableAmount`, `remainingAvailableAmount`, `status`, `createdAt`, `warnings` | `addInputSnapshot`, `canFund`, `applyAllocation`, `attachWarning`, `finalize` |
| `WeeklyComputationItem` | `id`, `computationId`, `sourceType`, `sourceId`, `snapshot` | `capture` |
| `FundAllocation` | `allocationId`, `computationId`, `homeCost`, `reference`, `fundable`, `remainingAfterAllocation`, `createdAt` | `record`, `markFundable` |
| `MortgageCalculator` | n/a domain service | `calculateWeeklyEscrow`, `calculateTotalWeeklyCost`, `calculateAffordabilityCap`, `calculateWeeklyGrant`, `calculateExpectedBeneficiaryRepayment` |
| `FundsAvailabilityCalculator` | n/a domain service | `calculateWeeklyInvestmentIncome`, `calculateWeeklyOperatingExpense`, `calculateExpectedMortgageRepayments`, `calculateExpectedGrants`, `calculateStartingAvailableAmount`, `checkHomeFundability` |
| `ReportService` | n/a application/report service | `generateWeeklyFundsReport`, `generateInvestmentListing`, `generateMortgageListing` |

## 3. Relationships and Multiplicity

| Relationship | Multiplicity | Meaning |
|---|---|---|
| `WeeklyComputation` has `WeeklyComputationItem` | `1` to `0..*` | A computation stores input snapshots used for audit and reporting. |
| `WeeklyComputation` has `FundAllocation` | `1` to `0..*` | A weekly computation may record zero or more approved allocations. |
| `WeeklyComputationItem` snapshots source records | `0..*` to `0..1` source | Snapshot sources may be `Investment`, `OperatingExpenseEstimate`, or `Mortgage`. |
| `FundsAvailabilityCalculator` uses `MortgageCalculator` | dependency | Weekly funds calculation delegates per-mortgage calculations. |
| `FundsAvailabilityCalculator` uses source entities | dependency | It reads investments, the active expense estimate, and active mortgages. |
| `ReportService` reads calculation results | dependency | It renders computations, allocations, investments, and mortgages into reports. |

## 4. Calculation Responsibility Notes

- `Mortgage` owns per-account state and exposes calculation-friendly methods for escrow, total weekly cost, affordability cap, grant estimate, and expected beneficiary repayment.
- `MortgageCalculator` centralizes formula logic for mortgage-related calculations so UI and persistence code do not duplicate financial rules.
- `FundsAvailabilityCalculator` owns the Q-001 weekly availability formula:

```text
weekly investment income - weekly operating expenses + expected mortgage repayments - expected grants
```

- Q-008 is resolved by user decision. `MortgageCalculator.calculateExpectedBeneficiaryRepayment(...)` calculates `total weekly mortgage cost - weekly grant` for each active mortgage, and `FundsAvailabilityCalculator.calculateExpectedMortgageRepayments(...)` aggregates those values.
- Eligibility screening, employment evidence checks, local median home price checks, and the 90% mortgage comparison remain out of pilot scope and are not modeled as classes here.

## 5. Verification Checklist

- [x] Attributes align with `docs/08-data/data-model.md`.
- [x] Operations support `docs/07-design/operation-contracts.md`.
- [x] Relationships and multiplicities match the domain model.
- [x] Q-008 calculation responsibility is visible and resolved.
- [x] Draw.io file contains the class diagram source.
