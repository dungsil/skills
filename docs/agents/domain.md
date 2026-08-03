# Domain Docs

How the engineering skills consume this repository's domain documentation.

## Before exploring

Read the root `CONTEXT.md` and relevant records under `docs/adr/` when they exist. Missing files are not an error; `/vibe-modeling` creates them lazily when terms or decisions are resolved.

## Layout

This is a single-context repository:

```text
/
├── CONTEXT.md
└── docs/adr/
```

## Vocabulary and decisions

Use glossary terms consistently in issue titles, specifications, hypotheses, and tests. If work contradicts an ADR, surface the conflict instead of silently overriding it.
