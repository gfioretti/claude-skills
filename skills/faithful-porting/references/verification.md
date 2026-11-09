# Verification strategy — the oracle

An **oracle** is any external source of truth about the original's correct
behaviour. Without one you can only prove your port is self-consistent, never
that it matches the original. Get an oracle *before* you write production code.

## Two oracle sources — harvest both

1. **Golden I/O you capture by running the original.** Feed known inputs, save the
   exact outputs as reference files. The strongest oracle, because it is the
   original's *actual* behaviour.
   - **Pin the original's default settings.** The goldens were produced under some
     configuration; discover it and reproduce it exactly. In nesmith the launch
     default for the dither amount was `0` (a no-op) — found in the WinForms
     designer / startup state. One wrong default breaks every byte-match.
   - Keep the **raw input and the reference output together** (e.g. one directory
     per fixture) so a test can load both.
   - Cover an easy case, a hard/edge case, and a stress case if you can.
   - **If the original is runnable, differential-test it.** Generate many random
     inputs, run *both* the original and your port, and assert byte-equal. This is
     a far broader oracle than a handful of captured files and flushes out quirks
     you didn't anticipate — make the generator stress every branch (negatives,
     ties, large magnitudes, non-ASCII, empty input).

2. **The legacy codebase's own tests, fixtures, and sample data.** Already-authored,
   free oracle material. Scan for `test/`, `spec/`, `fixtures/`, `testdata/`,
   `*.golden`, example inputs, and assertion-bearing tests.
   - **Reuse fixtures** as golden inputs.
   - **Port the assertions** — they encode expected values and edge cases the
     running tool may never surface through its normal output.
   - They also document intended contracts and corner behaviour.

If the original neither emits capturable outputs nor ships tests, find a capture
path: add an export/dump, instrument it to log intermediate state, or intercept
its I/O. Do not proceed to implementation without *some* oracle.

## The verification ladder (strongest first)

Pick the strongest rung available for each piece you test.

1. **Golden byte-match.** `assert_eq!(port(input), reference_bytes)`. Use whenever
   a golden exists. This is the gold standard and catches quirks you didn't know
   to look for.

2. **Computed vectors.** When no golden exists but the expected value is
   *derivable*. Hand-compute only truly trivial cases. For anything non-trivial,
   **run the real algorithm or a throwaway script to compute the expected value —
   never guess.** A guessed vector that happens to pass proves nothing; a guessed
   vector that's wrong sends you debugging correct code.

3. **Structural invariants.** When exact output isn't derivable without the full
   pipeline (heuristics, scoring, selection), pin what you *can* assert — counts,
   sizes, ranges, ordering, conservation ("merging never loses an element") — and
   **state plainly in the test/PR that the real proof is end-to-end.** Do not
   treat a structural pass as "verified."

4. **End-to-end golden.** The ultimate oracle. When the whole pipeline runs, match
   the final outputs byte-for-byte; this **retro-validates** the internal stages
   that had no standalone golden. A stage proven only by structural invariants is
   not done until the end-to-end goldens pass.

### Enabling a golden for a hard stage

Some stages can't be verified in isolation (nesmith's palette heuristic, FS
dither). Don't implement them blind. Build the **pipeline harness** first — enough
of the surrounding stages that the original's end-to-end golden can be produced —
then implement the hard stage *against that golden*. Order the backlog so the
harness lands before the stage that needs it.

## Test-code I/O lives in dev-dependencies

Golden tests often need to read files or decode formats (images, archives). Keep
those as **dev-dependencies** of the pure engine crate/module, so the engine
itself stays dependency-free (no fs/decode in production code — that belongs in
adapters). nesmith added the `image` crate only as a `nesmith-core`
dev-dependency, used solely to decode fixture PNGs in the golden tests.

## Checklist

- [ ] Found/produced an oracle (golden I/O and/or harvested tests) before coding.
- [ ] Reproduced the original's default settings for the goldens.
- [ ] Each test uses the strongest available rung; structural-only tests say so.
- [ ] Hard stages implemented against an end-to-end golden, not blind.
- [ ] No guessed expected values.
