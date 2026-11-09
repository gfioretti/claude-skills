# Faithful replication — reproduce behaviour exactly

A port must reproduce the original's behaviour **including its quirks and bugs**.
Byte-for-byte parity fails on the smallest divergence, and the divergences are
rarely in the "interesting" logic — they're in the boring details below. Port
*from the open source*, line by line; do not reconstruct from memory or intuition.

## Porting patterns

- **Globals → a state struct.** Legacy procedural code often threads behaviour
  through module-level/global variables. Translate them into the fields of one
  private struct with a method per phase, behind a pure public function. (nesmith:
  `Dz4`, `Gen`.) This makes the state explicit and the port testable.
- **Keep variable names close to the source.** It makes line-by-line comparison
  trivial and preserves intent. Cite the source `file:line` in comments.
- **Do not refactor or optimize behaviour during the port.** Faithfulness first.
  Clean up only after the stage is green against its oracle.
- **Preserve verbatim source identifiers** in comments and planning docs — don't
  "translate" names like `ToNES`, `Array.Sort`, `FromArgb`.

## The quirk catalog (what breaks byte-parity)

Check each of these against the source language's exact semantics:

- **Sort stability.** Match the *source language's* sort. .NET `Array.Sort` is an
  introspective sort that falls back to **insertion sort for ≤16 elements** →
  effectively **stable** for small arrays; a stable Rust `sort_by_key` reproduces
  its tie order. An unstable sort would reorder equal keys and diverge.
- **First-match / `break` loop semantics.** "Scan and take the first match, then
  break" vs "take the last/best" changes results on ties. Replicate the exact loop
  shape, including which comparison is strict (`<` vs `<=`) — strict `<` keeps the
  first/lowest index on ties.
- **Integer truncation & rounding.** `(int)` casts (truncate toward zero),
  float→int conversion, and the order of operations inside a cast
  (`(int)(a*w + b)` ≠ `(int)(a*w) + b`). Match the exact arithmetic and cast
  points.
- **Division *type* — mirror the operation, not just the rounding.** If the
  source does **float** division then truncates (Python 3 `int(a/b)`, where `/`
  is float), reproduce the float path (`(a as f64 / b as f64) as i64`) — do **not**
  substitute integer division (`a / b`). They agree for small values but diverge
  when float rounding of the quotient differs from exact integer truncation.
  Swapping the operation because it's "cleaner" or "more correct" is exactly the
  kind of improvement parity forbids. (A skill-guided agent made this very slip in
  a behaviour test — the op type is a classic fidelity trap.)
- **Colour / pixel semantics.** e.g. .NET `Color.FromArgb(r,g,b)` forces **alpha
  255** (opaque) — it silently drops the source alpha. Reproduce such forced
  fields exactly.
- **Sentinel values.** `-1`/`0xFF`/`999999` markers, "disabled" flags, and
  "absurd value" tie-breakers. Use a type that can hold the sentinel (e.g. `i64`
  for a `-2`) and apply it at the same point.
- **Off-by-one and bounds.** Rounding dimensions (`& 0x1f8`), partial-block
  handling, `len-1` guards, inclusive vs exclusive ranges.
- **Fixed strides / buffer sizes.** The original may index a fixed-width buffer
  (e.g. a 256-wide row) regardless of the actual width. Reproduce the stride the
  original uses, not the "logical" one.
- **Truncation on narrowing writes.** Writing a wider value into a byte output
  (an index > 255 into a byte field) truncates to the low byte — match it.

## When the spec and the source disagree

Planning docs, comments, or a human's description can be wrong; the **source is
the ground truth**. If your spec says "alpha is passed through" but the source
forces alpha to 255, the source wins. **Flag the conflict to the human**, note the
decision, and port what the source does. (nesmith hit exactly this and chose the
source's force-255 behaviour.)

## Checklist (per stage)

- [ ] Ported from the open source, line by line — not from memory.
- [ ] Globals captured in a state struct; names kept close to the source.
- [ ] Walked the quirk catalog against this stage's exact semantics.
- [ ] No behaviour "improvements" slipped in.
- [ ] Any spec↔source conflict flagged; source behaviour chosen.
