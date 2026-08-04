# Research Tickets

A research ticket has two actors: an **AFK investigation** that only reads and returns source-cited findings, and a caller that owns every write, persistence record, pointer, and resolution.

## AFK investigation

- Investigate the ticket question using primary sources where available—official docs, source, specs, and public read-only APIs—and cite each substantive conclusion.
- It may inspect local read-only resources and return conclusions, constraints, unknowns, and any safe human checklist needed for an external action.
- It must not write files, branches, commits, artifacts, maps, tickets, or comments; use credentials; push or publish; or cause any external side effect. It never resolves a ticket or speaks for the human.

## Caller persistence

The caller preserves the complete returned findings before the charting or work session exits. Persistence is tracker-specific.

### Local Markdown

The caller writes one canonical Markdown findings note using stable map and ticket keys:

```
.agents/research/<map-key>/<ticket-key>.md
```

For Local Markdown, `<map-key>` is the `.agents/plans/<effort>/` directory name and `<ticket-key>` is the ticket filename stem. Keep the complete findings and source citations in this note. Do not put detailed findings in the local ticket comments; add only a path pointer:

```
Research: .agents/research/<map-key>/<ticket-key>.md
```

Keep the repository's `.agents/` ignore policy unchanged. Do not create, checkout, commit, or push a research-only branch. This note is the local handoff artifact, not a delivery change.

### Hosted or other external issue tracker

The caller stores the complete findings on the same research ticket, by default as one dedicated issue comment or note. Start the record with a stable marker so a later session can find it even when the title changes:

```
<!-- vibe-deep-plan research: <map-identity>/<ticket-identity> -->
```

If the provider's comment limit is too small, preserve the full result in ordered comments (`Research record 1/N`, `2/N`, and so on). If comments cannot hold it, use a tracker-native or repository-owned snippet, wiki page, attachment, or equivalent durable artifact and put its URL in a ticket comment. Never truncate the findings or leave them only in a local, unpushed file. Record the durable location as:

```
Research record: <comment, note, or artifact URL>
```

Hosted persistence has no `Branch`, `Commit`, or `Path` pointer, and it never creates a `research/...` branch. If no durable external surface is available, keep the ticket claimed and report the persistence failure instead of discarding or resolving the result.

## Charting and reuse

When charting starts AFK research, claim the ticket, wait for its result, persist it using the matching tracker rule above, and record the local path or hosted record URL. Release the claim only after both the findings record and its pointer are present.

Charting leaves the ticket open: it does not resolve it or add a map gist. If either persistence step fails, keep the ticket claimed so the result is not lost.

In the next session, inspect the canonical local note or hosted comment/artifact before researching again. If it belongs to this ticket, reuse it and repair a missing pointer when possible. Rerun read-only research only when the canonical record is unavailable, unusable, or belongs to another ticket.

## Resolution and authority

Once a decision is made, record `Decision: <decision>` and the local note path or hosted research-record URL in the final `## Answer` or resolution comment, close the ticket, and add only a one-line gist to the map. A research record or pointer is not itself a resolution.

There is no research branch to push or publish. Any external comment or durable artifact must still follow the configured tracker workflow and approval rules.

Never store credential values in findings, branches, tickets, maps, comments, commands, or logs. Record only a credential type and safe-store reference.
