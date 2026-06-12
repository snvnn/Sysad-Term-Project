# 11. Validation and Test Strategy

## 1. Purpose

이 문서는 구현 단계에서 MSG Foundation 파일럿 시스템이 요구사항을 충족하는지 검증하기 위한 테스트 전략을 정의한다.

## 2. Test Scope

### In Scope

- 투자/운영비/모기지 데이터 입력 검증
- 주간 투자 수익 계산
- 주간 운영 비용 계산
- 에스크로 계산
- 주간 모기지 총액 계산
- 28% 소득 한도 계산
- 주간 보조금 계산
- 주간 가용 자금 계산
- 주택 비용 fundability 판단
- 보고서 3종 생성
- 권한/감사/데이터 보존 기본 검증

### Out of Scope for Pilot

- 실제 은행/보험/세무 API 연동
- 실제 대출 계약 실행
- 수혜자 자격 심사
- 90% commercial mortgage comparison
- 고용 증거 확인
- 지역 중간 주택 가격 비교
- 세금 신고서/급여 명세서 OCR 검증
- 성능 부하 테스트 대규모 시나리오
- 외부 인증 시스템 연동

## 3. Test Levels

| Level | Purpose | Examples |
|---|---|---|
| Unit Test | 계산 규칙을 독립적으로 검증 | grant calculation, escrow calculation |
| Integration Test | repository + service 흐름 검증 | run weekly computation and save snapshot |
| Contract Test | API/request/response 구조 검증 | weekly computation endpoint schema |
| Report Test | 보고서 필수 컬럼과 값 검증 | mortgage listing includes update dates |
| Security Test | 권한과 민감정보 접근 검증 | trustee cannot edit mortgage income |
| Manual QA | 사용 흐름과 보고서 이해 가능성 검토 | manager runs weekly report |

## 4. Calculation Test Cases

| Test ID | Requirement | Scenario | Expected Result | Status |
|---|---|---|---|---|
| TC-CALC-001 | FR-004 | annual investment return sum = 52000 | weekly investment income = 1000 | Draft |
| TC-CALC-002 | FR-005 | annual operating expense = 26000 | weekly operating expense = 500 | Draft |
| TC-CALC-003 | FR-006 | tax=5200, insurance=1040 | weekly escrow = 120 | Draft |
| TC-CALC-004 | FR-007 | weekly P&I=600, escrow=120 | total weekly mortgage cost = 720 | Draft |
| TC-CALC-005 | FR-008 | weekly income=2000 | affordability cap = 560 | Draft |
| TC-CALC-006 | FR-009 | total cost=720, cap=560 | weekly grant = 160 | Draft |
| TC-CALC-007 | FR-010 | active mortgage A cost=720 grant=160; active mortgage B cost=500 grant=0; suspended mortgage cost=300 grant=0 | expected mortgage repayments = 560 + 500 = 1060; suspended mortgage excluded | Resolved |
| TC-CALC-008 | FR-011 | two grants 160 and 0 | expected grants = 160 | Draft |
| TC-CALC-009 | FR-012 | investment=1000, expense=500, repayments=560, grants=160 | available amount = 900 | Resolved |

## 5. Data Validation Test Cases

| Test ID | Requirement | Scenario | Expected Result |
|---|---|---|---|
| TC-INV-001 | FR-001 | create investment with all fields | saved and listed |
| TC-INV-002 | FR-001 | duplicate item number | rejected |
| TC-INV-003 | FR-001 | negative estimated annual return | rejected |
| TC-OPEX-001 | FR-002 | create active operating expense estimate | current estimate returned |
| TC-MTG-001 | FR-003 | create mortgage with all required fields | saved and listed |
| TC-MTG-002 | FR-003 | missing income updated date | rejected |
| TC-MTG-003 | FR-003 | negative annual tax | rejected |

## 6. Fundability Test Cases

| Test ID | Requirement | Scenario | Expected Result |
|---|---|---|---|
| TC-FUND-001 | FR-013 | home cost <= remaining amount | fundable true |
| TC-FUND-002 | FR-014 | allocation recorded | remaining amount decreases by home cost |
| TC-FUND-003 | FR-014 | home cost > remaining amount | allocation rejected or fundable false |

## 7. Report Test Cases

| Test ID | Requirement | Scenario | Expected Result |
|---|---|---|---|
| TC-RPT-001 | FR-015 | weekly computation exists | weekly report includes all computation inputs and results |
| TC-RPT-002 | FR-016 | investments exist | investment report includes item number/name/return/update date |
| TC-RPT-003 | FR-017 | mortgages exist | mortgage report includes all required fields and estimated grant |

## 8. Out-of-Pilot Scope Review

These requirements are intentionally not test cases for the pilot. The validation activity is a scope review confirming they remain outside the implemented/design pilot boundary.

| Requirement | Scope Review Check |
|---|---|
| FR-018 | Marriage-duration eligibility remains outside pilot workflows, contracts, and diagrams. |
| FR-019 | Employment evidence verification remains outside pilot workflows, contracts, and diagrams. |
| FR-020 | Local median home price comparison remains outside pilot workflows, contracts, and diagrams. |
| FR-021 | 90% mortgage/savings condition remains outside pilot workflows, contracts, and diagrams. |

## 9. Non-functional Validation

| NFR | Validation Method |
|---|---|
| NFR-001 Calculation accuracy | deterministic decimal unit tests, rounding policy tests |
| NFR-002 Traceability | verify computation snapshot stores input values and update dates |
| NFR-003 Usability | manual review of report labels and warnings |
| NFR-004 Minimal scope | review implemented data model against pilot data list |
| NFR-005 Security | role-based access tests |
| NFR-006 Auditability | verify audit records for data changes and computations |
| NFR-007 Extensibility | architecture review against module boundaries |

## 10. Test Data Set

A minimal deterministic test dataset should include:

- 2 investments
- 1 operating expense estimate
- 3 mortgages:
  - one requiring grant
  - one not requiring grant
  - one with high tax/insurance to test escrow effect
- 1 weekly computation
- 1 fundability pass case
- 1 fundability fail case

## 11. Acceptance Gate for Implementation Phase

Implementation should not be considered ready unless:

- all Must functional requirements have tests
- all core calculation tests pass
- report outputs include required fields
- resolved formula rules are implemented exactly
- no sensitive sample data is real personal data
