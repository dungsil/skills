# Logic Prototype

A tiny interactive terminal app that lets the user drive a state model by hand. Use this when the question is about **business logic, state transitions, or data shape** — the kind of thing that looks reasonable on paper but only feels wrong once you push it through real cases.

Every artifact belongs in `.agents/prototype/<name>/`: the pure logic, TUI, README and its run command, dependency manifests and lockfiles, fixtures, and any scratch persistence. The prototype may inspect production source read-only for context, but must never import production modules or modify production source, root manifests or task runners, production configuration, databases, authentication, routes, or shared components. Use prototype-local dependencies, in-memory or local scratch data, and stand-ins instead.

## When this is the right shape

- "I'm not sure if this state machine handles the edge case where X then Y."
- "Does this data model actually let me represent the case where..."
- "I want to feel out what the API should look like before writing it."
- Anything where the user wants to **press buttons and watch state change**.

If the question is "what should this look like" — wrong branch. Use [PROTOTYPE-UI.md](PROTOTYPE-UI.md).

## Process

### 1. State the question

Before writing code, write down what state model and what question you're prototyping. One paragraph, in `.agents/prototype/<name>/README.md` or a comment at the top of a prototype-local file. A logic prototype that answers the wrong question is pure waste — make the question explicit so it can be checked later, whether the user is watching now or returning to it AFK.

### 2. Pick the language
Reuse the host project's language and runtime when practical. If the project has no obvious runtime (e.g. a docs repo), ask.

Keep every dependency manifest and lockfile inside `.agents/prototype/<name>/`; do not modify a root manifest or task runner, or add a new runtime merely for the prototype.

### 3. Isolate the logic in a portable module

Put the actual logic — the bit that's answering the question — behind a small, pure interface inside `.agents/prototype/<name>/`. It should be readable as a reference for a separate later production implementation. The TUI around it is throwaway; the logic module and TUI must remain separate.

The right shape depends on the question:

- **A pure reducer** — `(state, action) => state`. Good when actions are discrete events and state is a single value.
- **A state machine** — explicit states and transitions. Good when "which actions are even legal right now" is part of the question.
- **A small set of pure functions** over a plain data type. Good when there's no implicit current state — just transformations.
- **A class or module with a clear method surface** when the logic genuinely owns ongoing internal state.

Pick whichever shape best fits the question being asked, *not* whichever is easiest to wire to a TUI. Keep it pure: no I/O, terminal code, `console.log` for control flow, production-module imports, or live authentication, data, database, or configuration mutations. The prototype-local TUI imports the prototype-local logic and calls into it; nothing flows the other direction.

This is what makes the prototype useful past its own lifetime: the validated reducer / machine / function set records a decision and reference for a later production implementation. Do not lift, copy, or merge it into production source while resolving the prototype.

### 4. Build the smallest TUI that exposes the state

Keep the TUI in `.agents/prototype/<name>/` beside the logic. It may use only prototype-local dependencies, fixtures, scratch data, and stand-ins.

Build it as a **lightweight TUI** — on every tick, clear the screen (`console.clear()` / `print("\033[2J\033[H")` / equivalent) and re-render the whole frame. The user should always see one stable view, not an ever-growing scrollback.

Each frame has two parts, in this order:

1. **Current state**, pretty-printed and diff-friendly (one field per line, or formatted JSON). Use **bold** for field names or section headers and **dim** for less important context (timestamps, IDs, derived values). Native ANSI escape codes are fine — `\x1b[1m` bold, `\x1b[2m` dim, `\x1b[0m` reset. No need to pull in a styling library unless one is already in the project.
2. **Keyboard shortcuts**, listed at the bottom: `[a] add user  [d] delete user  [t] tick clock  [q] quit`. Bold the key, dim the description, or vice-versa — whatever reads cleanly.

Behaviour:

1. **Initialise state** — a single in-memory object/struct, or clearly disposable prototype-local scratch data when persistence itself is the question. Render the first frame on start.
2. **Read one keystroke (or one line)** at a time, dispatch it through the local pure interface, and replace state with the result.
3. **Re-render** the full frame after every action — don't append, replace.
4. **Loop until quit.**

The whole frame should fit on one screen.

### 5. Make it runnable in one local command

Document exactly one copy/paste command in `.agents/prototype/<name>/README.md`. It must start the prototype from its local code and, where used, its local manifest and dependencies — for example, `pnpm --dir .agents/prototype/<name> start`.

Reuse the host runtime when practical, but do not add a root task-runner script or edit a root manifest. The one-command handoff stays prototype-local.

### 6. Hand it over

Give the user that exact prototype-local run command. They'll drive it themselves; the interesting moments are when they say "wait, that shouldn't be possible" or "huh, I assumed X would be different" — those are the bugs in the *idea*, which is the whole point. If they want new actions added, add them. Prototypes evolve.

### 7. Capture the answer and the prototype

Once the prototype has answered its question, capture the answer and prototype the way [PROTOTYPE.md](PROTOTYPE.md) describes: record the verdict and question in the ticket's resolution comment, close the prototype ticket, and preserve the prototype as a primary source. The validated logic is a decision/reference for a separate later production implementation, not code to lift into production during prototype resolution.

## Anti-patterns

- **Don't add tests.** A prototype that needs tests is no longer a prototype.
- **Don't connect it to production services.** Never change or use the production database, configuration, authentication, or data mutations. Use an in-memory store unless persistence is the question; then use only a clearly disposable scratch file or store under `.agents/prototype/<name>/`.
- **Don't generalise.** No "what if we wanted to support X later." The prototype answers one question.
- **Don't blur the logic and the TUI together.** If the reducer / state machine references `console.log`, prompts, terminal escape codes, or production modules, it's no longer portable. Keep the TUI as a thin shell over a pure prototype-local module.
- **Don't ship the TUI shell or lift its logic into production.** The shell is optimised for being driven by hand from a terminal; the validated logic is evidence for a later, separate production implementation.
