# MSG Foundation Term Project - Analysis and Design Report Draft

## 1. Cover Information

| Item | Content |
|---|---|
| Project | The MSG Foundation Pilot System |
| Course / Assignment | Systems Analysis and Design Term Project |
| Report Status | Draft for submission editing |
| Repository Scope | Analysis and design documentation only |
| Implementation Scope | Not implemented in this repository |
| Rich Picture | Hand-drawn by the student and submitted separately |

## 2. Executive Summary

This term project analyzed and designed a pilot information system for the MSG Foundation. The foundation supports young married couples by providing 100% mortgages and grant support. The main business problem is that the foundation must regularly determine how much money is available for new home purchases while considering investment income, operating expenses, expected mortgage repayments, and expected grant payments.

The project scope was limited to analysis and design. No application code, database migration, production deployment, external integration, or real financial transaction was implemented. The repository instead produces a structured set of requirements, domain, OOAD, architecture, interface, data, report, security, validation, and implementation-planning deliverables.

The final design keeps the pilot intentionally small. It manages only three main data groups: investment data, operating expense data, and mortgage data. It also supports three required reports: weekly funds computation results, listing of all investments, and listing of all mortgages.

## 3. Project Background and Problem Definition

The MSG Foundation is based on Martha Stockton Greengage's estate and is intended to help young married couples purchase homes. The foundation needs a system that can reduce operating effort and make weekly fund decisions more consistent.

The problem definition identified three core business questions:

1. How much money is available at the beginning of each week for new home purchases?
2. How do existing mortgage repayments and grant obligations affect that amount?
3. Can the foundation generate reliable weekly and on-demand reports from investment, operating expense, and mortgage data?

The main risks of the current manual process are calculation errors, inconsistent fundability decisions, weak traceability of data changes, and high report preparation effort. These risks are documented in [Problem definition](../01-initiation/problem-definition.md).

## 4. Scope and Boundary Decisions

The project was scoped as a pilot system for analysis and design. The scope statement defines the work as requirements extraction, stakeholder analysis, domain modeling, data modeling, architecture design, interface design, report design, security/operations planning, validation planning, and implementation roadmap preparation.

The pilot includes:

- Investment data management
- Operating expense estimate management
- Mortgage account data management
- Weekly funds computation
- Fundability check and allocation recording
- Weekly funds, investment listing, and mortgage listing reports

The pilot excludes:

- Full applicant eligibility screening
- 90% commercial mortgage comparison
- Employment evidence verification
- Local median home price comparison
- Real bank, insurance, tax, or payment integrations
- Actual mortgage contract execution
- Tax return or pay stub OCR processing

The exclusions are deliberate. They keep the project aligned with the assignment focus and prevent the pilot from expanding beyond the data required for weekly fund calculations. The boundary is recorded in [Scope statement](../01-initiation/scope-statement.md), [Use cases](../03-requirements/use-cases.md), and [Test strategy](../11-validation/test-strategy.md).

## 5. Analysis Process

### 5.1 Requirements Extraction

The first step was to preserve and summarize the source requirements. The repository keeps both a raw extraction and a cleaned requirements extract:

- [Extracted raw text](../00-source/requirements-extracted-raw.txt)
- [Requirements extract](../00-source/requirements-extracted.md)

The extracted requirements were then converted into functional requirements, non-functional requirements, data requirements, business rules, and former open issues in the [Software requirements specification](../03-requirements/software-requirements-specification.md).

### 5.2 Stakeholder and User Analysis

The stakeholder analysis identified the major people and groups affected by the pilot:

- Trustees / Board
- Foundation Manager
- Investment Advisor
- Mortgage Program Officer
- Married Couple / Mortgagees
- Software Operator / Administrator
- Course Evaluator / Instructor

The Foundation Manager is the primary operational user because they run weekly computations and generate reports. The Mortgage Program Officer maintains mortgage data, while the Investment Advisor provides or reviews investment return estimates. Trustees mainly consume reports. This role separation is documented in [Stakeholder and user analysis](../02-stakeholders/stakeholder-analysis.md).

