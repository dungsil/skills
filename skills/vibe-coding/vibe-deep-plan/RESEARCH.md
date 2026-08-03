# Research Tickets

A `research` ticket delegates only **read-only investigation** to a background subagent. The subagent may inspect primary sources — official documentation, source code, specifications, first-party APIs, and local resources — but it must not write files, create a branch or artifact, update the map or a ticket, make a write-capable call, or use a credential.

Its job:

1. Investigate the ticket question against **primary sources**, following every substantive claim to the source that owns it.
2. Return source-cited findings, known constraints, and remaining unknowns to the calling session; do not persist a report.
3. If the findings reveal account creation, permission changes, data movement, or credential use, classify each category and return the exact human checklist needed for it.

## The caller resolves it

The calling session — never the research subagent — owns the ticket lifecycle and tracker writes:

1. Claim the selected research ticket before starting its investigation.
2. Check that the returned result answers the ticket as fully as available: key conclusions, primary-source evidence for each, known constraints, and remaining unknowns. Continue investigating instead of posting a partial answer.
3. Preserve the complete result inside that ticket: use its resolution comment on a hosted tracker or `## Answer` on the local tracker. If one hosted-tracker comment cannot hold it, split it into clearly ordered consecutive comments such as `Part 1/N` through `Part N/N`; keep every part on the same ticket.
4. After the complete answer or final part is posted, close the ticket immediately and append only its linked title plus a one-line gist to the map's **Decisions so far**. Never copy the detailed findings into the map.

Do not create a separate research document, artifact, or branch merely to preserve findings. The only exception is when a research document is itself an explicit destination deliverable under its own ticket contract.

The investigation never performs an external side effect. If resolving the selected ticket requires account creation, permission changes, data movement, or credential use, the calling session must follow the skill's human-attended approval rules; refusal, ambiguity, or no response leaves the ticket open and blocked.

Never record a credential value in a map, ticket, comment, resolution text, command or output log, or research artifact. Retain only the credential type and safe-store reference.
