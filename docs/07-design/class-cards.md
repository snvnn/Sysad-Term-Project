# 07. Class Cards

## 1. Purpose

이 문서는 MSG Foundation 파일럿 시스템의 설계 검증용 클래스 카드, 또는 객체 카드를 정리한다. 각 카드는 강의의 롤플레잉 검증 방식에 맞춰 한 학생이 하나의 객체 역할을 맡았을 때 필요한 정보를 담는다.

각 카드에는 다음 네 가지 정보를 반드시 포함한다.

| Card Element | Meaning |
|---|---|
| 클래스 이름, 역할 | 롤플레잉에서 참가자가 맡을 객체의 정체성 |
| 알아야 될 정보, Attribute | 객체가 스스로 알고 유지해야 하는 데이터와 상태 |
| 행해야 될 정보, Responsibility / Operation | 객체가 수행해야 하는 행동, 계산, 상태 변경 |
| 의존성, Association | 내 카드에 없는 정보를 얻기 위해 도움을 요청할 수 있는 다른 객체로 가는 연결 경로 |

Source design references:

- [Class diagram](class-diagram.md)
- [Operation contracts](operation-contracts.md)
- [Design sequence diagrams](sequence-diagrams.md)
- [Data model](../08-data/data-model.md)

## 2. Role-Playing Validation Rules

- 참가자는 자신이 맡은 카드의 attribute만 직접 알고 있다고 가정한다.
- 카드에 없는 정보가 필요하면 dependency / association에 적힌 다른 객체에게 요청한다.
- 어떤 책임을 수행할 길이 없으면 association 누락 후보로 기록한다.
- Q-008 계산은 반드시 `Mortgage`, `MortgageCalculator`, `FundsAvailabilityCalculator`의 책임 분리대로 수행한다.
- 자격 심사, 고용 증빙 확인, 지역 중간 주택 가격 비교, 90% commercial mortgage 비교는 파일럿 밖 업무이므로 카드 책임에 넣지 않는다.

## 3. Class Cards

### CC-001. `Money`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `Money`: 금액과 통화를 안전하게 표현하는 value object |
| 알아야 될 정보, Attribute | `amount`, `currency` |
| 행해야 될 정보, Responsibility / Operation | 금액 더하기 `plus`; 금액 빼기 `minus`; 금액 곱하기 `multiply`; 금액 나누기 `divide`; 센트 단위 반올림 `roundToCents`; 음수가 아닌지 확인 `isNonNegative` |
| 의존성, Association | 없음. 독립 value object이며 다른 domain object가 금액 표현을 위해 사용한다. |

Role-playing note:

- 금액 계산이 필요한 객체는 raw number 대신 `Money`에게 계산과 반올림을 맡긴다.
- `Money`는 mortgage, investment, report의 의미를 알지 않는다.

### CC-002. `Investment`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `Investment`: 재단 투자 항목과 예상 연간 수익을 보관하는 domain entity |
| 알아야 될 정보, Attribute | `itemNumber`, `itemName`, `estimatedAnnualReturn`, `returnLastUpdatedDate` |
| 행해야 될 정보, Responsibility / Operation | 연간 예상 수익을 주간 수익으로 변환 `weeklyReturn`; 예상 연간 수익과 갱신일 변경 `updateReturn` |
| 의존성, Association | `Money`: 수익 금액 계산과 반올림에 사용; `FundsAvailabilityCalculator`: 주간 가용 자금 계산 시 이 객체들을 읽는다; `ReportService`: 투자 목록 보고서 생성 시 이 객체들을 읽는다. |

Role-playing note:

- `Investment`는 자신의 annual return만 알고 있으며 전체 투자 합계는 계산하지 않는다.
- 전체 weekly investment income은 `FundsAvailabilityCalculator`가 여러 `Investment`를 모아 계산한다.

### CC-003. `OperatingExpenseEstimate`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `OperatingExpenseEstimate`: 재단의 연간 운영비 추정치를 보관하는 domain entity |
| 알아야 될 정보, Attribute | `estimateId`, `estimatedAnnualOperatingExpenses`, `expensesLastUpdatedDate`, `active` |
| 행해야 될 정보, Responsibility / Operation | 연간 운영비를 주간 운영비로 변환 `weeklyExpense`; 현재 추정치를 활성화 `activate`; 새 추정치로 교체 `replaceEstimate` |
| 의존성, Association | `Money`: 운영비 계산과 반올림에 사용; `FundsAvailabilityCalculator`: 주간 가용 자금 계산 시 active estimate를 읽는다. |

