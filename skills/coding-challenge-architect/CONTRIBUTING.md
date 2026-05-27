# Contributing to coding-challenge-architect

Thanks for considering a contribution to this specific skill. For collection-wide guidance (repo structure rules, general style, proposing new skills), see [../../CONTRIBUTING.md](../../CONTRIBUTING.md). This file covers what's most useful for *this* skill specifically.

## What's most valuable

### 1. Adding a new target language

If a backend-mainstream language isn't covered (or its coverage is shallow), adding it is the highest-impact contribution. Doing this well means touching three files:

- **`references/progression-patterns.md`** — add a new section with a 10-step table and calibration notes
- **`references/framework-defaults-by-language.md`** — add a section listing the recommended libraries with alternatives and reasons
- **`references/pitfalls-by-source-language.md`** — if the language is also commonly a *source* background, add a section describing the muscle memory developers carry into other languages

Bonus: include a worked example under `examples/` showing the skill's output for the new language.

Languages worth considering: Swift (server-side), Dart (server-side), Crystal, F#, Clojure. Use the existing entries as a model.

### 2. Updating stale library defaults

Ecosystems shift. `framework-defaults-by-language.md` reflects 2026 mainstream choices but won't stay current automatically. If you spot a default that's no longer the right pick:

- Open a PR with the change
- In the PR description, include a one-line justification: what shifted, when, and what's the new mainstream consensus
- Link to evidence where possible (release notes, deprecation announcements, ecosystem surveys)

### 3. Worked examples

A complete `examples/<source>-to-<target>/` walkthrough is one of the highest-signal contributions. It shows the skill's output for a concrete scenario and helps potential users calibrate expectations before they run the skill themselves.

A worked example should include all 12 files (`README.md`, `CONVENTIONS.md`, `step-01..step-10.md`) and a top-level `README.md` in the example directory explaining the inputs: target language + source background + cross-cutting preferences (build tool, deployment, messaging).

### 4. Bug fixes and clarifications

- Typos, broken links, outdated commands: open a PR directly
- Reference-file inconsistencies (e.g., a library mentioned in one file but not another): open a PR, no issue needed
- Substantive changes to the workflow in SKILL.md: open an issue first

## Style guidelines

These supplement the collection-wide guidelines in [../../CONTRIBUTING.md](../../CONTRIBUTING.md).

**The skill is opinionated by necessity.** Picks should be defensible based on what mainstream backend teams actually use in production in the current year — not what's trending on Hacker News, not what's most starred on GitHub. When in doubt, lean conservative.

**Be honest about trade-offs.** The skill explicitly surfaces "this picks X over Y because Z" in CONVENTIONS.md and step pitfalls sections. Don't pretend choices are uncontested when they aren't. The "interview signal vs production pragmatism diverges" framing throughout the skill is intentional.

**Mid/senior backend developer voice.** Concrete and concise. Avoid "best practices" hand-waving; avoid breathless framing. The audience already knows what an HTTP request is, what a database is, and what async means — they want help with the *specifics* of how a new language expresses those things.

**Match the existing tone.** Read the existing reference files before adding new ones. The voice is direct, slightly opinionated, willing to call out where the language community is wrong about itself.

## What's out of scope

These would require restructuring the skill significantly — if you want any of them, better to fork than to PR:

- **Frontend, mobile, ML, or embedded development.** The skill's whole shape (10 progressive steps building toward a multi-service backend system) doesn't fit these domains.
- **"Beginner-friendly" reframing.** The skill assumes meaningful programming experience. A beginner-oriented version would need different elicitation, different pitfalls, different pacing.
- **One-off coding exercises.** LeetCode-style problems aren't what this builds.
- **Removing opinionation.** "Make every choice configurable" turns a useful skill into an unhelpful generator. The opinions are the point.

## Process

1. **For non-trivial changes, open an issue first.** Easier to align on scope and approach before writing.
2. **Keep PRs focused.** One concern per PR — a new target language, a stale default update, or a fix. Don't bundle.
3. **PRs should update SKILL.md *or* a reference file, rarely both.** If your change requires touching both, that's a signal to re-examine whether the change belongs at the workflow level or the reference level.
4. **Include a short rationale in the PR description.** "Why this change" is more useful than "what changed" — the diff shows what changed.

## License

By submitting a PR, you agree that your contribution is licensed under Apache 2.0, matching the rest of the collection. See [../../LICENSE](../../LICENSE).
