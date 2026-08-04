# Research Tickets

A `research` ticket delegates only **read-only investigation** to a background subagent. The subagent may inspect primary sources — official documentation, source code, specifications, first-party APIs, and local resources — but it must not write files, create a branch or artifact, update the map or a ticket, make a write-capable call, or use a credential.

Its job:

1. Investigate the ticket question against **primary sources**, following every substantive claim to the source that owns it.
2. Return source-cited findings, known constraints, and remaining unknowns to the calling session; the subagent never persists a report itself.
3. If the findings reveal account creation, permission changes, data movement, or credential use, classify each category and return the exact human checklist needed for it.

## The caller owns persistence and resolution

The calling session — never the research subagent — owns attachments, ticket lifecycle, and tracker writes:

1. When charting launches research, temporarily claim each ticket before dispatch, wait for every launched investigation, and attach every returned result before exit. Release the temporary claim after a successful attachment; if work is still live or cannot return, leave the ticket open and claimed for an explicit handoff rather than losing or duplicating it.
2. Attach the complete returned result to that same ticket as a clearly marked **non-resolution research update**: use a normal comment or note on a hosted tracker, or `## Comments` on the local tracker. Include conclusions, primary-source evidence, known constraints, and remaining unknowns. Split oversized hosted updates into ordered `Part 1/N` through `Part N/N` comments on the same ticket. The attachment does not close or resolve the ticket and never updates the map.
3. When a session selects the ticket for resolution, read its existing comments and attachments before starting new research. Reuse findings that already answer the question; do not repeat an investigation merely because another session attached the result.
4. Check whether the findings remain complete and current for their source, version, date, scope, and assumptions. Preserve older findings as history; mark contradicted or materially changed claims stale or superseded, then investigate and attach only the missing or changed evidence. A partial update may be attached, but it must not be presented as a resolution.
5. Resolve only from a complete, current answer. On a hosted tracker, post a final resolution comment that identifies the complete attached findings; on the local tracker, record the final answer under `## Answer`. Then close the ticket and append only its linked title plus a one-line gist to the map's **Decisions so far**. Never copy the detailed findings into the map.

Do not create a separate research document, artifact, or branch merely to preserve findings. The only exception is when a research document is itself an explicit destination deliverable under its own ticket contract.

The investigation never performs an external side effect. Safe findings and checklists may be attached while the ticket remains open, but account creation, permission changes, data movement, and credential use still require the skill's human-attended approval rules. Refusal, ambiguity, or no response leaves the ticket open and blocked.

Never record a credential value in a map, ticket, comment, resolution text, command or output log, or research artifact. Retain only the credential type and safe-store reference.
