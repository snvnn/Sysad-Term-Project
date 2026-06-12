# MSG Foundation Term Project - 분석 및 설계 보고서 초안

## 1. 표지 정보

| 항목 | 내용 |
|---|---|
| 프로젝트 | MSG Foundation 파일럿 시스템 |
| 과목 / 과제 | Systems Analysis and Design Term Project |
| 보고서 상태 | 제출 전 편집용 초안 |
| 저장소 범위 | 분석 및 설계 문서 산출물 |
| 구현 범위 | 이 저장소에서는 구현하지 않음 |
| Rich Picture | 사용자가 수기로 작성하여 별도 제출 |

## 2. 요약

이 Term Project는 MSG Foundation을 위한 파일럿 정보 시스템을 분석하고 설계한 결과를 정리한다. MSG Foundation은 젊은 기혼 부부가 주택을 구입할 수 있도록 100% 모기지와 보조금을 제공하는 재단이다. 재단의 핵심 업무 문제는 투자 수익, 운영비, 예상 모기지 상환액, 예상 보조금 지급액을 고려하여 매주 신규 주택 구입에 사용할 수 있는 금액을 일관되게 산정하는 것이다.

이 프로젝트의 범위는 분석과 설계로 제한하였다. 애플리케이션 코드, 데이터베이스 마이그레이션, 운영 배포, 외부 시스템 연동, 실제 금융 거래 처리는 포함하지 않았다. 대신 요구사항, 도메인, OOAD, 아키텍처, 인터페이스, 데이터, 보고서, 보안, 검증, 구현 계획 문서를 체계적으로 작성하였다.

최종 설계는 파일럿 범위에 맞게 작게 유지하였다. 시스템은 투자 데이터, 운영비 추정 데이터, 모기지 데이터를 관리하고, 주간 가용 자금 계산 결과 보고서, 전체 투자 목록 보고서, 전체 모기지 목록 보고서를 제공한다.

## 3. 프로젝트 배경과 문제 정의

MSG Foundation은 Martha Stockton Greengage의 유산을 기반으로 운영되며, 젊은 기혼 부부의 주택 구입을 지원하는 목적을 가진다. 재단은 매주 가용 자금을 계산하고 보고서를 준비해야 하므로, 수작업 부담을 줄이고 계산 결과의 일관성을 높일 수 있는 시스템이 필요하다.

문제 정의 단계에서는 다음 세 가지 핵심 비즈니스 질문을 정리하였다.

1. 매주 초 신규 주택 구입에 사용할 수 있는 금액은 얼마인가?
2. 기존 모기지의 예상 상환액과 보조금 의무는 그 금액에 어떤 영향을 주는가?
3. 투자, 운영비, 모기지 데이터를 바탕으로 신뢰할 수 있는 주간 및 요청 기반 보고서를 생성할 수 있는가?

현재 수작업 방식의 주요 위험은 계산 오류, fundability 판단의 일관성 부족, 데이터 변경 이력 추적의 어려움, 보고서 작성 부담이다. 이 내용은 [Problem definition](../01-initiation/problem-definition.md)에 정리되어 있다.

## 4. 범위와 경계 결정

이 프로젝트는 분석 및 설계 중심의 파일럿 시스템으로 범위를 제한하였다. [Scope statement](../01-initiation/scope-statement.md)는 요구사항 추출, 이해관계자 분석, 도메인 모델링, 데이터 모델링, 아키텍처 설계, 인터페이스 설계, 보고서 설계, 보안 및 운영 계획, 검증 계획, 구현 로드맵 작성을 범위로 정의한다.

파일럿에 포함한 항목은 다음과 같다.

- 투자 데이터 관리
- 운영비 추정 데이터 관리
- 모기지 계정 데이터 관리
- 주간 가용 자금 계산
- 주택 구입 가능 여부 확인
- 자금 배정 기록
- 주간 자금 보고서, 투자 목록 보고서, 모기지 목록 보고서

파일럿에서 제외한 항목은 다음과 같다.

