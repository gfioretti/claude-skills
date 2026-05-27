# Progression Patterns by Target Language

The 10-step skeleton stays the same in spirit across languages: project setup → data → persistence → lifecycle → testing → async → second service → third service + feedback → hardening → observability/CI. The *ordering* and *emphasis* shift based on which concepts are most distinctive (or most likely to mislead) in each language.

This file has a per-language template for each mainstream backend language. **Use the matching one as a starting point — don't force-fit a language into a generic skeleton.** Calibrate based on the user's specific background and interview goals.

The languages covered are the ones currently mainstream for backend engineering in 2026: Go, Rust, Java, Kotlin, Scala, Python, Node/TypeScript, C#, Ruby. If asked about something outside this list, fall back to the closest analog and call out the limitation to the user.

---

## Go

**What's distinctive:** structural interfaces, explicit error values, goroutines + channels, project layout conventions, stdlib-first culture.

| # | Theme | Concepts |
|---|---|---|
| 1 | Project skeleton + HTTP | `go mod`, `cmd/internal/pkg` layout, stdlib `net/http` (1.22+ patterns), struct tags |
| 2 | Domain modeling + interfaces | Structural interfaces, sentinel errors, `errors.Is/As`, accept-interfaces-return-structs |
| 3 | Persistence | `pgx`, prepared statements, `pgxpool`, migrations with `migrate` |
| 4 | Config + logging + lifecycle | `log/slog`, env-driven config, `signal.NotifyContext`, `errgroup`-based shutdown |
| 5 | Testing | Table-driven tests, `httptest`, build tags for integration tests, hand-rolled fakes |
| 6 | Kafka producer | `franz-go`, goroutines + channels, sync vs async publish tradeoffs |
| 7 | Second service (consumer) | Consumer groups, idempotency, multi-binary modules, `cmd/<name>` pattern |
| 8 | Third service + feedback loop | Multiple topics, eventual consistency, DLQs, state-machine modeling |
| 9 | Concurrency hardening | Worker pools, `golang.org/x/time/rate`, `sync.Pool`, circuit breakers |
| 10 | Observability + CI | Prometheus client, OpenTelemetry, Docker, GitHub Actions, vendoring |

**Calibration notes:**
- Step 2 must emphasize "define interfaces where consumed." This is the most-tripped Go convention for JVM and C# developers.
- Step 4's `errgroup` pattern is a Go-specific idiom worth dwelling on — Java devs will reach for ExecutorService.
- Avoid heavy frameworks throughout. Stdlib-first signals senior-Go literacy in interviews.

---

## Rust

**What's distinctive:** ownership + borrowing, Result/Option, traits + generics, zero-cost abstractions, async without runtime in std.

| # | Theme | Concepts |
|---|---|---|
| 1 | Project skeleton + types | `cargo`, basic types, `Result<T, E>`, `Option<T>`, error handling with `?` |
| 2 | Ownership + borrowing | Lifetimes, references vs values, the borrow checker, common patterns |
| 3 | Traits + generics | Trait-based polymorphism, generic bounds, dynamic vs static dispatch tradeoffs |
| 4 | Persistence (sync first) | `sqlx`, compile-time-checked queries, migrations via `sqlx-cli` |
| 5 | Testing | `#[test]`, integration tests in `tests/`, `proptest` for properties, `cargo nextest` |
| 6 | Async runtime + HTTP | `tokio`, `axum`, the cost model of `.await`, when to use `spawn` vs `block_on` |
| 7 | Kafka + second service | `rdkafka` (via `rust-rdkafka`), async consumers, `tokio::select!` |
| 8 | Third service + IPC | gRPC with `tonic` or more Kafka topics, `serde` schema evolution |
| 9 | Concurrency primitives | `tokio::sync` channels, `Arc<Mutex>` vs `Arc<RwLock>`, `Send/Sync` bounds |
| 10 | Observability + release | `tracing`, `metrics` crate, release-mode build flags, container images |

**Calibration notes:**
- **Don't introduce async before ownership is solid.** Async + borrow checker simultaneously will break the user's spirit. That's why persistence comes before async — sync DB work lets the user focus on data modeling and error flow.
- Step 6 is a heavyweight step. It introduces the runtime *and* the web framework. Plan for 4 hours, not 2.
- For users coming from GC'd languages, expect them to over-clone in early steps. Cover this in pitfalls.

---

## Java (21+ — virtual threads era)

**What's distinctive:** records + sealed types + pattern matching (modern Java), virtual threads, mature ecosystem, framework dominance vs framework-free trade-off.

