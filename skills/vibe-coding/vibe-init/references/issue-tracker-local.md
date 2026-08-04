# Issue tracker: Local Markdown

Issues and specs (you may know a spec as a PRD) for this repo live as markdown files in `.agents/plans/`.

## Conventions

- One feature per directory: `.agents/plans/<feature-slug>/`
- The spec is `.agents/plans/<feature-slug>/spec.md`
- Implementation issues are one file per ticket at `.agents/plans/<feature-slug>/issues/<NN>-<slug>.md`, numbered from `01` — never a single combined tickets file
- Triage state is recorded as a `Status:` line near the top of each issue file (see `triage-labels.md` for the role strings)
- Comments and conversation history append to the bottom of the file under a `## Comments` heading. Research-ticket comments contain only a pointer to the canonical note, never detailed findings.

## Research-note persistence

- The caller writes the complete, source-cited findings to `.agents/research/<effort>/<ticket-stem>.md`; `<effort>` is the map directory name and `<ticket-stem>` is the ticket filename without `.md`.
- Keep `.agents/` ignored and unchanged. Do not create, checkout, commit, or push a research-only branch. The note is a local handoff artifact.
- Add only `Research: .agents/research/<effort>/<ticket-stem>.md` to `## Comments` while charting, and repeat the path in the final `## Answer` with the decision.
- Keep the research ticket open and omit the map gist during charting. If the note or pointer write fails, keep `Status: claimed` so the findings are not lost. Before rerunning research, inspect the canonical note first.

## When a skill says "publish to the issue tracker"

Create a new file under `.agents/plans/<feature-slug>/` (creating the directory if needed).

## When a skill says "fetch the relevant ticket"

Read the file at the referenced path. The user will normally pass the path or the issue number directly.

## Wayfinding operations

Used by `/vibe-deep-plan`. The **map** is a file with one **child** file per ticket.

- **Map**: `.agents/plans/<effort>/map.md` — the Notes / Decisions-so-far / Fog body.
- **Child ticket**: `.agents/plans/<effort>/issues/NN-<slug>.md`, numbered from `01`, with the question in the body. A `Type:` line records the ticket type (`조사`/`프로토타입`/`인터뷰`/`작업`); a `Status:` line records `claimed`/`resolved`.
- **Blocking**: a `Blocked by: NN, NN` line near the top. A ticket is unblocked when every file it lists is `resolved`.
- **Frontier**: scan `.agents/plans/<effort>/issues/` for files that are open, unblocked, and unclaimed; first by number wins.
- **Claim**: set `Status: claimed` and save before any work. Local Markdown has no `Assignee:` field; do not serialize hosted-tracker assignee metadata.
- **Resolve**: append the answer under an `## Answer` heading, set `Status: resolved`, then append a context pointer (gist + link) to the map's Decisions-so-far in `map.md`.
