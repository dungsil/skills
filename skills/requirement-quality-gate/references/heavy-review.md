# HEAVY Independent Review

Use this process only for `HEAVY` reviews. `LIGHT` reviews may skip it.

## Review Packet

Before finalizing the report, give an independent reviewer:

- original source requirement and requested review scope
- extracted criteria with evidence domains and `In scope` values
- implementation evidence table and test or execution results
- draft gate statuses
- known ambiguities and limits

## Adversarial Review

Require findings for all three dimensions:

- **Scope:** no criterion exceeds the source requirement or requested scope; code and operational gates are not mixed; independent obligations are split; no hidden criterion was added.
- **Evidence:** every positive judgment has domain-appropriate evidence; tests do not replace implementation; missing out-of-scope evidence is not treated as a defect; capability and execution are distinct.
- **Verdict:** each status reflects only its gate; limitations are not blockers; separate-gate results do not contaminate the current gate; ambiguity is not mislabeled as a defect.

Resolve every disagreement before reporting. Accept or reject it with a reason, apply accepted changes, and recalculate affected statuses. Calling a reviewer without incorporating or explicitly resolving its findings is not completion.

## Fallback

If an independent reviewer or subagent is unavailable, state that independent review was not performed and answer these questions with evidence and any resulting draft change:

1. Does each criterion belong to the requested scope?
2. Is each concern an implementation defect or only missing operational evidence?
3. Would removing an excluded item still leave the requested code behavior fully verified?
4. Are different evidence domains being combined into one status?
5. Does every `WARNING` or `FAIL` identify an actual in-scope implementation problem?
