# Step Template

Every step file follows this structure. The sections aren't optional — they serve specific purposes, and skipping one is the path to a step that reads like a tutorial instead of a learning experience.

---

```markdown
# Step N — <Theme>

**Goal:** <one sentence: what mental model the user is building this step>

**Time-box:** <2, 3, or 4 hours>

## What to Build

<The concrete deliverables. Layout diagrams. Code skeletons where they clarify intent. Specific endpoints, schemas, file paths. Aim for ~30–60% of the step's word count to be in this section.>

<If introducing new tooling (a database, a message queue, a new dependency), explicitly call out the bootstrap commands. Don't assume the user remembers how to do `go mod init` or `cargo new` correctly for this language's conventions.>

## <Language> Concepts to Internalize

<Bulleted or short-sectioned discussion of the language ideas this step exercises. This is where you go deep on the "why" — why pointer receivers, why borrow checker errors look this way, why context is plumbed through every I/O call.>

<Each concept gets enough explanation that a competent programmer from another language can connect it to what they already know. Don't define `interface{}` as "an empty interface" — explain what it means in practice and when you actually reach for it.>

## <Source Language> Pitfalls

<This section is the differentiator. Identify the specific instincts from the source language that will mislead the user here. Phrase them as anti-patterns the user might fall into without realizing.>

<Example for "JVM → Go": "Don't put the interface next to the impl. In Java, RepositoryImpl.java lives next to Repository.java. In Go, the interface goes in the consumer package and implementations live elsewhere as adapters.">

<Example for "Python → Rust": "Don't fight the borrow checker by sprinkling .clone() everywhere. If you reach for clone more than twice in a function, the data model is probably wrong — refactor to borrows or restructure ownership.">

<Aim for 4–7 pitfalls per step. Each should reference something concrete from the source language.>

## Acceptance Checks

```bash
<Shell commands the user runs to verify the step works. Should produce visible, copy-pasteable output. Concrete commands like `make test` or `curl localhost:8080/healthz` — not vague "make sure it works" instructions.>
```

<If there are architectural rules the step introduces (e.g. "domain types don't import database libraries"), include a check command — grep, go list, cargo tree, whatever the language provides — to verify the rule.>

## Stretch Goals

<3–5 optional extensions that go deeper into the step's concepts. Stretch goals should NOT be required for later steps to work — they're for users who want to spend extra time and have specific weaknesses they're working on.>

<Examples: "Add a benchmark that compares X to Y", "Implement a lint rule that enforces Z", "Write a property-based test for the validation logic">

## Reflection Prompts (for your NOTES.md)

<3–4 open-ended questions the user should think about. These have no clean answers — they're the questions an interviewer might ask follow-up to "tell me about this project." Good reflection prompts surface tradeoffs, what-if scenarios, and "what would you change at scale."

Don't make these questions trivially Googleable. The point is to provoke thinking, not to be a worksheet.>

---

**Next:** [Step N+1 — <next theme>](./step-NN-next-theme.md)
```

---

## Why each section exists

**Goal** — sets the mental frame. The reader should know after one sentence whether this step is about types, concurrency, persistence, etc.

**Time-box** — a contract with the user. If a step takes 8 hours, it should have been two steps. The time-box forces honest scoping.

**What to Build** — concrete deliverables. This is the spec. Vague specs produce vague code. Show the layout, show the schemas, show the endpoints.

**Language Concepts** — the "why" behind the deliverables. This is what differentiates a coding challenge from a tutorial: not "here's the syntax" but "here's the conceptual model behind the syntax."

**Source Language Pitfalls** — the highest-value section. Generic language tutorials can't write this — they don't know what background to compare against. This is what makes the challenge personal to the user.

**Acceptance Checks** — closes the loop. Without these, users move on while having broken something subtly. The commands also become useful to paste into NOTES.md as proof of completion.

**Stretch Goals** — calibration for varying skill levels. Strong users compress two steps into one and use stretch goals for depth. Weaker users skip them entirely. Both are valid.

**Reflection Prompts** — interview gold. The user keeps NOTES.md and mines it for interview talking points. The prompts should generate observations the user can actually say in an interview.

## Length calibration

Each step file should land between **150 and 350 lines** of markdown. Below 150, the step is probably underspecified. Above 350, it's probably two steps disguised as one. The Linkr challenge's steps averaged around 250 lines each.

The first and last steps (project setup, observability/CI) tend to be longer because they cover more cross-cutting territory. The middle steps tend to be shorter because they exercise one focused concept.

## Code in step files

Include code skeletons when they clarify intent — interface signatures, struct definitions, the shape of a handler. Don't include complete implementations. The user is supposed to write the code; you're showing them the shape.

When showing code:

- Use realistic identifiers, not `Foo`/`Bar`
- Include imports so the user knows which packages/crates are in play
- Add comments that explain *why* something is shaped that way, not what the code does
- Truncate long sections with `// ... existing logic ...` rather than padding with boilerplate
