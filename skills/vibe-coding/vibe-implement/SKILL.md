---
name: vibe-implement
description: Implements a piece of work from a spec or set of tickets, test-first at pre-agreed seams, then reviews and commits it. Use when the user asks to implement, build, or ship a spec, ticket, or plan that is already agreed.
disable-model-invocation: true
---

# Implementing Work

Implement the work described by the user in the spec or tickets.

## Plan boundary

This skill never performs plan-family work. It does no triage, discovery, spec drafting or revision, decomposition, ticket creation or splitting, ticket publication, or plan amendment. Implementation starts only from an already agreed spec or ticket.

If the input needs planning, or the plan is wrong or underspecified, stop implementation and return the work to the caller or to `/vibe-plan`/`/vibe-deep-plan`. Never repair or change the plan inside this skill.

## Completion ownership

At invocation, establish and record exactly one mode:

- **Caller-supplied** — the caller (for example `/vibe-goal`) supplies a per-ticket assigned work branch and worktree it owns, and owns integration and final landing. The same ticket's implement → review → finding-fix → resume reuses that one exact assigned workspace; never add a second workspace for the same ticket.
- **Standalone** — a direct `/vibe-implement` workflow that owns tracker-directed branch retention or publication and the in-commit ticket update for a version-controlled local Markdown tracker; for a hosted tracker it owns tracker-change handling only in a later or resumed action after independent landing proof. It never owns final landing.

If the completion mode is unclear, stop and ask before creating or changing a workspace.

Before touching anything, look for an existing **workspace record** for this same logical work — implementation, review, fixing review findings, and resuming a prior run are all the same work. A workspace record ties a work unit ID to its original target/ref, fixed point, branch, worktree path, and current owner/state; it lives in the caller or handoff result that supplied the workspace (caller-supplied) or in the run's own prior return record (standalone). If a single matching record exists and no other active owner holds it, reuse it exactly: continue all work in its existing worktree, reattach a worktree to its retained branch if the worktree was removed (do not create a new numbered branch), keep its original fixed point (do not recompute it from the current HEAD), and create no new branch or worktree. If there is no record, more than one candidate, another active owner, or an unexpected head or unsafe dirt, stop and report — do not guess by branch name, number, slug, or the latest `vibe/*`, and do not create a new sibling. Only when this is a new logical work with no matching record may you capture the current commit (`git rev-parse HEAD`) as the **fixed point** the review will run against, then create one workspace under Workspace isolation below and record it in your return result.

## Workspace isolation

Apply the record lookup from Completion ownership first, before classifying the change's size. If it matched an existing workspace record for this same logical work — whatever the change's size, even a one-line review-finding fix or resume — continue all implementation, testing, review-finding fixes, and resumption inside that exact workspace (its existing worktree, or its retained branch with a worktree reattached) and skip to the linearity rule below; never route such a continuation to the main checkout. In caller-supplied mode, use only the caller-supplied per-ticket assigned branch and worktree identified by the caller record — never an integration branch — and never add a second workspace for implementation, review, finding fixes, or retry across the same ticket, regardless of how small the change is; a missing or ambiguous caller-supplied record is a stop-and-report condition, not a license to create a new workspace. If no matching record exists in standalone mode, then classify the requested change: a single-file change or other trivial change is below the isolation threshold and stays in place; do not create a dedicated worktree for it. For a larger change with no matching record in standalone mode, if the project uses git with at least one commit (`git rev-parse HEAD` succeeds), and this is a new logical work, isolate the work in a dedicated worktree instead of editing the main checkout:

1. Name the branch `vibe/{number}-{function}` — `{number}` is the next sequential number among existing `vibe/*` branches (start at `1`), `{function}` is a short kebab-case slug of the feature (e.g. `vibe/3-user-auth`).
2. Create it: `git worktree add .agents/worktrees/{number}-{function} -b vibe/{number}-{function}` (branch off the fixed point). Make sure `.agents/worktrees/` is git-ignored.
3. Do all implementation, testing, and review inside that worktree.
Keep the task branch as the implementation source and leave the original branch unchanged. The same logical work — initial implementation, fixing review findings, and any resumption — reuses the one workspace its record identifies and never spawns a second branch or worktree; a continued run never creates a new `vibe/{next-number}-*` branch for the same work. A run creates at most one branch and at most one worktree, and keeps that branch's history linear: never merge the original branch, an integration branch, or the target into the work branch. If the base moved, rebase the work branch onto it in standalone mode; in caller-supplied mode report the moved base to the caller instead of merging or rebasing on your own. A caller-supplied run works only in its supplied work branch and worktree: never create an extra branch or worktree, modify the original target checkout, publish or merge the work branch, or clean, delete, or otherwise alter the caller-supplied worktree after returning. After the reviewed work is committed, follow the completion disposition below; never merge automatically. Record the workspace (work unit ID, original target/ref, fixed point, branch, worktree path, owner/state) in the completion return so a later review-fix or resumption of the same logical work can reuse it exactly instead of creating a new workspace.

