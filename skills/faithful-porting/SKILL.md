---
name: faithful-porting
description: >
  Use when porting, rewriting, migrating, or reimplementing an existing program
  into a new language or framework and the new output must match the original —
  "port X to Y", language rewrite, legacy reimplementation, byte-for-byte or
  behaviour parity, reproducing a legacy tool's output.
metadata:
  author: gvfioretti
  version: "1.1.0"
---

# Faithful, golden-verified porting

Port an existing tool to a new language by treating the **original source as the
spec** and its **reference outputs as the oracle**: reproduce behaviour exactly
(quirks and all) and prove each stage byte-for-byte against the original.

## When to use

- Rewriting/reimplementing an existing program in another language where the new
  output must match the old (file formats, byte streams, deterministic results).

**Not** for: greenfield features, or a redesign where diverging from the original
is the *point*. If you're free to improve the behaviour, this discipline fights
you — use ordinary feature development instead.

## The two sources of truth

1. **Source code = the spec.** Port it faithfully, including bugs and quirks, with
   exact file:line references. You are translating, not designing.
2. **Reference outputs = the oracle.** Objective proof. Without one, "TDD" only
   proves your port is self-consistent, not that it's *correct*.

## Process

Work the phases in order. Check each off before moving on.

- [ ] **Phase 0 — Understand.** Read the legacy codebase: what it does, its stack
  and structure, and the tool's purpose + the *intended users of the ported
  version* (that shapes language and architecture).
- [ ] **Phase 1 — Capture the oracle** (both sources). (a) Run the original under
  its **pinned default settings** — *find what the defaults actually are*; a
  single wrong default (nesmith's dither amount was `0` = a no-op) breaks every
  match — and save input/output pairs as golden files. (b) **Scan the legacy
  codebase for its own tests, fixtures, and sample data** and harvest them (reuse
  fixtures as inputs; port its assertions). See
  [Verification strategy](references/verification.md). If it neither emits outputs
  nor ships tests, find a capture path (export, instrument, intercept).
- [ ] **Phase 2 — Decide the port** with the human (use
  `superpowers:brainstorming`): **target language**, **project name** (settle it
  *before* scaffolding — it seeds the repo, modules, identifiers), and
  **architecture** (deliberate — don't assume hexagonal/DDD *or* flat).
- [ ] **Phase 3 — Scaffold + automate.** Create the repo, push upstream, and stand
  up the delivery loop *from PR #1*: CI gate (format/lint/test), branch
  protection, **auto-merge on green**. See [Workflow](references/workflow.md).
- [ ] **Phase 4 — Plan the backlog.** Identify epics with the human and **order by
  verifiability + data dependency** (deterministic/golden-verifiable first,
  riskiest heuristic last) — not business priority. Write `planning/` + a
  `BACKLOG.md` + a README. **Break down only the next epic; keep the rest coarse
  (just-in-time)**, re-sequencing as dependencies surface. **Scope first:** present
  each feature's scope (story + tasks) and flag the genuine decisions *before* any
  code; let the human adjust the plan, then wait for the go. A plan is cheap to
  change; built code isn't. See [Planning board](references/planning-board.md).
- [ ] **Phase 5 — Implement per ticket** (one ticket per branch off fresh `main`):
  **RED** a failing test pinning behaviour → **GREEN** a faithful, mechanical port
  (**re-read the exact source lines as you write; never port from memory**;
  globals → a state struct — see
  [Faithful replication](references/faithful-replication.md)) → **verify** against
  the oracle, full suite, lint/format → **update the ticket's status (and its
  parent story-row) in this branch** → PR with auto-merge → watch it land → next
  ticket off fresh `main`. A stage with no standalone golden: build the pipeline
  harness to produce one, then implement against it.
  - **Wait for an explicit "start ticket N"** before implementing — don't build on
    a vague or background signal. Once the human says "keep rolling," run the loop
    unattended ticket-by-ticket and pause only for a real decision.
  - **Scaffold-only mode** — some humans want to *own* the port. Then write **only
    the RED test** on the feature branch, confirm it fails, hand off, and **wait**;
    do not write the GREEN implementation until asked. RED and the human's GREEN
    commits share the *same* feature branch, which PRs once green. (This is
    different from pre-scaffolding many tickets ahead — see the holding-branch note
    in [Workflow](references/workflow.md).)
- [ ] **Phase 6 — Finish.** Adapters/CLI, docs, reconcile the board, update memory.
- [ ] **Phase 7 — Beyond parity.** When the **end-to-end goldens pass, the porting
  discipline is done** and this skill no longer applies. Further capabilities (a
  GUI, new features) are **ordinary development** — brainstorm them and diverge
  from the original freely. If the engine was built hexagonally, new front-ends
  **reuse the core through new adapters** rather than forking it (nesmith's egui
  GUI drove `core` via in-memory `ImageSource`/`ArtifactSink` fakes — no engine
  changes). Switch to normal feature dev + `superpowers:brainstorming` here.

## Quick reference

| Per-ticket loop | Pick the oracle (strongest available) |
|---|---|
| RED test → GREEN faithful port → verify vs oracle → update ticket status in-branch → PR `--auto` → watch → next | 1. Golden byte-match · 2. Computed vector (run the real algorithm, never guess) · 3. Structural invariant (+ prove end-to-end later) · 4. End-to-end golden only |

Pairs with `superpowers:test-driven-development` (RED→GREEN),
`superpowers:verification-before-completion` (prove before claiming done), and
`superpowers:brainstorming` (name/language/architecture).

## One worked example (nesmith: DZ4 compression)

The original's DZ4 compressor was ~250 lines of C# driven by module-level globals
(`rle_index`, `rle_count`, `hacky_index`, …). The faithful port:

- **Globals → a private state struct.** `struct Dz4 { rle: Vec<u8>, rle_count:
  usize, hacky_index: i64, … }` with one method per phase, behind a pure public
  `fn compress(&[u8]) -> Vec<u8>`.
- **Quirks preserved**, not "fixed": the `hacky_index` header-skip, the
  back-reference-in-the-middle-of-a-literal branch, `i64` to hold the `-2`
  sentinel.
- **Golden test as the oracle** (the raw inputs and `.dz4` outputs already lived
  in `tests/golden/`):

```rust
#[test]
fn simcity_chr_matches_golden_dz4() {
    let input = read_golden("simcity", "final.chr");
    let expected = read_golden("simcity", "final.chr.dz4");
    assert_eq!(compress(&input), expected);
}
```

It matched byte-for-byte on the first complete implementation — because the port
was mechanical and the oracle was real.

## Common mistakes

- **Skipping the oracle** → tests that only prove self-consistency.
- **Guessing a test vector** instead of computing it (run the real algorithm / a
  throwaway script) or using a golden.
- **Porting from memory** instead of re-reading the exact source lines.
- **"Improving" the original's behaviour** mid-port (do that *after* parity, if at
  all).
