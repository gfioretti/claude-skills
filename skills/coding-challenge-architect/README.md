# coding-challenge-architect

A Claude skill that designs progressive 10-step coding challenges for mid/senior backend engineers learning a new language or refreshing rusty knowledge. Each challenge compounds into a working multi-service system with REST, persistence, and async messaging — calibrated to your source-language background so the pitfalls actually land.

## What it produces

Given a target language and your background, the skill generates a complete documentation set: a `README.md` framing the challenge, a `CONVENTIONS.md` with build/dependency/architecture rules, and ten `step-NN-<theme>.md` files that compound from "hello world HTTP" to "observable multi-service system."

See [examples/jvm-to-go/](../../../../../../skills/coding-challenge-architect/examples/jvm-to-go/) for a complete worked example — a JVM developer learning Go through a URL shortener with click analytics and content moderation, three Go services communicating over Kafka.

## Why this exists

Most "learn a new language" resources are either:

1. **Tutorials** that walk through syntax but never produce a real system, or
2. **Sample projects** that show finished code but don't compound the way a learner needs.

Neither is well-suited to a mid/senior backend engineer who already knows what a struct is, has shipped production services, and wants to internalize a new language's *idioms* — the conventions that distinguish "writes code that compiles" from "writes code a senior reviewer respects."

This skill produces challenges shaped for that audience. Each step targets a different cluster of language concepts. Each step has a "Pitfalls" section calling out instincts from your *source* language that will mislead you. The whole thing functions as interview prep, a portfolio piece, or both.

## Supported target languages

The skill has dedicated templates for backend-mainstream languages as of 2026:

- **Go** (1.22+)
- **Rust** (2024 edition)
- **Java** (21+ with virtual threads)
- **Kotlin** (with Ktor + coroutines)
- **Scala 3** (Cats Effect or ZIO)
- **Python** (3.12+ with FastAPI + uv)
- **Node.js / TypeScript** (with Fastify)
- **C# / .NET 8+** (Minimal APIs)
- **Ruby** (with Sinatra/Hanami, not Rails)

Other languages will fall back to the closest analog with a disclaimer. See [Limitations](#limitations) below.

## Supported source-language backgrounds

The "Pitfalls" sections — the part that makes each challenge personal — are calibrated for these backgrounds:

Java, Kotlin, Scala, C# (.NET), Python, Node.js / TypeScript, Ruby, Go, Rust, C/C++.

If your background is outside this list, the skill will use the closest analog and tell you so.

## Installation

This skill lives in the [`<your-github-username>/claude-skills`](../../) monorepo. Each skill can be installed individually.

### Claude Code

```
/plugin marketplace add <your-github-username>/claude-skills
/plugin install coding-challenge-architect@<your-github-username>
```

After installing, the skill is available — just describe what you want.

### Claude.ai

Upload this folder (`skills/coding-challenge-architect/`) via **Settings → Capabilities → Skills → Upload custom skill**. See [Using skills in Claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude) for the current upload flow.

### Claude API

See the [Skills API quickstart](https://docs.claude.com/en/api/skills-guide) for uploading custom skills.

## How to use it

Once installed, ask Claude something like:

> "Design a coding challenge for a Python developer learning Rust, with backend focus."

> "I want a structured 10-step refresher project for Go. I have JVM background and I'm prepping for take-home interviews."

> "Help me build muscle memory in Scala 3 — I'm a Java dev who hasn't touched Scala in years."

The skill will ask 6–8 elicitation questions (target language, source background, proficiency mode, interview context, deployment/messaging preferences, etc.), propose a system architecture, and then produce the 12-file documentation set.

Expect a few minutes of back-and-forth. You'll likely iterate on the system's domain and the step sequencing before settling.

## Limitations

This skill is scoped to **backend systems** — services with REST, persistence, and async messaging. It is **not** suitable for:

- **Frontend, mobile, embedded, or ML development.** These domains have different shapes; a backend-services template would force-fit and produce something useless.
- **Pure language tutorials for absolute beginners.** The skill assumes meaningful programming experience.
- **One-off coding exercises** (LeetCode-style problems).
- **Specific applications you actually want to deploy.** The output is a learning artifact, not a product.

If you want any of those, this isn't the right tool — look elsewhere.

## What's in this folder

```
coding-challenge-architect/
├── SKILL.md                                   # the skill itself (read by Claude)
├── README.md                                  # this file
├── CONTRIBUTING.md                            # skill-specific contribution notes
├── references/
│   ├── step-template.md                       # structure for each step file
│   ├── progression-patterns.md                # 10-step templates per target language
│   ├── pitfalls-by-source-language.md         # source-language idiom mismatches
│   └── framework-defaults-by-language.md      # library picks per target language
└── examples/
    └── jvm-to-go/                             # full worked example (Linkr challenge)
```

The SKILL.md is ~200 lines. Reference files are 100–250 lines each. Read SKILL.md first to understand what the skill does; read the references to understand how it makes its choices.

## A note on opinions

The skill is opinionated by design. It picks `pgx` over `lib/pq` for Go. It picks Javalin over Spring for Java learning challenges. It picks `asyncpg` over SQLAlchemy for Python. It picks vendored dependencies and `make`-based builds. These are *learning* choices — exposing what popular frameworks hide — and they reflect what production backend teams in 2026 actually reach for, not what's trending on Hacker News.

Each pick is defensible but not the only valid answer. The skill explicitly surfaces alternatives in `CONVENTIONS.md` for every challenge it generates, so users see what *wasn't* picked and why.

If your team uses different defaults (and many do — especially in enterprise shops), tell the skill during elicitation and it'll honor your preferences.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidance specific to this skill, and [../../CONTRIBUTING.md](../../CONTRIBUTING.md) for the collection-wide contribution policy.

The most useful contributions are:

1. **New target languages** — adding entries to the three reference files
2. **Updating stale library defaults** as ecosystems shift
3. **Worked examples** under `examples/` for new language combinations

## License

Apache License 2.0. See [../../LICENSE](../../LICENSE).

## Acknowledgments

This skill was built iteratively through actual use — a JVM developer prepping for Go interviews produced the original Linkr challenge that became the worked example in `examples/jvm-to-go/`. The skill abstracts the lessons from that process: which questions to ask up front, which design choices to make explicit, which idioms to surface for which source-language backgrounds.
