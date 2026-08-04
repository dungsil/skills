---
name: vibe-deep-plan
description: Charts a huge chunk of work — more than one agent session can hold — as a shared map of decision tickets on the repo's issue tracker, then resolves them one at a time until the way to the destination is clear. Use when the work is too big to plan in one session, or when the user asks to chart, extend, or work through a decision map.
metadata:
  disable-model-invocation: "true"
---

# Charting a Decision Map

A loose idea has arrived — too big for one agent session, and wrapped in fog: the way from here to the **destination** isn't visible yet. Wayfinding is about finding that way, not charging at the destination. This skill charts the way as a **shared map** on the repo's issue tracker, then works its **decision tickets** — questions whose resolution is a decision, not slices of a build to execute — one at a time until the route is clear.

The destination varies per effort, and naming it is the first act of charting — it shapes every ticket. It might be a spec to hand off and iterate on, a decision to lock before planning starts, or a change made in place like a data-structure migration. The map is domain-agnostic — engineering work, course content, whatever fits the shape.

## Plan, don't do

This skill is **planning** by default: each ticket resolves a decision, and the map is done when the way is clear — nothing left to decide before someone goes and does the thing. The pull to just do the work is usually the signal you've reached the edge of the map and it's time to hand off. An effort can override this in its **Notes** — carrying execution into the map itself — but absent that, produce decisions, not deliverables.

### When the map clears, hand off — don't build

The map is done when no open tickets remain and **Not yet specified** is empty. That is the moment to leave: run `/vibe-plan`, entering at its **spec** stage. The map's Destination and Decisions-so-far are exactly its input — every decision is already made, so there is nothing left to interview.

Going straight to `/vibe-implement` instead skips that synthesis and throws the linked detail away. Take that shortcut only when the effort turned out genuinely small — one ticket's worth of work.

### When a session fills up mid-ticket

That's a different exit: the ticket isn't resolved, you've just run out of room. Leave the ticket **claimed**, hand the thread off (see the `/vibe-handoff` skill), and resume it in a fresh session. Don't post a half-answer as the resolution comment.

## Refer by name

Every map and ticket is an issue, so it has a **name** — its title. In everything the human reads — narration, the map's Decisions-so-far — refer to it by that name, never by a bare id, number, or slug. A wall of `#42, #43, #44` is illegible; names read at a glance. The id and URL don't vanish — a name wraps its link — but they ride *inside* the name, never stand in for it.

## The Map

The map is a single issue on this repo's issue tracker, labelled `상태:초안` — the canonical artifact. Its tickets are child issues of the map.

The map is an **index**, not a store. It lists the decisions made and points at the tickets that hold their detail; a decision lives in exactly one place — its ticket — so the map never restates it, only gists it and links.

`상태:초안` and the triage states are the same axis, so they are **mutually exclusive**. A map is an index of an in-flight effort, not a request awaiting evaluation — it never carries `상태:분류필요` or any other triage state, and it is never triaged. The same holds for its decision tickets: they carry a `유형:` label and nothing from the `상태:` axis, because a question the map already owns has nothing left to triage. Only when the map clears and `/vibe-plan` publishes implementation tickets do the triage states apply again.

**Where the map, its child tickets, blocking, and frontier queries physically live is tracker-specific.** The issue tracker should have been provided to you — run `/vibe-init` if not. Consult the tracker doc's "Wayfinding operations" section for how _this_ repo expresses them. If no tracker has been provided, default to the local-markdown tracker.

### The map body

The whole map at low resolution, loaded once per session. Open tickets are **not** listed — they are open child issues, found by query.

```markdown
## Destination

<what reaching the end of this map looks like — the spec, decision, or change this effort is finding its way to. One or two lines; every session orients to it before choosing a ticket.>

## Notes

<domain; skills every session should consult; standing preferences for this effort>

## Decisions so far

<!-- the index — one line per closed ticket: enough to judge relevance, then zoom the link for the detail the ticket holds -->

- [<closed ticket title>](link) — <one-line gist of the answer>

## Not yet specified

<!-- see "Fog of war": in-scope fog you can't ticket yet; graduates as the frontier advances -->

## Out of scope

<!-- see "Out of scope": work ruled beyond the destination; closed, never graduates -->
```

### Tickets

Each ticket is a **child issue** of the map; the tracker's issue id is its identity. Its body is the question, sized to one 100K token agent session:

```markdown
## Question

<the decision or investigation this ticket resolves>
```

