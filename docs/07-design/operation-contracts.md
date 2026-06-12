# 07. 오퍼레이션 컨트랙트

## 1. 목적

이 문서는 MSG Foundation 파일럿 시스템의 설계 단계 산출물로, 주요 시스템 오퍼레이션의 사전 조건과 사후 조건을 정의한다. 오퍼레이션 컨트랙트는 SSD의 시스템 이벤트를 이후 클래스 다이어그램 / 설계 시퀀스 다이어그램의 도메인 상태 변경과 내부 객체 협력으로 연결한다.

## 2. 컨트랙트 작성 규칙

| 항목 | 규칙 |
|---|---|
| 범위 | 분석/설계 산출물만 작성하며 구현 코드는 작성하지 않는다. |
| 금액 | 모든 금액은 십진/고정소수점 방식으로 계산하고 저장/표시는 센트 단위로 반올림한다. |
| 날짜 | 주 시작일은 공휴일/재단 휴업일을 제외한 첫 영업일이다. |
| 행위자 권한 | 사전 조건에는 최소 권한을 명시한다. 세부 인증 구현은 개발자 재량이다. |
| Q-008 | `expected mortgage repayments`는 활성 모기지별 수혜자 실제 예상 주간 상환액의 합으로 확정한다. |

## 3. 컨트랙트 요약

| 컨트랙트 | 오퍼레이션 | 관련 UC | 관련 SSD | 주요 도메인 변경 |
|---|---|---|---|---|
| OC-001 | `createInvestment(...)` | UC-001 | 해당 없음 | `Investment` 생성 |
| OC-002 | `updateInvestmentReturn(...)` | UC-001 | 해당 없음 | `Investment` 예상 수익/갱신일 변경 |
| OC-003 | `updateOperatingExpenseEstimate(...)` | UC-002 | 해당 없음 | 활성 `OperatingExpenseEstimate` 생성/활성화 |
| OC-004 | `createMortgage(...)` | UC-003 | SSD-002 | `Mortgage` 생성 |
| OC-005 | `updateMortgageIncome(...)` | UC-003 | SSD-002 | `Mortgage` 소득/갱신일 변경 |
| OC-006 | `runWeeklyComputation(...)` | UC-004 | SSD-001 | `WeeklyComputation` 및 입력 스냅샷 생성 |
| OC-007 | `checkHomeFundability(...)` | UC-005 | SSD-003 | 자금 지원 가능성 결과 산출, 상태 변경 없음 |
| OC-008 | `recordFundAllocation(...)` | UC-006 | SSD-003 | `FundAllocation` 생성 및 잔여 금액 차감 |
| OC-009 | `generateWeeklyFundsReport(...)` | UC-007 | SSD-004 | 보고서 뷰 생성, 선택적으로 감사 이벤트 기록 |
| OC-010 | `generateInvestmentListingReport(...)` | UC-008 | SSD-004 | 투자 목록 보고서 뷰 생성 |
| OC-011 | `generateMortgageListingReport(...)` | UC-009 | SSD-004 | 모기지 목록 보고서 뷰 생성 |

## 4. 컨트랙트 상세

### OC-001. createInvestment

| 항목 | 내용 |
|---|---|
| 오퍼레이션 | `createInvestment(itemNumber, itemName, estimatedAnnualReturn, returnLastUpdatedDate)` |
| 교차 참조 | UC-001, FR-001, DR-001 |
| 사전 조건 | 행위자는 투자 데이터를 관리할 권한이 있다. `itemNumber`는 아직 사용되지 않았다. `itemName`은 비어 있지 않다. `estimatedAnnualReturn >= 0`이다. `returnLastUpdatedDate`가 제공된다. |
| 사후 조건 | 새로운 `Investment`가 생성된다. `Investment.itemNumber`가 설정된다. `Investment.itemName`이 설정된다. `Investment.estimatedAnnualReturn`이 설정된다. `Investment.returnLastUpdatedDate`가 설정된다. 해당 투자 항목은 이후 주간 투자 수입 계산에 사용할 수 있다. |
| 비고 | 투자 자문 담당자가 값을 제공할 수 있지만, 파일럿에서 기본 입력 행위자는 재단 관리자이다. |

### OC-002. updateInvestmentReturn

