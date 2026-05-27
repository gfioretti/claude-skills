# Pitfalls by Source Language

The "Pitfalls" section in each step is where the challenge becomes personal to the user. Generic tutorials can't write this — they don't know what background to compare against.

This reference catalogs the most common idiom mismatches by source-language background. When writing the Pitfalls section for a given step, identify which of these traps are relevant to the step's concepts and surface them.

The source languages covered are the ones common among mid/senior backend engineers in 2026: Java, Kotlin, Scala, C#, Python, Node/TypeScript, Ruby, Go, Rust, C/C++. Pick whichever applies to the user's stated background.

---

## From Java → almost anywhere else

**No DI framework, no annotations.** The user will reach for `@Autowired`, `@Component`, `@Service`. Tell them: explicit constructor wiring in `main` is the whole story. No Spring, no Guice, no Dagger.

**No Lombok-equivalents.** `@Data`, `@Getter`, `@Setter` don't exist outside the JVM ecosystem. The user has to write getters or accept that fields are public by capitalization convention or visibility modifier.

**No checked exceptions.** Errors are values returned alongside results (Go, Rust) or unchecked (Python, JS). The compiler may not force you to handle them at all. Don't pretend the error system is "exceptions with extra steps."

**No `Optional<T>` for nullability.** Other languages handle nullability differently — `Option`/`Result` (Rust), nil-safe operators (Kotlin), zero values (Go), `None` (Python typing). Pick one mental model and stick with it; don't translate `Optional.ofNullable` directly.

**Static methods and singletons are over-applied.** Java devs reach for static utility classes. Most languages prefer free functions or methods on the appropriate type.

**Fat JAR thinking doesn't translate.** The deployment artifact in other ecosystems is usually a binary, a wheel, an npm package, or a container. The "where does my classpath live" question has no analog.

**Specific to Spring users:**
- `@RestController` doesn't exist; routes are registered imperatively
- `@Transactional` doesn't span DB + message broker (you'll need the outbox pattern)
- `application.yml` is rare; env vars and typed config structs are common
- AOP and interceptors are uncommon outside the JVM
- `@Async` doesn't have a counterpart; you write the goroutine/Task/coroutine explicitly

---

## From Kotlin → almost anywhere else

