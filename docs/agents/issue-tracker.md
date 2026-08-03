# Issue tracker: Local Markdown

Issues and specs for this repo live as Markdown files in `.agents/plans/` and are intentionally not tracked by Git.

## Conventions

- One feature per directory: `.agents/plans/<feature-slug>/`
- The spec is `.agents/plans/<feature-slug>/spec.md`
- Implementation issues are one file per ticket at `.agents/plans/<feature-slug>/issues/<NN>-<slug>.md`, numbered from `01`
- Triage state is recorded as a `Status:` line near the top of each work item
- Comments and conversation history append under a `## Comments` heading

## Publishing and fetching

- When a skill says **publish to the issue tracker**, create a file under `.agents/plans/<feature-slug>/`.
- When a skill says **fetch the relevant ticket**, read the referenced file.

## Wayfinding operations

- **Map**: `.agents/plans/<effort>/map.md`
- **Child ticket**: `.agents/plans/<effort>/issues/NN-<slug>.md` with `Type:` and `Status:` lines
- **Blocking**: `Blocked by: NN, NN`; a ticket is unblocked when each listed file is resolved
- **Frontier**: open, unblocked, unclaimed files in numeric order
- **Claim**: set `Status: claimed` before work
- **Resolve**: append `## Answer`, set `Status: resolved`, then add a gist and link to the map's Decisions-so-far
