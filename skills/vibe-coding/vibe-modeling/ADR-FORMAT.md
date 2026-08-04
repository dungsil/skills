# ADR Format

Store ADRs in `docs/adr/` and number them in order: `0001-slug.md`, `0002-slug.md`, and later numbers.

Create the `docs/adr/` directory only when the first ADR is needed.

## Template

```md
# {Short decision title}

{1–3 sentences: context, decision, and reason.}
```

An ADR can have one paragraph. Record the decision and its reason. No other sections are required.

## Optional sections

Include a section only when it adds useful information. Most ADRs do not need extra sections.

- **Status** frontmatter (`proposed | accepted | deprecated | superseded by ADR-NNNN`) — use when a decision is reconsidered
- **Considered Options** — use when rejected options need to be remembered
- **Consequences** — use when effects on other parts of the system need to be stated

## Numbering

Scan `docs/adr/` for the highest existing number and add one.

## When to offer an ADR

Do not create an ADR automatically. Offer an ADR only when all three conditions are true:

1. **Hard to reverse** — the cost of changing the decision later is meaningful.
2. **Reason not clear from code** — a future reader cannot determine the reason from the code.
3. **Actual comparison of alternatives** — real options were compared and one was chosen for specific reasons.

Do not offer an ADR for a decision that is easy to reverse. Do not offer an ADR when its reason is clear from the code. Do not offer an ADR when no real alternative was compared.

### Decisions that meet these conditions

- **Architecture decisions.** "We use a monorepo." "The write model uses events, and the read model is in Postgres."
- **Integration patterns between contexts.** "Ordering and Billing use domain events, not synchronous HTTP."
- **Technology choices that would take about three months to replace.** Database, message bus, auth provider, deployment target. Do not record every library. Record only choices that would take about three months to replace.
- **Boundary and scope decisions.** "Customer data belongs to the Customer context. Other contexts use its ID only." State both what a context owns and what it does not own.
- **Intentional choices that differ from normal expectations.** "We use manual SQL instead of an ORM because X." Record the choice when a reasonable reader would expect a different choice. State that the choice is intentional and give the reason.
- **Constraints not visible in the code.** "We cannot use AWS because of compliance requirements." "Response times must be under 200ms because of the partner API contract."
- **Rejected alternatives when the reason is not clear.** If GraphQL was considered and REST was chosen for specific reasons, record those reasons. Otherwise, a future reader may suggest GraphQL without knowing why it was rejected.
