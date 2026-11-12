# Workflow — velocity without losing safety

The port moves fast when every change flows through the same automated loop, and
the engine stays verifiable because of how it's shaped. Set this up in Phase 3,
*before* implementation, so it works from PR #1.

## Architecture (chosen in Phase 2, not assumed)

Decide the shape deliberately with the human — don't default to either extreme:

- **When the engine benefits from isolation / reuse / golden-testability**, use a
  **hexagonal / ports-and-adapters** layout: a pure engine (domain value objects +
  use cases + I/O **port traits**) with *no* framework, filesystem, decode, or UI
  dependency; concrete **adapters** (file I/O, decoding) in a separate module;
  and a thin CLI/entry point wiring them. This lets you unit-test the whole engine
  against goldens with **in-memory fakes** of the ports — no real I/O in the core
  tests. It also lets a future GUI reuse the engine by swapping adapters. (This fit
  nesmith: `core` / `adapters` / `cli`.)
- **For a small tool/script**, keep it flatter — full hexagonal + DDD is
  over-engineering.

## The merge-often loop

Per feature, run the same loop:

1. **Branch off a fresh `main`.** One story/ticket per branch.
2. Implement to green (RED → GREEN → verify; see the skill body).
3. **Open a PR with auto-merge enabled.**
4. **Background-watch the PR** until it merges (poll its state), then branch the
   **next** feature off the now-updated `main`.

Set up once, in the scaffold phase:

- **CI gate**: format check + lint (deny warnings) + tests, on every PR.
- **Branch protection on `main`** requiring the CI check — with **no required
   reviews** for a solo project, so a green PR can merge itself.
- **Auto-merge enabled** repo-wide; **auto-delete merged branches**.

This keeps `main` always-green and lets you merge dozens of small PRs without
hand-holding. **Merge often** — don't let finished work pile up as a deep stack of
unmerged branches.

## Pitfalls

- **Stacked-PR base retargeting.** If PR B is based on branch A and you merge A and
  B in quick succession, the host may not retarget B to `main` before B merges →
  **B lands on the wrong base**. Fix: merge A, let the host retarget B to `main`,
  *then* merge B. Better: avoid stacking — merge each story before starting the
  next, so every branch forks from a fresh `main`.
- **An all-RED branch is unmergeable — but a per-ticket RED→GREEN branch is fine.**
  A branch that is *only* failing tests won't compile, so CI can't go green and it
  can't merge. That's a hazard only when you **pre-scaffold many tickets ahead** —
  keep that bulk scaffold on a throwaway holding branch. The normal loop is safe:
  scaffold the RED test and bring it to GREEN on the **same** feature branch before
  the PR. In **scaffold-only mode** (the human writes GREEN), the RED commit and the
  human's GREEN commits still share that one branch — just don't open the PR while
  it's still RED.
- **Direct pushes to a protected `main`** are rejected — even trivial doc fixes go
  through a (fast, auto-merging) PR.
- **Local vs CI toolchain drift.** A newer CI toolchain enforces lints your local
  one lacks (nesmith: CI clippy failed on `float-literal-f32-fallback` that the
  older local clippy didn't have). Treat the **CI gate as the source of truth**;
  pin/align toolchain versions to cut surprise failures, and never claim green off a
  local-only check.
- **Squash-merge hides the branch from `git branch -d`.** A squash merge rewrites
  history, so the feature branch isn't an ancestor of `main` and `-d` refuses it.
  Confirm the PR actually merged (`gh pr view <n> --json state,mergedAt`), then
  `git branch -D`.
- **When the project grows a front-end, split CI.** A GUI/native front-end drags
  heavy, slow deps (nesmith's egui added ~180 crates + system libs, pushing the one
  job from <1 min to ~6). Split into a **fast required core job** (quick feedback —
  the branch-protection context) and a **slower front-end job in parallel**, cached
  per job. If you rename the required job, **keep the exact branch-protection check
  name** (or update protection first) or auto-merge blocks forever.

## Collaboration cadence

- **Scope before you build, and gate on an explicit go.** Present a feature's scope
  (story + tasks) and flag the genuine decisions *before* writing code; let the human
  adjust, then wait for an explicit "start ticket N." Don't start a ticket on a vague
  or background signal. Once they say "keep rolling," run the loop unattended
  ticket-by-ticket and pause only for a real decision.
- **Checkpoint big or risky pieces** with the human rather than blasting through;
  surface genuine decisions.
- **A background event (a watcher firing) or silence is NOT approval.** Only a real
  human message is. Confirm, then act on the *prior* genuine instruction.
- **Commit cadence.** Default to committing only when asked; if the human agrees to a
  rolling cadence, commit **each logical unit as you go without re-asking** — scope
  doc and implementation as *separate* commits, each with the attribution trailer —
  but still **gate push/PR on an explicit ask** unless told otherwise.
- **Report faithfully.** Name any stage you couldn't verify, flag deviations from
  the spec, and never claim "done/verified" without the evidence (run the check,
  show the result).

## Checklist (Phase 3)

- [ ] Repo created + pushed; architecture skeleton laid down.
- [ ] CI gate (format + lint + test) runs on PRs.
- [ ] `main` protected by the CI check; no required reviews (solo).
- [ ] Auto-merge + auto-delete-branch enabled.
- [ ] The per-feature loop (branch → PR `--auto` → watch → next) is in use.