- **Missing the original's default settings** that produced the goldens.
- **Over-detailing distant epics** up front (break down just-in-time).
- **Updating board status in one end-of-project pass** → stale tickets. Update
  status (parent rows included) *in the implementing branch*.
- **Merging a stacked PR before its base retargets** → it lands on the wrong base.
- **Treating a stage with no golden as proven** (it isn't until end-to-end passes).
- **Writing the implementation when the human wanted to own it** — in scaffold-only
  mode, stop at RED and wait.
- **Building a ticket on a vague or background signal** instead of an explicit go.
- **Carrying the porting discipline into post-parity feature work** — once the
  goldens pass, it's ordinary development; brainstorm and diverge.

## Reference files

- [Verification strategy](references/verification.md) — oracle sources + the ladder.
- [Faithful replication](references/faithful-replication.md) — the quirk catalog.
- [Planning board](references/planning-board.md) — the scrumban backlog pattern.
- [Workflow](references/workflow.md) — architecture + the merge-often loop + cadence.

## Ground rules

- **ALWAYS** re-read the source as you port, and verify against the oracle; name
  any stage you cannot verify.
- **ALWAYS** update a ticket's status in the branch that implements it.
- **NEVER** guess a test vector, port from memory, or silently "fix" the
  original's behaviour.
- **PREFER** faithfulness over cleanliness until green; refactor only after.
- **Brainstorm** the project name (and language, and architecture) *before*
  scaffolding.
- When the spec/planning doc conflicts with the source, **flag it to the human and
  default to the source** — it is the ground truth.
