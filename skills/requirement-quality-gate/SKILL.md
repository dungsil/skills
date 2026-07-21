---
name: requirement-quality-gate
description: Verifies whether source code, diffs, tests, runtime evidence, or implementation artifacts satisfy stated product or engineering requirements. Separates implementation gates from operation, deployment, data, and manual gates. Use for requirement quality gates, acceptance-criteria checks, implementation verification reports, or change-to-requirement mapping.
---

# Requirement Quality Gate

Use this skill to turn stated requirements into concise, evidence-backed gate reports. Verify evidence; do not implement fixes unless the user separately asks for changes.

## Scope Model

Before deriving acceptance criteria, classify every source-stated obligation by the thing being judged:

| Evidence domain | Obligation |
|---|---|
| `CODE` | Source code, API, policy, batch, or persistence behavior is implemented |
| `TEST` | A required automated test or verification artifact exists or produced the stated result |
| `MIGRATION` | A migration or backfill capability is implemented |
| `OPERATION` | A command or job ran in the target environment |
| `DEPLOYMENT` | A deployment or release completed |
| `DATA` | Target or production data reached the required state |
| `MANUAL` | A person confirmed behavior through a browser, admin UI, or manual QA |

- The user's requested review scope determines included and excluded domains. Evidence availability never expands scope.
- Code, implementation, PR, and change reviews default to `CODE`; include `MIGRATION` when migration or backfill implementation is required. Inspect `TEST` evidence as needed, but create a `TEST` criterion only when the source requirement explicitly requires a test or test result.
- Include `OPERATION`, `DEPLOYMENT`, or `DATA` in the current gate only when the user explicitly requests those outcomes. Include `MANUAL` only when manual confirmation is requested.
- Classify the obligation, not its supporting artifact. A test may support a `CODE` or `MIGRATION` criterion without replacing implementation evidence or creating a `TEST` criterion.
- If obligations require different completion evidence or execution actors, evaluate them as independent gate items. A user-requested implementation review may present `CODE`, supporting `TEST`, and `MIGRATION` gate items under one current implementation scope, but their criteria and statuses must remain visible. Always separate `OPERATION`, `DEPLOYMENT`, and `DATA` from implementation gates; never aggregate their statuses.
- Distinguish capability from execution: an implemented CLI or job is `CODE`/`MIGRATION`; running it is `OPERATION`; changed target data is `DATA`.

Record the coarse evidence boundary separately as `CODE`, `RUNTIME`, or `MIXED` based on the evidence sources actually inspected. This boundary describes evidence availability; it does not select domains or alter gate scope.

## Workflow

1. **Partition the request:** separate the source requirement, requested review scope, supplied evidence, and limits. Only the source requirement can create acceptance criteria.
2. **Build the gate plan:** apply the scope model, list included and excluded domains, and create a gate item for every independent obligation before deriving criteria. `CODE`/`TEST`/`MIGRATION` items may share a user-requested implementation scope; operational, deployment, and data items may not.
3. **Derive criteria:** phrase source-stated required behavior, not files, migrations, tests, logs, commands, or evidence that may prove it. For every criterion record `ID`, `Criterion`, `Evidence domain`, `In scope`, `Evidence`, `Status`, and `Impact on overall status`.
   - Out-of-scope source obligations may remain as traceability rows, but must use `In scope=false`, `OUT_OF_SCOPE` or `SEPARATE_GATE`, and impact `none`.
   - Review instructions, supplied evidence, likely safeguards, and recommendations cannot add criteria.
4. **Choose the review tier:**
   - `LIGHT`: narrow UI, copy, validation, or local behavior. Independent review is optional.
   - `HEAVY`: security, auth, permissions, persistence, transactions, concurrency, cache invalidation, external integrations, broad cross-layer changes, or user-requested rigor. Independent review is required before the final report.
5. **Gather evidence:** inspect user-provided artifacts first, then the current branch against the likely base, working-tree/staged/untracked changes, and finally relevant existing source found by requirement terms.
6. **Map evidence:** prefer service, controller, use case, adapter, repository, endpoint, handler, policy, validator, migration, and integration code over DTOs, generated output, docs, or superficial name matches.
   - A positive `CODE` or `MIGRATION` judgment requires source or diff implementation evidence. Tests and command results are supporting evidence, not substitutes.
   - Other gates require primary evidence for their domain: test source/results for `TEST`, execution records for `OPERATION`, release records for `DEPLOYMENT`, observed state for `DATA`, and recorded human observation for `MANUAL`.
7. **Draft each gate verdict:** map every in-scope criterion, calculate that gate's status only, and list excluded obligations under separate gates.
8. **Review `HEAVY` drafts independently:** follow the process below, resolve every disagreement, update the draft, and recalculate status.
9. **Report:** use [references/report-template.md](references/report-template.md) for a full report; a shorter report must preserve the same gate, scope, separate-gate, and independent-review facts.

## Status Aggregation

Use criterion statuses `satisfied`, `satisfied with risk`, `not satisfied`, `unknown`, `OUT_OF_SCOPE`, `SEPARATE_GATE`, or `not applicable`.

```text
overall status =
  aggregate(status of acceptance criteria where in_scope = true)
```

Apply these rules in order:

1. `ERROR`: required inputs could not be processed.
2. `NO_CHANGES_FOUND`: an implementation review found no relevant implementation or change evidence.
3. `FAIL`: any required in-scope criterion is clearly `not satisfied`.
4. `NEEDS_REVIEW`: no criterion clearly fails, but an essential in-scope criterion is `unknown` because mapping or required evidence is insufficient.
5. `WARNING`: no criterion fails or remains essentially unknown, but an in-scope criterion is `satisfied with risk` due to a real non-blocking implementation or verification risk.
6. `PASS`: all required in-scope criteria are `satisfied`.