### 5.3 Requirements Structuring

The SRS defines 21 functional requirements. Requirements `FR-001` through `FR-017` form the pilot's analysis/design scope. Requirements `FR-018` through `FR-021` are retained as future or out-of-pilot candidates so that they are not lost, but they are not designed as pilot workflows.

The main Must requirements cover:

- Investment, operating expense, and mortgage data management
- Weekly investment income and weekly operating expense calculation
- Weekly escrow, total mortgage cost, income cap, grant, and repayment calculation
- Weekly available amount calculation
- Weekly funds report
- Investment listing report
- Mortgage listing report

Traceability is maintained in the [Requirements traceability matrix](../03-requirements/requirements-traceability-matrix.md).

## 6. Key Domain Decisions

### 6.1 Weekly Available Amount Formula

The weekly available amount formula was resolved as:

```text
weekly investment income
- weekly operating expenses
+ expected mortgage repayments
- expected grants
```

This treats investment income and beneficiary repayments as inflows, while operating expenses and grants are outflows.

### 6.2 Expected Mortgage Repayments

The most important calculation decision was Q-008. The final decision is:

```text
expected mortgage repayments
= sum(active mortgage expected beneficiary weekly repayment)

expected beneficiary weekly repayment
= total weekly mortgage cost - weekly grant
= min(total weekly mortgage cost, affordability cap)
```

This avoids double-counting grants. If the design used the full mortgage cost as repayment and then also subtracted expected grants, the grant amount would be reflected twice in the available amount formula. The resolved rule is documented in [Core questions](../01-initiation/core-questions.md), [Domain model](../04-domain/domain-model.md), and [SRS](../03-requirements/software-requirements-specification.md).

### 6.3 Money, Rounding, and Date Assumptions

The project uses decimal or fixed-point money semantics and rounds stored/displayed values to cents. Annual amounts are converted to weekly amounts by dividing by 52. The week start is the first business day, and the week end is the last business day, excluding public holidays and foundation closure days. These decisions are recorded in [Core questions](../01-initiation/core-questions.md).

## 7. OOAD Deliverables

### 7.1 Brief Use Cases

The brief use cases describe the main user goals without adding internal implementation objects. The use cases are:

| ID | Use Case |
|---|---|
| UC-001 | Manage Investments |
| UC-002 | Manage Operating Expense Estimate |
| UC-003 | Manage Mortgages |
| UC-004 | Run Weekly Funds Computation |
| UC-005 | Check Home Fundability |
| UC-006 | Record Fund Allocation |
| UC-007 | Generate Weekly Funds Report |
| UC-008 | Generate Investment Listing |
| UC-009 | Generate Mortgage Listing |

The full use case set is in [Brief use cases](../03-requirements/use-cases.md).

### 7.2 System Sequence Diagrams

System Sequence Diagrams define the interaction between actors and the system boundary only. Internal classes, repositories, calculators, and services are intentionally excluded from SSDs.

The SSD set includes:

| ID | Diagram |
|---|---|
| SSD-001 | Run Weekly Funds Computation |
| SSD-002 | Manage Mortgage Data |
| SSD-003 | Check Home Fundability |
| SSD-004 | Generate Reports |

The source files are [System sequence diagrams](../03-requirements/system-sequence-diagrams.md) and [system-sequence-diagrams.drawio](../03-requirements/system-sequence-diagrams.drawio).

### 7.3 Operation Contracts

Operation contracts connect the system events and use cases to domain state changes. Each contract lists the operation, cross references, preconditions, postconditions, and notes.

The contracts include data management operations, weekly computation, fundability, allocation, and reporting operations. The most calculation-heavy contract is `OC-006 runWeeklyComputation(...)`, which creates a `WeeklyComputation` with input snapshots and applies the resolved weekly funds formula. The contracts are documented in [Operation contracts](../07-design/operation-contracts.md).