- 전체 신청자 자격 심사
- 90% 상업 모기지 비교 조건 판정
- 고용 증빙 검증
- 지역 중간 주택 가격 비교
- 실제 은행, 보험, 세금, 결제 시스템 연동
- 실제 모기지 계약 체결
- 세금 신고서 또는 급여명세서 OCR 처리

이러한 제외는 의도적인 범위 통제이다. 과제에서 요구한 핵심 데이터와 주간 자금 계산에 집중하기 위해, 자격 심사와 외부 검증 업무는 파일럿 밖의 선행 절차로 유지하였다. 관련 경계는 [Scope statement](../01-initiation/scope-statement.md), [Use cases](../03-requirements/use-cases.md), [Test strategy](../11-validation/test-strategy.md)에 반영되어 있다.

## 5. 분석 과정

### 5.1 요구사항 추출

첫 단계에서는 원천 요구사항을 보존하고 요약하였다. 저장소에는 원문 추출본과 정리된 요구사항 추출본을 함께 보관하였다.

- [Extracted raw text](../00-source/requirements-extracted-raw.txt)
- [Requirements extract](../00-source/requirements-extracted.md)

이후 추출한 요구사항을 기능 요구사항, 비기능 요구사항, 데이터 요구사항, 비즈니스 규칙, 당시의 open issue로 구조화하여 [Software requirements specification](../03-requirements/software-requirements-specification.md)에 반영하였다.

### 5.2 이해관계자와 사용자 분석

이해관계자 분석에서는 파일럿 시스템의 영향을 받는 주요 사람과 그룹을 식별하였다.

- 이사회 / Trustees
- Foundation Manager
- Investment Advisor
- Mortgage Program Officer
- Married Couple / Mortgagees
- Software Operator / Administrator
- Course Evaluator / Instructor

Foundation Manager는 주간 계산 실행과 보고서 생성을 담당하는 핵심 운영 사용자이다. Mortgage Program Officer는 모기지 데이터를 관리하고, Investment Advisor는 투자 수익 추정 정보를 제공하거나 검토한다. Trustees는 주로 보고서를 소비한다. 이 역할 구분은 [Stakeholder and user analysis](../02-stakeholders/stakeholder-analysis.md)에 정리되어 있다.

### 5.3 요구사항 구조화

SRS는 21개의 기능 요구사항을 정의한다. `FR-001`부터 `FR-017`까지는 파일럿의 분석 및 설계 범위에 포함된다. `FR-018`부터 `FR-021`까지는 요구사항에서 완전히 삭제하지 않고 future 또는 out-of-pilot 후보로 보존하였다.

주요 Must 요구사항은 다음 업무를 포함한다.

- 투자, 운영비, 모기지 데이터 관리
- 주간 투자 수익과 주간 운영비 계산
- 주간 escrow, 전체 모기지 비용, 소득 한도, 보조금, 수혜자 상환액 계산
- 주간 가용 자금 계산
- 주간 자금 보고서
- 투자 목록 보고서
- 모기지 목록 보고서

요구사항 추적성은 [Requirements traceability matrix](../03-requirements/requirements-traceability-matrix.md)에서 관리한다.

## 6. 핵심 도메인 결정

### 6.1 주간 가용 자금 공식

주간 가용 자금 공식은 다음과 같이 확정하였다.

```text
weekly investment income
- weekly operating expenses
+ expected mortgage repayments
- expected grants
```

이 공식은 투자 수익과 수혜자 상환액을 유입으로, 운영비와 보조금 지급액을 유출로 본다.

### 6.2 Expected Mortgage Repayments

가장 중요한 계산 결정은 Q-008이었다. 최종 결정은 다음과 같다.

```text
expected mortgage repayments
= sum(active mortgage expected beneficiary weekly repayment)

expected beneficiary weekly repayment
= total weekly mortgage cost - weekly grant
= min(total weekly mortgage cost, affordability cap)
```

즉, expected mortgage repayments는 활성 mortgage별로 수혜자가 실제로 납부할 것으로 예상되는 주간 상환액의 합이다. 전체 모기지 비용을 상환액으로 더하고 별도로 expected grants를 빼면 보조금이 이중 반영될 수 있으므로, 수혜자 부담분만 상환액으로 계산한다.