Exclude from aggregation: `In scope=false`, `OUT_OF_SCOPE`, `SEPARATE_GATE`, `not applicable`, unrequested operation/deployment/data confirmation, future recommended tests, documentation recommendations, and non-blocking specification ambiguity. A separate gate's `NEEDS_REVIEW` never lowers the current gate.

Use `WARNING` only for current-scope risks such as an important untested branch, uncertain error path, unclear boundary behavior, or high-risk logic that available in-scope evidence cannot fully verify. Missing operation logs during a code review, an undeployed PR, unchanged production data during implementation review, and a recommendation for future tests are not warning reasons. If tests are explicitly required, a missing test is an in-scope `TEST` failure; otherwise tests affect status only when their absence creates a concrete current-scope risk.

When the user asks whether a mixed requirement is wholly complete, report every gate separately. You may state that full completion is not yet verified, but do not overwrite a code gate's `PASS` with another gate's `NEEDS_REVIEW`.

## Report Value Localization

Keep canonical values for internal reasoning and aggregation. In a Korean report, output only the Korean display values below unless the user explicitly asks for canonical tokens.

| Category | Canonical values | Korean display values |
|---|---|---|
| Overall status | `PASS`, `WARNING`, `FAIL`, `NEEDS_REVIEW`, `NO_CHANGES_FOUND`, `ERROR` | `통과`, `경고`, `실패`, `검토 필요`, `관련 변경 없음`, `오류` |
| Review tier | `LIGHT`, `HEAVY` | `경량 검토`, `심층 검토` |
| Evidence boundary | `CODE`, `RUNTIME`, `MIXED` | `코드 증거`, `실행 증거`, `혼합 증거` |
| Evidence domain | `CODE`, `TEST`, `MIGRATION`, `OPERATION`, `DEPLOYMENT`, `DATA`, `MANUAL` | `코드`, `테스트`, `마이그레이션`, `운영 실행`, `배포`, `데이터`, `수동 검증` |
| Criterion status | `satisfied`, `satisfied with risk`, `not satisfied`, `unknown`, `OUT_OF_SCOPE`, `SEPARATE_GATE`, `not applicable` | `충족`, `위험 동반 충족`, `미충족`, `확인 불가`, `범위 밖`, `별도 게이트`, `해당 없음` |
| Scope and impact | `true`, `false`, `included`, `none` | `포함`, `제외`, `종합 판정 반영`, `영향 없음` |
| Boolean and severity | `yes`, `no`; `critical`, `high`, `medium`, `low`, `none` | `예`, `아니요`; `치명적`, `높음`, `중간`, `낮음`, `없음` |

Translate gate types, mapping statuses, table values, and section labels consistently with the user's language. Do not mix Korean display values with canonical tokens in the same report.

## HEAVY Independent Review

Before finalizing a `HEAVY` report, give an independent reviewer this packet:

- original source requirement and requested review scope
- extracted criteria with evidence domains and `In scope` values
- implementation evidence table and test or execution results
- draft gate statuses
- known ambiguities and limits

Require an adversarial review of:

- **Scope:** no criterion exceeds the source requirement or requested scope; code and operational gates are not mixed; independent obligations are split; no hidden criterion was added.
- **Evidence:** every positive judgment has domain-appropriate evidence; tests do not replace implementation; missing out-of-scope evidence is not treated as a defect; capability and execution are distinct.
- **Verdict:** each status reflects only its gate; limitations are not blockers; separate-gate results do not contaminate the current gate; ambiguity is not mislabeled as a defect.

Resolve every disagreement before reporting. Accept or reject it with a reason, apply accepted changes, and recalculate affected statuses. Calling a reviewer without incorporating or explicitly resolving its findings is not completion.

If an independent reviewer or subagent is unavailable, perform a structured self-challenge and state in the report that independent review was not performed. Answer with evidence and any resulting draft change:

1. Does each criterion belong to the requested scope?
2. Is each concern an implementation defect or only missing operational evidence?
3. Would removing an excluded item still leave the requested code behavior fully verified?
4. Are different evidence domains being combined into one status?
5. Does every `WARNING` or `FAIL` identify an actual in-scope implementation problem?

## Evidence and Ambiguity Rules

- Do not invent requirements, source evidence, tests, command output, browser behavior, deployment, or data state.
- Tests complement implementation evidence; they never replace it.
- Every positive judgment must cite evidence appropriate to its domain.
- Missing out-of-scope evidence is a limitation or separate-gate concern, not a defect in the current gate.
- Prefer explicit priorities and concrete examples when they resolve conflicting prose. If implementation matches the resolved example, the gate may `PASS`; record the wording conflict as a non-blocking limitation or specification recommendation. Use `NEEDS_REVIEW` or `WARNING` only when plausible interpretations change the evaluated outcome.
- Keep unrelated changed files visible as unmapped changes or limitations; do not force them into the requirement.

## Verification Checklist

Before reporting:

- Every source obligation has a domain and gate; independent domains or actors are split.
- Every criterion traces to the source requirement and has all required fields.
- Only `In scope=true` criteria affect the current status.
- Every positive `CODE`/`MIGRATION` judgment has implementation evidence; tests are supplemental.
- Separate gates show their own status and impact `none` on the current gate.
- Missing or unrun verification is visible without becoming a hidden criterion.
- `HEAVY` independent review results, disagreements, resolutions, and resulting changes are recorded; unavailable review is disclosed with the self-challenge result.
- Mapping relevance remains separate from correctness judgment.
- Final status, severity, limits, and recommendations use the user's language.
