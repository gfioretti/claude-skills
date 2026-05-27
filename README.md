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