이 결정은 [Core questions](../01-initiation/core-questions.md), [Domain model](../04-domain/domain-model.md), [SRS](../03-requirements/software-requirements-specification.md)에 반영되어 있다.

### 6.3 금액, 반올림, 날짜 가정

금액은 decimal 또는 fixed-point money semantics를 사용한다고 가정하고, 저장 및 표시 값은 센트 단위로 반올림한다. 연간 금액은 52로 나누어 주간 금액으로 변환한다. 주의 시작일은 첫 영업일, 종료일은 마지막 영업일로 보며, 공휴일과 재단 휴무일은 제외한다. 이 결정은 [Core questions](../01-initiation/core-questions.md)에 기록되어 있다.

## 7. OOAD 산출물

### 7.1 Brief Use Cases

Brief Use Cases는 내부 구현 객체를 추가하지 않고 사용자의 주요 목표를 설명한다. 작성한 use case는 다음과 같다.

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

전체 use case는 [Brief use cases](../03-requirements/use-cases.md)에 정리되어 있다.

### 7.2 System Sequence Diagrams

System Sequence Diagram은 actor와 system boundary 사이의 메시지만 보여준다. 내부 클래스, repository, calculator, service는 SSD에 포함하지 않았다.

SSD 집합은 다음 네 개 다이어그램으로 구성된다.

| ID | Diagram |
|---|---|
| SSD-001 | Run Weekly Funds Computation |
| SSD-002 | Manage Mortgage Data |
| SSD-003 | Check Home Fundability |
| SSD-004 | Generate Reports |

문서와 원본 다이어그램은 [System sequence diagrams](../03-requirements/system-sequence-diagrams.md), [system-sequence-diagrams.drawio](../03-requirements/system-sequence-diagrams.drawio)에 있다.

### 7.3 Operation Contracts

Operation Contract는 system event와 use case를 domain state change로 연결한다. 각 contract는 operation, cross reference, precondition, postcondition, note를 포함한다.

Contract 집합은 데이터 관리, 주간 계산, fundability 확인, allocation 기록, 보고서 생성 operation을 다룬다. 계산 책임이 가장 큰 contract는 `OC-006 runWeeklyComputation(...)`이며, 이 contract는 입력 snapshot을 가진 `WeeklyComputation`을 생성하고 확정된 주간 가용 자금 공식을 적용한다. 자세한 내용은 [Operation contracts](../07-design/operation-contracts.md)에 있다.

### 7.4 Class Diagram

Class Diagram은 핵심 domain 객체와 service 책임을 표현한다. 주요 클래스는 다음과 같다.

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

설계에서는 mortgage별 계산 공식을 `MortgageCalculator`에 두고, 주간 가용 자금 집계는 `FundsAvailabilityCalculator`에 둔다. 이를 통해 계산 책임을 UI나 persistence layer에 흩어지지 않게 했다. Class Diagram은 도형 다이어그램으로 제출본 편집 시 이미지로 첨부한다.

Class Diagram의 각 클래스를 롤플레잉 검증에 사용할 수 있도록 Class Cards도 작성하였다. 각 카드는 클래스 이름과 역할, 알아야 될 정보, 행해야 될 정보, 의존성 또는 association을 포함한다. 보고서 제출본에서 Class Cards는 외부 링크로 대체하지 않고, 이 보고서의 부록에 원문 내용을 직접 포함한다.

### 7.5 Design Sequence Diagrams

Design Sequence Diagram은 SSD를 내부 객체 협력 수준으로 확장한다. SSD와 달리 DSD는 UI/controller, application service, repository, domain entity, calculator, report rendering 객체를 보여준다.

DSD 집합은 11개 page로 구성되며, 투자 관리, 운영비 추정 업데이트, 모기지 관리, 주간 계산, fundability 확인, allocation 기록, 세 가지 보고서 생성을 모두 포함한다. 문서는 [Design sequence diagrams](../07-design/sequence-diagrams.md), 원본 다이어그램은 [sequence-diagrams.drawio](../07-design/sequence-diagrams.drawio)에 있다.

