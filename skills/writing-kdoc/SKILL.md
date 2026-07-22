---
name: writing-kdoc
description: Write or review concise Korean KDoc comments for Kotlin code. Use when adding, revising, or checking KDoc for Kotlin classes, objects, functions, properties, constructors, extension receivers, type parameters, return values, exceptions, deprecations, symbol links, or Dokka output.
---

# Writing KDoc

Write concise, contract-focused KDoc that matches the Kotlin code, tests, and project style.

## Core Rules

- Write KDoc in Korean. Keep Kotlin identifiers, API names, tags, and code literals in their original form.
- Do not return example code or generate usage examples.
- Do not add new `@author` or `@since` tags. Preserve existing ones unless the user explicitly asks to remove them.
- Document public and protected declarations when their contract is useful to callers.
- Document `internal` and private declarations only when behavior is complex, non-obvious, or intentionally differs from nearby public declarations.
- Do not generate boilerplate documentation for simple properties or obvious accessors.
- Keep documentation aligned with the code's actual behavior, tests, and project style.

## Korean Style

- Use concise report-style declarative sentences.
- Do not use endings like `~입니다.`; change `~합니다.` to `~한다.`.
- Prefer natural Korean developer wording over literal or parser-like translations.
- Keep the first paragraph's opening line as a short summary phrase.
- Do not add a period to the summary unless surrounding KDoc requires it for consistency.

## Contract Documentation

- Document what the declaration promises, not how every line is implemented.
- Document nullable input, nullable output, and default behavior when they are part of the caller-visible contract.
- Keep exception conditions in `@throws` or `@exception`, not mixed into general prose.
- Document only failures callers can reasonably trigger or need to handle.
- If similar declarations intentionally have different contracts, state the difference briefly and review each contract separately.
- Let focused tests carry edge-case detail when the KDoc would otherwise become long.

## KDoc Syntax

- KDoc uses Markdown, not Javadoc inline tags or paragraph HTML.
- Treat the first paragraph as the summary. Separate additional detail with one blank KDoc line; do not use `<p>`.
- A short KDoc may stay on one line. Use the standard multiline `/** ... */` shape for longer contracts.
- Use backticks for code literals such as `true`, `false`, and `null`; do not use `{@code}`.
- Link declarations and parameters with `[name]`. Use `[label][qualified.name]` for custom labels. Qualified member links use dots.
- Prefer inline links over `@see` when the reference fits naturally in prose.

## Parameters and Return Values

- Generally avoid `@param` and `@return`. Describe parameters and return values in the prose and link parameters as `[parameter]`.
- Use `@param` or `@return` only when a long or independent contract does not fit the prose clearly.
- `@param` documents both value parameters and type parameters.
- Use `@property` for primary-constructor properties when direct property KDoc would be awkward.
- Use `@constructor` for a primary-constructor contract and `@receiver` for an extension receiver contract when either needs separate documentation.

## Kotlin-Specific Contracts

- Document public declarations unless the contract is completely obvious. An override may omit KDoc when it adds no contract beyond the inherited declaration.
- Do not restate a property's name and type. Document caller-visible nullability, mutability, units, defaults, normalization, side effects, or domain invariants.
- For extension functions, document receiver assumptions and mutation or side effects when they are not obvious.
- Use `@throws` or `@exception` only for caller-visible failure conditions worth acting on; Kotlin has no checked-exception completeness requirement.
- KDoc has no `@deprecated` tag. Use Kotlin's `@Deprecated` annotation and keep replacement guidance there.
- Do not add `@sample` by default because usage examples are excluded. Preserve an existing valid `@sample`, or add one only when the user or project explicitly requires it.
- Use `@suppress` only when an externally visible declaration must intentionally be excluded from generated documentation.

## Review Checklist

- The core rules, Korean style, and contract rules above are satisfied.
- The first paragraph is a concise Korean summary.
- Detail paragraphs use blank KDoc lines, not `<p>`.
- Code literals use backticks and declaration references use KDoc links.
- `@param` and `@return` are omitted unless prose would be less clear.
- Primary-constructor properties and extension receivers use the correct KDoc contract shape.
- Deprecation guidance uses `@Deprecated`, never `@deprecated`.
- No new usage example or `@sample` was added without an explicit requirement.
