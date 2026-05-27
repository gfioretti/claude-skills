# Contributing

Thanks for considering a contribution. This file covers the general approach for the whole collection. Each skill also has its own `CONTRIBUTING.md` under `skills/<skill-name>/` with skill-specific guidance — read that too if you're working on an existing skill.

## What's most valuable

### Improving an existing skill

The most common contribution. Each skill has its own `CONTRIBUTING.md` describing what's most useful for that skill specifically (e.g., adding a new language to `coding-challenge-architect`). Read the skill's contribution guide first.

For changes that span multiple skills (e.g., updating shared conventions), open an issue first to discuss scope.

### Proposing a new skill

New skills are welcome if they fit the software-development theme of this collection. Open an **issue first** with:

1. **What the skill does** in one paragraph
2. **What audience it targets** and what problem it solves
3. **Why this collection** rather than a standalone repo (does it benefit from being near the other skills here?)
4. **A rough outline** of the SKILL.md content

I'd rather align on scope before either of us writes code. If the proposal fits, then a PR adding `skills/<new-skill-name>/` with the full structure is welcome.

If the proposed skill doesn't fit this collection's theme, that's not a rejection of the skill itself — fork it into your own repo. The collection's coherence depends on staying focused.

### Bug fixes and small improvements

- Typos, broken links, outdated install commands: open a PR directly
- Stale library defaults in a reference file: open a PR with a one-line rationale in the description
- Substantive workflow changes to a SKILL.md: open an issue first

## Style guidelines

These apply to all skills in this collection. Individual skills may add more specific guidance in their own `CONTRIBUTING.md`.

**Opinionated by design.** Skills here pick defaults explicitly. The picks should be defensible based on what mainstream practitioners actually use in production — not what's trending, not what's most starred. When in doubt, lean conservative.

**Honest about trade-offs.** When a skill picks one approach over another, surface the alternatives and the reasoning. Pretending a contested choice is uncontested produces skills that don't hold up under interview-grade scrutiny.

**Direct voice.** Concrete and concise. Avoid "best practices" hand-waving. The audience is practitioners, not students.

**Match the existing tone.** Before writing new content, read existing reference files in the relevant skill. The voice should feel consistent across the collection.

## Repo structure rules

When adding a new skill, follow this structure:

```
skills/<skill-name>/
├── SKILL.md              # required — the skill itself
├── README.md             # required — human-facing README
├── CONTRIBUTING.md       # required — skill-specific contribution notes
├── references/           # optional — reference files Claude loads on demand
│   └── *.md
└── examples/             # optional but encouraged — worked examples
    └── <example-name>/
        └── ...
```

Naming conventions:
- Skill folder names: `lowercase-with-hyphens`
- Reference file names: `lowercase-with-hyphens.md`
- Example folder names: descriptive of the input scenario, lowercase-with-hyphens

## What's out of scope for this collection

This collection focuses on **software development workflows**. Skills outside that theme should be hosted elsewhere:

- Creative writing, design, art
- Business operations, sales, marketing
- Personal productivity unrelated to development
- Healthcare, legal, finance domain expertise

That's not a value judgment — those skills are useful — they just don't belong in this catalog.

Also out of scope:

- **Skills that execute untrusted code.** Skills here are markdown-only. If a skill genuinely needs scripts (e.g., for code generation or file manipulation), it requires extra scrutiny — open an issue first.
- **Skills that depend on private services or paid APIs without alternatives.** Skills should work for anyone who installs them.

## Process

1. **For non-trivial changes, open an issue first.** Aligning on scope before writing saves everyone time.
2. **Keep PRs focused.** One skill, one concern per PR. Don't bundle unrelated changes.
3. **Include rationale in the PR description.** "Why this change" beats "what changed" — the diff shows what.
4. **Test before submitting.** If your change affects a skill's behavior, install it locally and verify it produces sensible output for a representative prompt.

## Code of conduct

Be respectful. Disagreements about technical choices are healthy; personal attacks aren't. Lead with reasoning when proposing changes that contradict existing opinions.

## License

By submitting a contribution, you agree it's licensed under Apache 2.0, matching the rest of the collection. See [LICENSE](./LICENSE).
