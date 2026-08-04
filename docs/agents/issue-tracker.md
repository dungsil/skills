# Issue tracker: Local Markdown

Issues and specs for this repo live as Markdown files in `.agents/plans/`. Detailed research lifecycle rules belong in `RESEARCH.md`; this file records only the local persistence pointer convention.

## Conventions

- One feature per directory: `.agents/plans/<feature-slug>/`
- The spec is `.agents/plans/<feature-slug>/spec.md`
- Implementation issues are one file per ticket at `.agents/plans/<feature-slug>/issues/<NN>-<slug>.md`, numbered from `01`
- Triage state is recorded as a `Status:` line near the top of each work item
- Comments and conversation history append under a `## Comments` heading. Research-ticket entries point to the canonical local note and never contain detailed findings.

## Research-note persistence

- The caller writes complete, source-cited findings to `.agents/research/<effort>/<ticket-stem>.md`, where `<effort>` is the map directory name and `<ticket-stem>` is the child-ticket filename without `.md`.
- Keep `.agents/` ignored and unchanged. Do not create, checkout, commit, or push a research-only branch; the note is a local handoff artifact.
- Research `## Comments` entries contain only `Research: .agents/research/<effort>/<ticket-stem>.md`. The final `## Answer` repeats that path with the decision.
- Keep `Status: claimed` until the note and pointer are written. During charting, leave the ticket open and do not add a map gist. Before rerunning, inspect the canonical note first.

## Publishing and fetching

- When a skill says **publish to the issue tracker**, create a file under `.agents/plans/<feature-slug>/`.
- When a skill says **fetch the relevant ticket**, read the referenced file.

## Wayfinding operations

- **Map**: `.agents/plans/<effort>/map.md`
- **Child ticket**: `.agents/plans/<effort>/issues/NN-<slug>.md` with `Type:` and `Status:` lines
- **Blocking**: `Blocked by: NN, NN`; a ticket is unblocked when each listed file is resolved
- **Frontier**: open, unblocked, unclaimed files in numeric order
- **Claim**: set `Status: claimed` and save before any work. Local Markdown has no `Assignee:` field; do not serialize hosted-tracker assignee metadata.
- **Resolve**: append `## Answer`, set `Status: resolved`, then add only a linked ticket title and one-line gist to the map's Decisions-so-far.
