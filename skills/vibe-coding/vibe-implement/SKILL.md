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
4. Keep the task branch as the implementation source and leave the original branch unchanged. After it is committed and reviewed, follow the completion disposition below; never merge it automatically.

If the repo has no commits yet (or the project isn't a git repo), skip isolation and work in place.

Build test-first at pre-agreed seams, following [TDD.md](TDD.md) — the red → green loop, seam discipline, and the anti-patterns to avoid.

Reference docs, read as needed:

- [TDD.md](TDD.md) — the red → green loop and its rules
- [tests.md](tests.md) — what a good test looks like, with examples
- [mocking.md](mocking.md) — when to mock and what to mock instead
- [MERGE-CONFLICTS.md](MERGE-CONFLICTS.md) — resolving an in-progress merge or rebase conflict

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

## Completion disposition

1. Read `docs/agents/issue-tracker.md`.
2. From the task branch, run `/vibe-review` against the fixed point you recorded at the start, passing the implemented spec or ticket so both review axes have their source. Don't ask the user for a fixed point; you already know it. Commit the reviewed work to the task branch.
3. For a local Markdown tracker, confirm the unmerged task branch is retained before removing the worktree. Report the retained branch name.
4. For GitHub, push the task branch and create a pull request targeting the original branch. Remove the worktree only after publication succeeds, and report the pull request URL.
5. For GitLab, push the task branch and create a merge request targeting the original branch. Remove the worktree only after publication succeeds, and report the merge request URL.
6. For another hosted tracker that supports pull/merge requests, follow its documented workflow to publish the task branch targeting the original branch. Remove the worktree only after publication succeeds, and report the created request URL.

In every case, do not merge automatically; the original branch remains unchanged. If separately requested work encounters an in-progress merge or rebase conflict, resolve it following [MERGE-CONFLICTS.md](MERGE-CONFLICTS.md).