| 항목 | 내용 |
|---|---|
| 오퍼레이션 | `updateInvestmentReturn(itemNumber, estimatedAnnualReturn, returnLastUpdatedDate)` |
| 교차 참조 | UC-001, FR-001, FR-004, DR-001 |
| 사전 조건 | 행위자는 투자 데이터를 관리할 권한이 있다. `itemNumber`에 해당하는 `Investment`가 존재한다. `estimatedAnnualReturn >= 0`이다. `returnLastUpdatedDate`가 제공되며 최신 갱신일을 의미한다. |
| 사후 조건 | 대상 `Investment.estimatedAnnualReturn`이 교체된다. `Investment.returnLastUpdatedDate`가 교체된다. 이후 주간 계산은 갱신된 값을 사용한다. 이미 확정된 기존 `WeeklyComputation` 스냅샷은 자동 재계산하지 않는다. |
| 비고 | 기존 스냅샷을 보존하면 보고서 추적성을 유지할 수 있다. |

### OC-003. updateOperatingExpenseEstimate

| 항목 | 내용 |
|---|---|
| 오퍼레이션 | `updateOperatingExpenseEstimate(estimatedAnnualOperatingExpenses, expensesLastUpdatedDate)` |
| 교차 참조 | UC-002, FR-002, FR-005, DR-002 |
| 사전 조건 | 행위자는 운영 비용 데이터를 관리할 권한이 있다. `estimatedAnnualOperatingExpenses >= 0`이다. `expensesLastUpdatedDate`가 제공된다. |
| 사후 조건 | 새 `OperatingExpenseEstimate`가 생성되거나 기존 추정치가 갱신된다. 해당 추정치는 활성/최신 추정치로 표시된다. 기간별 레코드를 사용하는 경우 이전 활성 추정치는 더 이상 현재 활성 추정치가 아니다. 이후 주간 계산은 이 활성 추정치를 사용한다. 이미 확정된 계산 스냅샷은 변경되지 않는다. |
| 비고 | 원문 요구사항은 최신 값과 마지막 갱신일만 요구한다. 파일럿에서는 전체 변경 이력 추적이 필수는 아니다. |

### OC-004. createMortgage

| 항목 | 내용 |
|---|---|
| 오퍼레이션 | `createMortgage(accountNumber, mortgageesLastName, originalPurchasePrice, issueDate, weeklyPI, currentWeeklyIncome, incomeUpdatedDate, annualTax, taxUpdatedDate, annualInsurancePremium, insuranceUpdatedDate)` |
| 교차 참조 | UC-003, SSD-002, FR-003, FR-006, FR-007, FR-008, FR-009, DR-003 |
| 사전 조건 | 행위자는 모기지 데이터를 관리할 권한이 있다. `accountNumber`는 아직 사용되지 않았다. 필요한 날짜가 모두 제공된다. 모든 금액 입력은 `>= 0`이다. 자격 심사는 이미 시스템 밖에서 처리되었다. |
| 사후 조건 | 새 `Mortgage`가 생성된다. 모기지 식별자, 구매가, 발행일, 주간 원리금(P&I), 현재 주간 소득, 연간 세금, 연간 보험료, 각 갱신일이 설정된다. 명시적으로 `Draft`로 생성하지 않는 한 `Mortgage.status`는 `Active`로 설정된다. 활성 상태인 모기지는 주간 계산에 포함될 수 있다. |
| 비고 | 이 오퍼레이션은 해당 부부가 MSG Foundation 지원 자격을 갖는지 판단하지 않는다. |

### OC-005. updateMortgageIncome

| 항목 | 내용 |
|---|---|
| 오퍼레이션 | `updateMortgageIncome(accountNumber, currentWeeklyIncome, incomeUpdatedDate)` |
| 교차 참조 | UC-003, SSD-002, FR-003, FR-008, FR-009, DR-003 |
| 사전 조건 | 행위자는 모기지 데이터를 관리할 권한이 있다. `accountNumber`에 해당하는 `Mortgage`가 존재한다. `currentWeeklyIncome >= 0`이다. `incomeUpdatedDate`가 제공된다. 모기지는 `Closed` 또는 `Cancelled` 상태가 아니다. |
| 사후 조건 | `Mortgage.currentCombinedGrossWeeklyIncome`이 교체된다. `Mortgage.incomeLastUpdatedDate`가 교체된다. 이후 보조금과 상환 가능 상한 계산은 갱신된 소득을 사용한다. 이미 확정된 계산 스냅샷은 변경되지 않는다. |
| 비고 | 세금, 보험, P&I, 상태 변경에도 유사한 갱신 오퍼레이션을 사용할 수 있다. 이 컨트랙트는 28% 상한과 보조금 계산에 직접 영향을 주는 소득 갱신에 초점을 둔다. |

### OC-006. runWeeklyComputation

