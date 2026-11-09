# Claude Skills

Open-source [Claude skills](https://support.claude.com/en/articles/12512176-what-are-skills)
for software development workflows. Each skill is self-contained under `skills/`.

## Available skills

### [coding-challenge-architect](./skills/coding-challenge-architect/)

Designs progressive 10-step coding challenges for mid/senior backend engineers
learning a new language or refreshing rusty knowledge. Generates a complete
documentation set: a README, a conventions document, and ten step files that
compound from "hello world HTTP" to "observable multi-service system." Each step
is calibrated to your source-language background so the pitfalls actually land.

Supports Go, Rust, Java 21+, Kotlin, Scala 3, Python 3.12+, Node/TypeScript,
C# (.NET 8+), and Ruby as target languages. Source-language pitfalls calibrated
for Java, Kotlin, Scala, C#, Python, Node/TypeScript, Ruby, Go, Rust, and C/C++.

[See the skill →](./skills/coding-challenge-architect/)

### [faithful-porting](./skills/faithful-porting/)

Guides a faithful, golden-verified **port of an existing tool to a new language** —
reproduce the original's behaviour exactly (quirks and all) and prove each stage
**byte-for-byte** against reference outputs. Covers capturing the oracle (golden
I/O plus the legacy tool's own tests and fixtures), ordering the backlog by
verifiability and data dependency, a per-ticket RED → GREEN → verify loop with a
quirk catalog for the fidelity traps that break byte-parity, and a merge-often
delivery workflow.

Distilled from a real C#→Rust port that reached byte-for-byte parity across every
artifact, and pressure-tested with the `writing-skills` baseline/skill loop.

[See the skill →](./skills/faithful-porting/)

## Installation

### Claude Code

\`\`\`
/plugin marketplace add gfioretti/claude-skills
/plugin install <skill-name>@gfioretti
\`\`\`

### Claude.ai

Upload the specific skill folder via **Settings → Capabilities → Skills → Upload custom skill**.
See [Using skills in Claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude).

### Claude API

See the [Skills API quickstart](https://docs.claude.com/en/api/skills-guide).

## Contributing

PRs welcome. See [CONTRIBUTING.md](./CONTRIBUTING.md).

## License

Apache 2.0. See [LICENSE](./LICENSE).