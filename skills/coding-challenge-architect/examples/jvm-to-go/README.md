# Example: JVM developer learning Go

A complete worked output of the `coding-challenge-architect` skill, generated for the following inputs:

| Input | Value |
|---|---|
| Target language | Go 1.23 |
| Source background | JVM (Java/Kotlin/Scala), 10+ years |
| Proficiency mode | Refreshing (had used Go previously, rusty now) |
| Interview context | Take-home assignments + system design rounds |
| Deployment model | Containers (Docker + Compose) |
| Messaging primitive | Apache Kafka (KRaft, no Zookeeper) |
| Build tool | `make` with per-service Makefiles + top-level orchestrator |
| Dependency management | Vendored per service |
| Architectural discipline | DDD as design discipline, not folder layout |
| Repository structure | Multi-module monorepo with `go.work` |

The result: **Linkr**, a URL shortener with click analytics and content moderation. Three Go services communicating over Kafka topics (`link.created`, `link.clicked`, `link.moderated`), each exposing REST APIs. Each service is its own Go module; `pkg/events/` is a shared module holding wire-format DTOs as the anti-corruption layer between bounded contexts.

## What's in this folder

- **`README.md`** — the challenge's main README (architecture diagram, step table, how to use, tooling)
- **`CONVENTIONS.md`** — the cross-cutting rules (Make targets, vendoring workflow, multi-module monorepo bootstrap, DDD-as-discipline guidance)
- **`step-01-project-skeleton.md` ... `step-10-observability-and-ci.md`** — the ten progressive steps

Read `README.md` first to see the framing, then `CONVENTIONS.md` for the rules, then the step files in order.

## Why this is useful

If you're considering using the skill, this example shows you exactly what to expect: the level of detail, the voice, the structure, the trade-off framing. The skill is opinionated — this example demonstrates *how* opinionated and where it surfaces alternatives.

If you're a contributor adding a new language to the skill, this example is the bar to clear for an `examples/` addition.

## A note on the choices baked in

The conventions reflect a specific scenario (operator-savvy backend dev, take-home interview prep, real production-shaped artifact). Other valid choices for similar inputs:

- Single-module repo with `cmd/<service>/` directories (simpler, less independent)
- No vendoring (smaller repo, requires network at build time)
- Lighter architectural discipline (idiomatic Go straight up, no DDD framing)

The skill would have produced different output for any of these. Run it yourself with your own preferences to see how the trade-offs shift.
