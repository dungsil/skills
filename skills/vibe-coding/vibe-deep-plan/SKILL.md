---
name: vibe-deep-plan
description: Charts a huge chunk of work — more than one agent session can hold — as a shared map of decision tickets on the repo's issue tracker, then resolves them one at a time until the way to the destination is clear. Use when the work is too big to plan in one session, or when the user asks to chart, extend, or work through a decision map.
disable-model-invocation: true
---

# Charting a Decision Map

A loose idea has arrived — too big for one agent session, and wrapped in fog: the way from here to the **destination** isn't visible yet. Wayfinding is about finding that way, not charging at the destination. This skill charts the way as a **shared map** on the repo's issue tracker, then works its **decision tickets** — questions whose resolution is a decision, not slices of a build to execute — one at a time until the route is clear.

The destination varies per effort, and naming it is the first act of charting — it shapes every ticket. It might be a spec to hand off and iterate on, a decision to lock before planning starts, or a change made in place like a data-structure migration. The map is domain-agnostic — engineering work, course content, whatever fits the shape.

## Plan, don't do

This skill is **planning** by default: each ticket resolves a decision, and the map is done when the way is clear — nothing left to decide before someone goes and does the thing. The pull to just do the work is usually the signal you've reached the edge of the map and it's time to hand off. An effort can override this in its **Notes** — carrying execution into the map itself — but absent that, produce decisions, not deliverables.

### When the map clears, hand off — don't build

The map is done when no open tickets remain and **Not yet specified** is empty. Hand off to `/vibe-plan` at its **spec** stage. For Local Markdown, pass the cleared `map.md` path; `/vibe-plan` reuses its parent `.agents/plans/<effort>/` directory, writes `spec.md` there in Stage 2, and writes implementation tickets under that same directory's `issues/` in Stage 3. For GitHub, GitLab, or another hosted tracker, pass the map URL or number; `/vibe-plan` reads the map and every linked closed ticket. First it loads each linked decision's question and final answer: for Local Markdown this is `## Question` and `## Answer`, while hosted trackers use the issue question/body and final resolution comment or note. It follows raw research, comments, attachments, or prototype artifacts only when the final answer references them or the spec needs their evidence. The map is an index, not a store: its Destination and Decisions-so-far plus those linked decisions are the handoff input, so there is nothing left to interview.

Going straight to `/vibe-implement` instead skips that synthesis and throws the linked detail away. Take that shortcut only when the effort turned out genuinely small — one ticket's worth of work.

### When a session fills up mid-ticket

That's a different exit: the ticket isn't resolved, you've just run out of room. Leave the ticket **claimed**, hand the thread off (see the `/vibe-handoff` skill), and resume it in a fresh session. Don't post a half-answer as the resolution comment.

## Refer by name

Every map and ticket has a **name**. On hosted trackers, the name is the issue title; in Local Markdown, the map is `map.md` and the decision record's filename/path is its identity. In everything the human reads — narration and the map's Decisions-so-far — refer to that name, never by a bare id, number, or slug. A hosted id or URL and a local path remain available inside the name or link.

## The Map

On hosted trackers, the map is a single issue labelled `상태:초안`, and its tickets are child issues. In Local Markdown, the canonical map is `.agents/plans/<effort>/map.md`, and its tickets are type-specific decision records under `research/`, `interviews/`, `prototypes/`, or `tasks/`.

The map is an **index**, not a store. It lists the decisions made and points at the tickets that hold their detail; a decision lives in exactly one place — its ticket — so the map never restates it, only gists it and links.

`상태:초안` and the hosted triage states are the same axis, so they are **mutually exclusive**. Hosted maps and their decision issues never carry triage states. Local maps have no triage label; Local Markdown decision records use the separate `Status: open` → `claimed` → `open`/`resolved` lifecycle. Only when the map clears and `/vibe-plan` publishes implementation tickets do the triage states apply again.

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

Each ticket is a child decision item of the map. Hosted trackers represent it as a child issue with a tracker id; Local Markdown stores it as a type-specific decision record whose path is its identity. Its body is the question, sized to one 100K token agent session:

```markdown
## Question

<the decision or investigation this ticket resolves>
```

