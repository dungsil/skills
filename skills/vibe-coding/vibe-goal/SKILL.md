---
name: vibe-goal
description: Drives a whole goal from request to reviewed, committed code — routes into `/vibe-plan` (or `/vibe-deep-plan`) and takes their published tickets, dispatches one fresh sub-agent per ticket into its own assigned branch and worktree, replays the reviewed ticket results into a single goal integration workspace, then safely lands only a reviewed and gated result. Use when the user wants a feature taken end to end, says "do this whole thing", or asks to run plan-through-review in one go.
disable-model-invocation: true
metadata:
  argument-hint: "The goal — an idea, request, spec, or issue reference"
---

# Driving a Goal to Done

One run from a goal to reviewed, committed code. This skill **orchestrates**; it does not implement, and it does not plan. It may invoke or route `/vibe-plan` and `/vibe-deep-plan`, but it **never performs plan work itself** — triage, discovery, spec drafting or revision, decomposition, ticket design, and ticket publication are owned by those skills. Planning happens through `/vibe-plan` (or `/vibe-deep-plan` first, when the work is too big), each ticket is implemented by a **fresh sub-agent** running `/vibe-implement`, and the whole goal closes on a standards review plus a formal requirement quality gate.

Each ticket gets its own **assigned branch and worktree** — a per-ticket workspace isolated from every other ticket. Different tickets can run in parallel; the same ticket's implement → review → finding-fix → resume reuses its one exact assigned workspace. The goal keeps a single **integration workspace** that is separate from the assigned workspaces: reviewed ticket results are replayed into it for whole-goal verification, never committed into it by a ticket sub-agent. A resume reuses the recorded integration workspace and each ticket's recorded assigned workspace by their exact records — it never opens a second for the same goal or the same ticket.

The issue tracker and triage label vocabulary should have been provided to you — run `/vibe-init` if `docs/agents/issue-tracker.md` is missing.

## Landing and tracker state

`/vibe-review` is read-only: it reports findings and never changes tracker state. A review pass, a sub-agent completion claim, or the user's general goal request does not authorize a tracker write. The parent spec issue is read-only: never include it in a tracker-change preview or modify it.

The goal owns integration and final landing. For a **hosted** tracker it must not preview or change ticket state while work exists only in the **integration workspace**; only after Stage 4 has authoritative evidence that the exact reviewed integration commit SHA is present on the original target branch may it show an exact preview of every checkbox, the complete comment, and each status or close change, then wait for separate, explicit approval immediately before writing — rejection or no response leaves the tracker unchanged. For a **version-controlled local Markdown** tracker there is nothing to preview: each ticket's checkboxes ride that ticket's own implementation commit, so integration and landing carry code and checklist together. Either way the orchestrator never edits a ticket file itself and never puts a tracker-only commit on the target branch.

## The orchestrator never plans or writes production code

Your context is the only thing holding the goal together: the ticket graph, what landed, what's left. Spending it on implementation is how a run dies halfway.

- **Never edit source files yourself.** Every code change goes through a ticket sub-agent.
- **Never read a diff in full.** Read the sub-agent's report and the review verdict; open code only to adjudicate a specific disputed claim.
- **Stay in one context window** from Stage 1 routing through the last ticket. If it degrades anyway, hand off with `/vibe-handoff` — pass the ledger, not the history.

There is no exception. The planning stages belong to `/vibe-plan` and `/vibe-deep-plan`: you route to them, they produce the tickets, and you consume what they publish. If the plan must change later, route back to Stage 1 and the plan skill — never amend the plan yourself.

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

## Stage 2 — Build or resume the ledger and integration workspace

When `/vibe-plan` has published its tickets, first establish the **canonical goal ID** — the durable identifier this goal's work connects to: the published spec issue / path, or, on a resumed run, the same identifier the prior run recorded in its ledger. This ID is what ties a logical goal to one integration workspace across sessions, resumes, and handoffs; nothing else does.

### Resume or create

