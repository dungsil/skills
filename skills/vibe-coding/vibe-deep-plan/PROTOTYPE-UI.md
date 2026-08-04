# UI Prototype

Generate **several radically different UI variations**, switchable from a floating bottom bar, inside a **standalone prototype app** under `.agents/prototype/<name>/`. The user flips between variants in the browser, picks one (or steals bits from each), then throws the rest away.

If the question is about logic/state rather than what something looks like — wrong branch. Use [PROTOTYPE-LOGIC.md](PROTOTYPE-LOGIC.md).

## Contents

- When this is the right shape
- The prototype is a standalone app — always
- Reproducing real context without importing it
- Process — state the question, scaffold, copy context, generate variants, wire them, build the switcher, hand over, capture
- Anti-patterns

## When this is the right shape

- "What should this page look like?"
- "I want to see a few options for this dashboard before committing."
- "Try a different layout for the settings screen."
- Any time the user would otherwise spend a day picking between three vague mockups in their head.

## The prototype is a standalone app — always

There are no sub-shapes. Whether the question is about a brand-new surface or about a section of the existing `/settings` page, the variants live in a prototype-local app with its own entrypoint under `.agents/prototype/<name>/`. **Nothing is mounted on a production route.**

The old temptation is to render variants on the real page, because a variant judged in a vacuum always looks fine. That instinct is right and the mechanism is wrong: hosting on the real route buys fidelity by paying in production diff — the host page, its data layer, its shared components, and the build config all get edited for something destined for the bin. Isolation buys the same fidelity a different way: **copy the parts of the real context that change the judgement into the prototype**.

### Hard boundaries

- **Everything lives under `.agents/prototype/<name>/`** — variant sources, the switcher, fixtures, copied shell components, styles, the dependency manifest, and the run command.
- **No imports from production source.** Not `@/components/…`, not `../../src/…`, not the real types, hooks, or API clients. If a variant needs the real sidebar, copy the file into the prototype directory and trim it down.
- **No live auth, data, or mutations.** Fixtures and in-memory state only. An affordance that would write calls a local stub that logs what it would have done.
- **No edits to production routes, shared components, build config, or manifests** — root `package.json`, lockfiles, `vite.config`, `next.config`, `tsconfig`, `Makefile`/`justfile`, CI config, route manifests. All of it stays untouched.

Run two scoped isolation checks rather than requiring a clean repository:

- **Production boundary** — compare `git status --short` captured before scaffolding with the same command afterward, ignoring unrelated pre-existing work. Confirm that no prototype-created production change appeared; any prototype-related change outside `.agents/prototype/<name>/` is a boundary violation.
- **Prototype contents** — if `.agents/prototype/<name>/` is ignored, list its files with `git ls-files --others --ignored --exclude-standard .agents/prototype/<name>/`; otherwise verify the tracked/untracked prototype diff is confined to that path. In either case, confirm the variants, switcher, fixtures, manifest, and README landed under `.agents/prototype/<name>/`.

This is also why there's no production-build gate to write. The prototype has no path into a production bundle, so there's nothing to hide behind an environment check — the isolation *is* the guard.

PROTOTYPE.md's "obey the project's routing convention" applies **inside** the prototype app. Route the prototype however its own entrypoint routes; don't add to the project's route tree.

## Reproducing real context without importing it

Fidelity is the whole reason to care about context, so don't skip it — reproduce it locally. Copy only what changes the judgement:

- **Shell** — the header, sidebar, and page chrome at their real dimensions, so each variant is judged in the space it will actually get. Either copy the real components in and trim them, or stub them as fixed-size blocks with the right labels and widths.
- **Density** — real row counts, real string lengths, real worst cases: the 40-character workspace name, the empty state, the account with 47 notification toggles. A three-row happy-path fixture makes every layout look good and teaches nothing.
- **Data shapes** — mirror the actual payload/props shape in a local fixture module, with the fields retyped inline. Copy the shape; never import the type.
- **Styling system** — the project's real system (Tailwind config values, shadcn tokens, MUI theme, plain CSS variables) reproduced by copying the relevant tokens into the prototype. Approximate rather than import.

Copy enough that the variants disagree in ways that matter, then stop. Rebuilding the app is not the goal.

**Worked example — a section of an existing page.** For "what structure should the notification section of `/settings` have": the prototype app renders a *local copy* of the settings shell — same nav, same page header, same surrounding section stack — with only the notification section swapped per variant, fed by a fixture that mirrors the real preferences payload including its long labels and its empty group. The judgement is exactly as sharp as it would be on the real route. `/settings` itself is never opened.

## Process

### 1. State the question and pick N

Default to **3 variants**. More than 5 stops being radically different and starts being noise — cap there.

Write the plan in one line, at the top of the prototype's README:

> "Three structurally different takes on the /settings notification section, in `.agents/prototype/settings-notifications/`, switchable via `?variant=`."

This works whether the user is here to push back or not.

### 2. Scaffold the prototype app

- Create `.agents/prototype/<name>/` with its **own entrypoint** — whatever is cheapest in the project's framework: a small Vite app, a standalone framework app, or a single HTML file plus one script.
- Reuse the project's framework and package manager so the variants look right, but declare any dependencies in the **prototype's own manifest**. Never add a dependency or a script to the root manifest.
- **One command to run**, documented in the prototype's README and runnable from the prototype directory — e.g. `cd .agents/prototype/<name> && pnpm dev`, or `bun run index.tsx`. Run metadata stays prototype-local; the project's task runner is not touched.
- Leave the repository's existing ignore policy unchanged; if `.agents/prototype/<name>/` is ignored, force-add that path only on the throwaway branch (Step 8).

