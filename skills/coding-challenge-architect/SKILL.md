---
name: coding-challenge-architect
description: Design progressive, multi-step coding challenges that compound into a working multi-service backend system, calibrated for mid/senior engineers learning a new language or framework hands-on. Use this skill whenever the user wants to create a structured backend coding challenge to learn or refresh a programming language, prep for technical interviews or take-home assignments, build muscle memory in a new server-side stack, or explore a new framework through realistic project work. Trigger on phrases like "design a coding challenge", "interview prep project", "language refresher", "compounding exercises", "hands-on learning project", "structured project for learning X", "new language refresher with realistic system design", or any request from a backend engineer for tailored progressive practice material — even if they don't use the word "challenge."
---

# Coding Challenge Architect

Design progressive coding challenges that compound across ~10 steps into a working multi-service backend system. Each step targets a different cluster of language and ecosystem idioms; the whole thing functions as both a hands-on learning vehicle and an interview-prep portfolio piece.

**Target audience:** mid/senior backend engineers learning a new language, framework, or stack — either from scratch (fluent in language A, new to language B) or refreshing rusty knowledge.

This skill produces **a documentation set, not code**. The output is a set of markdown files the user works through over multiple sessions — each step is theirs to implement.

## When this skill fits

Use this skill when:

- The user is a backend engineer (or moving into backend) and wants structured practice
- They want a real multi-service system at the end, not 10 disconnected exercises
- They want idiomatic code — what production teams in 2026 actually write, not "code that compiles"
- They're interview-aware: take-homes, system design, or live coding are on their mind
- They want their existing language background respected (the pitfalls section is the value-add)

## When this skill does NOT fit

Be honest about scope. Redirect users elsewhere if:

- **Frontend/mobile/embedded/ML** — this skill assumes "backend services with persistence and messaging." Mobile development, ML pipelines, frontend frameworks, embedded systems all have different shapes.
- **Pure language tutorial** — if the user is brand-new to programming, this isn't a starting point.
- **One-off coding exercises** — LeetCode-style problems aren't what this builds.
- **A specific application they want to deploy** — this is a learning artifact, not a real product.

If unsure, say so and ask the user to clarify their goal before drafting.

## Workflow

### 1. Elicit (one round of questions, asked together)

Before drafting anything, get these from the user. If the conversation already answers some, don't re-ask — confirm and ask the rest. Always ask in one message so the user can answer in one shot.

**Required:**

1. **Target language and version.** "Go 1.23", "Rust 2024 edition", "Python 3.12", "Java 21". Version matters: community conventions shift (Go's `slog` is 1.21+; Java virtual threads are 21+; Python's `uv` consolidated post-3.11).

2. **Source language and recency.** Their primary backend language today. Distinguish:
   - "I write production code in X today" — strong source-language pitfalls
   - "I used X heavily 2+ years ago" — softer pitfalls, more recap
   - "I know multiple languages roughly equally" — ask which dominates their muscle memory

3. **Proficiency mode for the target language:**
   - **"Refreshing"** — used the target before, months or years ago. Skip basics; emphasize idioms and recent additions.
   - **"Learning from scratch"** — fluent elsewhere, new here. Step 1 needs more scaffolding; type-system fundamentals deserve more time in step 2.

4. **Interview context.** Take-home assignments? Live coding? System design rounds? All three? A specific company target? This shapes which idioms get emphasized.

5. **Deployment model preference.** Default to **containers (Docker + Compose)** unless they say otherwise. Sensible alternatives: bare binaries on VMs, serverless (less common for this kind of system). If they have no preference, use the default.

6. **Messaging primitive preference.** Default to **Apache Kafka** unless they say otherwise. Common alternatives: RabbitMQ, NATS, Redis Streams. If they have no preference, use the default and mention alternatives in CONVENTIONS.md.

**Optional but high-value:**

7. **Cross-cutting conventions they want followed.** Build tool (Make, Just, Gradle), dependency management (vendoring, lockfiles), architectural discipline (DDD, hex arch, plain layered), monorepo structure. Many senior engineers have strong opinions; surface them now.

