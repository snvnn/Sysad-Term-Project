# 02. Stakeholder and User Analysis

## 1. Purpose

MSG Foundation 파일럿 시스템의 사용자, 이해관계자, 외부 주체를 식별하고 각 주체의 목표·책임·권한·관심사를 정의한다.

## 2. Stakeholder Map

| ID | Stakeholder | Type | Goals | Responsibilities | System Interaction |
|---|---|---|---|---|---|
| STK-001 | MSG Foundation Trustees / Board | Governance | 재단 자금이 목적에 맞게 사용되는지 확인 | 정책 승인, 파일럿 도입 결정 | 보고서 검토, 운영 성과 확인 |
| STK-002 | Foundation Manager | Primary User | 주간 자금 계산과 보고서 생성 | 데이터 입력/검토, 주간 계산 실행, 보고서 제출 | 대부분의 시스템 기능 사용 |
| STK-003 | Investment Advisor | External/Operational | 미사용 자금 투자 및 예상 수익 제공 | 투자 항목과 예상 연간 수익 갱신 | 투자 데이터 제공/검토 |
| STK-004 | Mortgage Program Officer | Primary User | 모기지 계정과 고객 지불 정보를 관리 | 모기지 데이터 등록/갱신, 자금 가용성 확인 | 모기지 목록/계산 결과 사용 |
| STK-005 | Married Couple / Mortgagees | Data Subject | 주택 구입 지원 및 감당 가능한 상환 | 소득 증빙, 세금/보험 관련 정보 제공 | 직접 사용자 여부는 미정 |
| STK-006 | Software Operator / Administrator | System Admin | 파일럿 시스템 유지 | 사용자 계정/백업/데이터 품질 관리 | 관리자 기능 사용 |
| STK-007 | Course Evaluator / Instructor | Evaluation | 과제 요구사항 충족 여부 평가 | 산출물과 설계 검토 | GitHub 문서 검토 |

## 3. User Roles

### ROLE-001. Foundation Manager

- **Primary goal:** 매주 시작 시 사용 가능한 모기지 자금을 계산하고 보고한다.
- **Permissions:** 투자/운영비/모기지 데이터 조회, 계산 실행, 보고서 출력.
- **Key scenarios:**
  - 이번 주 자금 결산서 생성
  - 모든 투자 목록 확인
  - 모든 모기지 목록 확인
- **Pain points:** 계산 오류, 산식 불명확성, 데이터 최신성 확인 어려움.

### ROLE-002. Mortgage Program Officer

- **Primary goal:** 모기지 계정별 주간 지불액, 소득, 세금, 보험 정보를 최신 상태로 유지한다.
- **Permissions:** 모기지 데이터 생성/수정/조회.
- **Key scenarios:**
  - 새 모기지 계정 등록
  - 소득 갱신일 확인
  - 주간 보조금 예상액 확인
- **Pain points:** 소득 변동에 따른 고객 부담액 변동 추적.

### ROLE-003. Investment Advisor

- **Primary goal:** 투자 항목과 예상 연간 수익 정보를 제공한다.
- **Permissions:** 투자 데이터 제공 또는 검토. 파일럿에서는 재단 관리자/승인된 운영 담당자가 입력하고, 투자 고문 직접 사용은 선택 확장으로 둔다.
- **Key scenarios:**
  - 분기별 예상 수익 갱신
  - 주말 미사용 자금 투자 보고

### ROLE-004. System Administrator

- **Primary goal:** 파일럿 시스템의 사용자 접근과 데이터 백업을 관리한다.
- **Permissions:** 사용자/권한/백업/감사 로그 관리.
- **Key scenarios:**
  - 사용자 계정 생성
  - 데이터 백업 확인
  - 운영 로그 점검

## 4. User Journey — Weekly Funds Computation

1. Foundation Manager가 주 시작일에 시스템에 접속한다.
2. 시스템은 투자 데이터, 운영 비용 데이터, 활성 모기지 데이터를 불러온다.
3. Manager는 최근 갱신일을 확인한다.
4. 시스템은 주간 투자 수익, 주간 운영 비용, 예상 모기지 상환액, 예상 보조금 총액을 계산한다.
5. 시스템은 해당 주 시작 시 사용 가능한 금액을 표시한다.
6. Manager는 주간 자금 결산서를 저장/출력한다.
7. 주중 신규 주택 구매 요청이 있으면 주택 비용이 가용 모기지 금액 이하인지 확인한다.
8. 승인 가능하면 해당 주 가용 모기지 금액을 주택 비용만큼 감소시킨다.
9. 주말에 미사용 자금은 투자 고문에게 전달되어 투자된다.

## 5. User Journey — On-demand Reports

### Investment Listing

1. Manager가 투자 목록 보고서를 요청한다.
2. 시스템은 모든 투자 항목을 item number 기준으로 조회한다.
3. 시스템은 item name, estimated annual return, last updated date를 표시한다.

### Mortgage Listing

1. Manager가 모기지 목록 보고서를 요청한다.
2. 시스템은 모든 모기지 계정을 조회한다.
3. 시스템은 계정 번호, mortgagees last name, 주택 원가, 발행일, 주간 P&I, 소득, 세금, 보험료 및 갱신일을 표시한다.

## 6. Open Issues

| ID | Issue | Status |
|---|---|---|
| OI-STK-001 | 투자 고문이 직접 시스템을 사용하는지, 관리자에게 데이터를 전달하는지만 하는지 미정 | Developer Decision: 관리자/운영 담당자 입력, 투자 고문 직접 사용은 선택 확장 |
| OI-STK-002 | 부부/신청자가 직접 시스템을 사용하는지 미정 | Resolved: 파일럿 직접 사용자는 아님 |
| OI-STK-003 | 파일럿의 사용자 인증 방식과 사용자 수 미정 | Developer Decision: 단순 관리자/승인 운영 담당자 모델 |
