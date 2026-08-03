# Research Subagents

A `research` ticket is AFK only for **read-only investigation**. A background subagent may inspect primary sources — official documentation, source code, specifications, first-party APIs, and local resources — but it must not write files, create a branch or artifact, update the map or a ticket, make a write-capable call, or use a credential.

Its job:

1. Investigate the question against **primary sources**, following every substantive claim to the source that owns it.
2. Return source-cited, read-only findings and remaining unknowns to its caller; do not persist a Markdown report.
3. If the findings reveal account creation, permission changes, data movement, or credential use, classify that work as human-attended and identify the affected category or categories.

## Hand it back to the map

An AFK research result is not a ticket resolution. It must not create a findings file, branch, context pointer, comment, or map update; it must not close the ticket. The caller receives the findings plus a numbered, target-specific human checklist naming the exact action, expected impact and scope, reversibility or rollback path, and any credential type with its safe-store reference.

Only a human-attended caller may act on those findings. Immediately before executing account creation, permission changes, data movement, or credential use, it previews the target, exact action, expected impact and scope, and reversibility, then obtains a separate affirmative approval for that category. A plan-level or blanket approval is insufficient. On refusal, ambiguity, or no response, leave the ticket open and blocked and cause no external side effect.

Never record a credential value in a map, ticket, comment, resolution text, command or output log, or research artifact. Retain only the credential type and safe-store reference.
