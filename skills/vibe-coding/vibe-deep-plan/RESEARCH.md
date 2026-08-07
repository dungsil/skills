# Research Tickets

A research ticket has two actors: an **AFK investigation** that only reads and returns source-cited findings, and a caller that owns every write, persistence record, pointer, and resolution.

## AFK investigation

- Investigate the ticket question using primary sources where available—official docs, source, specs, and public read-only APIs—and cite each substantive conclusion.
- It may inspect local read-only resources and return conclusions, constraints, unknowns, and any safe human checklist needed for an external action.
- It must not write files, branches, commits, artifacts, maps, tickets, or comments; use credentials; push or publish; or cause any external side effect. It never resolves a ticket or speaks for the human.

## Caller persistence

The caller preserves the complete returned findings before the charting or work session exits. Persistence is tracker-specific.

### Local Markdown

The research ticket and its canonical findings record are one file:

```
.agents/plans/<effort>/research/<ticket-stem>.md
```

The file carries the ticket's `Type: 조사`, `Status`, question, and complete source-cited findings under `## Research`. Append the final decision under `## Answer`. Human-led interview records use the separate `.agents/plans/<effort>/interviews/<ticket-stem>.md` directory.

Do not create a separate `.agents/research/` note or pointer comment for Local Markdown. Leave the repository's ignore policy unchanged. Do not create, checkout, commit, or push a research-only branch; this record is the local handoff artifact, not a delivery change.

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

When charting starts AFK research, use the configured tracker’s claim operation. For Local Markdown, set the record to `Status: claimed` before dispatch, wait for the result, and persist it using the local rule above; after the record is written, set it back to `Status: open`. For hosted trackers, assign or claim the issue before dispatch, wait for the result, persist it using the hosted rule above, and release only this session’s assignment or claim after the record and pointer are present. If persistence fails or the session hands off unfinished work, keep the Local Markdown record `Status: claimed` or keep the hosted issue assigned and open, and report the persistence failure.

Charting leaves the ticket open: for Local Markdown, a successfully persisted research record is `Status: open`; for hosted trackers, the issue remains open after its claim is released. Charting does not add a map gist. If persistence fails, keep the Local Markdown record `Status: claimed` or keep the hosted issue assigned and open so the result is not lost.

In the next session, inspect the canonical local research record or hosted comment/artifact before researching again. If it belongs to this ticket, reuse it and repair a missing hosted pointer when possible. Rerun read-only research only when the canonical record is unavailable, unusable, or belongs to another ticket.

## Resolution and authority

Once a decision is made, record `Decision: <decision>` and finish the tracker-specific resolution. For Local Markdown, add the canonical research-record path in the final `## Answer`, then set the record to `Status: resolved`. For hosted trackers, add the hosted research-record URL in the resolution comment or note, then close the issue when the tracker has a close operation. Finally, add only a one-line gist to the map. A research record or pointer is not itself a resolution.

There is no research branch to push or publish. Any external comment or durable artifact must still follow the configured tracker workflow and approval rules.

Never store credential values in findings, branches, tickets, maps, comments, commands, or logs. Record only a credential type and safe-store reference.
