# Skills Generate
Generate [Agent Skills](https://agentskills.io/home) from project documentation.

PLEASE STRICTLY FOLLOW THE BEST PRACTICES FOR SKILL: https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices

- Focus on agents capabilities and practical usage patterns.
- Ignore user-facing guides, introductions, get-started, install guides, etc.
- Ignore content that LLM agents already confident about in their training data.
- Make the skill as concise as possible, avoid creating too many references.

## Validation

- After creating or changing any skill under `skills/`, run `bun run lint` and ensure it passes before considering the skill update complete.

## Agent skills

### Issue tracker

Issues and PRDs are tracked as local Markdown under `.agents/plans/`. See `docs/agents/issue-tracker.md`.

### Triage labels

Canonical triage and planning roles use the default Korean label vocabulary. See `docs/agents/triage-labels.md`.

### Domain docs

This repository uses a single-context domain model. See `docs/agents/domain.md`.
