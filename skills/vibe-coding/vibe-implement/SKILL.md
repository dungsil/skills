---
name: vibe-implement
description: Implements a piece of work from a spec or set of tickets, test-first at pre-agreed seams, then reviews and commits it. Use when the user asks to implement, build, or ship a spec, ticket, or plan that is already agreed.
metadata:
  disable-model-invocation: "true"
---

# Implementing Work

Implement the work described by the user in the spec or tickets.

## Completion ownership

At invocation, establish and record exactly one mode:

- **Caller-owned** — only when the caller explicitly says it owns integration and final landing. `/vibe-goal` invokes ticket work in this mode.
- **Standalone** — a direct `/vibe-implement` workflow that owns tracker-directed branch retention or publication and, only in a later or resumed action after independent landing proof, tracker-change handling. It never owns final landing.

If the completion mode is unclear, stop and ask before creating or changing a workspace.

Before touching anything, record the current commit (`git rev-parse HEAD`) — that is the **fixed point** the review will run against.

## Workspace isolation

In caller-owned mode, use only the ticket branch and worktree supplied by the caller; never create or use an integration workspace. If no caller-supplied ticket worktree is in use and the project uses git with at least one commit (`git rev-parse HEAD` succeeds), isolate the work in a dedicated worktree instead of editing the main checkout:

1. Name the branch `vibe/{number}-{function}` — `{number}` is the next sequential number among existing `vibe/*` branches (start at `1`), `{function}` is a short kebab-case slug of the feature (e.g. `vibe/3-user-auth`).
2. Create it: `git worktree add .agents/worktrees/{number}-{function} -b vibe/{number}-{function}` (branch off the fixed point). Make sure `.agents/worktrees/` is git-ignored.
3. Do all implementation, testing, and review inside that worktree.
Keep the task branch as the implementation source and leave the original branch unchanged. A caller-owned run works only in its supplied ticket branch and worktree: never modify the integration workspace or original target checkout, publish or merge the ticket branch, or clean, delete, or otherwise alter the caller-owned worktree after returning. After the reviewed work is committed, follow the completion disposition below; never merge automatically.

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
2. From the task branch, run `/vibe-review` against the fixed point recorded at the start, passing the implemented spec or ticket so both review axes have their source. Do not ask the user for a fixed point; it is already known. `/vibe-review` is read-only: it reports findings but never changes an issue body, checkbox, comment, label, or status. A passing review never authorizes a tracker write.
3. After the read-only review has no unresolved findings, commit the reviewed work on its work branch. Then follow the mode recorded at invocation:
   - **Caller-owned.** Commit only the ticket branch. Never publish the ticket branch. Never merge or land it into the integration workspace or original target. Never clean, delete, or otherwise alter the caller-owned worktree. Never change tracker state. Return only the branch name, exact commit SHA, verification evidence, and the read-only `/vibe-review` verdict and report to the caller. The caller owns integration and final landing.
   - **Standalone.** Keep the reviewed task branch as the implementation source instead of landing it: for a local Markdown tracker, confirm the unmerged branch is retained before removing the worktree and report the branch name; for GitHub, push the task branch and create a pull request targeting the original branch; for GitLab, push it and create a merge request targeting the original branch; and for another hosted tracker that supports pull/merge requests, follow its documented publication workflow. Remove the worktree only after retention or publication succeeds, and report the request URL for a hosted tracker. Never merge automatically; the original branch remains unchanged.

If unrelated dirty state, conflict uncertainty, or another unsafe condition prevents a safe disposition, do not publish, merge, or clean; preserve the source and result branches and commits, leave user state untouched, and report the blocker. For an in-progress merge or rebase conflict, follow [MERGE-CONFLICTS.md](MERGE-CONFLICTS.md).

Publication is not landing. Only in a later or resumed standalone action, after independently obtaining authoritative evidence that the exact reviewed commit SHA is present on the designated target branch (for example, `git merge-base --is-ancestor <commit> <target-branch>` succeeds), may the workflow propose tracker changes. Only after that proof may it show an exact tracker-change preview: every checkbox to check, the complete comment to post, and every status or close change. Wait for separate, explicit approval of that preview immediately before writing. A request to implement, a completed review, rejection, or no response is not approval; without approval, leave the tracker unchanged. Never propose or modify the parent spec issue.
