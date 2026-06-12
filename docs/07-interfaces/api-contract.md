# 07. Interface and API Contract

## 1. Interface Strategy

현재 과제는 구현 전 설계 단계이므로 HTTP API를 반드시 구현한다는 의미는 아니다. 그러나 구현자가 CLI, Web, Desktop 중 어떤 형태를 선택하더라도 동일한 application service contract를 따를 수 있도록 논리 API를 정의한다.

## 2. Resource Model

- Investment
- OperatingExpenseEstimate
- Mortgage
- WeeklyComputation
- FundAllocation
- Report

## 3. Logical API Endpoints

### Investments

#### `GET /investments`

Returns all investment records.

Response:

```json
{
  "items": [
    {
      "itemNumber": "INV-001",
      "itemName": "Treasury Bond Fund",
      "estimatedAnnualReturn": "125000.00",
      "returnLastUpdatedDate": "2026-04-01"
    }
  ]
}
```

#### `POST /investments`

Creates an investment record.

Request:

```json
{
  "itemNumber": "INV-001",
  "itemName": "Treasury Bond Fund",
  "estimatedAnnualReturn": "125000.00",
  "returnLastUpdatedDate": "2026-04-01"
}
```

### Operating Expense Estimate

#### `GET /operating-expense-estimates/current`

Returns current active annual operating expense estimate.

Response:

```json
{
  "estimatedAnnualOperatingExpenses": "520000.00",
  "expensesLastUpdatedDate": "2026-04-01"
}
```

#### `POST /operating-expense-estimates`

Creates a new estimate. The latest effective estimate becomes current unless implementation defines effective dates.

### Mortgages

#### `GET /mortgages`

Returns all mortgage records.

#### `POST /mortgages`

Creates a mortgage record.

Request:

```json
{
  "accountNumber": "MTG-1001",
  "mortgageesLastName": "Smith",
  "originalPurchasePrice": "250000.00",
  "mortgageIssuedDate": "2026-05-15",
  "weeklyPrincipalAndInterestPayment": "1450.00",
  "currentCombinedGrossWeeklyIncome": "4200.00",
  "incomeLastUpdatedDate": "2026-06-01",
  "annualRealEstateTax": "5200.00",
  "taxLastUpdatedDate": "2026-01-01",
  "annualHomeownerInsurancePremium": "1300.00",
  "insuranceLastUpdatedDate": "2026-01-01",
  "status": "ACTIVE"
}
```

### Weekly Computation

#### `POST /weekly-computations`

Runs and stores weekly funds computation.

Request:

```json
{
  "weekStartDate": "2026-06-15",
  "formulaVersion": "Q-001-resolved-v1"
}
```

Response:

```json
{
  "computationId": "WC-2026-06-15",
  "weekStartDate": "2026-06-15",
  "weeklyInvestmentIncome": "100000.00",
  "weeklyOperatingExpense": "10000.00",
  "expectedMortgageRepayments": "25000.00",
  "expectedGrants": "5000.00",
  "startingAvailableAmount": "110000.00",
  "remainingAvailableAmount": "110000.00",
  "formulaVersion": "Q-001-resolved-v1",
  "status": "RESOLVED_FORMULA_WITH_OPEN_REPAYMENT_SCOPE",
  "warnings": [
    "Expected mortgage repayments use the recommended beneficiary-paid interpretation; final domain confirmation is still required."
  ]
}
```

> Formula Q-001-resolved-v1: `weeklyInvestmentIncome - weeklyOperatingExpense + expectedMortgageRepayments - expectedGrants`.

#### `POST /weekly-computations/{id}/fundability-checks`

Checks whether a home cost can be funded from the remaining available weekly amount.

Request:

```json
{
  "homeCost": "240000.00",
  "reference": "Application or mortgage reference optional"
}
```

Response:

```json
{
  "fundable": false,
  "homeCost": "240000.00",
  "remainingAvailableAmount": "110000.00",
  "shortfall": "130000.00"
}
```

#### `POST /weekly-computations/{id}/allocations`

Records an approved fund allocation and reduces remaining available amount.

Request:

```json
{
  "homeCost": "95000.00",
  "reference": "Approved purchase"
}
```

### Reports

#### `GET /reports/weekly-funds/{computationId}`

Returns the weekly funds computation report.

#### `GET /reports/investments`

Returns all investments listing.

#### `GET /reports/mortgages`

Returns all mortgages listing.

## 4. Error Contract

```json
{
  "errorCode": "VALIDATION_ERROR",
  "message": "annualRealEstateTax must be non-negative",
  "fieldErrors": [
    {"field": "annualRealEstateTax", "message": "must be >= 0"}
  ]
}
```

| Code | Meaning |
|---|---|
| VALIDATION_ERROR | Input field invalid. |
| NOT_FOUND | Requested resource does not exist. |
| DUPLICATE_ID | item number/account number already exists. |
| OPEN_DOMAIN_DECISION | Operation depends on a still-open domain interpretation, such as expected mortgage repayment scope. |
| INSUFFICIENT_FUNDS | Allocation exceeds remaining weekly available amount. |

## 5. Compatibility Rules

- Do not rename JSON fields without a versioned contract.
- Amounts are serialized as strings to avoid floating point ambiguity.
- Dates use ISO-8601 `YYYY-MM-DD`.
- Use explicit formula version for weekly computations.

## 6. Event Candidates

| Event | Trigger | Payload |
|---|---|---|
| `WeeklyComputationCreated` | Weekly computation saved | computationId, weekStartDate, result summary |
| `FundAllocationRecorded` | Home purchase allocation recorded | computationId, homeCost, remainingAvailableAmount |
| `MortgageIncomeUpdated` | Mortgage income updated | accountNumber, newIncome, updatedDate |
| `InvestmentReturnUpdated` | Investment expected return updated | itemNumber, newReturn, updatedDate |