Before creating anything, first decide whether this is a **resume** or a **fresh goal**. A resume is when the user explicitly asks to continue, finish, or pick up a prior goal run — or the entry comes from a published ticket set or handoff that names a prior run. Anything else is a fresh goal. With that decision in hand, look for an existing **workspace record** — a ledger, handoff, or caller record that ties the canonical goal ID to an integration workspace with its recorded original target, original fixed point, integration branch, worktree path, and current owner/state. **Never match by branch name, number, slug, the latest `vibe/*` branch, or a similar-looking name** — a name that seems right is not a match. Only an exact, single record authorizes reuse.

- **Exact record, no active owner, worktree present and clean** → reuse that integration workspace as-is.
- **Exact record, no active owner, worktree gone but the recorded integration branch still exists** → re-attach a worktree to that same integration branch (`git worktree add <path> <recorded-branch>`); create no new branch.
- **Fresh goal, no matching record** → this is a genuinely **new goal**. Record the original target branch or ref and its fixed point — the **original fixed point**, frozen for the whole goal — then create a clean, goal-specific integration branch and separate worktree rooted exactly at that fixed point: this is the **integration workspace**. Create it from the commit object without checking out, resetting, cleaning, stashing, staging, or otherwise altering the user's original checkout. Its tracked and untracked changes are user-owned, even when dirty. If a clean separate worktree cannot be created, stop and report it; never reuse the original checkout or a shared worktree.
- **Resume, but no matching record found** → a resume that cannot find its record cannot safely reuse a workspace, and must not create a new sibling integration workspace either — that would fork the goal into two integration lines. Stop and report that the resume record for this canonical goal ID is missing; the user either locates the record or explicitly authorises a fresh start. Do not guess, do not create, do not reuse.
- **More than one candidate, an active owner conflict (another run or sub-agent owns the recorded workspace), or the branch head or clean state does not match the record** → do not reuse, do not create a new sibling integration workspace, and do not guess. Stop and report what was found and why reuse is ambiguous. The user resolves the conflict.

On reuse, the continuation's fixed point is the **original fixed point the record captured at first run**, never the current head; the Stage 4 whole-goal review still runs against that first fixed point.

### The ledger

Write the **ledger** — the one artifact you keep in context for the rest of the run. It is the workspace record and the live state of every ticket. Refer to tickets by title, never by a bare number.

```markdown
## Goal

<one line — what "done" means for this run>

## Canonical goal ID

<spec issue / path, or the same identifier a prior run recorded on resume>

## Original target

<branch or ref selected for final landing; its user checkout remains untouched>

## Original fixed point

<the commit SHA captured at first run, from `git rev-parse HEAD`; the whole-goal review runs against this>

## Integration workspace

<goal-specific integration branch>; <separate worktree path>; rooted at <original fixed point>; clean; owner: <this run / idle>; state: <new / resumed>

## Spec

<link to the published spec issue / path>

## Tickets

| Ticket (canonical ID) | Assigned branch / worktree | Blocked by | Status | Wave base (integration head at dispatch) | Owner / state | Returned reviewed SHA | Verification | Replay evidence |
|---|---|---|---|---|---|---|---|---|
| <title> (link) | vibe/<id> ; .agents/worktrees/<id> | — | pending | — | — / idle | — | — | — |
| <title> (link) | vibe/<id> ; .agents/worktrees/<id> | <title> | running | <base SHA> | sub-agent / active | <reviewed SHA> | <verdict> | — |
| <title> (link) | vibe/<id> ; .agents/worktrees/<id> | <title> | integrated | <base SHA> | — / idle | <reviewed SHA> | <verdict> | replayed to integration @ <head> |
```

Status is `pending` → `running` → `integrated` → `landed`, or `blocked` / `failed`. `integrated` means the reviewed ticket result has been replayed onto the integration head and ticket-attributed verification passed at that head; it unblocks dependents. `landed` is recorded only after Stage 4 proves the reviewed integration result is present on the original target branch. Neither status authorizes a hosted tracker write.

Record the original fixed point and clean integration workspace before the first ticket sub-agent starts. On resume the original fixed point stays the first run's value, not the current head.

