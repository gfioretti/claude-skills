# Framework Defaults by Target Language

When designing a challenge, library choices come up at almost every step. Rather than having Claude rederive these per session (which produces inconsistent and sometimes stale recommendations), this file pre-decides defaults for each mainstream backend language.

**How to use these:** Pick the defaults as the challenge's starting point. List the alternative(s) parenthetically in CONVENTIONS.md so the user knows what other options exist and why these were picked. If the user has a preference, honor it — these are defaults, not mandates.

**Currency:** these reflect what experienced backend teams reach for in 2026. Recheck when the skill is used; ecosystems shift. If the user mentions they're targeting a specific company or team, ask what that team uses — match it where possible.

---

## Go

| Concern | Default | Alternative | Reason |
|---|---|---|---|
| HTTP routing | stdlib `net/http` (1.22+) or `chi` | `gin`, `echo`, `fiber` | Stdlib is enough; `chi` for middleware ergonomics. `gin`/`echo` signal "I haven't tried the stdlib lately." |
| Postgres driver | `jackc/pgx/v5` | `database/sql` + `lib/pq` (deprecated) | pgx is native, faster, has better types |
| Migrations | `golang-migrate/migrate` | `pressly/goose`, `atlas` | migrate is the most widely deployed |
| Kafka client | `twmb/franz-go` | `segmentio/kafka-go`, `IBM/sarama` (legacy) | franz-go outperforms and has cleaner APIs |
| Config | `caarlos0/env` + env vars | `kelseyhightower/envconfig`, `spf13/viper` (heavy) | Tiny, no magic |
| Logging | stdlib `log/slog` (1.21+) | `zap`, `zerolog` | Stdlib is finally good enough |
| Validation | `go-playground/validator` | hand-rolled | Tag-based, well-established |
| Testing | stdlib `testing` + `testify` (optional) | `gocheck`, `ginkgo` | Stdlib is the convention; testify is widely tolerated |
| Mocking | hand-rolled fakes | `mockgen`, `moq` | Hand-rolled is the senior-Go default |
| Integration tests | `testcontainers-go` | docker-compose-driven tests | Containers in-process |
| Metrics | `prometheus/client_golang` | OpenTelemetry metrics | Prometheus is the dominant choice |
| Tracing | OpenTelemetry | Jaeger client (legacy) | OTel is the consolidated standard |
| Build | `make` | `task`, `mage` | Universally understood |

**Conservative move:** stdlib-first wherever defensible. Senior Go reviewers notice when you reach for a heavy framework before the stdlib stops being enough.

---

## Rust

| Concern | Default | Alternative | Reason |
|---|---|---|---|
| Web framework | `axum` | `actix-web`, `rocket`, `warp` | Tokio-native, ergonomic, the consolidating default |
| Async runtime | `tokio` | `async-std` (declining), `smol` | Tokio is overwhelmingly the choice |
| Postgres driver | `sqlx` | `tokio-postgres` (lower-level), `diesel` (sync-leaning) | sqlx for compile-time-checked queries with async |
| Migrations | `sqlx-cli` | `refinery`, `diesel migrations` | Pairs naturally with sqlx |
| Kafka client | `rdkafka` (via `rust-rdkafka`) | `apache-avro` + manual, pure-Rust attempts | librdkafka bindings remain the practical choice |
| Config | `config` crate + env | `figment`, hand-rolled | Layered config (file + env) |
| Logging | `tracing` | `log` + `env_logger` | Structured, async-aware |
| Serialization | `serde` | nothing else, really | Universal |
| Validation | `validator` | hand-rolled | Derive-macro-driven |
| Testing | stdlib `#[test]` + `proptest` for properties | `quickcheck` | Built-in; proptest for property-based |
| HTTP testing | `axum::Router` + `tower::ServiceExt` | manual tower stack | Use the framework's test utilities |
| Metrics | `metrics` crate + Prometheus exporter | OpenTelemetry | metrics-rs is more pragmatic for Rust |
| Tracing | `tracing` + `tracing-opentelemetry` | manual OpenTelemetry | Pair with the logging crate |
| Build | `cargo` + optional `just` recipes | `make`, `cargo-make` | Cargo is enough; `just` for non-cargo tasks |

**Calibration:** the framework wars settled later in Rust than in Go. Axum is the safe pick in 2026 but still introduce alternatives — the user may encounter actix-web in older codebases.

---

## Modern Java (21+)

