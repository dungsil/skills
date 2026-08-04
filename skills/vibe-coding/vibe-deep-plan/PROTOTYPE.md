# Prototype Tickets

How a `유형:프로토타입` ticket gets resolved. Prototype is a **HITL** type — the artifact exists to give the human something concrete to react to, so it only resolves through that live exchange.

A prototype is **throwaway code that answers a question**. The question decides the shape.

## Pick a branch

Identify which question is being answered — from the user's prompt, the surrounding code, or by asking if the user is around:

- **"Does this logic / state model feel right?"** → [PROTOTYPE-LOGIC.md](PROTOTYPE-LOGIC.md). Build a tiny interactive terminal app that pushes the state machine through cases that are hard to reason about on paper.
- **"What should this look like?"** → [PROTOTYPE-UI.md](PROTOTYPE-UI.md). Generate several radically different UI variations on a prototype-local route/entrypoint under `.agents/prototype/<name>/`, switchable via a URL search param and a floating bottom bar.

The two branches produce very different artifacts — getting this wrong wastes the whole prototype. If the question is genuinely ambiguous and the user isn't reachable, default to whichever branch better matches the surrounding code (a backend module → logic; a page or component → UI) and state the assumption at the top of the prototype.

## Rules that apply to both

1. **Isolate it before code.** Create `.agents/prototype/<name>/` before writing code. Keep every artifact there: source, dependency manifests/lockfiles, fixtures, scratch data, route config, and run instructions. Production source may be read only for context; it must never be imported.
2. **No production impact.** Never modify or import production source/modules, edit root manifests, task runners, routes, configs, or shared components, or use live authentication or mutate live data. Use prototype-local dependencies and local copies or stand-ins.
3. **One documented command to run.** Document one command inside `.agents/prototype/<name>/` that starts the prototype from that directory without relying on project-level changes.
4. **No persistence by default.** State lives in memory. If the question explicitly involves persistence, use a clearly named, wipeable scratch DB or local file inside `.agents/prototype/<name>/`.
5. **Skip the polish.** No tests, no error handling beyond what makes the prototype _runnable_, no abstractions. The point is to learn something fast.
6. **Surface the state.** After every action (logic) or on every variant switch (UI), print or render the full relevant state so the user can see what changed.
7. **Record, don't implement.** After the live HITL exchange validates a decision, capture the complete prototype directory as a **primary source** on a throwaway branch, out of main, and record the verdict. Do not implement it in production; that is a separate later ticket.

## Wiring it back to the map

The prototype is an **asset**, not the answer. Link it from the ticket; never paste it into the body.

- Commit `.agents/prototype/<name>/` as a **primary source** to a throwaway branch, out of main, and leave a context pointer to that branch on the ticket.
- Post the verdict — which shape won and why — as the ticket's **resolution comment**, then close the ticket and append the one-line gist plus link to the map's **Decisions so far**. Do not fold the prototype into production during this resolution; production implementation is a separate later ticket.
- The map records the decision; the branch keeps the prototype as a primary source.
