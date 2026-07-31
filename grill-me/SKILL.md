---
name: grill-me
description: Relentless interview about an idea with zero repo context — reads nothing, writes nothing, ends by restating the settled tree.
disable-model-invocation: true
---

# Grill me

Run a `/grilling` session about the user's idea — with **zero repo context**. The idea may not be code-shaped at all.

Two rules are the whole skill; both fight your defaults:

- **Never read the codebase.** No file reads, no greps, no exploration — the user's words are the only source. When a question seems to need the repo, ask the user; if the interview keeps hitting questions only code can answer, say so and offer `/grilling` instead.
- **Never write anything.** No `CONTEXT.md`, no ADRs, no scratch files — the conversation is the workspace.

## Completion

End by restating the **settled tree** in chat — the contract [`grilling`](../grilling/SKILL.md) defines — as one block the user can paste into a fresh session. That block is this skill's only deliverable; the interview isn't done until it exists.

Want it persisted or continued elsewhere? Suggest `/plan` with the settled tree as the brief (writes an OpenSpec change), or paste the settled tree into a fresh session. This skill itself saves nothing.
