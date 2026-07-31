---
name: vibe-implement
description: Implements a piece of work from a spec or set of tickets, test-first at pre-agreed seams, then reviews and commits it. Use when the user asks to implement, build, or ship a spec, ticket, or plan that is already agreed.
metadata:
  disable-model-invocation: "true"
---

# Implementing Work

Implement the work described by the user in the spec or tickets.

Before touching anything, record the current commit (`git rev-parse HEAD`) — that is the **fixed point** the review will run against.

## Workspace isolation

If the project uses git and has at least one commit (`git rev-parse HEAD` succeeds), isolate the work in a dedicated worktree instead of editing the main checkout:

1. Name the branch `vibe/{number}-{function}` — `{number}` is the next sequential number among existing `vibe/*` branches (start at `1`), `{function}` is a short kebab-case slug of the feature (e.g. `vibe/3-user-auth`).
2. Create it: `git worktree add .agents/worktrees/{number}-{function} -b vibe/{number}-{function}` (branch off the fixed point). Make sure `.agents/worktrees/` is git-ignored.
3. Do all implementation, testing, and review inside that worktree.
4. When the work is committed and reviewed, merge the branch back into the original branch from the main checkout, then clean up: `git worktree remove .agents/worktrees/{number}-{function}`.

If the repo has no commits yet (or the project isn't a git repo), skip isolation and work in place.

Build test-first at pre-agreed seams, following [TDD.md](TDD.md) — the red → green loop, seam discipline, and the anti-patterns to avoid.

Reference docs, read as needed:

- [TDD.md](TDD.md) — the red → green loop and its rules
- [tests.md](tests.md) — what a good test looks like, with examples
- [mocking.md](mocking.md) — when to mock and what to mock instead
- [MERGE-CONFLICTS.md](MERGE-CONFLICTS.md) — resolving an in-progress merge or rebase conflict

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, run `/vibe-review` against the fixed point you recorded at the start, and pass it the spec or ticket you implemented so both review axes have their source. Don't ask the user for a fixed point; you already know it.

Commit your work to the task branch, merge it back as described above. If landing it runs into an in-progress merge or rebase conflict, resolve it following [MERGE-CONFLICTS.md](MERGE-CONFLICTS.md).