### 7.4 Class Diagram

The class diagram captures the core domain and service responsibilities. The main classes are:

- `Money`
- `Investment`
- `OperatingExpenseEstimate`
- `Mortgage`
- `WeeklyComputation`
- `WeeklyComputationItem`
- `FundAllocation`
- `MortgageCalculator`
- `FundsAvailabilityCalculator`
- `ReportService`

The design assigns mortgage-specific formulas to `MortgageCalculator` and weekly fund aggregation to `FundsAvailabilityCalculator`. This keeps calculation logic out of the UI and persistence layers. The class diagram is documented in [Class diagram](../07-design/class-diagram.md), with the source diagram in [class-diagram.drawio](../07-design/class-diagram.drawio).

### 7.5 Design Sequence Diagrams

Design Sequence Diagrams expand the SSDs into internal object collaboration. Unlike SSDs, the DSDs show UI/controller, application services, repositories, domain entities, calculators, and report rendering.

The DSD set includes 11 pages, covering investment management, operating expense updates, mortgage management, weekly computation, fundability, allocation, and all three report types. The DSD documentation is in [Design sequence diagrams](../07-design/sequence-diagrams.md), with source diagrams in [sequence-diagrams.drawio](../07-design/sequence-diagrams.drawio).

## 8. Architecture and Design Strategy

The recommended architecture is a modular monolith with layered architecture. This choice fits the small pilot scope and avoids unnecessary distributed-system complexity.

The main layers are:

- Presentation Layer: user input, screens, menus, report requests
- Application Layer: use case orchestration, transaction boundary, authorization checks
- Domain Layer: mortgage, grant, repayment, and weekly fund calculation rules
- Repository Layer: persistence ports and adapters
- Report Renderer: weekly, investment, and mortgage report output
- Audit Logger: data changes and computation events

The dependency direction is:

```text
Presentation -> Application -> Domain
Application -> Repository Port <- Repository Adapter
Application -> Report Port <- Report Adapter
```

The architecture is documented in [Architecture design](../06-architecture/architecture-design.md).

## 9. Data, Interface, and Report Design

The data model defines logical entities for investments, operating expense estimates, mortgages, weekly computations, weekly computation input snapshots, and fund allocations. Weekly computation snapshots preserve the values used for each report, which supports auditability and reproducibility. The data model is in [Data model and schema design](../08-data/data-model.md).

The interface contract defines logical endpoints or service contracts that could be implemented as CLI, Web, or Desktop workflows. It uses explicit money strings and ISO dates to avoid ambiguity. The contract is in [API and interface contract](../07-interfaces/api-contract.md).

The report specification defines a report-first user experience. The required reports are:

1. Weekly funds computation results
2. Listing of all investments
3. Listing of all mortgages

The report design emphasizes readable labels, input freshness, required calculation results, and print-on-request support. Details are in [UX/UI and report specification](../09-ui/report-specification.md).

## 10. Validation Strategy

The validation plan covers unit, integration, contract, report, security, and manual QA levels. The most important tests are the deterministic calculation tests for investment income, operating expense, escrow, total mortgage cost, affordability cap, weekly grant, expected mortgage repayments, expected grants, and available amount.

The Q-008 test case checks that expected mortgage repayments include only active mortgages and use beneficiary-paid weekly repayment amounts:

```text
active mortgage A: cost 720, grant 160 -> repayment 560
active mortgage B: cost 500, grant 0 -> repayment 500
suspended mortgage: excluded
expected mortgage repayments = 560 + 500 = 1060
```

The validation strategy is documented in [Test strategy](../11-validation/test-strategy.md).

## 11. Traceability and Consistency Controls

Traceability is maintained through requirement IDs, use case IDs, operation contract IDs, SSD/DSD IDs, and test IDs. The RTM maps each functional requirement to the relevant source evidence, design artifacts, and test artifacts.

