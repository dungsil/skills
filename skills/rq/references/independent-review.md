# Independent Review

Every quality gate requires independent subagent review before the final report.

## Assignment

1. Split the request into independent source requirements before dispatch. Keep every criterion and evidence-domain gate under its parent requirement.
2. Dispatch exactly one subagent per independent source requirement, in parallel when there are multiple requirements.
3. Each reviewer owns scope, evidence, and verdict verification for all criteria and gate items derived from its assigned requirement.
4. Do not create extra reviewers when one requirement splits into `CODE`, `OPERATION`, `DEPLOYMENT`, `DATA`, or other gates. Do not create separate scope, evidence, verdict, or adversarial reviewers unless the user explicitly requests another review pass.

One independent source requirement means one reviewer. Review tier and gate count change review depth, not reviewer count or topology.

## Review Packet

Give each reviewer only the material relevant to its assigned item:

- source requirement and requested review scope
- extracted criteria with evidence domains and `In scope` values
- implementation evidence and test or execution results
- separate-gate relationship and current-gate impact
- draft criterion and gate statuses
- known ambiguities and limits

## Reviewer Result

Each reviewer returns:

- `PASS` or `CHANGES_REQUIRED`
- scope result: criteria belong to the source requirement and requested scope
- evidence result: every judgment has domain-appropriate primary evidence
- verdict result: statuses, severity, aggregation, and separate-gate impact are correct
- exact supporting evidence or rule
- objections, required changes, and unresolved ambiguity or limitation

For `HEAVY`, the same reviewer also attempts a plausible counterexample or alternate interpretation that could change its assigned item's outcome.

## Completion

The main agent resolves every finding, records accepted and rejected objections with reasons, updates the draft, and recalculates affected statuses. A reviewer call without item-level results or resolution is incomplete.

Independent review is complete when every independent source requirement has one reviewer result and all disagreements are resolved. If a required subagent is unavailable, mark that requirement's independent verification incomplete; a self-review cannot satisfy the independent-review requirement.

## Optional Post-report Adversarial Verification

Run this only when the user explicitly requests adversarial verification after the initial report.

1. Use the existing report, original requirement, and cited evidence as the review packet.
2. Dispatch one new adversarial subagent per independent source requirement, in parallel. The verifier covers every criterion and gate item derived from that requirement.
3. Each verifier tries to falsify its assigned requirement's result by finding:
   - an omitted, invented, or mis-scoped obligation
   - a positive judgment without domain-appropriate primary evidence
   - a test or execution claim substituted for implementation evidence
   - an excluded or separate-gate result contaminating the current status
   - a plausible counterexample or alternate interpretation that changes the outcome
4. Return `UPHELD` or `CHANGES_REQUIRED`, exact evidence, the attempted counterexample, and the affected criteria or status.
5. The main agent resolves findings once. Do not start another adversarial round unless the user requests it.

Return an addendum containing the item assignments, findings, resolutions, and whether the original verdict remains valid. Rewrite the full report only when requested.