| Concern | Default | Alternative | Reason |
|---|---|---|---|
| Web framework | `Javalin` (for the challenge) | Spring Boot, Quarkus, Micronaut | Javalin is minimal and exposes what Spring hides — better for learning. Acknowledge Spring is industry-dominant. |
| Build tool | `Gradle` (Kotlin DSL) | Maven, sbt (for Scala) | Modern Java leans Gradle; Kotlin DSL for type-safety |
| Postgres driver | `JDBC` directly or `jOOQ` | JPA/Hibernate | Hand-written SQL beats JPA for the learning value |
| Migrations | `Flyway` | Liquibase | Simpler, SQL-first |
| Kafka client | Apache Kafka client (official) | spring-kafka (if using Spring) | Use the bare client to see the consumer loop |
| Config | record-based, env-driven | Spring `@Value`, MicroProfile Config | Don't hide config behind framework magic |
| Logging | SLF4J + Logback (default), `System.Logger` for libraries | `log4j2` (post-2021 baggage) | SLF4J is the de facto API |
| Validation | Jakarta Bean Validation | hand-rolled | Annotation-driven, ecosystem-standard |
| Testing | JUnit 5 + AssertJ | TestNG (declining), Spock | JUnit 5 is universal |
| Mocking | Mockito | EasyMock, JMockit | Mockito is overwhelmingly dominant |
| Integration | Testcontainers | embedded H2 (avoid) | Real Postgres in Docker, not embedded |
| Metrics | Micrometer | OpenTelemetry metrics | Micrometer integrates with Prometheus, Datadog, etc. |
| Tracing | OpenTelemetry Java agent | manual instrumentation | Agent-based is the path of least resistance |
| Concurrency | virtual threads (Project Loom, Java 21+) | reactive (Reactor, RxJava) | Virtual threads obsolete most reactive patterns |

**Calibration:** the user almost certainly knows Spring already if they have Java background. Picking Javalin or similar exposes what Spring hides — better for *learning* even though Spring is what they'll use at work. Make this trade-off explicit in CONVENTIONS.md.

---

## Kotlin

| Concern | Default | Alternative | Reason |
|---|---|---|---|
| Web framework | `Ktor` | Spring Boot, http4k, Javalin | Ktor is Kotlin-native, coroutine-first, what Kotlin teams reach for in greenfield |
| Build tool | Gradle (Kotlin DSL) | Maven | Type-safe, Kotlin-idiomatic |
| Postgres driver | `Exposed` or JDBC directly | JPA/Hibernate | Exposed is Kotlin-DSL, JDBC for control |
| Migrations | Flyway | Liquibase | Same reasoning as Java |
| Kafka client | Apache Kafka client + coroutine wrapper, or `kotlinx-kafka` | spring-kafka | Stay close to the metal |
| Config | `hoplite` or env-driven data classes | Spring config, environment | Hoplite supports YAML/TOML with type safety |
| Logging | SLF4J + Logback + `kotlin-logging` | manual SLF4J | kotlin-logging gives idiomatic syntax |
| Concurrency | coroutines (built-in) | reactive (Reactor) | Coroutines are the Kotlin answer |
| Serialization | `kotlinx.serialization` | Jackson, Gson | First-party, compile-time, multiplatform |
| Testing | JUnit 5 + Kotest, MockK | JUnit alone, Mockito | Kotest for property tests, MockK for Kotlin-native mocking |
| Validation | hand-rolled with sealed result types | Bean Validation | Idiomatic Kotlin prefers explicit Result types |
| Metrics | Micrometer | OpenTelemetry | Same as Java |

**Calibration:** Kotlin's "feel" is meaningfully different from Java's. Lean into coroutines, sealed classes, and `data class`. If the challenge looks like Java with Kotlin syntax, you've miscalibrated.

---

## Scala 3

| Concern | Default | Alternative | Reason |
|---|---|---|---|
| Web framework | `http4s` (Cats Effect) or `Pekko HTTP` | Play, ZIO-HTTP | http4s is the FP-canonical choice; Pekko for actor-based |
| Build tool | `sbt` | Mill, scala-cli for small projects | sbt remains dominant for production |
| Effect system | Cats Effect 3 or ZIO 2 | manual Future, blocking | One of these must be chosen — there is no "default" Scala |
| Postgres driver | `doobie` (Cats) or `quill` (ZIO/Cats) | JDBC directly | doobie composes with Cats effect |
| Migrations | Flyway | Liquibase | Same as Java |
| Kafka client | `fs2-kafka` (Cats) or `zio-kafka` (ZIO) | Apache Kafka client + manual wrap | Match the effect system |
| Config | `pureconfig` (Cats) or `zio-config` (ZIO) | typesafe-config | Type-safe config matching effect choice |
| Logging | `log4cats` + SLF4J + Logback | scala-logging | log4cats integrates with effect system |
| Serialization | `circe` | `json4s`, `play-json`, `upickle` | Cats-aligned, derives via Magnolia |
| Testing | `munit` or `weaver` | ScalaTest (heavier), specs2 | munit is the lightweight modern default |
| Property testing | `scalacheck` | hedgehog | Universal |
| Metrics | Micrometer | OpenTelemetry | Same as Java |