| # | Theme | Concepts |
|---|---|---|
| 1 | Project skeleton + Javalin | Gradle/Maven project structure, Javalin (or Spark) HTTP, record-based DTOs |
| 2 | Modern types + immutability | `record`, `sealed` interfaces, pattern matching for switch, `Optional` discipline |
| 3 | Persistence | JDBC + `jOOQ` or hand-rolled DAOs, Flyway migrations |
| 4 | Config + logging + lifecycle | env-driven `record` configs, SLF4J + Logback structured logging, shutdown hooks |
| 5 | Testing | JUnit 5, AssertJ, Mockito, Testcontainers for integration |
| 6 | Virtual threads + Kafka producer | Virtual threads (Java 21+), `Thread.startVirtualThread`, Kafka client basics |
| 7 | Second service (consumer) | Kafka consumer with virtual threads, idempotency, multi-module Gradle |
| 8 | Third service + feedback loop | Bidirectional Kafka, eventual consistency, DLQ patterns |
| 9 | Concurrency hardening | Structured concurrency (Java 21 preview), `ScopedValue`, rate limiting |
| 10 | Observability + CI | Micrometer + Prometheus, OpenTelemetry Java agent, Docker, GitHub Actions |

**Calibration notes:**
- **Avoid Spring throughout** unless the user explicitly asks. The user likely knows Spring already — picking Javalin exposes what Spring hides and is better for *learning*. Surface this trade-off in CONVENTIONS.md.
- Step 6 leans hard into virtual threads if Java 21+. If user is stuck on Java 17, fall back to executor-service-based async and call out what they're missing.
- Pattern matching, records, and sealed types are recent enough that even Java veterans haven't fully internalized them. Don't assume.

---

## Kotlin (with Ktor + coroutines)

**What's distinctive:** coroutines, null safety, data classes, extension functions, multi-platform potential.

| # | Theme | Concepts |
|---|---|---|
| 1 | Project skeleton + Ktor | Gradle Kotlin DSL, Ktor minimal setup, data classes for DTOs |
| 2 | Idiomatic Kotlin types | Sealed classes for state, `Result<T>` patterns, nullability discipline, smart casts |
| 3 | Persistence | `Exposed` DSL or JDBC directly, Flyway migrations, coroutine-friendly DB access |
| 4 | Config + logging + lifecycle | `hoplite` for config, kotlin-logging, Ktor's lifecycle hooks, structured shutdown |
| 5 | Testing | Kotest (or JUnit 5), MockK, Testcontainers, Ktor's test utilities |
| 6 | Coroutines + Kafka producer | Coroutines vs threads, structured concurrency, `kotlinx.coroutines`, Kafka basics |
| 7 | Second service (consumer) | Coroutine consumer loops, `Flow` for streaming, multi-module Gradle |
| 8 | Third service + feedback loop | Bidirectional messaging, eventual consistency in a coroutine-native style |
| 9 | Concurrency hardening | `CoroutineScope` discipline, `Channel` vs `Flow`, rate limiting, cancellation |
| 10 | Observability + CI | Micrometer, OpenTelemetry, Docker, GitHub Actions |

**Calibration notes:**
- The Kotlin *feel* must come through. If the challenge reads like Java with Kotlin syntax, you've miscalibrated.
- Lean into `data class`, sealed hierarchies, extension functions, scope functions (`let`, `apply`, `run`, `also`) — but only where they earn their keep.
- Coroutines step is the conceptual peak. Plan for 4 hours and lots of explanation.

---

## Scala 3 (Cats Effect or ZIO)

**What's distinctive:** purely functional effects, advanced type system (givens, opaque types, type classes), strong "pick a side" between Cats Effect and ZIO ecosystems.

**Before designing the challenge:** elicit which effect system the user wants. The challenge structure is the same; library choices and code idioms diverge significantly.

| # | Theme | Concepts |
|---|---|---|
| 1 | Project skeleton + http4s/ZIO-HTTP | `sbt`, basic effect type (`IO` or `ZIO`), HTTP routes |
| 2 | Domain modeling | Opaque types as value classes, given instances, sealed traits / enums, ADTs |
| 3 | Effect handling | `for`-comprehensions, error channels, refined types where relevant |
| 4 | Persistence | `doobie` (Cats) or `quill` (ZIO), database session management within an effect |
| 5 | Testing | munit or weaver, scalacheck for properties, effect-aware test utilities |
| 6 | fs2-kafka or zio-kafka | Streams (`fs2.Stream` or `ZStream`), Kafka producer as a stream |
| 7 | Second service (consumer) | Stream-based consumer, sbt multi-module |
| 8 | Third service + feedback loop | Multi-topic streams, error handling with `EitherT` or ZIO error channel |
| 9 | Concurrency hardening | Fiber-based concurrency, supervision, structured concurrency primitives |
| 10 | Observability + CI | Micrometer or zio-metrics, OpenTelemetry, Docker, GitHub Actions |

