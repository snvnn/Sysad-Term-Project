# 03. Requirements Traceability Matrix

| Req ID | Source Evidence | Design Artifact | Test Artifact | Status |
|---|---|---|---|---|
| FR-001 | PDF p.4 investment data | `docs/08-data/data-model.md` Investment | TC-INV-001 | Draft |
| FR-002 | PDF p.4 operating expense data | `docs/08-data/data-model.md` OperatingExpense | TC-OPEX-001 | Draft |
| FR-003 | PDF p.4-5 mortgage data | `docs/08-data/data-model.md` Mortgage | TC-MTG-001 | Draft |
| FR-004 | PDF p.3 weekly investment income | `docs/07-interfaces/api-contract.md` computation | TC-CALC-001 | Draft |
| FR-005 | PDF p.3 weekly operating expense | `docs/07-interfaces/api-contract.md` computation | TC-CALC-002 | Draft |
| FR-006 | PDF p.3 escrow payment | `docs/04-domain/domain-model.md` BR-003 | TC-CALC-003 | Draft |
| FR-007 | PDF p.3 total weekly payment | `docs/04-domain/domain-model.md` BR-004 | TC-CALC-004 | Draft |
| FR-008 | PDF p.3 28% income limit | `docs/04-domain/domain-model.md` BR-005 | TC-CALC-005 | Draft |
| FR-009 | PDF p.3 grant difference | `docs/04-domain/domain-model.md` BR-006 | TC-CALC-006 | Draft |
| FR-010 | PDF p.3-4 total expected repayments | `docs/03-requirements/software-requirements-specification.md` OI-REQ-002 | TC-CALC-007 | Open / Needs Discussion |
| FR-011 | PDF p.3-4 total grants | `docs/07-interfaces/api-contract.md` computation | TC-CALC-008 | Draft |
| FR-012 | PDF p.4 amount available | `docs/03-requirements/software-requirements-specification.md` BR-009 | TC-CALC-009 | Resolved |
| FR-013 | PDF p.4 home cost <= mortgage amount | `docs/07-interfaces/api-contract.md` fundability | TC-FUND-001 | Draft |
| FR-014 | PDF p.4 reduce available amount | `docs/08-data/data-model.md` FundAllocation | TC-FUND-002 | Draft |
| FR-015 | PDF p.5 weekly funds report | `docs/09-ui/report-specification.md` | TC-RPT-001 | Draft |
| FR-016 | PDF p.5 investment listing | `docs/09-ui/report-specification.md` | TC-RPT-002 | Draft |
| FR-017 | PDF p.5 mortgage listing | `docs/09-ui/report-specification.md` | TC-RPT-003 | Draft |
| FR-018 | PDF p.3 marriage duration | Future eligibility module | TC-ELIG-001 | Out of Pilot |
| FR-019 | PDF p.3 employment evidence | Future eligibility module | TC-ELIG-002 | Out of Pilot |
| FR-020 | PDF p.3 median area home price | Future eligibility module | TC-ELIG-003 | Out of Pilot |
| FR-021 | PDF p.3 90% mortgage/savings condition | Future eligibility module | TC-ELIG-004 | Out of Pilot |
