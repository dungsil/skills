---
name: vibe-goal
description: Drives a whole goal from request to reviewed, committed code — routes into planning, publishes tickets, dispatches one fresh sub-agent per ticket to implement and review it, integrates completed work in a dedicated workspace, then safely lands only a reviewed and gated result. Use when the user wants a feature taken end to end, says "do this whole thing", or asks to run plan-through-review in one go.
disable-model-invocation: true
metadata:
  argument-hint: "The goal — an idea, request, spec, or issue reference"
---

# Driving a Goal to Done

One run from a goal to reviewed, committed code. This skill **orchestrates**; it does not implement. Planning happens through `/vibe-plan` (or `/vibe-deep-plan` first, when the work is too big), each ticket is implemented by a **fresh sub-agent** running `/vibe-implement`, and the whole goal closes on a standards review plus a formal requirement quality gate.

The issue tracker and triage label vocabulary should have been provided to you — run `/vibe-init` if `docs/agents/issue-tracker.md` is missing.

## Landing and tracker state

`/vibe-review` is read-only: it reports findings and never changes tracker state. A review pass, a sub-agent completion claim, or the user's general goal request does not authorize a tracker write. The parent spec issue is read-only: never include it in a tracker-change preview or modify it.

The goal owns integration and final landing. For a **hosted** tracker it must not preview or change ticket state while work exists only on ticket branches or in the **integration workspace**; only after Stage 4 has authoritative evidence that the exact reviewed integration commit SHA is present on the original target branch may it show an exact preview of every checkbox, the complete comment, and each status or close change, then wait for separate, explicit approval immediately before writing — rejection or no response leaves the tracker unchanged. For a **version-controlled local Markdown** tracker there is nothing to preview: each ticket's checkboxes ride that ticket's own implementation commit, so integration and landing carry code and checklist together. Either way the orchestrator never edits a ticket file itself and never puts a tracker-only commit on the target branch.

## The orchestrator never writes production code

Your context is the only thing holding the goal together: the ticket graph, what landed, what's left. Spending it on implementation is how a run dies halfway.

- **Never edit source files yourself.** Every code change goes through a ticket sub-agent.
- **Never read a diff in full.** Read the sub-agent's report and the review verdict; open code only to adjudicate a specific disputed claim.
- **Stay in one context window** from planning through the last ticket. If it degrades anyway, hand off with `/vibe-handoff` — pass the ledger, not the history.

The exception is the planning stages, which you run yourself: they produce the tickets everything else consumes.

## Stage 1 — Route the goal

Read what the user brought and pick the entry, in one line, then act:

|What the user brought|Route|
|---|---|
|Work too big for one planning session — fog between here and the destination|`/vibe-deep-plan` first; when the map clears, its Destination and Decisions-so-far enter `/vibe-plan` at the **spec** stage|
|Anything else — external request, loose idea, settled conversation, spec, issue reference|`/vibe-plan`, which picks its own entry stage from the same input|
|A published ticket set from a previous run|Skip to **Stage 2** with those tickets|

Two routes end the run early, and that's a success, not a failure:

- Triage lands `wontfix` or `needs-info` → report the outcome and stop. Nothing to implement.
- Triage lands `ready-for-human` → report why it can't be delegated and stop.

`/vibe-deep-plan` is a **multi-session** skill. If you route there, expect this run to end when the session fills up mid-map; hand off and resume. Don't try to force a whole decision map and its implementation into one window.

## Stage 2 — Build the ledger and integration workspace

When `/vibe-plan` has published its tickets, first record the original target branch or ref and its fixed point. Then create a clean, goal-specific integration branch and separate worktree rooted exactly at that fixed point: this is the **integration workspace**. Create it from the commit object without checking out, resetting, cleaning, stashing, staging, or otherwise altering the user's original checkout. Its tracked and untracked changes are user-owned, even when dirty. If a clean separate worktree cannot be created, stop and report it; never reuse the original checkout or a shared worktree. The integration branch is the run's **single durable artifact**; every ticket branch and ticket worktree is disposable scaffolding you delete once its commits are proven contained.