Role-playing note:

- `OperatingExpenseEstimate`는 운영비 입력의 최신성 및 활성 상태를 담당한다.
- 여러 추정치 중 어떤 값을 사용할지는 application workflow 또는 repository가 active record를 제공하고, 계산은 `FundsAvailabilityCalculator`가 수행한다.

### CC-004. `Mortgage`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `Mortgage`: 승인되어 관리 대상이 된 재단 모기지 계정 |
| 알아야 될 정보, Attribute | `accountNumber`, `mortgageesLastName`, `originalPurchasePrice`, `mortgageIssuedDate`, `weeklyPrincipalAndInterestPayment`, `currentCombinedGrossWeeklyIncome`, `annualRealEstateTax`, `annualHomeownerInsurancePremium`, `status` |
| 행해야 될 정보, Responsibility / Operation | 주간 escrow 산출에 필요한 값 제공 `weeklyEscrow`; 총 주간 모기지 비용 산출에 필요한 값 제공 `totalWeeklyMortgageCost`; 소득 28% 한도 산출에 필요한 값 제공 `affordabilityCap`; 주간 보조금 산출에 필요한 값 제공 `weeklyGrant`; 수혜자 예상 주간 상환액 산출에 필요한 값 제공 `expectedBeneficiaryRepayment`; 소득 갱신 `updateIncome`; 활성 상태 확인 `isActive` |
| 의존성, Association | `Money`: 금액 값 표현; `MortgageCalculator`: mortgage별 escrow, 총 비용, affordability cap, grant, expected beneficiary repayment 계산 위임; `FundsAvailabilityCalculator`: active mortgage 목록을 모아 expected repayments와 expected grants 계산; `WeeklyComputationItem`: 계산 입력 snapshot으로 캡처; `FundAllocation`: allocation이 특정 mortgage 또는 주택 구매 reference와 연결될 수 있음; `ReportService`: mortgage listing report 생성 시 읽음. |

Role-playing note:

- `Mortgage`는 자기 계정의 입력 상태를 알고 있다.
- Q-008에서 실제 합산 대상이 되는 값은 active mortgage의 expected beneficiary weekly repayment이다.
- 전체 mortgage repayment 합계는 `Mortgage` 하나가 계산하지 않고 `FundsAvailabilityCalculator`가 active mortgage들을 모아 계산한다.

### CC-005. `WeeklyComputation`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `WeeklyComputation`: 특정 주의 주간 자금 계산 결과와 남은 가용 자금을 보관하는 aggregate root |
| 알아야 될 정보, Attribute | `computationId`, `weekStartDate`, `formulaVersion`, `weeklyInvestmentIncome`, `weeklyOperatingExpense`, `expectedMortgageRepayments`, `expectedGrants`, `startingAvailableAmount`, `remainingAvailableAmount`, `status`, `createdAt`, `warnings` |
| 행해야 될 정보, Responsibility / Operation | 입력 snapshot 추가 `addInputSnapshot`; 특정 주택 비용을 funding할 수 있는지 확인 `canFund`; allocation 적용 후 remaining amount 차감 `applyAllocation`; 경고 추가 `attachWarning`; 계산 결과 확정 `finalize` |
| 의존성, Association | `WeeklyComputationItem`: `1` to `0..*`, 계산 입력 snapshot 보관; `FundAllocation`: `1` to `0..*`, 같은 주의 자금 배정 기록 보관; `Money`: 계산 결과 금액 표현; `FundsAvailabilityCalculator`: 최초 계산 결과 생성에 사용; `ReportService`: weekly funds report 생성 시 읽음. |

Role-playing note:

- `WeeklyComputation`은 이미 계산된 결과와 상태를 유지한다.
- 투자, 운영비, mortgage 원본 데이터를 직접 찾아다니지 않고 `WeeklyComputationItem` snapshot으로 당시 입력을 보존한다.