### 3. Copy in the context

Build the local shell, fixtures, and style tokens described in [Reproducing real context](#reproducing-real-context-without-importing-it) before drafting variants. Doing it after means the variants get designed against a vacuum and then retrofitted.

### 4. Generate radically different variants

Draft each variant. Hold each one to:

- The surface's purpose and the fixture data it has access to.
- The project's component library / styling system, as reproduced locally (TailwindCSS, shadcn, MUI, plain CSS, whatever).
- A clear exported component name, e.g. `VariantA`, `VariantB`, `VariantC`.

Variants must be **structurally different** — different layout, different information hierarchy, different primary affordance, not just different colours. Three slightly-tweaked card grids isn't a UI prototype, it's wallpaper. If two drafts come out too similar, redo one with explicit "do not use a card grid" guidance.

### 5. Wire them together

A single switcher lives at the prototype app's entrypoint:

```tsx
// pseudo-code — adapt to the project's framework
import { fixture } from './fixtures';        // local fixture, never a real loader/query

const variant = searchParams.get('variant') ?? 'A';
return (
  <PrototypeShell>                            {/* local copy of the real chrome */}
    {variant === 'A' && <VariantA {...fixture} />}
    {variant === 'B' && <VariantB {...fixture} />}
    {variant === 'C' && <VariantC {...fixture} />}
    <PrototypeSwitcher variants={['A','B','C']} current={variant} />
  </PrototypeShell>
);
```

Data comes from the fixture module above the switcher; only the rendered subtree changes per variant.

### 6. Build the floating switcher

A small fixed-position bar at the bottom-centre of the screen with three pieces:

- **Left arrow** — cycles to the previous variant (wraps around).
- **Variant label** — shows the current variant key and, if the variant exports a name, that name too. e.g. `B — Sidebar layout`.
- **Right arrow** — cycles forward (wraps around).

Behaviour:

- Clicking an arrow updates the `?variant=` URL search param so the variant is **shareable and reload-stable**. Use whatever the prototype app has — the framework router (`router.replace`, `navigate`) or `history.replaceState` plus a re-render for a plain-HTML prototype.
- Keyboard: `←` and `→` arrow keys also cycle. Don't intercept arrow keys when an `<input>`, `<textarea>`, or `[contenteditable]` is focused.
- Visually distinct from the page (e.g. high-contrast pill, subtle shadow) so it's obviously not part of the design being evaluated.

The switcher is a prototype-local component — it lives next to the variants under `.agents/prototype/<name>/`, never in the project's shared UI folder.

### 7. Hand it over

Surface the run command, the URL, and the `?variant=` keys. This is the HITL half of the ticket: the user flips through whenever they get to it, and the ticket only resolves through that exchange — never pick a winner on their behalf.

The interesting feedback is usually **"I want the header from B with the sidebar from C"** — that's the actual design they want. Add it as a further variant and hand it back.

### 8. Capture the verdict — implementation is a later ticket

Once a variant has won, capture the answer — which variant, why, and any mix-and-match the user asked for — then capture the prototype the way [PROTOTYPE.md](PROTOTYPE.md) describes: the full set of variants and the switcher ride to a **throwaway branch** as the primary source; leave the repository's existing ignore policy unchanged, and force-add `.agents/prototype/<name>/` on that throwaway branch only when the path is ignored. Add a context pointer to that branch on the ticket. Post the verdict as the resolution comment, close the ticket, and append the one-line gist to the map's **Decisions so far**.

**Do not fold the winner into production code here.** This ticket resolves a *decision*; building it is separate work — a follow-up task ticket, or output of `/vibe-plan` once the map clears. So the verdict has to carry what that later work needs:

- Which variant won, and the structural decisions inside it that matter (information hierarchy, primary affordance, layout).
- Which bits were borrowed from losing variants.
- What the production version must genuinely wire to — the real route, real components, real data source and auth — none of which the prototype touched.

The main branch keeps only the recorded decision; no variant code, no switcher, no prototype directory.

## Anti-patterns

- **Variants that differ only in colour or copy.** That's a tweak, not a prototype. Real variants disagree about structure.
- **Importing from production source "just for this one component".** One import couples the throwaway to the real tree and drags its providers, types, and config along. Copy the file into the prototype directory and trim it.
- **Mounting variants on the real route**, or gating them behind a feature flag or an environment check in production code. The prototype has its own entrypoint; that's the point.
- **Touching root manifests, build config, or the task runner** to make the prototype run. If it needs a dependency or a script, declare it in the prototype's own manifest.
- **Thin fixtures.** Three tidy rows flatter every layout. Reproduce the real density and the ugly cases, or the prototype answers nothing.
- **Sharing too much code between variants.** A shared shell is fine — that's the copied context. A shared `<Layout>` defeats the point: each variant must be free to throw out the layout.
- **Wiring variants to real data, auth, or mutations.** Fixtures and logging stubs only. The question is "what should this look like", not "does the backend work".
- **Promoting the prototype directly to production.** The variant code was written under prototype constraints (no tests, minimal error handling, fake data). The follow-up ticket rewrites it properly.