**Calibration notes:**
- **Elicit Cats Effect vs ZIO in step 1.** Do not pick for the user. The challenge structure works either way but every library choice changes.
- Scala 3 features (givens, extension methods, enums, opaque types) should be visible throughout — not hidden behind Scala 2 patterns.
- Streams (fs2 or ZStream) are central to Scala's Kafka story in a way they aren't in Java/Kotlin. Lean in.

---

## Python (3.12+ with FastAPI + uv)

**What's distinctive:** type hints maturing, async/await, packaging consolidation (uv), Pydantic ubiquity.

| # | Theme | Concepts |
|---|---|---|
| 1 | Project skeleton + packaging | `uv`, `pyproject.toml`, virtual environments, project layout, FastAPI basics |
| 2 | Type hints + Pydantic | Type hints in earnest, Pydantic v2 models, `mypy`/`pyright` strict mode |
| 3 | Async HTTP + persistence | `asyncpg`, async context managers, dependency injection via FastAPI |
| 4 | Config + logging + lifecycle | `pydantic-settings`, `structlog`, FastAPI lifespan events, graceful shutdown |
| 5 | Testing | `pytest`, `pytest-asyncio`, fixtures, `httpx.AsyncClient`, `testcontainers-python` |
| 6 | Async Kafka producer | `aiokafka`, `asyncio` patterns, fire-and-forget vs awaited publishes |
| 7 | Second service (consumer) | Async consumer loops, idempotency, packaging multi-binary projects |
| 8 | Third service + feedback loop | Multi-topic patterns, eventual consistency, schema evolution with Pydantic |
| 9 | Concurrency hardening | Backpressure with `asyncio.Queue`, `aiolimiter` for rate limits, circuit breakers |
| 10 | Observability + CI | `prometheus-client`, OpenTelemetry Python, Docker, GitHub Actions |

**Calibration notes:**
- The ORM debate is hot in Python land. **For this challenge, prefer asyncpg + hand-written SQL over SQLAlchemy/Tortoise.** It exposes more of the data layer and is more interview-defensible for "I understand the database."
- The GIL is gone in 3.13+ (free-threaded build) — if user is on 3.13+, mention this; otherwise asyncio is the concurrency story.
- Avoid Django. It's huge, opinionated, and hides too much for a learning challenge.

---

## Node.js / TypeScript

**What's distinctive:** single-threaded event loop, npm ecosystem scale, TypeScript type system, framework volatility.

| # | Theme | Concepts |
|---|---|---|
| 1 | Project skeleton + Fastify | `pnpm`, `tsconfig.json` strict mode, project layout, Fastify basics |
| 2 | TypeScript types + Zod | Discriminated unions, strict null checks, Zod schemas + inferred types |
| 3 | Persistence | `postgres.js` or `pg` directly, `kysely` for type-safe queries, migration files |
| 4 | Config + logging + lifecycle | Env vars + Zod validation, `pino` structured logging, signal handling |
| 5 | Testing | `vitest`, `supertest` for HTTP, Testcontainers, mocking strategies |
| 6 | Kafka producer | `kafkajs`, single-threaded event loop tradeoffs, batching |
| 7 | Second service (consumer) | Consumer groups in KafkaJS, idempotency, monorepo with workspaces (`pnpm`/`turbo`) |
| 8 | Third service + feedback loop | Multi-topic, eventual consistency, schema evolution with Zod |
| 9 | Concurrency hardening | Worker threads (when to use), backpressure, rate limiting, retry/circuit-break |
| 10 | Observability + CI | `prom-client`, OpenTelemetry Node SDK, Docker, GitHub Actions |

**Calibration notes:**
- The framework story is volatile. Fastify is the right call in 2026 but verify currency.
- Lean into TypeScript's type system — Zod + inferred types should be visible everywhere. If the challenge reads like JS with optional type annotations, you've miscalibrated.
- Worker threads are a real and underused tool — don't skip them in step 9.

---

## C# / .NET 8+

**What's distinctive:** records + pattern matching (modern C#), minimal APIs, `async/await` everywhere, strong tooling.