## 8. 아키텍처와 설계 전략

권장 아키텍처는 layered architecture를 적용한 modular monolith이다. 이 선택은 작은 파일럿 범위에 적합하며, 불필요한 distributed system 복잡도를 피한다.

주요 layer는 다음과 같다.

- Presentation Layer: 사용자 입력, 화면, 메뉴, 보고서 요청
- Application Layer: use case orchestration, transaction boundary, authorization check
- Domain Layer: mortgage, grant, repayment, weekly fund calculation rule
- Repository Layer: persistence port와 adapter
- Report Renderer: weekly, investment, mortgage report output
- Audit Logger: 데이터 변경 및 계산 이벤트 기록

의존성 방향은 다음과 같이 정리하였다.

```text
Presentation -> Application -> Domain
Application -> Repository Port <- Repository Adapter
Application -> Report Port <- Report Adapter
```

아키텍처 설계는 [Architecture design](../06-architecture/architecture-design.md)에 정리되어 있다.

## 9. 데이터, 인터페이스, 보고서 설계

데이터 모델은 investment, operating expense estimate, mortgage, weekly computation, weekly computation input snapshot, fund allocation entity를 정의한다. Weekly computation snapshot은 각 보고서에서 사용한 입력값을 보존하여 auditability와 reproducibility를 지원한다. 데이터 모델은 [Data model and schema design](../08-data/data-model.md)에 있다.

인터페이스 계약은 CLI, Web, Desktop 중 어떤 형태로 구현하더라도 사용할 수 있는 논리적 endpoint 또는 service contract를 정의한다. 금액은 명시적 money string으로, 날짜는 ISO date로 표현하여 모호성을 줄였다. 계약은 [API and interface contract](../07-interfaces/api-contract.md)에 있다.

보고서 설계는 report-first 사용자 경험을 기준으로 작성하였다. 필수 보고서는 다음 세 가지이다.

1. Weekly funds computation results
2. Listing of all investments
3. Listing of all mortgages

보고서 설계에서는 읽기 쉬운 label, 입력 데이터 freshness, 필수 계산 결과, 요청 시 출력 가능성을 강조하였다. 자세한 내용은 [UX/UI and report specification](../09-ui/report-specification.md)에 있다.

## 10. 검증 전략

검증 계획은 unit, integration, contract, report, security, manual QA 수준을 포함한다. 가장 중요한 테스트는 투자 수익, 운영비, escrow, 전체 모기지 비용, affordability cap, weekly grant, expected mortgage repayments, expected grants, available amount에 대한 deterministic calculation test이다.

Q-008 테스트 케이스는 expected mortgage repayments가 활성 mortgage만 포함하고, 각 mortgage의 수혜자 부담 주간 상환액을 사용하는지 확인한다.

```text
active mortgage A: cost 720, grant 160 -> repayment 560
active mortgage B: cost 500, grant 0 -> repayment 500
suspended mortgage: excluded
expected mortgage repayments = 560 + 500 = 1060
```

검증 전략은 [Test strategy](../11-validation/test-strategy.md)에 정리되어 있다.

## 11. 추적성과 일관성 관리

추적성은 requirement ID, use case ID, operation contract ID, SSD/DSD ID, test ID를 통해 관리한다. RTM은 각 functional requirement를 source evidence, design artifact, test artifact와 연결한다.

예시는 다음과 같다.

| Requirement | Traceability Path |
|---|---|
| FR-010 expected mortgage repayments | UC-004, SSD-001, OC-006, DSD-006, class diagram, class cards, BR-007, TC-CALC-007 |
| FR-012 weekly available amount | UC-004, SSD-001, OC-006, DSD-006, BR-008, TC-CALC-009 |
| FR-015 weekly funds report | UC-007, SSD-004, OC-009, DSD-009, report specification, TC-RPT-001 |
| FR-016 investment listing | UC-008, SSD-004, OC-010, DSD-010, report specification, TC-RPT-002 |
| FR-017 mortgage listing | UC-009, SSD-004, OC-011, DSD-011, report specification, TC-RPT-003 |

