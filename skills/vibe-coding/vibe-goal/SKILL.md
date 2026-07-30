---
name: vibe-goal
description: Drives a whole goal from request to reviewed, committed code — routes into planning, publishes tickets, then dispatches one fresh sub-agent per ticket to implement and review it, gating each wave on the dependency frontier. Use when the user wants a feature taken end to end, says "do this whole thing", or asks to run plan-through-review in one go.
metadata:
  disable-model-invocation: "true"
  argument-hint: "The goal — an idea, request, spec, or issue reference"
---

# Driving a Goal to Done

One run from a goal to reviewed, committed code. This skill **orchestrates**; it does not implement. Planning happens through `/vibe-plan` (or `/vibe-deep-plan` first, when the work is too big), each ticket is implemented by a **fresh sub-agent** running `/vibe-implement`, and the whole goal ends on a final review pass.

The issue tracker and triage label vocabulary should have been provided to you — run `/vibe-init` if `docs/agents/issue-tracker.md` is missing.

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

## Stage 2 — Build the ledger

When `/vibe-plan` has published its tickets, write the **ledger** — the one artifact you keep in context for the rest of the run. Refer to tickets by title, never by a bare number.

```markdown
## Goal

<one line — what "done" means for this run>

## Fixed point

<the commit SHA at the start of the run, from `git rev-parse HEAD`>

## Spec

<link to the published spec issue / path>

## Tickets

| Ticket | Blocked by | Status | Landed as |
|---|---|---|---|
| <title> (link) | — | pending | |
| <title> (link) | <title> | pending | |
```

Status is one of `pending` → `running` → `landed` / `blocked` / `failed`.

Record the fixed point **before** the first ticket sub-agent starts. It is what the Stage 4 whole-goal review runs against.

Show the ledger to the user and confirm the execution order before dispatching anything.

## Stage 3 — Work the frontier

The **frontier** is every ticket whose blockers are all `landed`. Run it in waves: dispatch the whole frontier, wait, verify, recompute, repeat.

### Dispatch

One **fresh sub-agent per ticket**, all of a wave dispatched in a single batch so they run concurrently. Each sub-agent gets, and only gets:

- The ticket reference and its full body (fetched from the tracker — don't make the sub-agent guess where it lives).
- The spec link, for context it can read if it needs to.
- The instruction: **run `/vibe-implement` on this ticket, and nothing else.** `/vibe-implement` already records its own fixed point, builds test-first, runs `/vibe-review`, and commits to the current branch — do not re-specify any of that.
- The boundary: implement **only** this ticket. Out-of-scope problems it notices get reported back, not fixed.

Do not paste the conversation, the other tickets, or the plan history. A ticket that can't be understood from its own body is a planning defect — fix the ticket, don't compensate in the prompt.

**Parallel waves need decided contracts.** Two tickets in the same wave may touch the same files; that's fine, but any interface they share must already be pinned in the spec or the tickets. If a wave's tickets would have to negotiate a contract between themselves, that's a signal the tickets were cut wrong — merge them, or serialise them with a blocking edge, before dispatching.

### Verify each return

`completed` from a sub-agent is a claim, not a fact. Before marking a ticket `landed`, check cheaply and independently:

- A commit exists for it (`git log <fixed-point>..HEAD --oneline`).
- Its acceptance criteria are addressed — per the sub-agent's report and the `/vibe-review` verdict it ran.
- The review findings, if any, are either fixed or explicitly accepted by the user.

Then update the ledger and the ticket's status on the tracker.

### When a ticket fails

Don't redispatch the same prompt at the same wall. Diagnose which of these it is:

- **Ticket too big for one context** — split it into blocked slices, publish them, and put them on the frontier.
- **Ticket underspecified** — an open decision leaked past planning. Take it back to the user (`/vibe-grilling` if it's more than a one-liner), amend the ticket, redispatch.
- **Something is genuinely broken in the codebase** — hand that off to `/vibe-debug` as its own ticket, and treat it as a blocker of the failed one.
- **The plan is wrong** — stop the run, report it, and go back to Stage 1. Downstream tickets built on a wrong plan waste more than the restart does.

Mark the ticket `failed` in the ledger with one line of reason. Tickets it blocks stay `pending`; the rest of the frontier keeps moving.

## Stage 4 — Close the goal

When no ticket is `pending` or `running`:

1. **Full suite once.** Typecheck, tests, and whatever the repo's checks are — run them at the goal level, not per ticket.
2. **Whole-goal review.** Run `/vibe-review` against the Stage 2 fixed point, passing the spec. Per-ticket reviews saw one slice each; this one sees the seams between them, which is where the interesting findings are.
3. **Adjudicate.** Present the two axes. Findings the user wants fixed become new tickets and go back through Stage 3 — never patched inline by you.
4. **Report.** The goal, the tickets that landed with their links, the commits, the review verdict, and anything deliberately left undone.

Do not close or modify the parent spec issue.