| # | Theme | Concepts |
|---|---|---|
| 1 | Project skeleton + Minimal APIs | `dotnet new`, project structure, ASP.NET Core Minimal APIs, record DTOs |
| 2 | Modern C# types | `record`, init-only properties, pattern matching, nullable reference types (NRT) on |
| 3 | Persistence | Dapper or `Npgsql` directly, DbUp or FluentMigrator, async DB access |
| 4 | Config + logging + lifecycle | `IConfiguration` with strongly typed options, Serilog, `IHostApplicationLifetime` |
| 5 | Testing | xUnit, FluentAssertions, NSubstitute, `WebApplicationFactory<T>`, Testcontainers |
| 6 | Kafka producer | `Confluent.Kafka`, async producer patterns, channel-based queue |
| 7 | Second service (consumer) | Consumer groups, idempotency, `dotnet sln` multi-project, hosted services |
| 8 | Third service + feedback loop | Multi-topic Kafka, eventual consistency, channel-based fan-out |
| 9 | Concurrency hardening | `IAsyncEnumerable`, `System.Threading.Channels`, rate limiting, Polly for resilience |
| 10 | Observability + CI | OpenTelemetry .NET, Prometheus exporter, Docker, GitHub Actions |

**Calibration notes:**
- Modern .NET (8+) is materially different from .NET Framework. Verify the user is on 8+.
- Minimal APIs are a recent shift from MVC controllers. Lean into them; mention controllers as the legacy pattern.
- Avoid Entity Framework Core unless the user asks for it. Dapper or raw Npgsql exposes more.
- Polly (in step 9) is the canonical resilience library — don't hand-roll circuit breakers if Polly does it.

---

## Ruby (with Sinatra/Hanami, not Rails)

**What's distinctive:** dynamic typing, metaprogramming culture, Rails dominance creates a "what's underneath" gap.

**Note:** Pick Sinatra or Hanami for the challenge to expose what Rails hides. If the user pushes back ("I want to learn Rails"), Rails is fine — adjust accordingly.

| # | Theme | Concepts |
|---|---|---|
| 1 | Project skeleton + Sinatra | bundler, `Gemfile`, Sinatra app structure, Rack basics |
| 2 | Domain modeling | Plain Ruby objects, `Struct`/`Data`, `Comparable`, dry-types for validation |
| 3 | Persistence | `Sequel` ORM (lighter than ActiveRecord) or `pg` directly, sequel-migrations |
| 4 | Config + logging + lifecycle | `dry-configurable`, structured logger, Rack middleware for lifecycle |
| 5 | Testing | RSpec, factory_bot (or hand-rolled), VCR for external calls, Testcontainers |
| 6 | Kafka producer | `ruby-kafka`, thread-based async producer |
| 7 | Second service (consumer) | Consumer in a separate process, idempotency, bundler workspaces |
| 8 | Third service + feedback loop | Multi-topic, eventual consistency |
| 9 | Concurrency hardening | Fibers vs threads, the GIL/GVL, `Concurrent::Promises`, rate limiting |
| 10 | Observability + CI | `prometheus-client` (Ruby), OpenTelemetry Ruby, Docker, GitHub Actions |

**Calibration notes:**
- Ruby's `Data` class (3.2+) is genuinely useful — leverage it for value objects.
- The GVL/GIL is a real constraint. Cover it in step 9 explicitly.
- Ractors exist but aren't production-ready in 2026. Mention but don't build on them.

---

## Cross-language calibration rules

Regardless of which language template you start from:

- **Persistence comes before async** in most templates. The exception is languages where async is the default I/O model (Node, modern Python) — there the async runtime is established by step 1 of necessity.
- **Testing arrives by step 5.** Earlier and the user doesn't have enough code to test; later and they've built a habit of skipping tests.
- **The second service arrives in steps 6–8.** Earlier and the first service isn't substantial enough; later and momentum stalls.
- **Observability and CI is always step 10.** It's the maturity capstone.
- **Step 1 should produce something runnable.** A working `make run` (or equivalent) by the end of step 1 is non-negotiable. Don't drag setup over two steps.

These aren't rigid. Move things around if the language's idioms suggest otherwise. But moving more than two steps from these defaults usually means you've miscalibrated.

## On user proficiency

The challenge structure assumes the user has *some* prior programming experience and is either learning a new language or refreshing a known one. Two flavors:

**"Refresher" mode:** User has used the language before (months or years ago). Skip the absolute basics — they know what a `struct` is, they remember the type system roughly. Spend more time on idioms and recent additions (records in Java, virtual threads, `uv` in Python).

**"Learning from scratch" mode:** User is fluent in another language but new to this one. Step 1 needs more scaffolding — explain `go mod`/`cargo`/`uv` conceptually, not just the commands. Spend more time on the type system fundamentals (step 2 may need to be longer). The pitfalls section becomes especially important because the user has fresh muscle memory from their source language.

The skill's elicitation should distinguish these. Same 10-step skeleton; different depth at the early steps.