| 항목 | 내용 |
|---|---|
| 오퍼레이션 | `runWeeklyComputation(weekStartDate, formulaVersion)` |
| 교차 참조 | UC-004, SSD-001, FR-004~FR-012, DR-004, BR-001~BR-011 |
| 사전 조건 | 행위자는 주간 계산을 실행할 권한이 있다. `weekStartDate`는 해당 주의 첫 영업일이다. 활성 투자 데이터가 존재하거나, 누락된 투자 항목을 0으로 처리하면서 경고를 표시할 수 있다. 활성 운영비 추정치가 존재하거나, 시스템이 실행 차단 경고를 표시한다. 활성 모기지가 없으면 모기지 합계는 0으로 처리할 수 있다. 산식 버전은 명시된다. |
| 사후 조건 | `Investment`로부터 주간 투자 수입이 계산된다. 활성 운영비 추정치로부터 주간 운영비가 계산된다. 활성 모기지별로 주간 에스크로 금액, 총 주간 모기지 비용, 상환 가능 상한, 주간 보조금이 계산된다. 예상 보조금 합계가 계산된다. 예상 모기지 상환액 합계는 활성 모기지의 예상 수혜자 주간 상환액 합계로 계산되며, 각 상환액은 `total weekly mortgage cost - weekly grant`이다. 시작 가용 금액은 `weekly investment income - weekly operating expenses + expected mortgage repayments - expected grants`로 계산된다. 입력 스냅샷과 결과 필드를 가진 `WeeklyComputation`이 생성된다. 오래되었거나 누락된 데이터에 대한 경고가 연결된다. |
| 비고 | Q-008은 사용자 결정으로 확정되었다. `expected mortgage repayments`는 수혜자가 재단에 실제로 납부할 것으로 예상되는 금액이다. |

### OC-007. checkHomeFundability

| 항목 | 내용 |
|---|---|
| 오퍼레이션 | `checkHomeFundability(computationId, homeCost, reference)` |
| 교차 참조 | UC-005, SSD-003, FR-013, DR-005 |
| 사전 조건 | 행위자는 자금 지원 가능성을 확인할 권한이 있다. `computationId`에 해당하는 `WeeklyComputation`이 존재한다. 해당 계산 결과는 현재 주에 사용할 수 있는 상태이다. `homeCost >= 0`이다. |
| 사후 조건 | 시스템은 `homeCost`와 `WeeklyComputation.remainingAvailableAmount`를 비교한다. 자금 지원 가능성 결과가 반환된다. `homeCost <= remainingAvailableAmount`이면 결과는 `fundable = true`이다. `homeCost > remainingAvailableAmount`이면 결과는 `fundable = false`이며 부족액이 계산된다. 이 오퍼레이션은 `FundAllocation`을 생성하지 않는다. `remainingAvailableAmount`는 변경되지 않는다. |
| 비고 | SSD는 단순 자금 지원 가능성 확인과 자금 배정 기록을 분리한다. 이는 의도하지 않은 자금 차감을 방지하기 위한 것이다. |

### OC-008. recordFundAllocation

| 항목 | 내용 |
|---|---|
| 오퍼레이션 | `recordFundAllocation(computationId, homeCost, reference)` |
| 교차 참조 | UC-006, SSD-003, FR-014, DR-005 |
| 사전 조건 | 행위자는 자금 배정을 기록할 권한이 있다. `computationId`에 해당하는 `WeeklyComputation`이 존재하며 보관/취소 상태가 아니다. `homeCost >= 0`이다. `homeCost <= WeeklyComputation.remainingAvailableAmount`이다. 자금 배정은 해당 오프라인/비즈니스 절차에 따라 승인되었다. |
| 사후 조건 | 새 `FundAllocation`이 생성된다. `FundAllocation.computationId`는 주간 계산을 참조한다. `FundAllocation.homeCost`, `reference`, `fundable = true`, `remainingAfterAllocation`이 설정된다. `WeeklyComputation.remainingAvailableAmount`는 `homeCost`만큼 감소한다. 해당 자금 배정은 주간 자금 보고서에 표시된다. |
| 비고 | 이 오퍼레이션은 주간 가용 금액 내에서 이루어진 자금 지원 결정을 기록한다. 신청자 자격 심사는 수행하지 않는다. |

### OC-009. generateWeeklyFundsReport

