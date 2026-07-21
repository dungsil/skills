# Independent Review

Every quality gate requires independent subagent review before the final report.

## Tier Requirements

- `LIGHT`: one independent reviewer may verify scope, evidence, and verdict together.
- `HEAVY`: run the two-wave procedure below. One reviewer cannot cover multiple first-wave roles.

## Review Packet

Give every reviewer:

- original source requirement and requested review scope
- extracted criteria with evidence domains and `In scope` values
- implementation evidence table and test or execution results
- separate gates and their current-gate impact
- draft criterion and gate statuses
- known ambiguities and limits

## HEAVY Wave 1: Specialist Reviewers

Dispatch three independent subagents in parallel:

1. **Scope reviewer:** verify every criterion and separate gate belongs to the source requirement and requested scope; detect hidden criteria, mixed execution actors, and code/operation contamination.
2. **Evidence reviewer:** verify every criterion has domain-appropriate primary evidence; detect tests replacing implementation evidence, unsupported positive judgments, and out-of-scope evidence treated as a defect.
3. **Verdict reviewer:** verify every criterion status, aggregate calculation, severity, separate-gate impact, and ambiguity treatment; detect limitations mislabeled as blockers or unrelated gate results contaminating the current status.

Each specialist returns, for every reviewed item:

- `PASS` or `CHANGES_REQUIRED`
- exact evidence or rule supporting the result
- objections and required changes
- unresolved ambiguity or limitation

The main agent resolves every finding, records accepted and rejected objections with reasons, updates the draft, and recalculates affected statuses. A reviewer call without item-level results or resolution is incomplete.

## HEAVY Wave 2: Adversarial Verification

After Wave 1 resolution, dispatch a fourth subagent that did not draft the report or perform a specialist review. Give it the original packet, all specialist findings, resolutions, and the revised report.

The adversarial verifier must assume the revised verdict is wrong and try to falsify it by:

- finding a source obligation omitted, invented, or assigned to the wrong gate
- finding a positive criterion without primary implementation or domain evidence
- finding tests or execution claims used as substitutes for implementation
- finding an out-of-scope or separate-gate result that changes the current gate
- constructing a plausible counterexample or alternate interpretation that changes the outcome
- checking that every accepted change was applied and every status was recalculated

If it returns `CHANGES_REQUIRED`, revise the report, rerun every affected specialist reviewer, then rerun adversarial verification. Repeat until the adversarial verifier returns `PASS` with no unresolved blocking objection.

## Completion

- `LIGHT` is complete after the independent reviewer result and all disagreements are resolved.
- `HEAVY` is complete only after all three specialist results, their resolutions, any required specialist reruns, and a final adversarial `PASS` are recorded.
- If any required subagent is unavailable, mark independent verification incomplete. A structured self-challenge may document risks but is not independent review and cannot satisfy the completion rule.

