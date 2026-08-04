---
name: vibe-modeling
description: Define a project's domain terms and record important architecture decisions. Use when the user wants to define domain terms, record an architecture decision, or when another skill must update the domain model.
---

# Vibe Modeling

Use this skill when the domain model changes. Read the glossary before changing terms. Use concrete cases for unclear relationships. Record agreed terms immediately. Propose an ADR only for a decision that meets the ADR conditions. Do not use this skill only to read `CONTEXT.md`.

## File structure

Most repositories have one context:

```
/
├── CONTEXT.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

If a root `CONTEXT-MAP.md` exists, the repository has multiple contexts. The map lists each context and its location:

```
/
├── CONTEXT-MAP.md
├── docs/
│   └── adr/                          ← system-wide decisions
├── src/
│   ├── ordering/
│   │   ├── CONTEXT.md
│   │   └── docs/adr/                 ← context-specific decisions
│   └── billing/
│       ├── CONTEXT.md
│       └── docs/adr/
```

Create a file only when it has content. If `CONTEXT.md` is absent, create it after the first term is agreed. If `docs/adr/` is absent, create it only when the first ADR is needed.

## During the session

### Use direct language

- Use short, literal sentences in every question, explanation, and record. Do not use idioms, metaphors, or culture-dependent expressions.
- In `CONTEXT.md`, write every canonical term as `한국어 (English)`. Write Korean first. Write definitions in Korean.
- Use CEFR B1 or lower vocabulary for Korean terms, definitions, questions, explanations, and records.
- The CEFR A1–A2 rule applies only to English names. Do not apply it to Korean.
- For every general English name, use an everyday CEFR A1–A2 word. This is required.
- Use an English technical term only when it is clearly established as that domain name in Korea. This is the only exception to the English name rule.

### Compare with the glossary

When a user uses a term or meaning that conflicts with `CONTEXT.md`, state the conflict immediately. Ask: "`CONTEXT.md` defines `취소 (Cancel)` as X. Do you mean X or Y?"

### Make unclear language precise

When a user uses a term with more than one meaning, propose one precise canonical term. Ask: "Do you mean `고객 (Customer)` or `사용자 (User)`? They are different concepts."

### Use concrete cases

When domain relationships are discussed, use concrete cases. Check the boundary between concepts and relevant edge cases. Ask the user to state which concept includes each case.

### Compare with code

When the user states how something works, compare the statement with the code. If they differ, state the difference: "The code cancels the whole `주문 (Order)`. You said part of an order can be cancelled. Which behavior is correct?"

### Update CONTEXT.md immediately

When a term is agreed, update `CONTEXT.md` immediately. Do not delay the record. Use the format in [CONTEXT-FORMAT.md](CONTEXT-FORMAT.md).

`CONTEXT.md` contains only glossary terms. Exclude implementation details, specifications, notes on current work, and design decisions.

### Propose ADRs only when required

Do not create an ADR automatically. Propose an ADR only when all three conditions are true:

1. **Hard to reverse** — changing the decision later has a meaningful cost.
2. **Reason not clear from code** — a future reader cannot determine the reason from the code.
3. **Actual comparison of alternatives** — real options were compared and one was chosen for specific reasons.

If one condition is absent, do not propose an ADR. Use the format in [ADR-FORMAT.md](ADR-FORMAT.md).