**Null safety is a Kotlin invention, not a universal.** Other languages handle nullability through sum types (Rust's `Option`, Haskell's `Maybe`), zero values (Go), or nullable references with weaker compile-time checks (Java, C#). Kotlin devs miss `?.` and `!!` first.

**Coroutines are not the universal concurrency model.** Goroutines (Go) and async/await (Rust, Python, JS, C#) are different mental models. The "suspend functions are colored differently than regular functions" experience translates loosely to Rust/Python's async, but Go doesn't color functions at all.

**Data classes don't generalize.** Records in Java/C# are similar but with slightly different ergonomics. Go's struct with public fields is the closest; Rust requires `#[derive(Debug, Clone, PartialEq)]` explicitly. Don't expect free equality/hashCode everywhere.

**Sealed classes for exhaustive pattern matching.** Rust enums are the cleanest translation; Scala sealed traits work; Java sealed types (16+) are close. Python uses `match` on type unions. Don't translate sealed-class hierarchies into deep inheritance trees.

**Scope functions (`let`, `apply`, `run`, `also`, `with`) are Kotlin-specific syntactic sugar.** Other languages express the same patterns with `?` operators, builders, or explicit variable binding.

**Extension functions don't exist in most languages.** C# has them; Rust has trait impls; nothing else really. Don't try to retrofit them — write free functions.

---

## From Scala → almost anywhere else

**Implicit conversions and given instances don't translate.** Rust traits + type classes are the closest analog. Most languages will require explicit conversion or method calls. Don't write code assuming implicit conversion will fire.

**Higher-kinded types are mostly absent.** Rust GATs are partial; Java/C# generics don't go this deep; Go has no path. If the user reaches for `F[_]`, redirect them to concrete types or functions taking specific effects.

**`for`-comprehensions don't generalize beyond Scala/Haskell.** Other languages express monadic composition with `await`, `?`, or explicit chaining. Don't expect uniform syntax across effect-like types.

**Type classes via given/implicits don't exist in most languages.** Rust traits are the cleanest equivalent; Haskell type classes are the original. Java/Kotlin/C# interfaces are nominal and require explicit implementation. Go's structural interfaces are the loosest match.

**Avoid bringing Cats/ZIO patterns into imperative languages.** Tagless final encoding, free monads, and effect typeclasses do not translate. Pure functions and explicit IO boundaries are the only piece that crosses the gap.

**Sealed traits → algebraic data types.** Rust enums and TypeScript discriminated unions are the closest matches. Java/C# sealed types are getting there. Don't translate sealed traits into class hierarchies.

---

## From C# → almost anywhere else

**LINQ doesn't translate.** Closest analogs: Rust's iterator chains, Java's Streams, Python's list comprehensions + itertools. None of them are quite as fluent. Don't try to recreate `Where().Select().ToList()` patterns 1:1.

**`async/await` works similarly in JavaScript/Python/Rust but differs in Go.** Go has goroutines without `await` ceremony. Don't reach for `await` syntactic patterns in Go code.

**Properties don't exist outside C#/Kotlin/Swift.** Java/Go/Rust use explicit getters and setters (where mutation is allowed at all). The instinct to write `MyProp { get; set; }` doesn't translate.

**`IEnumerable<T>` ≠ universal iterator.** Rust's `Iterator` trait is close but stricter (lifetime concerns). Go uses channels or slices. Python uses generators. Don't assume lazy iteration semantics carry over.

**Dependency injection is less central elsewhere.** C# devs reach for `IServiceCollection`/`builder.Services`; most other languages wire dependencies explicitly in `main`. There's no `Microsoft.Extensions.DependencyInjection` equivalent in Go culture.

**Nullable reference types are a C# 8+ thing.** Other languages either don't have them (Java pre-21 has `@Nullable` annotations at best) or take a different approach (sum types in Rust/Haskell).

**Specific to ASP.NET Core users:**
- Middleware pipelines exist in most frameworks but with different conventions
- Model binding/validation via attributes doesn't translate; expect explicit parsing
- Background services / `IHostedService` have analogs but require manual wiring

---

## From Python → almost anywhere else

**No duck typing free pass.** In statically typed languages, the compiler needs to know types up front. This is fine for explicit interfaces (Go, TypeScript) but requires explicit declarations. Stop relying on "if it walks like a duck."

**No `__init__.py` magic.** Module systems in other languages don't have Python's package quirks. Modules are usually directories or files with no special init file.

**No `*args, **kwargs` punt.** Most languages require explicit function signatures. The user can't defer argument design.

**No GIL-related shortcuts.** Python developers often write "single-threaded-ish" code assuming the GIL serializes things. Other languages have true parallelism, and the user must think about synchronization with mutexes, channels, or actor-like patterns.

**Significant whitespace is gone.** Brace-using languages permit dense code. Don't take that as license to write unreadable code — but `if x:` becomes `if x {` and one-liners are common.

**`pip install` is not the universal pattern.** `go get`, `cargo add`, `npm install`, `bundle add`, `dotnet add package`, `mix deps.get` all behave differently. Especially: most of them update a lockfile that gets committed.

**Specific to Django/FastAPI users:**
- ORMs are less universally accepted in other ecosystems; query builders or raw SQL are more common in Go/Rust
- Migrations may not be auto-generated from models
- Dependency injection via function arguments (FastAPI-style) doesn't translate; expect explicit construction

---

## From JavaScript / TypeScript → almost anywhere else

**No NPM ecosystem scale.** Other languages have smaller, more curated dependency ecosystems. `left-pad` doesn't have a counterpart. Pick a few well-known libraries and commit.

**No callbacks / promises everywhere.** Most languages have async/await but use it more sparingly than JS. Don't async every function reflexively.

**`undefined` and `null` distinction doesn't exist.** Most languages have one nullable type (or none, in Rust's case). The user has to pick `Some`/`None` or nullable or zero-value semantics.

**Loose equality is gone.** No `==` vs `===`. Type coercion is generally explicit. The user has to convert types deliberately.

**`this` binding issues vanish.** Most languages bind `self`/`this` deterministically. No "arrow function vs regular function" gotchas.

**Single-threaded event loop ≠ universal.** JavaScript users assume cooperative multitasking. Most other languages preempt and require real synchronization primitives.

**TypeScript's type system is structural; most others are nominal.** Rust, Java, C#, Kotlin require explicit interface/trait implementation. Go is structural (closest match to TypeScript). This trips people up in both directions.

**No `Promise.all`/`Promise.allSettled` ergonomics.** Equivalents exist (Go's `errgroup`, Rust's `join!`, Python's `asyncio.gather`) but are less ubiquitous. Expect more verbose concurrency code.

---

## From Ruby → almost anywhere else

**No metaprogramming magic.** `method_missing`, monkey-patching, and `eval` are absent or strongly discouraged in most languages. Reach for explicit interfaces or sum types.

**Rails-style convention-over-configuration is rare.** Other web frameworks tend to be more explicit. The user has to specify routes, schemas, and validations.

**Blocks and procs are different elsewhere.** Closures exist everywhere but with different syntax and semantics. Don't write code assuming `yield` works the same way.

**No `nil.respond_to?`.** Defensive checks need to use actual nil/null checks or type-system features. The instinct to send messages to potentially-nil objects doesn't translate.

**Symbol/string distinction doesn't exist.** Most languages have one string type. Don't waste energy on the `:foo` vs `"foo"` distinction.

**No implicit `self`/method chaining everywhere.** Method calls in many languages require `self`/`this` (Python, Rust) or explicit references. Method chaining works but is less ubiquitous.

---

## From Go → almost anywhere else

**`if err != nil` doesn't translate.** Most languages have exceptions or `Result`/`Either` types. The user has to learn the local error pattern — `try`/`catch` (Java, Python, JS, C#, Ruby), `?` operator (Rust), monadic chaining (Scala). The Go pattern looks weird in other languages.

**Goroutines aren't free in other languages.** Most languages' "lightweight threads" are heavier than goroutines. Java 21 virtual threads are the closest. The user should be more careful with spawn counts elsewhere.

**Interface satisfaction is different.** Go is structural; Java, Rust, C#, Kotlin are nominal. The user has to declare conformance with `implements` or `impl Trait for Type`.

**No `gofmt` universal equivalent.** Most languages have formatters but with more configurability and less uniform adoption. The user has to pick a style and convince the team.

**Zero values aren't a thing.** Other languages require explicit initialization. Don't rely on "the zero value of a struct is useful."

**Generic type ergonomics differ.** Go's generics are intentionally constrained. Rust, Java, C# generics support much more. Don't undershoot.

---

## From Rust → almost anywhere else

**Ownership doesn't translate.** GC'd languages let the user pass references freely. The user can stop fighting the borrow checker but should still think about aliasing in mutable data.

**`Result<T, E>` is sometimes absent.** Most other languages use exceptions or have different sum-type ergonomics. The `?` operator has no analog except in Rust-influenced languages.

**Zero-cost abstractions are not universal.** Other languages may have runtime cost for what looks similar (virtual dispatch, boxed values, garbage collection). Don't assume `map().filter().collect()` is free.

**Pattern matching ergonomics vary widely.** Rust's `match` is more powerful than most. Python's `match` is comparable; Scala/Kotlin sealed types come close; Java pattern matching for switch is getting there; Go has nothing comparable.

**Trait-based polymorphism vs interfaces.** Most languages use interfaces (nominal) or implicit interfaces (structural Go). Coherence rules (orphan instances) are a Rust thing.

**Cargo ergonomics spoil you.** Other languages' package managers are less polished. Expect rougher edges in npm, pip, gem, mvn.

---

## From C / C++ → almost anywhere else

**Manual memory management is gone (mostly).** GC'd languages handle allocation; Rust handles it with ownership. The user can stop thinking about `free()` but should understand what's happening underneath.

**No header files.** Module systems replace `#include`. The user has to learn the module conventions of the target language.

**Macros are usually different or absent.** Generics or higher-order functions usually do what macros did. Reaching for macros (where they exist) is often the wrong instinct.

**Undefined behavior doesn't exist (in most languages).** Most languages have well-defined behavior even in pathological cases. The user can stop worrying about every edge case but should still write defensive code.

**Build systems are simpler.** `cargo`, `go build`, `npm`, `pip` are all simpler than `make`+`cmake`. The user should embrace this.

**No raw pointer manipulation.** Even in Rust, `unsafe` is opt-in and used sparingly. In Go/Java/Python/JS, raw memory access is essentially impossible. Don't reach for pointer arithmetic.

---

## How to use this reference

When writing a step's "Pitfalls" section:

1. Identify the concepts the step introduces.
2. Look up which of those concepts have idiom mismatches with the user's source language.
3. Write 4–7 pitfalls. Each should be concrete: "In Java you do X. Here you do Y because Z."
4. Use direct, slightly opinionated language. Pitfalls that read like passive observations don't stick.

**Bad pitfall:** "Go has a different error handling model."

**Good pitfall:** "**No checked exceptions.** Errors are values returned alongside results. The compiler will let you ignore them — `golangci-lint`'s `errcheck` won't. The Java instinct to add `throws IOException` to method signatures has no analog; if you care about an error, you handle it where it's returned."

The good version is concrete, names the source-language concept being replaced, and tells the user what tool replaces the compiler's old job.

**On multiple source backgrounds:** if the user has experience in two languages (common: "Java + Python" or "Java + Kotlin"), pick the dominant one for pitfalls. Their muscle memory comes from where they've spent the most recent time. The other background is a footnote.

**On novel combinations:** if the user's source language isn't in this list (e.g., they're a Clojure developer learning Rust), the closest analog approach is acceptable but call out the limitation: "I'm using JVM pitfalls here as the closest match to your background — let me know if any feel off." This invites correction rather than producing generic content.
