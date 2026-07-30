# Idiomatic Kotlin

## State And Domain Types

- Default to `val` and expose `List`, `Set`, `Map`, `Collection`, or domain-specific read-only interfaces. Keep mutable implementations private.
- Use `data class` only when structural equality, `copy`, destructuring, and `toString` are all valid semantics.
- Use `@JvmInline value class` for scalar domain distinctions such as IDs or codes when validation, serialization, reflection, generic boxing, and Java calling behavior remain acceptable.
- Use sealed classes or interfaces for closed alternatives that callers should handle exhaustively.
- Prefer constructor parameters for required state. Use factories when construction validates, normalizes, selects a subtype, or may fail.
- Avoid `lateinit` outside framework-managed lifecycle points or tests where initialization is externally guaranteed.

## Nullability And Failure

- Make absence part of the type with `T?`; do not use sentinel values or nullable collections when an empty collection means the same thing.
- Prefer smart casts, `?.`, `?:`, `firstOrNull`, `getOrNull`, and explicit branching. Use `!!` only when a nearby invariant proves non-null and no typed alternative can express it.
- Use `require`/`requireNotNull` for caller argument violations, `check`/`checkNotNull` for invalid receiver state, and `error` for unreachable illegal state.
- Preserve the original cause when translating infrastructure exceptions. Catch the narrowest exception that can be handled meaningfully.
- Use `Result` or a domain result type only when callers are expected to branch on failure. Do not wrap every internal exception mechanically.

## Functions And APIs

- Prefer expression bodies for genuinely single-expression functions; use block bodies when steps, branching, logging, or debugging benefit from names.
- Prefer default parameters over Kotlin-only overloads. Use named arguments for booleans or adjacent same-typed primitives when call-site meaning is otherwise unclear.
- Prefer a property over a no-argument function only when access is cheap, stable for unchanged state, and non-throwing.
- Use extension functions for operations naturally expressed on a receiver and implementable from its public contract. Restrict visibility to avoid namespace pollution.
- Use operator, infix, DSL, and reified APIs only when their reading matches established Kotlin semantics and materially simplifies callers.
- Minimize public declarations. For published libraries, enable explicit API mode where practical and declare public visibility and types deliberately.

## Collections And Control Flow

- Prefer `if` for binary conditions and `when` for three or more alternatives or sealed/type dispatch.
- Prefer `map`, `filter`, `associate`, `groupBy`, `fold`, and related operations when the pipeline remains obvious. Prefer a loop when it avoids a complex chain, repeated passes, or unnecessary allocations.
- Prefer `for` to terminal `forEach` unless `forEach` belongs in a longer chain or handles a nullable receiver.
- Use `Sequence` for demonstrably useful laziness: large or unbounded inputs, several intermediate stages, or early termination. Small/simple collection work is usually clearer and faster as eager operations.
- Avoid rebuilding collections in loops; use builders or mutable accumulation privately, then expose a read-only result.

## Scope Functions

- `apply`: configure and return the receiver.
- `also`: perform a side effect and return the receiver.
- `let`: transform or operate on a nullable/intermediate value and return the lambda result.
- `run`: compute a result using receiver members.
- `with`: group operations on an existing receiver without chaining.
- Avoid nesting or mixing receiver-style and argument-style scope functions when a named local is clearer.