전체 matrix는 프로젝트 산출물에 유지되어 있으며, 제출본에는 필요한 범위의 추적성 표를 본문 또는 부록으로 직접 포함한다.

## 12. 분석 및 설계 과정에서 겪은 문제와 해결

### 12.1 주간 가용 자금 공식의 부호 모호성

원천 요구사항은 investment income, operating expenses, mortgage repayments, grants를 나열했지만 각 항목의 부호 convention을 완전히 명시하지 않았다. 프로젝트에서는 investment income과 repayments를 유입으로, operating expenses와 grants를 유출로 해석하여 해결하였다.

해결 결과는 다음과 같다.

```text
available = income - expenses + repayments - grants
```

### 12.2 Expected Mortgage Repayment 범위 모호성

원천 요구사항은 expected mortgage repayments가 전체 모기지 비용인지, 수혜자가 실제로 납부할 부분인지 명시하지 않았다. 프로젝트에서는 이를 open issue로 보존한 뒤 검토를 거쳐 최종 결정하였다.

해결 결과는 다음과 같다.

```text
expected mortgage repayments = sum of active mortgage beneficiary-paid weekly repayments
```

이 결정은 보조금 이중 반영을 막고, 주간 가용 자금 공식의 의미를 일관되게 만든다.

### 12.3 파일럿 범위와 자격 심사 규칙의 분리

사례 설명에는 혼인 기간, 고용 증빙, 지역 중간 주택 가격 비교, 90% mortgage/savings 조건처럼 eligibility에 가까운 조건이 포함되어 있다. 이를 파일럿에 포함하면 데이터 모델과 workflow가 과제의 핵심 데이터 범위를 크게 벗어난다.

해결 결과:

해당 항목은 out-of-pilot 또는 future scope로 문서화하였다. 파일럿은 자격 심사가 오프라인에서 완료된 뒤 시스템에 관리 대상 mortgage가 들어온다고 가정한다.

### 12.4 OOAD 산출물 경계 분리

System Sequence Diagram과 Design Sequence Diagram은 혼동되기 쉽다. 프로젝트에서는 두 산출물을 다음처럼 분리하였다.

- SSD는 actor-to-system message만 표시한다.
- DSD는 internal object collaboration을 표시한다.
- Operation Contract는 두 수준을 연결한다.

이 분리는 분석 산출물과 설계 산출물의 역할을 명확하게 유지하는 데 도움이 되었다.

## 13. 현재 산출물 패키지

현재 저장소에는 다음 산출물 그룹이 포함되어 있다.

| 그룹 | 주요 산출물 |
|---|---|
| Source and initiation | 요구사항 추출, 문제 정의, 범위 정의, 핵심 질문 |
| Stakeholder and requirements | 이해관계자 분석, SRS, RTM, use cases, SSDs |
| Domain and context | Domain model, current state and constraints |
| Architecture and interfaces | Architecture design, API/interface contract |
| OOAD design | Operation contracts, class diagram, class cards, DSDs |
| Data and reports | Data model, report specification |
| Security and validation | Security/operations plan, test strategy |
| Implementation planning | Roadmap and WBS |

[Project documentation index](../PROJECT_INDEX.md)와 저장소 [README](../../README.md)는 전체 산출물의 진입점이다.

## 14. 제출 전 참고 사항

- Rich Picture는 이 저장소에서 생성하지 않았다. 사용자가 수기로 작성하여 별도 제출하는 산출물이다.
- draw.io 파일은 저장소에서 편집 가능한 원본 다이어그램으로 유지한다.
- 이 보고서는 초안이다. 제출 전 과목명, 학생명, 교수자명, 제출일 등 과목 양식에서 요구하는 정보를 채워야 한다.
- 이 저장소에는 구현 코드가 없다. 분석 및 설계 산출물 패키지로 유지한다.

