# 03. System Sequence Diagrams

## 1. Purpose

이 문서는 MSG Foundation 파일럿 시스템의 요구사항 분석 산출물인 System Sequence Diagram(SSD)을 설명한다. 실제 다이어그램은 draw.io 형식으로 관리한다.

Draw.io file:

```text
docs/03-requirements/system-sequence-diagrams.drawio
```

## 2. SSD Boundary Rule

SSD는 actor와 system boundary 사이의 system event만 표현한다.

- 내부 객체, service, repository, calculator는 SSD에 표시하지 않는다.
- 내부 객체 간 협력은 이후 설계 단계의 Design Sequence Diagram에서 표현한다.
- 모든 SSD의 system participant는 `MSG Foundation Pilot System`이다.

## 3. Included Diagrams

| SSD ID | Diagram Page | Actor | Related Use Case | Related Requirements |
|---|---|---|---|---|
| SSD-001 | Run Weekly Funds Computation | Foundation Manager | UC-004 | FR-004~FR-012 |
| SSD-002 | Manage Mortgage Data | Mortgage Program Officer | UC-003 | FR-003, FR-006~FR-009 |
| SSD-003 | Check Home Fundability | Foundation Manager | UC-005, UC-006 | FR-013, FR-014 |
| SSD-004 | Generate Reports | Foundation Manager / Trustees | UC-007~UC-009 | FR-015~FR-017 |

## 4. Diagram Notes

### SSD-001. Run Weekly Funds Computation

Main system events:

1. `requestWeeklyComputation(weekStartDate)`
2. `showDataFreshnessWarnings(warnings)`
3. `confirmRunComputation()`
4. `returnWeeklyComputationResult(startingAvailableAmount, expectedGrants, warnings)`
5. `finalizeOrSaveReport()`
6. `confirmComputationSaved(computationId)`

Domain decision carried forward:

- Q-008: `expected mortgage repayments` is the sum of expected beneficiary-paid weekly repayments for active mortgages.

### SSD-002. Manage Mortgage Data

Main system events:

1. `openMortgageManagement()`
2. `showMortgageList()`
3. `submitMortgageData(account, P&I, income, tax, insurance, updatedDates)`
4. `validateAndShowResult(success or errors)`
5. `saveMortgageRecord()`
6. `confirmMortgageSaved(accountNumber)`

Out-of-pilot reminder:

- Applicant eligibility and 90% mortgage screening are not included.

### SSD-003. Check Home Fundability

Main system events:

1. `requestFundabilityCheck(computationId, homeCost, reference)`
2. `returnFundabilityResult(fundable, remainingAmount, shortfall)`
3. `requestRecordAllocation(homeCost, reference)`
4. `confirmAllocationRecorded(remainingAfterAllocation)`

Design note:

- Checking fundability alone does not reduce remaining funds.
- Recording allocation performs the reduction.

### SSD-004. Generate Reports

Main system events:

1. `requestReport(reportType, optionalComputationId)`
2. `showReportOptionsOrValidationMessage()`
3. `confirmPrintOrExportRequest()`
4. `returnPrintableReport(reportContent, warnings)`

Design note:

- Output medium is a developer decision as long as print-on-request is supported.

## 5. Verification Checklist

- [x] SSDs use only actor and system boundary participants.
- [x] SSDs are based on brief use cases.
- [x] Q-008 resolved repayment definition is carried forward.
- [x] Draw.io file contains one page per SSD.
