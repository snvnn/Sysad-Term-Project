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

## Key Open Issues

- 주간 가용 자금 산식의 정확한 부호와 의미
- “90% mortgage” 조건과 100% MSG Foundation mortgage의 관계
- 신청 자격 심사가 파일럿 시스템 범위에 포함되는지 여부
- 보고서 출력 형식
- 금액 반올림 규칙
- 주 시작/종료 기준일

## Suggested Next Step

구현 단계로 넘어가기 전 다음 결정을 확정해야 한다.

1. 구현 플랫폼: CLI / Web / Desktop
2. 주간 가용 자금 산식
3. 금액 반올림 정책
4. 보고서 출력 형식
5. 신청 자격 심사 범위