## 15. 결론

이 프로젝트는 MSG Foundation 파일럿 시스템에 대한 일관된 분석 및 설계 패키지를 작성하였다. 핵심 설계 결과는 투자, 운영비, 모기지 데이터를 관리하고 추적 가능한 domain formula를 통해 주간 가용 자금을 계산하는 작은 report-first 시스템이다. OOAD 산출물은 사용자 목표를 system event, operation contract, domain class, internal object collaboration, validation scenario로 연결한다.

최종 패키지는 향후 구현자가 플랫폼을 선택한 뒤 문서화된 workflow, 계산 규칙, 저장 구조, 보고서 요구사항을 바탕으로 구현을 시작할 수 있는 수준의 설계 기준선을 제공한다.

## 16. 첨부 산출물 원문

이 절은 제출본에 첨부할 산출물을 단순 링크가 아니라 보고서 본문에 직접 포함하기 위한 영역이다. 사진 또는 이미지가 필요한 다이어그램은 사용자가 제출본 편집 시 직접 삽입한다.

### 16.1 다이어그램 첨부 위치

다음 다이어그램은 이미지 첨부가 필요한 산출물이므로, 제출본 편집 시 사용자가 직접 사진 또는 export 이미지를 삽입한다.

| Diagram Artifact | Attachment Note |
|---|---|
| Rich Picture | 사용자 수기 작성본을 사진 또는 스캔본으로 첨부 |
| System Sequence Diagrams, SSD | draw.io 원본 기준 이미지 첨부 |
| Class Diagram | draw.io 원본 기준 이미지 첨부 |
| Design Sequence Diagrams, DSD | draw.io 원본 기준 이미지 첨부 |

### 16.2 Class Cards 원문

아래 내용은 클래스 카드 산출물의 원문을 보고서 본문에 직접 포함한 것이다.

#### 16.2.1 Purpose

이 문서는 MSG Foundation 파일럿 시스템의 설계 검증용 클래스 카드, 또는 객체 카드를 정리한다. 각 카드는 강의의 롤플레잉 검증 방식에 맞춰 한 학생이 하나의 객체 역할을 맡았을 때 필요한 정보를 담는다.

각 카드에는 다음 네 가지 정보를 반드시 포함한다.

| Card Element | Meaning |
|---|---|
| 클래스 이름, 역할 | 롤플레잉에서 참가자가 맡을 객체의 정체성 |
| 알아야 될 정보, Attribute | 객체가 스스로 알고 유지해야 하는 데이터와 상태 |
| 행해야 될 정보, Responsibility / Operation | 객체가 수행해야 하는 행동, 계산, 상태 변경 |
| 의존성, Association | 내 카드에 없는 정보를 얻기 위해 도움을 요청할 수 있는 다른 객체로 가는 연결 경로 |

#### 16.2.2 Role-Playing Validation Rules

- 참가자는 자신이 맡은 카드의 attribute만 직접 알고 있다고 가정한다.
- 카드에 없는 정보가 필요하면 dependency / association에 적힌 다른 객체에게 요청한다.
- 어떤 책임을 수행할 길이 없으면 association 누락 후보로 기록한다.
- Q-008 계산은 반드시 `Mortgage`, `MortgageCalculator`, `FundsAvailabilityCalculator`의 책임 분리대로 수행한다.
- 자격 심사, 고용 증빙 확인, 지역 중간 주택 가격 비교, 90% commercial mortgage 비교는 파일럿 밖 업무이므로 카드 책임에 넣지 않는다.

#### 16.2.3 CC-001. `Money`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `Money`: 금액과 통화를 안전하게 표현하는 value object |
| 알아야 될 정보, Attribute | `amount`, `currency` |
| 행해야 될 정보, Responsibility / Operation | 금액 더하기 `plus`; 금액 빼기 `minus`; 금액 곱하기 `multiply`; 금액 나누기 `divide`; 센트 단위 반올림 `roundToCents`; 음수가 아닌지 확인 `isNonNegative` |
| 의존성, Association | 없음. 독립 value object이며 다른 domain object가 금액 표현을 위해 사용한다. |

