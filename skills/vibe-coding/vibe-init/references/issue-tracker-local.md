# Issue tracker: Local Markdown

Issues and specs (you may know a spec as a PRD) for this repo live as Markdown files in `.agents/plans/`.

## Conventions

- One feature per directory: `.agents/plans/<feature-slug>/`
- The spec is `.agents/plans/<feature-slug>/spec.md`
- Implementation issues are one file per ticket under `.agents/plans/<feature-slug>/issues/<NN>-<slug>.md`, numbered from `01`; `/vibe-plan` owns this directory.
- Deep-plan decision records are one file per ticket in a type-specific directory:
  - `research/<NN>-<slug>.md` for `유형:조사` (AFK research)
  - `interviews/<NN>-<slug>.md` for `유형:인터뷰` (HITL conversation)
  - `prototypes/<NN>-<slug>.md` for `유형:프로토타입` (throwaway artifact)
  - `tasks/<NN>-<slug>.md` for `유형:작업` (manual prerequisite)
- Deep-plan record identity is its actual relative file path, such as `research/03-compare-providers.md` or `interviews/04-confirm-scope.md`; the numeric prefix is only an ordering key. Blocking references must include the type directory and `.md` filename, and `issues/` is reserved for implementation tickets.
- Every deep-plan record has a `Type:` line and a `Status:` line near the top. Triage states apply to implementation tickets published by `/vibe-plan`.
- Deep-plan records start with `Status: open`; claiming changes it to `Status: claimed`; releasing a completed charting handoff changes it back to `Status: open`; only a final answer changes it to `Status: resolved`. An unfinished handoff or failed persistence stays `claimed`.
- Comments and conversation history append to the bottom of the record under a `## Comments` heading. Research findings stay in the research record, not in an implementation issue.

## Version control and completion updates

- `.agents/plans/` is tracked in git; the scratch paths (`.agents/worktrees/`, `.agents/prototype/`) stay ignored. Ticket files are therefore **branch content** — a ticket's checklist state is whatever the branch you are reading says it is.
- An implementation ticket's acceptance checkboxes and completion comment are checked off **on the feature branch that implements it**, shipping with implementation code in one commit. Never a tracker-only commit, and never a commit on the target branch ahead of the code: merging is a human decision, and the merge must carry code and checklist together or neither. Intermediate red→green commits are fine — put the ticket edits in the still-uncommitted implementation commit, or amend the unpublished branch tip when the code is already committed.
- The completion comment names the branch, the reviewed commit SHA, and the verification evidence.
- `상태:` is not a completion field. It records triage routing and stays unchanged when work completes; all boxes checked in merged history is the completion record.

## Research-note persistence

- For Local Markdown, the research ticket and canonical findings record are the same `.agents/plans/<effort>/research/<ticket-stem>.md` file. Keep complete source-cited findings under `## Research` and the final decision under `## Answer`.
- Leave the repository's ignore policy unchanged. Do not create, checkout, commit, or push a research-only branch; the record is a handoff artifact on the current branch, not a delivery change.
- Set a research record to `Status: claimed` before starting investigation. After complete findings are written, set it back to `Status: open` during charting; if persistence fails or the session hands off unfinished work, keep it `claimed`. Set it to `Status: resolved` only with the final decision under `## Answer`. Before rerunning research, inspect the canonical record first.

## When a skill says "publish to the issue tracker"

- `/vibe-plan` Stage 2 spec publish writes `.agents/plans/<feature-slug>/spec.md`; when the input is a cleared Local Markdown map, reuse that map's existing `.agents/plans/<effort>/` directory.
- `/vibe-plan` Stage 3 implementation publish writes `.agents/plans/<feature-slug>/issues/<NN>-<slug>.md`; for a cleared map or a spec at `.agents/plans/<effort>/spec.md`, use that same `<effort>/issues/` directory; any other local spec path uses the configured `.agents/plans/<feature-slug>/issues/` root.
- `/vibe-deep-plan` publishes decision records under the matching `research/`, `interviews/`, `prototypes/`, or `tasks/` directory.

## When a skill says "fetch the relevant ticket"

Read the referenced local planning artifact exactly as given: `map.md`, `spec.md`, a deep-plan decision record, or an implementation issue. Do not substitute another feature root. The user will normally pass the path directly.

## Wayfinding operations

Used by `/vibe-deep-plan`. The map is a file with one decision record per ticket.

- **Map**: `.agents/plans/<effort>/map.md` — the Notes / Decisions-so-far / Fog body.
- **Research record**: `.agents/plans/<effort>/research/NN-<slug>.md` with `Type: 조사` and `Status:` lines.
- **Interview record**: `.agents/plans/<effort>/interviews/NN-<slug>.md` with `Type: 인터뷰` and `Status:` lines.
- **Prototype record**: `.agents/plans/<effort>/prototypes/NN-<slug>.md` with `Type: 프로토타입`, `Status:`, and a pointer to `.agents/prototype/<name>/` when an artifact exists.
- **Task record**: `.agents/plans/<effort>/tasks/NN-<slug>.md` with `Type: 작업` and `Status:` lines.
- **Blocking**: `Blocked by: research/03-compare-providers.md, interviews/04-confirm-scope.md`; a record is unblocked when each listed record is resolved.
- **Frontier**: scan the union of `research/`, `interviews/`, `prototypes/`, and `tasks/` for records with `Status: open` and no open blockers; sort by numeric prefix, then canonical relative path.
- **Claim**: set `Status: claimed` and save before any work. Local Markdown has no `Assignee:` field; do not serialize hosted-tracker assignee metadata.
- **Resolve**: append the answer under an `## Answer` heading, set `Status: resolved`, then append only a linked record title and one-line gist to the map's Decisions-so-far in `map.md`.
