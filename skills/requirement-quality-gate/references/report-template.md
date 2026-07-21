# Requirement Quality Gate Report Template

Return only the Markdown report unless the user asks for additional commentary. Use the user's language for display text and prose. Report independent gates separately; never let a separate gate lower the current gate's status.

# Requirement Quality Gate Report

## 1. Overall Decision

| Item | Value |
|---|---|
| Status | `<PASS | WARNING | FAIL | NEEDS_REVIEW | NO_CHANGES_FOUND | ERROR>` |
| Review tier | `<LIGHT | HEAVY>` |
| Judgment | `<one-sentence judgment for this gate only>` |
| Evaluated scope | `<user-requested scope>` |
| Gate type | `<code implementation | test verification | migration implementation | operation execution | deployment | data state | manual verification>` |
| Evidence boundary | `<CODE | RUNTIME | MIXED; evidence sources inspected, not gate scope>` |
| Included evidence domains | `<CODE, TEST, MIGRATION, OPERATION, DEPLOYMENT, DATA, MANUAL as applicable>` |
| Excluded evidence domains | `<excluded domains and why>` |
| Separate gates | `<gate names and statuses, or none>` |
| Independent review performed | `<yes | no>` |
| Independent review result | `<accepted changes, no-change conclusion, fallback, or not applicable>` |
| Success criteria | `<short summary of in-scope criteria>` |
| Evidence coverage | `<performed and missing evidence scenarios>` |

## 2. Reviewed Requirement

> `<source requirement only; exclude review scope, supplied evidence, and limits>`

| Item | Details |
|---|---|
| Review scope | `<artifacts and outcomes the user requested>` |
| Supplied evidence | `<user-stated files, tests, outputs, or none>` |
| Input limits | `<unavailable evidence or none>` |
| Current gate criteria | `<in-scope source-stated required behavior>` |
| Separate gate obligations | `<source-stated obligations assigned to other gates, or none>` |
| Constraints and non-goals | `<constraints, non-goals, or none>` |
| Ambiguities | `<resolved and unresolved ambiguities, their outcome impact, or none>` |

## 3. Implementation Mapping

| Item | Value |
|---|---|
| Mapping confidence | `<0.00-1.00>` |
| Relevant implementation files | `<file list>` |
| Relevant tests or verification files | `<test or verification files, or none>` |
| Mapping status | `<satisfied | not satisfied | partially satisfied | unknown | not applicable>` |
| Mapping rationale | `<why these files and evidence are relevant>` |

## 4. Criteria Results

Every row must derive from the source requirement. Evidence-only statements never become criteria. Out-of-scope source obligations may remain for traceability only when marked `In scope=false`, `OUT_OF_SCOPE` or `SEPARATE_GATE`, and impact `none`.

```text
overall status =
  aggregate(status of acceptance criteria where in_scope = true)
```

| ID | Criterion | Evidence domain | In scope | Status | Severity | Implementation or primary evidence | Overall status impact | Judgment | Recommendation |
|---|---|---|---|---|---|---|---|---|---|
| `AC-1` | `<required behavior>` | `<CODE | TEST | MIGRATION | OPERATION | DEPLOYMENT | DATA | MANUAL>` | `<true | false>` | `<satisfied | satisfied with risk | not satisfied | unknown | OUT_OF_SCOPE | SEPARATE_GATE | not applicable>` | `<critical | high | medium | low | none>` | `<evidence>` | `<included | none>` | `<judgment>` | `<recommendation>` |

Rows with `In scope=false`, `OUT_OF_SCOPE`, `SEPARATE_GATE`, or `not applicable` never affect the current gate's status.

## 5. Test and Verification Evidence

| Type | Item | Result |
|---|---|---|
| Relevant test | `<test file or command>` | `<verified behavior>` |
| Missing verification | `<missing test or check>` | `<current-scope risk, separate-gate gap, or recommendation>` |
| Executed check | `<command or manual check>` | `<result>` |

## 6. Separate Gates

| Gate | Evidence domain | Status | Reason | Impact on current gate |
|---|---|---|---|---|
| `<gate>` | `<CODE | TEST | MIGRATION | OPERATION | DEPLOYMENT | DATA | MANUAL>` | `<PASS | WARNING | FAIL | NEEDS_REVIEW | NO_CHANGES_FOUND | ERROR>` | `<reason>` | `<none>` |

Use `none` when the row belongs to an independent gate. Missing evidence for that gate must not lower the current gate.

## 7. Independent Review

| Item | Result |
|---|---|
| Reviewer used | `<yes | no>` |
| Scope review | `<result>` |
| Evidence review | `<result>` |
| Verdict review | `<result>` |
| Disagreements | `<issues or none>` |
| Resolution | `<accepted/rejected findings, reasons, and resulting draft changes>` |

For `HEAVY`, record the independent reviewer or the structured self-challenge fallback. A reviewer call without findings resolution is incomplete.

## 8. Risks and Recommended Actions

### Risks

| Severity | Risk | Related criteria or files |
|---|---|---|
| `<critical | high | medium | low>` | `<in-scope risk>` | `<criteria or files>` |

### Recommended Actions

| Priority | Action | Expected effect |
|---:|---|---|
| `<1>` | `<action>` | `<effect>` |

## 9. Scope and Limits

| Item | Value |
|---|---|
| Change scope | `<user scope | branch diff | working tree | untracked files | searched source | none>` |
| Comparison method | `<merge-base diff | direct diff | staged | unstaged | mixed | provided diff | none>` |
| Reviewed files | `<files>` |
| Excluded files | `<files and reasons>` |
| External validation or separate gates | `<items not decidable in this gate, or none>` |
| Limits | `<limits or none>` |