Write the **ledger** — the one artifact you keep in context for the rest of the run. Refer to tickets by title, never by a bare number.

```markdown
## Goal

<one line — what "done" means for this run>

## Original target

<branch or ref selected for final landing; its user checkout remains untouched>

## Fixed point

<the commit SHA at the start of the run, from `git rev-parse HEAD`>

## Integration workspace

<goal-specific integration branch>; <separate worktree path>; rooted at <fixed point>; clean

## Spec

<link to the published spec issue / path>

## Tickets

| Ticket | Blocked by | Status | Ticket result | Integration evidence (returned reviewed SHA → replayed range → new integration head) |
|---|---|---|---|---|
| <title> (link) | — | pending | | |
| <title> (link) | <title> | pending | | |
```

Status is `pending` → `running` → `integrated` → `landed`, or `blocked` / `failed`. `integrated` means the reviewed ticket commits were replayed onto the integration head, proven contained patch-equivalently, and ticket-attributed verification passed at the new head; it unblocks dependents. `landed` is recorded only after Stage 4 proves the reviewed integration result is present on the original target branch. Neither status authorizes a hosted tracker write.

Record the fixed point and clean integration workspace before the first ticket sub-agent starts. The fixed point is what the Stage 4 whole-goal review runs against.

Show the ledger to the user and confirm the execution order before dispatching anything.

## Stage 3 — Work the frontier in the integration workspace

The **frontier** is every ticket whose blockers are all `integrated` or `landed`. Run it in waves: dispatch the whole frontier, wait for returns, verify them, integrate verified results one at a time, recompute, repeat.

### Dispatch

Record the current integration branch head as the base for a wave. One **fresh sub-agent per ticket**, all of a wave dispatched in a single batch so they run concurrently. Each sub-agent gets, and only gets:

