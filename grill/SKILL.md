---
name: grill
description: Router for grill variants — pick the right stress-test interview before building.
disable-model-invocation: true
---

# Grill

Pick one variant and invoke it. All run a relentless interview before building.

| Variant | Invoke | When |
|---|---|---|
| **Grilling** | `/grilling` | Generic plan or design stress-test |
| **Grill me** | `/grill-me` | An idea with zero repo context — reads nothing, writes nothing |
| **Grill plan** | `/grill-plan NNN` | An existing handoff plan under `.scratch/` — then refine or publish |
| **Grill to plan** | `/grilling`, then hand the settled tree to `/plan` (see Compose below) | Open design → settled decisions → plan handoff |
| **Grill with docs** | `/grill-with-docs` | Grilling that also writes ADRs and glossary via `/domain-modeling` |
| **Grill yagni** | `/grilling` through the `/yagni` lens — walk the design tree, but make every branch earn its place | Stress-test a plan or design for over-engineering |

## Compose: grilling → plan

Run a `/grilling` session — or skip straight to the handoff if this conversation already holds a settled grilling. Once grilling reaches shared understanding, hand the settled **design tree** the [`grilling`](../grilling/SKILL.md) skill defines to `/plan` as a description brief — carry it whole, so the plans come out self-contained and `/plan` never re-opens a closed branch. The grilling *is* the audit: `/plan` skips its own audit and plans straight from your decisions, doing only its light recon for the project's verification gates.

When the plan matters enough to pay for two drafts, hand the same brief to `/duel` instead — two independent candidates, blind-judged.

Then invoke `/plan` with the brief: one self-contained plan per independently-executable unit in `.scratch/_backlog/plans/`, honour the sequencing, and **ask only what the brief leaves open — never re-litigate a settled branch.**

No interview wanted — just "check this plan or spec"? That's the `vet` skill (autonomous), not a grill.
