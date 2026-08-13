---
name: vibe-next-plan
description: Surveys the codebase for grounded product direction — 4–6 evidence-backed suggestions for what to build next — then grills the user's pick into a spec or decision map for the planning skills. Read-only on source code. Use when the user asks "what should we build next", wants a roadmap or feature ideas, or needs to decide the next move.
disable-model-invocation: true
metadata:
  argument-hint: "Optional focus — a module, subsystem, or theme to narrow the survey"
---

# Surveying What to Build Next

A **direction survey**: read the codebase, find what it wants to become, and present grounded options the maintainer can act on. This skill is the `improve next` variant moved into the vibe-coding family — it produces **decisions, not deliverables**, and hands off to the planning skills.

The survey is **read-only on source code**. It creates **no persistent files** — not under `src/`, not under `.agents/plans/`, not under `docs/agents/out-of-scope/`. The deliverable is the in-conversation report; downstream planning skills own every file. No edits, no fixes, no "quick wins while you're in there."

## Why a separate skill

`/vibe-plan` starts from a request the user already has. This skill is for when the user **doesn't have one yet** — they want to know what's worth doing. It does the recon and direction audit that `improve next` does, then hands its output to the same planning pipeline `vibe-plan` and `vibe-deep-plan` already consume.

## Workflow

### Phase 1 — Recon

Map the territory before judging it. The recon facts scope the direction search and feed every suggestion's evidence.

- Read `README`, `AGENTS.md`/`CLAUDE.md`, `CONTRIBUTING`, root config files (`package.json`, `pyproject.toml`, `go.mod`, etc.), CI config, and the directory structure.
- Read the project's domain glossary (`CONTEXT.md`) and any ADRs in the area the user named — the vocabulary is what makes suggestions grounded instead of generic.
- Identify: language(s), framework(s), package manager, how to build / test / lint / typecheck (exact commands — these go into downstream plans as verification gates), test coverage shape, deployment target.
- Note repo conventions: code style, naming, folder layout, error-handling and state-management patterns.
- Check git signal where useful (`git log --oneline -30`, churn hotspots) for what's actively evolving vs. frozen. High-churn areas are where direction is most valuable — that's where the maintainer is already investing.

If the user named a focus — a module, a subsystem, a theme — weight the recon and the audit toward it. Skip the inference below.

Otherwise, let churn hotspots pull your attention first. If changes are scattered with no clear hot spot, widen the net.

### Phase 2 — Direction audit

Audit only the **direction** category: not what's broken, but what this codebase wants to become. Aim for **4–6 grounded suggestions**.

For a repo of any real size, fan out with parallel read-only subagents. If the host agent can't spawn subagents, audit directly. Subagents do not inherit this skill's context, so each subagent prompt must include:

- The recon facts that scope the search (languages, frameworks, key directories, what to skip).
- The domain glossary terms from `CONTEXT.md` — so suggestions use the project's own names.
- The grounding rule (below) and the finding format (below), pasted in full or pointed at by absolute path.
- An explicit instruction to return suggestions only — no fixes, no file dumps.

#### Grounding rule

Every suggestion must cite **evidence from the repo itself**. A suggestion that could apply to any project in the category ("add dark mode", "add AI") is noise, not a finding. Sources of grounded direction signal:

- **Unfinished intent** — TODO/FIXME clusters around one theme, feature flags never rolled out, stubbed or half-built modules, commented-out feature code, abandoned mid-feature work visible in git history.
- **Stated-but-undelivered** — README/docs/roadmap promises with no corresponding code, CLI flags or config options that are no-ops, issue templates for features that don't exist.
- **Surface asymmetries** — one-directional pairs (export without import, create without bulk-create, webhooks out but not in), entities with CRUD minus one, a public API that internal code clearly needed and hand-rolled around.
- **The adjacent possible** — capabilities the existing architecture makes disproportionately cheap: a plugin system one interface away, a public API one route file from the existing service layer, an integration the data model already supports.
- **Friction worth productizing** — things users of this project evidently do by hand around it (visible in docs, examples, issues) that the project could absorb.

#### Finding format

Every suggestion comes back in this shape:

```markdown
### [DIRECTION-NN] Short imperative title

- **Evidence**: `path/file.ts:123` — one-sentence description of what's there. (Repeat per location; 2–5 strongest locations, note "and ~N similar sites" if widespread.)
- **Impact**: Product/user value — who wants this and why now. Concrete, not "would be nice".
- **Effort**: S (hours) / M (a day-ish) / L (multi-day) — coarse; say so. Direction estimates are rougher than fix estimates.
- **Risk**: What building this could cost or break; LOW/MED/HIGH plus one line why.
- **Confidence**: HIGH (strong repo evidence) / MED (signal, needs validation) / LOW (smell, needs investigation).
- **Trade-offs**: 2–3 sentences. What this opens up, what it forecloses, what it costs to maintain.
```

### Phase 3 — Vet

**Vet before presenting — subagents over-report.** For every suggestion that will make the table, open the cited code yourself and confirm it. Expect three failure classes: **by-design behavior** mistaken for unfinished work (a no-op flag that's intentionally a placeholder); **mis-attributed evidence** (real signal, wrong file or line); and duplicates across subagents. Downgrade, correct, or reject accordingly.

A suggestion that fails the grounding test — generic enough to apply to any project — is rejected, not downgraded. Record rejections in the report's "considered and rejected" section so they aren't re-surfaced next run.

### Phase 4 — Present

Present the vetted suggestions as a table, ordered by leverage (impact ÷ effort, weighted by confidence):

| # | Suggestion | Impact | Effort | Risk | Confidence | Evidence |

Follow the table with each suggestion's full **Trade-offs** — the maintainer weighs these, not the surveyor. Do not rank them into a single "best pick"; the maintainer decides.

Then ask which suggestion to take forward. Default suggestion: the top 1–2 by leverage. Also surface **dependency ordering** — "suggestion 2 builds on the data model 3 already has, so 3 should land first if both are picked."

Wait for the selection. Do not plan anything nobody asked for.

### Phase 5 — Hand off

For each selected suggestion, hand off to the right downstream skill. This skill **does not write plans itself** — it produces the direction, then lets the planning pipeline take over.

1. **If the suggestion is small enough for one planning session** — hand off to `/vibe-plan`. Pass the suggestion's evidence, impact, trade-offs, and the recon facts (build/test/lint commands, conventions, ADRs) as the starting input. `/vibe-plan` enters at its **grill** stage and sharpens it into a spec.

2. **If the suggestion is too big for one session** — fog between here and the destination — hand off to `/vibe-deep-plan`. Pass the same starting material. `/vibe-deep-plan` charts a decision map.

3. **If the maintainer wants to think before planning** — stop here. The survey is the deliverable. The maintainer can come back to `/vibe-plan` or `/vibe-deep-plan` later with a selected suggestion.

State which handoff you recommend and why, in one line, then wait.

## What this skill never does

- **Never edit source code.** No fixes, no implementations, no "quick wins."
- **Never write implementation plans.** That's `/vibe-plan`'s job.
- **Never create tickets on the tracker.** That's `/vibe-plan` or `/vibe-deep-plan`'s job.
- **Never reproduce secret values.** If the survey finds credentials, reference the `file:line` and credential type only — never the value.
- **Never re-litigate an ADR.** If a suggestion contradicts a recorded ADR, surface the conflict and mark it — don't override it.

## Tone

Advising, not selling. State suggestions plainly with evidence, flag uncertainty honestly, and prefer "not worth doing" verdicts over padding the list. A short list of high-confidence, high-leverage suggestions beats a long one.
