# Sysad Term Project — The MSG Foundation

이 저장소는 `The MSG Foundation Case Study` 요구사항을 바탕으로 소프트웨어 시스템을 분석·설계하기 위한 산출물을 관리한다.

## Current Scope

현재 과제 범위는 **시스템 분석과 설계까지**로 제한한다.

- 구현하지 않는다.
- DB/API/배포 환경을 변경하지 않는다.
- 요구사항, 도메인, 아키텍처, 데이터, UI, 보안, 테스트 전략, 구현 계획을 문서화한다.

## Documentation Index

Full index: [`docs/PROJECT_INDEX.md`](docs/PROJECT_INDEX.md)

## Deliverables

### Source

- [`docs/00-source/requirements-extracted-raw.txt`](docs/00-source/requirements-extracted-raw.txt)
- [`docs/00-source/requirements-extracted.md`](docs/00-source/requirements-extracted.md)

### Analysis

- [`docs/01-initiation/problem-definition.md`](docs/01-initiation/problem-definition.md)
- [`docs/01-initiation/scope-statement.md`](docs/01-initiation/scope-statement.md)
- [`docs/01-initiation/core-questions.md`](docs/01-initiation/core-questions.md)
- [`docs/02-stakeholders/stakeholder-analysis.md`](docs/02-stakeholders/stakeholder-analysis.md)
- [`docs/03-requirements/software-requirements-specification.md`](docs/03-requirements/software-requirements-specification.md)
- [`docs/03-requirements/requirements-traceability-matrix.md`](docs/03-requirements/requirements-traceability-matrix.md)
- [`docs/04-domain/domain-model.md`](docs/04-domain/domain-model.md)
- [`docs/05-context/current-state-and-constraints.md`](docs/05-context/current-state-and-constraints.md)

### Design

- [`docs/06-architecture/architecture-design.md`](docs/06-architecture/architecture-design.md)
- [`docs/07-interfaces/api-contract.md`](docs/07-interfaces/api-contract.md)
- [`docs/08-data/data-model.md`](docs/08-data/data-model.md)
- [`docs/09-ui/report-specification.md`](docs/09-ui/report-specification.md)
- [`docs/10-security-operations/security-and-operations.md`](docs/10-security-operations/security-and-operations.md)
- [`docs/11-validation/test-strategy.md`](docs/11-validation/test-strategy.md)
- [`docs/12-implementation/roadmap-and-wbs.md`](docs/12-implementation/roadmap-and-wbs.md)
- [`docs/13-ooad-deliverables/remaining-deliverables-plan.md`](docs/13-ooad-deliverables/remaining-deliverables-plan.md)

## Key Decisions Reflected

- 과제 범위는 **분석 및 설계 산출물**까지이며, 구현은 선택 사항이다.
- 파일럿 시스템은 자격 심사, 90% 모기지 조건 판단, 고용 증거 확인, 주택 가격 검증을 직접 수행하지 않는다.
- 핵심 관리 데이터는 투자 데이터, 운영 비용 데이터, 모기지 데이터로 제한한다.
- 주간 가용 자금 산식은 `weekly investment income - weekly operating expenses + expected mortgage repayments - expected grants`로 둔다.
- 구현 플랫폼과 보고서 출력 방식은 개발자 재량으로 결정한다.
- 데이터 입력 권한은 단순 관리자/승인 운영 담당자 중심 모델을 기본으로 한다.
- 금액은 decimal/fixed-point로 계산하고 저장/표시 시 센트 단위로 반올림한다.
- 주간 기준은 공휴일과 재단 휴업일을 제외한 첫/마지막 영업일로 둔다.

## Remaining Open Issue

- `expected mortgage repayments`가 수혜자 실제 예상 상환액인지, 보조금 지급 전 전체 P&I+에스크로 총액인지는 출처가 명확하지 않으므로 최종 구현 전 추가 논의가 필요하다. 현재 권장 해석은 수혜자 실제 예상 상환액 기준이다.

## Suggested Next Step

공식 제출 전 다음을 확인한다.

1. Q-008 `expected mortgage repayments` 합산 범위 최종 확인
2. 문서 산출물 간 산식/용어 일관성 리뷰
3. 제출 형식에 맞춘 최종 기술 문서 패키징
