# JVM Interoperability And Compatibility

## Platform Types And Boundaries

- Give public functions and member properties explicit Kotlin types when initialized or returned from Java platform types. Decide nullable versus non-null at the boundary instead of letting `T!` spread.
- Validate untrusted Java/framework values at ingress. Do not scatter `!!` throughout internal Kotlin code to compensate for one unsafe boundary.
- Inspect generated getter/setter names for boolean `is...` properties and framework binding expectations.
- Remember that Kotlin `internal` is represented as public JVM bytecode with name mangling; it is not a security boundary.

## Java Caller Shape

- Prefer Kotlin defaults for Kotlin callers. Add `@JvmOverloads` or manual overloads only when Java callers need them and the generated overload set is desirable.
- Use `@Throws` when Java callers must see checked exceptions in the method signature.
- Use `@JvmStatic`, `@JvmField`, `@JvmName`, `@JvmWildcard`, and `@JvmSuppressWildcards` only to solve a verified Java API or signature problem.
- Check top-level declaration file-facade names when Java calls them; use file-level `@JvmName` only when the Java-facing name is part of the intended API.
- Use annotation use-site targets such as `@field:`, `@get:`, or `@param:` when a framework or Java reflection inspects a specific generated element.
- Review value classes, function types, `Unit`, `Nothing`, variance, default parameters, and companion objects from the Java call site before exposing them publicly.

## Published API Compatibility

- Treat binary, source, and behavioral compatibility as separate constraints.
- Enable explicit API mode and binary API validation for published libraries when the project supports them.
- Declare public return and property types explicitly. Inferred implementation types can become accidental binary contracts.
- Adding a default parameter can preserve Kotlin source calls while breaking existing JVM binaries. Preserve old entry points with supported version-overload mechanisms or explicit overloads.
- Changing a return type, including narrowing it, can break JVM binary compatibility.
- Avoid public `data class` types when constructor, `copy`, destructuring, or property growth must evolve compatibly.
- Treat public inline bodies and `@PublishedApi internal` declarations as compatibility-sensitive because client bytecode can contain them.
- Evolve published APIs through deprecation and replacement paths rather than immediate removal.