Each ticket carries a `유형:` label — one of `유형:조사` (research), `유형:프로토타입` (prototype), `유형:인터뷰` (grilling), `유형:작업` (task) (see [Ticket Types](#ticket-types)).

A session **claims** a ticket by assigning it to the dev driving the map, **first**, before any work, so concurrent sessions skip it. That assignee _is_ the claim: an open, unassigned ticket is unclaimed.

Blocking uses the tracker's **native** dependency relationship — essential because it renders the frontier _visually_ in the tracker's own UI, so the human sees what's takeable without opening the map. Only a tracker that lacks native blocking falls back to a body convention. A ticket is **unblocked** when every ticket blocking it is closed; the **frontier** is the open, unblocked, unclaimed children — the edge of the known.

The question stays intact in the ticket body. Non-resolution research findings may be attached through the tracker's comment mechanism; record the final answer only through its resolution operation (see [Work through the map](#work-through-the-map)). Assets created while resolving a ticket are linked from the issue, not pasted in.

## Ticket Types

Every ticket is either **HITL** — human in the loop, worked *with* a human who speaks for themselves — or **AFK**, driven by the agent alone. For an AFK research ticket, only the background investigation is delegated: that subagent may inspect documentation, public or read-only APIs, and local resources, but it must not write files, publish artifacts or branches, modify the map or tickets, use credentials, or cause an **external side effect**. The calling session reviews the returned result and owns any ticket or map update. A HITL ticket only resolves through its live exchange; the agent never stands in for the human's side of it (a grilling agent that answers its own questions has broken this).

- **Research** (AFK investigation): A read-only subagent investigates documentation, third-party APIs, or local resources like knowledge bases and returns source-cited findings and remaining unknowns to its caller; the calling session resolves the ticket under [RESEARCH.md](RESEARCH.md). Use when knowledge outside the current working directory is required.
- **Prototype** (HITL): Raise the fidelity of the discussion by making a cheap, rough, concrete artifact to react to — an outline, a rough take, a stub, or UI/logic code. Resolved by building throwaway code — see [PROTOTYPE.md](PROTOTYPE.md) to pick the branch, then [PROTOTYPE-LOGIC.md](PROTOTYPE-LOGIC.md) for state/logic questions or [PROTOTYPE-UI.md](PROTOTYPE-UI.md) for "what should it look like". Links the prototype as an asset. Use when "how should it look" or "how should it behave" is the key question.
- **Grilling** (HITL): Conversation via the /vibe-grilling and /vibe-modeling skills, one question at a time. The default case.
- **Task** (HITL): Manual work that must happen before a *decision* can be made — nothing to decide, prototype, or research, but the discussion is blocked until it is done. Signing up for a service, provisioning access, or moving data are human-attended external side effects, never AFK work. Before approval, provide read-only findings and an exact, target-specific human checklist. Resolve only after authorized work is complete; record resulting facts needed by later tickets, but for credentials record only their type and safe-store reference.

### Human-attended external side effects

**Account creation, permission changes, data movement, and credential use** are separate categories of human-attended **external side effects**, always HITL regardless of ticket type. A map, plan, prior approval, or broad “go ahead” never grants blanket authority.

Before requesting approval, complete only permitted read-only investigation and return its findings with a numbered, target-specific checklist the human can follow. Immediately before executing **each** category, preview:

- the target;
- the exact action;
- the expected impact and scope; and
- reversibility, including the rollback or recovery path.

Ask for and receive a separate affirmative approval that names that category before its first action. Approval for one category never authorizes another; re-preview and ask again if the target, exact action, or scope changes. A refusal, ambiguity, or no response is no approval: leave the ticket open and blocked, do not post a resolution comment, close it, or update **Decisions so far**, and cause no external side effect.

Never record credential values in maps, tickets, comments, resolution text, commands or output logs, or research artifacts. Retain only the credential type and its safe-store reference; credential use still requires its own just-in-time approval.

## Fog of war

The map is _deliberately_ incomplete: don't chart what you can't yet see. Beyond the live tickets lies the **fog of war** — the dim view of decisions and investigations you can tell are coming but can't yet pin down, because they hang on questions still open. Resolving a ticket clears the fog ahead of it, graduating whatever's now specifiable into fresh tickets — one at a time, until the way to the destination is clear and no tickets remain.

The map's **Not yet specified** section is where that dim view is written down: the suspected question, the area to revisit later. It's the undiscovered frontier _toward_ the destination — everything here is in scope, just not sharp enough to ticket. Write as loosely or as fully as the view allows; it doubles as a signpost for collaborators reading where the effort is headed.

**Fog or ticket?** The test is whether you can state the question precisely now — _not_ whether you can answer it now.

- **Ticket when** the question is already sharp — even if it's blocked and you can't act on it yet.
- **Not yet specified when** you can't yet phrase it that sharply. Don't pre-slice the fog into ticket-sized pieces: it's coarser than a ticket, and one patch may graduate into several tickets, or none, once the frontier reaches it.

**Not yet specified** excludes what's already decided (Decisions so far), what's already a live ticket, and what's out of scope (the next section).

## Out of scope

Fog only ever gathers _toward_ the destination. The destination fixes the scope, so work beyond it is **out of scope** — it isn't fog, and it doesn't belong in **Not yet specified**. It gets its own **Out of scope** section on the map: work you've consciously ruled out of _this_ effort. Scope, not sharpness, lands it here.

Out-of-scope work never graduates — the frontier stops at the destination — so it returns only if the destination is redrawn, and then as a fresh effort, not a resumption.

Ruling something out of scope is a scoping act, not a step on the route. When a ticket that already exists turns out to sit past the destination — mis-scoped in while charting, or exposed by a resolution — **close it** (a closed ticket is unambiguously off the frontier) and leave one line in the **Out of scope** section: the gist plus why it's out of scope, linking the closed ticket. It stays out of **Decisions so far**, which records the route actually walked — a scope boundary isn't a step on it.

## Invocation

Two modes. By default, a work session claims and resolves no more than one ticket. Charting-time research attachments are handoff records, not resolutions, and do not count against that limit. An explicit user request may continue the same charting invocation into parallel work on multiple **named, unblocked HITL** tickets; only those named tickets join the exception, and every claim, human exchange, approval, resolution, and map update still follows its normal rule.

### Chart the map

User invokes with a loose idea.

1. **Name the destination.** Run a `/vibe-grilling` and `/vibe-modeling` session to pin down what this map is finding its way to — the spec, decision, or change. The destination fixes the scope, so it's settled first.
2. **Map the frontier.** Grill again, **breadth-first** this time: fan out across the whole space rather than deep on any one thread, surfacing the open decisions and the first steps takeable now. **If this surfaces no fog** — the way to the destination is already clear, the whole journey small enough for one session — you don't need a map. Stop and ask the user how they'd like to proceed.
3. **Create the map** (label `상태:초안`): Destination and Notes filled in, Decisions-so-far empty, the fog sketched into **Not yet specified**.
4. **Create the tickets you can specify now** as child issues of the map — then wire blocking edges in a **second pass** (issues need ids before they can reference each other). Wiring sorts them into the frontier and the blocked; everything you can't yet specify stays in the fog — the **Not yet specified** section.
5. **Fire the read-only research subagents.** Temporarily claim each `research` ticket before dispatch, then run its AFK investigation in parallel under [RESEARCH.md](RESEARCH.md). The subagents return source-cited findings to the calling session and never write files, artifacts, branches, tickets, or the map.
6. **Honor an explicit parallel-work request.** If the user asks to use the wait for an interview or another named unblocked HITL ticket, transition that ticket into **Work through the map** and claim it before work. Multiple named HITL lanes may be interleaved, but each asks one question at a time, waits for the user's own answer, and never answers on the user's behalf. Do not infer this exception from idle time or add unnamed tickets.
7. **Persist every research return before exit.** Await every investigation launched in step 5. The calling session attaches each complete result to its research ticket as a non-resolution hosted comment/note or local `## Comments`, then releases the temporary claim. Do not close the ticket, add `## Answer`, post a resolution, or update **Decisions so far**. If a launched investigation cannot finish, leave its ticket open and claimed and hand it off explicitly.
8. Stop only after every launched result is attached or explicitly handed off; charting itself resolves nothing unless the user invoked the step 6 exception.

### Work through the map

User invokes with a map (URL or number). A ticket is **optional** — without one, you pick the next decision, not the user.

1. Load the **map** — the low-res view, not every ticket body.
2. Choose the ticket. If the user named one, use it; otherwise take the first frontier ticket in order. Read its claim state before changing it. Resume a claim owned by this session or transferred through an explicit handoff. Never infer staleness from age, silence, or attached findings, and never silently steal another owner's claim. Reclaim only when the user or current owner explicitly says the prior session is abandoned; immediately reread the ticket to confirm no newer owner or activity, then replace the claim once. An ownerless local `Status: claimed` is stale only under that same explicit direction. Otherwise choose another frontier ticket or leave it untouched.
3. **Claim before substantive work**, then load the ticket's full body, comments, and attachments. For research, reuse complete current findings instead of rerunning the investigation. Check source, version, date, scope, and assumptions; preserve stale history, mark changed or contradicted claims superseded, and investigate only missing or changed evidence. Invoke the skills named in `## Notes`. If the ticket would cause an external side effect, first return read-only findings and an exact human checklist, then follow [Human-attended external side effects](#human-attended-external-side-effects) for every category. If in doubt, use `/vibe-grilling` and `/vibe-modeling`.
4. Record the resolution only when the answer is complete and current and the ticket requires no external side effect, or every required category was independently approved and completed. A research attachment is not a resolution. Research follows [RESEARCH.md](RESEARCH.md); for other ticket types, post the answer as a resolution comment, close the issue, and append a context pointer to **Decisions so far**. If approval is refused or absent, leave the ticket open and blocked; do not record a resolution, close it, update the map, or cause an external side effect.
5. Add newly-surfaced tickets (create-then-wire); graduate any fog the answer has made specifiable, clearing each graduated patch from **Not yet specified** so it lives only as its new ticket. If the answer reveals a ticket — this one or another — sits beyond the destination, **rule it out of scope** rather than resolving it on the route. If the decision invalidates other parts of the map, update or delete those tickets.

The user may run unblocked tickets in parallel, so expect other sessions to be editing the tracker concurrently.