Show the ledger to the user and confirm the execution order before dispatching anything.

## Stage 3 — Work the frontier in parallel, replay results into the integration workspace

The **frontier** is every ticket whose blockers are all `integrated` or `landed`. Dispatch the **entire frontier at once** — every ready ticket in parallel, each into its own assigned branch and worktree. Do not serialize ready tickets; they are independent by definition of the frontier. Within the frontier, different tickets run concurrently in separate assigned workspaces; a ticket that depends on another waits until its blocker is `integrated`, then joins the next wave.

### Dispatch

For each ready ticket in the frontier, create a per-ticket **assigned workspace**: a dedicated branch and worktree rooted at the integration head. Record that head as the ticket's **wave base** — the fixed point its review will run against. Create each assigned workspace from the commit object without checking out, resetting, cleaning, stashing, or otherwise altering the user's original checkout or the integration workspace. Record the assigned branch, worktree path, wave base, owner, and state in the ledger before the sub-agent starts. Dispatch one **fresh sub-agent** per ticket, concurrently. Each gets, and only gets:

- The ticket reference and its full body (fetched from the tracker — don't make the sub-agent guess where it lives).
- The spec link, for context it can read if it needs to.
- The **caller-supplied** invocation: the ticket's own assigned branch and worktree, plus the exact wave base SHA.
- The instruction: **run `/vibe-implement` in caller-supplied mode on this ticket, and nothing else.**
- The boundary: implement **only** this ticket. Out-of-scope problems it notices get reported back, not fixed.
- The linearity rule: commit **directly to the assigned ticket branch** in its worktree — keep it linear on the wave base, never merge the original target or the integration branch into it, never create an extra branch or worktree, and report a moved base instead of rebasing or merging on its own.

Each ticket in the ledger carries its **canonical ticket ID**, its **assigned branch and worktree**, its **wave base** (the integration head at dispatch), its **owner/state**, and its **status** (`pending` → `running` → `integrated`/`failed`).

A **retry of the same ticket** — a review-findings fix or a corrective redispatch — reuses that ticket's **exact assigned workspace**: the same assigned branch and worktree recorded for that canonical ticket ID. A fresh sub-agent is allowed; a new branch, worktree, or ticket ID is not. If the assigned workspace is stale, dirty, or its head does not match the recorded state, that is a stop-and-report condition, not a reset shortcut; preserve the exact workspace and surface the mismatch so it can be resolved explicitly. Never guess a workspace by name, slug, number, or the latest `vibe/*` branch; reuse only by the exact recorded record. Only a genuinely **new** ticket (a split slice with its own canonical ID, or a correction ticket from Stage 4) gets its own new assigned workspace and ledger entry.

Every goal-dispatched implementer follows this **shared return contract**: in caller-supplied mode, commit directly to the assigned ticket branch in its worktree, keep it linear on the wave base, and return the assigned branch name, the exact wave base SHA, the exact reviewed head SHA, verification evidence, and the read-only `/vibe-review` verdict. It must never merge the original target or the integration branch into the ticket branch, create any extra branch or worktree, alter the original checkout or the integration workspace, clean or delete the assigned workspace, or modify hosted tracker state — for a version-controlled local Markdown tracker its own ticket file is branch content and belongs in that same commit, never in a separate one. If the wave base moved, it reports that instead of rebasing or merging on its own. A missing branch, head SHA, wave base SHA, verification record, or review verdict is an incomplete return, not a completed ticket.

Do not paste the conversation, the other tickets, or the plan history. A ticket that can't be understood from its own body is a planning defect — route it back to the plan skill to fix it, don't compensate in the prompt.

### Verify each return and replay into integration

`completed` from a sub-agent is a claim, not a fact. A review pass is report evidence, never tracker authority. Before a returned result may be accepted, check cheaply and independently:

- The assigned ticket branch exists, its head SHA is the returned reviewed SHA, and it is based on the returned wave base SHA — which must match the base recorded at dispatch.
- Its acceptance criteria are addressed — per the sub-agent's verification evidence and read-only `/vibe-review` verdict.
- The review findings, if any, are either fixed or explicitly accepted by the user.

Once these checks pass, **replay** the reviewed ticket result into the integration workspace, **one ticket at a time**: confirm the integration workspace is clean and its head matches the expected integration head, rebase or cherry-pick the reviewed ticket commits onto that head, advance it fast-forward only (`--ff-only`), and verify patch-equivalent containment — the reviewed ticket commits are present on the new integration head with no added, dropped, or altered commits. Then **rerun the ticket's own verification at the new integration head**: run the ticket's test/verification commands in the integration workspace at that head and confirm they still pass — a patch that was green in isolation can break at the integration seam. Only when both patch containment and the rerun pass is the replay accepted. Record the new integration head as the replay evidence. Unrelated dirt, an unexpected file, or a changed integration head is a safe stop: report it and do not clean, reset, stash, delete, or touch the user's original checkout, the integration workspace, or the assigned workspace. If the checks fail, the ticket is not `integrated`; preserve both workspaces and report the blocker.

After successful replay, record the ticket's returned reviewed SHA, verification, and replay evidence in the ledger, mark the owner idle, and mark the ticket `integrated`. **Retain the assigned ticket branch and worktree** until the goal lands: a same-ticket review fix or resumption reattaches to that exact branch and worktree by its record, never a new one. `integrated` is integration-head evidence, not original-target landing proof. Do not preview or change hosted tracker state during Stage 3; a version-controlled local Markdown ticket file arrives inside its own ticket commit, never as an orchestrator edit.

### When a ticket fails

Diagnose which of these it is:

- **Ticket too big for one context** — route it back to the plan skill, which splits it into blocked slices and publishes them; the published slices enter the ledger as new tickets, each with its own assigned workspace. The split parts run next in their dependency order.
- **Ticket underspecified** — stop implementation and route it back to `/vibe-plan` for the missing decision and ticket amendment. `/vibe-goal` does not grill the user or amend the ticket itself; retry only after the plan skill republishes it.
- **Something is genuinely broken in the codebase** — report the blocker to `/vibe-plan`; only a blocker ticket that the plan skill publishes may enter the ledger and run through `/vibe-debug` in its own assigned workspace.
- **The plan is wrong** — stop the run, report it, and go back to Stage 1: the plan skill owns the fix, you never amend the plan yourself. Downstream tickets built on a wrong plan waste more than the restart does.

A failure or a review finding that stays within the same ticket runs in that ticket's **same assigned workspace** — a retry of the same canonical ticket, never a new workspace, branch, or ticket ID. Any new Stage 4 correction ticket must first be designed and published by `/vibe-plan`; only then may `/vibe-goal` add its canonical ID and assigned workspace. If the assigned workspace is dirty or its state is ambiguous, stop, preserve it, and report; do not reset or guess.

## Stage 4 — Review, gate, then safely land the integration result

When every ticket is `integrated` and none is `pending`, `running`, `blocked`, or `failed`, record the integration branch and its exact current head SHA as the review candidate. Confirm the integration workspace is clean; unexplained dirt or files stop the run and preserve that branch and SHA.

1. **Full suite once.** Typecheck, tests, and whatever the repo's checks are — run them in the integration workspace at the recorded candidate, not per ticket and not in the user's checkout.
2. **Standards axis.** Run `/vibe-review` from the Stage 2 fixed point to the recorded integration branch and commit. Per-ticket reviews each saw one slice; this one sees the seams between them, which is where the interesting findings are. Tell it the Spec axis is being handled by the gate, so it doesn't duplicate it.
3. **Spec verdict.** Run `/rq` with the Stage 2 fixed point as the change boundary and the recorded integration result as its head, scoped to the implementation domains (`CODE`, plus `MIGRATION` when the goal required one). The source requirement is the goal's **definition of done** — the acceptance criteria the run actually committed to (the spec's acceptance criteria, or the ledger's goal line when the spec has none). User stories beyond that stay traceability rows, out of scope; gating every story in a large spec duplicates what the per-ticket reviews already covered. The gate returns per-criterion status and an aggregated `PASS` / `WARNING` / `NEEDS_REVIEW` / `FAIL`.
   - Default to the gate's `LIGHT` tier. Take `HEAVY` only when the goal touched the domains that always warrant it — security, auth, permissions, persistence, transactions, external integrations — the same rule `vibe-review` escalates on.
   - Operation, deployment, and data obligations stay **separate gates**. They don't lower the implementation verdict, and you don't run them here unless the user asks.
4. **Adjudicate.** Present the Standards findings and the gate report side by side, unmerged. Anything the gate marks `not satisfied` or `unknown`, and any Standards finding the user wants fixed, becomes a **correction**: route it back to the plan skill, which designs and publishes the new ticket; it then flows back through Stage 3 — never patched inline by you. A correction ticket is a genuinely new canonical ticket with its own canonical ID and its own assigned workspace, dispatched and replayed like any other ticket. Re-run the gate after those tickets integrate; the goal isn't closed on a `FAIL`.
5. **Land only when safe.** Use a clean, independent landing context; never checkout, reset, clean, stash, or merge in the user's original checkout. Land by **fast-forward only** (`--ff-only`) onto the original target, carrying the exact reviewed integration commit. If the target moved off its recorded fixed point, rebase the integration branch onto the current target head, re-run the Stage 4 full suite at the new candidate, record the new candidate SHA, and only then fast-forward. Never create a merge commit on the target, never force-push, and never reset or rewrite the target. If workspace dirt, an unexpected file, an unclear conflict, a failing re-run, or missing landing proof makes landing unsafe, do not land. Preserve the reviewed integration branch and SHA, report them, and leave tracker state untouched. A clearly intended landing conflict is resolved by staging only the explicit, intended conflict paths — never whole-worktree staging, never user, secret, or unexpected files.
6. **Prove landing, then ask.** Only after authoritative evidence proves the exact reviewed integration commit SHA is present on the original target branch, record affected tickets as `landed`. For a version-controlled local Markdown tracker that landing already carried each ticket's checked checklist, so there is nothing to preview and nothing to write — never add a tracker-only commit on the target. For a hosted tracker, show the exact checkbox changes, complete comment, and status or close change proposed for each ticket; exclude the parent spec issue. Wait for separate, explicit approval before applying only that preview. Rejection or no response leaves the tracker unchanged.
7. **Report.** The goal, the tickets that landed with their links, the integration branch and reviewed commit, the final landing evidence, the gate's overall status, the Standards findings, and anything deliberately left undone.

Never close, modify, or include the parent spec issue in any tracker-change preview, even after landing proof and approval.

## End of run — leave exactly one artifact

The only final landing and publication target is the **goal integration result** — the reviewed integration commit on the original target branch. The assigned ticket branches are caller-owned ephemeral scaffolding, reused across a ticket's implement → review → fix → resume and never standalone publication targets: do not open a per-ticket PR or MR on a hosted tracker for any of them. After the final report, the run leaves **exactly one surviving artifact**, cleaned up in the safe order — worktree first, branch after.

- **With landing proof** — the survivor is the original target branch. Clean up the ticket assigned artifacts and the integration workspace this run created: for each one, remove the worktree with `git worktree remove <path>` first, then delete the branch with `git branch -D`. This leaves only the original target.
- **Without landing proof** (landing unsafe or unapproved) — the survivor is the reviewed integration branch plus at most its own worktree. Clean up an integrated ticket's assigned artifact only when its patch-equivalent containment in the integration survivor is proven **and** it is no longer needed for a same-ticket review fix or resumption; if either is unclear, preserve it and report it as a named exception. Never force-delete a branch whose landing is unproven.

Always remove a worktree before deleting its branch — a checked-out branch cannot be deleted while its worktree still holds it, so deleting the branch first only fails. Never delete or alter a user-owned or unrelated branch or worktree, and never clean, reset, or stash one. If the run stops on a blocker, preserve the affected artifacts and report them instead. A ticket's assigned workspace is retained through its same-ticket review/fix and resumption (Stage 3) and is not deleted until the goal lands.