8. **Target company or stack.** If they mention a specific company or team, the skill should bias library choices toward what that team actually uses. Ask if they have a target.

### 2. Propose the system

Before writing any steps, propose **one paragraph + a system diagram** describing the final state of the challenge. The system should:

- Be composed of **2–4 services** — enough surface area to exercise the language's whole story, not so much that the user drowns
- Include at least **one async/messaging boundary** (Kafka by default) — concurrency and eventual consistency are where languages diverge most and where interviews probe deepest
- Include at least **one persistence boundary** (Postgres by default) — touches the language's I/O, error handling, and connection-pool story
- Expose at least **one external interface** (REST API by default; gRPC if user has a preference) — surfaces serialization, validation, routing
- Have a **realistic domain** the user finds engaging — URL shortener, build cache, log aggregator, package registry, chat backend, code search, feature flag service. Avoid overdone domains (todo list, blog, ecommerce) unless the user specifically asks. Pick something with non-trivial state transitions.

Get explicit confirmation on the system **before** writing step files. Domain matters: a poorly-fitting domain will demotivate the user three steps in.

### 3. Design the 10-step progression

Read `references/progression-patterns.md` and pick the template matching the user's target language. The file has per-language templates for Go, Rust, Java, Kotlin, Scala, Python, Node/TypeScript, C#, and Ruby — start from one of these, don't force-fit a generic skeleton.

Within the template, calibrate based on proficiency mode:
- **Refresher mode:** step 1 is brief (boot a service); spend more time on idioms in steps 2-5.
- **Learning-from-scratch mode:** step 1 is more elaborate (explain why `go mod`/`cargo`/`uv` work the way they do); step 2 may stretch to cover type-system fundamentals.

Each step must:
- **Introduce a different cluster of language concepts** — don't repeat themes across steps.
- **Compound** on what came before. By step 7 the user should be modifying step-3 code, not starting fresh.
- **Be time-boxable to 2–4 hours each.** Steps that take 8 hours kill motivation; split them.
- **Surface interview-relevant tradeoffs.** Sync vs async publish. Idempotency strategies. State machine design. Worker pool sizing.
- **Have a clear acceptance check** — shell commands or tests that verify the user actually built the thing.

Read `references/step-template.md` for the exact structure each step file must follow.

### 4. Pick libraries and frameworks

Read `references/framework-defaults-by-language.md` for the recommended libraries for the user's target language. Follow these defaults unless:

- The user expressed a preference (during elicitation or earlier in the conversation)
- The user named a target company with known stack preferences
- The defaults are stale (verify currency — the defaults file is a starting point, not gospel)

In CONVENTIONS.md, list each library choice with the alternatives parenthetically and a one-line reason. The user benefits from knowing what *wasn't* picked and why.

### 5. Write the conventions document

Cross-cutting rules that apply to every step belong in `CONVENTIONS.md`, not repeated 10 times. Capture:

- **Build tool**: full Makefile/build.sh/Taskfile with required targets per the user's preference
- **Dependency management**: how new deps are added, where they're committed, how reproducibility is enforced (vendoring, lockfiles, etc.)
- **Architectural discipline**: if the user asked for DDD, hex arch, or similar, this is where the layering rules live. Be **specific about what's kept and what's dropped** in favor of the target language's idioms. Senior engineers respect honesty about trade-offs more than they respect dogma.
- **Module boundaries**: if the system is a monorepo with multiple services, define how they're isolated (separate modules, workspace files, import lints).
- **Library choices**: the picks from step 4 with alternatives and reasons.
- **Deployment model**: how services run locally (compose) and would run in production.

The conventions doc should be honest about trade-offs. Users coming from another language will be reluctant to abandon habits unless they understand why.

### 6. Write the README

The README is the entry point. Include:

- A one-paragraph framing of the challenge and who it's for
- The system architecture diagram (ASCII or mermaid)
- A table of the 10 steps with the language concepts each introduces
- "How to use this challenge" — time-boxing advice, what to skip, what to obsess over
- Recommended tooling (linters, formatters, debuggers — anything language-specific)
- Library choices worth pre-deciding (referencing CONVENTIONS.md)
- Interview-prep framing: what to keep notes on, what to talk about in interviews