| 항목 | 내용 |
|---|---|
| 오퍼레이션 | `generateWeeklyFundsReport(computationId)` |
| 교차 참조 | UC-007, SSD-004, FR-015, DR-004, NFR-002, NFR-003 |
| 사전 조건 | 행위자는 주간 자금 보고서를 조회할 권한이 있다. `computationId`에 해당하는 `WeeklyComputation`이 존재한다. |
| 사후 조건 | 주간 자금 보고서 뷰가 생성된다. 보고서는 주 시작일, 산식 버전, 생성 시각, 입력 요약, 예상 모기지 상환액, 예상 보조금, 시작 가용 금액, 배정 내역, 잔여 가용 금액, 데이터 최신성 경고를 포함한다. 원본 데이터는 수정되지 않는다. 감사 로그가 활성화되어 있으면 보고서 생성 감사 이벤트가 기록된다. |
| 비고 | 출력 매체는 화면, 인쇄 가능한 text/HTML 또는 개발자가 선택한 다른 형식일 수 있다. 단, 요청 시 인쇄를 지원해야 한다. |

### OC-010. generateInvestmentListingReport

| 항목 | 내용 |
|---|---|
| 오퍼레이션 | `generateInvestmentListingReport()` |
| 교차 참조 | UC-008, SSD-004, FR-016, DR-001, NFR-002, NFR-003 |
| 사전 조건 | 행위자는 투자 목록 보고서를 조회할 권한이 있다. 투자 레코드가 비어 있어도 보고서 요청 자체는 유효하다. |
| 사후 조건 | 투자 목록 보고서 뷰가 생성된다. 보고서는 각 투자 항목의 항목 번호, 항목명, 예상 연간 수익, 수익 정보 최종 갱신일을 포함한다. 투자 항목이 없으면 빈 상태 메시지를 포함한다. 원본 데이터는 수정되지 않는다. 감사 로그가 활성화되어 있으면 보고서 생성 감사 이벤트가 기록된다. |
| 비고 | 이 오퍼레이션은 필수 투자 목록 보고서를 지원하며, 투자 레코드를 생성하거나 갱신하는 오퍼레이션과 분리된다. |

### OC-011. generateMortgageListingReport

| 항목 | 내용 |
|---|---|
| 오퍼레이션 | `generateMortgageListingReport()` |
| 교차 참조 | UC-009, SSD-004, FR-017, DR-003, NFR-002, NFR-003, NFR-005 |
| 사전 조건 | 행위자는 모기지 목록 보고서를 조회할 권한이 있으며, 역할상 허용되는 민감 소득 필드만 볼 수 있다. 모기지 레코드가 비어 있어도 보고서 요청 자체는 유효하다. |
| 사후 조건 | 모기지 목록 보고서 뷰가 생성된다. 보고서는 계좌 번호, 모기지 수혜자 성, 원 구매가, 발행일, 주간 원리금, 권한이 있는 경우 현재 주간 소득, 소득/세금/보험 갱신일, 연간 세금, 연간 보험료, 예상 주간 보조금을 포함한다. 모기지가 없으면 빈 상태 메시지를 포함한다. 원본 데이터는 수정되지 않는다. 감사 로그가 활성화되어 있으면 보고서 생성 감사 이벤트가 기록된다. |
| 비고 | 이 오퍼레이션은 신청자 자격 심사, 90% 모기지 비교, 고용 증빙 검증을 수행하지 않는다. |

## 5. 파일럿 제외 오퍼레이션 후보

| 오퍼레이션 후보 | 제외 사유 |
|---|---|
| `screenApplicantEligibility(...)` | 수혜자 심사/선정은 시스템 밖에서 오프라인으로 수행한다. |
| `calculateCommercial90PercentMortgage(...)` | 90% 모기지 비교 조건은 파일럿 시스템 직접 계산 범위가 아니다. |
| `verifyEmploymentEvidence(...)` | 고용 증거 데이터는 파일럿 데이터 목록에 포함되지 않는다. |
| `compareHomePriceToLocalMedian(...)` | 지역 중간 주택 가격 데이터는 파일럿 범위에 포함되지 않는다. |

## 6. 다음 설계 산출물과의 추적성

| 다음 산출물 | 이 문서의 사용 방식 |
|---|---|
| Class Diagram | 컨트랙트의 사후 조건은 필요한 속성, 메서드, 연관 관계를 식별한다. |
| Design Sequence Diagram | 각 컨트랙트는 설계 시퀀스 다이어그램에서 내부 객체 협력으로 매핑된다. |
| Test Strategy | 사전 조건과 사후 조건은 검증 조건 및 인수 확인 항목으로 전환된다. |

## 7. 확정된 계산 결정

Q-008은 사용자 결정으로 확정되었다.

```text
expected mortgage repayments
= sum(active mortgage expected beneficiary weekly repayment)
```

활성 모기지별 계산은 다음과 같다.

```text
expected beneficiary weekly repayment
= total weekly mortgage cost - weekly grant
= min(total weekly mortgage cost, affordability cap)
```

이 정의는 Q-001 산식에서 보조금이 이중 차감되는 문제를 방지한다.
