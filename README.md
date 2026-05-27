# Claude Skills

A collection of Claude skills focused on software development workflows. Each skill is self-contained in its own folder under `skills/` with its own README, reference files, and (where applicable) worked examples.

## Available skills

### [coding-challenge-architect](./skills/coding-challenge-architect/)

Designs progressive 10-step coding challenges for mid/senior backend engineers learning a new language or refreshing rusty knowledge. Generates a complete documentation set: a README, a conventions document, and ten step files that compound from "hello world HTTP" to "observable multi-service system." Each step is calibrated to your source-language background so the pitfalls actually land.

Supports Go, Rust, Java 21+, Kotlin, Scala 3, Python 3.12+, Node/TypeScript, C# (.NET 8+), and Ruby as target languages. Pitfalls sections calibrated for Java, Kotlin, Scala, C#, Python, Node/TypeScript, Ruby, Go, Rust, and C/C++ as source backgrounds.

[See the skill →](./skills/coding-challenge-architect/)

---

_More skills will land here as they're built. Each new one gets its own folder under `skills/` and a row in the table above._

## Installation

Each skill installs independently. The general patterns:

### Claude Code

Register this repo as a marketplace and install individual skills:

```
/plugin marketplace add <your-github-username>/claude-skills
/plugin install <skill-name>@<your-github-username>
```

Replace `<skill-name>` with the folder name under `skills/` (e.g., `coding-challenge-architect`).

### Claude.ai

Upload the specific skill folder (e.g., `skills/coding-challenge-architect/`) via **Settings → Capabilities → Skills → Upload custom skill**. See [Anthropic's docs](https://support.claude.com/en/articles/12512180-using-skills-in-claude) for the current flow.

### Claude API

Upload via the Skills API — see the [quickstart guide](https://docs.claude.com/en/api/skills-guide).

## Repository layout

```
claude-skills/
├── README.md                          # this file — catalog of all skills
├── LICENSE                            # Apache 2.0 (applies to all skills here)
├── CONTRIBUTING.md                    # general contribution guide
├── .gitignore
└── skills/
    └── <skill-name>/
        ├── SKILL.md                   # the skill itself (read by Claude)
        ├── README.md                  # human-facing README for the skill
        ├── CONTRIBUTING.md            # skill-specific contribution notes
        ├── references/                # reference files Claude loads on demand
        └── examples/                  # worked examples of what the skill produces
```

This mirrors the [Anthropic skills repo](https://github.com/anthropics/skills) layout — one folder per skill, self-contained, with a top-level catalog.

## Themes

All skills here focus on **software development workflows**: writing code, learning languages, building systems, prepping for interviews, structuring projects. If you're looking for skills in other domains (creative writing, data analysis, business operations), check the [Anthropic skills repo](https://github.com/anthropics/skills) or other community collections.

## Security note

Skills installed from a public repo execute arbitrary code in Claude's environment. The skills in this collection are **pure markdown** — no scripts, no executables — but install only from sources you trust as a general practice. See [Anthropic's guidance](https://support.claude.com/en/articles/12512198-creating-custom-skills) for more.

## Contributing

Pull requests welcome for:

- **New skills** that fit the software-development theme
- **Improvements** to existing skills (new languages, stale-default updates, worked examples)
- **Bug fixes and clarifications**

See [CONTRIBUTING.md](./CONTRIBUTING.md) for the general approach and each skill's own `CONTRIBUTING.md` for skill-specific guidance.

## License

Apache License 2.0. See [LICENSE](./LICENSE). Applies to all skills in this repository unless individual skills note otherwise (none currently do).

## Acknowledgments

These skills are built iteratively through actual use — each one started as a real problem the author wanted Claude to help with, then abstracted into something reusable. The voice across the collection is intentionally direct and opinionated: shipping skills that pretend every choice is equally valid produces skills that aren't useful.