Role-playing note:

- 금액 계산이 필요한 객체는 raw number 대신 `Money`에게 계산과 반올림을 맡긴다.
- `Money`는 mortgage, investment, report의 의미를 알지 않는다.

#### 16.2.4 CC-002. `Investment`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `Investment`: 재단 투자 항목과 예상 연간 수익을 보관하는 domain entity |
| 알아야 될 정보, Attribute | `itemNumber`, `itemName`, `estimatedAnnualReturn`, `returnLastUpdatedDate` |
| 행해야 될 정보, Responsibility / Operation | 연간 예상 수익을 주간 수익으로 변환 `weeklyReturn`; 예상 연간 수익과 갱신일 변경 `updateReturn` |
| 의존성, Association | `Money`: 수익 금액 계산과 반올림에 사용; `FundsAvailabilityCalculator`: 주간 가용 자금 계산 시 이 객체들을 읽는다; `ReportService`: 투자 목록 보고서 생성 시 이 객체들을 읽는다. |

Role-playing note:

- `Investment`는 자신의 annual return만 알고 있으며 전체 투자 합계는 계산하지 않는다.
- 전체 weekly investment income은 `FundsAvailabilityCalculator`가 여러 `Investment`를 모아 계산한다.

#### 16.2.5 CC-003. `OperatingExpenseEstimate`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `OperatingExpenseEstimate`: 재단의 연간 운영비 추정치를 보관하는 domain entity |
| 알아야 될 정보, Attribute | `estimateId`, `estimatedAnnualOperatingExpenses`, `expensesLastUpdatedDate`, `active` |
| 행해야 될 정보, Responsibility / Operation | 연간 운영비를 주간 운영비로 변환 `weeklyExpense`; 현재 추정치를 활성화 `activate`; 새 추정치로 교체 `replaceEstimate` |
| 의존성, Association | `Money`: 운영비 계산과 반올림에 사용; `FundsAvailabilityCalculator`: 주간 가용 자금 계산 시 active estimate를 읽는다. |

Role-playing note:

- `OperatingExpenseEstimate`는 운영비 입력의 최신성 및 활성 상태를 담당한다.
- 여러 추정치 중 어떤 값을 사용할지는 application workflow 또는 repository가 active record를 제공하고, 계산은 `FundsAvailabilityCalculator`가 수행한다.

#### 16.2.6 CC-004. `Mortgage`

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

#### 16.2.7 CC-005. `WeeklyComputation`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `WeeklyComputation`: 특정 주의 주간 자금 계산 결과와 남은 가용 자금을 보관하는 aggregate root |
| 알아야 될 정보, Attribute | `computationId`, `weekStartDate`, `formulaVersion`, `weeklyInvestmentIncome`, `weeklyOperatingExpense`, `expectedMortgageRepayments`, `expectedGrants`, `startingAvailableAmount`, `remainingAvailableAmount`, `status`, `createdAt`, `warnings` |
| 행해야 될 정보, Responsibility / Operation | 입력 snapshot 추가 `addInputSnapshot`; 특정 주택 비용을 funding할 수 있는지 확인 `canFund`; allocation 적용 후 remaining amount 차감 `applyAllocation`; 경고 추가 `attachWarning`; 계산 결과 확정 `finalize` |
| 의존성, Association | `WeeklyComputationItem`: `1` to `0..*`, 계산 입력 snapshot 보관; `FundAllocation`: `1` to `0..*`, 같은 주의 자금 배정 기록 보관; `Money`: 계산 결과 금액 표현; `FundsAvailabilityCalculator`: 최초 계산 결과 생성에 사용; `ReportService`: weekly funds report 생성 시 읽음. |

Role-playing note:

- `WeeklyComputation`은 이미 계산된 결과와 상태를 유지한다.
- 투자, 운영비, mortgage 원본 데이터를 직접 찾아다니지 않고 `WeeklyComputationItem` snapshot으로 당시 입력을 보존한다.