### CC-006. `WeeklyComputationItem`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `WeeklyComputationItem`: 주간 계산에 사용된 입력값의 snapshot item |
| 알아야 될 정보, Attribute | `id`, `computationId`, `sourceType`, `sourceId`, `snapshot` |
| 행해야 될 정보, Responsibility / Operation | source record의 계산 시점 값을 보존 `capture` |
| 의존성, Association | `WeeklyComputation`: 반드시 하나의 computation에 속함; `Investment`: sourceType이 investment일 때 snapshot 대상; `OperatingExpenseEstimate`: sourceType이 operating expense일 때 snapshot 대상; `Mortgage`: sourceType이 mortgage일 때 snapshot 대상. |

Role-playing note:

- 이 카드는 audit과 report 재현성을 위한 증거 카드이다.
- 원본 source가 나중에 바뀌어도 snapshot은 당시 계산 값을 유지한다.

### CC-007. `FundAllocation`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `FundAllocation`: 특정 주의 가용 자금에서 주택 구입 비용을 배정하거나 fundability 판단을 기록하는 entity |
| 알아야 될 정보, Attribute | `allocationId`, `computationId`, `homeCost`, `reference`, `fundable`, `remainingAfterAllocation`, `createdAt` |
| 행해야 될 정보, Responsibility / Operation | allocation 기록 `record`; fundable 여부 표시 `markFundable` |
| 의존성, Association | `WeeklyComputation`: allocation은 특정 computation의 remaining available amount를 기준으로 함; `Mortgage`: allocation reference가 관리 대상 mortgage 또는 주택 구매 reference를 가리킬 수 있음; `Money`: `homeCost`와 `remainingAfterAllocation` 표현. |

Role-playing note:

- `FundAllocation`은 신청자 자격 심사를 수행하지 않는다.
- 파일럿에서는 주택 비용이 남은 가용 자금 이하인지와 그 결과 기록에 집중한다.

### CC-008. `MortgageCalculator`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `MortgageCalculator`: mortgage별 금융 계산 규칙을 집중시키는 domain service |
| 알아야 될 정보, Attribute | 별도 저장 attribute 없음. 입력으로 받은 `Mortgage`와 `Money` 값을 사용한다. |
| 행해야 될 정보, Responsibility / Operation | 주간 escrow 계산 `calculateWeeklyEscrow`; 총 주간 모기지 비용 계산 `calculateTotalWeeklyCost`; 소득 28% affordability cap 계산 `calculateAffordabilityCap`; 주간 보조금 계산 `calculateWeeklyGrant`; Q-008 수혜자 예상 주간 상환액 계산 `calculateExpectedBeneficiaryRepayment` |
| 의존성, Association | `Mortgage`: mortgage별 입력 데이터 요청; `Money`: 계산 결과 표현; `FundsAvailabilityCalculator`: mortgage별 계산을 이 service에 위임. |

Role-playing note:

- Q-008의 per-mortgage 계산 책임은 이 카드에 있다.
- 계산식은 `expected beneficiary weekly repayment = total weekly mortgage cost - weekly grant = min(total weekly mortgage cost, affordability cap)`이다.
- 여러 mortgage의 합계 계산은 `FundsAvailabilityCalculator`가 담당한다.

### CC-009. `FundsAvailabilityCalculator`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `FundsAvailabilityCalculator`: 주간 가용 자금 산식과 fundability 판단을 수행하는 domain service |
| 알아야 될 정보, Attribute | 별도 저장 attribute 없음. 계산 시점의 investments, active operating expense estimate, active mortgages, home cost를 입력으로 받는다. |
| 행해야 될 정보, Responsibility / Operation | 주간 투자 수익 합계 계산 `calculateWeeklyInvestmentIncome`; 주간 운영비 계산 `calculateWeeklyOperatingExpense`; active mortgage들의 expected mortgage repayments 합계 계산 `calculateExpectedMortgageRepayments`; expected grants 합계 계산 `calculateExpectedGrants`; starting available amount 계산 `calculateStartingAvailableAmount`; 주택 비용 fundability 확인 `checkHomeFundability` |
| 의존성, Association | `Investment`: weekly investment income 계산 입력; `OperatingExpenseEstimate`: weekly operating expense 계산 입력; `Mortgage`: active mortgage 필터링 및 mortgage 입력 접근; `MortgageCalculator`: mortgage별 escrow, grant, repayment 계산 위임; `Money`: 산식 결과 표현; `WeeklyComputation`: 계산 결과 생성 및 저장 대상으로 연결. |