The README should reference CONVENTIONS.md prominently and link to every step file.

### 7. Write the steps

One file per step, named `step-NN-short-name.md`. Each follows the structure in `references/step-template.md`.

For each step, the **pitfalls section** is where the challenge becomes personal to the user. Read `references/pitfalls-by-source-language.md` to find the user's source language and pull the relevant pitfalls. Don't just copy them — adapt to the specific step's concepts.

**Draft step 1 and step 5 first, then check in with the user.** These are the most architectural steps and the user's reaction will surface miscalibrations early. Step 1 sets the layout and tone; step 5 establishes testing posture. If both land, the middle steps will mostly follow.

Then write the rest in order.

### 8. Present and iterate

Present the file set with `present_files` (or equivalent). Then ask for feedback on:

- **Shape:** Does the system make sense? Do the 10 steps cover what they wanted?
- **Depth:** Are individual steps too dense or too thin?
- **Calibration:** Are the pitfalls hitting their actual instincts? Is the proficiency mode right?

Be ready to:
- Adjust the system's domain if the user finds it boring
- Re-sequence steps if a concept lands too early or too late
- Tighten or expand individual steps based on the user's pacing
- Drop or add concepts based on their experience level

The first draft is rarely the right shape. Treat it as a starting point.

## Honest calibration

Some realities to keep in mind across the workflow:

- **Idiomatic varies by language.** What's idiomatic Java would be over-engineered Go. What's idiomatic Rust would be impossible to compile in Python. When in doubt, lean toward what production teams in that language actually write — not what "best practices" articles claim. They diverge.

- **Cross-cutting conventions sometimes fight idioms.** A user asking for "DDD throughout" in Go is asking for a layered structure that's alien to Go culture. Be honest: there's a difference between DDD-the-discipline (aggregates, bounded contexts, repository contracts) and DDD-the-folder-layout (`domain/application/infrastructure/`). The first translates well; the second doesn't. Surface this trade-off and let them choose.

- **Interview signal vs production pragmatism diverges.** Vendoring dependencies is uncommon in greenfield Go projects but signals operational maturity to senior reviewers. Heavy DDD ceremony is uncommon in idiomatic Go but signals architectural thinking to enterprise hiring managers. The user's interview context shapes which trade-off wins. Surface these explicitly.

- **The challenge is opinionated by necessity.** Every choice (this library, this layout, this testing approach) is defensible but not the only valid answer. Each step should acknowledge alternatives where they exist — this models the kind of thinking interviewers reward.

- **Target language ecosystems shift.** Library defaults in `references/framework-defaults-by-language.md` reflect 2026 mainstream choices but ecosystems evolve. If you suspect a default is stale, say so to the user and verify before committing.

## Output format

The final output is a directory containing:

```
<challenge-name>/
├── README.md
├── CONVENTIONS.md
├── step-01-<theme>.md
├── step-02-<theme>.md
├── step-03-<theme>.md
├── step-04-<theme>.md
├── step-05-<theme>.md
├── step-06-<theme>.md
├── step-07-<theme>.md
├── step-08-<theme>.md
├── step-09-<theme>.md
└── step-10-<theme>.md
```

Place this under `/mnt/user-data/outputs/<challenge-name>/` and present the files with `present_files`. Lead with `README.md` so the user opens that first.

## Reference files

- `references/step-template.md` — the exact structure each step file must follow, with annotations explaining why each section exists. Read this before writing the first step.
- `references/progression-patterns.md` — per-language templates for the 10-step progression. Pick the matching template for the target language; don't force-fit. Has calibration notes for each.
- `references/pitfalls-by-source-language.md` — the curated source-language pitfalls for filling in the "Pitfalls" section of each step. Read the section matching the user's stated source language.
- `references/framework-defaults-by-language.md` — recommended library and tooling defaults per target language, with alternatives and reasons. Use these unless the user has specific preferences.