#### 16.2.8 CC-006. `WeeklyComputationItem`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `WeeklyComputationItem`: 주간 계산에 사용된 입력값의 snapshot item |
| 알아야 될 정보, Attribute | `id`, `computationId`, `sourceType`, `sourceId`, `snapshot` |
| 행해야 될 정보, Responsibility / Operation | source record의 계산 시점 값을 보존 `capture` |
| 의존성, Association | `WeeklyComputation`: 반드시 하나의 computation에 속함; `Investment`: sourceType이 investment일 때 snapshot 대상; `OperatingExpenseEstimate`: sourceType이 operating expense일 때 snapshot 대상; `Mortgage`: sourceType이 mortgage일 때 snapshot 대상. |

Role-playing note:

- 이 카드는 audit과 report 재현성을 위한 증거 카드이다.
- 원본 source가 나중에 바뀌어도 snapshot은 당시 계산 값을 유지한다.

#### 16.2.9 CC-007. `FundAllocation`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `FundAllocation`: 특정 주의 가용 자금에서 주택 구입 비용을 배정하거나 fundability 판단을 기록하는 entity |
| 알아야 될 정보, Attribute | `allocationId`, `computationId`, `homeCost`, `reference`, `fundable`, `remainingAfterAllocation`, `createdAt` |
| 행해야 될 정보, Responsibility / Operation | allocation 기록 `record`; fundable 여부 표시 `markFundable` |
| 의존성, Association | `WeeklyComputation`: allocation은 특정 computation의 remaining available amount를 기준으로 함; `Mortgage`: allocation reference가 관리 대상 mortgage 또는 주택 구매 reference를 가리킬 수 있음; `Money`: `homeCost`와 `remainingAfterAllocation` 표현. |

Role-playing note:

- `FundAllocation`은 신청자 자격 심사를 수행하지 않는다.
- 파일럿에서는 주택 비용이 남은 가용 자금 이하인지와 그 결과 기록에 집중한다.

#### 16.2.10 CC-008. `MortgageCalculator`

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

#### 16.2.11 CC-009. `FundsAvailabilityCalculator`

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

#### 16.2.12 CC-010. `ReportService`

| Element | Content |
|---|---|
| 클래스 이름, 역할 | `ReportService`: 요구된 세 가지 보고서를 생성하는 application/report service |
| 알아야 될 정보, Attribute | 별도 저장 attribute 없음. 보고서 생성 요청과 조회된 domain data를 입력으로 받는다. |
| 행해야 될 정보, Responsibility / Operation | 주간 자금 계산 결과 보고서 생성 `generateWeeklyFundsReport`; 투자 목록 보고서 생성 `generateInvestmentListing`; 모기지 목록 보고서 생성 `generateMortgageListing` |
| 의존성, Association | `WeeklyComputation`: weekly funds report의 핵심 데이터; `WeeklyComputationItem`: 계산 입력 snapshot 표시; `FundAllocation`: allocation 결과 표시; `Investment`: investment listing report 입력; `Mortgage`: mortgage listing report 입력; `Money`: 금액 표시 format; report renderer 또는 output adapter: 실제 출력 형식 생성. |

Role-playing note:

- `ReportService`는 계산 규칙을 새로 수행하지 않고 이미 확정된 domain data와 computation result를 읽어 출력한다.
- 보고서 값이 이상하면 계산 객체가 아니라 원본 `WeeklyComputation`과 snapshot을 추적한다.

#### 16.2.13 Dependency Check Matrix

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

#### 16.2.14 Verification Checklist

- [x] 모든 카드가 클래스 이름, 알아야 될 정보, 행해야 될 정보, 의존성을 포함한다.
- [x] Class Diagram의 10개 클래스가 모두 카드로 작성되어 있다.
- [x] 카드의 attributes와 operations는 Class Diagram의 class table과 일치한다.
- [x] Q-008 계산 책임이 `MortgageCalculator`와 `FundsAvailabilityCalculator`에 명시되어 있다.
- [x] 자격 심사와 90% commercial mortgage 비교는 out-of-pilot으로 유지되어 있다.
