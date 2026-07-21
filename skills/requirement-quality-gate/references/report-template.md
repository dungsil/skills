# Requirement Quality Gate Report Template

Return only the Markdown report unless the user asks for additional commentary. Use the user's language for display text and prose. Keep acceptance criteria within the selected evidence boundary; list operational completion claims under external validation instead.

# Requirement Quality Gate Report

## 1. Overall Decision

| Item | Value |
|---|---|
| Status | `<PASS | WARNING | FAIL | NEEDS_REVIEW | NO_CHANGES_FOUND | ERROR>` |
| Review tier | `<LIGHT | HEAVY>` |
| Judgment | `<one-sentence final judgment>` |
| Evidence boundary | `<CODE | RUNTIME | MIXED>` |
| Success criteria | `<short summary>` |
| Evidence coverage | `<performed and missing evidence scenarios>` |

## 2. Reviewed Requirement

> `<source requirement only; exclude review scope, supplied evidence, and limits>`

| Item | Details |
|---|---|
| Review scope | `<artifacts to inspect and requested evidence boundary>` |
| Supplied evidence | `<user-stated files, tests, outputs, or none>` |
| Input limits | `<unavailable runtime or operational evidence, or none>` |
| Acceptance criteria within boundary | `<AC-1: source-stated required behavior; never files, tests, logs, or evidence-only statements>` |
| External validation items | `<deployment, target database execution, manual operation, live external state, or none>` |
| Constraints and non-goals | `<constraints, non-goals, or none>` |
| Ambiguities | `<ambiguities or none>` |

## 3. Implementation Mapping

| Item | Value |
|---|---|
| Mapping confidence | `<0.00-1.00>` |
| Relevant implementation files | `<file list>` |
| Relevant tests or verification files | `<test or verification files, or none>` |
| Mapping status | `<satisfied | not satisfied | partially satisfied | unknown | not applicable>` |
| Mapping rationale | `<why these files/evidence are relevant>` |

## 4. Criteria Results

Only source-derived acceptance criteria belong in this table. Do not add implementation evidence or external validation as criterion rows.

| Acceptance criterion | Status | Severity | Implementation evidence | Judgment | Recommendation |
|---|---|---|---|---|---|
| `<criterion>` | `<satisfied | not satisfied | partially satisfied | unknown | not applicable>` | `<critical | high | medium | low | none>` | `<evidence>` | `<judgment>` | `<recommendation>` |

## 5. Test and Verification Evidence

| Type | Item | Result |
|---|---|---|
| Relevant test | `<test file or command>` | `<verified behavior>` |
| Missing test | `<missing test>` | `<unverified behavior>` |
| Executed check | `<command or manual check>` | `<result>` |

## 6. Risks and Recommended Actions

### Risks

| Severity | Risk | Related files |
|---|---|---|
| `<critical | high | medium | low>` | `<risk>` | `<files>` |

### Recommended Actions

| Priority | Action | Expected effect |
|---:|---|---|
| `<1>` | `<action>` | `<effect>` |

## 7. Scope and Limits

| Item | Value |
|---|---|
| Change scope | `<user scope | branch diff | working tree | untracked files | searched source | none>` |
| Comparison method | `<merge-base diff | direct diff | staged | unstaged | mixed | provided diff | none>` |
| Reviewed files | `<files>` |
| Excluded files | `<files and reasons>` |
| External validation items | `<items not decidable from the reviewed evidence, or none>` |
| Limits | `<limits or none>` |
