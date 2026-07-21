# Independent Review

Every quality gate requires independent subagent review before the final report.

## Assignment

1. Split the source requirement into independent requirement or gate items before dispatch.
2. Dispatch exactly one subagent per item, in parallel when there are multiple items.
3. Each reviewer owns scope, evidence, and verdict verification end to end for its assigned item.
4. Do not create separate scope, evidence, verdict, or adversarial reviewers. Add another review pass only when the user explicitly requests it.

One independent item means one reviewer. Review tier changes review depth, not reviewer count or topology.

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

Independent review is complete when every independent item has one reviewer result and all disagreements are resolved. If a required subagent is unavailable, mark that item's independent verification incomplete; a self-review cannot satisfy the independent-review requirement.