**Calibration:** Scala has the strongest "pick a side" problem of any language here. Cats Effect and ZIO are mutually exclusive ecosystems. **Elicit which the user wants in step 1 — don't pick for them.** The challenge structure works either way but library choices change radically.

---

## Python (3.12+)

| Concern | Default | Alternative | Reason |
|---|---|---|---|
| Web framework | `FastAPI` | Flask (smaller), Starlette (lower-level), Litestar (modern) | FastAPI is the dominant choice for new services |
| Project manager | `uv` | `poetry`, `pdm`, `pip-tools` + venv | uv is fast, modern, consolidating |
| Postgres driver | `asyncpg` (async) or `psycopg` v3 (sync) | SQLAlchemy Core, Django ORM | Async-native or modern sync; avoid the ORM debate for backend-systems learning |
| Migrations | `alembic` (if SQLAlchemy) or hand-rolled SQL files | Django migrations | Alembic is the framework-agnostic default |
| Kafka client | `aiokafka` (async) or `confluent-kafka-python` (sync) | `kafka-python` (declining) | Confluent's client wraps librdkafka; aiokafka for asyncio |
| Config | `pydantic-settings` | hand-rolled, `dynaconf` | Type-safe, Pydantic-aligned with FastAPI |
| Logging | stdlib `logging` + `structlog` | `loguru` | structlog for structured output |
| Validation | `pydantic` v2 | `marshmallow`, hand-rolled | Pydantic v2 is fast and ubiquitous |
| Testing | `pytest` + `pytest-asyncio` | unittest | Pytest is overwhelming dominant |
| Integration | `testcontainers-python` | docker-compose | Containers from inside tests |
| Type checking | `mypy` or `pyright` | none (avoid) | Strict mode from day one |
| Metrics | `prometheus-client` | OpenTelemetry metrics | Prometheus is the standard exposition |
| Tracing | OpenTelemetry Python | manual | OTel auto-instrumentation works for FastAPI |
| Async runtime | asyncio | trio, curio (niche) | asyncio is the practical choice |

**Calibration:** the Python ecosystem in 2026 has settled around `uv` for project management — older skills documents will say `poetry`. Verify currency. Also: the "ORM vs not" debate matters. For a backend-systems learning challenge, **prefer hand-written SQL via asyncpg/psycopg over an ORM** — it exposes more of the underlying concepts.

---

## Node.js / TypeScript

| Concern | Default | Alternative | Reason |
|---|---|---|---|
| Runtime | Node.js 20+ LTS | Bun, Deno | Node is the safe default; mention Bun for performance |
| Web framework | `Fastify` | Express (declining), Hono, NestJS (heavier) | Fastify is fast, modern, schema-driven |
| Package manager | `pnpm` | npm, yarn, bun | pnpm is the disk-efficient modern default |
| TypeScript config | `strict: true`, `noUncheckedIndexedAccess: true` | non-strict | Strict from day one |
| Postgres driver | `postgres.js` or `node-postgres` (pg) | Prisma (ORM) | Raw drivers for learning; Prisma works but hides the SQL |
| Query builder (if used) | `kysely` | Drizzle, raw SQL | Type-safe, no runtime overhead |
| Migrations | `node-pg-migrate` or `kysely-migration-cli` | Prisma migrate | SQL-file-based |
| Kafka client | `kafkajs` | `node-rdkafka` (native bindings) | KafkaJS is pure-JS, easier to set up |
| Config | `zod` schemas over `process.env` | dotenv alone | Validated, type-safe |
| Logging | `pino` | `winston` (slower), bunyan | Pino is the performance leader |
| Validation | `zod` | yup, joi, valibot | Zod is dominant for new code |
| Testing | `vitest` or stdlib `node:test` | Jest (slower, CommonJS-heavy) | Vitest is fast and ESM-native |
| HTTP testing | `supertest` + framework | manual fetch | Universal |
| Metrics | `prom-client` | OpenTelemetry metrics | Prometheus exposition |
| Tracing | OpenTelemetry Node SDK | manual | Auto-instrumentation for Fastify exists |

**Calibration:** the JS/TS ecosystem moves faster than any other on this list. Verify these defaults are current when the skill is used. Express in particular is on its way out for new projects — but lots of existing code uses it, so familiarity is fine.

---

## C# / .NET (.NET 8+)

