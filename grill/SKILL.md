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
| **Grill to plan** | `/grill-then-plan` | Open design → settled decisions → `/plan` handoff |
| **Grill with docs** | `/grill-with-docs` | Grilling that also writes ADRs and glossary via `/domain-modeling` |
| **Grill yagni** | `/grill-yagni` | Stress-test a plan or design for over-engineering — every branch must earn its place |

No interview wanted — just "check this plan or spec"? That's the `vet` skill (autonomous), not a grill.
