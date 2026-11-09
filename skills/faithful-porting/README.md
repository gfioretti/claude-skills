# faithful-porting

A Claude skill for **faithfully porting an existing tool to a new language**,
verified **byte-for-byte** against the original's output.

See [`SKILL.md`](./SKILL.md) for the full process. In short:

- **Source = spec, outputs = oracle.** Reproduce the original's behaviour exactly
  (quirks and all) and prove each stage against reference outputs — not against
  self-consistent tests.
- **Phases:** understand → capture the oracle (golden I/O + the legacy tool's own
  tests/fixtures) → brainstorm language/name/architecture → scaffold + CI and
  auto-merge → plan the backlog (ordered by verifiability + data dependency,
  broken down just-in-time) → implement per ticket (RED → GREEN → verify,
  re-reading the exact source) → finish.
- **References:** [verification](./references/verification.md) (the oracle ladder),
  [faithful replication](./references/faithful-replication.md) (the quirk catalog
  of fidelity traps), [planning board](./references/planning-board.md), and
  [workflow](./references/workflow.md).

Distilled from a real C#→Rust port that reached byte-for-byte parity on every
artifact, and pressure-tested with the `writing-skills` baseline/skill loop.
