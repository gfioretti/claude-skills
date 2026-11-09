# Planning board — the scrumban backlog

A living board keeps the port ordered, mapped to the source, and honest about
what's actually done. Keep it in the new repo as a `planning/` tree plus a
`BACKLOG.md` index.

## Structure: Epic → Story → Task → Subtask

- **Epic** — a priority tier / area of the port (a folder).
- **Story** — one deliverable, one `feature/<name>` branch (a doc, or a folder
  with an overview + task tickets when big enough to split).
- **Task** — a numbered ticket under a big story (`NN-*.md`).
- **Subtask** — a checkbox inside a story or task doc.

Status key: ⬜ todo · 🟡 in progress · ✅ done.

Each ticket cites **the exact source `file:line` range it ports**, a short
background (why it exists, what it does), and acceptance criteria phrased against
the oracle (which golden / what invariant proves it). `BACKLOG.md` is the index:
a table of stories with status + links, and a per-epic checklist.

## Ordering: by verifiability + data dependency

Sequence the board so each stage can be *proven* when you build it — not by
business priority:

1. **Deterministic, independently golden-verifiable stages first.** They give
   fast, objective wins and early confidence.
2. **A stage that needs another's output comes after it** (respect real data
   dependencies — these often only become clear as you work; re-sequence then).
3. **The riskiest heuristic last**, by which point the pipeline exists to produce
   its end-to-end golden (so it isn't implemented blind).

## Just-in-time breakdown

**Break down only the next epic/story into tasks; keep later epics coarse** (a
one-line stub each). You will discover dependencies and constraints *during*
implementation — detailing everything up front guarantees rework. Detail each
epic when you reach it, and **re-sequence the board as you learn.**

## Keep status live — in the implementing branch

**Update a ticket's status — and its parent story-row — in the same branch/PR
that implements it.** Doing status in a separate end-of-project pass leaves the
board stale (nesmith's `chr`/`nametable` story rows read "in progress/todo" for a
long time while every subtask under them was done, because status was deferred —
it needed a reconciliation PR at the end).

Two rules that avoid the drift:

- Tick the subtask **and** bump the parent story-row status in the same change.
- A **story is "done" only when its end-to-end oracle passes**, not merely when
  its subtasks are coded. Until then it stays 🟡, even if every task box is ticked
  (a stage verified only by structural invariants is not yet proven).

## Checklist

- [ ] `planning/` tree + `BACKLOG.md` index created up front.
- [ ] Each ticket cites its source `file:line` + acceptance-vs-oracle.
- [ ] Board ordered by verifiability + dependency, not priority.
- [ ] Only the next epic detailed; the rest coarse; re-sequenced as you learn.
- [ ] Status updated (ticket + parent row) in the implementing branch.
- [ ] Stories marked done only once their end-to-end oracle passes.
