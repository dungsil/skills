---
name: writing-javadoc
description: Write or review concise Korean Javadoc comments for Java code. Use when adding, revising, or checking Javadocs for Java types, constructors, fields, methods, private helpers, parameters, return values, null behavior, exceptions, deprecations, references, generic type parameters, test-aligned contracts, or project style consistency.
---

# Writing Javadoc

Use this skill to write concise, contract-focused Javadoc that matches the surrounding Java code, tests, and project style.

## Core Rules

- Write Javadocs in Korean. Keep Java identifiers, API names, tags, and code literals in their original form.
  - Do not return example code or generate usage examples.
  - Do not add new `@author`, `@version`, or `@since` tags.
  - Preserve existing `@author`, `@version`, and `@since` tags when revising existing Javadocs unless the user explicitly asks to remove them.
  - Document public and protected members when their contract is useful to callers.
  - Document package-private and private members only when the behavior is complex, non-obvious, or intentionally differs from nearby public methods.
  - Do not generate boilerplate documentation for simple fields or type member properties.
  - Keep documentation close to the code's actual behavior and tested contract.

## Korean Style

- Use concise report-style declarative sentences.
  - Do not use endings like `~입니다.`
  - Change `~합니다.` to `~한다.`
  - Prefer natural Korean developer wording. Avoid parser-like wording such as `소비한다` unless the code is actually parser/tokenizer logic.
  - Keep the first line as a short summary phrase, similar to `문자열 정규화`.
  - Do not add a period to the first summary line unless surrounding Javadocs require it for consistency.

## Method Javadoc Shape

Use this order for method Javadocs:

1. Short summary phrase.
   1. `{@code <p>}` when detail is useful.
   2. One or two behavior sentences.
   3. `@param` tags.
   4. `@return` tag for non-void methods unless the summary already makes the return value completely obvious.
   5. `@throws` tags for documented exceptions.

Keep the body short. Let focused tests carry edge-case detail when the Javadoc would become long.

## Contract Documentation

- Document what the method promises, not how every line is implemented.
  - Document `null` behavior when it is part of the public contract.
  - Put exception behavior in `@throws`, not in the prose body.
  - Only document exceptions callers can reasonably trigger or need to know.
  - Use `@see` for references to other types or members.
  - Use `@param <T>` for generic type parameters when the type parameter's role is not obvious.
  - Use `{@code}` for inline code snippets, including `true`, `false`, and `null`.
  - Avoid HTML tags in Javadocs except for `{@code <p>}` when paragraph separation is needed.
  - Use `@deprecated` to mark a member as deprecated and provide an alternative.
  - If two similar methods intentionally have different contracts, document that difference briefly.
  - If changing one method must not mechanically change another, state that the contracts are different and should be checked separately.

## Review Checklist

- First line is a short summary phrase, not a full sentence.
  - First line does not end with a needless period.
  - Prose is not longer than the surrounding Javadoc style.
  - `@throws` is used for exception contracts.
  - No example code or usage examples are included.
  - Existing `@author`, `@version`, and `@since` tags are preserved unless removal was explicitly requested.
  - No new `@author`, `@version`, or `@since` tags are added.
  - No HTML tags are added except `{@code <p>}`.
  - Sentence endings follow the project style.