Examples:

| Requirement | Traceability Path |
|---|---|
| FR-010 expected mortgage repayments | UC-004, SSD-001, OC-006, DSD-006, class diagram, BR-007, TC-CALC-007 |
| FR-012 weekly available amount | UC-004, SSD-001, OC-006, DSD-006, BR-008, TC-CALC-009 |
| FR-015 weekly funds report | UC-007, SSD-004, OC-009, DSD-009, report specification, TC-RPT-001 |
| FR-016 investment listing | UC-008, SSD-004, OC-010, DSD-010, report specification, TC-RPT-002 |
| FR-017 mortgage listing | UC-009, SSD-004, OC-011, DSD-011, report specification, TC-RPT-003 |

The full matrix is in [Requirements traceability matrix](../03-requirements/requirements-traceability-matrix.md).

## 12. Problems Encountered and Resolutions

### 12.1 Ambiguous Weekly Available Amount Formula

The source listed investment income, operating expenses, mortgage repayments, and grants but did not fully specify the sign convention. The project resolved this by treating income and repayments as inflows, and expenses and grants as outflows.

Resolution:

```text
available = income - expenses + repayments - grants
```

### 12.2 Ambiguous Expected Mortgage Repayment Scope

The source did not explicitly state whether expected mortgage repayments meant full mortgage cost or the beneficiary-paid portion. The project initially preserved this as an open issue, then resolved it after review.

Resolution:

```text
expected mortgage repayments = sum of active mortgage beneficiary-paid weekly repayments
```

This makes the formula consistent and avoids grant double counting.

### 12.3 Pilot Scope Versus Eligibility Rules

The case description includes eligibility-like conditions, such as marriage duration, employment evidence, local median home price comparison, and a 90% mortgage/savings condition. Including these in the pilot would significantly expand the data model and workflows beyond the assignment's required data list.

Resolution:

These items are documented as out-of-pilot or future scope. The pilot assumes that eligibility screening is handled offline before a mortgage is managed by the system.

### 12.4 OOAD Boundary Separation

System Sequence Diagrams and Design Sequence Diagrams can easily be mixed. The project separated them deliberately:

- SSDs show only actor-to-system messages.
- DSDs show internal object collaboration.
- Operation contracts bridge the two levels.

This separation keeps the analysis and design artifacts readable and consistent with OOAD expectations.

## 13. Current Deliverable Package

The current repository contains the following major deliverable groups:

| Group | Main Artifacts |
|---|---|
| Source and initiation | Requirements extraction, problem definition, scope statement, core questions |
| Stakeholder and requirements | Stakeholder analysis, SRS, RTM, use cases, SSDs |
| Domain and context | Domain model, current state and constraints |
| Architecture and interfaces | Architecture design, API/interface contract |
| OOAD design | Operation contracts, class diagram, DSDs |
| Data and reports | Data model, report specification |
| Security and validation | Security/operations plan, test strategy |
| Implementation planning | Roadmap and WBS |

The [Project documentation index](../PROJECT_INDEX.md) and repository [README](../../README.md) provide the navigational entry points.

## 14. Remaining Submission Notes

- The Rich Picture is not generated in this repository. It is a user-created hand-drawn artifact and should be submitted separately.
- The draw.io files are maintained as editable source diagrams in the repository.
- This report is a draft. Before submission, fill in course, student, instructor, and date information as required by the course format.
- The repository intentionally contains no implementation code. It is an analysis and design package.

## 15. Conclusion

The project produced a coherent analysis and design package for the MSG Foundation pilot system. The main design result is a small, report-first system that manages investment, operating expense, and mortgage data, then computes weekly fund availability using a traceable domain formula. The OOAD artifacts connect user goals to system events, operation contracts, domain classes, internal object collaboration, and validation scenarios.

The final package is implementation-ready in the sense that a future builder can select a platform and implement the documented workflows, calculations, storage model, and reports without needing to reinterpret the core domain rules.
