# Coroutines And Flow

## Ownership And Structure

- Start work in a scope owned by the lifecycle that should cancel it. Prefer `coroutineScope` or `supervisorScope` for child work that belongs to the current operation.
- Do not create ad hoc scopes in functions or use `GlobalScope` for ordinary application work. Inject or own a longer-lived scope only when the work must intentionally outlive the caller.
- The layer that starts a coroutine owns its failure observation and lifecycle. Lower layers usually expose `suspend` functions for one-shot work and `Flow` for streams.
- Use `async` only for genuine concurrent decomposition whose result is awaited. Sequential dependencies should remain sequential.

## Context And Blocking Work

- A `suspend` modifier does not make blocking code non-blocking. The boundary performing file, JDBC, legacy network, or CPU-heavy work owns the required `withContext` shift.
- Keep libraries context-preserving unless their contract explicitly owns execution. In applications, inject or centralize dispatcher selection when deterministic testing or platform policy requires it; do not scatter hardcoded dispatchers.
- Avoid redundant `withContext` around already suspending, context-safe APIs.

## Cancellation And Failure

- Cancellation is control flow. Never catch and suppress `CancellationException`; rethrow it before translating or wrapping other failures.
- `launch` propagates uncaught child failure through its hierarchy; `async` exposes failure through `await`. Observe every started job or deferred result according to its contract.
- `CoroutineExceptionHandler` is for uncaught root-coroutine handling, typically logging or terminal reporting. It does not recover failed children and does not handle `async` failures before `await`.
- Use supervision only when sibling failures are intentionally independent. Handle each supervised child's failure; supervision is not silent error suppression.
- Keep non-cancellable cleanup minimal and limited to operations that must complete during cancellation.

## Flow And State

- Expose `Flow`, `StateFlow`, or another read-only stream type; keep `MutableStateFlow` and mutable state private.
- Choose cold versus shared/stateful flow deliberately. Document replay, initial value, completion, errors, and collection lifetime when callers cannot infer them.
- Avoid hidden launches inside flow operators. Prefer declarative operators and let the collecting scope own cancellation.
- Do not use Flow for a single immediate value when a regular or suspending function is clearer.