Hosted tickets carry one `유형:` label — `유형:조사`, `유형:프로토타입`, `유형:인터뷰`, or `유형:작업`. Local Markdown records carry the matching `Type:` line (see [Ticket Types](#ticket-types)).

Before any work, a session **claims** a ticket so concurrent sessions skip it. Use the configured tracker’s claim operation: hosted trackers assign the ticket to the driving dev; Local Markdown starts unclaimed records with `Status: open`, changes them to `Status: claimed` before work, and returns them to `Status: open` after successful charting persistence. A final answer changes the local record to `Status: resolved`; unfinished handoff or failed persistence stays `claimed`. Local Markdown never writes an `Assignee:` field.

Blocking uses the tracker's **native** dependency relationship — essential because it renders the frontier _visually_ in the tracker's own UI, so the human sees what's takeable without opening the map. Only a tracker that lacks native blocking falls back to a body convention. A ticket is **unblocked** when every ticket blocking it is closed; the **frontier** is the open, unblocked, unclaimed children — the edge of the known.

The question stays intact in the ticket body; research persistence follows [RESEARCH.md](RESEARCH.md).
Research persistence is tracker-specific: Local Markdown keeps each research ticket and its complete findings in `.agents/plans/<effort>/research/<ticket-stem>.md`; interview, prototype, and task records live under `.agents/plans/<effort>/interviews/<ticket-stem>.md`, `.agents/plans/<effort>/prototypes/<ticket-stem>.md`, and `.agents/plans/<effort>/tasks/<ticket-stem>.md`. `.agents/plans/<effort>/issues/` is reserved for implementation tickets published by `/vibe-plan`. Hosted trackers keep the complete findings in the ticket's comment or note, or in a linked durable snippet, wiki page, attachment, or equivalent. No tracker creates a research-only branch.

## Ticket Types

Every ticket is either **HITL** — human in the loop, worked *with* a human who speaks for themselves — or **AFK**, driven by the agent alone. For an AFK research ticket, only the background investigation is delegated: that subagent may inspect documentation, public or read-only APIs, and local resources, but it must not write files, publish artifacts or branches, modify the map or tickets, use credentials, or cause an **external side effect**. The caller receives its findings with primary-source citations and persists them under [RESEARCH.md](RESEARCH.md). A HITL ticket only resolves through its live exchange; the agent never stands in for the human's side of it (a grilling agent that answers its own questions has broken this).

- **Research** (AFK investigation): A read-only subagent returns findings with primary-source citations and remaining unknowns; the caller persists them under [RESEARCH.md](RESEARCH.md). Use when knowledge outside the current working directory is required.
- **Prototype** (HITL): Raise the fidelity of the discussion by making a cheap, rough, concrete artifact to react to — an outline, a rough take, a stub, or UI/logic code. Keep every artifact under `.agents/prototype/<name>/`; it must not modify or import production source/modules, use live authentication or mutate live data, or edit project-level manifests, task runners, routes, configs, or shared components. Resolved by building throwaway code — see [PROTOTYPE.md](PROTOTYPE.md) to pick the branch, then [PROTOTYPE-LOGIC.md](PROTOTYPE-LOGIC.md) for state/logic questions or [PROTOTYPE-UI.md](PROTOTYPE-UI.md) for "what should it look like". Links the prototype as an asset. Use when "how should it look" or "how should it behave" is the key question.
- **Grilling** (HITL): Conversation via the /vibe-grilling and /vibe-modeling skills, one question at a time. The default case.
- **Task** (HITL): Manual work that must happen before a *decision* can be made — nothing to decide, prototype, or research, but the discussion is blocked until it is done. Signing up for a service, provisioning access, or moving data are human-attended external side effects, never AFK work. Before approval, provide read-only findings and an exact, target-specific human checklist. Resolve only after authorized work is complete; record resulting facts needed by later tickets, but for credentials record only their type and safe-store reference.

### Human-attended external side effects

**Account creation, permission changes, data movement, and credential use** are separate categories of human-attended **external side effects**, always HITL regardless of ticket type. A map, plan, prior approval, or broad “go ahead” never grants blanket authority.

Before requesting approval, complete only permitted read-only investigation and return its findings with a numbered, target-specific checklist the human can follow. Immediately before executing **each** category, preview:

- the target;
- the exact action;
- the expected impact and scope; and
- reversibility, including the rollback or recovery path.

Ask for and receive a separate affirmative approval that names that category before its first action. Approval for one category never authorizes another; re-preview and ask again if the target, exact action, or scope changes. A refusal, ambiguity, or no response is no approval: for Local Markdown, keep the record at `Status: claimed` so it stays out of the frontier; for hosted trackers, keep the issue open, assigned, and blocked; do not append `## Answer`, post a resolution comment or note, set `Status: resolved`, close the issue, update **Decisions so far**, or cause any external side effect.

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

Two modes. By default, a work session claims and resolves no more than one ticket. Research persistence during charting follows [RESEARCH.md](RESEARCH.md); it is a handoff record, not a resolution, and does not count against that limit. An explicit user request may continue the same charting invocation into parallel work on multiple **named, unblocked HITL** tickets; only those named tickets join the exception, and every claim, human exchange, approval, resolution, and map update still follows its normal rule.

### Chart the map

User invokes with a loose idea.

1. **Name the destination.** Run a `/vibe-grilling` and `/vibe-modeling` session to pin down what this map is finding its way to — the spec, decision, or change. The destination fixes the scope, so it's settled first.
2. **Map the frontier.** Grill again, **breadth-first** this time: fan out across the whole space rather than deep on any one thread, surfacing the open decisions and the first steps takeable now. **If this surfaces no fog** — the way to the destination is already clear, the whole journey small enough for one session — you don't need a map. Stop and ask the user how they'd like to proceed.
3. **Create the map**: hosted trackers create the map issue with `상태:초안`; Local Markdown writes `.agents/plans/<effort>/map.md` without a hosted triage label. Destination and Notes are filled in, Decisions-so-far is empty, and the fog is sketched into **Not yet specified**.
4. **Create the tickets you can specify now** as child tickets of the map — hosted trackers wire child issues in a second pass because they need ids before they can reference each other; Local Markdown writes the type-specific records in the matching `research/`, `interviews/`, `prototypes/`, or `tasks/` directory. Wiring sorts them into the frontier and the blocked; everything you can't yet specify stays in the fog — the **Not yet specified** section.
5. **Fire the read-only research subagents.** Temporarily claim each `research` ticket before dispatch, launch AFK investigations in parallel, wait for every return, and have the caller persist each result under [RESEARCH.md](RESEARCH.md) before exit.
6. **Honor an explicit parallel-work request.** If the user asks to use the wait for an interview or another named unblocked HITL ticket, transition that ticket into **Work through the map** and claim it before work. Multiple named HITL lanes may be interleaved, but each asks one question at a time, waits for the user's own answer, and never answers on the user's behalf. Do not infer this exception from idle time or add unnamed tickets.
7. **Finish charting.** Do not resolve research tickets while charting; after successful persistence, a hosted research issue remains open with the session claim released, while a Local Markdown research record is `Status: open`. Any ticket without a persisted result stays claimed for explicit handoff.

### Work through the map

User invokes with a map path, URL, or number. A ticket is **optional** — without one, you pick the next decision, not the user.

1. Load the **map** — the low-res view, not every ticket body.
2. Choose the ticket. If the user named one, use it; otherwise take the first frontier ticket in order. Read its claim state before changing it. Resume a claim owned by this session or transferred through an explicit handoff. Never infer staleness from age, silence, or attached findings, and never silently steal another owner's claim. Reclaim only when the user or current owner explicitly says the prior session is abandoned; immediately reread the ticket to confirm no newer owner or activity, then replace the claim once. An ownerless local `Status: claimed` is stale only under that same explicit direction. Otherwise choose another frontier ticket or leave it untouched.
3. **Claim before substantive work**, then load the ticket's full body, comments, and attachments. For research, inspect the canonical local note or hosted comment/artifact before rerunning; if it is missing or unusable, inspect every configured durable location first under [RESEARCH.md](RESEARCH.md). Invoke the skills named in `## Notes`. If the ticket would cause an external side effect, first return read-only findings and an exact human checklist, then follow [Human-attended external side effects](#human-attended-external-side-effects) for every category. If in doubt, use `/vibe-grilling` and `/vibe-modeling`.
4. Record the resolution only when the answer is complete and the ticket requires no external side effect, or every required category was independently approved and completed. A research record or pointer is not a resolution. For research, follow [RESEARCH.md](RESEARCH.md): Local Markdown appends `## Answer` and sets `Status: resolved`; hosted trackers post the final resolution comment or note and close the issue. For other ticket types, Local Markdown appends `## Answer` and sets `Status: resolved`; hosted trackers post the answer comment and close the issue. Then append only the linked record or ticket title and one-line gist to **Decisions so far**. If approval is refused, ambiguous, or absent, for Local Markdown keep the record at `Status: claimed` so it stays out of the frontier; for hosted trackers keep the issue open, assigned, and blocked; do not append `## Answer`, post a resolution comment or note, set `Status: resolved`, close the issue, update **Decisions so far**, or cause any external side effect.
5. Add newly-surfaced tickets (create-then-wire); graduate any fog the answer has made specifiable, clearing each graduated patch from **Not yet specified** so it lives only as its new ticket. If the answer reveals a ticket — this one or another — sits beyond the destination, **rule it out of scope** rather than resolving it on the route. If the decision invalidates other parts of the map, update or delete those tickets.

The user may run unblocked tickets in parallel, so expect other sessions to be editing the tracker concurrently.
