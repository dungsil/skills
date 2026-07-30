---
name: vibe-implement
description: Implements a piece of work from a spec or set of tickets, test-first at pre-agreed seams, then reviews and commits it. Use when the user asks to implement, build, or ship a spec, ticket, or plan that is already agreed.
metadata:
  disable-model-invocation: "true"
---

# Implementing Work

Implement the work described by the user in the spec or tickets.

Before touching anything, record the current commit (`git rev-parse HEAD`) — that is the **fixed point** the review will run against.

Build test-first at pre-agreed seams, following [TDD.md](TDD.md) — the red → green loop, seam discipline, and the anti-patterns to avoid.

Reference docs, read as needed:

- [TDD.md](TDD.md) — the red → green loop and its rules
- [tests.md](tests.md) — what a good test looks like, with examples
- [mocking.md](mocking.md) — when to mock and what to mock instead
- [MERGE-CONFLICTS.md](MERGE-CONFLICTS.md) — resolving an in-progress merge or rebase conflict

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, run `/vibe-review` against the fixed point you recorded at the start, and pass it the spec or ticket you implemented so both review axes have their source. Don't ask the user for a fixed point; you already know it.

Commit your work to the current branch. If landing it runs into an in-progress merge or rebase conflict, resolve it following [MERGE-CONFLICTS.md](MERGE-CONFLICTS.md).