- The ticket reference and its full body (fetched from the tracker — don't make the sub-agent guess where it lives).
- The spec link, for context it can read if it needs to.
- The **caller-owned** invocation, including its assigned ticket branch and worktree plus the integration branch and exact base SHA.
- The instruction: **run `/vibe-implement` in caller-owned mode on this ticket, and nothing else.**
- The boundary: implement **only** this ticket. Out-of-scope problems it notices get reported back, not fixed.
- The linearity rule: keep the ticket branch **linear on the supplied base** — never merge the integration branch or the original target into it, never create an extra branch or worktree, and report a moved base instead of rebasing or merging on its own.

Every goal-dispatched implementer follows this **shared return contract**: in caller-owned mode, create or use only its assigned ticket branch and worktree from that integration base, keep that branch linear on it, commit only there, and return the ticket branch name, exact committed and reviewed SHA, verification evidence, and read-only `/vibe-review` verdict. It must never merge the integration branch or the original target into its branch, create any extra branch or worktree, alter either checkout, clean or delete the caller-owned workspace, or modify hosted tracker state — for a version-controlled local Markdown tracker its own ticket file is branch content and belongs in that same ticket commit, never in a separate one. If the supplied base moved, it reports that instead of rebasing or merging on its own. The caller deletes the ticket branch and worktree after integration, so the implementer leaves nothing else behind. A missing branch, SHA, verification record, or review verdict is an incomplete return, not a completed ticket.

Do not paste the conversation, the other tickets, or the plan history. A ticket that can't be understood from its own body is a planning defect — fix the ticket, don't compensate in the prompt.

**Parallel waves need decided contracts.** Two tickets in the same wave may touch the same files; that's fine, but any interface they share must already be pinned in the spec or the tickets. If a wave's tickets would have to negotiate a contract between themselves, that's a signal the tickets were cut wrong — merge them, or serialise them with a blocking edge, before dispatching.

### Verify and integrate each return

`completed` from a sub-agent is a claim, not a fact. A review pass is report evidence, never tracker authority. Before a returned result may enter the integration workspace, check cheaply and independently:

- The returned ticket branch and exact commit SHA exist, the reviewed SHA is the returned SHA, and the branch is based on the wave's recorded integration base.
- Its acceptance criteria are addressed — per the sub-agent's verification evidence and read-only `/vibe-review` verdict.
- The review findings, if any, are either fixed or explicitly accepted by the user.

Integrate verified completed returns from a wave **sequentially, one ticket branch at a time**, by replay — never as a bulk merge, an octopus merge, or any `git merge` that would create a merge commit. Skill-created history stays linear. Before each branch, confirm the integration workspace is at its recorded expected head and clean. Unrelated dirt, an unexpected file, or a changed integration head is a safe stop: report it and do not clean, reset, stash, delete, or touch the user's original checkout.

For one ticket at a time, replay only the returned commits that resolve to its exact reviewed SHA: rebase that ticket branch onto the current integration head, then advance the integration branch to the replayed tip by **fast-forward only** (`--ff-only`). Never create a merge commit. If a rebase conflict's intended resolution is not unambiguous from the ticket and review context, stop and report; never guess, force-continue, skip a commit, or stage everything. When the intended resolution is clear, resolve and stage **only** the explicit, intended conflict paths; never use whole-worktree staging or stage user, secret, or unexpected files. Rebase rewrites commit ids, so the reviewed SHA no longer exists on the integration branch: before the ticket may become `integrated`, prove the reviewed commits are contained **patch-equivalently** in the replayed range — `git cherry` or `git range-diff` between the returned reviewed SHA and that range — and re-run that ticket's acceptance verification at the new integration head. Record the ticket title, returned branch and reviewed SHA, the replayed commit range, the new integration head SHA, commands or checks run, and result in the ledger. Only after containment is proven and that verification passes may the ticket become `integrated` and the next branch be replayed. A conflict, unproven containment, or verification failure stops automatic integration before the next ticket and preserves the integration result for diagnosis.

`integrated` is integration-branch evidence, not original-target landing proof. Do not preview or change hosted tracker state during Stage 3; a version-controlled local Markdown ticket file arrives inside its own ticket commit, never as an orchestrator edit.

### When a ticket fails

Don't redispatch the same prompt at the same wall. Diagnose which of these it is:

- **Ticket too big for one context** — split it into blocked slices, publish them, and put them on the frontier.
- **Ticket underspecified** — an open decision leaked past planning. Take it back to the user (`/vibe-grilling` if it's more than a one-liner), amend the ticket, redispatch.
- **Something is genuinely broken in the codebase** — hand that off to `/vibe-debug` as its own ticket, and treat it as a blocker of the failed one.
- **The plan is wrong** — stop the run, report it, and go back to Stage 1. Downstream tickets built on a wrong plan waste more than the restart does.

Mark the ticket `failed` in the ledger with one line of reason. Tickets it blocks stay `pending`; the rest of the frontier keeps moving.

## Stage 4 — Review, gate, then safely land the integration result

When every ticket is `integrated` and none is `pending`, `running`, `blocked`, or `failed`, record the integration branch and its exact current head SHA as the review candidate. Confirm the integration workspace is clean; unexplained dirt or files stop the run and preserve that branch and SHA.

1. **Full suite once.** Typecheck, tests, and whatever the repo's checks are — run them in the integration workspace at the recorded candidate, not per ticket and not in the user's checkout.
2. **Standards axis.** Run `/vibe-review` from the Stage 2 fixed point to the recorded integration branch and commit. Per-ticket reviews each saw one slice; this one sees the seams between them, which is where the interesting findings are. Tell it the Spec axis is being handled by the gate, so it doesn't duplicate it.
3. **Spec verdict.** Run `/rq` with the Stage 2 fixed point as the change boundary and the recorded integration result as its head, scoped to the implementation domains (`CODE`, plus `MIGRATION` when the goal required one). The source requirement is the goal's **definition of done** — the acceptance criteria the run actually committed to (the spec's acceptance criteria, or the ledger's goal line when the spec has none). User stories beyond that stay traceability rows, out of scope; gating every story in a large spec duplicates what the per-ticket reviews already covered. The gate returns per-criterion status and an aggregated `PASS` / `WARNING` / `NEEDS_REVIEW` / `FAIL`.
   - Default to the gate's `LIGHT` tier. Take `HEAVY` only when the goal touched the domains that always warrant it — security, auth, permissions, persistence, transactions, external integrations — the same rule `vibe-review` escalates on.
   - Operation, deployment, and data obligations stay **separate gates**. They don't lower the implementation verdict, and you don't run them here unless the user asks.
4. **Adjudicate.** Present the Standards findings and the gate report side by side, unmerged. Anything the gate marks `not satisfied` or `unknown`, and any Standards finding the user wants fixed, becomes a new ticket back through Stage 3 — never patched inline by you. Re-run the gate after those tickets integrate; the goal isn't closed on a `FAIL`.
5. **Land only when safe.** Use a clean, independent landing context; never checkout, reset, clean, stash, or merge in the user's original checkout. Land by **fast-forward only** (`--ff-only`) onto the original target, carrying the exact reviewed integration commit. If the target moved off its recorded fixed point, rebase the integration branch onto the current target head, re-run the Stage 4 full suite at the new candidate, record the new candidate SHA, and only then fast-forward. Never create a merge commit on the target, never force-push, and never reset or rewrite the target. If workspace dirt, an unexpected file, an unclear conflict, a failing re-run, or missing landing proof makes landing unsafe, do not land. Preserve the reviewed integration branch and SHA, report them, and leave tracker state untouched. A clearly intended landing conflict follows the same explicit-path-only staging rule as Stage 3.
6. **Prove landing, then ask.** Only after authoritative evidence proves the exact reviewed integration commit SHA is present on the original target branch, record affected tickets as `landed`. For a version-controlled local Markdown tracker that landing already carried each ticket's checked checklist, so there is nothing to preview and nothing to write — never add a tracker-only commit on the target. For a hosted tracker, show the exact checkbox changes, complete comment, and status or close change proposed for each ticket; exclude the parent spec issue. Wait for separate, explicit approval before applying only that preview. Rejection or no response leaves the tracker unchanged.
7. **Report.** The goal, the tickets that landed with their links, the integration branch and reviewed commit, the final landing evidence, the gate's overall status, the Standards findings, and anything deliberately left undone.

Never close, modify, or include the parent spec issue in any tracker-change preview, even after landing proof and approval.

## End of run — leave exactly one artifact

After the final report, the run leaves **exactly one surviving artifact**. With landing proof, that is the original target branch: delete every ticket branch and the integration branch this run created, and remove every worktree it created. Without landing proof — landing unsafe or unapproved — the survivor is the final linear integration branch plus at most its own worktree, and the ticket branches and ticket worktrees still go.

Delete a branch only against the Stage 3 containment proof that its commits are present patch-equivalently in the surviving artifact. If containment cannot be proven for one branch, keep that branch, report it as a named exception, and delete nothing else about it. `git worktree remove <path>` is allowed for the worktrees. Replay rewrote the commit ids, so a ticket tip is not an ancestor of the survivor and `git branch -d` will refuse it: once containment is proven, delete those ephemeral ticket branches — and the integration branch after landing proof — with `git branch -D`. Never force-delete a branch whose containment is unproven. Never delete or alter a user-owned or unrelated branch or worktree, and never clean, reset, or stash one. If the run stops on a blocker, preserve the affected artifacts and report them instead.
