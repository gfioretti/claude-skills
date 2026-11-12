# Case study — nesmith (PRISM/GET_PAL → Rust)

A worked example of the whole discipline on a real port, start to finish. The
other references state the rules; this shows how they played out — including the
stumbles.

## The port

- **Original:** PRISM / GET_PAL, Doug Fraker's C# WinForms NES image→graphics
  converter (MIT). ~4.7k lines of logic in `Form1.cs` + a DZ4 compressor in
  `Compress.cs`; the rest is WinForms plumbing to ignore.
- **Target:** `nesmith`, a cross-platform Rust CLI. Converts a 256×240 image into a
  CHR tile set, a nametable + attribute table, and a 13-colour NES palette (4
  sub-palettes under the 16×16 attribute constraint), with dithering, brightness,
  auto palette generation, and DZ4 compression.
- **Why faithful:** the output feeds real NES toolchains — it has to be *byte*
  identical, quirks and all.

## Two sources of truth (Phase 0–1)

- **Spec = the C# source.** Every ticket cited the exact `Form1.cs:NNNN-MMMM` range
  it ported. The source — not the planning doc, not intuition — was ground truth.
- **Oracle = golden files**, one directory per fixture in `tests/golden/`:
  `palette.pal` (16 B), `ntat.nam` (+`.dz4`, 1024 B), `final.chr` (+`.dz4`). Three
  fixtures chosen for coverage: `simcity` (easy, logo), `anime` (mid), and
  `cute_dog` (**photographic stress case** — >256 unique tiles, 16 KB CHR).
- **The default-settings trap:** the goldens were produced with the WinForms launch
  default **dither amount = 0** (a no-op). Miss that and *every* byte-match fails.
  Found it by reading the designer/startup state, not guessing.

## Architecture (Phase 2, chosen not assumed)

Hexagonal, because the engine benefited from golden-testability and future reuse:

- `nesmith-core` — pure engine: `domain` value objects + conversion services,
  `application::convert_image` use case, `ports` (I/O traits). No `image`/fs/CLI deps.
- `nesmith-adapters` — `image`-crate decoding, filesystem output.
- `nesmith-cli` — the `clap` binary wiring the use case to adapters.

Test I/O (decoding fixture PNGs) lived in **`nesmith-core` dev-dependencies** only
(`image`), keeping production code dependency-free.

## Backlog ordered by verifiability (Phase 4)

Deterministic/golden-verifiable first, riskiest heuristic last — **not** business
priority:

`colour → chr → nametable → compress` (each independently golden-verifiable) → then
`brightness + dither` → then the **palette auto-gen heuristic last** (riskiest, no
standalone golden) → then wire `ConvertImage` → then the CLI.

**Enabling a golden for the hard stage:** the palette heuristic and FS dither can't
be verified in isolation. Rather than implement blind, the surrounding pipeline was
built first so the original's **end-to-end** golden could retro-validate them.

## The quirks that actually broke parity (Phase 5)

Faithfulness over cleanliness — bugs preserved, not "fixed":

- **DZ4:** module-level globals (`rle_index`, `hacky_index`, …) → a private `Dz4`
  state struct; the `hacky_index` header-skip and the back-reference-mid-literal
  branch kept verbatim; `i64` to hold the `-2` sentinel. **Matched byte-for-byte on
  the first complete implementation.**
- **`Color.FromArgb(r,g,b)` forces alpha 255** — silently drops source alpha. The
  planning doc said "alpha passes through"; the **source won**, flagged to the human.
- **.NET `Array.Sort`** is effectively **stable for ≤16 elements** (insertion-sort
  fallback) → a stable Rust `sort_by_key` reproduces its tie order.
- Float-division-then-truncate, `(int)` cast points, fixed 256-wide strides.

## Workflow in practice (Phase 3, 5–6)

- **Merge-often loop:** one story per branch off fresh `main`; PR with `--auto`; a
  background watcher polled the PR to merge, then the next branch forked from the
  updated `main`. CI gate = fmt + clippy(`-D warnings`) + test; `main` protected by
  that check, **no required reviews** (solo), auto-merge + auto-delete-branch on.
- **Scaffold-only mode:** the human wanted to own the port, so each branch got
  **only the RED test**; they wrote GREEN; RED + GREEN shared the branch, PR'd once
  green. (See the Phase-5 variant.)
- **Scope-first:** scope doc committed and reviewed before any implementation; an
  explicit "start ticket N" gated each build.
- **House style:** British spelling across code too (`colour.rs`, `nes_colour_byte`),
  while source tokens stayed verbatim (`ToNES`, `FromArgb`).

## The stumbles (what the rules exist to prevent)

- **Stacked branches early** — `colour → chr → nametable` were stacked unmerged
  until the human asked "are we not merging back to main?" Fixed by merging each
  story before starting the next. → the merge-often rule.
- **Stale board** — `chr`/`nametable` story rows read "in progress" long after every
  subtask was done, needing an end-of-project reconciliation pass. → update status
  (and the parent row) *in the implementing branch*.

## Beyond parity (Phase 7)

Once the end-to-end goldens passed, the port was *done* and the discipline ended.
Everything after was ordinary feature development on the ported engine:

- A **`--palette-file`** lockable-palette feature, and a **`nesmith preview`** render.
- An **egui desktop GUI** reusing `nesmith-core` through **in-memory adapters**
  (`InMemoryImageSource` / `CollectSink`) — zero engine changes — then live preview,
  palette lock/load/save, and colour swatches, each brainstormed and diverging
  freely from the original WinForms UI.
- When the GUI landed, CI was **split** into a fast required `core` job and a slower
  parallel `gui` job (egui pulled ~180 crates + system libs), keeping the required
  check name stable so auto-merge didn't block. A Linux-only clippy lint
  (`float-literal-f32-fallback`) that the older local clippy missed was caught by
  the CI gate — exactly why the gate is the source of truth.

The payoff of the hexagonal choice: a whole new front-end reused the byte-exact
engine by **adding adapters, not forking the core**.
