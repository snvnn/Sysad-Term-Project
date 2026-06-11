# 05. Current State, Constraints, and Assumptions

## 1. Current State

현재 확인된 기존 시스템은 없다. 요구사항 PDF는 MSG Foundation 이사회가 컴퓨터 시스템 도입을 검토하고 있으며, 소프트웨어 개발 조직에 파일럿 프로젝트를 위임한다고 설명한다.

## 2. Business Constraints

| ID | Constraint | Impact |
|---|---|---|
| CON-BIZ-001 | 파일럿 프로젝트 비용을 가능한 낮게 유지해야 한다. | 최소 기능과 단순 구조 우선. |
| CON-BIZ-002 | 주간 자금 계산에 필요한 데이터만 포함해야 한다. | 신청 심사/문서 관리/외부 연동은 기본 범위 제외. |
| CON-BIZ-003 | 수탁자들이 컴퓨터 경험이 없다. | 보고서와 UI는 이해하기 쉬워야 한다. |
| CON-BIZ-004 | 재단 자금 사용 판단은 정확성과 추적성이 중요하다. | 계산 snapshot, 감사 로그 필요. |

## 3. Technical Constraints

| ID | Constraint | Impact |
|---|---|---|
| CON-TECH-001 | 과제 현재 단계는 분석/설계만 수행한다. | 구현/배포/DB 변경 금지. |
| CON-TECH-002 | 요구사항에 플랫폼이 명시되지 않았다. | 설계는 CLI/Web/Desktop 중 선택 가능한 구조로 작성. |
| CON-TECH-003 | 외부 금융/은행/보험 API가 명시되지 않았다. | 수동 입력 기반 파일럿으로 설계. |
| CON-TECH-004 | 금액 계산 정밀도가 중요하다. | 구현 단계에서 decimal fixed-point 필요. |

## 4. Assumptions

| ID | Assumption | Rationale | Validation |
|---|---|---|---|
| ASM-001 | 파일럿 시스템의 핵심은 주간 자금 계산과 보고서 생성이다. | PDF가 데이터 범위를 세 유형으로 제한. | 교수자/사용자 확인. |
| ASM-002 | 신청 자격 조건은 문서화하되 기본 파일럿 구현 범위에서는 제외한다. | 파일럿 데이터 목록에 결혼/고용/중간가/저축 데이터 없음. | 사용자 확인. |
| ASM-003 | 보고서는 우선 화면 또는 Markdown/CSV 출력으로 충분하다. | 출력 매체 미명시. | 제출 형식 확인. |
| ASM-004 | 운영 비용 추정치는 시점별 이력을 남긴다. | 분기별 갱신 언급. | 도메인 담당자 확인. |
| ASM-005 | 모기지 데이터는 활성 모기지만 주간 계산에 포함한다. | 종료/취소 상태는 계산 대상 아님. | 설계 리뷰. |

## 5. Risks

| ID | Risk | Severity | Mitigation |
|---|---|---:|---|
| RSK-001 | 핵심 산식이 모호하여 계산 결과가 잘못될 수 있다. | High | 산식 후보를 명시하고 Needs Verification으로 관리. |
| RSK-002 | 90% 모기지 조건 범위가 불명확하다. | Medium | 자격 심사 모듈을 future scope로 분리. |
| RSK-003 | 금융 금액 반올림 규칙 미정으로 테스트가 흔들릴 수 있다. | Medium | decimal 정책과 rounding policy를 설계 문서에 분리. |
| RSK-004 | 사용자가 컴퓨터에 익숙하지 않아 UI가 과할 수 있다. | Medium | 보고서 중심의 단순 플로우 설계. |
| RSK-005 | 파일럿 범위가 커지면 비용 제약을 위반할 수 있다. | High | 데이터 범위와 비목표를 엄격히 유지. |

## 6. Evidence

- `docs/00-source/requirements-extracted.md`
- `docs/01-initiation/problem-definition.md`
- `docs/01-initiation/scope-statement.md`