If the repo has no commits yet (or the project isn't a git repo), skip isolation and work in place.

Build test-first at pre-agreed seams, following [TDD.md](TDD.md) — the red → green loop, seam discipline, and the anti-patterns to avoid.

Reference docs, read as needed:

- [TDD.md](TDD.md) — the red → green loop and its rules
- [tests.md](tests.md) — what a good test looks like, with examples
- [mocking.md](mocking.md) — when to mock and what to mock instead
- [MERGE-CONFLICTS.md](MERGE-CONFLICTS.md) — resolving an in-progress merge or rebase conflict

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

## Commit signing

This workflow's commits are authored by an AI, not a human, so they must never be signed as if a person made them. Never pass `-S`, `--gpg-sign`, or `--signoff` to `git commit`. If the repository or global config enables signing by default (`commit.gpgsign = true`), override it per commit with `git -c commit.gpgsign=false commit …` so the result is honestly unsigned. This applies to every commit this workflow creates, including the checkbox-flip edits that ride the implementation commit.

## Completion disposition

1. Read `docs/agents/issue-tracker.md`.
2. From the task branch, run `/vibe-review` against the fixed point recorded at the start, passing the implemented spec or ticket so both review axes have their source. Do not ask the user for a fixed point; it is already known. `/vibe-review` is read-only: it reports findings but never changes an issue body, checkbox, comment, label, or status. A passing review never authorizes a tracker write.
3. After the read-only review has no unresolved findings, commit the reviewed work on its work branch; for a version-controlled local Markdown tracker, include the ticket's checkbox flips ([Tracker updates](#tracker-updates)) in that commit. Then follow the mode recorded at invocation:
   - **Caller-supplied.** Commit directly to the caller-supplied work branch, keeping it linear on the caller-supplied base; create no extra branch or worktree. Never publish the work branch, treat it as a durable artifact, or clean it yourself. Never merge or land it into the original target. Never clean, delete, or otherwise alter the caller-supplied worktree — the caller owns it and disposes of it. Never change hosted tracker state; a version-controlled local Markdown ticket file is branch content and rides the ticket commit under [Tracker updates](#tracker-updates). Return the branch name, the exact supplied ticket base SHA, exact reviewed head SHA, verification evidence, the read-only `/vibe-review` verdict and report, a note if the supplied base moved, and a one-line resume record (work unit ID, the work branch, the worktree path, and the current owner/state) so a later review-fix or retry of the same ticket reuses this exact workspace instead of creating a new branch or worktree — nothing more. The caller owns integration and final landing.
   - **Standalone.** End with exactly one surviving branch and no leftover worktree: the reviewed `vibe/*` branch, linear on the fixed point — rebase it onto the target if the target moved, never a merge commit. Keep that branch as the implementation source instead of landing it: for a local Markdown tracker, confirm the unmerged branch is retained before removing the worktree and report the branch name; for GitHub, push the task branch and create a pull request targeting the original branch; for GitLab, push it and create a merge request targeting the original branch; and for another hosted tracker that supports pull/merge requests, follow its documented publication workflow. Remove the worktree only after retention or publication succeeds. Report the retained branch name (and the request URL for a hosted tracker) plus a one-line resume record (work unit ID, original target/ref, fixed point, retained branch, worktree path empty once removed, and current owner/state) so a later review-fix or resumption reattaches a worktree to that exact retained branch instead of creating a new `vibe/*` branch. Leave no other `vibe/*` branch or worktree behind from the same run. Never merge automatically; the original branch remains unchanged.

If unrelated dirty state, conflict uncertainty, or another unsafe condition prevents a safe disposition, do not publish, merge, clean, or delete anything; preserve the source and result branches and commits, leave user state untouched, and report the blocker. Removing a branch or worktree is allowed only once the disposition is safe. For an in-progress merge or rebase conflict, follow [MERGE-CONFLICTS.md](MERGE-CONFLICTS.md).

## Tracker updates

- **Local Markdown, version-controlled.** The ticket file is branch content, not an external system. Inside the work branch's worktree, flip the acceptance checkboxes to `[X]` and include those edits in the same commit as the implementation — no separate tracker commit, no comment, no commit on the original or target branch. Do not wait for landing proof and do not ask for a tracker-change approval: the user owns merging, and the merge carries code and checklist together or neither. Leave the `상태:` line unchanged.
- **Hosted trackers (GitHub, GitLab, another hosted tracker).** Tracker state is external and cannot land atomically with a merge, so publication is not landing. Only in a later or resumed standalone action, after independently obtaining authoritative evidence that the exact reviewed commit SHA is present on the designated target branch (for example, `git merge-base --is-ancestor <commit> <target-branch>` succeeds), may the workflow propose tracker changes. Only after that proof may it show an exact tracker-change preview: every checkbox to check, the complete comment to post, and every status or close change. Wait for separate, explicit approval of that preview immediately before writing. A request to implement, a completed review, rejection, or no response is not approval; without approval, leave the tracker unchanged.

Never propose or modify the parent spec issue on any tracker.
