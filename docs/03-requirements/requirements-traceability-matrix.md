# 03. Requirements Traceability Matrix

| Req ID | Source Evidence | Design Artifact | Test Artifact | Status |
|---|---|---|---|---|
| FR-001 | PDF p.4 investment data | UC-001; OC-001/OC-002; DSD-001/DSD-002; `docs/07-design/class-diagram.md` Investment; `docs/08-data/data-model.md` Investment | TC-INV-001 | Design Complete |
| FR-002 | PDF p.4 operating expense data | UC-002; OC-003; DSD-003; `docs/07-design/class-diagram.md` OperatingExpenseEstimate; `docs/08-data/data-model.md` OperatingExpenseEstimate | TC-OPEX-001 | Design Complete |
| FR-003 | PDF p.4-5 mortgage data | UC-003; SSD-002; OC-004/OC-005; DSD-004/DSD-005; `docs/07-design/class-diagram.md` Mortgage; `docs/08-data/data-model.md` Mortgage | TC-MTG-001 | Design Complete |
| FR-004 | PDF p.3 weekly investment income | UC-004; SSD-001; OC-006; DSD-006; `docs/07-design/class-diagram.md` Investment/FundsAvailabilityCalculator | TC-CALC-001 | Design Complete |
| FR-005 | PDF p.3 weekly operating expense | UC-004; SSD-001; OC-006; DSD-006; `docs/07-design/class-diagram.md` OperatingExpenseEstimate/FundsAvailabilityCalculator | TC-CALC-002 | Design Complete |
| FR-006 | PDF p.3 escrow payment | UC-003/UC-004; SSD-001/SSD-002; OC-004/OC-006; DSD-004/DSD-006; `docs/07-design/class-diagram.md` MortgageCalculator | TC-CALC-003 | Design Complete |
| FR-007 | PDF p.3 total weekly payment | UC-003/UC-004; SSD-001/SSD-002; OC-004/OC-006; DSD-004/DSD-006; `docs/07-design/class-diagram.md` Mortgage/MortgageCalculator | TC-CALC-004 | Design Complete |
| FR-008 | PDF p.3 28% income limit | UC-003/UC-004; SSD-001/SSD-002; OC-005/OC-006; DSD-005/DSD-006; `docs/07-design/class-diagram.md` MortgageCalculator | TC-CALC-005 | Design Complete |
| FR-009 | PDF p.3 grant difference | UC-003/UC-004; SSD-001/SSD-002; OC-005/OC-006; DSD-005/DSD-006; `docs/07-design/class-diagram.md` MortgageCalculator | TC-CALC-006 | Design Complete |
| FR-010 | PDF p.3-4 total expected repayments | UC-004; SSD-001; OC-006; DSD-006; `docs/07-design/class-diagram.md` MortgageCalculator/FundsAvailabilityCalculator; `docs/03-requirements/software-requirements-specification.md` OI-REQ-002/BR-007 | TC-CALC-007 | Design Complete |
| FR-011 | PDF p.3-4 total grants | UC-004; SSD-001; OC-006; DSD-006; `docs/07-design/class-diagram.md` MortgageCalculator/FundsAvailabilityCalculator | TC-CALC-008 | Design Complete |
| FR-012 | PDF p.4 amount available | UC-004; SSD-001; OC-006; DSD-006; `docs/07-design/class-diagram.md` FundsAvailabilityCalculator; `docs/03-requirements/software-requirements-specification.md` BR-008 | TC-CALC-009 | Design Complete |
| FR-013 | PDF p.4 home cost <= mortgage amount | UC-005; SSD-003; OC-007; DSD-007; `docs/07-design/class-diagram.md` WeeklyComputation/FundsAvailabilityCalculator | TC-FUND-001 | Design Complete |
| FR-014 | PDF p.4 reduce available amount | UC-006; SSD-003; OC-008; DSD-008; `docs/07-design/class-diagram.md` WeeklyComputation/FundAllocation; `docs/08-data/data-model.md` FundAllocation | TC-FUND-002 | Design Complete |
| FR-015 | PDF p.5 weekly funds report | UC-007; SSD-004; OC-009; DSD-009; `docs/07-design/class-diagram.md` ReportService; `docs/09-ui/report-specification.md` | TC-RPT-001 | Design Complete |
| FR-016 | PDF p.5 investment listing | UC-008; SSD-004; OC-010; DSD-010; `docs/07-design/class-diagram.md` ReportService/Investment; `docs/09-ui/report-specification.md` | TC-RPT-002 | Design Complete |
| FR-017 | PDF p.5 mortgage listing | UC-009; SSD-004; OC-011; DSD-011; `docs/07-design/class-diagram.md` ReportService/Mortgage; `docs/09-ui/report-specification.md` | TC-RPT-003 | Design Complete |
| FR-018 | PDF p.3 marriage duration | Out-of-pilot use case list; Future eligibility module | n/a - out-of-pilot scope review only | Out of Pilot |
| FR-019 | PDF p.3 employment evidence | Out-of-pilot use case list; Future eligibility module | n/a - out-of-pilot scope review only | Out of Pilot |
| FR-020 | PDF p.3 median area home price | Out-of-pilot use case list; Future eligibility module | n/a - out-of-pilot scope review only | Out of Pilot |
| FR-021 | PDF p.3 90% mortgage/savings condition | Out-of-pilot use case list; Future eligibility module | n/a - out-of-pilot scope review only | Out of Pilot |
