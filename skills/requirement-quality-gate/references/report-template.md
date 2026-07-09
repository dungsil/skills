# Requirement Quality Gate Report Template

Return only the Markdown report unless the user asks for additional commentary. Use the user's language for display text and prose.

# Requirement Quality Gate Report

## 1. Overall Decision

| Item | Value |
|---|---|
| Status | `<PASS | WARNING | FAIL | NEEDS_REVIEW | NO_CHANGES_FOUND | ERROR>` |
| Review tier | `<LIGHT | HEAVY>` |
| Judgment | `<one-sentence final judgment>` |
| Success criteria | `<short summary>` |
| Evidence coverage | `<performed and missing evidence scenarios>` |

## 2. Reviewed Requirement

> `<requirement text>`

| Item | Details |
|---|---|
| Acceptance criteria | `<AC-1: criterion; AC-2: criterion>` |
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
| Limits | `<limits or none>` |
