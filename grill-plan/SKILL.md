---
name: grill-plan
description: Grill an existing handoff plan under `.scratch/`, then route to `/plan tighten` or `/to-spec`. Invoke as `/grill-plan NNN` (backlog) or `/grill-plan <requirement> NN`.
disable-model-invocation: true
---

Grill one handoff plan that `/plan` already wrote, then hand the settled result onward. (For a check without an interview, use the `vet` skill instead — the autonomous counterpart to this one.)

## Resolve

**Backlog plan** — argument is a three-digit number `NNN`:

- Resolve to exactly one file: `.scratch/_backlog/plans/NNN-*.md`
- Zero or multiple matches → STOP, list `.scratch/_backlog/plans/`, ask which.

**Requirement plan** — argument is `<requirement-slug> <NN>` (two tokens):

- Resolve to exactly one file: `.scratch/<requirement-slug>/plans/NN-*.md`
- Zero or multiple matches → STOP, list that requirement's `plans/`, ask which.

**Path** — argument is a path under `.scratch/` → that file.

Read that plan in full. The plan is **self-contained by construction** (that's `/plan`'s contract), so it *is* the brief — no upfront codebase survey; verify its claims against the codebase as they come under attack.

## Grill

Run a `/grilling` session against the plan. Walk its design tree — don't re-explain the plan, attack it:

- **Decisions** — is each chosen approach still right? Where's the weakest one?
- **Assumptions** — every as-is claim and "key assumption" the plan leans on (named seams and symbols exist, constraints hold). Verify against the codebase; a drifted claim is a live finding.
- **Scope & sequencing** — right in/out boundaries? Right `Depends on` order?
- **Done criteria & STOP conditions** — machine-checkable, or prose hiding a judgment call? Specific to this plan's real risks, or boilerplate?

## Route

Once grilling reaches shared understanding, hand off. Ask which route, recommending one from what the grilling changed:

- **Plan held its shape, decisions just sharpened** → `/plan tighten <resolved-plan-path>`. Default for refinement.
- **Grilling surfaced product-level scope worth publishing** → `/to-spec`. Synthesizes this conversation into a spec for the tracker — no second interview.
- **Direction changed enough that the plan is now wrong** → `/plan <brief>`. Heavier: re-plans from a fresh brief.
- **Grilling killed the premise — the problem is gone or not worth solving** → no rewrite: mark the plan's `README.md` row REJECTED with a one-line rationale, per `reconcile` conventions.

Carry the **settled tree** into whichever you pick — the contract [`grilling`](../grilling/SKILL.md) defines — so the downstream skill never re-opens a closed branch.
