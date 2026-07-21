# Requirement Quality Gate Report Template

Return only the Markdown report unless the user asks for additional commentary. Use the user's language for headings, labels, values, and prose. Before writing a Korean report, read [korean-report-values.md](korean-report-values.md) and do not expose internal canonical tokens. Report independent gates separately; never let a separate gate lower the current gate's status.

# Requirement Quality Gate Report

## 1. Overall Decision

| Item | Value |
|---|---|
| Status | `<통과 | 경고 | 실패 | 검토 필요 | 관련 변경 없음 | 오류>` |
| Review tier | `<경량 검토 | 심층 검토>` |
| Judgment | `<one-sentence judgment for this gate only>` |
| Evaluated scope | `<user-requested scope>` |
| Gate type | `<코드 구현 | 테스트 검증 | 마이그레이션 구현 | 운영 실행 | 배포 | 데이터 상태 | 수동 검증>` |
| Evidence boundary | `<코드 증거 | 실행 증거 | 혼합 증거; 검사한 증거 출처이며 게이트 범위가 아님>` |
| Included evidence domains | `<코드, 테스트, 마이그레이션, 운영 실행, 배포, 데이터, 수동 검증 중 해당 항목>` |
| Excluded evidence domains | `<excluded domains and why>` |
| Separate gates | `<게이트 이름과 상태 또는 없음>` |
| Independent review performed | `<예 | 아니요>` |
| Independent review result | `<반영한 변경 | 변경 없음 | 자체 반증 대체 | 해당 없음>` |
| Success criteria | `<short summary of in-scope criteria>` |
| Evidence coverage | `<performed and missing evidence scenarios>` |

## 2. Reviewed Requirement

> `<source requirement only; exclude review scope, supplied evidence, and limits>`

| Item | Details |
|---|---|
| Review scope | `<artifacts and outcomes the user requested>` |
| Supplied evidence | `<사용자가 제시한 파일, 테스트, 출력 또는 없음>` |
| Input limits | `<사용할 수 없는 증거 또는 없음>` |
| Current gate criteria | `<in-scope source-stated required behavior>` |
| Separate gate obligations | `<다른 게이트로 분리한 원 요구사항 또는 없음>` |
| Constraints and non-goals | `<제약과 비목표 또는 없음>` |
| Ambiguities | `<해소되거나 미해소된 모호성, 결과 영향 또는 없음>` |

## 3. Implementation Mapping

| Item | Value |
|---|---|
| Mapping confidence | `<0.00-1.00>` |
| Relevant implementation files | `<file list>` |
| Relevant tests or verification files | `<관련 테스트 또는 검증 파일, 없으면 없음>` |
| Mapping status | `<충족 | 미충족 | 부분 충족 | 확인 불가 | 해당 없음>` |
| Mapping rationale | `<why these files and evidence are relevant>` |

## 4. Criteria Results

모든 행은 원 요구사항에서 도출해야 한다. 증거만 설명하는 문장은 승인 기준이 아니다. 범위 밖 원 요구사항을 추적 목적으로 남길 때는 범위를 `제외`, 상태를 `범위 밖` 또는 `별도 게이트`, 종합 판정 영향을 `영향 없음`으로 표시한다.

```text
종합 상태 =
  범위가 "포함"인 승인 기준 상태만 집계
```

| ID | Criterion | Evidence domain | In scope | Status | Severity | Implementation or primary evidence | Overall status impact | Judgment | Recommendation |
|---|---|---|---|---|---|---|---|---|---|
| `AC-1` | `<required behavior>` | `<코드 | 테스트 | 마이그레이션 | 운영 실행 | 배포 | 데이터 | 수동 검증>` | `<포함 | 제외>` | `<충족 | 위험 동반 충족 | 미충족 | 확인 불가 | 범위 밖 | 별도 게이트 | 해당 없음>` | `<치명적 | 높음 | 중간 | 낮음 | 없음>` | `<evidence>` | `<종합 판정 반영 | 영향 없음>` | `<judgment>` | `<recommendation>` |

범위가 `제외`이거나 상태가 `범위 밖`, `별도 게이트`, `해당 없음`인 행은 현재 게이트 상태에 영향을 주지 않는다.

## 5. Test and Verification Evidence

| Type | Item | Result |
|---|---|---|
| Relevant test | `<test file or command>` | `<verified behavior>` |
| Missing verification | `<missing test or check>` | `<current-scope risk, separate-gate gap, or recommendation>` |
| Executed check | `<command or manual check>` | `<result>` |

## 6. Separate Gates

| Gate | Evidence domain | Status | Reason | Impact on current gate |
|---|---|---|---|---|
| `<gate>` | `<코드 | 테스트 | 마이그레이션 | 운영 실행 | 배포 | 데이터 | 수동 검증>` | `<통과 | 경고 | 실패 | 검토 필요 | 관련 변경 없음 | 오류>` | `<reason>` | `<영향 없음>` |

독립 게이트의 현재 게이트 영향에는 `영향 없음`을 사용한다. 해당 게이트의 증거 부재가 현재 게이트를 하향해서는 안 된다.

## 7. Independent Review

| Item | Result |
|---|---|
| Reviewers used | `<요구사항 또는 게이트 항목 → 서브에이전트 목록>` |
| Requirement assignments | `<각 리뷰어에게 할당한 독립 요구사항 또는 게이트 항목>` |
| Per-requirement results | `<각 항목의 범위·증거·판정 결과와 근거>` |
| Disagreements | `<이견 또는 없음>` |
| Resolution | `<수용·기각 근거와 반영한 변경>` |

독립 요구사항 또는 게이트 항목마다 서브에이전트 한 명을 할당한다. 각 리뷰어가 할당 항목의 범위·증거·판정을 함께 검증하며, 여러 항목은 병렬로 검토한다. 검토 유형은 깊이만 바꾸며 리뷰어 수나 구조를 바꾸지 않는다. 사용자가 추가 검증을 명시하지 않으면 역할별 리뷰어나 별도 적대 검증자를 추가하지 않는다.

## 8. Risks and Recommended Actions

### Risks

| Severity | Risk | Related criteria or files |
|---|---|---|
| `<치명적 | 높음 | 중간 | 낮음>` | `<in-scope risk>` | `<criteria or files>` |

### Recommended Actions

| Priority | Action | Expected effect |
|---:|---|---|
| `<1>` | `<action>` | `<effect>` |

## 9. Scope and Limits

| Item | Value |
|---|---|
| Change scope | `<사용자 지정 범위 | 브랜치 diff | 작업 트리 | 미추적 파일 | 검색한 소스 | 없음>` |
| Comparison method | `<merge-base 비교 | 직접 diff | 스테이징 변경 | 미스테이징 변경 | 혼합 | 제공된 diff | 없음>` |
| Reviewed files | `<files>` |
| Excluded files | `<files and reasons>` |
| External validation or separate gates | `<현재 게이트에서 판단할 수 없는 항목 또는 없음>` |
| Limits | `<제한사항 또는 없음>` |

## Optional Adversarial Verification Addendum

Include this section only when the user explicitly requests post-report adversarial verification. Return only this addendum unless the user requests a revised full report.

| Item | Result |
|---|---|
| Trigger | `<사용자 요청 문구>` |
| Assignments | `<요구사항 또는 게이트 항목 → 적대 검증자>` |
| Findings | `<항목별 유지 또는 변경 필요, 근거, 반례 시도>` |
| Resolution | `<수용·기각 근거와 상태 변경>` |
| Original verdict | `<유효 | 수정 필요>` |