| Concern | Default | Alternative | Reason |
|---|---|---|---|
| Web framework | ASP.NET Core Minimal APIs | Controllers (older), Carter (community) | Minimal APIs is the modern path |
| ORM / data access | `Dapper` (micro-ORM) or raw `Npgsql` | Entity Framework Core | Dapper exposes SQL; EF Core fine but hides more |
| Postgres driver | `Npgsql` | nothing competitive | Universal in .NET |
| Migrations | `DbUp` or `FluentMigrator` | EF Core migrations | SQL-first option for the learning value |
| Kafka client | `Confluent.Kafka` (official) | nothing serious else | Confluent's official binding |
| Config | `Microsoft.Extensions.Configuration` + strongly-typed binding | manual env | Built-in, idiomatic |
| Logging | `Microsoft.Extensions.Logging` + Serilog | NLog, log4net (legacy) | Serilog is the structured-logging default |
| Validation | `FluentValidation` | DataAnnotations | More expressive, decoupled from models |
| Testing | `xUnit` + `FluentAssertions` | NUnit, MSTest (declining) | xUnit is dominant for new projects |
| Mocking | `NSubstitute` or `Moq` | none (avoid) | NSubstitute is the modern preference; Moq has had recent controversy |
| Integration | `WebApplicationFactory<T>` + Testcontainers | manual | Built-in for ASP.NET Core |
| Metrics | OpenTelemetry .NET | App Insights, Prometheus.NET | OTel is the consolidating standard |
| Tracing | OpenTelemetry .NET + `System.Diagnostics.Activity` | Application Insights | OTel + built-in Activity API |
| Build | `dotnet` CLI | MSBuild directly (rarely needed) | The CLI is fine |

**Calibration:** modern .NET (8+) is meaningfully different from .NET Framework. Verify the user is on modern .NET. ASP.NET Core's Minimal APIs are a recent shift away from MVC controllers and worth leaning into.

---

## Ruby

| Concern | Default | Alternative | Reason |
|---|---|---|---|
| Web framework | `Sinatra` (for the challenge) or `Hanami` | Rails (massive, hides too much) | Rails is what the user knows; pick a smaller framework to expose what Rails hides |
| Bundler | bundler + RBS optional | nothing else | Universal |
| Postgres driver | `pg` gem | nothing else competitive | The driver |
| ORM / data access | `Sequel` | ActiveRecord (Rails), raw `pg` | Sequel is the Rails-alternative that doesn't hide as much |
| Migrations | Sequel migrations or `sequel-migrations` | Rails migrations | Match Sequel choice |
| Kafka client | `ruby-kafka` (Zendesk fork is current) | `karafka` (heavier framework) | ruby-kafka is the lower-level option |
| Config | `dry-configurable` or env-driven | Rails config | Lighter than Rails patterns |
| Logging | stdlib `Logger` + `lograge`-style formatting | semantic_logger | structlog-equivalent for Ruby |
| Validation | `dry-validation` | ActiveModel validations | Composable, Rails-free |
| Testing | `RSpec` | minitest | RSpec is dominant despite community ambivalence |
| Mocking | RSpec mocks | mocha (older) | Built into RSpec |
| Metrics | `prometheus-client` (Ruby) | StatsD-based | Prometheus exposition |
| Tracing | OpenTelemetry Ruby | manual | OTel auto-instrumentation for Rack |

**Calibration:** Ruby in 2026 is mostly Rails. Picking Sinatra/Hanami for the challenge is a *learning* choice — exposes more of the underlying machinery. Make this explicit. If the user objects ("but I want to learn Rails specifically"), Rails is fine — adjust the library choices accordingly.

---

## Cross-language defaults (apply everywhere)

| Concern | Default | Reason |
|---|---|---|
| Database | PostgreSQL 16+ | Universal, JSON support, mature |
| Message queue | Apache Kafka (KRaft, no Zookeeper) | Most-asked-about in interviews; if user wants alternatives, RabbitMQ/NATS/Redis Streams are fine |
| Container | Distroless or Alpine multi-stage | Small, secure |
| Local orchestration | Docker Compose | Universal |
| Observability stack | Prometheus + Grafana + OpenTelemetry → Tempo/Jaeger | The mainstream open-source stack |
| Version control | Git, conventional commits | Universal |
| CI | GitHub Actions (defaults) | What take-homes get pushed to |

These cross-language defaults stay constant unless the user explicitly says otherwise — they're the deployment context, not the language story.

---

## Special cases

**If the user names a specific company or interview target:** Match what that team uses. E.g., a user prepping for Shopify should learn Ruby with Rails-like patterns even if the skill normally picks Sinatra. The skill should ask this during elicitation.

**If the user says "what would Senior Engineers at $bigtech reach for":** Bias toward what's defensible in code review — boring, stdlib-leaning, observable. Less toward what's trending on Hacker News.

**If the user is targeting a takehome assignment:** Lean lighter on dependencies. The reviewer doesn't want to install 47 packages to read the code. Stdlib + 3-4 dependencies max is the sweet spot.
