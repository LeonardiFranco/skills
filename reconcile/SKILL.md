---
name: reconcile
description: Keep `.scratch` plan backlogs alive — verify DONE plans still hold, investigate BLOCKED ones, check TODO premises still stand. Covers `_backlog/plans/` and `<requirement>/plans/`. Read-only on source; writes only under those plan trees.
license: MIT
---

# Reconcile

A plan backlog rots: code moves under it, executors finish or get blocked, findings get fixed in passing. Reconcile processes what happened since the last session so every `plans/README.md` under `.scratch/` stays trustworthy.

**First, read [the advisor contract](../improve/references/advisor-contract.md)** (read-only on source; you only ever write under plan trees in `.scratch/`). The plan structure lives in [the plan template](../improve/references/plan-template.md) — plans carry decisions, not code excerpts, so there is nothing mechanical to refresh; what can rot is a plan's *premise*.

## Workflow

1. Read `.scratch/README.md` for the master index.
2. Reconcile **each** plans tree: `.scratch/_backlog/plans/` and every `.scratch/<requirement>/plans/` that exists.
3. For each tree, read its `README.md` and every plan file. Then, per status:

- **DONE** — spot-check (cheap checks only) that the done criteria still hold on the current HEAD. Mark verified in that tree's index. Don't delete plan files — they're the record.
- **BLOCKED** — read the reason and investigate the underlying obstacle in the codebase. Obstacle gone or trivially routed around → refresh in place. Obstacle dead-ends the finding → REJECTED with one line of rationale. Obstacle puts the *approach* in question → hand the plan to the `vet` skill for the verdict; you sweep, it judges.
- **IN PROGRESS** (stale) — flag to the user; an executor probably died mid-run. Check the worktree if one exists.
- **TODO** — check the premise, not the facts: does "Why this matters" still describe a real problem on current HEAD (it may have been fixed in passing), and do the plan's named Decisions still hold against the code? Cheap reads only — the executor rediscovers current state at dispatch time; you're guarding against dispatching a plan whose reason to exist is gone. Finding gone → REJECTED ("fixed independently"). A named decision contradicted, or the approach in question → route to the `vet` skill rather than judging inline; you sweep, it judges.

When you refresh a plan's verification gates, re-source the commands from the project's current steering — the same project-steers-verification rule that governed planning (see the advisor contract).

**Completion criterion:** every row in every reconciled `plans/README.md` reflects reality on the current HEAD — DONE spot-checked, BLOCKED resolved or rejected, TODO premises checked — and a short report tells the user what's verified done, what's rejected, and what's executable right now.
