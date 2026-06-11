# Source Requirements Extract — The MSG Foundation Case Study

- Source file: `TermProject_The_MSG_Foundation_1.pdf`
- Extracted on: 2026-06-11
- Purpose: 요구사항 분석과 설계를 위한 원문 기반 요약/근거 문서

## Case Summary

Martha Stockton Greengage는 23억 달러의 전 재산을 자선 단체에 기부했고, 이 유언에 따라 젊은 부부가 주택을 구입할 수 있도록 저비용 대출을 제공하는 MSG(Martha Stockton Greengage) Foundation이 설립된다.

MSG 재단 이사회는 운영 비용을 줄이기 위해 컴퓨터 시스템 도입을 검토한다. 파일럿 프로젝트의 목표는 매주 주택 구입에 사용할 수 있는 금액을 결정하는 데 필요한 계산을 수행하는 소프트웨어 제품을 만드는 것이다.

## Mortgage Background

일반 주택 구매자는 구매가의 일부를 보증금으로 지불하고 나머지를 모기지로 대출받는다. 모기지는 부동산을 담보로 하는 대출이다.

예시:

- 주택 가격: `$100,000`
- 보증금: `10% = $10,000`
- 대출 원금: `$90,000`
- 조건: 30년, 연 7.5%, 월 0.625%
- 월 P&I: `$629.30`

P&I는 원금과 이자를 포함한 월별 지불액이다. 초기에는 이자 비중이 크고 시간이 지날수록 원금 상환 비중이 커진다.

## Standard Mortgage Lender Concerns

금융 회사는 대출 상환을 보장하기 위해 다음을 요구한다.

1. 모기지 증서 서명
   - 상환 불이행 시 주택을 매각하여 대출 잔액을 회수할 수 있음.
2. 주택 보험 가입
   - 보험료는 일반적으로 에스크로 계좌에 월별 적립 후 연 1회 납부.
3. 부동산세 납부
   - 부동산세 역시 에스크로 계좌에 월별 적립 후 연 1회 납부.
4. 상환 능력 확인
   - 총 월 지불액(P&I + 보험 + 부동산세)이 총소득의 28%를 초과하면 일반적으로 모기지를 받을 수 없음.
5. 선지급 비용
   - 보통 원금의 2% points 요구.
   - Closing costs와 points를 합하면 예시상 약 `$7,000`.

## MSG Foundation Grant Eligibility Conditions

MSG 재단은 다음 조건이 만족될 때 주택 구입을 위해 100% 모기지를 부여한다.

1. 부부가 결혼한 지 최소 1년 이상 10년 미만이다.
2. 남편과 아내 모두 유급 고용 상태다.
   - 두 사람 모두 전년도 최소 48주 동안 정규직 고용 증거를 제공해야 한다.
3. 주택 가격이 지난 12개월 동안 해당 지역의 주택 공시 중간 가격보다 낮다.
4. 고정금리 30년 모기지 조건에서 다음 중 하나 이상에 해당한다.
   - 90% 모기지에 대한 할부금 합산이 총소득의 28%를 초과한다.
   - 주택 비용의 10% + `$7,000`을 지불할 충분한 저축이 없다.
5. 재단이 주택을 구입하기에 충분한 자금을 가지고 있다.

## Weekly Payment and Grant Rule

신청이 승인되면 30년 동안 부부가 MSG 재단에 매주 지불해야 하는 금액은 다음의 합이다.

- 고정 P&I 지불액
- 에스크로 지불액: `(연간 부동산세 + 연간 주택 소유자 보험료) / 52`

이 총액이 부부의 주당 총수입의 28%보다 크면, MSG 재단은 차액을 보조금 형태로 지불한다. 따라서 모기지는 매주 전액 지불되지만, 부부는 합산 총소득의 28% 이상을 지불하지 않는다.

부부는 매년 소득세 신고서 사본을 제출해야 하며, 현재 총소득 증거로 급여 명세서 사본을 제출할 수 있다. 따라서 부부가 실제로 지불해야 하는 금액은 주마다 달라질 수 있다.

## Weekly Funds Availability Algorithm

MSG 재단은 모기지 신청 승인 가능 자금을 다음 알고리즘으로 결정한다.

1. 매주 초 투자에서 예상되는 연간 투자 소득을 계산하고 52로 나눈다.
2. 예상 연간 MSG Foundation 운영 비용을 52로 나눈다.
3. 해당 주의 총 예상 모기지 상환액을 계산한다.
4. 해당 주의 총 예상 교부금(grants)을 계산한다.
5. 주의 시작에 사용 가능한 금액을 위 항목 1~4를 이용해 계산한다.
6. 주중에 주택 비용이 모기지 금액 이하이면, MSG 재단은 해당 주택 구입 자금이 있는 것으로 간주한다. 그 주에 사용할 수 있는 모기지 금액은 해당 주택 비용만큼 줄어든다.
7. 매주 말에 MSG 재단 투자 고문은 사용하지 않은 자금을 투자한다.

> Needs Verification: 원문 5번은 “위 항목 1, 2, 3, 4 이다”라고 되어 있어 정확한 산식이 명확하지 않다. 이후 요구사항 분석 단계에서 사용 가능 금액 산식을 확정해야 한다.

## Pilot Project Data Scope

파일럿 프로젝트 비용을 낮게 유지하기 위해 주간 자금 계산에 필요한 데이터 항목만 소프트웨어 제품에 포함한다.

### Investment Data

- Item number
- Item name
- Estimated annual return
  - 새로운 정보가 들어오면 갱신됨
  - 보통 연 4회 갱신
- Date estimated annual return was last updated

### Operating Expense Data

- Estimated annual operating expenses
  - 연 4회 결정
- Date estimated annual operating expenses were last updated

### Mortgage Data

- Account number
- Last name of mortgagees
- Original purchase price of home
- Date mortgage was issued
- Weekly principal and interest payment
- Current combined gross weekly income
- Date combined gross weekly income was last updated
- Annual real-estate tax
- Date annual real-estate tax was last updated
- Annual homeowner’s insurance premium
- Date annual homeowner’s insurance premium was last updated

## Required Reports

MSG 관리자와의 추가 논의에서 다음 세 가지 보고서가 필요하다고 식별되었다.

1. Weekly funds computation results
2. Listing of all investments — 요청 시 출력
3. Listing of all mortgages — 요청 시 출력
