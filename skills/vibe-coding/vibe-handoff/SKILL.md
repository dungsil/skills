---
name: vibe-handoff
description: Compacts the current conversation into a handoff document another agent can pick up. Use when the context window is filling up, or the user asks to hand off, pause, or continue the work in a fresh session.
disable-model-invocation: true
metadata:
  argument-hint: "What will the next session be used for?"
---

# Handing Off a Session

Write a handoff document summarising the current conversation so a fresh agent can continue the work. Save it to the temporary directory of the user's OS — not the current workspace.

Include a "suggested skills" section in the document, which suggests skills that the agent should invoke.

Do not duplicate content already captured in other artifacts (specs, plans, ADRs, issues, commits, diffs). Reference them by path or URL instead.

Redact any sensitive information, such as API keys, passwords, or personally identifiable information.

If the user passed arguments, treat them as a description of what the next session will focus on and tailor the doc accordingly.