Role-playing note:

- Q-001 산식 책임은 이 카드에 있다.
- 산식은 `weekly investment income - weekly operating expenses + expected mortgage repayments - expected grants`이다.
- Q-008 aggregation 책임은 이 카드에 있으며, active mortgage의 expected beneficiary weekly repayment만 합산한다.

### CC-010. `ReportService`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `ReportService`: 요구된 세 가지 보고서를 생성하는 application/report service |
| 알아야 될 정보, Attribute | 별도 저장 attribute 없음. 보고서 생성 요청과 조회된 domain data를 입력으로 받는다. |
| 행해야 될 정보, Responsibility / Operation | 주간 자금 계산 결과 보고서 생성 `generateWeeklyFundsReport`; 투자 목록 보고서 생성 `generateInvestmentListing`; 모기지 목록 보고서 생성 `generateMortgageListing` |
| 의존성, Association | `WeeklyComputation`: weekly funds report의 핵심 데이터; `WeeklyComputationItem`: 계산 입력 snapshot 표시; `FundAllocation`: allocation 결과 표시; `Investment`: investment listing report 입력; `Mortgage`: mortgage listing report 입력; `Money`: 금액 표시 format; report renderer 또는 output adapter: 실제 출력 형식 생성. |

Role-playing note:

- `ReportService`는 계산 규칙을 새로 수행하지 않고 이미 확정된 domain data와 computation result를 읽어 출력한다.
- 보고서 값이 이상하면 계산 객체가 아니라 원본 `WeeklyComputation`과 snapshot을 추적한다.

## 4. Dependency Check Matrix

| Responsibility | Owning Card | Required Path |
|---|---|---|
| 주간 투자 수익 합계 계산 | `FundsAvailabilityCalculator` | `FundsAvailabilityCalculator` -> `Investment` -> `Money` |
| 주간 운영비 계산 | `FundsAvailabilityCalculator` | `FundsAvailabilityCalculator` -> `OperatingExpenseEstimate` -> `Money` |
| mortgage별 grant 계산 | `MortgageCalculator` | `MortgageCalculator` -> `Mortgage` -> `Money` |
| Q-008 expected repayment 계산 | `MortgageCalculator`, `FundsAvailabilityCalculator` | `FundsAvailabilityCalculator` -> active `Mortgage` -> `MortgageCalculator` -> `Money` |
| 주간 가용 자금 계산 | `FundsAvailabilityCalculator` | `Investment`, `OperatingExpenseEstimate`, active `Mortgage`, `MortgageCalculator`, `Money` |
| 계산 입력 snapshot 보존 | `WeeklyComputationItem` | `WeeklyComputation` -> `WeeklyComputationItem` -> source record |
| 주택 비용 fundability 판단 | `WeeklyComputation`, `FundsAvailabilityCalculator` | `WeeklyComputation` -> `Money`; 또는 `FundsAvailabilityCalculator` -> `Money` |
| 자금 배정 기록 | `FundAllocation` | `FundAllocation` -> `WeeklyComputation` -> `Money` |
| 주간 자금 보고서 생성 | `ReportService` | `ReportService` -> `WeeklyComputation` -> `WeeklyComputationItem`, `FundAllocation` |
| 투자 목록 보고서 생성 | `ReportService` | `ReportService` -> `Investment` |
| 모기지 목록 보고서 생성 | `ReportService` | `ReportService` -> `Mortgage` |

## 5. Verification Checklist

- [x] 모든 카드가 클래스 이름, 알아야 될 정보, 행해야 될 정보, 의존성을 포함한다.
- [x] Class Diagram의 10개 클래스가 모두 카드로 작성되어 있다.
- [x] 카드의 attributes와 operations는 [Class diagram](class-diagram.md)의 class table과 일치한다.
- [x] Q-008 계산 책임이 `MortgageCalculator`와 `FundsAvailabilityCalculator`에 명시되어 있다.
- [x] 자격 심사와 90% commercial mortgage 비교는 out-of-pilot으로 유지되어 있다.
