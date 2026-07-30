# Research Subagents

How a `research` ticket gets resolved. Research is the one **AFK** ticket type — the agent drives it alone, so several can run in parallel while the human is busy elsewhere.

Spin up a **background agent** per ticket, so the map keeps moving while it reads.

Its job:

1. Investigate the question against **primary sources** — official docs, source code, specs, first-party APIs — not a secondary write-up of them. Follow every claim back to the source that owns it.
2. Write the findings to a single Markdown file, citing each claim's source.
3. Save it where the repo already keeps such notes; match the existing convention, and if there is none, put it somewhere sensible and say where.

## Wiring it back to the map

A research ticket is only resolved once its findings are reachable from the map:

- Capture the findings on a throwaway `research/<name>` branch, and leave a context pointer on the ticket that names where the file lives.
- Post the answer as the ticket's **resolution comment**, close the ticket, then append the one-line gist plus link to the map's **Decisions so far** — same as any other ticket.
- Research is the exception to "never resolve more than one ticket per session": several research tickets may be fired and resolved in parallel, because none of them needs the human.
